---
title: Abilitare Snapshot Debugger per le app .NET e .NET Core in funzioni di Azure | Microsoft Docs
description: Abilitare Snapshot Debugger per le app .NET e .NET Core in funzioni di Azure
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: 48eb3cf81384446a07fea69572ac16e0b80cee38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025629"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Abilitare Snapshot Debugger per le app .NET e .NET Core in funzioni di Azure

Snapshot Debugger attualmente funziona per le app ASP.NET e ASP.NET Core in esecuzione in funzioni di Azure nei piani di servizio Windows.

È consigliabile eseguire l'applicazione nel livello di servizio Basic o superiore quando si usa Snapshot Debugger.

Per la maggior parte delle applicazioni, i livelli di servizio gratuito e condiviso non hanno memoria o spazio su disco sufficiente per salvare gli snapshot.

## <a name="prerequisites"></a>Prerequisiti

* [Abilitare il monitoraggio Application Insights nel app per le funzioni](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>Abilita Snapshot Debugger

Se si esegue un tipo diverso di servizio di Azure, di seguito sono riportate le istruzioni per abilitare Snapshot Debugger su altre piattaforme supportate:
* [Servizio app di Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Servizi Service Fabric di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Microsoft Azure e set di scalabilità di macchine virtuali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Computer fisici o virtuali locali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Per abilitare Snapshot Debugger nell'app per le funzioni, è necessario aggiornare il `host.json` file aggiungendo la proprietà `snapshotConfiguration` come definito di seguito e ridistribuire la funzione.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

Snapshot Debugger è preinstallato come parte del runtime di funzioni di Azure, che per impostazione predefinita è disabilitato.

Poiché Snapshot Debugger è incluso nel runtime di funzioni di Azure, non è necessario aggiungere pacchetti NuGet aggiuntivi o impostazioni dell'applicazione.

Come riferimento, per una semplice app per le funzioni (.NET Core), di seguito viene illustrato il modo in cui l'aspetto di `.csproj` , `{Your}Function.cs` e `host.json` dopo l'abilitazione snapshot debugger.

Csproj progetto

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Classe Function

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

File host

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Abilita Snapshot Debugger per altri cloud

Attualmente le uniche aree che richiedono modifiche all'endpoint sono [Azure per enti pubblici](../../azure-government/compare-azure-government-global-azure.md#application-insights) e [Azure Cina](/azure/china/resources-developer-guide).

Di seguito è riportato un esempio di `host.json` aggiornamento con l'endpoint dell'agente cloud per il governo degli Stati Uniti:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

Di seguito sono elencate le sostituzioni supportate dell'endpoint Snapshot Debugger Agent:

|Proprietà    | Cloud per enti pubblici degli Stati Uniti | Cloud per la Cina |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>Disabilitare Snapshot Debugger

Per disabilitare Snapshot Debugger nell'app per le funzioni, è sufficiente aggiornare il file impostando `host.json` `false` sulla proprietà `snapshotConfiguration.isEnabled` .

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

È consigliabile avere Snapshot Debugger abilitata in tutte le app per semplificare la diagnostica delle eccezioni delle applicazioni.

## <a name="next-steps"></a>Passaggi successivi

- Generare il traffico all'applicazione che può attivare un'eccezione. Attendere da 10 a 15 minuti per l'invio degli snapshot all'istanza di Application Insights.
- [Visualizzare gli snapshot](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) nel portale di Azure.
- Personalizzare Snapshot Debugger configurazione in base al caso d'uso nell'app per le funzioni. Per ulteriori informazioni, vedere la pagina [relativa alla configurazione dello snapshot in host.js](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Per informazioni sulla risoluzione dei problemi di Snapshot Debugger, vedere [snapshot debugger risoluzione dei](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)problemi.