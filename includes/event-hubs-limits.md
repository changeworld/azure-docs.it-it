---
title: include file description: include file services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 31/03/2021 ms.author: spelluru ms.custom: "include file", "fasttrack-edit","iot","event-hubs"

---

Le tabelle seguenti forniscono le quote e i limiti specifici di [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Per informazioni sui prezzi di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Limiti comuni per tutti i livelli
I limiti seguenti sono comuni tra tutti i livelli. 

| Limite |  Note | valore |
| --- |  --- | --- |
| Numero di spazi dei nomi di Hub eventi per sottoscrizione |- |100 |
| Numero di hub eventi per ogni spazio dei nomi | Le successive richieste per la creazione di un nuovo hub eventi vengono rifiutate. |10 |
| Dimensioni del nome di un hub eventi |- | 256 caratteri |
| Dimensioni del nome di un gruppo di consumer | Il protocollo Kafka non richiede la creazione di un gruppo di consumer. | <p>Kafka: 256 caratteri</p><p>AMQP: 50 caratteri |
| Numero di ricevitori non epoch per gruppo consumer |- |5 |
| Numero di regole di autorizzazione per spazio dei nomi | Le richieste successive di creazione di regole di autorizzazione vengono rifiutate.|12 |
| Numero di chiamate al metodo GetRuntimeInformation |  - | 50 al secondo | 
| Numero di reti virtuali | - | 128 | 
| Numero di regole di configurazione IP | - | 128 | 

### <a name="basic-vs-standard-tiers"></a>Livelli Basic e Standard
La tabella seguente illustra i limiti che possono essere diversi per i livelli Basic e Standard. 

| Limite | Note | Basic | Standard |
|---|---|--|---|
| Dimensioni massime della pubblicazione di Hub eventi| &nbsp; | 256 KB | 1 MB |
| Numero di gruppi consumer per hub eventi | &nbsp; |1 |20 |
| Numero di connessioni AMQP per spazio dei nomi | Le richieste successive di connessioni aggiuntive vengono rifiutate e il codice chiamante riceverà un'eccezione. |100 |5\.000|
| Periodo di conservazione massimo dei dati dell'evento | &nbsp; |1 giorno |1-7 giorni |
| Numero massimo di unità di velocità effettiva |Il superamento di questo limite causa la limitazione dei dati e la generazione di un'[eccezione di server occupato](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Per richiedere un numero maggiore di unità elaborate per un livello Standard, inviare una [richiesta di supporto](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). Le [unità elaborate aggiuntive](../articles/event-hubs/event-hubs-auto-inflate.md) sono disponibili in blocchi da 20 in base a un acquisto con impegno. |20 | 20 | 
| Numero di partizioni per hub eventi | |32 | 32 | 

> [!NOTE]
>
> È possibile pubblicare gli eventi singolarmente o in batch. 
> Il limite di pubblicazione (in base a SKU) si applica indipendentemente dal fatto che si tratta di un singolo evento o di un batch. Gli eventi di pubblicazione maggiori della soglia massima verranno rifiutati.

### <a name="dedicated-tier-vs-standard-tier"></a>Livello Dedicato e Standard
L'offerta Hub eventi Dedicato viene fatturata a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello Dedicato offre tutte le funzionalità del piano Standard, ma con capacità e limiti su scala aziendale per i clienti con carichi di lavoro intensi. 

Fare riferimento a questo [documento](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) per informazioni su come creare un cluster di Hub eventi dedicato usando il portale di Azure.

| Funzionalità | Standard | Dedicato |
| --- |:---|:---|
| Larghezza di banda | 20 unità elaborate (fino a 40 unità elaborate) | 20 unità di capacità |
| Spazi dei nomi |  100 per sottoscrizione | 50 per cu (100 per sottoscrizione) |
| Hub eventi |  10 per spazio dei nomi | 1\.000 per spazio dei nomi |
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Dimensioni del messaggio | 1 milione di byte | 1 milione di byte |
| Partizioni | 32 per hub eventi | 1024 per hub eventi<br/>2\.000 per unità di capacità |
| Gruppi di consumer | 20 per hub eventi | Nessun limite per unità di capacità, 1.000 per hub eventi |
| Connessioni negoziate | 1\.000 incluse, massimo 5.000 | 100.000 incluse massimo |
| Conservazione dei messaggi | 7 giorni, 84 TB inclusi per unità elaborata | 90 giorni, 10 TB inclusi per unità di capacità |
| Acquisizione | Pagamento per ogni ora | Incluso |


### <a name="schema-registry-limitations"></a>Limitazioni del registro schemi

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Limiti uguali per i livelli standard e dedicato 
| Funzionalità | Limite | 
|---|---|
| Lunghezza massima del nome di un gruppo di schemi | 50 |  
| Lunghezza massima del nome di uno schema | 100 |    
| Dimensioni in byte per schema | 1 MB |   
| Numero di proprietà per gruppo di schemi | 1024 |
| Dimensioni in byte per la chiave del gruppo di proprietà | 256 | 
| Dimensioni in byte per valore della proprietà gruppo | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Limiti diversi per i livelli standard e dedicato 

| Limite | Standard | Dedicato | 
|---|---|--|
| Dimensioni del registro schemi (spazio dei nomi) in megabyte | 25 |  1024 |
| Numero di gruppi di schemi in un registro schemi o spazio dei nomi | 1 - escluso il gruppo predefinito | 1000 |
| Numero di versioni dello schema in tutti i gruppi di schemi | 25 | 10000 |