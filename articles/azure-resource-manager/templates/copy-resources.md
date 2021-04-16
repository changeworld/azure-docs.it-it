---
title: Distribuire più istanze di risorse
description: Usare l'operazione di copia e le matrici in un modello Azure Resource Manager (modello arm) per distribuire più volte il tipo di risorsa.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 5ddb0cabf0acae1ffe9b9e77e6defa70f9cbd61b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479968"
---
# <a name="resource-iteration-in-arm-templates"></a>Iterazione delle risorse nei modelli arm

Questo articolo illustra come creare più di un'istanza di una risorsa nel modello di Azure Resource Manager (modello arm). Aggiungendo il ciclo di copia alla sezione resources del modello, è possibile impostare dinamicamente il numero di risorse da distribuire. Si evita anche di dover ripetere la sintassi del modello.

È anche possibile usare il ciclo di copia [con le](copy-properties.md)proprietà , [le variabili](copy-variables.md)e gli [output .](copy-outputs.md)

Se è necessario specificare se una risorsa viene distribuita, vedere l'[elemento condizionale](conditional-resource-deployment.md).

## <a name="syntax"></a>Sintassi

# <a name="json"></a>[JSON](#tab/json)

Aggiungere `copy` l'elemento alla sezione resources del modello per distribuire più istanze della risorsa. `copy`L'elemento ha il formato generale seguente:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

La `name` proprietà è qualsiasi valore che identifica il ciclo. La `count` proprietà specifica il numero di iterazioni desiderate per il tipo di risorsa.

