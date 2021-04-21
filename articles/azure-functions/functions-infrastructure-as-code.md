---
title: Automatizzare la distribuzione delle risorse dell'app per le funzioni in Azure
description: Informazioni su come creare un modello di Azure Resource Manager per distribuire l'app per le funzioni.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 4bbd3491c45b15d43ae0e94b37b916cd8091e655
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834211"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizzare la distribuzione di risorse per l'app per le funzioni in Funzioni di Azure

È possibile usare un modello di Azure Resource Manager per distribuire un'app per le funzioni. Questo articolo descrive le risorse e i parametri necessari per questa operazione. A seconda dei [trigger e dei binding](functions-triggers-bindings.md) potrebbe essere necessario distribuire risorse aggiuntive nell'app per le funzioni.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Per i modelli di esempio, vedere:
- [App per le funzioni in un piano a consumo]
- [App per le funzioni in un piano di servizio app di Azure]

## <a name="required-resources"></a>Risorse necessarie

Una Funzioni di Azure in genere è costituita da queste risorse:

| Risorsa                                                                           | Requisito | Informazioni di riferimento sulla sintassi e le proprietà                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Un'app per le funzioni                                                                     | Obbligatoria    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| Un account [Archiviazione di Azure](../storage/index.yml)                                   | Obbligatoria    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Componente [Application Insights](../azure-monitor/app/app-insights-overview.md) | Facoltativo    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |
| Un [piano di hosting](./functions-scale.md)                                             | Facoltativo<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Un piano di hosting è necessario solo quando si sceglie di eseguire l'app per le funzioni in un [piano Premium](./functions-premium-plan.md) o in un piano di [servizio app.](../app-service/overview-hosting-plans.md)

> [!TIP]
> Anche se non è obbligatorio, è consigliabile configurare Application Insights per l'app.

<a name="storage"></a>
### <a name="storage-account"></a>Account di archiviazione

Per un'app per le funzioni è necessario l'account di archiviazione di Azure. È necessario un account per utilizzo generico che supporti BLOB, tabelle, code e i file. Per altre informazioni, vedere i [requisiti dell'account di archiviazione per Funzioni di Azure](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

È anche necessario specificare la proprietà `AzureWebJobsStorage` come impostazione dell'app nella configurazione del sito. Se l'app per le funzioni non usa Application Insights per il monitoraggio, è necessario specificare anche `AzureWebJobsDashboard` come impostazione dell'app.

Il runtime di Funzioni di Azure usa la stringa di connessione `AzureWebJobsStorage` per creare code interne.  Quando Application Insights non è abilitato, il runtime usa la stringa di connessione `AzureWebJobsDashboard` per accedere all'archivio tabelle di Azure e fornire dati per la scheda **Monitoraggio** nel portale.

Queste proprietà sono specificate nella raccolta `appSettings` dell'oggetto `siteConfig`:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights è consigliabile monitorare le app per le funzioni. La Application Insights viene definita con il tipo **Microsoft.Insights/components** e il tipo **web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Inoltre, la chiave di strumentazione deve essere fornita all'app per le funzioni usando `APPINSIGHTS_INSTRUMENTATIONKEY` l'impostazione dell'applicazione. Questa proprietà è specificata nella `appSettings` raccolta `siteConfig` nell'oggetto :

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Piano di hosting

La definizione del piano di hosting varia e può essere una delle seguenti:
* [Piano a consumo](#consumption) (impostazione predefinita)
* [Piano Premium](#premium)
* [Piano di servizio app](#app-service-plan)

### <a name="function-app"></a>App per le funzioni

La risorsa dell'app per le funzioni viene definita usando una risorsa di tipo **Microsoft.Web/sites** e il tipo **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Se si definisce in modo esplicito un piano di hosting, è necessario un elemento aggiuntivo nella matrice dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Un'app per le funzioni deve includere le impostazioni dell'applicazione seguenti:

| Nome impostazione                 | Descrizione                                                                               | Valori di esempio                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Stringa di connessione a un account di archiviazione utilizzata dal runtime di Funzioni per l'accodamento interno | Vedere [Account di archiviazione](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Versione del runtime Funzioni di Azure                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Stack del linguaggio da usare per le funzioni in questa app                                   | `dotnet`, `node`, `java`, `python` o `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Necessario solo se si usa lo `node` stack del linguaggio, specifica la versione da usare              | `10.14.1`                             |

Queste proprietà vengono specificate nella `appSettings` raccolta nella `siteConfig` proprietà :

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Distribuire nel piano a consumo

Il piano a consumo alloca automaticamente la potenza di calcolo quando il codice è in esecuzione, si ridimensiona in base alle esigenze per gestire il carico e quindi si ridimensiona quando il codice non è in esecuzione. Non è necessario pagare per le macchine virtuali inattive e non è necessario riservare capacità in anticipo. Per altre informazioni, vedere [Ridimensionamento e hosting di Funzioni di Azure](consumption-plan.md).

Per un modello di Azure Resource Manager di esempio, vedere [App per le funzioni nel piano a consumo].

### <a name="create-a-consumption-plan"></a>Creare un piano a consumo

Non è necessario definire un piano a consumo. Uno verrà creato o selezionato automaticamente per ogni area quando si crea la risorsa dell'app per le funzioni stessa.

Il piano a consumo è un tipo speciale di risorsa "serverfarm". Per Windows, è possibile specificarlo usando il `Dynamic` valore per le proprietà e `computeMode` `sku` :

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Il piano a consumo non può essere definito in modo esplicito per Linux. Verrà creato automaticamente.

Se si definisce in modo esplicito il piano a consumo, sarà necessario impostare la proprietà nell'app in modo che punti `serverFarmId` all'ID risorsa del piano. È necessario assicurarsi che anche l'app per le funzioni abbia `dependsOn` un'impostazione per il piano.

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

Le impostazioni richieste da un'app per le funzioni in esecuzione nel piano a consumo rinvia tra Windows e Linux. 

#### <a name="windows"></a>Windows

In Windows un piano a consumo richiede un'impostazione aggiuntiva nella configurazione del sito: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Questa proprietà configura l'account di archiviazione in cui sono archiviati il codice e la configurazione dell'app per le funzioni.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> Non impostare [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) l'impostazione così come viene generata automaticamente quando il sito viene creato per la prima volta.  

#### <a name="linux"></a>Linux

In Linux, l'app per le funzioni deve avere la proprietà impostata su e la `kind` `functionapp,linux` proprietà deve essere `reserved` impostata su `true` . 

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

Le [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) impostazioni e non sono supportate in [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) Linux.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Distribuire nel piano Premium

Il piano Premium offre la stessa scalabilità del piano a consumo, ma include risorse dedicate e funzionalità aggiuntive. Per altre informazioni, vedere [Funzioni di Azure Piano Premium.](./functions-premium-plan.md)

### <a name="create-a-premium-plan"></a>Creare un piano Premium

Un piano Premium è un tipo speciale di risorsa "serverfarm". È possibile specificarlo usando `EP1` , o per il valore della proprietà `EP2` `EP3` `Name` nell'oggetto `sku` [descrizione](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

La proprietà di un'app per le funzioni in un piano Premium `serverFarmId` deve essere impostata sull'ID risorsa del piano creato in precedenza. Inoltre, un piano Premium richiede un'impostazione aggiuntiva nella configurazione del sito: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Questa proprietà configura l'account di archiviazione in cui sono archiviati il codice e la configurazione dell'app per le funzioni.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> Non impostare [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) l'impostazione così come viene generata automaticamente quando il sito viene creato per la prima volta.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Distribuire nel piano di servizio app

Nel piano di servizio app, le app per le funzioni vengono eseguite in macchine virtuali dedicate in SKU Basic, Standard e Premium, analogamente alle app Web. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/overview-hosting-plans.md).

Per un modello di Azure Resource Manager di esempio, vedere [App per le funzioni nel piano di servizio app di Azure].

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Un piano di servizio app è definito da una risorsa "serverfarm".

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Per eseguire l'app in Linux, è necessario impostare anche `kind` su `Linux` :

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

La proprietà di un'app per le funzioni in un piano di servizio `serverFarmId` app deve essere impostata sull'ID risorsa del piano creato in precedenza.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

Le app Linux devono includere anche una `linuxFxVersion` proprietà in `siteConfig` . Se si distribuisce semplicemente il codice, il valore di è determinato dallo stack di runtime desiderato nel formato ```runtime|runtimeVersion``` :

| Stack            | Valore di esempio                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

Se si [distribuisce un'immagine](./functions-create-function-linux-custom-image.md)del contenitore personalizzata, è necessario specificarla con e includere la configurazione che consente di eseguire il pulled dell'immagine, come `linuxFxVersion` [app Web per contenitori](../app-service/index.yml). Impostare anche `WEBSITES_ENABLE_APP_SERVICE_STORAGE` su , poiché il contenuto `false` dell'app viene fornito nel contenitore stesso:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Personalizzazione di una distribuzione

Un'app per le funzioni contiene numerose risorse figlio che possono essere usate nella distribuzione, incluse le impostazioni dell'app e le opzioni di controllo del codice sorgente. È anche possibile scegliere di rimuovere la **risorsa figlio sourcecontrols** e usare invece un'opzione [di distribuzione](functions-continuous-deployment.md) diversa.

> [!IMPORTANT]
> Per poter distribuire l'applicazione usando Azure Resource Manager, è importante comprendere come vengono distribuite le risorse in Azure. Nell'esempio seguente, le configurazioni di livello superiore vengono applicate usando **siteConfig**. È importante impostare le configurazioni a un livello superiore perché forniscono informazioni al motore di runtime e di distribuzione di Funzioni di Azure. Le informazioni di livello superiore sono necessarie prima di applicare la risorsa figlia **sourcecontrols/web**. Anche se è possibile configurare queste impostazioni nella risorsa **config/appSettings** del livello figlio, in alcuni casi l'app per le funzioni deve essere distribuita *prima di applicare* **config/appSettings**. Quando ad esempio si usano le funzioni con [app per la logica](../logic-apps/index.yml), le funzioni sono una dipendenza di un'altra risorsa.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Questo modello usa il [valore Impostazioni](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) app di progetto, che imposta la directory di base in cui il motore di distribuzione di Funzioni (Kudu) cerca il codice distribuibile. Nel repository le funzioni sono in una sottocartella della cartella **src**. Pertanto, nell'esempio precedente abbiamo impostato il valore di impostazione dell'app su `src`. Se le funzioni sono nella radice del repository o se non si sta distribuendo dal controllo del codice sorgente, è possibile rimuovere questo valore di impostazione dell'app.

## <a name="deploy-your-template"></a>Distribuire il modello

Il modello può essere distribuito in uno dei modi seguenti:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md)
* [Portale di Azure](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Pulsante Deploy to Azure per la distribuzione in Azure

Sostituire ```<url-encoded-path-to-azuredeploy-json>``` con una versione [con codifica URL](https://www.bing.com/search?q=url+encode) del percorso non elaborato del file `azuredeploy.json` in GitHub.

Di seguito è riportato un esempio che usa la sintassi markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Di seguito è riportato un esempio che usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Distribuire tramite PowerShell

I comandi di PowerShell seguenti creano un gruppo di risorse e distribuiscono un modello che crea un'app per le funzioni con le risorse necessarie. Per l'esecuzione in locale, è necessario [Azure PowerShell](/powershell/azure/install-az-ps) installato. Eseguire [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) per accedere.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Per testare questa distribuzione, [](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) è possibile usare un modello come questo che crea un'app per le funzioni in Windows in un piano a consumo. Sostituire `<function-app-name>` con un nome univoco per l'app per le funzioni.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come sviluppare e configurare le Funzioni di Azure.

* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Come configurare le impostazioni dell'app per le funzioni di Azure](functions-how-to-use-azure-function-app-settings.md)
* [Create your first Azure function](./functions-get-started.md) (Creare la prima funzione di Azure)

<!-- LINKS -->

[App per le funzioni in un piano a consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[App per le funzioni in un piano di servizio app di Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
