---
title: Eseguire contenitori init
description: Eseguire i contenitori init in Istanze di Azure Container eseguire le attività di installazione in un gruppo di contenitori prima dell'esecuzione dei contenitori dell'applicazione.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 9ccaf1a67d6ca3bcff422acb591b528cc72a9608
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763938"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Eseguire un contenitore init per le attività di configurazione in un gruppo di contenitori

Istanze di Azure Container supporta i *contenitori init* in un gruppo di contenitori. I contenitori init vengono eseguiti fino al completamento prima dell'avvio del contenitore dell'applicazione o dei contenitori. Analogamente ai contenitori init di [Kubernetes,](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)usare uno o più contenitori init per eseguire la logica di inizializzazione per i contenitori di app, ad esempio l'impostazione di account, l'esecuzione di script di installazione o la configurazione di database.

Questo articolo illustra come usare un modello Azure Resource Manager per configurare un gruppo di contenitori con un contenitore init.

## <a name="things-to-know"></a>Informazioni importanti

* **Versione dell'API:** è necessario almeno Istanze di Azure Container'API versione 2019-12-01 per distribuire i contenitori init. Eseguire la distribuzione `initContainers` usando una proprietà in un file [YAML](container-instances-multi-container-yaml.md) o in [un Resource Manager modello](container-instances-multi-container-group.md).
* **Ordine di esecuzione:** i contenitori Init vengono eseguiti nell'ordine specificato nel modello e prima di altri contenitori. Per impostazione predefinita, è possibile specificare un massimo di 59 contenitori init per gruppo di contenitori. Nel gruppo deve essere presente almeno un contenitore non init.
* **Ambiente host:** i contenitori Init vengono eseguiti nello stesso hardware del resto dei contenitori nel gruppo.
* **Risorse:** non si specificano le risorse per i contenitori init. Vengono concesse le risorse totali, ad esempio CPU e memoria disponibili per il gruppo di contenitori. Durante l'esecuzione di un contenitore init, non vengono eseguiti altri contenitori nel gruppo.
* **Proprietà supportate:** i contenitori Init possono usare le proprietà del gruppo, ad esempio volumi, segreti e identità gestite. Tuttavia, non possono usare porte o un indirizzo IP se configurati per il gruppo di contenitori. 
* **Criteri di riavvio:** ogni contenitore init deve uscire correttamente prima dell'avvio del contenitore successivo nel gruppo. Se un contenitore init non viene chiuso correttamente, l'azione di riavvio dipende dai criteri [di riavvio](container-instances-restart-policy.md) configurati per il gruppo:

    |Criteri nel gruppo  |Criteri init  |
    |---------|---------|
    |Always     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Mai     |Mai         |
* **Addebiti:** il gruppo di contenitori comporta addebiti dalla prima distribuzione di un contenitore init.

## <a name="resource-manager-template-example"></a>Esempio di modello di Resource Manager

Per iniziare, copiare il codice JSON seguente in un nuovo file denominato `azuredeploy.json`. Il modello configura un gruppo di contenitori con un contenitore init e due contenitori di applicazioni:

* Il *contenitore init1* esegue l'immagine [busybox](https://hub.docker.com/_/busybox) da Docker Hub. Viene sospensione per 60 secondi e quindi scrive una stringa della riga di comando in un file in un [volume emptyDir.](container-instances-volume-emptydir.md)
* Entrambi i contenitori di applicazioni eseguono l'immagine `aci-wordcount` del contenitore Microsoft:
    * Il *contenitore hamlet* esegue l'app wordcount nella configurazione predefinita, contando le frequenze delle parole nel play *Hamlet di* Shakespeare.
    * Il *contenitore* dell'app giulietta legge la stringa della riga di comando dal volume emptDir per eseguire l'app wordcount invece su Romeo e *Giulietta di* Shakespeare.

Per altre informazioni ed esempi sull'uso `aci-wordcount` dell'immagine, vedere [Impostare le variabili di ambiente nelle istanze del contenitore](container-instances-environment-variables.md).

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
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>Distribuire il modello

Creare un gruppo di risorse con il comando [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Distribuire il modello con il [comando az deployment group create.][az-deployment-group-create]

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

In un gruppo con un contenitore init, il tempo di distribuzione aumenta a causa del tempo necessario per il completamento del contenitore o dei contenitori init.


## <a name="view-container-logs"></a>Visualizzare i log dei contenitori

Per verificare che il contenitore init sia stato eseguito correttamente, visualizzare l'output del log dei contenitori dell'app usando [il comando az container logs.][az-container-logs] L'argomento `--container-name` specifica il contenitore da cui effettuare il pull dei log. In questo esempio eseguire il pull dei log per i *contenitori hamlet* e *juliet,* che mostrano output del comando diverso:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Output:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Output:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passaggi successivi

I contenitori Init consentono di eseguire attività di configurazione e inizializzazione per i contenitori dell'applicazione. Per altre informazioni sull'esecuzione di contenitori basati su attività, vedere [Eseguire attività in contenitori con criteri di riavvio](container-instances-restart-policy.md).

Istanze di Azure Container fornisce altre opzioni per modificare il comportamento dei contenitori dell'applicazione. Alcuni esempi:

* [Impostare le variabili di ambiente nelle istanze del contenitore](container-instances-environment-variables.md)
* [Impostare la riga di comando in un'istanza del contenitore per eseguire l'override dell'operazione della riga di comando predefinita](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-container-logs]: /cli/azure/container#az_container_logs
