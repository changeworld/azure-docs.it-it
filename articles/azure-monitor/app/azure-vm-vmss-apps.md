---
title: Monitorare le prestazioni in macchine virtuali di Azure-applicazione Azure Insights
description: Application Performance Monitoring per VM di Azure e set di scalabilità di macchine virtuali di Azure. Tempo di caricamento e di risposta del grafico, informazioni sulle dipendenze e impostazione di avvisi sulle prestazioni.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 0951d1d622f59de4780735fad78ac73649ea2369
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711482"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Distribuire l'agente di Application Insights di monitoraggio di Azure in macchine virtuali di Azure e set di scalabilità di macchine virtuali di Azure

L'abilitazione del monitoraggio per le applicazioni Web basate su .NET o Java in esecuzione in [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/) e [set di scalabilità di macchine virtuali di Azure](../../virtual-machine-scale-sets/index.yml) è ora più semplice che mai. Ottenere tutti i vantaggi derivanti dall'utilizzo di Application Insights senza modificare il codice.

Questo articolo illustra come abilitare il monitoraggio Application Insights usando l'agente Application Insights e fornisce indicazioni preliminari per l'automazione del processo per le distribuzioni su larga scala.
> [!IMPORTANT]
> Le applicazioni basate su **Java** in esecuzione in VM di Azure e vmss vengono monitorate con **[Application Insights agente Java 3,0](./java-in-process-agent.md)**, disponibile a livello generale.

> [!IMPORTANT]
> L'agente applicazione Azure Insights per le applicazioni ASP.NET in esecuzione in **macchine virtuali di Azure e vmss** è attualmente disponibile in anteprima pubblica. Per il monitoraggio delle applicazioni ASP.Net in esecuzione **in locale**, usare l' [agente applicazione Azure Insights per i server locali](./status-monitor-v2-overview.md), disponibile a livello generale e completamente supportato.
> La versione di anteprima per le macchine virtuali di Azure e VMSS viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate e alcune potrebbero avere funzionalità vincolate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Abilitare Application Insights

Esistono due modi per abilitare il monitoraggio delle applicazioni per le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure ospitate:

### <a name="auto-instrumentation-via-application-insights-agent"></a>Strumentazione automatica tramite Application Insights Agent

* Questo metodo è il più semplice da abilitare e non è necessaria alcuna configurazione avanzata. Viene spesso definito monitoraggio "Runtime".

* Per le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure, è consigliabile abilitare almeno questo livello di monitoraggio. In seguito, in base allo scenario specifico, è possibile valutare se la strumentazione manuale è necessaria.

> [!NOTE]
> La strumentazione automatica è attualmente disponibile solo per le applicazioni ospitate da IIS .NET e Java. Usare un SDK per instrumentare ASP.NET Core, Node.js e applicazioni Python ospitate in macchine virtuali di Azure e set di scalabilità di macchine virtuali.


#### <a name="net"></a>.NET

  * L'agente di Application Insights raccoglie automaticamente gli stessi segnali di dipendenza predefiniti come .NET SDK. Per altre informazioni, vedere [raccolta automatica delle dipendenze](./auto-collect-dependencies.md#net) .
        
#### <a name="java"></a>Java
  * Per Java, **[Application Insights java 3,0 Agent](./java-in-process-agent.md)** è l'approccio consigliato. Le librerie e i Framework più diffusi, nonché i log e le dipendenze vengono [raccolti automaticamente](./java-in-process-agent.md#auto-collected-requests-dependencies-logs-and-metrics), con numerose [configurazioni aggiuntive](./java-standalone-config.md)

### <a name="code-based-via-sdk"></a>Basato sul codice tramite SDK
    
