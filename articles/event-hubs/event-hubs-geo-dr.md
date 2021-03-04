---
title: Ripristino di emergenza geografico - Hub eventi di Azure | Microsoft Docs
description: Come usare le aree geografiche per il failover ed eseguire il ripristino di emergenza in Hub eventi di Azure
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: eb6ef1a7536b819d1bc973740a0da6fdf3d756d5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042369"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Hub eventi di Azure - Ripristino di emergenza geografico 

La resilienza contro interruzioni disastrose delle risorse di elaborazione dati è un requisito per molte aziende e in alcuni casi anche richiesto dalle normative del settore. 

Hub eventi di Azure distribuisce già il rischio di errori irreversibili dei singoli computer o persino dei rack completi tra i cluster che si estendono su più domini di errore all'interno di un Data Center e implementa meccanismi di rilevamento e failover trasparenti, in modo che il servizio continui a funzionare entro i livelli di servizio garantiti e in genere senza interruzioni evidenti in caso di errori di questo tipo. Se è stato creato uno spazio dei nomi di hub eventi con l'opzione Enabled per le [zone di disponibilità](../availability-zones/az-overview.md), il rischio di interruzione viene ulteriormente distribuito in tre strutture fisicamente separate e il servizio dispone di un numero sufficiente di riserve di capacità per far fronte immediatamente alla perdita irreversibile completa dell'intera struttura. 

Il modello di cluster di hub eventi di Azure all-Active con supporto per la zona di disponibilità offre resilienza in caso di guasti hardware gravi e anche di perdita irreversibile di intere strutture del Data Center. È comunque possibile che si verifichino situazioni gravi con la distruzione fisica diffusa che anche le misure non possono essere sufficientemente difesa. 

La funzionalità di ripristino di emergenza geografico di hub eventi è progettata per semplificare il ripristino da una situazione di emergenza e per abbandonare un'area di Azure non riuscita e senza dover modificare le configurazioni dell'applicazione. L'abbandono di un'area di Azure comporterà in genere diversi servizi e questa funzionalità mira principalmente a mantenere l'integrità della configurazione dell'applicazione composita.  

La funzionalità di ripristino Geo-Disaster garantisce che l'intera configurazione di uno spazio dei nomi (hub eventi, gruppi di consumer e impostazioni) venga replicata continuamente da uno spazio dei nomi primario a uno spazio dei nomi secondario quando abbinato e consente di avviare un failover solo una volta dal database primario al database secondario in qualsiasi momento. Lo spostamento del failover consente di reindirizzare il nome dell'alias scelto per lo spazio dei nomi allo spazio dei nomi secondario e quindi di suddividere l'associazione. Il failover è quasi istantaneo una volta avviata. 

> [!IMPORTANT]
> La funzionalità consente la continuità immediata delle operazioni con la stessa configurazione, ma **non replica i dati dell'evento**. A meno che l'emergenza non abbia causato la perdita di tutte le zone, i dati dell'evento conservati nell'hub eventi primario dopo il failover saranno recuperabili e gli eventi cronologici potranno essere ottenuti da una volta ripristinato l'accesso. Per la replica dei dati degli eventi e il funzionamento degli spazi dei nomi corrispondenti nelle configurazioni attive/attive per far fronte a interruzioni e emergenze, non bisogna occuparsi di questo set di funzionalità di ripristino di emergenza geografico, ma attenersi alle [linee guida](event-hubs-federation-overview.md)per la replica.  

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
1. Nella portale di Azure passare allo spazio dei nomi primario.
1. Selezionare **ripristino geografico** nel menu a sinistra e selezionare **Avvia associazione** sulla barra degli strumenti. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Avviare l'associazione dallo spazio dei nomi primario":::    
1. Nella pagina **avvio associazione** , attenersi alla seguente procedura:
    1. Selezionare uno spazio dei nomi secondario esistente o crearne uno in un'area diversa. In questo esempio viene selezionato uno spazio dei nomi esistente.  
    1. In **alias** immettere un alias per l'associazione del ripristino di emergenza geografico. 
    1. Scegliere quindi **Create** (Crea). 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Selezionare lo spazio dei nomi secondario":::        
1. Verrà visualizzata la pagina **alias ripristino di emergenza geografico** . È anche possibile passare a questa pagina dallo spazio dei nomi primario selezionando **ripristino geografico** nel menu a sinistra.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Pagina alias ripristino di emergenza geografico":::    
1. Nella pagina **alias ripristino di emergenza geografico** selezionare **criteri di accesso condivisi** nel menu a sinistra per accedere alla stringa di connessione primaria per l'alias. Usare questa stringa di connessione invece di utilizzare direttamente la stringa di connessione allo spazio dei nomi primario/secondario. 
1. In questa pagina di **Panoramica** è possibile eseguire le azioni seguenti: 
    1. Suddividere l'associazione tra gli spazi dei nomi primari e secondari. Selezionare **Interrompi associazione** sulla barra degli strumenti. 
    1. Eseguire manualmente il failover allo spazio dei nomi secondario. Selezionare **failover** sulla barra degli strumenti. 
    
        > [!WARNING]
        > Il failover attiverà lo spazio dei nomi secondario e rimuoverà lo spazio dei nomi primario dall'associazione di Geo-Disaster Recovery. Creare un altro spazio dei nomi per avere una nuova coppia di ripristino di emergenza geografico. 

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

Tenere presente le considerazioni seguenti:

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

Quando si usano le zone di disponibilità, i metadati e i dati (eventi) vengono replicati nei data center della zona di disponibilità. 

![3][]

## <a name="private-endpoints"></a>Endpoint privati
Questa sezione offre ulteriori considerazioni quando si usa il ripristino di emergenza geografico con gli spazi dei nomi che usano endpoint privati. Per informazioni generali sull'uso di endpoint privati con Hub eventi, vedere [Configurare gli endpoint privati](private-link-service.md).

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

* L'[esempio disponibile in GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) illustra in dettaglio un semplice flusso di lavoro per la creazione di un'associazione geografica e l'avvio di un failover per uno scenario di ripristino di emergenza.
* Nella [informazioni di riferimento sulle API del servizio REST](/rest/api/eventhub/) sono descritte le API che consentono di eseguire la configurazione del ripristino di emergenza geografico.

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

- Introduzione all'Hub eventi
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
