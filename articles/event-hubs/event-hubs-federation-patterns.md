---
title: Modelli di attività di replica degli eventi-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce indicazioni dettagliate per l'implementazione di modelli di attività di replica di eventi specifici
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 438964c228f060dede93abf582c9504b698db8b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934612"
---
# <a name="event-replication-tasks-patterns"></a>Modelli di attività di replica degli eventi

I Cenni preliminari sulla [Federazione](event-hubs-federation-overview.md) e sulle [funzioni Replicator](event-hubs-federation-replicator-functions.md) illustrano la logica per e gli elementi di base delle attività di replica ed è consigliabile acquisire familiarità con questi elementi prima di continuare con questo articolo.

In questo articolo vengono illustrate in dettaglio le linee guida di implementazione per diversi modelli evidenziati nella sezione Panoramica.

## <a name="replication"></a>Replica

Il modello di replica copia gli eventi da un hub eventi a quello successivo o da un hub eventi a un'altra destinazione, ad esempio una coda del bus di servizio. Gli eventi vengono trasmessi senza apportare alcuna modifica al payload dell'evento.

L'implementazione di questo modello è coperta dalla [replica degli eventi tra gli hub eventi](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) e la [replica degli eventi tra gli hub eventi e](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) gli esempi del bus di servizio e l'esercitazione [usare Apache Kafka MirrorMaker con hub eventi](event-hubs-kafka-mirror-maker-tutorial.md) per il caso specifico di replica dei dati da un broker di Apache Kafka a hub eventi.

### <a name="streams-and-order-preservation"></a>Flussi e conservazione degli ordini

