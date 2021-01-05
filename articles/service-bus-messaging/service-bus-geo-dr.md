---
title: Ripristino di emergenza geografico per il bus di servizio di Azure | Microsoft Docs
description: Come usare le aree geografiche per il failover ed eseguire il ripristino di emergenza nel bus di servizio di Azure
ms.topic: article
ms.date: 01/04/2021
ms.openlocfilehash: c07721c07923a40da9fe28e0e3116bfd6a52210f
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862357"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Ripristino di emergenza geografico per il bus di servizio di Azure

La resilienza contro interruzioni disastrose delle risorse di elaborazione dati è un requisito per molte aziende e in alcuni casi anche richiesto dalle normative del settore. 

Il bus di servizio di Azure distribuisce già il rischio di errori irreversibili dei singoli computer o persino dei rack completi tra i cluster che si estendono su più domini di errore all'interno di un Data Center e implementa meccanismi di rilevamento e failover trasparenti in modo che il servizio continui a funzionare entro i livelli di servizio assicurati e in genere senza interruzioni evidenti in caso di errori. Se è stato creato uno spazio dei nomi del bus di servizio con l'opzione Enabled per le [zone di disponibilità](../availability-zones/az-overview.md), il rischio è che il rischio di interruzione sia ulteriormente distribuito in tre strutture fisicamente separate e il servizio dispone di un numero sufficiente di riserve di capacità per affrontare immediatamente la perdita completa e irreversibile dell'intera struttura. 

Il modello di cluster del bus di servizio di Azure completamente attivo con supporto per la zona di disponibilità è superiore a qualsiasi prodotto di broker di messaggi locale in termini di resilienza in caso di guasti hardware gravi e persino di perdita irreversibile di intere strutture del Data Center. È comunque possibile che si verifichino situazioni gravi con la distruzione fisica diffusa che anche le misure non possono essere sufficientemente difesa. 

La funzionalità di ripristino di emergenza geografico del bus di servizio è progettata per semplificare il ripristino da una situazione di emergenza e per abbandonare un'area di Azure non riuscita e senza dover modificare le configurazioni dell'applicazione. L'abbandono di un'area di Azure comporterà in genere diversi servizi e questa funzionalità mira principalmente a mantenere l'integrità della configurazione dell'applicazione composita. La funzionalità è disponibile a livello globale per lo SKU Premium del bus di servizio. 

La funzionalità di ripristino Geo-Disaster garantisce che l'intera configurazione di uno spazio dei nomi (code, argomenti, sottoscrizioni, filtri) venga replicata continuamente da uno spazio dei nomi primario a uno spazio dei nomi secondario quando abbinato e consente di avviare un failover solo una volta dal database primario a quello secondario in qualsiasi momento. Lo spostamento del failover consente di reindirizzare il nome dell'alias scelto per lo spazio dei nomi allo spazio dei nomi secondario e quindi di suddividere l'associazione. Il failover è quasi istantaneo una volta avviata. 

> [!IMPORTANT]
> La funzionalità consente la continuità immediata delle operazioni con la stessa configurazione, ma **non replica i messaggi contenuti nelle code o nelle sottoscrizioni degli argomenti o nelle code dei messaggi non recapitabili**. Per mantenere la semantica della coda, tale replica richiederà non solo la replica dei dati del messaggio, ma di ogni modifica dello stato nel Broker. Per la maggior parte degli spazi dei nomi del bus di servizio, il traffico di replica necessario supera il traffico dell'applicazione e con le code con velocità effettiva elevata, la maggior parte dei messaggi viene comunque replicata nel database secondario mentre sono già stati eliminati dal database primario, causando un traffico eccessivamente inutile. Per le route di replica a latenza elevata, che si applicano a molti abbinamenti che si scelgono per il ripristino di emergenza geografico, potrebbe anche essere Impossibile per il traffico di replica sostenere in modo sostenibile il traffico dell'applicazione a causa di effetti di limitazione delle richieste di latenza.
 
> [!TIP]
> Per replicare il contenuto delle code e delle sottoscrizioni degli argomenti e operare gli spazi dei nomi corrispondenti nelle configurazioni attive/attive per far fronte a interruzioni e emergenze, non appoggiarsi a questo set di funzionalità di ripristino di emergenza geografico, ma attenersi alle [linee guida](service-bus-federation-overview.md)per la replica.  

## <a name="outages-and-disasters"></a>Emergenze e interruzioni

È importante notare la distinzione tra "interruzioni" ed "emergenze". 

Un'*interruzione* è l'indisponibilità temporanea del bus di servizio di Azure e può interessare alcuni componenti del servizio, ad esempio un archivio di messaggistica, oppure l'intero data center. Dopo la risoluzione del problema, tuttavia, il bus di servizio torna di nuovo disponibile. In genere, un'interruzione non determina la perdita di messaggi o di altri dati. Un'interruzione può essere provocata ad esempio da un'interruzione dell'alimentazione nel data center. Alcune interruzioni sono solo brevi perdite di connessione dovute a problemi di rete o temporanei. 

