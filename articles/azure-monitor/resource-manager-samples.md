---
title: Esempi di modelli di Resource Manager per Monitoraggio di Azure
description: Distribuire e configurare le funzionalità di Monitoraggio di Azure con i modelli di Resource Manager
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.openlocfilehash: ac2ed571359958feddd4d7952ea3b5f549688c77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046976"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Esempi di modelli di Resource Manager per Monitoraggio di Azure

Monitoraggio di Azure può essere distribuito e configurato su larga scala usando un [modello di Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Gli articoli seguenti forniscono modelli di esempio per diverse funzionalità di Monitoraggio di Azure. Questi esempi possono essere modificati in base a requisiti specifici e distribuiti con qualsiasi metodo standard per la distribuzione di modelli di Resource Manager. 

## <a name="deploying-the-sample-templates"></a>Distribuzione dei modelli di esempio
I passaggi di base per usare gli esempi sono:

1. Copiare il modello e salvarlo come file JSON.
2. Modificare i parametri per l'ambiente e salvarli come file JSON.
4. Distribuire il modello usando [qualsiasi metodo di distribuzione per i modelli di Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). 

Ad esempio, usare i comandi seguenti per distribuire il modello e il file dei parametri in un gruppo di risorse tramite PowerShell o l'interfaccia della riga di comando di Azure.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Elenco di modelli di esempio

- [Agenti](agents/resource-manager-agent.md): distribuire e configurare l'agente di Log Analytics e l'estensione di diagnostica.
- Avvisi
  - [Regole di avviso per i log](alerts/resource-manager-alerts-log.md): avvisi delle query su log e log attività di Azure.
  - [Regole di avviso delle metriche](alerts/resource-manager-alerts-metric.md): avvisi delle metriche che usano tipi diversi di logica.
- [Application Insights](app/resource-manager-app-resource.md)
- [Impostazioni di diagnostica](essentials/resource-manager-diagnostic-settings.md): creare le impostazioni di diagnostica per inoltrare log e metriche di diversi tipi di risorse.
- [Query su log](logs/resource-manager-log-queries.md): creare query su log salvate in un'area di lavoro Log Analytics.
- [Area di lavoro Log Analytics](logs/resource-manager-workspace.md): creare un'area di lavoro Log Analytics e configurare la raccolta di origini dati diverse dall'agente di Log Analytics.
- [Cartelle di lavoro](visualize/resource-manager-workbooks.md): creare cartelle di lavoro.
- [Container Insights](containers/resource-manager-container-insights.md) : caricare i cluster in informazioni dettagliate sul contenitore.
- [Monitoraggio di Azure per le macchine virtuali](vm/resource-manager-vminsights.md): eseguire l'onboarding di VM in Monitoraggio di Azure per le macchine virtuali.



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [modelli di Resource Manager](../azure-resource-manager/templates/overview.md)