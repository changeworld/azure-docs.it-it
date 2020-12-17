---
title: Modelli di attività di replica del messaggio-bus di servizio di Azure | Microsoft Docs
description: Questo articolo fornisce indicazioni dettagliate per l'implementazione di modelli di attività di replica dei messaggi specifici
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657501"
---
# <a name="message-replication-tasks-patterns"></a>Modelli di attività di replica del messaggio

I Cenni preliminari sulla [Federazione](service-bus-federation-overview.md) e sulle [funzioni Replicator](service-bus-federation-replicator-functions.md) illustrano la logica per e gli elementi di base delle attività di replica ed è consigliabile acquisire familiarità con questi elementi prima di continuare con questo articolo.

In questo articolo vengono illustrate in dettaglio le linee guida di implementazione per diversi modelli evidenziati nella sezione Panoramica. 

## <a name="replication"></a>Replica 

Il modello di replica copia i messaggi da una coda o da un argomento a quello successivo oppure da una coda o da un argomento a un'altra destinazione, ad esempio un hub eventi. I messaggi vengono inviati senza apportare alcuna modifica al payload del messaggio. 

L'implementazione di questo modello è illustrata nell'esempio [relativo alla replica dei messaggi da e verso il bus di servizio di Azure](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) .

### <a name="sequences-and-order-preservation"></a>Sequenze e conservazione degli ordini

Il modello di replica non mira a mantenere l'ordine assoluto dei messaggi di una coda o di un argomento di origine in una coda o un argomento di destinazione, ma si concentra, quando necessario, per mantenere l'ordine relativo dei messaggi in cui l'applicazione lo richiede. L'applicazione Abilita questa operazione abilitando il supporto della sessione per l'entità di origine e raggruppando i messaggi correlati con la stessa [chiave di sessione](message-sessions.md).

Le funzioni di replica predefinite con rilevamento della sessione garantiscono che le sequenze di messaggi con lo stesso ID sessione recuperato da un'entità di origine vengano inviate alla coda o all'argomento di destinazione come batch nella sequenza originale e con lo stesso ID sessione. 

### <a name="service-assigned-metadata"></a>Metadati assegnati dal servizio 

I metadati assegnati dal servizio di un messaggio ottenuto dalla coda o dall'argomento di origine, dal numero di sequenza e dall'ora di Accodamento originali, vengono sostituiti da nuovi valori assegnati al servizio nella coda o nell'argomento di destinazione, ma con le attività di replica predefinite fornite negli esempi, i valori originali vengono conservati nelle proprietà utente: `repl-enqueue-time` (ISO8601 String) e `repl-sequence` .

Queste proprietà sono di tipo stringa e contengono il valore file delle rispettive proprietà originali.  Se il messaggio viene inviato più volte, i metadati assegnati al servizio dell'origine immediata vengono aggiunti alle proprietà già esistenti, con valori separati da punti e virgola. 

### <a name="failover"></a>Failover

Se si usa la replica per finalità di ripristino di emergenza, per proteggersi da messaggi di disponibilità a livello di area nel servizio del bus di servizio o da interruzioni di rete, in uno scenario di questo tipo è necessario eseguire un failover da una coda o da un argomento a quello successivo, indicando a produttori e/o consumer di usare l'endpoint secondario.

Per tutti gli scenari di failover si presuppone che gli elementi necessari degli spazi dei nomi siano strutturalmente identici, ovvero che le code e gli argomenti abbiano un nome identico e che le regole di firma di accesso condiviso e/o le regole di controllo degli accessi in base al ruolo siano configurate nello stesso modo. È possibile creare (e aggiornare) uno spazio dei nomi secondario seguendo le [linee guida per lo spostamento degli spazi dei nomi](move-across-regions.md) e l'omissione del passaggio di pulizia.

Per fare in modo che i producer e i consumer commutino, è necessario rendere disponibili le informazioni sullo spazio dei nomi da utilizzare per la ricerca in una posizione facile da raggiungere e aggiornare. Se i producer o i consumer riscontrano errori frequenti o persistenti, devono consultare tale posizione e modificare la configurazione. Sono disponibili diversi modi per condividere la configurazione, ma sono presenti due elementi: DNS e condivisioni file.