Il termine *emergenza* indica la perdita permanente o a lungo termine di un cluster del bus di servizio, un'area di Azure o un data center. L'area o il data center potrebbe o meno tornare di nuovo disponibile oppure potrebbe rimanere inattivo per ore o giorni. Un'emergenza può essere causata, ad esempio, da un incendio, un'inondazione o un terremoto. Una situazione di emergenza che diventa permanente potrebbe causare la perdita di alcuni messaggi, eventi o altri dati. Tuttavia, nella maggior parte dei casi non dovrebbe esserci perdita di dati e i messaggi possono essere ripristinati dopo aver eseguito il backup del data center.

La funzionalità di ripristino di emergenza geografico del bus di servizio di Azure è una soluzione di ripristino di emergenza. I concetti e il flusso di lavoro illustrati in questo articolo sono applicabili a scenari di emergenza, non a interruzioni temporanee. Per una descrizione dettagliata del ripristino di emergenza in Microsoft Azure, vedere [questo articolo](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Concetti e terminologia di base

La funzionalità di ripristino di emergenza implementa il ripristino di emergenza dei metadati e si basa sugli spazi dei nomi primari e secondari di ripristino di emergenza. Si noti che la funzionalità di ripristino di emergenza geografico è disponibile solo per lo [SKU Premium](service-bus-premium-messaging.md). Non è necessario apportare modifiche alla stringa di connessione, perché la connessione viene effettuata tramite un alias.

In questo articolo viene usata la terminologia seguente:

-  *Alias*: nome per una configurazione di ripristino di emergenza impostata. L'alias fornisce una singola stringa di connessione FQDN (nome di dominio completo) stabile. Le applicazioni usano questa stringa di connessione alias per connettersi a uno spazio dei nomi. L'uso di un alias garantisce che la stringa di connessione rimanga invariata quando viene attivato il failover.

-  *Spazio dei nomi primario/secondario*: spazi dei nomi corrispondenti all'alias. Lo spazio dei nomi primario è "attivo" e riceve i messaggi (può essere uno spazio dei nomi esistente o nuovo). Lo spazio dei nomi secondario è "passivo" e non riceve i messaggi. I metadati vengono sincronizzati tra entrambi gli spazi dei nomi, quindi entrambi possono facilmente accettare messaggi senza modifiche al codice dell'applicazione o alla stringa di connessione. Per fare in modo che solo lo spazio dei nomi attivo riceva i messaggi, è necessario usare l'alias. 

-  *Metadati*: entità come code, argomenti e sottoscrizioni e le relative proprietà del servizio associate allo spazio dei nomi. Si noti che solo le entità e le relative impostazioni vengono replicate automaticamente. I messaggi non vengono replicati.

-  *Failover*: processo di attivazione dello spazio dei nomi secondario.

## <a name="setup"></a>Configurazione

Di seguito viene fornita una panoramica della procedura di configurazione dell'associazione tra gli spazi dei nomi.

![1][]

Il processo di configurazione si articola nelle fasi seguenti:

1. Effettuare il provisioning di uno spazio dei nomi premium del bus di servizio.

2. Effettuare il provisioning di uno spazio dei nomi premium del bus di servizio _*_secondario_*_ in un'area _different da dove viene effettuato il provisioning dello spazio dei nomi In questo modo è possibile attivare l'isolamento dei guasti tra aree di data center diverse.

3. Creare un'associazione tra lo spazio dei nomi primario e lo spazio dei nomi secondario per ottenere l'**alias***.

    >[!NOTE] 
    > Se è stata [eseguita la migrazione dello spazio dei nomi standard del bus di servizio di Azure a Premium del bus di servizio di Azure](service-bus-migrate-standard-premium.md), è necessario usare l'alias preesistente, ad esempio la stringa di connessione dello spazio dei nomi standard del bus di servizio, per creare la configurazione del ripristino di emergenza tramite _ *PS/CLI** o l' **API REST**.
    >
    >
    > Questo perché, durante la migrazione, la stringa di connessione dello spazio dei nomi Standard del bus di servizio di Azure o il nome DNS stesso diventa un alias dello spazio dei nomi Premium del bus di servizio di Azure.
    >
    > Le applicazioni client devono utilizzare questo alias (ad esempio la stringa di connessione dello spazio dei nomi Standard del bus di servizio di Azure) per connettersi allo spazio dei nomi Premium in cui è stata configurata l'associazione del ripristino di emergenza.
    >
    > Se per configurare il ripristino di emergenza si usa il portale, quest'ultimo visualizzerà questa avvertenza in forma riepilogativa.


4. Usare l'*_alias_** ottenuto nel passaggio 3 per connettere le applicazioni client allo spazio dei nomi primario abilitato per il ripristino di emergenza geografico. Inizialmente, l'alias fa riferimento allo spazio dei nomi primario.

5. [Facoltativo] Aggiungere funzionalità di monitoraggio per rilevare i casi in cui è necessario un failover.

## <a name="failover-flow"></a>Flusso del failover

Un failover viene attivato manualmente dal cliente (sia in modo esplicito con un comando o tramite la logica di business di proprietà del client che attiva il comando) e mai da Azure. In questo modo viene concessa al cliente la proprietà completa e la visibilità per la risoluzione di interruzioni sulla rete backbone di Azure.

![4][]

In seguito all'attivazione del failover:

1. La stringa di connessione _*_alias_*_ viene aggiornata in modo da puntare allo spazio dei nomi Premium secondario.

2. I client (mittenti e destinatari) si connettono automaticamente allo spazio dei nomi secondario.

3. L'associazione esistente tra lo spazio dei nomi Premium primario e quello secondario viene interrotta.

Dopo aver avviato il failover:

1. È necessario poter eseguire di nuovo il failover nel caso in cui si verifichi un'altra interruzione. Configurare quindi un altro spazio dei nomi passivo e aggiornare l'associazione. 

2. Eseguire il pull dei messaggi dallo spazio dei nomi primario precedente quando è di nuovo disponibile. Successivamente, usare tale spazio dei nomi per la messaggistica regolare di fuori della configurazione del ripristino geografico oppure eliminare lo spazio dei nomi primario precedente.

> [!NOTE]
> È supportata solo la semantica di inoltro in caso di errore. In questo scenario, si esegue il failover e quindi si esegue di nuovo l'associazione con un nuovo spazio dei nomi. Il failback, ad esempio in un cluster SQL, non è supportato. 

È possibile automatizzare il failover con sistemi di monitoraggio o con soluzioni di monitoraggio personalizzate. Tale automazione, tuttavia, richiede pianificazione e lavoro aggiuntivi che esulano dall'ambito di questo articolo.

![2][]

## <a name="management"></a>Gestione

Se si commette un errore, ad esempio associando le aree non corrette durante la configurazione iniziale, è possibile interrompere l'associazione dei due spazi dei nomi in qualsiasi momento. Per usare gli spazi dei nomi associati come normali spazi dei nomi, eliminare l'alias.

## <a name="use-existing-namespace-as-alias"></a>Usare lo spazio dei nomi esistente come alias

Se si ha uno scenario in cui non è possibile modificare le connessioni di producer e consumer, è possibile riutilizzare il nome dello spazio dei nomi come nome dell'alias. Vedere il [codice di esempio su GitHub qui](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Esempi

Gli [esempi su GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) mostrano come configurare e avviare un failover. Questi esempi illustrano i concetti seguenti:

- Un esempio .NET e le impostazioni necessarie in Azure Active Directory per usare Azure Resource Manager con il bus di servizio per configurare e abilitare il ripristino di emergenza geografico.
- Passaggi necessari per eseguire il codice di esempio.
- Modalità di utilizzo di uno spazio dei nomi esistente come alias.
- I passaggi per abilitare alternativamente il ripristino di emergenza geografico tramite PowerShell o l'interfaccia della riga di comando.
- [L'invio e la ricezione](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) dallo spazio dei nomi primario o secondario corrente tramite l'alias.

## <a name="considerations"></a>Considerazioni

Tenere presente le considerazioni seguenti per questa versione:

1. Quando si pianifica il failover, è consigliabile considerare anche il fattore tempo. Ad esempio, se si perde la connettività per più di 15-20 minuti, è possibile decidere di avviare il failover.

2. Il fatto che non vengano replicati dati significa che le sessioni attive non vengono replicate. Il rilevamento dei duplicati e i messaggi pianificati potrebbero inoltre non funzionare. Le nuove sessioni, i nuovi messaggi pianificati e i nuovi duplicati funzioneranno. 

3. È necessario [provare a effettuare](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) il failover di un'infrastruttura distribuita complessa almeno una volta.

4. La sincronizzazione delle entità può richiedere tempo, circa un minuto per 50-100 entità. Anche le sottoscrizioni e le regole contano come entità.

## <a name="availability-zones"></a>Zone di disponibilità

Lo SKU Premium del bus di servizio supporta anche le [zone di disponibilità](../availability-zones/az-overview.md) fornendo località con isolamento di errore all'interno di un'area di Azure.

> [!NOTE]
> Il supporto per le zone di disponibilità per il bus di servizio di Azure Premium è disponibile solo nelle [aree di Azure](../availability-zones/az-region.md) in cui sono presenti le zone di disponibilità.

Usando il portale di Azure, è possibile abilitare le zone di disponibilità solo negli spazi dei nomi. Il bus di servizio non supporta la migrazione degli spazi dei nomi esistenti. Non è possibile disabilitare la ridondanza della zona dopo che è stata abilitata nello spazio dei nomi.

![3][]

## <a name="private-endpoints"></a>Endpoint privati
Questa sezione presenta considerazioni aggiuntive sull'uso del ripristino di emergenza geografico con spazi dei nomi che usano endpoint privati. Per informazioni sull'uso di endpoint privati con il bus di servizio in generale, vedere [Integrare il bus di servizio di Azure con Collegamento privato di Azure](private-link-service.md).

### <a name="new-pairings"></a>Nuove associazioni
Se si tenta di creare un'associazione tra uno spazio dei nomi primario con un endpoint privato e uno spazio dei nomi secondario senza un endpoint privato, l'associazione ha esito negativo. L'associazione avrà esito positivo solo se entrambi gli spazi dei nomi, primario e secondario, hanno endpoint privati. È consigliabile usare le stesse configurazioni per gli spazi dei nomi primario e secondario e per le reti virtuali in cui sono stati creati gli endpoint privati. 

> [!NOTE]
> Quando si tenta di associare lo spazio dei nomi primario con un endpoint privato e lo spazio dei nomi secondario, il processo di convalida controlla solo se esiste un endpoint privato nello spazio dei nomi secondario. Non controlla se l'endpoint funziona o se funzionerà dopo il failover. È responsabilità dell'utente assicurarsi che lo spazio dei nomi secondario con endpoint privato funzioni come previsto dopo il failover.
>
> Per verificare che le configurazioni degli endpoint privati siano uguali, inviare una richiesta [Get queues](/rest/api/servicebus/stable/queues/get) allo spazio dei nomi secondario dall'esterno della rete virtuale e verificare che si riceva un messaggio di errore dal servizio.

### <a name="existing-pairings"></a>Associazioni esistenti
Se l'associazione tra uno spazio dei nomi primario e uno secondario esiste già, la creazione di endpoint privati nello spazio dei nomi primario ha esito negativo. Per risolvere il problema, creare prima un endpoint privato nello spazio dei nomi secondario e quindi crearne uno per lo spazio dei nomi primario.

> [!NOTE]
> Mentre l'accesso allo spazio dei nomi secondario è consentito in sola lettura, è possibile eseguire aggiornamenti nelle configurazioni degli endpoint privati. 

### <a name="recommended-configuration"></a>Configurazione consigliata
Quando si crea una configurazione di ripristino di emergenza per l'applicazione e il bus di servizio, è necessario creare endpoint privati per entrambi gli spazi dei nomi, primario e secondario, del bus di servizio nelle reti virtuali che ospitano entrambe le istanze, primaria e secondaria, dell'applicazione.

Si supponga di avere due reti virtuali, VNET-1 e VNET-2, e gli spazi dei nomi, rispettivamente primario e secondario, ServiceBus-Namespace1-Primary e ServiceBus-Namespace2-Secondary. È necessario eseguire la procedura seguente: 

- In ServiceBus-Namespace1-Primary creare due endpoint privati che usano subnet da VNET-1 e VNET-2
- In ServiceBus-Namespace2-Secondary creare due endpoint privati che usano le stesse subnet da VNET-1 e VNET-2 

![Endpoint privati e reti virtuali](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


Il vantaggio di questo approccio è che il failover può verificarsi a livello di applicazione indipendentemente dallo spazio dei nomi del bus di servizio. Esaminare gli scenari seguenti: 

_ *Failover solo applicazione:** in questo caso, l'applicazione non esisterà in VNET-1, ma passerà a VNET-2. Poiché entrambi gli endpoint privati sono configurati sia in VNET-1 che VNET-2 per entrambi gli spazi dei nomi primario e secondario, l'applicazione funzionerà. 

**Failover del solo spazio dei nomi del bus di servizio**: anche in questo caso, poiché entrambi gli endpoint privati sono configurati in entrambe le reti virtuali per entrambi gli spazi dei nomi primario e secondario, l'applicazione funzionerà. 

> [!NOTE]
> Per materiale sussidiario sul ripristino di emergenza geografico di una rete virtuale, vedere [Rete virtuale - Continuità aziendale](../virtual-network/virtual-network-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sull'API REST per il ripristino di emergenza geografico, vedere [qui](/rest/api/servicebus/stable/disasterrecoveryconfigs).
- Eseguire l'[esempio di GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) sul ripristino di emergenza geografico.
- Vedere l'[esempio che invia messaggi a un alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) per il ripristino di emergenza geografico.

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli articoli seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
