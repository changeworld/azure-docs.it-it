---
title: Test case per il toolkit di test
description: Descrive i test eseguiti dal toolkit di test dei modelli di Arm.
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7805d6dbdb8b93968a2792ed6dfaf2ac8fea9ae5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363394"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Test case predefiniti per il toolkit di test del modello di Arm

Questo articolo descrive i test predefiniti che vengono eseguiti con il toolkit di [test](test-toolkit.md) del modello per i Azure Resource Manager (modelli arm). Fornisce esempi che superano o non superano il test. Include il nome di ogni test. Per eseguire un test specifico, vedere [Parametri di test.](test-toolkit.md#test-parameters)

## <a name="use-correct-schema"></a>Usare lo schema corretto

Nome test: **Schema DeploymentTemplate corretto**

Nel modello è necessario specificare un valore di schema valido.

Nell'esempio seguente **viene superato** questo test.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

La proprietà dello schema nel modello deve essere impostata su uno degli schemi seguenti:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>I parametri devono esistere

Nome test: **La proprietà Parameters deve esistere**

Il modello deve avere un elemento parameters. I parametri sono essenziali per rendere i modelli riutilizzabili in ambienti diversi. Aggiungere parametri al modello per i valori che cambiano durante la distribuzione in ambienti diversi.

L'esempio seguente **supera** questo test:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>I parametri dichiarati devono essere usati

Nome test: è **necessario fare riferimento ai parametri**

Per ridurre la confusione nel modello, eliminare tutti i parametri definiti ma non usati. Questo test trova tutti i parametri che non vengono usati in nessun punto del modello. L'eliminazione dei parametri inutilizzati semplifica anche la distribuzione del modello perché non è necessario specificare valori non necessari.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>I parametri protetti non possono avere impostazioni predefinite hardcoded

Nome test: **I parametri di stringa sicura non possono avere valori predefiniti**

Non specificare un valore predefinito hard-coded per un parametro sicuro nel modello. Una stringa vuota è valida per il valore predefinito.

I tipi **SecureString o SecureObject** vengono utilizzati **nei** parametri che contengono valori sensibili, ad esempio le password. Quando un parametro usa un tipo sicuro, il valore del parametro non viene registrato o archiviato nella cronologia di distribuzione. Questa azione impedisce a un utente malintenzionato di individuare il valore sensibile.

Tuttavia, quando si specifica un valore predefinito, tale valore è individuabile da chiunque possa accedere al modello o alla cronologia di distribuzione.

L'esempio seguente **non supera** questo test:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

L'esempio successivo **supera** questo test:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Gli URL dell'ambiente non possono essere hardcoded

Nome test: **DeploymentTemplate non deve contenere URI hardcoded**

Non codificare gli URL dell'ambiente nel modello. Usare invece la funzione [di ambiente per](template-functions-deployment.md#environment) ottenere in modo dinamico questi URL durante la distribuzione. Per un elenco degli host URL bloccati, vedere l'test case [.](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)

L'esempio seguente **non supera** questo test perché l'URL è hardcoded.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

Il test ha **esito negativo anche** quando viene usato con [concat](template-functions-string.md#concat) o [uri](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Nell'esempio seguente **viene superato** questo test.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>La posizione usa il parametro

Nome test: **La posizione non deve essere hardcoded**

I modelli devono avere un parametro denominato location. Usare questo parametro per impostare la posizione delle risorse nel modello. Nel modello principale (denominatoazuredeploy.js _in o_ _mainTemplate.js_ in ), questo parametro può essere impostato sul percorso del gruppo di risorse per impostazione predefinita. Nei modelli collegati o annidati, il parametro location non deve avere un percorso predefinito.

Gli utenti del modello potrebbero avere aree limitate disponibili. Quando si codifica a hard code la posizione della risorsa, è possibile che agli utenti venga impedito di creare una risorsa in tale area. Gli utenti potrebbero essere bloccati anche se si imposta il percorso della risorsa su `"[resourceGroup().location]"` . È possibile che il gruppo di risorse sia stato creato in un'area a cui altri utenti non possono accedere. A tali utenti viene impedito di usare il modello.

Fornendo un parametro di posizione che per impostazione predefinita corrisponde alla posizione del gruppo di risorse, gli utenti possono usare il valore predefinito quando opportuno, ma specificare anche una località diversa.

L'esempio **seguente non** supera questo test perché la posizione nella risorsa è impostata su `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

L'esempio seguente usa un parametro location ma **non supera** questo test perché il parametro location usa una posizione hardcoded per impostazione predefinita.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Creare invece un parametro che per impostazione predefinita corrisponde alla posizione del gruppo di risorse, ma consente agli utenti di specificare un valore diverso. L'esempio **seguente** supera questo test quando il modello viene usato come modello principale.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Tuttavia, se l'esempio precedente viene usato come modello collegato, il test ha **esito negativo.** Se usato come modello collegato, rimuovere il valore predefinito.

## <a name="resources-should-have-location"></a>Le risorse devono avere una posizione

Nome test: **Le risorse devono avere una posizione**

La posizione di una risorsa deve essere impostata su [un'espressione modello](template-expressions.md) o `global` . L'espressione modello in genere usa il parametro location descritto nel test precedente.

Nell'esempio seguente **il** test ha esito negativo perché il percorso non è un'espressione o `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Nell'esempio seguente **viene superato** questo test.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

Anche l'esempio successivo **supera** questo test.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>Le dimensioni della macchina virtuale utilizzano il parametro

Nome test: **le dimensioni della macchina virtuale devono essere un parametro**

Non hardcoded le dimensioni della macchina virtuale. Specificare un parametro in modo che gli utenti del modello possano modificare le dimensioni della macchina virtuale distribuita.

Nell'esempio seguente il test **ha esito** negativo.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Fornire invece un parametro .

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Impostare quindi le dimensioni della macchina virtuale su tale parametro.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>I valori minimi e massimi sono numeri

Nome test: **i valori minimo e massimo sono numeri**

Se si definiscono i valori min e max per un parametro, specificarli come numeri.

L'esempio seguente **non supera** questo test:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Specificare invece i valori come numeri. L'esempio seguente **supera** questo test:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Questo avviso viene visualizzato anche se si specifica un valore minimo o massimo, ma non l'altro.

## <a name="artifacts-parameter-defined-correctly"></a>Parametro Artifacts definito correttamente

Nome test: **parametro artifacts**

Quando si includono parametri per `_artifactsLocation` e , usare i valori predefiniti e i tipi `_artifactsLocationSasToken` corretti. Per superare questo test, è necessario che siano soddisfatte le condizioni seguenti:

* Se si specifica un parametro, è necessario specificare l'altro parametro
* `_artifactsLocation` deve essere una **stringa**
* `_artifactsLocation` deve avere un valore predefinito nel modello principale
* `_artifactsLocation` non può avere un valore predefinito in un modello annidato
* `_artifactsLocation` deve avere o `"[deployment().properties.templateLink.uri]"` l'URL del repo non elaborato per il valore predefinito
* `_artifactsLocationSasToken` deve essere **secureString**
* `_artifactsLocationSasToken` può avere solo una stringa vuota per il valore predefinito
* `_artifactsLocationSasToken` non può avere un valore predefinito in un modello annidato

## <a name="declared-variables-must-be-used"></a>Le variabili dichiarate devono essere usate

Nome test: è **necessario fare riferimento alle variabili**

Per ridurre la confusione nel modello, eliminare tutte le variabili definite ma non usate. Questo test trova tutte le variabili che non vengono usate in alcun punto del modello.

## <a name="dynamic-variable-should-not-use-concat"></a>La variabile dinamica non deve usare concat

Nome test: **i riferimenti a variabili dinamiche non devono usare Concat**

In alcuni casi è necessario costruire dinamicamente una variabile in base al valore di un'altra variabile o parametro. Non usare la funzione [concat](template-functions-string.md#concat) quando si imposta il valore. Usare invece un oggetto che include le opzioni disponibili e ottenere dinamicamente una delle proprietà dall'oggetto durante la distribuzione.

Nell'esempio seguente **viene superato** questo test. La **variabile currentImage** viene impostata dinamicamente durante la distribuzione.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Usare la versione recente dell'API

Nome test: **apiVersions Should Be Recent**

La versione dell'API per ogni risorsa deve usare una versione recente. Il test valuta la versione in uso rispetto alle versioni disponibili per il tipo di risorsa.

## <a name="use-hardcoded-api-version"></a>Usare la versione dell'API hardcoded

Nome test: **Provider apiVersions non consentito**

La versione dell'API per un tipo di risorsa determina le proprietà disponibili. Specificare una versione dell'API hard-coded nel modello. Non recuperare una versione dell'API determinata durante la distribuzione. Non si saprà quali proprietà sono disponibili.

L'esempio seguente **ha esito negativo** per questo test.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

Nell'esempio seguente **viene superato** questo test.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Le proprietà non possono essere vuote

Nome test: **il modello non deve contenere spazi vuoti**

Non impostare come hardcoded le proprietà in un valore vuoto. I valori vuoti includono stringhe, oggetti o matrici null e vuoti. Se una proprietà è stata impostata su un valore vuoto, rimuoverla dal modello. Tuttavia, è possibile impostare una proprietà su un valore vuoto durante la distribuzione, ad esempio tramite un parametro .

## <a name="use-resource-id-functions"></a>Usare le funzioni id risorsa

Nome test: **gli ID devono essere derivati dagli ID risorsa**

Quando si specifica un ID risorsa, usare una delle funzioni id risorsa. Le funzioni consentite sono:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Non usare la funzione concat per creare un ID risorsa. L'esempio seguente **non supera** questo test.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

L'esempio seguente **supera** questo test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>La funzione ResourceId ha parametri corretti

Nome test: **ResourceIds non deve contenere**

Quando si generano ID risorsa, non usare funzioni non necessarie per i parametri facoltativi. Per impostazione predefinita, la [funzione resourceId](template-functions-resource.md#resourceid) usa la sottoscrizione e il gruppo di risorse correnti. Non è necessario specificare tali valori.

L'esempio seguente **non** supera questo test perché non è necessario specificare l'ID sottoscrizione corrente e il nome del gruppo di risorse.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

L'esempio seguente **supera** questo test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Questo test si applica a:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [reference](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

Per `reference` e , il test ha esito `list*` **negativo** quando si usa per `concat` costruire l'ID risorsa.

## <a name="dependson-best-practices"></a>Procedure consigliate per dependsOn

Nome test: **Procedure consigliate dependsOn**

Quando si impostano le dipendenze di distribuzione, non usare la [funzione if](template-functions-logical.md#if) per testare una condizione. Se una risorsa dipende da una risorsa distribuita in [modo condizionale,](conditional-resource-deployment.md)impostare la dipendenza come si farebbe con qualsiasi risorsa. Quando una risorsa condizionale non viene distribuita, Azure Resource Manager automaticamente dalle dipendenze necessarie.

Nell'esempio seguente il test **ha esito** negativo.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

L'esempio successivo **supera** questo test.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Le distribuzioni nidificate o collegate non possono usare il debug

Nome test: **Le risorse di distribuzione non devono essere di debug**

Quando si definisce un [modello annidato](linked-templates.md) o collegato con il tipo di risorsa **Microsoft.Resources/deployments,** è possibile abilitare il debug per tale modello. Il debug va bene quando è necessario testare il modello, ma deve essere attivato quando si è pronti per usare il modello nell'ambiente di produzione.

## <a name="admin-user-names-cant-be-literal-value"></a>I nomi utente amministratore non possono essere valori letterali

Nome test: **adminUsername non deve essere un valore letterale**

Quando si imposta un nome utente amministratore, non usare un valore letterale.

L'esempio seguente **non supera** questo test:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Usare invece un parametro . L'esempio seguente **supera** questo test:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Usare l'immagine della macchina virtuale più recente

Nome test: Le **immagini della macchina virtuale devono usare la versione più recente**

Se il modello include una macchina virtuale con un'immagine, assicurarsi che sia in uso la versione più recente dell'immagine.

## <a name="use-stable-vm-images"></a>Usare immagini di macchine virtuali stabili

Nome test: **Le macchine virtuali non devono essere in anteprima**

Le macchine virtuali non devono usare immagini di anteprima.

L'esempio seguente **non supera** questo test.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

Nell'esempio seguente **viene superato** questo test.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Non usare l'estensione ManagedIdentity

Nome test: **ManagedIdentityExtension non deve essere usato**

Non applicare l'estensione ManagedIdentity a una macchina virtuale. L'estensione è stata deprecata nel 2019 e non deve più essere usata.

## <a name="outputs-cant-include-secrets"></a>Gli output non possono includere segreti

Nome test: **Gli output non devono contenere segreti**

Non includere valori nella sezione outputs che espongono potenzialmente segreti. L'output di un modello viene archiviato nella cronologia di distribuzione, in modo che un utente malintenzionato possa trovare queste informazioni.

L'esempio seguente **non** supera il test perché include un parametro sicuro in un valore di output.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

L'esempio **seguente ha** esito negativo perché usa una funzione [list*](template-functions-resource.md#list) negli output.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>Usare protectedSettings per i segreti commandToExecute

Nome test: **CommandToExecute deve usare ProtectedSettings per i segreti**

In un'estensione script personalizzata usare la proprietà crittografata quando `protectedSettings` include `commandToExecute` dati segreti, ad esempio una password. Esempi di tipi di dati segreti `secureString` sono , , funzioni o `secureObject` `list()` script.

Per altre informazioni sull'estensione script personalizzata per le macchine virtuali, vedere [Windows](
/azure/virtual-machines/extensions/custom-script-windows), [Linux](/azure/virtual-machines/extensions/custom-script-linux)e lo schema [Microsoft.Compute virtualMachines/extensions.](/azure/templates/microsoft.compute/virtualmachines/extensions)

In questo esempio, un modello con un parametro denominato e il tipo `adminPassword` `secureString` **supera il** test perché la proprietà crittografata `protectedSettings` include `commandToExecute` .

```json
"properties": [
  {
    "protectedSettings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

Il test **ha esito** negativo se la proprietà non crittografata `settings` include `commandToExecute` .

```json
"properties": [
  {
    "settings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'esecuzione del toolkit di test, vedere [Usare il toolkit di test dei modelli di Arm.](test-toolkit.md)
* Per un Microsoft Learn modulo che illustra l'uso del toolkit di test, vedere Anteprima delle modifiche e convalidare le risorse di Azure usando [what-if](/learn/modules/arm-template-test/)e il toolkit di test del modello di Arm.
