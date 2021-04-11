---
title: Creare i criteri per le proprietà della matrice nelle risorse
description: Informazioni su come usare i parametri della matrice e le espressioni del linguaggio della matrice, valutare l'alias [*] e aggiungere elementi con le regole di definizione di Criteri di Azure.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: d4e059f3691554aa91dfd15cf308ef62afa58928
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089968"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Creare i criteri per le proprietà della matrice nelle risorse di Azure

Le proprietà di Azure Resource Manager sono comunemente definite come stringhe e valori booleani. Quando esiste una relazione uno-a-molti, le proprietà complesse sono invece definite come matrici. In Criteri di Azure le matrici vengono usate in modi diversi:

- Tipo di un [parametro della definizione](../concepts/definition-structure.md#parameters) per offrire più opzioni
- Parte di una [regola dei criteri](../concepts/definition-structure.md#policy-rule) che usa le condizioni **in** o **notIn**
- Parte di una regola dei criteri che conta il numero di membri della matrice che soddisfano una condizione
- In [aggiungere](../concepts/effects.md#append) e [modificare](../concepts/effects.md#modify) gli effetti per aggiornare una matrice esistente

Questo articolo descrive tutti gli usi di Criteri di Azure e offre diverse definizioni di esempio.

## <a name="parameter-arrays"></a>Matrici di parametri

### <a name="define-a-parameter-array"></a>Definire una matrice di parametri

La definizione di un parametro come matrice consente l'uso di criteri flessibili quando è necessario più di un valore.
La definizione dei criteri consente di usare un'unica località qualsiasi per il parametro **allowedLocations**. Il valore predefinito è _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Il valore di **type** è _string_, quindi è possibile impostare un solo valore quando si assegnano i criteri. Se si assegnano questi criteri, le risorse nell'ambito sono consentite solo all'interno di un'unica area di Azure. La maggior parte delle definizioni dei criteri deve consentire un elenco di opzioni approvate, ad esempio _eastus2_, _eastus_ e _westus2_.

Per creare la definizione dei criteri che consenta più opzioni, usare _array_ per **type**. Gli stessi criteri possono essere riscritti nel modo seguente:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Dopo aver salvato una definizione dei criteri, la proprietà **type** in un parametro non può essere modificata.

Questa nuova definizione dei parametri accetta più di un valore durante l'assegnazione dei criteri. Dopo aver definito la proprietà della matrice **allowedValues**, i valori disponibili durante l'assegnazione sono esclusivamente quelli disponibili nell'elenco predefinito delle opzioni. L'uso di **allowedValues** è facoltativo.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passare i valori a una matrice di parametri durante l'assegnazione

Quando si assegnano i criteri tramite il portale di Azure, un parametro di **type** _array_ viene visualizzato come unica casella di testo, in cui viene visualizzato il suggerimento "Usare il ; per separare i valori. Ad esempio: Londra;New York". Per passare i valori delle località consentite di _eastus2_, _eastus_ e _westus2_ al parametro, usare la stringa seguente:

`eastus2;eastus;westus2`

Il formato del valore del parametro è diverso quando si usa l'interfaccia della riga di comando di Azure, Azure PowerShell o l'API REST. I valori vengono passati tramite una stringa JSON che include anche il nome del parametro.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Per usare questa stringa con ogni SDK, usare i comandi seguenti:

- Interfaccia della riga di comando di Azure: comando [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) con il parametro **params**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) con il parametro **PolicyParameter**
- API REST: durante l'operazione di [creazione](/rest/api/resources/policyassignments/create) di tipo _PUT_ come parte del corpo della richiesta, come valore della proprietà **properties.parameters**

## <a name="using-arrays-in-conditions"></a>Utilizzo di matrici in condizioni

### <a name="in-and-notin"></a>In e notIn

Le `in` `notIn` condizioni e funzionano solo con valori di matrice. Verificano l'esistenza di un valore in una matrice. La matrice può essere una matrice JSON letterale o un riferimento a un parametro di matrice. Ad esempio:

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>Conteggio valori

L'espressione di [conteggio dei valori](../concepts/definition-structure.md#value-count) conta il numero di membri della matrice che soddisfano una condizione. Fornisce un modo per valutare la stessa condizione più volte, usando valori diversi per ogni iterazione. Ad esempio, la condizione seguente controlla se il nome della risorsa corrisponde a qualsiasi modello di una matrice di modelli:

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Per valutare l'espressione, i criteri di Azure valutano la `where` condizione tre volte, una volta per ogni membro di `[ "test*", "dev*", "prod*" ]` , conteggiando il numero di volte in cui è stato valutato `true` . A ogni iterazione, il valore del membro della matrice corrente viene abbinato al `pattern` nome di indice definito da `count.name` . È possibile fare riferimento a questo valore all'interno della `where` condizione chiamando una funzione di modello speciale: `current('pattern')` .

| Iterazione | `current('pattern')` valore restituito |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

La condizione è true solo se il conteggio risultante è maggiore di 0.

Per rendere la condizione sopra più generica, usare il riferimento al parametro anziché una matrice di valori letterali:

 ```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Quando l'espressione di **conteggio dei valori** non è inclusa in nessun'altra espressione **count** , `count.name` è facoltativa e la `current()` funzione può essere usata senza argomenti:

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

Il **conteggio dei valori** supporta inoltre matrici di oggetti complessi, consentendo condizioni più complesse. Ad esempio, la condizione seguente definisce un valore di tag desiderato per ogni modello di nome e controlla se il nome della risorsa corrisponde al modello, ma non ha il valore del tag necessario:

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

Per esempi utili, vedere [esempi di conteggio dei valori](../concepts/definition-structure.md#value-count-examples).

## <a name="referencing-array-resource-properties"></a>Riferimento alle proprietà delle risorse della matrice

Molti casi d'uso richiedono l'utilizzo di proprietà di matrice nella risorsa valutata. Per alcuni scenari è necessario fare riferimento a un'intera matrice, ad esempio per verificarne la lunghezza. Altri richiedono l'applicazione di una condizione a ogni singolo membro della matrice (ad esempio, assicurarsi che tutte le regole del firewall blocchino l'accesso da Internet). Informazioni sui diversi modi in cui i criteri di Azure possono fare riferimento alle proprietà delle risorse e sul comportamento di questi riferimenti quando fanno riferimento a proprietà di matrice è la chiave per la scrittura di condizioni che coprono questi scenari.

### <a name="referencing-resource-properties"></a>Riferimento alle proprietà delle risorse

I criteri di Azure possono fare riferimento alle proprietà delle risorse usando gli [alias](../concepts/definition-structure.md#aliases) . Esistono due modi per fare riferimento ai valori di una proprietà di risorsa all'interno di criteri di Azure:

- Usare la condizione di [campo](../concepts/definition-structure.md#fields) per verificare se **tutte le** proprietà della risorsa selezionata soddisfano una condizione. Esempio:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Usare `field()` la funzione per accedere al valore di una proprietà. Esempio:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

La condizione di campo ha un comportamento "All" implicito. Se l'alias rappresenta una raccolta di valori, controlla se tutti i singoli valori soddisfano la condizione. La `field()` funzione restituisce i valori rappresentati dall'alias così come sono, che possono essere modificati da altre funzioni di modello.

### <a name="referencing-array-fields"></a>Riferimenti ai campi di matrice

Le proprietà delle risorse di matrice sono in genere rappresentate da due tipi diversi di alias. Un alias "normale" e gli [alias di matrice](../concepts/definition-structure.md#understanding-the--alias) `[*]` collegati:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Riferimento alla matrice

Il primo alias rappresenta un singolo valore, ovvero il valore della `stringArray` Proprietà dal contenuto della richiesta. Poiché il valore di tale proprietà è una matrice, non è utile nelle condizioni dei criteri. Ad esempio:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Questa condizione Confronta l'intera `stringArray` matrice con un solo valore stringa. La maggior parte delle condizioni, tra cui `equals` , accettano solo valori stringa, quindi non c'è molto da usare per il confronto di una matrice con una stringa. Lo scenario principale in cui viene fatto riferimento alla proprietà Array è utile quando si controlla se esiste:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Con la `field()` funzione, il valore restituito è la matrice dal contenuto della richiesta, che può quindi essere usato con una qualsiasi delle [funzioni di modello supportate](../concepts/definition-structure.md#policy-functions) che accettano argomenti di matrice. Ad esempio, la condizione seguente controlla se la lunghezza di `stringArray` è maggiore di 0:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Riferimento alla raccolta di membri della matrice

Gli alias che usano la `[*]` sintassi rappresentano una **raccolta di valori di proprietà selezionati da una proprietà di matrice**, che è diversa dalla selezione della proprietà della matrice stessa. Nel caso di `Microsoft.Test/resourceType/stringArray[*]` , restituisce una raccolta che contiene tutti i membri di `stringArray` . Come indicato in precedenza, una `field` condizione verifica che tutte le proprietà della risorsa selezionate soddisfino la condizione, pertanto la condizione seguente è vera solo se **tutti** i membri di `stringArray` sono uguali a "" valore "".

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Se la matrice contiene oggetti, `[*]` è possibile utilizzare un alias per selezionare il valore di una proprietà specifica da ogni membro della matrice. Esempio:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Questa condizione è true se i valori di tutte le `property` Proprietà in `objectArray` sono uguali a `"value"` . Per altri esempi, vedere [ \[ \* \] esempi di alias aggiuntivi](#additional--alias-examples).

Quando si usa la `field()` funzione per fare riferimento a un alias di matrice, il valore restituito è una matrice di tutti i valori selezionati. Questo comportamento indica che il caso di utilizzo comune della `field()` funzione, la possibilità di applicare funzioni modello ai valori delle proprietà delle risorse, è limitato. Le uniche funzioni di modello che è possibile usare in questo caso sono quelle che accettano argomenti di matrice. Ad esempio, è possibile ottenere la lunghezza della matrice con `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . Tuttavia, scenari più complessi, ad esempio l'applicazione di una funzione di modello a ogni membro della matrice e il confronto con un valore desiderato, sono possibili solo quando si usa l' `count` espressione. Per altre informazioni, vedere [espressione del conteggio dei campi](#field-count-expressions).

Per riepilogare, vedere il contenuto della risorsa di esempio seguente e i valori selezionati restituiti da diversi alias:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

Quando si usa la condizione di campo nel contenuto della risorsa di esempio, i risultati sono i seguenti:

| Alias | Valori selezionati |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Raccolta di valori vuota. |
| `Microsoft.Test/resourceType/missingArray[*].property` | Raccolta di valori vuota. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Quando si usa la `field()` funzione nel contenuto della risorsa di esempio, i risultati sono i seguenti:

| Expression | Valore restituito |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

### <a name="field-count-expressions"></a>Espressioni di conteggio campi

Le espressioni di [conteggio dei campi](../concepts/definition-structure.md#field-count) consentono di contare il numero di membri della matrice che soddisfano una condizione e confrontare il conteggio con un valore di destinazione. `Count` è più intuitivo e versatile per la valutazione delle matrici rispetto alle `field` condizioni. La sintassi è:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Se utilizzata senza una `where` condizione, `count` restituisce semplicemente la lunghezza di una matrice. Con il contenuto della risorsa di esempio della sezione precedente, l' `count` espressione seguente viene valutata `true` perché `stringArray` ha tre membri:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Questo comportamento funziona anche con matrici annidate. L'espressione seguente, ad esempio, `count` viene valutata `true` perché sono presenti quattro membri di matrice nelle `nestedArray` matrici:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

La potenza di `count` si trova nella `where` condizione. Quando viene specificato, criteri di Azure enumera i membri della matrice e ne valuta ognuno rispetto alla condizione, conteggiando il numero di membri della matrice valutati `true` . In particolare, in ogni iterazione della `where` valutazione della condizione, criteri di Azure seleziona un singolo membro della matrice ***i** _ e valuta il contenuto della risorsa rispetto alla `where` condizione _* come se ***i**_ fosse l'unico membro del array_ *. Se è disponibile un solo membro della matrice in ogni iterazione, è possibile applicare condizioni complesse a ogni singolo membro della matrice.

Esempio:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```

Per valutare l' `count` espressione, i criteri di Azure valutano la `where` condizione tre volte, una volta per ogni membro di `stringArray` , conteggiando il numero di volte in cui è stato valutato `true` .
Quando la `where` condizione si riferisce ai `Microsoft.Test/resourceType/stringArray[*]` membri della matrice, anziché selezionare tutti i membri di `stringArray` , verrà selezionato ogni volta un singolo membro della matrice:

| Iterazione | `Microsoft.Test/resourceType/stringArray[*]`Valori selezionati | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

`count`Restituisce `1` .

Ecco un'espressione più complessa:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iterazione | Valori selezionati | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

`count`Restituisce `1` .

Il fatto che l' `where` espressione venga valutata rispetto all' **intero** contenuto della richiesta (con modifiche solo al membro della matrice attualmente in corso di enumerazione) significa che la `where` condizione può anche fare riferimento a campi all'esterno della matrice:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  },
  "equals": 0
}
```

| Iterazione | Valori selezionati | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Le espressioni di conteggio annidate possono essere utilizzate per applicare condizioni ai campi di matrici annidate. Ad esempio, la condizione seguente controlla che la `objectArray[*]` matrice disponga esattamente di due membri con `nestedArray[*]` che contiene uno o più membri:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Iterazione | Valori selezionati | Risultato della valutazione del conteggio annidato |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` ha 2 membri => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` ha 2 membri => `true` |

Poiché entrambi i membri di `objectArray[*]` hanno una matrice figlio `nestedArray[*]` con due membri, l'espressione conteggio esterno restituisce `2` .

Esempio più complesso: verificare che la `objectArray[*]` matrice includa esattamente due membri con i `nestedArray[*]` membri uguali a `2` o `3` :

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "count": {
        "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
        "where": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "in": [ 2, 3 ]
        }
      },
      "greaterOrEquals": 1
    }
  },
  "equals": 2
}
```

| Iterazione | Valori selezionati | Risultato della valutazione del conteggio annidato
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `nestedArray[*]` contiene `2` => `true` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | `nestedArray[*]` contiene `3` => `true` |

Poiché entrambi i membri di `objectArray[*]` hanno una matrice figlio `nestedArray[*]` che contiene `2` o `3` , l'espressione del conteggio esterno restituisce `2` .

> [!NOTE]
> Le espressioni di conteggio campi annidati possono fare riferimento solo a matrici annidate. Ad esempio, l'espressione count che fa riferimento a `Microsoft.Test/resourceType/objectArray[*]` può avere un conteggio annidato che fa riferimento alla matrice nidificata `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` , ma non può avere un'espressione di conteggio annidata come destinazione `Microsoft.Test/resourceType/stringArray[*]` .

#### <a name="accessing-current-array-member-with-template-functions"></a>Accesso al membro della matrice corrente con funzioni di modello

Quando si usano le funzioni modello, usare la `current()` funzione per accedere al valore del membro della matrice corrente o ai valori di una delle relative proprietà. Per accedere al valore del membro della matrice corrente, passare l'alias definito in `count.field` o uno qualsiasi degli alias figlio come argomento della `current()` funzione. Ad esempio:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| Iterazione | `current()` valore restituito | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | Valore di `property` nel primo membro di `objectArray[*]` : `value1` | `true` |
| 2 | Valore di `property` nel primo membro di `objectArray[*]` : `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>Funzione Field all'interno delle condizioni WHERE

