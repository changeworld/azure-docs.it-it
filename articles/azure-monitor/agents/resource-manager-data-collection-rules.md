---
title: Esempi di modelli di Resource Manager per le regole di raccolta dati
description: Modelli di Azure Resource Manager di esempio per creare associazioni tra regole di raccolta dati e macchine virtuali in Monitoraggio di Azure.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: d241cb5d7ece260de42088eecfd669e5d3f40096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592286"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Esempi di modelli di Resource Manager per le regole di raccolta dati in Monitoraggio di Azure
Questo articolo include [modelli di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) di esempio per creare un'associazione tra una [regola di raccolta dati](data-collection-rule-overview.md) e l' [agente di monitoraggio di Azure](./azure-monitor-agent-overview.md). Ogni esempio include un file modello e un file di parametri con valori di esempio da fornire al modello.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Creare un'associazione con la macchina virtuale di Azure

Nell'esempio seguente viene creata un'associazione tra una macchina virtuale di Azure e una regola di raccolta dati.

### <a name="template-file"></a>File modello

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Creare un'associazione con Azure Arc

L'esempio seguente crea un'associazione tra un server abilitato per Azure Arc e una regola di raccolta dati.

### <a name="template-file"></a>File modello

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Passaggi successivi

* [Passare ad altri esempi per Monitoraggio di Azure](../resource-manager-samples.md).
* [Altre informazioni sull'agente di Log Analytics](./log-analytics-agent.md).
* [Altre informazioni sull'estensione di diagnostica](./diagnostics-extension-overview.md).