---
title: Ripristino di emergenza geografico - Hub eventi di Azure | Microsoft Docs
description: Come usare le aree geografiche per il failover ed eseguire il ripristino di emergenza in Hub eventi di Azure
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 504a83772c2ac8e3afc86465899357d0eda4eb92
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478659"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Hub eventi di Azure - Ripristino di emergenza geografico 

La resilienza contro interruzioni catastrofiche delle risorse di elaborazione dei dati è un requisito per molte aziende e, in alcuni casi, anche per le normative del settore. 

Hub eventi di Azure già estende il rischio di errori irreversibili di singoli computer o persino rack completi tra cluster che si estendono su più domini di errore all'interno di un data center e implementa meccanismi trasparenti di rilevamento degli errori e failover in modo che il servizio continui a funzionare entro i livelli di servizio garantiti e in genere senza interruzioni evidenti in caso di errori di questo tipo. Se è stato creato uno spazio dei nomi di Hub eventi con l'opzione abilitata per le zone di [disponibilità,](../availability-zones/az-overview.md)il rischio di interruzione viene ulteriormente distribuito in tre strutture separate fisicamente e il servizio dispone di sufficienti riserva di capacità per gestire immediatamente la perdita completa e irreversica dell'intera struttura. 

Il modello di cluster Hub eventi di Azure con supporto della zona di disponibilità offre resilienza contro gravi errori hardware e persino la perdita irreversibile di intere strutture del data center. Tuttavia, potrebbero esserci situazioni gravi con distruzione fisica generalizzata da cui anche queste misure non possono essere sufficientemente difese. 

La funzionalità di ripristino di emergenza geografico di Hub eventi è progettata per semplificare il ripristino da un'emergenza di questo tipo e abbandonare un'area di Azure in errore senza dover modificare le configurazioni dell'applicazione. L'abbandono di un'area di Azure comporta in genere diversi servizi e questa funzionalità mira principalmente a preservare l'integrità della configurazione dell'applicazione composita.  

La funzionalità di ripristino di Geo-Disaster garantisce che l'intera configurazione di uno spazio dei nomi (Hub eventi, gruppi di consumer e impostazioni) sia replicata continuamente da uno spazio dei nomi primario a uno spazio dei nomi secondario quando associato e consente di avviare uno spostamento di failover una sola volta dal database primario a quello secondario in qualsiasi momento. Lo spostamento del failover ri-puntare il nome alias scelto per lo spazio dei nomi allo spazio dei nomi secondario e quindi interrompere l'associazione. Il failover è quasi istantaneo dopo l'avvio. 

> [!IMPORTANT]
> La funzionalità consente la continuità immediata delle operazioni con la stessa configurazione, ma **non replica i dati dell'evento**. A meno che l'emergenza non causi la perdita di tutte le zone, i dati degli eventi conservati nell'hub eventi primario dopo il failover saranno recuperabili e gli eventi storici potranno essere ottenuti da tale area dopo il ripristino dell'accesso. Per la replica dei dati degli eventi e il funzionamento degli spazi dei nomi corrispondenti nelle configurazioni attive/attive per gestire interruzioni e situazioni di emergenza, non fare riferimento a questo set di funzionalità di ripristino di emergenza geografico, ma seguire le linee guida per la [replica.](event-hubs-federation-overview.md)  

## <a name="outages-and-disasters"></a>Emergenze e interruzioni

È importante notare la distinzione tra "interruzioni" ed "emergenze". Un'**interruzione** è l'indisponibilità temporanea di Hub eventi di Azure e può interessare alcuni componenti del servizio, ad esempio un archivio di messaggistica, oppure l'intero data center. Dopo la risoluzione del problema, tuttavia, Hub eventi torna di nuovo disponibile. Un'interruzione non determina in genere la perdita di messaggi o di altri dati. Un'interruzione può essere provocata ad esempio da un'interruzione dell'alimentazione nel data center. Alcune interruzioni sono solo brevi perdite di connessione dovute a problemi di rete o temporanei. 

