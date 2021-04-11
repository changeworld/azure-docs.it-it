---
title: Struttura e sintassi del file bicipite
description: Descrive la struttura e le proprietà di un file bicipite usando la sintassi dichiarativa.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 09993ae9c08f53144de8e94e6555ad93bec681f6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168689"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Comprendere la struttura e la sintassi dei file bicipite

Questo articolo descrive la struttura di un file bicipite. Vengono presentate le diverse sezioni del file e le proprietà disponibili in tali sezioni.

Questo articolo è destinato agli utenti che hanno familiarità con i file bicipite. Fornisce informazioni dettagliate sulla struttura del modello. Per un'esercitazione dettagliata che illustra il processo di creazione di un file bicipite, vedere [esercitazione: creare e distribuire il primo file bicipite Azure Resource Manager](bicep-tutorial-create-first-bicep.md).

## <a name="template-format"></a>Formato del modello

Un file bicipite presenta gli elementi seguenti. Gli elementi possono essere visualizzati in qualsiasi ordine.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

output <output-name> <output-data-type> = <output-value>
```

Nell'esempio seguente viene illustrata un'implementazione di questi elementi.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

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

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Ambito di destinazione

Per impostazione predefinita, l'ambito di destinazione è impostato su `resourceGroup` . Se si esegue la distribuzione a livello di gruppo di risorse, non è necessario impostare l'ambito di destinazione nel file bicipite.

Di seguito sono elencati i valori consentiti:

* **resourceGroup** : valore predefinito, usato per le [distribuzioni di gruppi di risorse](deploy-to-resource-group.md).
* **sottoscrizione** : usata per le [distribuzioni di sottoscrizioni](deploy-to-subscription.md).
* **managementGroup** : usato per le [distribuzioni del gruppo di gestione](deploy-to-management-group.md).
* **tenant** : usato per le [distribuzioni tenant](deploy-to-tenant.md).

## <a name="parameters"></a>Parametri

Usare i parametri per i valori che devono variare per diverse distribuzioni. È possibile definire un valore predefinito per il parametro utilizzato se non viene specificato alcun valore durante la distribuzione.

Ad esempio, è possibile aggiungere un parametro SKU per specificare dimensioni diverse per una risorsa. È possibile usare le funzioni modello per creare il valore predefinito, ad esempio il recupero della posizione del gruppo di risorse.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Per i tipi di dati disponibili, vedere [tipi di dati nei modelli](data-types.md).

Per altre informazioni, vedere [Parameters in templates](template-parameters.md).

## <a name="parameter-decorators"></a>Elementi Decorator del parametro

È possibile aggiungere uno o più elementi Decorator per ogni parametro. Questi elementi Decorator definiscono i valori consentiti per il parametro. Nell'esempio seguente vengono specificati gli SKU che possono essere distribuiti tramite il file bicipite.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

Nella tabella seguente vengono descritti gli elementi Decorator disponibili e il modo in cui utilizzarli.

| Elemento Decorator | Apply to | Argomento | Descrizione |
| --------- | ---- | ----------- | ------- |
| autorizzate | all | array | Valori consentiti per il parametro. Utilizzare questo elemento Decorator per assicurarsi che l'utente fornisca valori corretti. |
| description | all | string | Testo che spiega come usare il parametro. La descrizione viene visualizzata dagli utenti tramite il portale. |
| maxLength | Array, stringa | INT | Lunghezza massima per i parametri di stringa e di matrice. Il valore è inclusivo. |
| maxValue | INT | INT | Valore massimo per il parametro integer. Questo valore è inclusivo. |
| metadata | all | object | Proprietà personalizzate da applicare al parametro. Può includere una proprietà Description equivalente all'elemento Decorator Description. |
| minLength | Array, stringa | INT | Lunghezza minima per i parametri di stringa e di matrice. Il valore è inclusivo. |
| minValue | INT | INT | Valore minimo per il parametro integer. Questo valore è inclusivo. |
| secure | String, oggetto | Nessuno | Contrassegna il parametro come protetto. Il valore di un parametro sicuro non viene salvato nella cronologia di distribuzione e non viene registrato. Per altre informazioni, vedere [proteggere stringhe e oggetti](data-types.md#secure-strings-and-objects). |

## <a name="variables"></a>Variabili

Usare le variabili per le espressioni complesse ripetute in un file bicipite. Ad esempio, è possibile aggiungere una variabile per un nome di risorsa costruito concatenando più valori contemporaneamente.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Non è possibile specificare un [tipo di dati](data-types.md) per una variabile. Al contrario, il tipo di dati viene dedotto dal valore.

Per altre informazioni, vedere [variabili nei modelli](template-variables.md).

## <a name="resource"></a>Risorsa

Usare la `resource` parola chiave per definire una risorsa da distribuire. La dichiarazione di risorsa include un nome simbolico per la risorsa. Questo nome simbolico verrà usato in altre parti del file bicipite se è necessario ottenere un valore dalla risorsa.

La dichiarazione di risorsa include anche il tipo di risorsa e la versione dell'API.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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
```

Nella dichiarazione di risorsa si includono le proprietà per il tipo di risorsa. Queste proprietà sono univoche per ogni tipo di risorsa.

Per altre informazioni, vedere [dichiarazione di risorse nei modelli](resource-declaration.md).

Per [distribuire una risorsa in modo condizionale](conditional-resource-deployment.md), aggiungere un' `if` espressione.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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
```

Per [distribuire più](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) di un'istanza di un tipo di risorsa, aggiungere un' `for` espressione. L'espressione può scorrere i membri di una matrice.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

## <a name="modules"></a>Moduli

Usare i moduli per collegare altri file bicipiti che contengono codice che si vuole riutilizzare. Il modulo contiene una o più risorse da distribuire. Le risorse vengono distribuite insieme a qualsiasi altra risorsa nel file bicipite.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Il nome simbolico consente di fare riferimento al modulo da un'altra posizione nel file. Ad esempio, è possibile ottenere un valore di output da un modulo usando il nome simbolico e il nome del valore di output.

Analogamente alle risorse, è possibile distribuire un modulo in modo condizionale o iterativo. La sintassi è identica per i moduli di risorse.

Per altre informazioni, vedere [use bicipit Modules](bicep-modules.md).

## <a name="resource-and-module-decorators"></a>Elementi Decorator di risorse e moduli

È possibile aggiungere un elemento Decorator a una definizione di risorsa o di modulo. L'unico elemento Decorator supportato è `batchSize(int)` . È possibile applicarlo solo a una definizione di risorsa o modulo che usa un' `for` espressione.

Per impostazione predefinita, le risorse vengono distribuite in parallelo. Non si conosce l'ordine in cui vengono completate. Quando si aggiunge l' `batchSize` elemento Decorator, le istanze vengono distribuite in modo seriale. Usare l'argomento integer per specificare il numero di istanze da distribuire in parallelo.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Per ulteriori informazioni, vedere [seriale o parallelo](copy-resources.md#serial-or-parallel).

## <a name="outputs"></a>Output

Usare output per restituire valore dalla distribuzione. In genere, viene restituito un valore da una risorsa distribuita quando è necessario riutilizzare tale valore per un'altra operazione.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Specificare un [tipo di dati](data-types.md) per il valore di output.

Per ulteriori informazioni, vedere [output nei modelli](template-outputs.md).

## <a name="comments"></a>Commenti

Usare `//` per i commenti a riga singola o `/* ... */` per i commenti su più righe

Nell'esempio seguente viene illustrato un commento a riga singola.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

Nell'esempio seguente viene illustrato un commento su più righe.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Stringhe a più righe

È possibile suddividere una stringa in più righe. Usare tre virgolette singole `'''` per iniziare e terminare la stringa a più righe. 

I caratteri all'interno della stringa a più righe vengono gestiti così come sono. I caratteri di escape non sono necessari. Non è possibile includere `'''` nella stringa a più righe. L'interpolazione di stringhe non è attualmente supportata.

È possibile avviare la stringa subito dopo l'apertura `'''` o includere una nuova riga. In entrambi i casi, la stringa risultante non include una nuova riga. A seconda delle terminazioni di riga nel file bicipite, le nuove righe vengono interpretate come `\r\n` o `\n` .

Nell'esempio seguente viene illustrata una stringa a più righe.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

L'esempio precedente è equivalente al codice JSON seguente.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione a bicipite, vedere [che cos'è il bicipite?](bicep-overview.md).
