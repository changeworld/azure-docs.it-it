---
title: Esempi di modelli di Resource Manager per Monitoraggio di Azure
description: Distribuire e configurare le funzionalità di Monitoraggio di Azure con i modelli di Resource Manager
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: 0791ccf10c76f2a1781bf373c674f606ca365fff
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627869"
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
- [Monitoraggio di Azure per i contenitori](containers/resource-manager-container-insights.md): eseguire l'onboarding di cluster in Monitoraggio di Azure per i contenitori.
- [Monitoraggio di Azure per le macchine virtuali](vm/resource-manager-vminsights.md): eseguire l'onboarding di VM in Monitoraggio di Azure per le macchine virtuali.



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [modelli di Resource Manager](../azure-resource-manager/templates/overview.md)