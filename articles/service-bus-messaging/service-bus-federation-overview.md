---
title: Replica di messaggi e Federazione tra più aree-bus di servizio di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica della replica degli eventi e della Federazione tra aree con il bus di servizio di Azure.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: e47f633fcd9248eab6f47936aa7c45877decc1fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880828"
---
# <a name="message-replication-and-cross-region-federation"></a>Replica di messaggi e federazione tra aree

All'interno degli spazi dei nomi, il bus di servizio di Azure supporta la [creazione di topologie di Code concatenate e sottoscrizioni di argomenti usando l'inoltro](service-bus-auto-forwarding.md) in modo da consentire l'implementazione di vari modelli di routing. È ad esempio possibile fornire ai partner le code dedicate a cui hanno autorizzazioni di trasmissione o ricezione e che possono essere temporaneamente sospese, se necessario, e connetterle in modo flessibile ad altre entità private dell'applicazione. È inoltre possibile creare topologie di routing multifase complesse oppure creare code di tipo cassetta postale, che svuotano le sottoscrizioni degli argomenti di tipo coda e consentono una maggiore capacità di archiviazione per ogni Sottoscrittore. 

Molte soluzioni sofisticate richiedono anche la replica dei messaggi tra i limiti dello spazio dei nomi per implementare questi e altri modelli. Potrebbe essere necessario che i messaggi scorrano tra gli spazi dei nomi associati a più tenant dell'applicazione diversi o in più aree di Azure diverse. 

