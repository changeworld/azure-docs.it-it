---
title: Pull dei dati di Azure Industrial Internet in ADX
description: Questa esercitazione illustra come eseguire il pull dei dati di IIoT in ADX.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787775"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Esercitazione: effettuare il pull dei dati di Azure industrialmente in ADX

La piattaforma Azure Industrial Internet (IIoT) combina i moduli perimetrali e i microservizi cloud con una serie di servizi PaaS di Azure per fornire funzionalità per l'individuazione di asset industriali e per raccogliere dati da tali asset tramite OPC UA. [Azure Esplora dati (ADX)](https://docs.microsoft.com/azure/data-explorer) è una destinazione naturale per i dati di IIoT con funzionalità di analisi dei dati che consente l'esecuzione di query flessibili sui dati inseriti dai server OPC UA connessi all'hub Internet tramite il server di pubblicazione OPC. Sebbene un cluster ADX possa inserire i dati direttamente dall'hub Internet, la piattaforma IIoT esegue ulteriormente l'elaborazione dei dati per renderli più utili prima di inserirli nell'hub eventi quando viene usata una distribuzione completa dei microservizi (fare riferimento all'architettura della piattaforma IIoT).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una tabella in ADX
> * Connettere l'hub eventi al cluster ADX
> * Analizzare i dati in ADX

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>Come rendere disponibili i dati nel cluster ADX per eseguire una query in modo efficace 

Se si esamina il formato del messaggio dall'hub eventi, come definito dalla classe Microsoft. Azure. IIoT. OpcUa. Subscriber. Models. MonitoredItemMessageModel, è possibile visualizzare un hint per la struttura necessaria per lo schema della tabella ADX.

![Struttura](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Di seguito sono riportati i passaggi necessari per rendere i dati disponibili nel cluster ADX e per eseguire query sui dati in modo efficace.  
1. Creare un cluster ADX. Se non è già stato effettuato il provisioning di un cluster ADX con la piattaforma IIoT o se si vuole usare un cluster diverso, seguire [questa procedura.](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
2. Abilitare l'inserimento dei flussi sul cluster ADX, come illustrato [qui](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster). 
3. Per creare un database [ADX, seguire questa procedura.](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database)

Per il passaggio seguente verrà usata l' [interfaccia Web ADX](https://docs.microsoft.com/azure/data-explorer/web-query-data) per eseguire le query necessarie. Assicurarsi di aggiungere il cluster all'interfaccia Web come illustrato nel collegamento.  
 
4. Creare una tabella in ADX per inserire i dati inseriti in.  Sebbene sia possibile utilizzare la classe MonitoredItemMessageModel per definire lo schema della tabella ADX, è consigliabile inserire prima i dati in una tabella di staging con una colonna di tipo [Dynamic](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic). Ciò offre maggiore flessibilità nella gestione dei dati e dell'elaborazione in altre tabelle, che possono essere combinate con altre origini dati, in modo da soddisfare le esigenze di più casi d'uso. La query ADX seguente crea la tabella di staging ' iiot_stage ' con una colonna ' payload ',

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

È anche necessario aggiungere un mapping di inserimento JSON per indicare al cluster di inserire l'intero messaggio JSON dall'hub eventi nella tabella di staging,

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. La tabella è ora pronta per ricevere i dati dall'hub eventi. 
6. Usare le istruzioni [qui](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) per connettere l'hub eventi al cluster ADX e iniziare a inserire i dati nella tabella di staging. È sufficiente creare la connessione perché è già stato effettuato il provisioning di un hub eventi dalla piattaforma IIoT.  
7. Una volta verificata la connessione, i dati inizieranno a scorrere verso la tabella e dopo un breve ritardo è possibile iniziare a esaminare i dati nella tabella. Usare la query seguente nell'interfaccia Web ADX per esaminare un campione di dati di 10 righe. Possiamo vedere qui come i dati nel payload sono simili alla classe MonitoredItemMessageModel citata in precedenza.

![Query](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. È ora possibile eseguire alcune analisi su questi dati analizzando direttamente i dati dinamici nella colonna "payload". In questo esempio viene calcolata la media dei dati di telemetria identificati da "DisplayName": "PositiveTrendData", nel tempo per le finestre di 10 minuti, su tutti i record inseriti da un determinato punto di tempo (definito dalla variabile min_t) Let min_t = DateTime (2020-10-23); iiot_stage | dove ToDateTime (payload) Timestamp) > min_t | dove ToString (payload). DisplayName) = =' PositiveTrendData ' | riepiloga event_avg = AVG (ToDouble (payload). ) Per bin (ToDateTime (payload). Timestamp), 10 m)
 
Poiché la colonna ' payload ' contiene un tipo di dati dinamico, è necessario eseguire la conversione dei dati in fase di query, in modo che i calcoli vengano eseguiti sui tipi di dati corretti.

![Timestamp payload](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Come indicato in precedenza, l'inserimento dei dati OPC UA in una tabella di staging con una colonna ' Dynamic ' offre flessibilità. Tuttavia, la necessità di eseguire conversioni di tipi di dati in fase di query può causare ritardi nell'esecuzione delle query in particolare se il volume di dati è di grandi dimensioni e se sono presenti numerose query simultanee. In questa fase, è possibile creare un'altra tabella con i tipi di dati già determinati, in modo da evitare le conversioni dei tipi di dati in fase di query.
 
9. Creare una nuova tabella per i dati analizzati costituiti da una selezione limitata dal contenuto del ' payload ' dinamico nella tabella di staging. Si noti che è stata creata una colonna valore per ognuno dei tipi di dati previsti previsti nei dati di telemetria.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Creare una funzione a livello di database per proiettare i dati necessari dalla tabella di staging. Qui è possibile selezionare gli elementi ' timestamp ',' PublisherId ',' DisplayName ',' DataType ' è NodeId ' della colonna ' payload ' e proiettarli come ' Tag_Timestamp ',' Tag_PublisherId ',' tag ',' Tag_Datatype ',' Tag_NodeId '. L'elemento ' value ' viene proiettato come tre parti diverse in base al tipo di dati ' DataType '.

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

Per altre informazioni sul mapping dei tipi di dati in ADX, vedere [qui](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)e per le funzioni in ADX è possibile iniziare da [qui](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions).
 
11. Applicare la funzione del passaggio precedente alla tabella analizzata usando i criteri di aggiornamento. Il [criterio](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy) di aggiornamento indica a ADX di aggiungere automaticamente dati a una tabella di destinazione ogni volta che vengono inseriti nuovi dati nella tabella di origine, in base a una query di trasformazione eseguita sui dati inseriti nella tabella di origine. È possibile usare la query seguente per assegnare la tabella analizzata come destinazione e la tabella della fase come origine per i criteri di aggiornamento definiti dalla funzione creata nel passaggio precedente.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Non appena viene eseguita la query precedente, i dati inizieranno a fluire e a popolare la tabella di destinazione ' iiot_parsed '. È possibile esaminare i dati in "iiot_parsed come segue".

![Tabella analizzata](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Esaminiamo ora come possiamo ripetere le analisi effettuate in un passaggio precedente; calcolare la media dei dati di telemetria identificati da "DisplayName": "PositiveTrendData", nel tempo per le finestre di 10 minuti, su tutti i record inseriti a partire da un punto di tempo specifico (definito dalla variabile min_t). Poiché i valori del tag ' PositveTrendData ' sono ora archiviati in una colonna di tipo Double Data, è previsto un miglioramento delle prestazioni delle query.

![Ripeti analisi](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. Si confronti infine le prestazioni delle query in entrambi i casi. È possibile trovare il tempo necessario per eseguire la query usando "stats" nell'interfaccia utente di ADX, che può trovarsi sopra i risultati della query.  

![Prestazioni query 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Prestazioni query 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

Si può notare che la query che usa la tabella analizzata è approssimativamente due volte più veloce di quella per la tabella di staging. In questo esempio è presente un set di dati di piccole dimensioni e non sono presenti query simultanee in esecuzione, quindi l'effetto sul tempo di esecuzione della query non è eccezionale. Tuttavia, per un carico di lavoro realistico si verificherebbe un notevole impatto sulle prestazioni. Per questo motivo è importante prendere in considerazione la separazione dei diversi tipi di dati in colonne diverse.

> [!NOTE] 
> Il criterio di aggiornamento funziona solo sui dati inseriti nella tabella di staging dopo che i criteri sono stati configurati e non è applicabile ad alcun dato preesistente. Questo aspetto deve essere tenuto in considerazione quando, ad esempio, è necessario modificare i criteri di aggiornamento. I dettagli completi sono disponibili nella documentazione di ADX.

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come modificare i valori predefiniti della configurazione, è possibile 

> [!div class="nextstepaction"]
> [Configurare i componenti dell'it industriale](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Visualizza e analizza i dati usando Time Series Insights](tutorial-visualize-data-time-series-insights.md)