Il termine *emergenza* indica la perdita permanente o a lungo termine di un cluster di Hub eventi, un'area di Azure o un data center. L'area o il data center potrebbe o meno tornare di nuovo disponibile oppure potrebbe rimanere inattivo per ore o giorni. Un'emergenza può essere causata, ad esempio, da un incendio, un'inondazione o un terremoto. Una situazione di emergenza che diventa permanente potrebbe causare la perdita di alcuni messaggi, eventi o altri dati. Tuttavia, nella maggior parte dei casi non dovrebbe esserci perdita di dati e i messaggi possono essere ripristinati dopo aver eseguito il backup del data center.

La funzionalità di ripristino di emergenza geografico di Hub eventi di Azure è una soluzione di ripristino di emergenza. I concetti e il flusso di lavoro illustrati in questo articolo sono applicabili a scenari di emergenza, non a interruzioni temporanee. Per una descrizione dettagliata del ripristino di emergenza in Microsoft Azure, vedere [questo articolo](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Concetti e terminologia di base

La funzionalità di ripristino di emergenza implementa il ripristino di emergenza dei metadati e si basa sugli spazi dei nomi primari e secondari di ripristino di emergenza. 

La funzionalità di ripristino di emergenza geografico è disponibile solo per gli [SKU standard e dedicati](https://azure.microsoft.com/pricing/details/event-hubs/). Non è necessario apportare modifiche alla stringa di connessione, perché la connessione viene effettuata tramite un alias.

In questo articolo viene usata la terminologia seguente:

-  *Alias*: nome per una configurazione di ripristino di emergenza impostata. L'alias fornisce una singola stringa di connessione FQDN (nome di dominio completo) stabile. Le applicazioni usano questa stringa di connessione alias per connettersi a uno spazio dei nomi. 

-  *Spazio dei nomi primario/secondario*: spazi dei nomi corrispondenti all'alias. Lo spazio dei nomi primario è "attivo" e riceve i messaggi (può essere uno spazio dei nomi esistente o nuovo). Lo spazio dei nomi secondario è "passivo" e non riceve messaggi. I metadati vengono sincronizzati tra entrambi gli spazi dei nomi, quindi entrambi possono facilmente accettare messaggi senza modifiche al codice dell'applicazione o alla stringa di connessione. Per fare in modo che solo lo spazio dei nomi attivo riceva i messaggi, è necessario usare l'alias.
-  *Metadati*: entità come hub eventi e gruppi di consumer e le relative proprietà del servizio associate allo spazio dei nomi. Solo le entità e le relative impostazioni vengono replicate automaticamente. I messaggi e gli eventi non vengono replicati. 
-  *Failover*: processo di attivazione dello spazio dei nomi secondario.

## <a name="supported-namespace-pairs"></a>Coppie di spazi dei nomi supportate
Sono supportate le combinazioni seguenti di spazi dei nomi primari e secondari:  

| Spazio dei nomi primario | Spazio dei nomi secondario | Supportato | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Sì | 
| Standard | Dedicato | Sì | 
| Dedicato | Dedicato | Sì | 
| Dedicato | Standard | No | 

> [!NOTE]
> Non è possibile associare spazi dei nomi che si trovano nello stesso cluster dedicato. È possibile associare spazi dei nomi che si trovano in cluster separati. 

## <a name="setup-and-failover-flow"></a>Configurazione e flusso del failover

La sezione seguente è una panoramica del processo di failover e illustra come configurare il failover iniziale. 

![1][]

### <a name="setup"></a>Configurazione

È prima di tutto necessario creare uno spazio dei nomi primario o usarne uno esistente e creare un nuovo spazio dei nomi secondario, quindi associare i due spazi dei nomi. L'associazione fornisce un alias che può essere usato per la connessione. Poiché si usa un alias, non è necessario modificare le stringhe di connessione. È possibile aggiungere solo nuovi spazi dei nomi all'associazione di failover. 

1. Creare lo spazio dei nomi primario.
1. Creare lo spazio dei nomi secondario in un'area diversa. Questo passaggio è facoltativo. È possibile creare lo spazio dei nomi secondario durante la creazione dell'associazione nel passaggio successivo. 
1. Nel portale di Azure passare allo spazio dei nomi primario.
1. Selezionare **Ripristino geografico** nel menu a sinistra e selezionare **Avvia associazione** sulla barra degli strumenti. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Avviare l'associazione dallo spazio dei nomi primario":::    
1. Nella pagina **Avvia associazione** seguire questa procedura:
    1. Selezionare uno spazio dei nomi secondario esistente o crearne uno in un'area diversa. In questo esempio viene selezionato uno spazio dei nomi esistente.  
    1. Per **Alias** immettere un alias per l'associazione con ripristino geografico. 
    1. Scegliere quindi **Create** (Crea). 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Selezionare lo spazio dei nomi secondario":::        
1. Verrà visualizzata la **pagina Alias ripristino di emergenza geografico.** È anche possibile passare a questa pagina dallo spazio dei nomi primario selezionando **Ripristino geografico** nel menu a sinistra.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Pagina alias di ripristino di emergenza geografico":::    
1. Nella pagina **Alias di ripristino di** emergenza geografico selezionare Criteri di **accesso** condiviso nel menu a sinistra per accedere alla stringa di connessione primaria per l'alias. Usare questa stringa di connessione invece di usare direttamente la stringa di connessione allo spazio dei nomi primario/secondario. 
1. In questa **pagina** Panoramica è possibile eseguire le azioni seguenti: 
    1. Interrompere l'associazione tra gli spazi dei nomi primario e secondario. Selezionare **Interrompi associazione sulla** barra degli strumenti. 
    1. Eseguire manualmente il failover nello spazio dei nomi secondario. Selezionare **Failover sulla** barra degli strumenti. 
    
        > [!WARNING]
        > Il failover attiverà lo spazio dei nomi secondario e rimuoverà lo spazio dei nomi primario dall'Geo-Disaster di ripristino. Creare un altro spazio dei nomi per avere una nuova coppia di ripristino di emergenza geografico. 

Infine, è necessario aggiungere funzionalità di monitoraggio per rilevare i casi in cui è necessario un failover. Nella maggior parte dei casi, il servizio fa parte di un ecosistema di grandi dimensioni, quindi i failover automatici sono raramente possibili, in quanto spesso i failover devono essere eseguiti in modo sincronizzato con il sottosistema o l'infrastruttura rimanente.

### <a name="example"></a>Esempio

In un esempio di questo scenario, si consideri una soluzione POS che genera messaggi o eventi. Hub eventi passa gli eventi a una soluzione di mapping o riformattazione, che quindi inoltra i dati mappati a un altro sistema per un'ulteriore elaborazione. A questo punto, tutti questi sistemi possono essere ospitati nella stessa area di Azure. La decisione di quando e quale parte del failover dipende dal flusso di dati nell'infrastruttura. 

È possibile automatizzare il failover con sistemi di monitoraggio o con soluzioni di monitoraggio personalizzate. Tale automazione, tuttavia, richiede pianificazione e lavoro aggiuntivi che esulano dall'ambito di questo articolo.

### <a name="failover-flow"></a>Flusso del failover

Se si avvia il failover, sono necessari due passaggi:

1. È necessario poter eseguire di nuovo il failover nel caso in cui si verifichi un'altra interruzione. Configurare quindi un altro spazio dei nomi passivo e aggiornare l'associazione. 

2. Eseguire il pull dei messaggi dallo spazio dei nomi primario precedente quando è di nuovo disponibile. Successivamente, usare tale spazio dei nomi per la messaggistica regolare di fuori della configurazione del ripristino geografico oppure eliminare lo spazio dei nomi primario precedente.

> [!NOTE]
> È supportata solo la semantica di inoltro in caso di errore. In questo scenario, si esegue il failover e quindi si esegue di nuovo l'associazione con un nuovo spazio dei nomi. Il failback, ad esempio in un cluster SQL, non è supportato. 

![2][]

## <a name="management"></a>Gestione

Se si commette un errore, ad esempio associando le aree non corrette durante la configurazione iniziale, è possibile interrompere l'associazione dei due spazi dei nomi in qualsiasi momento. Per usare gli spazi dei nomi associati come normali spazi dei nomi, eliminare l'alias.

## <a name="samples"></a>Esempi

L'[esempio su GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) illustra come configurare e avviare un failover. L'esempio illustra i concetti seguenti:

- Impostazioni necessarie in Azure Active Directory per usare Azure Resource Manager con Hub eventi. 
- Passaggi necessari per eseguire il codice di esempio. 
- Invio e ricezione dallo spazio dei nomi primario corrente. 

## <a name="considerations"></a>Considerazioni

Tenere presenti le considerazioni seguenti:

1. Da progettazione, il ripristino di emergenza geografico di Hub eventi non replica i dati e non è quindi possibile riutilizzare il valore di offset precedente dell'hub eventi primario per l'hub eventi secondario. È consigliabile riavviare il ricevitore di eventi con uno dei metodi seguenti:

   - *EventPosition.FromStart()* - Se si vogliono leggere tutti i dati nell'hub eventi secondario.
   - *EventPosition.FromEnd()* - Se si vogliono leggere tutti i nuovi dati dal momento della connessione all'hub eventi secondario.
   - *EventPosition.FromEnqueuedTime(dateTime)* - Se si vogliono leggere tutti i dati ricevuti nell'hub eventi secondario a partire da una data e da un'ora specificate.

2. Quando si pianifica il failover, è consigliabile considerare anche il fattore tempo. Ad esempio, se si perde la connettività per più di 15-20 minuti, è possibile decidere di avviare il failover. 
 
3. Il fatto che non vengano replicati dati significa che le sessioni attive correnti non vengono replicate. Il rilevamento dei duplicati e i messaggi pianificati potrebbero inoltre non funzionare. Le nuove sessioni, i messaggi pianificati e i nuovi duplicati funzioneranno. 

4. È necessario [provare a effettuare](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) il failover di un'infrastruttura distribuita complessa almeno una volta. 

5. La sincronizzazione delle entità può richiedere tempo, circa un minuto per 50-100 entità.

## <a name="availability-zones"></a>Zone di disponibilità 

Lo SKU Standard di Hub eventi supporta le [zone di disponibilità di Azure](../availability-zones/az-overview.md) fornendo località con isolamento di errore all'interno di un'area di Azure. 

> [!NOTE]
> Il supporto per le zone di disponibilità per lo SKU standard di Hub eventi di Azure è disponibile solo nelle [aree di Azure](../availability-zones/az-region.md) in cui sono presenti le zone di disponibilità.

Usando il portale di Azure, è possibile abilitare le zone di disponibilità solo negli spazi dei nomi. Hub eventi non supporta la migrazione di spazi dei nomi esistenti. Non è possibile disabilitare la ridondanza della zona dopo che è stata abilitata nello spazio dei nomi.

Quando si usano le zone di disponibilità, sia i metadati che i dati (eventi) vengono replicati tra i data center nella zona di disponibilità. 

![3][]

## <a name="private-endpoints"></a>Endpoint privati
In questa sezione vengono fornite altre considerazioni sull'uso del ripristino di emergenza geografico con spazi dei nomi che usano endpoint privati. Per informazioni generali sull'uso di endpoint privati con Hub eventi, vedere [Configurare gli endpoint privati](private-link-service.md).

### <a name="new-pairings"></a>Nuove associazioni
Se si tenta di creare un'associazione tra uno spazio dei nomi primario con un endpoint privato e uno spazio dei nomi secondario senza un endpoint privato, l'associazione ha esito negativo. L'associazione avrà esito positivo solo se entrambi gli spazi dei nomi, primario e secondario, hanno endpoint privati. È consigliabile usare le stesse configurazioni per gli spazi dei nomi primario e secondario e per le reti virtuali in cui sono stati creati gli endpoint privati.  

> [!NOTE]
> Quando si tenta di associare lo spazio dei nomi primario con un endpoint privato e uno spazio dei nomi secondario, il processo di convalida controlla solo se esiste un endpoint privato nello spazio dei nomi secondario. Non controlla se l'endpoint funziona o se funzionerà dopo il failover. È responsabilità dell'utente assicurarsi che lo spazio dei nomi secondario con endpoint privato funzioni come previsto dopo il failover.
>
> Per verificare che le configurazioni degli endpoint privati siano le stesse negli spazi dei nomi primario e secondario, inviare una richiesta di lettura, ad esempio [Get](/rest/api/eventhub/get-event-hub) per l'hub eventi, allo spazio dei nomi secondario dall'esterno della rete virtuale e verificare che si riceva un messaggio di errore dal servizio.

### <a name="existing-pairings"></a>Associazioni esistenti
Se l'associazione tra uno spazio dei nomi primario e uno secondario esiste già, la creazione di endpoint privati nello spazio dei nomi primario ha esito negativo. Per risolvere il problema, creare prima un endpoint privato nello spazio dei nomi secondario e quindi crearne uno per lo spazio dei nomi primario.

> [!NOTE]
> Mentre l'accesso allo spazio dei nomi secondario è consentito in sola lettura, è possibile eseguire aggiornamenti nelle configurazioni degli endpoint privati. 

### <a name="recommended-configuration"></a>Configurazione consigliata
Quando si crea una configurazione di ripristino di emergenza per l'applicazione e gli spazi dei nomi di Hub eventi, è necessario creare endpoint privati per entrambi gli spazi dei nomi, primario e secondario, di Hub eventi nelle reti virtuali che ospitano entrambe le istanze, primaria e secondaria, dell'applicazione. 

Si supponga di avere due reti virtuali, VNET-1 e VNET-2, e gli spazi dei nomi, rispettivamente primario e secondario, EventHubs-Namespace1-Primary e EventHubs-Namespace2-Secondary. È necessario eseguire la procedura seguente: 

- In EventHubs-Namespace1-Primary creare due endpoint privati che usano subnet da VNET-1 e VNET-2
- In EventHubs-Namespace2-Secondary creare due endpoint privati che usano le stesse subnet da VNET-1 e VNET-2 

![Endpoint privati e reti virtuali](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Il vantaggio di questo approccio è che il failover può verificarsi a livello di applicazione indipendentemente dallo spazio dei nomi di Hub eventi. Esaminare gli scenari seguenti: 

**Failover della sola applicazione**: in questo caso, l'applicazione non esiste in VNET-1 ma passa a VNET-2. Poiché entrambi gli endpoint privati sono configurati sia in VNET-1 che VNET-2 per entrambi gli spazi dei nomi primario e secondario, l'applicazione funzionerà. 

**Failover del solo spazio dei nomi di Hub eventi**: anche in questo caso, poiché entrambi gli endpoint privati sono configurati in entrambe le reti virtuali per entrambi gli spazi dei nomi primario e secondario, l'applicazione funzionerà. 

> [!NOTE]
> Per materiale sussidiario sul ripristino di emergenza geografico di una rete virtuale, vedere [Rete virtuale - Continuità aziendale](../virtual-network/virtual-network-disaster-recovery-guidance.md).
 
## <a name="next-steps"></a>Passaggi successivi
Esaminare gli esempi o la documentazione di riferimento seguenti. 
- [Esempio di geoDR .NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/DotNet/GeoDRClient) 
- [Esempio di GeoDR Java](https://github.com/Azure-Samples/eventhub-java-manage-event-hub-geo-disaster-recovery)
- [.NET - Esempi di Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [.NET - Esempi di Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)
- [Java - Esempi di azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Java - Esempi di azure-eventhubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)
- [Esempi per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
- [Esempi JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Esempi di TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
- [Informazioni di riferimento sulle API REST](/rest/api/eventhub/)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