La replica, tramite funzioni di Azure o analisi di flusso di Azure, non mira a garantire la creazione di cloni 1:1 esatti di un hub eventi di origine in un hub eventi di destinazione, ma è incentrato sulla conservazione dell'ordine relativo degli eventi in cui l'applicazione lo richiede. L'applicazione comunica questo problema raggruppando gli eventi correlati con la stessa chiave di partizione e gli [Hub eventi dispone i messaggi con la stessa chiave di partizione sequenziale nella stessa partizione](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> Le informazioni relative all'offset sono univoche per ogni hub eventi e gli offset per gli stessi eventi sono diversi nelle istanze di hub eventi. Per individuare una posizione in un flusso di eventi copiato, utilizzare offset basati sul tempo e fare riferimento ai [metadati assegnati al servizio propagati](#service-assigned-metadata).
>
> Gli offset basati sul tempo avviano il ricevitore in un momento specifico:
> - *EventPosition. FromStart ()* -leggere nuovamente tutti i dati conservati.
> - *EventPosition. FromEnd ()* : legge tutti i nuovi dati dal momento della connessione.
> - *EventPosition. FromEnqueuedTime (DateTime)* : tutti i dati a partire da una data e un'ora specificate.
>
> In EventProcessor è possibile impostare la posizione tramite InitialOffsetProvider in EventProcessorOptions. Con le altre API del ricevitore, la posizione viene passata tramite il costruttore. 


Gli helper della funzione di replica predefiniti [forniti come esempi](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) usati nelle linee guida basate su funzioni di Azure assicurano che i flussi di eventi con la stessa chiave di partizione recuperata da una partizione di origine vengano inviati nell'hub eventi di destinazione come batch nel flusso originale e con la stessa chiave di partizione.

Se il numero di partizioni dell'hub eventi di origine e di destinazione è identico, tutti i flussi nella destinazione eseguiranno il mapping alle stesse partizioni che facevano nell'origine.
Se il numero di partizioni è diverso, che è importante in alcuni degli altri modelli descritti nell'esempio seguente, il mapping sarà diverso, ma i flussi vengono sempre mantenuti insieme e in ordine.

L'ordine relativo degli eventi appartenenti a flussi diversi o a eventi indipendenti senza una chiave di partizione in una partizione di destinazione può essere sempre diverso dalla partizione di origine.

### <a name="service-assigned-metadata"></a>Metadati assegnati dal servizio

I metadati assegnati dal servizio di un evento ottenuto dall'hub eventi di origine, dall'ora di Accodamento originale, dal numero di sequenza e dall'offset, vengono sostituiti da nuovi valori assegnati dal servizio nell'hub eventi di destinazione, ma con le [funzioni di supporto](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication), le attività di replica fornite negli esempi, i valori originali vengono conservati nelle proprietà utente: `repl-enqueue-time` (stringa ISO8601), `repl-sequence` , `repl-offset` .

Queste proprietà sono di tipo stringa e contengono il valore file delle rispettive proprietà originali. Se l'evento viene inviato più volte, i metadati assegnati al servizio dell'origine immediata vengono aggiunti alle proprietà già esistenti, con valori separati da punti e virgola.

### <a name="failover"></a>Failover

Se si usa la replica per finalità di ripristino di emergenza, per proteggersi dagli eventi di disponibilità a livello di area nel servizio Hub eventi o dalle interruzioni di rete, in uno scenario di questo tipo è necessario eseguire un failover da un hub eventi alla successiva, indicando a produttori e/o consumer di usare l'endpoint secondario.

Per tutti gli scenari di failover si presuppone che gli elementi obbligatori degli spazi dei nomi siano strutturalmente identici, ovvero che hub eventi e gruppi di consumer abbiano un nome identico e che le regole di firma di accesso condiviso e/o le regole di controllo degli accessi in base al ruolo siano configurate nello stesso modo. È possibile creare (e aggiornare) uno spazio dei nomi secondario seguendo le [linee guida per lo spostamento degli spazi dei nomi](move-across-regions.md) e l'omissione del passaggio di pulizia.

Per fare in modo che i producer e i consumer commutino, è necessario rendere disponibili le informazioni sullo spazio dei nomi da utilizzare per la ricerca in una posizione facile da raggiungere e aggiornare. Se i producer o i consumer riscontrano errori frequenti o persistenti, devono consultare tale posizione e modificare la configurazione. Sono disponibili diversi modi per condividere la configurazione, ma sono presenti due elementi: DNS e condivisioni file.

#### <a name="dns-based-failover-configuration"></a>Configurazione del failover basato su DNS

Un approccio candidato consiste nel conservare le informazioni nei record SRV DNS in un DNS controllato e puntare ai rispettivi endpoint dell'hub eventi. 

> [!IMPORTANT] 
> Si noti che hub eventi non consente l'uso diretto degli endpoint con record CNAME, ovvero si userà DNS come meccanismo di ricerca resiliente per gli indirizzi degli endpoint e non per risolvere direttamente le informazioni sugli indirizzi IP.

Si supponga di essere proprietari del dominio `example.com` e, per l'applicazione, una zona `test.example.com` . Per due hub eventi alternativi, verranno ora create altre due zone annidate e un record SRV in ogni.

I record SRV sono, in base alla convenzione comune, preceduti da `_azure_eventhubs._amqp` e contengono due record dell'endpoint: uno per AMQP-over-TLS sulla porta 5671 e uno per AMQP-over-WebSocket sulla porta 443, entrambi puntano all'endpoint di hub eventi dello spazio dei nomi corrispondente alla zona.

| Zona                   | Record SRV                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

Nella zona dell'applicazione si creerà quindi una voce CNAME che punta alla zona subordinata corrispondente all'hub eventi primario:

| Record CNAME                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

L'uso di un client DNS che consente di eseguire query in modo esplicito in record CNAME e SRV (i client predefiniti di Java e .NET consentono solo una semplice risoluzione dei nomi agli indirizzi IP), quindi è possibile risolvere l'endpoint desiderato. Con [DnsClient.NET](https://dnsclient.michaco.net/), ad esempio, la funzione Lookup è:

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

La funzione restituisce il nome host di destinazione registrato per la porta 5671 della zona a cui è attualmente associato l'alias CNAME, come illustrato in precedenza.

Per eseguire un failover è necessario modificare il record CNAME e puntarlo alla zona alternativa.

Il vantaggio dell'uso di DNS e in particolare del [DNS di Azure](../dns/dns-overview.md)consiste nel fatto che le informazioni DNS di Azure vengono replicate a livello globale e pertanto resilienti in caso di interruzioni in una singola area.

Questa procedura è simile al funzionamento del [ripristino di emergenza geografico di hub eventi](event-hubs-geo-dr.md) , ma completamente sotto il proprio controllo e funziona anche con scenari attivi/attivi.

#### <a name="file-share-based-failover-configuration"></a>Configurazione del failover basato su condivisione file

L'alternativa più semplice all'uso del DNS per la condivisione delle informazioni sugli endpoint consiste nell'inserire il nome dell'endpoint primario in un file di testo normale e fornire il file da un'infrastruttura che sia affidabile rispetto alle interruzioni e che consenta comunque gli aggiornamenti.

Se è già stata eseguita un'infrastruttura di siti Web a disponibilità elevata con la disponibilità globale e la replica del contenuto, aggiungere tale file e ripubblicare il file se è necessaria un'opzione.

> [!CAUTION]
> È consigliabile pubblicare il nome dell'endpoint in questo modo, non una stringa di connessione completa, inclusi i segreti.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Considerazioni aggiuntive per il failover dei consumer

Per i consumer dell'hub eventi, ulteriori considerazioni sulla strategia di failover dipendono dalle esigenze del processore di eventi.

Se si verifica un'emergenza che richiede la ricompilazione di un sistema, inclusi i database, i dati di backup e i database vengono inseriti direttamente o tramite l'elaborazione intermedia dagli eventi contenuti nell'hub eventi, il backup viene ripristinato e quindi si desidera avviare la riproduzione degli eventi nel sistema dal momento in cui è stato creato il backup del database e non dal momento in cui il sistema originale è stato distrutto

Se un errore interessa solo una sezione di un sistema o un singolo hub eventi, che è diventato irraggiungibile, è probabile che si voglia continuare a elaborare gli eventi da circa la stessa posizione in cui è stata interrotta l'elaborazione.

Per realizzare uno scenario e usare il processore di eventi del rispettivo Azure SDK, [viene creato un nuovo archivio Checkpoint](event-processor-balance-partition-load.md#checkpointing) e viene fornita una posizione iniziale per la partizione, in base al _timestamp_ da cui si vuole riprendere l'elaborazione.

Se è ancora possibile accedere all'archivio dei checkpoint dell'hub eventi da cui si è passati, i [metadati propagati](#service-assigned-metadata) descritti in precedenza consentiranno di ignorare gli eventi che sono già stati gestiti e riprendere con precisione dal punto in cui è stato interrotto.

## <a name="merge"></a>Unione

Il modello di Unione ha una o più attività di replica che puntano a una destinazione, possibilmente simultaneamente ai producer normali che inviano eventi alla stessa destinazione.

Le varianti di questi schemi sono:

- Due o più funzioni di replica acquisiscono simultaneamente gli eventi da origini separate e li inviano alla stessa destinazione.
- Una funzione di replica acquisisce gli eventi da un'origine mentre la destinazione viene utilizzata anche direttamente dai producer.
- Il modello precedente, ma con mirroring tra due o più hub eventi, generando gli hub eventi che contengono gli stessi flussi, indipendentemente dalla posizione in cui vengono generati gli eventi.

Le prime due varianti di modello sono semplici e non differiscono dalle attività di replica normale.

Per l'ultimo scenario è necessario escludere gli eventi già replicati dalla replica. La tecnica è illustrata e illustrata nell'esempio [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) .

## <a name="editor"></a>Editor

Il modello di editor si basa sul modello di [replica](#replication) , ma i messaggi vengono modificati prima di essere inviati. 

Esempi di modifiche di questo tipo sono:

- **_Transcodifica_** : se il contenuto dell'evento (anche definito "corpo" o "payload") arriva dall'origine codificata usando il formato _Apache avro_ o un formato di serializzazione proprietario, tuttavia, l'aspettativa del sistema proprietario della destinazione è il fatto che il contenuto sia codificato in _JSON_ , un'attività di replica di transcodifica prima deserializza il payload da _Apache avro_ in un oggetto grafico in memoria e quindi serializza il grafico nel formato _JSON_ per l'evento che viene inviato. La transcodifica include anche attività di compressione e decompressione del **contenuto** .
- **_Trasformazione_** : gli eventi che contengono dati strutturati possono richiedere la ridefinizione dei dati per un consumo più semplice da parte dei consumer downstream. Questo può comportare un lavoro analogo alla Flating di strutture annidate, all'eliminazione di elementi dati estranei o alla ridefinizione del payload per adattarlo esattamente a un determinato schema.
- **_Batch_** : gli eventi possono essere ricevuti in batch (più eventi in un singolo trasferimento) da un'origine, ma devono essere inviati singolarmente a una destinazione o viceversa. Un'attività può quindi inviare più eventi in base a un singolo trasferimento di eventi di input o aggregare un set di eventi che vengono quindi trasferiti insieme.
- **_Convalida_** : i dati degli eventi da origini esterne spesso devono essere controllati per verificare se sono conformi a un set di regole prima di poter essere trasmessi. Le regole possono essere espresse utilizzando schemi o codice. Gli eventi che non sono conformi possono essere eliminati, con il problema indicato nei log oppure possono essere inoltrati a una destinazione di destinazione speciale per gestirli ulteriormente.
- **_Arricchimento_** : i dati degli eventi provenienti da alcune origini possono richiedere l'arricchimento con ulteriore contesto per poter essere usati nei sistemi di destinazione. Questo può comportare la ricerca di dati di riferimento e l'incorporamento di tali dati con l'evento o l'aggiunta di informazioni sull'origine note all'attività di replica, ma non contenute negli eventi.
- **_Filtro_** : è possibile che alcuni eventi provenienti da un'origine debbano essere trattenuti dalla destinazione in base a una regola. Un filtro verifica l'evento in base a una regola e rilascia l'evento se l'evento non corrisponde alla regola. Il filtraggio degli eventi duplicati osservando determinati criteri ed eliminando gli eventi successivi con gli stessi valori è un tipo di filtro.
- **_Crittografia_** : un'attività di replica potrebbe dover decrittografare il contenuto proveniente dall'origine e/o crittografare il contenuto in avanti in una destinazione e/o potrebbe dover verificare l'integrità del contenuto e dei metadati relativi a una firma trasferita nell'evento o allontanare tale firma.
- **_Attestazione_** : un'attività di replica può alleghiare i metadati, potenzialmente protetti da una firma digitale, a un evento che attesta che l'evento è stato ricevuto tramite un canale specifico o in un momento specifico.
- **_Concatenamento_** : un'attività di replica può applicare firme a flussi di eventi in modo che l'integrità del flusso sia protetta ed è possibile rilevare eventi mancanti.

I modelli di trasformazione, invio in batch e arricchimento vengono in genere implementati meglio con i processi di [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) . 

Tutti questi modelli possono essere implementati tramite funzioni di Azure, usando il [trigger di hub eventi](../azure-functions/functions-bindings-event-hubs-trigger.md) per l'acquisizione di eventi e l' [associazione di output di hub eventi](../azure-functions/functions-bindings-event-hubs-output.md) per la distribuzione.

## <a name="routing"></a>Routing

Il modello di routing si basa sul modello di [replica](#replication) , ma anziché avere un'origine e una destinazione, l'attività di replica ha più destinazioni, illustrate in C#:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

La funzione di routing considererà i metadati del messaggio e/o il payload del messaggio e quindi sceglierà una delle destinazioni disponibili da inviare a.

In analisi di flusso di Azure è possibile ottenere lo stesso risultato con la definizione di più output e quindi l'esecuzione di una query per output.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Proiezione log

Il modello di proiezione dei log rende flat il flusso di eventi su un database indicizzato, con eventi che diventano record nel database. In genere, gli eventi vengono aggiunti alla stessa raccolta o alla stessa tabella e la chiave di partizione dell'hub eventi diventa l'entità della chiave primaria che sta cercando di rendere univoco il record.

La proiezione di log può produrre uno storico di serie temporali dei dati dell'evento o una vista compatta, in base alla quale viene mantenuto solo l'evento più recente per ogni chiave di partizione. La forma del database di destinazione è in definitiva l'utente e le esigenze dell'applicazione. Questo modello è noto anche come "origine eventi".

> [!TIP]
> È possibile creare facilmente proiezioni di log nel [database SQL di Azure](../stream-analytics/sql-database-output.md) e [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) in analisi di flusso di Azure ed è preferibile scegliere questa opzione.

La funzione di Azure seguente proietta il contenuto di un hub eventi compresso in una raccolta di Azure CosmosDB.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Applicazioni di Event Replicator in funzioni di Azure][1]
- [Replica di eventi tra hub eventi][2]
- [Replica di eventi nel bus di servizio di Azure][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
