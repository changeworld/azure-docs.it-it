---
title: Registrazione diagnostica
titleSuffix: Azure Cognitive Services
description: Questa guida fornisce istruzioni dettagliate per abilitare la registrazione diagnostica per un servizio cognitivo di Azure. Questi log forniscono dati dettagliati e frequenti sul funzionamento di una risorsa usata per l'identificazione e il debug dei problemi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 4a78e233a41bf3b6682f52bac912528d6bcab76c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816336"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Abilitare la registrazione diagnostica per Servizi cognitivi di Azure

Questa guida fornisce istruzioni dettagliate per abilitare la registrazione diagnostica per un servizio cognitivo di Azure. Questi log forniscono dati dettagliati e frequenti sul funzionamento di una risorsa usata per l'identificazione e il debug dei problemi. Prima di continuare, è necessario avere un account Azure con una sottoscrizione ad almeno un servizio [cognitivo,](./bing-web-search/overview.md)ad esempio Ricerca Web Bing , [Servizi](./speech-service/overview.md)Voce o [LUIS.](./luis/what-is-luis.md)

## <a name="prerequisites"></a>Prerequisiti

Per abilitare la registrazione diagnostica, è necessario un percorso in cui archiviare i dati di log. Questa esercitazione usa Archiviazione di Azure e Log Analytics.

* [Archiviazione di Azure:](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) conserva i log di diagnostica per il controllo dei criteri, l'analisi statica o il backup. L'account di archiviazione non deve essere nella stessa sottoscrizione della risorsa che emette log, purché l'utente che configura l'impostazione abbia l'accesso RBAC di Azure appropriato a entrambe le sottoscrizioni.
* [Log Analytics:](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) strumento flessibile per la ricerca nei log e l'analisi che consente l'analisi dei log non elaborati generati da una risorsa di Azure.

> [!NOTE]
> Sono disponibili opzioni di configurazione aggiuntive. Per altre informazioni, vedere [Raccogliere e usare i dati di log dalle risorse di Azure.](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-diagnostic-log-collection"></a>Abilitare la raccolta dei log di diagnostica  

Per iniziare, abilitare la registrazione diagnostica usando il portale di Azure.

> [!NOTE]
> Per abilitare questa funzionalità tramite PowerShell o l'interfaccia della riga di comando di Azure, usare le istruzioni fornite in Raccogliere e utilizzare i dati [di log dalle risorse di Azure.](../azure-monitor/essentials/platform-logs-overview.md)

1. Passare al portale di Azure. Individuare e selezionare quindi una risorsa Servizi cognitivi. Ad esempio, la sottoscrizione a Ricerca Web Bing.   
2. Successivamente, dal menu di spostamento a sinistra individuare **Monitoraggio e** selezionare Impostazioni **di diagnostica.** Questa schermata contiene tutte le impostazioni di diagnostica create in precedenza per questa risorsa.
3. Se è presente una risorsa creata in precedenza che si desidera usare, è possibile selezionarla ora. In caso contrario, selezionare **+ Aggiungi impostazione di diagnostica**.
4. Immettere un nome per l'impostazione. Selezionare quindi **Archivia in un account di archiviazione** e Invia a log **Analytics.**
5. Quando viene richiesto di configurare, selezionare l'account di archiviazione e l'area di lavoro OMS che si desidera usare per archiviare i log di diagnostica. **Nota:** se non si ha un account di archiviazione o un'area di lavoro OMS, seguire le istruzioni per crearne uno.
6. Selezionare **Audit**, **RequestResponse** e **AllMetrics**. Impostare quindi il periodo di conservazione per i dati del log di diagnostica. Se un criterio di conservazione è impostato su zero, gli eventi per tale categoria di log vengono archiviati a tempo indeterminato.
7. Fare clic su **Salva**.

La registrazione dei dati può richiedere fino a due ore per eseguire query e analisi. Non è quindi necessario preoccuparsi se non si vede subito nulla.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Visualizzare ed esportare dati di diagnostica da Archiviazione di Azure

Archiviazione di Azure è una soluzione di archiviazione oggetti affidabile ottimizzata per l'archiviazione di grandi quantità di dati non strutturati. In questa sezione si apprenderà come eseguire query sull'account di archiviazione per ottenere il totale delle transazioni in un intervallo di tempo di 30 giorni ed esportare i dati in Excel.

1. Dal portale di Azure individuare la risorsa Archiviazione di Azure creata nell'ultima sezione.
2. Nel menu di spostamento a sinistra individuare **Monitoraggio** e selezionare **Metriche**.
3. Usare gli elenchi a discesa disponibili per configurare la query. Per questo esempio, si imposta l'intervallo di tempo su **Ultimi 30** giorni e la metrica su **Transazione**.
4. Al termine della query, verrà visualizzata una visualizzazione della transazione negli ultimi 30 giorni. Per esportare questi dati, usare il pulsante Esporta in **Excel** nella parte superiore della pagina.

Per altre informazioni sulle attività che è possibile eseguire con i dati di diagnostica, [Archiviazione di Azure](../storage/blobs/storage-blobs-introduction.md).

## <a name="view-logs-in-log-analytics"></a>Visualizzare i log in Log Analytics

Seguire queste istruzioni per esplorare i dati di Log Analytics per la risorsa.

1. Nell'portale di Azure, individuare e selezionare **Log Analytics** dal menu di spostamento a sinistra.
2. Individuare e selezionare la risorsa creata durante l'abilitazione della diagnostica.
3. In **Generale** individuare e selezionare **Log.** Da questa pagina è possibile eseguire query nei log.

### <a name="sample-queries"></a>Query di esempio

Ecco alcune query Kusto di base che è possibile usare per esplorare i dati di log.

Eseguire questa query per tutti i log di diagnostica Servizi cognitivi di Azure per un periodo di tempo specificato:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Eseguire questa query per visualizzare i 10 log più recenti:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Eseguire questa query per raggruppare le operazioni in base **alla risorsa**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Eseguire questa query per trovare il tempo medio necessario per eseguire un'operazione:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Eseguire questa query per visualizzare il volume di operazioni nel tempo suddiviso per OperationName con conteggi suddivisi ogni 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come abilitare la registrazione e sulle metriche e le categorie di log supportate dai vari servizi di Azure, leggere gli articoli Panoramica delle metriche [in](../azure-monitor/data-platform.md) Microsoft Azure e Panoramica dei log di diagnostica di [Azure.](../azure-monitor/essentials/platform-logs-overview.md)
* Per informazioni sugli hub eventi, leggere gli articoli seguenti:
  * [Che cos'è Hub eventi di Azure?](../event-hubs/event-hubs-about.md)
  * [Introduzione all'Hub eventi](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Leggere [Scaricare le metriche e i log di diagnostica da Archiviazione di Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
* Leggere [Comprendere le ricerche nei log Monitoraggio di Azure log](../azure-monitor/logs/log-query-overview.md).