#### <a name="net"></a>.NET
  * Per le app .NET, questo approccio è molto più personalizzabile, ma richiede [l'aggiunta di una dipendenza dai pacchetti NuGet di Application Insights SDK](./asp-net.md). Questo metodo consente inoltre di gestire gli aggiornamenti alla versione più recente dei pacchetti.

  * Se è necessario eseguire chiamate API personalizzate per tenere traccia degli eventi e delle dipendenze non acquisiti per impostazione predefinita con il monitoraggio basato su agenti, è necessario usare questo metodo. Per altre informazioni, vedere l' [articolo relativo all'API per eventi personalizzati e metriche](./api-custom-events-metrics.md) .

    > [!NOTE]
    > Solo per le app .NET: se viene rilevato sia il monitoraggio basato su agente che la strumentazione manuale basata su SDK, verranno rispettate solo le impostazioni di strumentazione manuale. Ciò consente di impedire l'invio di dati duplicati. Per ulteriori informazioni, vedere la [sezione relativa alla risoluzione dei problemi](#troubleshooting) di seguito.

#### <a name="net-core"></a>.NET Core
Per monitorare le applicazioni .NET Core, usare l' [SDK](./asp-net-core.md) 

#### <a name="java"></a>Java 

Se sono necessari dati di telemetria personalizzati aggiuntivi per le applicazioni Java, vedere che cosa [è disponibile](./java-in-process-agent.md#send-custom-telemetry-from-your-application), aggiungere [dimensioni personalizzate](./java-standalone-config.md#custom-dimensions)o usare [processori di telemetria](./java-standalone-telemetry-processors.md). 

#### <a name="nodejs"></a>Node.js

Per instrumentare l'applicazione Node.js, usare l' [SDK](./nodejs.md).

#### <a name="python"></a>Python

Per monitorare le app Python, usare l' [SDK](./opencensus-python.md).

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Gestire Application Insights agente per le applicazioni .NET in macchine virtuali di Azure con PowerShell

> [!NOTE]
> Prima di installare l'agente di Application Insights, è necessaria una stringa di connessione. [Creare una nuova risorsa Application Insights](./create-new-resource.md) o copiare la stringa di connessione da una risorsa di Application Insights esistente.

> [!NOTE]
> Non hai familiarità con PowerShell? Vedere la [Guida introduttiva](/powershell/azure/get-started-azureps).

Installare o aggiornare l'agente di Application Insights come estensione per le macchine virtuali di Azure
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> È possibile installare o aggiornare l'agente di Application Insights come estensione in più macchine virtuali su larga scala usando un ciclo di PowerShell.

Disinstalla l'estensione dell'agente Application Insights dalla macchina virtuale di Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Stato dell'estensione agente Application Insights query per la macchina virtuale di Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Ottiene l'elenco delle estensioni installate per la macchina virtuale di Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
È anche possibile visualizzare le estensioni installate nel pannello della [macchina virtuale di Azure](../../virtual-machines/extensions/overview.md) nel portale.

> [!NOTE]
> Verificare l'installazione facendo clic su Live Metrics Stream all'interno della risorsa Application Insights associata alla stringa di connessione usata per distribuire l'estensione agente Application Insights. Se si inviano dati da più macchine virtuali, selezionare le macchine virtuali di Azure di destinazione in nome server. Il flusso dei dati potrebbe richiedere fino a un minuto.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Gestire Application Insights agente per le applicazioni .NET nei set di scalabilità di macchine virtuali di Azure con PowerShell

Installare o aggiornare l'agente di Application Insights come estensione per il set di scalabilità di macchine virtuali di Azure
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Disinstallare l'estensione di monitoraggio dell'applicazione dai set di scalabilità di macchine virtuali di Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Eseguire query sullo stato dell'estensione di monitoraggio applicazioni per i set di scalabilità di macchine virtuali
```powershell
# Not supported by extensions framework
```

Ottiene l'elenco delle estensioni installate per i set di scalabilità di macchine virtuali di Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Suggerimenti per la risoluzione dei problemi relativi all'estensione Application Insights Monitoring Agent per le applicazioni .NET in esecuzione in macchine virtuali di Azure e set di scalabilità di macchine virtuali.

> [!NOTE]
> Le applicazioni .NET Core, Node.js e Python sono supportate solo in macchine virtuali di Azure e set di scalabilità di macchine virtuali di Azure tramite strumentazione manuale basata su SDK e pertanto i passaggi seguenti non si applicano a questi scenari.

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nelle directory seguenti:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [distribuire un'applicazione in un set di scalabilità di macchine virtuali di Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Configurare i test Web di disponibilità](monitor-web-app-availability.md) per ricevere un avviso se l'endpoint è inattivo.