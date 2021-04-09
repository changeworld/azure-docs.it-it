---
title: Moduli bicipite
description: Viene descritto come definire e utilizzare un modulo e come utilizzare gli ambiti del modulo.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 7a680e8aa0fa4d5ef9cac7f9e7ba07a3aa4ee1e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611736"
---
# <a name="use-bicep-modules-preview"></a>Usare i moduli bicipite (anteprima)

Il bicipite consente di suddividere una soluzione complessa in moduli. Un modulo bicipite è un set di una o più risorse da distribuire insieme. I moduli astraggono i dettagli complessi della dichiarazione di risorse non elaborate, che può migliorare la leggibilità. È possibile riutilizzare questi moduli e condividerli con altri utenti. In combinazione con le [specifiche del modello](./template-specs.md), viene creata una soluzione per la modularità e il riutilizzo del codice. Per un'esercitazione, vedere [esercitazione: aggiungere moduli bicipite](./bicep-tutorial-add-modules.md).

## <a name="define-modules"></a>Definire i moduli

Ogni file bicipite può essere utilizzato come modulo. Un modulo espone solo parametri e output come contratti ad altri file bicipite. I parametri e gli output sono facoltativi.

Il file bicipite seguente può essere distribuito direttamente per creare un account di archiviazione o essere usato come modulo.  Nella sezione successiva viene illustrato come utilizzare i moduli:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

L'output viene usato per passare i valori ai file del bicipite padre.

## <a name="consume-modules"></a>Utilizzare moduli

Usare la parola chiave _Module_ per utilizzare un modulo. Il file bicipite seguente distribuisce la risorsa definita nel file di modulo a cui si fa riferimento:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **Module**: parola chiave.
- **nome simbolico** (stgModule): identificatore per il modulo.
- **file di modulo**: il percorso del modulo in questo esempio viene specificato usando un percorso relativo (/StorageAccount.Bicep). Tutti i percorsi nel bicipite devono essere specificati usando il separatore di directory barra (/) per garantire una compilazione coerente multipiattaforma. Il carattere barra rovesciata () di Windows non \\ è supportato.
- La proprietà **_Name_** (storageDeploy) è obbligatoria quando si utilizza un modulo. Quando il bicipite genera il modello il, questo campo viene usato come nome della risorsa di distribuzione nidificata, generata per il modulo:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Per ottenere un valore di output da un modulo, recuperare il valore della proprietà con sintassi come: `stgModule.outputs.storageEndpoint` dove `stgModule` è l'identificatore del modulo.

## <a name="configure-module-scopes"></a>Configurare gli ambiti del modulo

Quando si dichiara un modulo, è possibile specificare una proprietà _scope_ per impostare l'ambito in cui distribuire il modulo:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

La proprietà _scope_ può essere omessa quando l'ambito di destinazione del modulo e l'ambito di destinazione del padre sono gli stessi. Quando non viene specificata la proprietà Scope, il modulo viene distribuito nell'ambito di destinazione dell'elemento padre.

Il file bicipite seguente mostra come creare un gruppo di risorse e distribuire un modulo nel gruppo di risorse:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>Passaggi successivi

- Per esaminare un'esercitazione, vedere [esercitazione: aggiungere moduli bicipite](./bicep-tutorial-add-modules.md).