La soluzione consentirà di gestire più spazi dei nomi del bus di servizio in aree diverse e di replicare i messaggi tra le code e gli argomenti e/o di scambiare messaggi con origini e destinazioni quali [Hub eventi di Azure](../event-hubs/event-hubs-about.md), [hub Azure](../iot-fundamentals/iot-introduction.md)o [Apache Kafka](https://kafka.apache.org). 

Questi scenari sono l'obiettivo di questo articolo. 

## <a name="federation-patterns"></a>Modelli di Federazione

Esistono numerose motivazioni potenziali per i motivi per cui potrebbe essere necessario spostare i messaggi tra entità del bus di servizio, ad esempio code o argomenti, oppure tra il bus di servizio e altre origini e destinazioni. 

Rispetto al set di modelli per gli [Hub eventi](../service-bus-messaging/service-bus-federation-overview.md), la Federazione per le entità di tipo coda è più complessa perché le code di messaggi promettono ai propri utenti la proprietà esclusiva di ogni singolo messaggio. si prevede di mantenere l'ordine di arrivo nel recapito dei messaggi e per il broker di coordinare la distribuzione equa dei messaggi tra [consumer concorrente](/azure/architecture/patterns/competing-consumers). 

Sono presenti impedimenti pratici, inclusi i vincoli del [teorema Cap](https://en.wikipedia.org/wiki/CAP_theorem), che rendono difficile fornire una visualizzazione unificata di una coda che è disponibile simultaneamente in più aree e che consente ai [consumer](/azure/architecture/patterns/competing-consumers) distribuiti a livello di area di assumere la proprietà esclusiva dei messaggi. Tale coda distribuita geograficamente richiede la replica completamente coerente non solo dei messaggi, ma anche lo stato di recapito di ogni messaggio prima che i messaggi possano essere resi disponibili per gli utenti. L'obiettivo di una coerenza completa per una coda ipotetica distribuita a livello di area è in conflitto diretto con l'obiettivo principale che praticamente tutti i clienti del bus di servizio di Azure presentano quando si considerano gli scenari di Federazione: disponibilità e affidabilità massime per le proprie soluzioni. 

I modelli presentati in questo articolo si concentrano quindi sulla disponibilità e sull'affidabilità e mirano anche a evitare la perdita di informazioni e la gestione duplicata dei messaggi. 

### <a name="resiliency-against-regional-availability-events"></a>Resilienza per gli eventi di disponibilità a livello di area 

Sebbene la disponibilità e l'affidabilità massime siano le priorità operative principali per il bus di servizio, esistono tuttavia molti modi in cui un Producer o un consumatore potrebbe non essere in grado di comunicare con il bus di servizio "primario" assegnato a causa di problemi di rete o di risoluzione dei nomi oppure se l'entità del bus di servizio potrebbe effettivamente non rispondere o restituire errori. Il processore del messaggio designato potrebbe anche non essere più disponibile.

Tali condizioni non sono "disastrose", in modo che sia possibile abbandonare completamente la distribuzione a livello di area, come si farebbe in una situazione di ripristino di emergenza, ma lo scenario aziendale di alcune applicazioni potrebbe essere già influenzato dagli eventi di disponibilità che durano non più di pochi minuti o addirittura secondi. Il bus di servizio di Azure viene spesso usato in ambienti cloud ibridi e con client che si trovano al perimetro della rete, ad esempio in negozi al dettaglio, ristoranti, rami bancari, siti di produzione, strutture logistiche e aeroporti. È possibile che un problema di routing o congestione della rete influisca sulla capacità di un sito di raggiungere l'endpoint del bus di servizio assegnato mentre un endpoint secondario in un'area diversa può essere raggiungibile. Allo stesso tempo, i sistemi che elaborano i messaggi provenienti da questi siti possono comunque avere accesso non consentito agli endpoint del bus di servizio primario e secondario. 

Ci sono molti esempi pratici di tali applicazioni cloud ibride e perimetrali con una bassa tolleranza aziendale per l'effetto dei problemi di routing di rete o di problemi di disponibilità temporanei di un'entità del bus di servizio. Questi includono l'elaborazione dei pagamenti presso i siti di vendita al dettaglio, il Boarding presso gli aeroporti e gli ordini dei telefoni cellulari, che sono tutti in un istante e vengono completati ogni volta che il percorso di comunicazione affidabile non è disponibile.

In questa categoria vengono illustrati tre modelli distribuiti distinti: replica "all-Active", replica "attivo-passivo" e replica "in eccesso". 

#### <a name="all-active-replication"></a>Replica All-Active

Il modello di replica "all-Active" consente a una replica attiva dello stesso argomento logico (o coda) di essere disponibile in più spazi dei nomi (e aree) e per tutti i messaggi diventano disponibili in tutte le repliche, indipendentemente dalla posizione in cui sono stati accodati. In genere, il modello consente di mantenere l'ordine dei messaggi relativi a qualsiasi server di pubblicazione. 

![Tutti i criteri attivi](media/service-bus-federation-overview/mirrored-topics.jpg)

Come illustrato nell'illustrazione, il modello si basa in genere sugli argomenti del bus di servizio. Un argomento per ogni spazio dei nomi che deve partecipare allo schema di replica. Ognuno di questi argomenti è costituito da una "sottoscrizione di replica" per tutti gli altri argomenti in cui i messaggi devono essere replicati. Nell'illustrazione precedente è sufficiente una coppia di argomenti e pertanto una singola sottoscrizione di replica per il rispettivo altro argomento. In uno scenario con tre spazi dei nomi *{N1, N2, N3}*, un argomento nello spazio dei nomi *N1* avrebbe due sottoscrizioni di replica, una per l'argomento corrispondente in *N2* e una per l'argomento corrispondente in *N3*. 

Ogni sottoscrizione di replica dispone di una regola che combina un'espressione di filtro SQL ( `replicated <> 1` ) e un'azione SQL ( `set replicated = 1` ). Il filtro della regola garantisce che solo i messaggi in cui la proprietà personalizzata `replication` non è impostata o non dispongano del valore come `1` idoneo per la sottoscrizione e l'azione imposta la proprietà esatta sul valore di `1` ogni messaggio selezionato in un secondo momento. L'effetto è che, quando il messaggio viene copiato nell'argomento corrispondente, non è più idoneo per la replica nella direzione opposta e pertanto si evitano i messaggi che rimbalzano tra le repliche.

Una sottoscrizione con una regola corrispondente può essere facilmente aggiunta a qualsiasi argomento usando l'interfaccia della riga di comando di Azure come questa.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

Per modellare una coda, ogni argomento è limitato a una sola sottoscrizione normale (ad eccezione delle sottoscrizioni di replica) che tutti i consumer condividono.

> Il modello di replica all-Active inserisce una copia di ogni messaggio inviato in uno degli argomenti in ciascuno degli argomenti. Questo significa che il codice dell'applicazione in ogni area visualizzerà ed elaborerà tutti i messaggi.
> Questo modello è adatto per gli scenari in cui i dati vengono condivisi in più aree o se l'elaborazione ridondante è in genere necessaria. Se è necessario elaborare ogni messaggio una sola volta, come per una normale coda, è necessario prendere in considerazione uno dei due modelli seguenti.  

#### <a name="active-passive-replication"></a>Replica Active-Passive

Il modello di replica "attivo-passivo" è una variante del modello precedente, in cui solo uno degli argomenti ("primario") viene usato attivamente dall'applicazione per l'invio e la ricezione di messaggi e i messaggi vengono replicati in un argomento secondario, nel caso in cui l'argomento primario possa diventare non disponibile o irraggiungibile. 

![Modello attivo passivo](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

La differenza principale tra questo modello e il modello precedente è che la replica è unidirezionale dall'argomento primario nell'argomento secondario. L'argomento secondario non diventa mai il database primario, ma è un'opzione di backup per quando l'argomento primario è temporaneamente inutilizzabile. 

Il vantaggio dell'uso di questo modello è che tenta di ridurre al minimo l'elaborazione duplicata. Durante la replica, la `TimeToLive` Proprietà Message viene impostata su un valore Duration per i messaggi replicati che riflette il tempo previsto durante il quale un errore del database primario provocherà un failover. Se, ad esempio, lo scenario del caso d'uso richiede un passaggio del consumer al database secondario entro un massimo di 1 minuto dal momento in cui il recupero dei messaggi dall'inizio principale Mostra problemi, il database secondario dovrebbe idealmente avere tutti i messaggi disponibili che non è stato possibile accedere al database primario, ma un numero minimo di messaggi già elaborati dal database primario prima del verificarsi dei problemi. Se si imposta `TimeToLive` su due volte tale periodo, 2 minuti durante la replica ( `set sys.TimeToLive = '0:2:0'` nell'azione della regola), il database secondario manterrà solo i messaggi per 2 minuti e eliminerà quelli meno recenti. Ciò significa che quando il ricevitore passa al database secondario, può leggere e rimuovere rapidamente i messaggi più vecchi dell'ultimo elaborato, quindi elaborarli dal primo messaggio che non sono ancora stati visualizzati. Il periodo di conservazione effettivo dipenderà dal caso d'uso specifico e in una rapida operazione si desidera e può passare al database secondario nell'applicazione. L' `TimeToLive` impostazione viene rispettata nell'intervallo da pochi secondi a giorni. 

Mentre l'applicazione usa il database secondario, può anche pubblicare direttamente nell'argomento secondario, che quindi funge da qualsiasi argomento normale. Dopo il passaggio al database secondario, il consumer visualizzerà quindi una combinazione di messaggi replicati e messaggi pubblicati direttamente nel database secondario. L'applicazione deve pertanto prima tornare alla pubblicazione primaria e consentire comunque lo svuotamento dei messaggi pubblicati localmente prima di riportare il consumer al database secondario. A causa della ripresa automatica della replica quando il database primario è nuovamente disponibile, il consumer otterrebbe anche nuovi messaggi pubblicati nel database primario durante tale periodo di tempo, anche se con latenza leggermente superiore. 

> Questo modello è adatto per scenari in cui i messaggi devono essere elaborati una sola volta. L'applicazione deve collaborare per tenere traccia dei messaggi elaborati dal database primario perché troverà i duplicati per la durata della finestra di failover nel database secondario e rileverà i duplicati durante il ritorno.
> Il criterio di deduplicazione dovrebbe essere il più possibile fornito dall'applicazione `MessageId` . Il `EnqueuedTimeUtc` valore è adatto anche come indicatore di filigrana, ma l'applicazione deve consentire una certa quantità di tempo di indisponibilità (diversi secondi) tra la replica primaria e quella secondaria come con qualsiasi sistema distribuito.


#### <a name="spillover-replication"></a>Replica in eccesso

Il modello di replica "in eccesso" consente l'uso attivo/attivo di più entità del bus di servizio in più aree per gestire lo scenario in cui il bus di servizio è integro, ma il *consumer* viene sovraccaricato con il numero di messaggi in sospeso o è non disponibile. Un motivo potrebbe essere il fatto che un database che supporta il processo del consumer può essere lento o non disponibile. Questo modello funziona con le code semplici e con le sottoscrizioni dell'argomento.  

![Replica in eccesso](media/service-bus-federation-overview/spillover.jpg)  

Come illustrato nell'illustrazione, il modello di replica di ricorrenza replica i messaggi della coda dei messaggi non [recapitabili](service-bus-dead-letter-queues.md) associata a una coda o una sottoscrizione a una coda o a un argomento associato in uno spazio dei nomi diverso. 

In assenza di una situazione di errore, i due spazi dei nomi vengono usati in parallelo, ognuno dei quali riceve un subset del traffico globale dei messaggi e i relativi consumer associati che gestiscono tale subset. Quando uno dei consumer inizia a presentare percentuali di errore elevate o si interrompe, i rispettivi messaggi finiranno nella coda dei messaggi non recapitabili tramite il superamento del numero di recapiti o della scadenza. Le attività di replica li raccoglieranno e li riaccodano nella coda abbinata, dove vengono quindi presentati al consumatore integro. 

Se l'elaborazione deve essere eseguita entro una determinata scadenza, è `TimeToLive` necessario impostare la per la coda e/o i messaggi in modo tale che l'elaborazione possa ancora verificarsi nel tempo dal database secondario in eccesso, ad esempio `TimeToLive` potrebbe essere impostato sulla metà del tempo consentito.

Analogamente al [modello all-Active](#all-active-replication), l'applicazione può aggiungere un indicatore al messaggio se il messaggio è già stato replicato una volta in modo che non venga eseguito il rimbalzo tra la coppia di code, ma viene invece inserito in una coda ausiliaria che funge da coda dei messaggi non recapitabili per il modello composito.

> Questo modello è adatto per gli scenari in cui la preoccupazione principale è la difesa da problemi di disponibilità di consumer o risorse su cui si basano i consumer e anche per ridistribuire i picchi di traffico in una delle code abbinate. Fornisce inoltre la protezione contro uno degli spazi dei nomi che non è più disponibile se i consumer leggono da entrambe le code, ma il ritardo di replica imposto dalla scadenza può causare la recapito dei `TimeToLive` messaggi all'interno dell'intervallo di tempo nello spazio dei nomi non disponibile. 

### <a name="latency-optimization"></a>Ottimizzazione della latenza 

Gli argomenti vengono usati per distribuire informazioni a più consumer. In alcuni casi, in particolare per i consumer con distribuzione geografica estesa, potrebbe essere utile replicare i messaggi di un argomento in un argomento di uno spazio dei nomi secondario più vicino ai consumer.

![Ottimizzazione della latenza](media/service-bus-federation-overview/latency-optimization.jpg)  

Ad esempio, quando si condividono dati tra hub continentali (Regional), è più efficiente trasferire le informazioni una sola volta tra gli hub e fare in modo che i consumer ottengano la copia dei dati da tali Hub. 

I trasferimenti di replica possono essere eseguiti in batch, che spesso gli utenti ottengono e stabiliscono i messaggi uno alla volta. Con una latenza di rete di base di 100 ms tra, ad America del Nord ed Europa, ogni messaggio richiede 200 ms più a lungo per l'elaborazione dei due round trip in un'entità remota per l'acquisizione e la risoluzione dei messaggi, rispetto a un'entità nella stessa area. 

### <a name="validation-reduction-and-enrichment"></a>Convalida, riduzione e arricchimento

I messaggi possono essere inviati a una coda o a un argomento del bus di servizio da parte dei client esterni alla propria soluzione.

![Convalida, riduzione, arricchimento](media/service-bus-federation-overview/validation.jpg)  

È possibile che tali messaggi richiedano la verifica della conformità a un determinato schema e che i messaggi non conformi vengano eliminati o non recapitabili. È possibile che alcuni messaggi debbano essere ridotti in complessità omettendo i dati e potrebbe essere necessario arricchirli aggiungendo dati in base alle ricerche dei dati di riferimento. Le operazioni possono essere eseguite con funzionalità personalizzate nell'attività di replica. 

### <a name="stream-to-queue-replication"></a>Replica da flusso a coda

Hub eventi di Azure è una soluzione ideale per la gestione di volumi estremi di eventi in ingresso. Ma non hub eventi né motori simili come Apache Kafka forniscono un modello [consumer concorrente](/azure/architecture/patterns/competing-consumers) gestito dal servizio, in cui più consumer possono gestire i messaggi dalla stessa origine simultaneamente senza rischiare l'elaborazione duplicata e infine risolvere tali messaggi una volta elaborati. 

![Integrazione](media/service-bus-federation-overview/hub-to-queue.jpg)

Un flusso alla replica di Accodamento trasferisce il contenuto di una singola partizione dell'hub eventi o il contenuto di un hub eventi completo in una coda del bus di servizio, da cui i messaggi possono essere elaborati in modo sicuro, transazionale e con consumer concorrente. Questa replica consente anche l'uso di tutte le altre funzionalità del bus di servizio per tali messaggi, incluso il routing con argomenti e il demultiplexing basato sulla sessione. 

### <a name="consolidation-and-normalization"></a>Consolidamento e normalizzazione 

Le soluzioni globali sono spesso costituite da footprint locali che sono in gran parte indipendenti, incluse le proprie capacità di elaborazione, ma le prospettive a livello di area e globale richiedono l'integrazione dei dati e quindi un consolidamento centralizzato degli stessi dati dei messaggi valutati nei rispettivi footprint regionali per la prospettiva locale. 

![Consolidamento](media/service-bus-federation-overview/merge.jpg)

La normalizzazione è uno scenario di consolidamento, in base al quale due o più sequenze di messaggi in ingresso contengono lo stesso tipo di informazioni, ma con strutture diverse o codifiche diverse e i messaggi devono essere transcodificati o trasformati prima di poter essere utilizzati. 

La normalizzazione può includere anche il lavoro crittografico, ad esempio la decrittografia dei payload crittografati end-to-end e la nuova crittografia con chiavi e algoritmi diversi per i destinatari downstream. 

### <a name="splitting-and-routing"></a>Suddivisione e routing

Gli argomenti del bus di servizio e le relative regole di sottoscrizione vengono spesso usati per filtrare un flusso di messaggi per un determinato gruppo di destinatari e quindi ottenere il set filtrato da una sottoscrizione. 

![Separazione in corso](media/service-bus-federation-overview/split.jpg)

In un sistema globale in cui i destinatari di tali messaggi sono distribuiti a livello globale o appartengono a applicazioni diverse, è possibile utilizzare la replica per trasferire messaggi da tale sottoscrizione a una coda o a un argomento in uno spazio dei nomi diverso da quello in cui vengono utilizzati.

### <a name="replication-applications-in-azure-functions"></a>Applicazioni di replica in funzioni di Azure

Per implementare i modelli precedenti, è necessario un ambiente di esecuzione scalabile e affidabile per le attività di replica che si desidera configurare ed eseguire. In Azure, l'ambiente di runtime più adatto per le attività senza stato è [funzioni di Azure](../azure-functions/functions-overview.md). 

Funzioni di Azure può essere eseguito con un' [identità gestita di Azure](../active-directory/managed-identities-azure-resources/overview.md) in modo che le attività di replica possano essere integrate con le regole di controllo degli accessi in base al ruolo dei servizi di origine e di destinazione senza dover gestire i segreti lungo il percorso di replica. Per le origini e le destinazioni di replica che richiedono credenziali esplicite, funzioni di Azure può conservare i valori di configurazione per tali credenziali in un archivio strettamente controllato dall'accesso all'interno di [Azure Key Vault](../key-vault/general/overview.md).

Funzioni di Azure consente inoltre di integrare direttamente le attività di replica con le reti virtuali di Azure e gli [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) per tutti i servizi di messaggistica di Azure ed è prontamente integrato con [monitoraggio di Azure](../azure-monitor/overview.md).

Soprattutto, funzioni di Azure dispone di trigger e associazioni di output precompilati e scalabili per [Hub eventi](../azure-functions/functions-bindings-service-bus.md)di Azure, [Hub Azure, Hub](../azure-functions/functions-bindings-event-iot.md), Azure [Service Bus](../azure-functions/functions-bindings-service-bus.md), [griglia di eventi](../azure-functions/functions-bindings-event-grid.md)di Azure e [archiviazione code di Azure](../azure-functions/functions-bindings-storage-queue.md), estensioni personalizzate per [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)e [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). La maggior parte dei trigger si adatta dinamicamente alle esigenze di velocità effettiva scalando il numero di istanze in esecuzione simultanea verso l'alto e verso il basso in base alle metriche documentate. 

Con il piano a consumo di funzioni di Azure, i trigger predefiniti possono anche essere ridimensionati fino a zero, mentre nessun messaggio è disponibile per la replica, il che significa che non vengono addebitati costi per mantenere la configurazione pronta per la scalabilità del backup. Il svantaggio principale dell'utilizzo del piano a consumo è che la latenza per le attività di replica "riattivazione" da questo stato è significativamente superiore a quella dei piani di hosting in cui viene mantenuta l'infrastruttura.  

A differenza di tutti questi, i motori di replica più comuni per la messaggistica e la gestione degli eventi, ad esempio [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) di Apache Kafka, richiedono un ambiente di hosting e scalare autonomamente il motore di replica. Ciò include la configurazione e l'integrazione delle funzionalità di sicurezza e di rete e l'agevolazione del flusso dei dati di monitoraggio, quindi non si ha ancora la possibilità di inserire attività di replica personalizzate nel flusso. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata esaminata una gamma di modelli di Federazione e viene illustrato il ruolo di funzioni di Azure come il runtime di replica di eventi e messaggistica in Azure. 

A questo punto, si potrebbe voler leggere come configurare un'applicazione Replicator con funzioni di Azure e quindi come replicare i flussi di eventi tra hub eventi e altri sistemi di messaggistica e gestione degli eventi:

- [Applicazioni di replica in funzioni di Azure](service-bus-federation-replicator-functions.md)
- [Replica di eventi tra entità del bus di servizio](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Routing degli eventi a hub eventi di Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Acquisire eventi da Hub eventi di Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif