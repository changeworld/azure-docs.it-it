---
title: Creare il file di parametri
description: Creare il file di parametri per passare i valori durante la distribuzione di un modello di Azure Resource Manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: ddeaed94396aa662b795ae5701aa367ba13d869b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531207"
---
# <a name="create-resource-manager-parameter-file"></a>Creare il file di parametri di Resource Manager

Anziché passare parametri come valori inline nello script, è possibile usare un file JSON contenente i valori dei parametri. Questo articolo illustra come creare un file di parametri da usare con un modello JSON o un file Bicep.

## <a name="parameter-file"></a>File di parametri

Un file di parametri usa il formato seguente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Si noti che il file di parametri archivia i valori dei parametri come testo normale. Questo approccio funziona per i valori che non sono sensibili, ad esempio uno SKU della risorsa. Il testo normale non funziona per i valori sensibili, ad esempio le password. Se è necessario passare un parametro contenente un valore sensibile, archiviare il valore in un insieme di credenziali delle chiavi. Fare quindi riferimento all'insieme di credenziali delle chiavi nel file dei parametri. Il valore sensibile verrà recuperato in modo sicuro durante la distribuzione.

Il file di parametri seguente include un valore di testo normale e un valore sensibile archiviato in un insieme di credenziali delle chiavi.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Per altre informazioni sull'uso dei valori contenuti in un insieme di credenziali delle chiavi, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definire i valori dei parametri

Per determinare come definire i nomi e i valori dei parametri, aprire il modello JSON o Bicep. Esaminare la sezione del modello relativa ai parametri. Gli esempi seguenti illustrano i parametri dei modelli JSON e Bicep.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

---

Nel file dei parametri il primo dettaglio da notare è il nome di ogni parametro. I nomi dei parametri nel file di parametri devono corrispondere ai nomi dei parametri nel modello.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Si noti il tipo di parametro. I tipi di parametro nel file di parametri devono usare gli stessi tipi del modello. In questo esempio entrambi i tipi di parametro sono stringhe.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Controllare il modello per i parametri con un valore predefinito. Se un parametro ha un valore predefinito, è possibile specificare un valore nel file dei parametri, ma non è obbligatorio. Il valore del file di parametri esegue l'override del valore predefinito del modello.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Controllare i valori consentiti del modello ed eventuali restrizioni, ad esempio la lunghezza massima. Questi valori specificano l'intervallo di valori che è possibile specificare per un parametro. In questo esempio può `storagePrefix` avere un massimo di 11 caratteri e deve specificare un valore `storageAccountType` consentito.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

> [!NOTE]
> Il file di parametri può contenere solo valori per i parametri definiti nel modello. Se il file di parametri contiene parametri aggiuntivi che non corrispondono ai parametri del modello, viene visualizzato un errore.

## <a name="parameter-type-formats"></a>Formati dei tipi di parametro

L'esempio seguente illustra i formati dei diversi tipi di parametro: string, integer, boolean, array e object.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
  }
}
```

## <a name="deploy-template-with-parameter-file"></a>Distribuire il modello con il file di parametri

Dall'interfaccia della riga di comando di Azure si passa un file di parametri locale usando `@` e il nome del file dei parametri. Ad esempio: `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Per altre informazioni, vedere Distribuire risorse con i modelli arm e l'interfaccia [della riga di comando di Azure.](./deploy-cli.md#parameters) Per distribuire i _file con estensione bicep_ è necessaria la versione 2.20 o successiva dell'interfaccia della riga di comando di Azure.

Da Azure PowerShell si passa un file di parametri locale usando il `TemplateParameterFile` parametro .

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Per altre informazioni, vedere [Distribuire risorse con modelli di Arm e Azure PowerShell](./deploy-powershell.md#pass-parameter-values). Per distribuire _i file con estensione bicep_ è Azure PowerShell versione 5.6.0 o successiva.

> [!NOTE]
> Non è possibile usare un file di parametri con il pannello del modello personalizzato nel portale.

> [!TIP]
> Se si usa il progetto Gruppo di risorse di [Azure in](create-visual-studio-deployment-project.md)Visual Studio , assicurarsi che l'azione di compilazione del file di parametri sia **impostata** su **Contenuto**.

## <a name="file-name"></a>Nome file

La convenzione di denominazione generale per il file di parametri è includere _i parametri_ nel nome del modello. Ad esempio, se il modello è denominato _azuredeploy.json_, il file di parametri sarà denominato _azuredeploy.parameters.json_. Questa convenzione di denominazione consente di vedere la connessione tra il modello e i parametri.

Per eseguire la distribuzione in ambienti diversi, creare più di un file di parametri. Quando si denotono i file dei parametri, identificarne l'uso, ad esempio sviluppo e produzione. Ad esempio, usareazuredeploy.parameters-dev.js _su_ e _azuredeploy.parameters-prod.jsper_ distribuire le risorse.

## <a name="parameter-precedence"></a>Precedenza dei parametri

È possibile usare i parametri inline e un file di parametri locale nella stessa operazione di distribuzione. Ad esempio, è possibile specificare alcuni valori nel file di parametri locale e aggiungere altri valori inline durante la distribuzione. Se si specificano valori per un parametro sia nel file dei parametri locale che inline, il valore inline ha la precedenza.

È possibile usare un file di parametri esterno specificando l'URI per il file. Quando si usa un file di parametri esterno, non è possibile passare altri valori inline o da un file locale. Tutti i parametri inline vengono ignorati. È necessario fornire tutti i valori dei parametri presenti nel file esterno.

## <a name="parameter-name-conflicts"></a>Conflitti nei nomi di parametro

Se il modello include un parametro con lo stesso nome di uno dei parametri nel comando di PowerShell, PowerShell presenta il parametro del modello con il suffisso `FromTemplate` . Ad esempio, un parametro `ResourceGroupName` denominato nel modello è in conflitto con il parametro nel cmdlet `ResourceGroupName` [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Viene richiesto di specificare un valore per `ResourceGroupNameFromTemplate` . Per evitare questa confusione, usare nomi di parametro che non vengono usati per i comandi di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come definire i parametri in un modello, vedere [Parametri nei modelli arm.](template-parameters.md)
- Per altre informazioni sull'uso dei valori contenuti in un insieme di credenziali delle chiavi, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](key-vault-parameter.md).