Usare le `mode` proprietà e per specificare se le risorse vengono distribuite in parallelo o in `batchSize` sequenza. Queste proprietà sono descritte in [Serial o Parallel.](#serial-or-parallel)

# <a name="bicep"></a>[Bicep](#tab/bicep)

I cicli possono essere usati per dichiarare più risorse:

- Iterazione su una matrice:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- Iterazione degli elementi di una matrice

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Uso dell'indice del ciclo

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

---

## <a name="copy-limits"></a>Limiti di copia

Il conteggio non può superare 800.

Il conteggio non può essere un numero negativo. Può essere zero se si distribuisce il modello con una versione recente dell'interfaccia della riga di comando di Azure, PowerShell o API REST. In particolare, è necessario usare:

- Azure PowerShell **2.6** o versione successiva
- Interfaccia della riga di comando di Azure **2.0.74** o versione successiva
- API REST **versione 2019-05-10** o successiva
- [Le distribuzioni collegate](linked-templates.md) devono usare l'API **versione 2019-05-10 o** successiva per il tipo di risorsa di distribuzione

Le versioni precedenti di PowerShell, dell'interfaccia della riga di comando e dell'API REST non supportano zero per il conteggio.

Prestare attenzione [all'uso della distribuzione in modalità completa](deployment-modes.md) con il ciclo di copia. Se si esegue la ridistribuzione in un gruppo di risorse con modalità completa, tutte le risorse non specificate nel modello dopo la risoluzione del ciclo di copia vengono eliminate.

## <a name="resource-iteration"></a>Iterazione delle risorse

Nell'esempio seguente viene creato il numero di account di archiviazione specificati nel `storageCount` parametro .

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ]
}
```

Si noti che il nome di ogni risorsa include la funzione `copyIndex()` che restituisce l'iterazione corrente nel ciclo. `copyIndex()` è in base zero. Quindi l'esempio seguente:

```json
"name": "[concat('storage', copyIndex())]",
```

Crea questi nomi:

- storage0
- storage1
- storage2

Per eseguire l'offset del valore di indice, è possibile passare un valore nella funzione `copyIndex()`. Il numero di iterazioni è ancora specificato nell'elemento di copia, ma il valore di `copyIndex` è offset dal valore specificato. Quindi l'esempio seguente:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Crea questi nomi:

- storage1
- storage2
- storage3

L'operazione di copia è utile quando si lavora con le matrici in quanto è possibile iterare ogni elemento della matrice. Usare la funzione `length` nella matrice per specificare il conteggio per le iterazioni e `copyIndex` per recuperare l'indice corrente nella matrice.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

Si noti che `i` l'indice viene usato nella creazione del nome della risorsa dell'account di archiviazione.

---

Nell'esempio seguente viene creato un account di archiviazione per ogni nome specificato nel parametro .

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

Se si desidera restituire valori dalle risorse distribuite, è possibile usare [la copia nella sezione outputs](copy-outputs.md).

## <a name="serial-or-parallel"></a>Seriale o parallelo

Per impostazione predefinita, Gestione risorse crea le risorse in parallelo. Non applica alcun limite al numero di risorse distribuite in parallelo, oltre al limite totale di 800 risorse nel modello. L'ordine di creazione non è garantito.

Tuttavia è consigliabile specificare che le risorse vengano distribuite in sequenza. Ad esempio, quando si aggiorna un ambiente di produzione, è consigliabile sfalsare gli aggiornamenti per aggiornarne solo un determinato numero in un dato momento.

Ad esempio, per distribuire in modo seriale gli account di archiviazione due alla volta, usare:

# <a name="json"></a>[JSON](#tab/json)

Per distribuire in modo seriale più istanze di una risorsa, impostare `mode` su **serial** e `batchSize` sul numero di istanze da distribuire contemporaneamente. Con la modalità seriale, Resource Manager crea una dipendenza da istanze precedenti nel ciclo in modo un batch venga avviato solo dopo il completamento del batch precedente.

Il valore per `batchSize` non può superare il valore di `count` nell'elemento copy.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

La `mode` proprietà accetta anche **parallel**, che è il valore predefinito.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Per distribuire in serie più istanze di una risorsa, impostare l'elemento Decorator sul numero di `batchSize` [](./bicep-file.md#resource-and-module-decorators) istanze da distribuire contemporaneamente. Con la modalità seriale, Resource Manager crea una dipendenza da istanze precedenti nel ciclo in modo un batch venga avviato solo dopo il completamento del batch precedente.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## <a name="iteration-for-a-child-resource"></a>Iterazione di una risorsa figlio

Non è possibile usare un ciclo di copia per una risorsa figlio. Per creare più istanze di una risorsa cosiddetta "annidata" all'interno di un'altra risorsa è invece necessario creare tale risorsa come una risorsa di livello superiore. La relazione con la risorsa padre si definisce con le proprietà type e name.

Si supponga, ad esempio, di definire in genere un set di dati come una risorsa figlio all'interno di una data factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Per creare più set di dati, spostarlo all'esterno della data factory. Il set di dati deve essere sullo stesso livello della data factory, di cui è comunque una risorsa figlio. La relazione fra set di dati e data factory viene mantenuta con le proprietà type e name. Poiché non è possibile dedurre il tipo dalla sua posizione nel modello, è necessario specificarne il nome completo nel formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Per stabilire una relazione padre/figlio con un'istanza della data factory, specificare il nome del set di dati che include il nome della risorsa padre. Usare il formato: `{parent-resource-name}/{child-resource-name}`.

Nell'esempio seguente viene descritta l'implementazione:

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## <a name="example-templates"></a>Modelli di esempio

Gli esempi seguenti mostrano alcuni scenari comuni per la creazione di più istanze di una risorsa o proprietà.

|Modello  |Descrizione  |
|---------|---------|
|[Copia risorsa di archiviazione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Distribuisce più account di archiviazione con un numero di indice nel nome. |
|[Copia seriale risorse di archiviazione](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Distribuisce più account di archiviazione uno alla volta. Il nome include il numero di indice. |
|[Copia risorsa di archiviazione con matrice](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Distribuisce più account di archiviazione. Il nome include un valore di una matrice. |

## <a name="next-steps"></a>Passaggi successivi

- Per impostare le dipendenze dalle risorse create in un ciclo di copia, vedere Definire l'ordine di distribuzione [delle risorse nei modelli arm.](define-resource-dependency.md)
- Per completare un'esercitazione, vedere [Esercitazione: Creare più istanze di risorse con i modelli arm.](template-tutorial-create-multiple-instances.md)
- Per un modulo Microsoft Learn che illustra la copia delle risorse, vedere [Gestire distribuzioni cloud complesse usando le funzionalità avanzate dei modelli di ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
- Per altri usi del ciclo di copia, vedere:
  - [Iterazione delle proprietà nei modelli di Arm](copy-properties.md)
  - [Iterazione delle variabili nei modelli di Arm](copy-variables.md)
  - [Iterazione di output nei modelli di Arm](copy-outputs.md)
- Per informazioni sull'uso della copia con modelli annidati, vedere [Using copy](linked-templates.md#using-copy).
