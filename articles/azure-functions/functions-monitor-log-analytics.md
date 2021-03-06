---
title: Monitoraggio di funzioni di Azure con i log di monitoraggio di Azure
description: Informazioni su come usare i log di monitoraggio di Azure con funzioni di Azure per monitorare le esecuzioni di funzioni.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b6ce9e77421df0563810fd7f8255720c1fd2d0c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591086"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitoraggio di funzioni di Azure con i log di monitoraggio di Azure

Funzioni di Azure offre un'integrazione con i [log di monitoraggio di Azure](../azure-monitor/logs/data-platform-logs.md) per monitorare le funzioni. Questo articolo illustra come configurare funzioni di Azure per inviare log generati dal sistema e generati dall'utente ai log di monitoraggio di Azure.

I log di monitoraggio di Azure offrono la possibilità di consolidare i log da diverse risorse nella stessa area di lavoro, dove possono essere analizzati con le [query](../azure-monitor/logs/log-query-overview.md) per recuperare, consolidare e analizzare rapidamente i dati raccolti.  È possibile creare e testare query usando [Log Analytics](../azure-monitor/logs/log-query-overview.md) nel portale di Azure e quindi analizzare direttamente i dati usando questi strumenti oppure salvare le query per usarle con [visualizzazioni](../azure-monitor/visualizations.md) o [regole degli avvisi](../azure-monitor/alerts/alerts-overview.md).

Monitoraggio di Azure usa una versione del [linguaggio di query Kusto](/azure/kusto/query/), usato da Esplora dati di Azure e adatto a query semplici nei log, ma che include anche funzionalità avanzate come le aggregazioni, i join e le analisi intelligenti. È possibile apprendere rapidamente il linguaggio di query usando le [numerose lezioni](../azure-monitor/logs/get-started-queries.md) disponibili.

> [!NOTE]
> L'integrazione con i log di monitoraggio di Azure è attualmente disponibile in anteprima pubblica per le app per le funzioni V2 e V3 in esecuzione nei piani di hosting a consumo, Premium e dedicato di Windows.

## <a name="setting-up"></a>Configurazione

1. Dalla sezione **monitoraggio** dell'app per le funzioni nella [portale di Azure](https://portal.azure.com)selezionare impostazioni di **diagnostica** e quindi selezionare Aggiungi impostazioni di **diagnostica**.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Selezionare le impostazioni di diagnostica":::

1. Nella pagina **impostazioni di diagnostica** , in **Dettagli categoria** e **log**, scegliere **FunctionAppLogs**.

   La tabella **FunctionAppLogs** contiene i log desiderati.

1. In **Dettagli destinazione** scegliere **Invia a log Analytics** e quindi selezionare l'area di **lavoro log Analytics**. 

1. Immettere un **nome per le impostazioni di diagnostica** e quindi fare clic su **Salva**.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Aggiungere un'impostazione di diagnostica":::

## <a name="user-generated-logs"></a>Log generati dall'utente

Per generare log personalizzati, utilizzare l'istruzione di registrazione specifica del linguaggio. Ecco i frammenti di codice di esempio:


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Esecuzione di query sui log

Per eseguire una query sui log generati:
 
1. Dall'app per le funzioni selezionare **impostazioni di diagnostica**. 

1. Dall'elenco **impostazioni di diagnostica** selezionare l'area di lavoro log Analytics configurata per l'invio dei log di funzione. 

1. Nella pagina **area di lavoro log Analytics** selezionare **log**.

   Funzioni di Azure scrive tutti i log nella tabella **FunctionAppLogs** in **LogManagement**. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Finestra query nell'area di lavoro Log Analytics":::

Ecco alcune query di esempio:

### <a name="all-logs"></a>Tutti i log

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Log di funzione specifici

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Eccezioni

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di funzioni di Azure](functions-overview.md).
- Altre informazioni sui [log di monitoraggio di Azure](../azure-monitor/logs/data-platform-logs.md).
- Altre informazioni sul [linguaggio di query](../azure-monitor/logs/get-started-queries.md).
