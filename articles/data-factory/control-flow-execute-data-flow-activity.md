---
title: Attività flusso di dati
description: Come eseguire flussi di dati dall'interno di una pipeline data factory dati.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 04/14/2021
ms.openlocfilehash: b0d42b42ab44b51294833e40b7fa0174256c655a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517174"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Flusso di dati'attività in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usare l'Flusso di dati per trasformare e spostare i dati tramite flussi di dati di mapping. Se non si ha una nuova versione dei flussi di dati, vedere [Panoramica del mapping Flusso di dati dati](concepts-data-flow-overview.md)

## <a name="syntax"></a>Sintassi

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
Flusso | Riferimento al Flusso di dati in esecuzione | DataFlowReference | Sì
integrationRuntime | Ambiente di calcolo in cui viene eseguito il flusso di dati. Se non viene specificato, verrà usato il runtime di integrazione di Azure a risoluzione automatica. | IntegrationRuntimeReference | No
compute.coreCount | Numero di core usati nel cluster Spark. Può essere specificato solo se viene usato il runtime di integrazione di Azure con risoluzione automatica | 8, 16, 32, 48, 80, 144, 272 | No
compute.computeType | Tipo di calcolo usato nel cluster Spark. Può essere specificato solo se viene usato il runtime di integrazione di Azure con risoluzione automatica | "General", "ComputeOptimized", "MemoryOptimized" | No
staging.linkedService | Se si usa un'origine o un sink Azure Synapse Analytics, specificare l'account di archiviazione usato per lo staging di PolyBase.<br/><br/>Se il Archiviazione di Azure è configurato con l'endpoint servizio di rete virtuale, è necessario usare l'autenticazione dell'identità gestita con l'opzione "Consenti servizio Microsoft attendibile" abilitata nell'account di archiviazione. Vedere Impatto dell'uso degli endpoint di servizio di rete virtuale con Archiviazione di [Azure.](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage) Informazioni anche sulle configurazioni [](connector-azure-data-lake-storage.md#managed-identity) necessarie rispettivamente [per BLOB](connector-azure-blob-storage.md#managed-identity) Azure Data Lake Storage Gen2 Azure.<br/> | LinkedServiceReference | Solo se il flusso di dati legge o scrive in un Azure Synapse Analytics
staging.folderPath | Se si usa un'origine o un sink Azure Synapse Analytics, il percorso della cartella nell'account di archiviazione BLOB usato per lo staging di PolyBase | string | Solo se il flusso di dati legge o scrive in Azure Synapse Analytics
Tracelevel | Impostare il livello di registrazione dell'esecuzione dell'attività flusso di dati | Fine, Coarse, None | No

![Eseguire Flusso di dati](media/data-flow/activity-data-flow.png "Eseguire Flusso di dati")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Ridimensionare dinamicamente il calcolo del flusso di dati in fase di esecuzione

Le proprietà Conteggio core e Tipo di calcolo possono essere impostate in modo dinamico per adattarsi alle dimensioni dei dati di origine in ingresso in fase di esecuzione. Usare attività della pipeline come Ricerca o Ottieni metadati per trovare le dimensioni dei dati del set di dati di origine. Usare quindi Aggiungi contenuto dinamico nelle proprietà Flusso di dati'attività.

![Dynamic Data flusso](media/data-flow/dyna1.png "Flusso di dati dinamico")

[Ecco una breve esercitazione video che illustra questa tecnica](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Flusso di dati runtime di integrazione

Scegliere la Integration Runtime da usare per l'esecuzione Flusso di dati'attività. Per impostazione predefinita, Data Factory il runtime di integrazione di Azure con risoluzione automatica con quattro core di lavoro. Questo IR ha un tipo di calcolo per utilizzo generico e viene eseguito nella stessa area della factory. Per le pipeline operative, è altamente consigliabile creare runtime di integrazione di Azure personalizzati che definiscono aree specifiche, tipo di calcolo, conteggi core e TTL per l'esecuzione dell'attività del flusso di dati.

Un tipo di calcolo minimo di per utilizzo generico (ottimizzato per il calcolo non è consigliato per carichi di lavoro di grandi dimensioni) con una configurazione di 8+8 (16 v-core totali) e 10 minuti è la raccomandazione minima per la maggior parte dei carichi di lavoro di produzione. Impostando un valore TTL di piccole dimensioni, il Azure IR può mantenere un cluster a caldo che non dovrà richiedere alcuni minuti di avvio per un cluster a freddo. È possibile velocizzare ancora di più l'esecuzione dei flussi di dati selezionando "Ri-uso rapido" nelle configurazioni dei flussi Azure IR dati. Per altre informazioni, vedere Runtime [di integrazione di Azure.](concepts-integration-runtime.md)

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> La Integration Runtime nell'attività Flusso di dati si applica solo alle *esecuzioni attivate* della pipeline. Il debug della pipeline con flussi di dati viene eseguito nel cluster specificato nella sessione di debug.

### <a name="polybase"></a>PolyBase

Se si usa un'Azure Synapse Analytics come sink o origine, è necessario scegliere un percorso di staging per il carico batch PolyBase. PolyBase consente il caricamento batch in blocco anziché caricare i dati riga per riga. PolyBase riduce drasticamente il tempo di caricamento in Azure Synapse Analytics.

## <a name="logging-level"></a>Livello di registrazione

Se non è necessaria ogni esecuzione della pipeline delle attività del flusso di dati per registrare completamente tutti i log di telemetria dettagliati, è possibile impostare facoltativamente il livello di registrazione su "Basic" o "None". Quando si eseguono i flussi di dati in modalità "Dettagliato" (impostazione predefinita), si richiede ad ADF di registrare completamente l'attività a ogni livello di partizione singola durante la trasformazione dei dati. Può trattarsi di un'operazione costosa, quindi solo l'abilitazione dettagliata durante la risoluzione dei problemi può migliorare le prestazioni complessive del flusso di dati e della pipeline. La modalità "Basic" registra solo le durate della trasformazione, mentre "None" fornisce solo un riepilogo delle durate.

![Livello di registrazione](media/data-flow/logging.png "Impostare il livello di registrazione")

## <a name="sink-properties"></a>Proprietà sink

La funzionalità di raggruppamento nei flussi di dati consente sia di impostare l'ordine di esecuzione dei sink sia di raggruppare i sink usando lo stesso numero di gruppo. Per facilitare la gestione dei gruppi, è possibile chiedere ad ADF di eseguire sink nello stesso gruppo in parallelo. È anche possibile impostare il gruppo di sink in modo che continui anche dopo che uno dei sink ha rilevato un errore.

Il comportamento predefinito dei sink del flusso di dati è l'esecuzione sequenziale di ogni sink, in modo seriale, e l'esito negativo del flusso di dati quando viene rilevato un errore nel sink. Inoltre, tutti i sink vengono impostati per impostazione predefinita sullo stesso gruppo, a meno che non vengano visualizzate le proprietà del flusso di dati e non si impostano priorità diverse per i sink.

![Proprietà sink](media/data-flow/sink-properties.png "Impostare le proprietà del sink")

## <a name="parameterizing-data-flows"></a>Parametrizzazione dei flussi di dati

### <a name="parameterized-datasets"></a>Set di dati con parametri

Se il flusso di dati usa set di dati con parametri, impostare i valori dei parametri nella **scheda** Impostazioni.

![Eseguire Flusso di dati parametri](media/data-flow/params.png "Parametri")

### <a name="parameterized-data-flows"></a>Flussi di dati con parametri

Se il flusso di dati è con parametri, impostare i valori dinamici dei parametri del flusso di dati nella **scheda** Parametri. È possibile usare il linguaggio delle espressioni della pipeline DIF o il linguaggio delle espressioni del flusso di dati per assegnare valori di parametro dinamici o letterali. Per altre informazioni, vedere Flusso di dati [Parameters](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Proprietà di calcolo con parametri.

È possibile parametrizzare il numero core o il tipo di calcolo se si usa il runtime di integrazione di Azure con risoluzione automatica e si specificano i valori per compute.coreCount e compute.computeType.

![Esempio di Flusso di dati di esecuzione](media/data-flow/parameterize-compute.png "Esempio di parametro")

## <a name="pipeline-debug-of-data-flow-activity"></a>Debug della pipeline di Flusso di dati attività

Per eseguire un'esecuzione della pipeline di debug con un'attività Flusso di dati, è necessario attivare la modalità di debug del flusso di dati tramite il dispositivo di scorrimento Flusso di dati **Debug** sulla barra superiore. La modalità di debug consente di eseguire il flusso di dati su un cluster Spark attivo. Per altre informazioni, vedere [Modalità di debug](concepts-data-flow-debug-mode.md).

![Pulsante Debug](media/data-flow/debug-button-3.png "Pulsante Debug")

La pipeline di debug viene eseguita nel cluster di debug attivo, non nell'ambiente di runtime di integrazione specificato nelle impostazioni Flusso di dati attività. È possibile scegliere l'ambiente di calcolo di debug all'avvio della modalità di debug.

## <a name="monitoring-the-data-flow-activity"></a>Monitoraggio dell'Flusso di dati attività

L Flusso di dati'attività ha un'esperienza di monitoraggio speciale in cui è possibile visualizzare informazioni su partizionamento, tempo di gestione delle fasi e derivazione dei dati. Aprire il riquadro di monitoraggio tramite l'icona a forma di occhiali in **Azioni**. Per altre informazioni, vedere Monitoraggio [dei flussi di dati.](concepts-data-flow-monitoring.md)

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Usare Flusso di dati'attività in un'attività successiva

L'attività flusso di dati restituisce metriche relative al numero di righe scritte in ogni sink e alle righe lette da ogni origine. Questi risultati vengono restituiti nella sezione `output` del risultato dell'esecuzione dell'attività. Le metriche restituite sono nel formato json seguente.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Ad esempio, per ottenere il numero di righe scritte in un sink denominato "sink1" in un'attività denominata "dataflowActivity", usare `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

Per ottenere il numero di righe lette da un'origine denominata 'source1' usata in tale sink, usare `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Se un sink non contiene righe scritte, non verrà visualizzato nelle metriche. L'esistenza può essere verificata usando la `contains` funzione . Ad esempio, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` controlla se sono state scritte righe in sink1.

## <a name="next-steps"></a>Passaggi successivi

Vedere attività del flusso di controllo supportate da Data Factory: 

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività Esegui pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere attività di metadati](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
