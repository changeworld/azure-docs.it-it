---
title: Creare dashboard di Azure a livello di codice
description: Usare un dashboard nell'portale di Azure come modello per creare dashboard di Azure a livello di codice. Include informazioni di riferimento JSON.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: 416eeb772e347b28fcb4a4dcc93c746562ea3571
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767060"
---
# <a name="programmatically-create-azure-dashboards"></a>Creare dashboard di Azure a livello di codice

Questo articolo illustra il processo di creazione e pubblicazione di dashboard di Azure a livello di codice. Il dashboard mostrato di seguito viene usato come riferimento in tutto il documento.

![Dashboard di esempio](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Panoramica

I dashboard condivisi nel [portale di Azure](https://portal.azure.com) sono [risorse come](../azure-resource-manager/management/overview.md) le macchine virtuali e gli account di archiviazione. È possibile gestire le risorse a livello di codice usando Azure Resource Manager [API REST,](/rest/api/)l'interfaccia della riga di comando di [Azure](/cli/azure)e [Azure PowerShell comandi](/powershell/azure/get-started-azureps).

Molte funzionalità si basano su queste API per semplificare la gestione delle risorse. Ognuno degli strumenti e delle API permette di creare, elencare, recuperare, modificare ed eliminare risorse in modi diversi. Poiché i dashboard sono risorse, è possibile scegliere l'API o lo strumento preferito da usare.

A seconda degli strumenti utilizzati, per creare un dashboard a livello di codice, si costruisce una rappresentazione JSON dell'oggetto dashboard. Questo oggetto contiene informazioni sui riquadri nel dashboard. Include dimensioni, posizioni, risorse a cui sono associati ed eventuali personalizzazioni dell'utente.

Il modo più pratico per creare questo documento JSON è usare il portale di Azure. È possibile aggiungere e posizionare i riquadri in modo interattivo. Esportare quindi il codice JSON e creare un modello dal risultato per usarlo successivamente in script, programmi e strumenti di distribuzione.

## <a name="create-a-dashboard"></a>Creare un dashboard

Per creare un dashboard, selezionare **Dashboard** dal [menu](https://portal.azure.com) portale di Azure, quindi selezionare Nuovo **dashboard.**

![Comando Nuovo dashboard](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Usare la raccolta di riquadri per trovare e aggiungere riquadri. Per aggiungere i riquadri, trascinarli e rilasciarli. Alcuni riquadri supportano il ridimensionamento usando un punto di manipolazione di trascinamento.

![trascinare il punto di manipolazione per modificare le dimensioni](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Altri hanno dimensioni fisse tra cui scegliere nel menu di scelta rapida.

![menu di scelta rapida dimensioni per modificare le dimensioni](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Condividere il dashboard

Dopo aver configurato il dashboard, il passaggio successivo consiste nel pubblicare il dashboard usando il **comando** Condividi.

![condivisione di un dashboard](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Selezionando **Condividi** viene richiesto di scegliere la sottoscrizione e il gruppo di risorse in cui pubblicare. È necessario avere accesso in scrittura alla sottoscrizione e al gruppo di risorse scelto. Per altre informazioni, vedere [Assegnare ruoli di Azure usando portale di Azure](../role-based-access-control/role-assignments-portal.md).

![apportare modifiche alla condivisione e all'accesso](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Recuperare la rappresentazione JSON del dashboard

La pubblicazione richiede solo pochi secondi. Al termine, il passaggio successivo consiste nel recuperare il codice JSON usando il **comando Download.**

![scaricare la rappresentazione JSON](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Creare un modello dalla rappresentazione JSON

Il passaggio successivo consiste nel creare un modello da questo json. Usare tale modello a livello di codice con le API di gestione delle risorse appropriate, gli strumenti da riga di comando o all'interno del portale.

Non è necessario comprendere completamente la struttura JSON del dashboard per creare un modello. Nella maggior parte dei casi, si vuole mantenere la struttura e la configurazione di ogni riquadro. Parametrizzare quindi il set di risorse di Azure a cui puntano i riquadri. Esaminare il dashboard JSON esportato e trovare tutte le occorrenze degli ID risorsa di Azure. Il dashboard di esempio contiene più riquadri che puntano tutti a una singola macchina virtuale di Azure. Questo perché il dashboard esamina solo questa singola risorsa. Se si cerca "/subscriptions" nel codice JSON di esempio, incluso alla fine del documento, si trovano diverse occorrenze di questo ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Per pubblicare questo dashboard per qualsiasi macchina virtuale in futuro, impostare i parametri per ogni occorrenza di questa stringa all'interno del codice JSON.

Esistono due approcci per le API che creano risorse in Azure:

* Le API imperative creano una risorsa alla volta. Per altre informazioni, vedere [Risorse](/rest/api/resources/resources).
* Un sistema di distribuzione basato su modello che crea più risorse dipendenti con una singola chiamata API. Per altre informazioni, vedere  [Distribuire risorse con modelli Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

La distribuzione basata su modello supporta la parametrizzazione e la modellizzazione. Questo approccio viene utilizzato in questo articolo.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Creare un dashboard a livello di codice dal modello usando una distribuzione di modelli

Azure offre la possibilità di orchestrare la distribuzione di più risorse. Si crea un modello di distribuzione che esprime il set di risorse da distribuire e le relazioni tra di esse.  Il formato JSON di ogni risorsa è lo stesso di quando le risorse vengono create una per una. La differenza è che il linguaggio del modello aggiunge alcuni concetti come variabili, parametri, funzioni di base e altro ancora. Questa sintassi estesa è supportata solo nel contesto della distribuzione di un modello. Non funziona se usato con le API imperative descritte in precedenza. Per altre informazioni, vedere [Comprendere la struttura e la sintassi dei Azure Resource Manager modelli.](../azure-resource-manager/templates/template-syntax.md)

La parametrizzazione deve essere eseguita usando la sintassi dei parametri del modello.  Sostituire tutte le istanze dell'ID risorsa trovato in precedenza, come illustrato di seguito.

Proprietà JSON di esempio con ID risorsa hard-coded:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Proprietà JSON di esempio convertita in versione parametrizzata basata sui parametri del modello

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Dichiarare i metadati del modello necessari e i parametri all'inizio del modello JSON come illustrato di seguito:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```
Dopo aver configurato il modello, distribuirlo usando uno dei metodi seguenti:

* [API REST](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Interfaccia della riga di comando di Azure](/cli/azure/group/deployment#az_group_deployment_create)
* [Pagina portale di Azure distribuzione del modello di distribuzione](https://portal.azure.com/#create/Microsoft.Template)

Verranno quindi mostrate due versioni del codice JSON del dashboard di esempio. La prima è la versione esportata dal portale e già associata a una risorsa. Il secondo è la versione del modello che può essere associata a livello di codice a qualsiasi macchina virtuale e distribuita usando Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>Rappresentazione JSON del dashboard di esempio prima della creazione di modelli

Questo esempio mostra cosa ci si può aspettare di vedere se è stato seguito con questo articolo. Le istruzioni hanno esportato la rappresentazione JSON di un dashboard già distribuito. Gli identificatori di risorsa hard-coded mostrano che questo dashboard punta a una macchina virtuale di Azure specifica.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Rappresentazione basata su modello del dashboard di esempio

La versione del modello del dashboard ha definito tre parametri denominati `virtualMachineName` , `virtualMachineResourceGroup` e `dashboardName` .  I parametri permettono di fare in modo che il dashboard punti a una macchina virtuale di Azure diversa a ogni distribuzione. Questo dashboard può essere configurato e distribuito a livello di codice in modo che punti a qualsiasi macchina virtuale di Azure. Per testare questa funzionalità, copiare il modello seguente e incollarlo nella pagina [portale di Azure di distribuzione del modello.](https://portal.azure.com/#create/Microsoft.Template)

Questo esempio distribuisce solo un dashboard, ma il linguaggio del modello permette di distribuire più risorse e di generare un bundle di uno o più dashboard insieme.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

Dopo aver visto un esempio di uso di un modello con parametri per distribuire un dashboard, è possibile provare a distribuire il modello usando le API [REST di Azure Resource Manager](/rest/api/), l'interfaccia della riga di comando di [Azure](/cli/azure)o i comandi [Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Creare un dashboard a livello di codice usando l'interfaccia della riga di comando di Azure

Preparare l'ambiente per l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Questi esempi usano il dashboard seguente: [portal-dashboard-template-testvm.jsin](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Sostituire il contenuto tra parentesi angolari con i valori.

Eseguire il [comando az portal dashboard create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) per creare un dashboard:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

È possibile aggiornare un dashboard con il comando [az portal dashboard update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update):

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Visualizzare i dettagli di un dashboard eseguendo il [comando az portal dashboard show:](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show)

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Per visualizzare tutti i dashboard della sottoscrizione corrente, usare [az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

È anche possibile visualizzare tutti i dashboard per un gruppo di risorse:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui desktop, vedere [Gestire le portale di Azure e le preferenze.](set-preferences.md)

Per altre informazioni sul supporto dell'interfaccia della riga di comando di Azure per i dashboard, vedere [az portal dashboard](/cli/azure/ext/portal/portal/dashboard).
