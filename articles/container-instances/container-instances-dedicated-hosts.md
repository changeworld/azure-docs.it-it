---
title: Eseguire la distribuzione in un host dedicato
description: Usare un host dedicato per ottenere un vero isolamento a livello di host per i Istanze di Azure Container di lavoro
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 72ad05eea9232e7a0d6ac24d1d0d22a971a7f6a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790834"
---
# <a name="deploy-on-dedicated-hosts"></a>Eseguire la distribuzione in host dedicati

"Dedicated" è uno sku Istanze di Azure Container (ACI) che fornisce un ambiente di calcolo isolato e dedicato per l'esecuzione sicura di contenitori. L'uso dello sku dedicato consente a ogni gruppo di contenitori di avere un server fisico dedicato in un data center di Azure, garantendo l'isolamento completo del carico di lavoro per soddisfare i requisiti di sicurezza e conformità dell'organizzazione. 

Lo sku dedicato è appropriato per i carichi di lavoro del contenitore che richiedono l'isolamento del carico di lavoro dal punto di vista del server fisico.

## <a name="prerequisites"></a>Prerequisiti

> [!NOTE]
> A causa di alcune limitazioni correnti, non è garantita l'approvazione di tutte le richieste di aumento dei limiti.

* Il limite predefinito per qualsiasi sottoscrizione di usare lo sku dedicato è 0. Se si vuole usare questo sku per le distribuzioni del contenitore di produzione, creare una richiesta supporto di Azure [per][azure-support] aumentare il limite.

## <a name="use-the-dedicated-sku"></a>Usare lo sku dedicato

> [!IMPORTANT]
> L'uso dello sku dedicato è disponibile solo nella versione api più recente (2019-12-01) attualmente in fase di implementazione. Specificare questa versione dell'API nel modello di distribuzione.
>

A partire dalla versione 2019-12-01 dell'API, è disponibile una proprietà nella sezione delle proprietà del gruppo di contenitori di un modello di distribuzione, necessaria per una distribuzione `sku` ACI. Attualmente, è possibile usare questa proprietà come parte di un modello Azure Resource Manager di distribuzione per ACI. Per altre informazioni sulla distribuzione di risorse ACI con un modello, vedere Esercitazione: Distribuire un gruppo [multi-contenitore](./container-instances-multi-container-group.md)usando un modello Resource Manager contenitore . 

La `sku` proprietà può avere uno dei valori seguenti:
* `Standard` - la scelta di distribuzione ACI standard, che garantisce comunque la sicurezza a livello di hypervisor 
* `Dedicated` : usato per l'isolamento a livello di carico di lavoro con host fisici dedicati per il gruppo di contenitori

## <a name="modify-your-json-deployment-template"></a>Modificare il modello di distribuzione JSON

Nel modello di distribuzione modificare o aggiungere le proprietà seguenti:
* In `resources` impostare `apiVersion` su `2019-12-01` .
* Nelle proprietà del gruppo di contenitori aggiungere una `sku` proprietà con valore `Dedicated` .

Di seguito è riportato un frammento di codice di esempio per la sezione resources di un modello di distribuzione del gruppo di contenitori che usa lo SKU dedicato:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Di seguito è riportato un modello completo che distribuisce un gruppo di contenitori di esempio che esegue una singola istanza di contenitore:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Distribuire il gruppo di contenitori

Se il file del modello di distribuzione è stato creato e modificato sul desktop, è possibile caricarlo nella directory Cloud Shell trascinando il file al suo interno. 

Creare un gruppo di risorse con il comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il modello con il [comando az deployment group create.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure. Una distribuzione corretta viene eseguita in un host dedicato.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