La `field()` funzione può essere usata anche per accedere al valore del membro della matrice corrente, purché l'espressione **count** non si trovi all'interno di una **condizione di esistenza** (la `field()` funzione fa sempre riferimento alla risorsa valutata nella condizione **if** ). Il comportamento di `field()` quando si fa riferimento alla matrice valutata è basato sui concetti seguenti:

1. Gli alias di matrice vengono risolti in una raccolta di valori selezionati da tutti i membri della matrice.
1. `field()` le funzioni che fanno riferimento agli alias di matrice restituiscono una matrice con i valori selezionati.
1. Se si fa riferimento all'alias di matrice conteggiato all'interno della `where` condizione, viene restituita una raccolta con un solo valore selezionato dal membro della matrice valutato nell'iterazione corrente.

Questo comportamento indica che, quando si fa riferimento al membro della matrice conteggiata con una `field()` funzione all'interno della `where` condizione, **viene restituita una matrice con un singolo membro**. Sebbene questo comportamento potrebbe non essere intuitivo, è coerente con l'idea che gli alias di matrice restituiscano sempre una raccolta di proprietà selezionate. Ecco un esempio:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iterazione | Valori dell'espressione | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Pertanto, quando è necessario accedere al valore dell'alias della matrice conteggiata con una `field()` funzione, il modo in cui eseguire questa operazione consiste nell'eseguirne il wrapping con una `first()` funzione di modello:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iterazione | Valori dell'espressione | `where` Risultato della valutazione |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Per esempi utili, vedere [esempi di conteggio dei campi](../concepts/definition-structure.md#field-count-examples).

## <a name="modifying-arrays"></a>Modifica di matrici

[Accodare](../concepts/effects.md#append) e [modificare](../concepts/effects.md#modify) le proprietà ALTER su una risorsa durante la creazione o l'aggiornamento. Quando si utilizzano le proprietà della matrice, il comportamento di questi effetti dipende dal fatto che l'operazione stia tentando di modificare **\[\*\]** o meno l'alias:

> [!NOTE]
> L'uso dell' `modify` effetto con gli alias è attualmente in **Anteprima**.

|Alias |Effetto | Risultato |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Criteri di Azure accoda l'intera matrice specificata nei dettagli dell'effetto, se mancante. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify``add`operazione with | Criteri di Azure accoda l'intera matrice specificata nei dettagli dell'effetto, se mancante. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify``addOrReplace`operazione with | Criteri di Azure accoda l'intera matrice specificata nei dettagli effetti se mancante o sostituisce la matrice esistente. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Criteri di Azure Accoda il membro della matrice specificato nei dettagli dell'effetto. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify``add`operazione with | Criteri di Azure Accoda il membro della matrice specificato nei dettagli dell'effetto. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify``addOrReplace`operazione with | Criteri di Azure rimuove tutti i membri della matrice esistenti e accoda il membro della matrice specificato nei dettagli dell'effetto. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Criteri di Azure aggiunge un valore alla `action` proprietà di ogni membro della matrice. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify``add`operazione with | Criteri di Azure aggiunge un valore alla `action` proprietà di ogni membro della matrice. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify``addOrReplace`operazione with | Criteri di Azure aggiunge o sostituisce la `action` proprietà esistente di ogni membro della matrice. |

Per altre informazioni, vedere gli [esempi di Append](../concepts/effects.md#append-examples).

## <a name="additional--alias-examples"></a>Altri esempi di alias [*]

È consigliabile usare le espressioni di [conteggio dei campi](#field-count-expressions) per verificare se tutti i membri di una matrice nel contenuto della richiesta soddisfano una condizione. Tuttavia, per alcune semplici condizioni è possibile ottenere lo stesso risultato usando una funzione di accesso Field con un alias di matrice, come descritto in [riferimento alla raccolta di membri della matrice](#referencing-the-array-members-collection). Questo modello può essere utile nelle regole dei criteri che superano il limite di espressioni di **conteggio** consentito. Ecco alcuni esempi per i casi d'uso comuni:

Esempio di regola dei criteri per la tabella dello scenario seguente:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

La matrice **ipRules** per la tabella dello scenario è la seguente:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Per ogni condizione di esempio riportata di seguito, sostituire `<field>` con `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Di seguito sono riportati i risultati della combinazione della condizione, la regola dei criteri di esempio e la matrice di valori esistenti precedenti:

|Condizione |Risultato | Scenario |Spiegazione |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nessuno |Nessuna corrispondenza |Un elemento della matrice restituisce false (127.0.0.1! = 127.0.0.1) e uno true (127.0.0.1! = 192.168.1.1), quindi la condizione **notEquals** è _false_ e l'effetto non viene attivato. |
|`{<field>,"notEquals":"10.0.4.1"}` |Effetto dei criteri |Nessuna corrispondenza |Entrambi gli elementi della matrice restituiscono true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), quindi la condizione **notEquals** è _true_ e l'effetto viene attivato. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Effetto dei criteri |Una o più corrispondenze |Un elemento della matrice restituisce false (127.0.0.1! = 127.0.0.1) e uno true (127.0.0.1! = 192.168.1.1), quindi la condizione **notEquals** è _false_. L'operatore logico restituisce true (**non** _false_), quindi l'effetto viene attivato. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nessuno |Una o più corrispondenze |Entrambi gli elementi della matrice restituiscono true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), quindi la condizione **notEquals** è _true_. L'operatore logico restituisce false (**non** _true_), quindi l'effetto non viene attivato. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Effetto dei criteri |Corrispondenza non totale |Un elemento della matrice restituisce true (127.0.0.1 == 127.0.0.1) e uno false (127.0.0.1 == 192.168.1.1), quindi la condizione **Equals** è _false_. L'operatore logico restituisce true (**non** _false_), quindi l'effetto viene attivato. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Effetto dei criteri |Corrispondenza non totale |Entrambi gli elementi della matrice restituiscono false (10.0.4.1 == 127.0.0.1 and 10.0.4.1 == 192.168.1.1), quindi la condizione **Equals** è _false_. L'operatore logico restituisce true (**non** _false_), quindi l'effetto viene attivato. |
|`{<field>,"Equals":"127.0.0.1"}` |Nessuno |Corrispondenza totale |Un elemento della matrice restituisce true (127.0.0.1 == 127.0.0.1) e uno false (127.0.0.1 == 192.168.1.1), quindi la condizione **Equals** è _false_ e l'effetto non viene attivato. |
|`{<field>,"Equals":"10.0.4.1"}` |Nessuno |Corrispondenza totale |Entrambi gli elementi della matrice restituiscono false (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), quindi la condizione **Equals** è _false_ e l'effetto non viene attivato. |

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [correggere le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
