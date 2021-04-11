---
title: Come monitorare Azure competenza
description: Informazioni su come configurare le metriche, gli avvisi e le impostazioni di diagnostica di Azure, usando monitoraggio di Azure.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4cb3965d359980856c238cd563ed8b761754660b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667742"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Metriche di competenza di Azure in monitoraggio di Azure

Questo articolo descrive come configurare le metriche, gli avvisi e le impostazioni di diagnostica per Azure, usando monitoraggio di Azure.

## <a name="monitor-azure-purview"></a>Monitora la competenza di Azure

Gli amministratori di Azure per le competenze possono usare monitoraggio di Azure per tenere traccia dello stato operativo dell'account di competenza. Vengono raccolte le metriche per fornire punti dati per tenere traccia dei potenziali problemi, risolvere i problemi e migliorare l'affidabilità dell'account di competenza. Le metriche vengono inviate a monitoraggio di Azure per gli eventi che si verificano in Azure.

## <a name="aggregated-metrics"></a>Metriche aggregate

È possibile accedere alle metriche dalla portale di Azure per un account di competenza. L'accesso alle metriche è controllato dall'assegnazione di ruolo dell'account di competenza. Per visualizzare le metriche, gli utenti devono far parte del ruolo "lettore di monitoraggio" in Azure. Per ulteriori informazioni sui livelli di accesso ai ruoli, vedere [autorizzazioni del ruolo di lettore di monitoraggio](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) .

La persona che ha creato l'account di competenza ottiene automaticamente le autorizzazioni per visualizzare le metriche. Se altri utenti vogliono visualizzare le metriche, aggiungerle al ruolo di **lettore di monitoraggio** attenendosi alla procedura seguente:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Aggiungere un utente al ruolo di lettore di monitoraggio

Per aggiungere un utente al ruolo di **lettore di monitoraggio** , il proprietario dell'account di competenza o il proprietario della sottoscrizione può seguire questa procedura:

1. Passare alla [portale di Azure](https://portal.azure.com) e cercare il nome dell'account Azure.

2. Selezionare **Controllo di accesso (IAM)** .

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Screenshot che illustra come accedere ad IAM.":::

3. Selezionare **Aggiungi un'assegnazione di ruolo**.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Screenshot che illustra come aggiungere un'assegnazione di ruolo.":::

4. Selezionare il **lettore di monitoraggio** dei ruoli e impostare assegna accesso a **Azure ad utente, gruppo o entità servizio**. E assegnare l'account AAD per accedere alle metriche.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Screenshot che illustra come aggiungere un ruolo di lettore di monitoraggio.":::

## <a name="metrics-visualization"></a>Visualizzazione metrica

Gli utenti nel ruolo di **lettore di monitoraggio** possono visualizzare le metriche aggregate e i log di diagnostica inviati a monitoraggio di Azure. Le metriche sono elencate nell'portale di Azure per l'account di competenza corrispondente. Nella portale di Azure selezionare la sezione metrica per visualizzare l'elenco di tutte le metriche disponibili.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Screenshot che mostra la sezione metriche di competenza disponibili." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Gli utenti di Azure di competenza possono anche accedere alla pagina delle metriche direttamente dal centro di gestione dell'account Azure per le competenze. Selezionare monitoraggio di Azure nella pagina principale di competenza Management Center per avviare portale di Azure.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Screenshot per avviare le metriche di competenza dal centro di gestione." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Metriche disponibili

Per acquisire familiarità con l'uso della sezione metrica nel portale di Azure leggere i due documenti seguenti. Introduzione [a Esplora metriche](../azure-monitor/essentials/metrics-getting-started.md) e [funzionalità avanzate di Esplora metriche](../azure-monitor/essentials/metrics-charts.md).

La tabella seguente contiene l'elenco delle metriche disponibili per l'esplorazione nel portale di Azure:

| Nome misurazione | Spazio dei nomi della metrica | Tipo di aggregazione | Descrizione |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Analisi annullata | Analisi automatizzata | Sum <br> Conteggio | Aggregare le analisi dell'origine dati annullata nel periodo di tempo |
| Analisi completata | Analisi automatizzata | Sum <br> Conteggio | Aggregare le analisi delle origini dati completate nel periodo di tempo |
| Analisi non riuscita | Analisi automatizzata | Sum <br> Conteggio | Aggregare le analisi delle origini dati non riuscite nel periodo di tempo |
| Tempo di analisi impiegato | Analisi automatizzata | Min <br> Max <br> Sum <br> Media | Aggregare il tempo totale impiegato dalle analisi nel periodo di tempo |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Log di diagnostica nell'account di archiviazione di Azure

Gli eventi di telemetria non elaborati vengono emessi in monitoraggio di Azure. Gli eventi possono essere registrati in un account di archiviazione del cliente scelto per un'ulteriore analisi. L'esportazione dei log viene eseguita tramite le impostazioni di diagnostica per l'account di competenza nella portale di Azure.

Seguire i passaggi per creare un'impostazione di diagnostica per l'account Azure.

1. Creare una nuova impostazione di diagnostica per raccogliere i log e le metriche della piattaforma seguendo questo articolo: [creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](../azure-monitor/essentials/diagnostic-settings.md). Selezionare la destinazione solo come account di archiviazione di Azure.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Screenshot che mostra la creazione del log di diagnostica." lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Registrare gli eventi in un account di archiviazione. È consigliabile usare un account di archiviazione dedicato per archiviare i log di diagnostica. In questo articolo viene [creato un account di archiviazione](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Screenshot che illustra l'assegnazione dell'account di archiviazione per il log di diagnostica." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Attendere fino a 15 minuti per iniziare a ricevere i log nell'account di archiviazione appena creato. [Vedere conservazione dei dati e schema dei log delle risorse nell'account di archiviazione di Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Una volta configurati i log di diagnostica, gli eventi vengono propagati all'account di archiviazione.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

L'evento tiene traccia del ciclo di vita dell'analisi. Un'operazione di analisi segue lo stato di avanzamento attraverso una sequenza di Stati, da in coda, in esecuzione e infine da uno stato terminale di completato | Non riuscito | Annullata. Viene registrato un evento per ogni transizione di stato e lo schema dell'evento avrà le seguenti proprietà.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

Il log di esempio per un'istanza di evento è illustrato nella sezione seguente.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Passaggi successivi

[Visualizza asset Insights](asset-insights.md)