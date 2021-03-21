---
title: Abilitare l'integrità Guest di VM Insights (anteprima)
description: Viene descritto come abilitare l'integrità Guest di VM Insights nella sottoscrizione e come eseguire l'onboarding delle macchine virtuali.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.custom: references_regions
ms.openlocfilehash: 5d4ff622f69445880c0de8cb74dc1aeee422c89b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052161"
---
# <a name="enable-vm-insights-guest-health-preview"></a>Abilitare l'integrità Guest di VM Insights (anteprima)
L'integrità Guest di VM Insights consente di visualizzare l'integrità di una macchina virtuale in base a quanto definito da un set di misurazioni delle prestazioni campionate a intervalli regolari. Questo articolo descrive come abilitare questa funzionalità nella sottoscrizione e come abilitare il monitoraggio Guest per ogni macchina virtuale.

## <a name="current-limitations"></a>Limitazioni correnti
L'integrità Guest di VM Insights presenta le limitazioni seguenti nell'anteprima pubblica:

- Attualmente sono supportate solo macchine virtuali di Azure. Azure Arc per server non è attualmente supportato.


## <a name="supported-operating-systems"></a>Sistemi operativi supportati
La macchina virtuale deve eseguire uno dei sistemi operativi seguenti: 

  - Ubuntu 16,04 LTS, Ubuntu 18,04 LTS
  - Windows Server 2012 o versioni successive

## <a name="supported-regions"></a>Aree supportate

La macchina virtuale deve trovarsi in una delle aree seguenti:

- Australia centrale
- Australia orientale
- Australia sud-orientale
- Canada centrale
- India centrale
- Stati Uniti centrali
- Asia orientale
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti orientali 2 EUAP
- Francia centrale
- Germania centro-occidentale
- Giappone orientale
- Corea centrale
- Stati Uniti centro-settentrionali
- Europa settentrionale
- Stati Uniti centro-meridionali
- Sudafrica settentrionale
- Asia sud-orientale
- Svizzera settentrionale
- Regno Unito meridionale
- Regno Unito occidentale
- Stati Uniti centro-occidentali
- Europa occidentale
- Stati Uniti occidentali
- Stati Uniti occidentali 2


Log Analytics area di lavoro deve trovarsi in una delle aree seguenti:

- Australia centrale
- Australia orientale
- Australia sud-orientale
- Canada centrale
- India del Canada
- Stati Uniti centrali
- Asia orientale
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti orientali 2 EUAP
- Francia centrale
- Giappone orientale
- Stati Uniti centro-settentrionali
- Europa settentrionale
- Stati Uniti centro-meridionali
- Asia sud-orientale
- Svizzera settentrionale
- Regno Unito meridionale
- Area Europa occidentale
- Stati Uniti occidentali
- Stati Uniti occidentali 2

## <a name="prerequisites"></a>Prerequisiti

- La macchina virtuale deve essere caricata in VM Insights.
- L'utente che esegue i passaggi di caricamento deve avere un accesso minimo a livello di collaboratore alla sottoscrizione in cui si trovano la regola di raccolta dati e macchina virtuale.
- I provider di risorse di Azure necessari devono essere registrati come descritto nella sezione seguente.

## <a name="register-required-azure-resource-providers"></a>Registrare i provider di risorse di Azure necessari
Per abilitare l'integrità Guest di VM Insights, è necessario che i provider di risorse di Azure seguenti siano registrati per la sottoscrizione. 

- Microsoft.WorkloadMonitor
- Microsoft.Insights

È possibile usare uno dei metodi disponibili per registrare un provider di risorse come descritto in [tipi e provider di risorse di Azure](../../azure-resource-manager/management/resource-providers-and-types.md). È anche possibile usare il comando di esempio seguente usando armclient, postazione o un altro metodo per effettuare una chiamata autenticata a Azure Resource Manager:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Abilitare una macchina virtuale usando il portale di Azure
Quando si abilita l'integrità guest per una macchina virtuale nel portale di Azure, vengono eseguite tutte le configurazioni necessarie. Ciò include la creazione della regola di raccolta dati richiesta, l'installazione dell'estensione per l'integrità Guest nella macchina virtuale e la creazione di un'associazione con la regola di raccolta dati.

Dalla visualizzazione **Introduzione** in VM Insights, fare clic sul collegamento accanto al messaggio di aggiornamento per una macchina virtuale, quindi fare clic sul pulsante **Aggiorna** . È anche possibile selezionare varie macchine virtuali da aggiornare insieme.

![Abilitare la funzionalità integrità nella macchina virtuale](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Abilitare una macchina virtuale usando il modello di Azure Resource Manager
Sono necessari tre passaggi per abilitare le macchine virtuali con Azure Resource Manager.

- Creare una regola di raccolta dati.
- Installare l'estensione di integrità Guest in ogni macchina virtuale
- Creare un'associazione tra la macchina virtuale e la regola di raccolta dati.

### <a name="create-data-collection-rule-dcr"></a>Creare una regola di raccolta dati (DCR)

> [!NOTE]
> Se si abilita una macchina virtuale usando il portale di Azure, viene creata automaticamente la regola di raccolta dati descritta di seguito. In questo caso, non è necessario eseguire questo passaggio.

La configurazione per i monitoraggi in integrità Guest di VM Insights è archiviata in [Data Collection Rules (DCR)](../agents/data-collection-rule-overview.md). Ogni macchina virtuale con l'estensione per l'integrità Guest dovrà avere un'associazione con questa regola.

> [!NOTE]
> È possibile creare regole di raccolta dati aggiuntive per modificare la configurazione predefinita dei monitoraggi, come descritto in [configurare il monitoraggio in integrità Guest di VM Insights (anteprima)](vminsights-health-configure.md).

Il modello richiede i valori per i parametri seguenti:

- **defaultHealthDataCollectionRuleName**: mantiene il nome predefinito definito nel modello.
- **destinationWorkspaceResourceId**: ID risorsa dell'area di lavoro log Analytics usata per la raccolta dei dati della macchina virtuale.
- **dataCollectionRuleLocation**: area della regola di raccolta dati. Deve corrispondere all'area dell'area di lavoro Log Analytics.


Distribuire il modello usando un [metodo di distribuzione per i modelli di gestione risorse](../../azure-resource-manager/templates/deploy-powershell.md). I comandi seguenti distribuiscono il modello e il file dei parametri usando PowerShell o l'interfaccia della riga di comando di Azure

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

La regola di raccolta dati definita nel modello di Gestione risorse seguente Abilita tutti i monitoraggi per le macchine virtuali con l'estensione per l'integrità Guest. Deve includere origini dati per ogni contatore delle prestazioni utilizzato dai monitoraggi.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>File di parametri di esempio

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Installare l'estensione di integrità Guest e associarla a una regola di raccolta dati
Usare il modello di Gestione risorse seguente per abilitare una macchina virtuale per l'integrità Guest. In questo modo viene installata l'estensione di integrità Guest e viene creata l'associazione con la regola di raccolta dati. È possibile distribuire questo modello usando un [metodo di distribuzione per i modelli di gestione risorse](../../azure-resource-manager/templates/deploy-powershell.md).


Ad esempio, usare i comandi seguenti per distribuire il modello e il file dei parametri in un gruppo di risorse tramite PowerShell o l'interfaccia della riga di comando di Azure.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>File modello

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>File di parametri di esempio

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Personalizzare i monitoraggi abilitati da VM Insights](vminsights-health-configure.md)