#### <a name="dns-based-failover-configuration"></a>Configurazione del failover basato su DNS

Un approccio candidato consiste nel conservare le informazioni nei record DNS SRV in un DNS controllato e puntare ai rispettivi endpoint della coda o dell'argomento. Si noti che hub messaggi non consente agli endpoint di essere direttamente associati a record CNAME, il che significa che si userà DNS come meccanismo di ricerca resiliente per gli indirizzi endpoint e non per risolvere direttamente le informazioni sugli indirizzi IP. 

Si supponga di essere proprietari del dominio `example.com` e, per l'applicazione, una zona `test.example.com` . Per due bus di servizio alternativi, verranno ora create altre due zone annidate e un record SRV in ogni. 

I record SRV sono, dopo la convenzione comune, con prefisso `_azure_servicebus._amqp` e contengono due record dell'endpoint: uno per AMQP-over-TLS sulla porta 5671 e uno per AMQP-over-WebSocket sulla porta 443, che puntano entrambi all'endpoint del bus di servizio dello spazio dei nomi corrispondente alla zona.

| Zona                 | Record SRV
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

Nella zona dell'applicazione si creerà quindi una voce CNAME che punta alla zona subordinata corrispondente alla coda o all'argomento primario:

| Record CNAME                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

L'uso di un client DNS che consente di eseguire query in modo esplicito in record CNAME e SRV (i client predefiniti di Java e .NET consentono solo una semplice risoluzione dei nomi agli indirizzi IP), quindi è possibile risolvere l'endpoint desiderato. Con [DnsClient.NET](https://dnsclient.michaco.net/), ad esempio, la funzione Lookup è:

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
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

Questa procedura è simile alla modalità di funzionamento del ripristino di emergenza [geografico del bus di servizio](service-bus-geo-dr.md) , ma completamente sotto il proprio controllo e funziona anche con scenari attivi/attivi.

#### <a name="file-share-based-failover-configuration"></a>Configurazione del failover basato su condivisione file

L'alternativa più semplice all'uso del DNS per la condivisione delle informazioni sugli endpoint consiste nell'inserire il nome dell'endpoint primario in un file di testo normale e fornire il file da un'infrastruttura che sia affidabile rispetto alle interruzioni e che consenta comunque gli aggiornamenti. 

Se è già stata eseguita un'infrastruttura di siti Web a disponibilità elevata con la disponibilità globale e la replica del contenuto, aggiungere tale file e ripubblicare il file se è necessaria un'opzione.

## <a name="merge"></a>Unione

Il modello di Unione ha una o più attività di replica che puntano a una destinazione, possibilmente simultaneamente ai producer normali che inviano messaggi alla stessa destinazione. 

Le varianti di questo modello sono:
- Due o più funzioni di replica acquisiscono contemporaneamente messaggi da origini separate e li inviano alla stessa destinazione.
- Una funzione di replica acquisisce i messaggi da un'origine mentre la destinazione viene utilizzata anche direttamente dai producer. 
- Il modello precedente, ma con mirroring dei messaggi tra due o più argomenti, che derivano gli argomenti che contengono gli stessi messaggi, indipendentemente dalla posizione in cui vengono generati i messaggi.

Le prime due varianti di modello sono semplici e non differiscono dalle attività di replica normale.

Per l'ultimo scenario è necessario escludere la replica dei messaggi già replicati. La tecnica è illustrata e illustrata nell'esempio attivo/attivo.

## <a name="editor"></a>Editor

Il modello di editor si basa sul modello di [replica](#replication) , ma i messaggi vengono modificati prima di essere inviati. Esempi di modifiche di questo tipo sono:

- **_Transcodifica_* _-se il contenuto del messaggio (detto anche "corpo" o "payload") arriva dall'origine codificata usando il formato _Apache avro * o un formato di serializzazione proprietario, tuttavia, l'aspettativa del sistema proprietario della destinazione è il fatto che il contenuto sia codificato in *JSON* , un'attività di replica di transcodifica prima deserializza il payload da *Apache avro* in un oggetto grafico in memoria e quindi serializza il grafico nel formato *JSON* per il messaggio che viene trasmesso. La transcodifica include anche attività di compressione e decompressione del **contenuto** .
- **_Trasformazione_* _-i messaggi che contengono dati strutturati possono richiedere la ridefinizione dei dati per un consumo più semplice da parte dei consumer downstream. Questo può comportare un lavoro analogo alla Flating di strutture annidate, all'eliminazione di elementi dati estranei o alla ridefinizione del payload per adattarlo esattamente a un determinato schema.
- Invio in _*_batch_*_ : i messaggi possono essere ricevuti in batch (più messaggi in un singolo trasferimento) da un'origine, ma devono essere inviati singolarmente a una destinazione o viceversa. Un'attività può quindi inviare più messaggi in base a un singolo trasferimento di messaggi di input o aggregare un set di messaggi che vengono quindi trasferiti insieme. 
- _*_Convalida_*_ : è spesso necessario controllare i dati dei messaggi provenienti da origini esterne per verificare se sono conformi a un set di regole prima che possano essere inoltri. Le regole possono essere espresse utilizzando schemi o codice. i messaggi che non sono conformi possono essere eliminati, con il problema indicato nei log oppure possono essere inoltrati a una destinazione di destinazione speciale per gestirli ulteriormente.   
- _*_Arricchimento_*_ : i dati dei messaggi provenienti da alcune origini possono richiedere l'arricchimento con ulteriore contesto per poter essere usati nei sistemi di destinazione. Questo può comportare la ricerca di dati di riferimento e l'incorporamento di tali dati con il messaggio oppure l'aggiunta di informazioni sull'origine note all'attività di replica, ma non contenute nei messaggi. 
- _*_Filtro_*_ : è possibile che alcuni messaggi provenienti da un'origine debbano essere trattenuti dalla destinazione in base a una regola. Un filtro verifica il messaggio rispetto a una regola e rilascia il messaggio se il messaggio non corrisponde alla regola. Filtrare i messaggi duplicati osservando determinati criteri ed eliminando i messaggi successivi con gli stessi valori è una forma di filtro.
- _*_Routing e partizionamento_*_ : alcune attività di replica possono consentire due o più destinazioni alternative e definire regole per le quali viene scelta la destinazione di replica per un determinato messaggio in base ai metadati o al contenuto del messaggio. Un tipo speciale di routing è il partizionamento, in cui l'attività assegna in modo esplicito le partizioni in una destinazione di replica in base alle regole.
- _*_Crittografia_*_ : un'attività di replica potrebbe dover decrittografare il contenuto proveniente dall'origine e/o crittografare il contenuto in avanti in una destinazione e/o potrebbe dover verificare l'integrità del contenuto e dei metadati relativi a una firma contenuta nel messaggio oppure allontanare tale firma. 
- _*_Attestazione_*_ : un'attività di replica può alleghi i metadati, potenzialmente protetti da una firma digitale, a un messaggio che attesta che il messaggio è stato ricevuto tramite un canale specifico o in un momento specifico.     
- _ *_Concatenamento_**-un'attività di replica può applicare firme a sequenze di messaggi in modo che l'integrità della sequenza sia protetta e che i messaggi mancanti possano essere rilevati.

Tutti questi modelli possono essere implementati tramite funzioni di Azure, usando il [trigger Hub messaggi](../azure-functions/functions-bindings-service-bus-trigger.md) per l'acquisizione dei messaggi e l' [associazione di output di code o argomenti](../azure-functions/functions-bindings-service-bus-output.md) per la distribuzione. 

## <a name="routing"></a>Routing.

Il modello di routing si basa sul modello di [replica](#replication) , ma anziché avere un'origine e una destinazione, l'attività di replica ha più destinazioni, illustrate in C#:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

La funzione di routing considererà i metadati del messaggio e/o il payload del messaggio e quindi sceglierà una delle destinazioni disponibili da inviare a.

## <a name="next-steps"></a>Passaggi successivi

- [applicazioni di Message Replicator in funzioni di Azure][1]
- [Replica dei messaggi tra il bus di servizio][2]
- [Replica dei messaggi in hub eventi di Azure][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub