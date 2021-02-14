---
title: Monitorare a livello di codice una data factory di Azure
description: Informazioni su come monitorare una pipeline in una data factory con diversi Software Development Kit (SDK).
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 038da033c2bdf78a0a2547cc713944bc11bf093d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379897"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Monitorare a livello di codice una data factory di Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Informazioni su come monitorare una pipeline in una data factory con diversi Software Development Kit (SDK). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Intervallo di dati

Data Factory memorizza i dati di esecuzione della pipeline solo per 45 giorni. Quando si eseguono query a livello di codice relative ai dati delle esecuzioni della pipeline di Data Factory, ad esempio con il comando di PowerShell `Get-AzDataFactoryV2PipelineRun` non sono presenti date massime per i parametri facoltativi `LastUpdatedAfter` e `LastUpdatedBefore`. Tuttavia, se si esegue una query per i dati relativi all'anno precedente, ad esempio, non si riceverà un errore ma solo i dati di esecuzione della pipeline dagli ultimi 45 giorni.

Se si vuole tenere i dati di esecuzione della pipeline per più di 45 giorni, configurare la registrazione diagnostica con [monitoraggio di Azure](monitor-using-azure-monitor.md).

## <a name="pipeline-run-information"></a>Informazioni sull'esecuzione della pipeline

Per le proprietà di esecuzione della pipeline, vedere informazioni di [riferimento sull'API PipelineRun](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/get#pipelinerun). Un'esecuzione di pipeline presenta uno stato diverso durante il ciclo di vita. i possibili valori dello stato di esecuzione sono elencati di seguito:

* Queued
* InProgress
* Completato
* Non riuscito
* Canceling
* Cancellati

## <a name="net"></a>.NET
Per una procedura dettagliata completa di creazione e monitoraggio di una pipeline con .NET SDK, vedere [creare una data factory e una pipeline con .NET](quickstart-create-data-factory-dot-net.md).

1. Aggiungere il codice seguente per controllare continuamente lo stato dell'esecuzione della pipeline fino al termine della copia dei dati.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Aggiungere il codice seguente per recuperare i dettagli dell'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Per la documentazione completa su .NET SDK, vedere le [Data Factory .NET SDK reference](/dotnet/api/microsoft.azure.management.datafactory) (Informazioni di riferimento su .NET SDK di Data Factory).

## <a name="python"></a>Python
Per una procedura dettagliata completa di creazione e monitoraggio di una pipeline con Python SDK, vedere [creare una data factory e una pipeline con Python](quickstart-create-data-factory-python.md).

Per monitorare l'esecuzione della pipeline, aggiungere il codice seguente

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Per la documentazione completa su Python SDK, vedere [Data Factory](/python/api/overview/azure/datafactory).

## <a name="rest-api"></a>API REST
Per una procedura dettagliata completa di creazione e monitoraggio di una pipeline con l'API REST, vedere [creare una data factory e una pipeline usando l'API REST](quickstart-create-data-factory-rest-api.md).
 
1. Eseguire lo script seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Eseguire lo script seguente per recuperare i dettagli sull'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Per la documentazione completa sull'API REST, vedere [Data Factory REST API Reference](/rest/api/datafactory/) (Informazioni di riferimento sull'API REST di Data Factory).

## <a name="powershell"></a>PowerShell
Per una procedura dettagliata completa di creazione e monitoraggio di una pipeline con PowerShell, vedere [creare una data factory e una pipeline con PowerShell](quickstart-create-data-factory-powershell.md).

1. Eseguire lo script seguente per verificare continuamente lo stato di esecuzione della pipeline fino al termine della copia dei dati.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ( ($run.Status -ne "InProgress") -and ($run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $run.Status)
                $run
                break
            }
            Write-Output ("Pipeline is running...status: " + $run.Status)
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Eseguire lo script seguente per recuperare i dettagli sull'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Per una documentazione completa sui cmdlet di PowerShell, vedere [Data Factory PowerShell cmdlet reference](/powershell/module/az.datafactory) (Informazioni di riferimento sui cmdlet di PowerShell per Data Factory).

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sull'utilizzo di Monitoraggio di Azure per monitorare le pipeline di Data Factory, vedere l'articolo sul [monitoraggio delle pipeline mediante Monitoraggio di Azure](monitor-using-azure-monitor.md). 

