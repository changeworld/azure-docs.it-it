---
title: Federazione multisito e più aree-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica della Federazione multisito e in più aree con hub eventi di Azure.
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861450"
---
# <a name="multi-site-and-multi-region-federation"></a>Federazione multisito e multiarea

Molte soluzioni sofisticate richiedono che gli stessi flussi di eventi siano resi disponibili per l'utilizzo in più posizioni o che richiedono la raccolta di flussi di eventi in più posizioni e quindi consolidati in una posizione specifica per l'utilizzo. Spesso è anche necessario arricchire o ridurre i flussi di eventi o eseguire conversioni di formato evento, anche per all'interno di una singola area e soluzione.

In pratica, ciò significa che la soluzione manterrà più hub eventi, spesso in aree diverse e spazi dei nomi di hub eventi e quindi replica gli eventi tra di essi. È anche possibile scambiare eventi con origini e destinazioni come il [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md), l' [Hub azure](../iot-fundamentals/iot-introduction.md)o l' [Apache Kafka](https://kafka.apache.org). 

La gestione di più hub eventi attivi in aree diverse consente inoltre ai client di scegliere e passare da un'area all'altra se il relativo contenuto viene Unito, rendendo il sistema globale più resiliente ai problemi di disponibilità a livello di area.

Questo capitolo della "Federazione" illustra i modelli di Federazione e come realizzare questi modelli usando l' [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) senza server o i runtime di [funzioni di Azure][1] , con la possibilità di avere una propria trasformazione o codice di arricchimento direttamente nel percorso del flusso di eventi. 

## <a name="federation-patterns"></a>Modelli di Federazione

Ci sono molte potenziali motivazioni per i motivi per cui potrebbe essere necessario spostare gli eventi tra diversi hub eventi o altre origini e destinazioni e vengono enumerati i modelli più importanti in questa sezione e vengono inoltre forniti collegamenti a linee guida più dettagliate per il rispettivo modello. 

- [Resilienza per gli eventi di disponibilità a livello di area](#resiliency-against-regional-availability-events)
- [Ottimizzazione della latenza](#latency-optimization)
- [Convalida, riduzione e arricchimento](#validation-reduction-and-enrichment)
- [Integrazione con i servizi di analisi](#integration-with-analytics-services)
- [Consolidamento e normalizzazione dei flussi di eventi](#consolidation-and-normalization-of-event-streams)
- [Suddivisione e routing dei flussi di eventi](#splitting-and-routing-of-event-streams)
- [Proiezioni di log](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>Resilienza per gli eventi di disponibilità a livello di area 

![Disponibilità a livello di area](media/event-hubs-federation-overview/regional-availability.jpg)

Sebbene la disponibilità e l'affidabilità massime siano le principali priorità operative per gli hub eventi, esistono tuttavia molti modi in cui un Producer o un consumatore potrebbe non essere in grado di comunicare con l'hub eventi "primario" assegnato a causa di problemi di rete o di risoluzione dei nomi o se un hub eventi potrebbe effettivamente non rispondere o restituire errori. 

Tali condizioni non sono "disastrose", in modo che sia possibile abbandonare la distribuzione a livello di area come si farebbe in una situazione di [ripristino di emergenza](event-hubs-geo-dr.md) , ma lo scenario aziendale di alcune applicazioni potrebbe essere già influenzato dagli eventi di disponibilità che durano non più di pochi minuti o addirittura secondi. 

Esistono due modelli di base per risolvere questi scenari:

- Il modello di [replica][4] riguarda la replica del contenuto di un hub eventi primario in un hub eventi secondario, in cui l'hub eventi primario viene in genere usato dall'applicazione per la produzione e l'utilizzo di eventi e il database secondario funge da opzione di fallback nel caso in cui l'hub eventi primario non sia più disponibile. Poiché la replica è unidirezionale, dal database primario a quello secondario, un passaggio di Producer e consumer da un database primario non disponibile a quello secondario provocherà la mancata ricezione di nuovi eventi da parte del database primario precedente e pertanto non sarà più aggiornata.
  La replica pura è pertanto adatta solo agli scenari di failover unidirezionali. Una volta eseguito il failover, il database primario precedente viene abbandonato ed è necessario creare un nuovo hub eventi secondario in un'area di destinazione diversa.
- Il modello di [Unione][5] estende il modello di replica eseguendo un'Unione continua del contenuto di due o più hub eventi. Ogni evento prodotto originariamente in uno degli hub eventi incluso nello schema viene replicato negli altri hub eventi. Quando gli eventi vengono replicati, vengono annotati in modo che vengano successivamente ignorati dal processo di replica della destinazione di replica. I risultati dell'uso del modello merge sono due o più hub eventi che conterranno lo stesso set di eventi in un modo coerente. 
  
In entrambi i casi, il contenuto degli hub eventi non sarà identico. Gli eventi di qualsiasi Producer e raggruppati in base alla stessa chiave di partizione verranno visualizzati nello stesso ordine relativo in cui sono stati originariamente inviati, ma l'ordine assoluto degli eventi potrebbe variare. Ciò vale soprattutto per gli scenari in cui il numero di partizioni degli hub eventi di origine e di destinazione è diverso, che è auspicabile per diversi modelli estesi descritti qui. Una [barra di divisione o un router](#splitting-and-routing-of-event-streams) può ottenere una sezione di un hub eventi molto più grande con centinaia di partizioni e imbuto in un hub eventi di dimensioni ridotte con poche partizioni, più adatte per gestire il subset con risorse di elaborazione limitate. Viceversa, un [consolidamento](#consolidation-and-normalization-of-event-streams) può ingrandire i dati da diversi hub eventi più piccoli in un singolo hub eventi più grande con più partizioni per soddisfare le esigenze di elaborazione e velocità effettiva consolidate.

Il criterio per mantenere insieme gli eventi è la chiave di partizione e non l'ID partizione originale. Altre considerazioni sull'ordine relativo e su come eseguire un failover da un hub eventi a quello successivo senza basarsi sullo stesso ambito degli offset di flusso sono illustrate nella descrizione del modello di [replica][4] .


Materiale sussidiario: 
- [Modello di replica][4]
- [Modello Unione][5]

### <a name="latency-optimization"></a>Ottimizzazione della latenza 

![Ottimizzazione della latenza](media/event-hubs-federation-overview/latency-optimization.jpg)  

I flussi di eventi vengono scritti una volta dai producer, ma possono essere letti un numero qualsiasi di volte dai consumer di eventi. Per gli scenari in cui un flusso di eventi in un'area è condiviso da più consumer ed è necessario accedervi più volte durante l'elaborazione dell'analisi che risiede in un'area diversa o in tutte le richieste che potrebbero decarere gli utenti simultanei, potrebbe essere utile inserire una copia del flusso di eventi vicino al processore di analisi per ridurre la latenza del round trip. 

Gli esempi appropriati per la replica da preferire per l'utilizzo di eventi in modalità remota da diverse aree sono soprattutto quelli in cui le aree sono molto distanti, ad esempio Europa e Australia sono quasi antipodi, geograficamente e latenze di rete possono superare facilmente 250 ms per qualsiasi round trip.
Non puoi accelerare la velocità della luce, ma puoi ridurre il numero di round trip a latenza elevata per interagire con i dati.

Materiale sussidiario: 
- [Modello di replica][4]

### <a name="validation-reduction-and-enrichment"></a>Convalida, riduzione e arricchimento

![Convalida, riduzione, arricchimento](media/event-hubs-federation-overview/validation-enrichment.jpg)  

I flussi di eventi possono essere inviati a un hub eventi da client esterni alla propria soluzione. Tali flussi di eventi possono richiedere la verifica della conformità degli eventi inviati esternamente a un determinato schema e per l'eliminazione di eventi non conformi. 

Negli scenari in cui i client hanno una larghezza di banda molto limitata, in molti scenari di "Internet delle cose" con larghezza di banda a consumo o in cui gli eventi vengono inviati originariamente su reti non IP con dimensioni di pacchetti vincolati, è possibile che gli eventi debbano essere arricchiti con i dati di riferimento per aggiungere altro contesto per poter essere usati dai processori di eventi downstream.

In altri casi, in particolare quando vengono consolidati i flussi, è possibile che i dati dell'evento debbano essere ridotti in complessità o dimensioni ridotte omettendo alcuni dettagli.

Una di queste operazioni può verificarsi nell'ambito dei flussi di replica, consolidamento o Unione. 

Materiale sussidiario: 
- [Modello di editor][6]

### <a name="integration-with-analytics-services"></a>Integrazione con i servizi di analisi

![Integrazione con i servizi di analisi](media/event-hubs-federation-overview/integration.jpg)

Molti dei servizi di analisi nativa del cloud di Azure, ad esempio analisi di flusso di Azure o sinapsi di Azure, funzionano meglio con i dati trasmessi o pre-batch serviti da Hub eventi di Azure e hub eventi di Azure consente anche l'integrazione con diversi pacchetti di analisi open source, ad esempio Apache Samza, Apache Flink, Apache Spark e Apache Storm. 

Se la soluzione USA principalmente il bus di servizio o griglia di eventi, è possibile rendere questi eventi facilmente accessibili a questi sistemi analitici e anche per l'archiviazione con l'acquisizione di hub eventi se li si inserisce nell'hub eventi. Griglia di eventi può eseguire questa operazione in modo nativo con l' [integrazione dell'hub eventi](../event-grid/handler-event-hubs.md), per il bus di servizio attenersi alle [linee guida](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)per la replica del bus di servizio.

Analisi di flusso [di Azure si integra direttamente con hub eventi](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs).

Materiale sussidiario: 
- [Modello di replica][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>Consolidamento e normalizzazione dei flussi di eventi

![Consolidamento e normalizzazione dei flussi di eventi](media/event-hubs-federation-overview/consolidation.jpg)

Le soluzioni globali sono spesso costituite da footprint locali che sono in gran parte indipendenti, incluse le proprie funzionalità di analisi, ma le prospettive di analisi a livello di area e di analisi globale richiedono una prospettiva integrata ed è per questo motivo che un consolidamento centrale degli stessi flussi di eventi viene valutato nei rispettivi footprint regionali per la prospettiva locale. 

La normalizzazione è uno scenario di consolidamento, in base al quale due o più flussi di eventi in ingresso contengono lo stesso tipo di eventi, ma con strutture diverse o codifiche diverse e gli eventi vengono maggiormente transcodificati o trasformati prima di poter essere utilizzati. 

La normalizzazione può includere anche il lavoro crittografico, ad esempio la decrittografia dei payload crittografati end-to-end e la nuova crittografia con chiavi e algoritmi diversi per i destinatari downstream. 

Materiale sussidiario: 
- [Modello Unione][5]
- [Modello di editor][6]

### <a name="splitting-and-routing-of-event-streams"></a>Suddivisione e routing dei flussi di eventi

![Suddivisione e routing dei flussi di eventi](media/event-hubs-federation-overview/splitting.jpg)

Hub eventi di Azure viene occasionalmente usato negli scenari di stile "pubblicazione-sottoscrizione" in cui un torrent in ingresso di eventi inseriti supera la capacità del bus di servizio di Azure o di griglia di eventi di Azure, entrambi dotati di funzionalità di distribuzione e filtro nativi di pubblicazione-sottoscrizione e sono preferiti per questo modello. 

Mentre una vera e propria funzionalità di pubblicazione-sottoscrizione lascia ai sottoscrittori di selezionare gli eventi desiderati, il modello di suddivisione presenta gli eventi di mapping del Producer alle partizioni da un modello di distribuzione predeterminato e i consumer designati eseguono il pull esclusivamente dalla partizione "their". Con l'hub eventi che memorizza nel buffer il traffico complessivo, il contenuto di una determinata partizione, che rappresenta una frazione del volume della velocità effettiva originale, può quindi essere replicato in una coda per un consumo di consumer affidabile, transazionale e concorrente.

Molti scenari in cui gli hub eventi vengono usati principalmente per lo spostamento degli eventi all'interno di un'applicazione all'interno di un'area sono casi in cui gli eventi Select, forse solo da una singola partizione, devono essere resi disponibili altrove. Questo scenario è simile allo scenario di suddivisione, ma potrebbe usare un router scalabile che considera tutti i messaggi in arrivo in un hub eventi e Cherry-Picks solo alcuni per il routing in avanti e potrebbero distinguere le destinazioni di routing in base ai metadati o al contenuto dell'evento. 

Materiale sussidiario:
- [Modello di routing][7]

### <a name="log-projections"></a>Proiezioni di log 

![Proiezione log](media/event-hubs-federation-overview/log-projection.jpg)

In alcuni scenari, è necessario avere accesso al valore più recente inviato per qualsiasi sottoflusso di un evento e comunemente contraddistinto dalla chiave di partizione. In Apache Kafka, questa operazione viene spesso eseguita abilitando la "compattazione dei log" in un argomento, che elimina tutti gli eventi tranne quelli più recenti con una chiave univoca. L'approccio di compattazione dei log presenta tre svantaggi di composizione: 

- La compattazione richiede una riorganizzazione continua del log, che è un'operazione eccessivamente costosa per un broker ottimizzato per carichi di lavoro di sola Accodamento. 
- La compattazione è distruttiva e non consente una prospettiva compattata e non compatta dello stesso flusso.
- Un flusso compresso ha ancora un modello di accesso sequenziale, ovvero la ricerca del valore desiderato nel log richiede la lettura dell'intero log nel peggiore dei casi, che in genere porta a ottimizzazioni che implementano il modello esatto presentato qui: proiezione del contenuto del log in un database o in una cache. 

In definitiva, un log compresso è un archivio chiave-valore e, di conseguenza, è l'opzione di implementazione peggiore possibile per tale archivio. È molto più efficiente per le ricerche e per le query per creare e usare una proiezione permanente del log in un archivio chiave-valore appropriato o in un altro database. 

Poiché gli eventi non sono modificabili e l'ordine viene sempre mantenuto in un log, qualsiasi proiezione di un log in un archivio chiave-valore sarà sempre identica per lo stesso intervallo di eventi, vale a dire che una proiezione mantenuta aggiornata fornisce sempre una visualizzazione autorevole e non esiste mai una buona ragione per ricompilarla dal contenuto del log dopo la compilazione. 

Materiale sussidiario:
- [Proiezione log][8]

## <a name="replication-application-technologies"></a>Tecnologie per le applicazioni di replica

Per implementare i modelli precedenti, è necessario un ambiente di esecuzione scalabile e affidabile per le attività di replica che si desidera configurare ed eseguire. In Azure, gli ambienti di runtime più adatti per queste attività sono attività senza stato sono [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) per attività di replica di flusso con stato e [funzioni di Azure](../azure-functions/functions-overview.md) per le attività di replica senza stato.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Applicazioni di replica con stato in analisi di flusso di Azure

Per le applicazioni di replica con stato che devono prendere in considerazione le relazioni tra gli eventi, creare eventi compositi, arricchire gli eventi o ridurre gli eventi, creare aggregazioni di dati e trasformare i payload degli eventi, [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) è l'opzione di implementazione migliore.

In analisi di flusso di Azure è possibile [creare processi](../stream-analytics/stream-analytics-quick-create-portal.md) che integrano [input](../stream-analytics/stream-analytics-add-inputs.md) e [output](../stream-analytics/stream-analytics-define-outputs.md) e integrano i dati dagli input tramite [query](/stream-analytics-query/stream-analytics-query-language-reference) che restituiscono un risultato che viene quindi reso disponibile negli output.

Le query sono basate sul [linguaggio di query SQL](/stream-analytics-query/stream-analytics-query-language-reference) e possono essere usate per filtrare, ordinare, aggregare e unire facilmente i dati in streaming in un determinato periodo di tempo. È anche possibile estendere questo linguaggio SQL con [funzioni definite dall'utente (UDF)](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md) [JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) e C#. È possibile modificare facilmente le opzioni di ordinamento degli eventi e la durata delle finestre temporali durante l'esecuzione di operazioni di aggregazione tramite semplici costrutti di linguaggio e/o configurazioni.

Ogni processo include uno o più output dei dati trasformati ed è possibile controllare cosa avviene in risposta alle informazioni analizzate. Ad esempio, è possibile:

- Inviare dati a servizi quali Funzioni di Azure, argomenti del bus di servizio o code per attivare comunicazioni o flussi di lavoro personalizzati a valle.
- Inviare dati a un dashboard di Power BI per la visualizzazione nel dashboard in tempo reale.
- Archiviare i dati in altri servizi di archiviazione di Azure, ad esempio Azure Data Lake, Azure sinapsi Analytics e così via, per eseguire analisi batch o eseguire il training di modelli di Machine Learning in base a pool di dati cronologici molto grandi e indicizzati.
- Le proiezioni di archivio (denominate anche "viste materializzate") nei database ([database SQL](../stream-analytics/sql-database-output.md), [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ).

### <a name="stateless-replication-applications-in-azure-functions"></a>Applicazioni di replica senza stato in funzioni di Azure

Per le attività di replica senza stato in cui si vuole eseguire il provisioning degli eventi senza considerare i payload o elaborarli singolarmente senza dover prendere in considerazione le relazioni degli eventi (ad eccezione dell'ordine relativo), è possibile usare funzioni di Azure, che offrono una grande flessibilità.

Funzioni di Azure dispone di trigger e associazioni di output predefiniti e scalabili per [Hub eventi](../azure-functions/functions-bindings-event-hubs.md)di Azure, [Hub Azure, Hub](../azure-functions/functions-bindings-event-iot.md), Azure [Service Bus](../azure-functions/functions-bindings-service-bus.md), [griglia di eventi](../azure-functions/functions-bindings-event-grid.md)di Azure e [archiviazione code di Azure](../azure-functions/functions-bindings-storage-queue.md), nonché estensioni personalizzate per [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)e [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). La maggior parte dei trigger si adatta dinamicamente alle esigenze di velocità effettiva scalando il numero di istanze in esecuzione simultanea verso l'alto e verso il basso in base alle metriche documentate. 

Per la creazione di proiezioni di log, funzioni di Azure supporta le associazioni di output per [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) e l' [archiviazione tabelle di Azure](../azure-functions/functions-bindings-storage-table-output.md).

Funzioni di Azure può essere eseguito in un' [identità gestita di Azure](../active-directory/managed-identities-azure-resources/overview.md) e, in questo, può conservare i valori di configurazione per le credenziali nell'archiviazione controllata in maniera rigorosa all'interno di [Azure Key Vault](../key-vault/general/overview.md).

Funzioni di Azure consente inoltre di integrare direttamente le attività di replica con le reti virtuali di Azure e gli [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) per tutti i servizi di messaggistica di Azure ed è prontamente integrato con [monitoraggio di Azure](../azure-monitor/overview.md).

Con il piano a consumo di funzioni di Azure, i trigger predefiniti possono anche essere ridimensionati fino a zero mentre nessun messaggio è disponibile per la replica, il che significa che non sono previsti costi per mantenere la configurazione pronta per la scalabilità del backup; il svantaggio principale dell'utilizzo del piano a consumo è che la latenza per le attività di replica "riattivazione" da questo stato è significativamente superiore a quella dei piani di hosting in cui viene mantenuta l'infrastruttura.  

A differenza di tutti questi, i motori di replica più comuni per la messaggistica e la gestione degli eventi, ad esempio [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) di Apache Kafka, richiedono un ambiente di hosting e scalare autonomamente il motore di replica. Ciò include la configurazione e l'integrazione delle funzionalità di sicurezza e di rete e l'agevolazione del flusso dei dati di monitoraggio, quindi non si ha ancora la possibilità di inserire attività di replica personalizzate nel flusso. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Scelta tra funzioni di Azure e analisi di flusso di Azure

Analisi di flusso di Azure (ASA) è la scelta migliore quando è necessario elaborare il payload degli eventi durante la replica. ASA può copiare gli eventi uno per uno oppure può creare aggregazioni che condensano le informazioni dei flussi di eventi prima di inviarle. Si può [facilmente appoggiare ai dati di riferimento](../stream-analytics/stream-analytics-use-reference-data.md) contenuti nell'archivio BLOB di Azure o nel database SQL di Azure senza dover importare tali dati in un flusso.

Con ASA, è possibile creare facilmente viste persistenti e materializzate dei flussi nei database con iperscalabilità. Si tratta di un approccio molto superiore al modello di "compressione dei log" goffo di Apache Kafka e alle proiezioni di tabella sul lato client volatili dei flussi Kafka. 

ASA può elaborare prontamente gli eventi con i payload codificati nei [formati CSV, JSON e Apache avro](../stream-analytics/stream-analytics-parsing-json.md) ed è possibile collegare [deserializzatori personalizzati](../stream-analytics/custom-deserializer.md) per qualsiasi altro formato.

Per tutte le attività di replica in cui si vogliono copiare i flussi di eventi "così come sono" e senza toccare i payload, oppure se è necessario implementare un router, eseguire operazioni di crittografia, modificare la codifica dei payload o, se necessario, per il controllo completo sul contenuto del flusso di dati, funzioni di Azure è l'opzione migliore.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata esaminata una gamma di modelli di Federazione e viene illustrato il ruolo di funzioni di Azure come il runtime di replica di eventi e messaggistica in Azure.

A questo punto, si potrebbe voler leggere come configurare un'applicazione Replicator con analisi di flusso di Azure o funzioni di Azure e come replicare i flussi di eventi tra hub eventi e altri sistemi di messaggistica e gestione degli eventi:

- [Modelli di attività di replica di eventi][10]
- [Elaborare dati con analisi di flusso di Azure][9]
- [Applicazioni di Event Replicator in funzioni di Azure][1]
- [Replica di eventi tra hub eventi][2]
- [Replica di eventi nel bus di servizio di Azure][3]
- [Usare Apache Kafka MirrorMaker con hub eventi][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md