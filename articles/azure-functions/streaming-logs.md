---
title: Trasmettere i log di esecuzione in Funzioni di Azure
description: 115-145 caratteri inclusi gli spazi. Questo sunto viene visualizzato nei risultati della ricerca.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4afb1068acda69c9dd65a423d887abea80c695cd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830899"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Abilitare i log di esecuzione di streaming in Funzioni di Azure

Durante lo sviluppo di un'applicazione, spesso si vuole vedere cosa viene scritto nei log quasi in tempo reale durante l'esecuzione in Azure.

Esistono due modi per visualizzare il flusso dei file di log generati dalle esecuzioni delle funzioni.

* **Streaming dei log predefinito**: la piattaforma del servizio app consente di visualizzare un flusso dei file di log dell'applicazione. Questa opzione equivale all'output visualizzato quando si esegue il debug delle funzioni durante lo [sviluppo locale](functions-develop-local.md) o quando si usa la scheda **Test** nel portale. Vengono visualizzate tutte le informazioni basate sui log. Per altre informazioni, vedere [Eseguire lo streaming dei log](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Questo metodo di streaming supporta solo una singola istanza e non può essere usato con un'app in esecuzione su Linux con un piano a consumo.

* **Live Metrics Stream**: se l'app per le funzioni è [connessa ad Application Insights](configure-monitoring.md#enable-application-insights-integration), è possibile visualizzare i dati di log e altre metriche quasi in tempo reale nel portale di Azure usando [Live Metrics Stream](../azure-monitor/app/live-stream.md). È opportuno usare questo metodo quando si monitorano funzioni in esecuzione su più istanze o su Linux con un piano a consumo. Questo metodo usa [dati campionati](configure-monitoring.md#configure-sampling).

I flussi di log possono essere visualizzati sia nel portale sia nella maggior parte degli ambienti di sviluppo locali. 

## <a name="portal"></a>Portale

Nel portale è possibile visualizzare entrambi i tipi di flussi di log.

### <a name="built-in-log-streaming"></a>Streaming dei log predefinito

Per visualizzare i log in streaming nel portale, selezionare la scheda **Funzionalità della piattaforma** nell'app per le funzioni. Nella sezione **Monitoraggio** scegliere quindi **Streaming dei log**.

![Abilitare i log in streaming nel portale](./media/functions-monitoring/enable-streaming-logs-portal.png)

In questo modo, l'app viene connessa al servizio di streaming dei log e i log dell'applicazione vengono visualizzati nella finestra. È possibile alternare tra i **Log applicazioni** e i **Log del server Web**.  

![Visualizzare i log in streaming nel portale](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Flusso di metriche live

Per visualizzare il servizio Live Metrics Stream per l'app, selezionare la scheda **Panoramica** dell'app per le funzioni. Dopo aver abilitato Application Insights, nella sezione **Funzionalità configurate** viene visualizzato un collegamento di **Application Insights**, che indirizza alla pagina di Application Insights per l'app in uso.

In Application Insights selezionare **Live Metrics Stream**. Nella sezione [Telemetria di esempio](configure-monitoring.md#configure-sampling) vengono visualizzate **campioni di voci di log**.

![Visualizzare Live Metrics Stream nel portale](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Core Tools

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per abilitare i log in streaming, è possibile usare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Usare i comandi seguenti per eseguire l'accesso, scegliere la sottoscrizione e trasmettere in streaming i file di log:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

Per abilitare i log in streaming, è possibile usare [Azure PowerShell](/powershell/azure/). Per PowerShell, usare il [comando Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) per abilitare la registrazione nell'app per le funzioni, come illustrato nel frammento di codice seguente: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Per altre informazioni, vedere [l'esempio di codice completo](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="next-steps"></a>Passaggi successivi

+ [Monitorare Funzioni di Azure](functions-monitoring.md)
+ [Analizzare Funzioni di Azure telemetria in Application Insights](analyze-telemetry-data.md)
