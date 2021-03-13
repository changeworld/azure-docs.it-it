---
title: Filtri eventi per Griglia di eventi di Azure
description: Questo articolo illustra come filtrare gli eventi quando si crea una sottoscrizione di Griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: fa63296f97bfa888cb0f425d0c03a5e4a7e46525
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419848"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Informazioni sui filtri eventi per le sottoscrizioni di Griglia di eventi

Questo articolo descrive i diversi modi per filtrare gli eventi che vengono inviati all'endpoint. Quando si crea una sottoscrizione di eventi, sono disponibili tre opzioni di filtro:

* Tipi di eventi
* L'oggetto inizia o termina con
* Campi e operatori avanzati

## <a name="event-type-filtering"></a>Filtro in base al tipo di evento

Per impostazione predefinita, tutti i [tipi di evento](event-schema.md) dell'origine eventi vengono inviati all'endpoint. È possibile decidere di inviare all'endpoint solo determinati tipi di evento. È ad esempio possibile ricevere le notifiche degli aggiornamenti alle risorse, ma non quelle relative ad altre operazioni, come le eliminazioni. In tal caso, filtrare in base al tipo di evento `Microsoft.Resources.ResourceWriteSuccess`. Fornire una matrice con i tipi di evento o specificare `All` per ottenere tutti i tipi di evento per l'origine eventi.

La sintassi JSON per l'applicazione di un filtro in base al tipo di evento è la seguente:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtro in base all'oggetto

Per applicare un filtro semplice in base all'oggetto, specificare un valore iniziale o finale per l'oggetto. È ad esempio possibile specificare che l'oggetto termina con `.txt` per ottenere solo gli eventi correlati al caricamento di un file di testo in un account di archiviazione. È anche possibile specificare che l'oggetto inizia con `/blobServices/default/containers/testcontainer` per ottenere tutti gli eventi per il contenitore specificato, ma non per altri contenitori nell'account di archiviazione.

Quando si pubblicano eventi per argomenti personalizzati, creare oggetti per gli eventi che facilitino i sottoscrittori a capire se sono interessati nell'evento. I sottoscrittori usano la proprietà subject per filtrare e instradare gli eventi. È consigliabile aggiungere il percorso in cui si è verificato l'evento, in modo che i sottoscrittori possano filtrare in base ai segmenti di tale percorso. Il percorso consente ai sottoscrittori di filtrare gli eventi a seconda della dimensione. Se ad esempio si specifica un percorso di tre segmenti, come `/A/B/C` nell'oggetto, i sottoscrittori possono filtrare in base al primo segmento `/A` per ottenere un ampio set di eventi. Tali sottoscrittori ricevono eventi con oggetti come `/A/B/C` o `/A/D/E`. Altri sottoscrittori possono filtrare in base a `/A/B` per ottenere un set di eventi più ristretto.

La sintassi JSON per il filtro in base al soggetto è:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtro avanzato

Per filtrare i valori nei campi dati e specificare l'operatore di confronto, usare l'opzione di filtro avanzato. Quando si applica un filtro avanzato si specificano gli elementi seguenti:

* operator type: il tipo di confronto.
* key: il campo nei dati dell'evento che viene usato per il filtro. Può essere un numero, un valore booleano, una stringa o una matrice.
* values: valore o valori da confrontare con la chiave.

## <a name="key"></a>Chiave
Key è il campo dei dati dell'evento che si sta utilizzando per filtrare. Può essere uno dei tipi seguenti:

- Number
- Boolean
- string
- Matrice  `enableAdvancedFilteringOnArrays`Per usare questa funzionalità, è necessario impostare la proprietà su true. Attualmente, il portale di Azure non supporta l'abilitazione di questa funzionalità. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

Per gli eventi nello **schema di griglia di eventi**, usare i valori seguenti per la chiave: `ID` ,, `Topic` `Subject` , `EventType` , o i dati dell' `DataVersion` evento (ad esempio `data.key1` ).

Per gli eventi nello **schema degli eventi Cloud**, usare i valori seguenti per la chiave, ovvero i `eventid` dati dell'evento,, `source` `eventtype` , `eventtypeversion` o (ad esempio `data.key1` ).

Per lo **schema di input personalizzato**, usare i campi dati dell'evento (ad esempio `data.key1` ). Per accedere ai campi nella sezione dati, usare la `.` notazione (punto). Ad esempio, `data.sitename` per `data.appEventTypeDetail.action` accedere a `sitename` o `action` per l'evento di esempio seguente.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>Valori
I valori possibili sono: Number, String, Boolean o Array

## <a name="operators"></a>Operatori

Gli operatori disponibili per i **numeri** sono:

## <a name="numberin"></a>NumberIn
L'operatore numberin restituisce true se il valore della **chiave** è uno dei valori di **filtro** specificati. Nell'esempio seguente viene verificato se il valore dell' `counter` attributo nella `data` sezione è 5 o 1. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a, b, c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
NumberNotIn restituisce true se il valore della **chiave** **non** è uno dei valori di **filtro** specificati. Nell'esempio seguente controlla se il valore dell' `counter` attributo nella `data` sezione non è 41 e 0. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a, b, c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
L'operatore NumberLessThan restituisce true se il valore della **chiave** è minore del valore **di** **filtro** specificato. Nell'esempio seguente controlla se il valore dell' `counter` attributo nella `data` sezione è inferiore a 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto al valore del filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
L'operatore NumberGreaterThan restituisce true se il valore della **chiave** è maggiore del valore **di** **filtro** specificato. Nell'esempio seguente viene verificato se il valore dell' `counter` attributo nella `data` sezione è maggiore di 20. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto al valore del filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
L'operatore NumberLessThanOrEquals restituisce true se il valore della **chiave** è **minore o uguale** al valore di **filtro** specificato. Nell'esempio seguente controlla se il valore dell' `counter` attributo nella `data` sezione è minore o uguale a 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto al valore del filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
L'operatore NumberGreaterThanOrEquals restituisce true se il valore della **chiave** è **maggiore o uguale** al valore di **filtro** specificato. Nell'esempio seguente controlla se il valore dell' `counter` attributo nella `data` sezione è maggiore o uguale a 30. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto al valore del filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
L'operatore NumberInRange restituisce true se il valore della **chiave** è in uno degli **intervalli di filtro** specificati. Nell'esempio seguente viene verificato se il valore dell' `key1` attributo nella `data` sezione è uno dei due intervalli: 3,14159-999,95, 3000-4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

La `values` proprietà è una matrice di intervalli. Nell'esempio precedente si tratta di una matrice di due intervalli. Di seguito è riportato un esempio di matrice con un intervallo da verificare. 

**Matrice con un intervallo:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Di seguito è riportato lo pseudo-codice con la chiave `[v1, v2, v3]` e il filtro, ovvero una matrice di intervalli. In questo pseudo codice, `a` e `b` sono valori bassi e alti di ogni intervallo della matrice. Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
L'operatore NumberNotInRange restituisce true se il valore della **chiave** **non** è in uno degli **intervalli di filtro** specificati. Nell'esempio seguente viene verificato se il valore dell' `key1` attributo nella `data` sezione è uno dei due intervalli: 3,14159-999,95, 3000-4000. In caso contrario, l'operatore restituirà false. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
La `values` proprietà è una matrice di intervalli. Nell'esempio precedente si tratta di una matrice di due intervalli. Di seguito è riportato un esempio di matrice con un intervallo da verificare.

**Matrice con un intervallo:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Di seguito è riportato lo pseudo-codice con la chiave `[v1, v2, v3]` e il filtro, ovvero una matrice di intervalli. In questo pseudo codice, `a` e `b` sono valori bassi e alti di ogni intervallo della matrice. Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


L'operatore disponibile per i **valori booleani** è: 

## <a name="boolequals"></a>BoolEquals
L'operatore BoolEquals restituisce true se il valore della **chiave** è il **filtro** del valore booleano specificato. Nell'esempio seguente viene verificato se il valore dell' `isEnabled` attributo nella `data` sezione è `true` . 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto al valore booleano del filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

Gli operatori disponibili per le **stringhe** sono:

## <a name="stringcontains"></a>StringContains
**StringContains** restituisce true se il valore della **chiave** **contiene** uno dei valori di **filtro** specificati (sotto forma di sottostringhe). Nell'esempio seguente viene verificato se il valore dell' `key1` attributo nella `data` sezione contiene una delle sottostringhe specificate: `microsoft` o `azure` . Ad esempio, `azure data factory` contiene `azure` . 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a,b,c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
L'operatore **StringNotContains** restituisce true se la **chiave** non **contiene** i valori di **filtro** specificati come sottostringhe. Se la chiave contiene uno dei valori specificati come una sottostringa, l'operatore restituisce false. Nell'esempio seguente, l'operatore restituisce true solo se il valore dell' `key1` attributo nella `data` sezione non include `contoso` e `fabrikam` come sottostringhe. 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a,b,c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```
Vedere la sezione [limitazioni](#limitations) per la limitazione corrente di questo operatore.

## <a name="stringbeginswith"></a>StringBeginsWith
L'operatore **StringBeginsWith** restituisce true se il valore della **chiave** **inizia con** uno dei valori di **filtro** specificati. Nell'esempio seguente viene verificato se il valore dell' `key1` attributo nella `data` sezione inizia con `event` o `grid` . Ad esempio, `event hubs` inizia con `event` .  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a,b,c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
L'operatore **StringNotBeginsWith** restituisce true se il valore della **chiave** non **inizia con** uno dei valori di **filtro** specificati. Nell'esempio seguente viene verificato se il valore dell' `key1` attributo nella `data` sezione non inizia con `event` o `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a,b,c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
L'operatore **StringEndsWith** restituisce true se il valore della **chiave** **termina con** uno dei valori di **filtro** specificati. Nell'esempio seguente viene verificato se il valore dell' `key1` attributo nella `data` sezione termina con `jpg` o `jpeg` o `png` . Ad esempio, `eventgrid.png` termina con `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a,b,c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
L'operatore **StringNotEndsWith** restituisce true se il valore della **chiave** non **termina con** uno dei valori di **filtro** specificati. Nell'esempio seguente controlla se il valore dell' `key1` attributo nella `data` sezione non termina con `jpg` o `jpeg` o `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a,b,c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
L'operatore **stringin** controlla se il valore della **chiave** **corrisponde esattamente** a uno dei valori di **filtro** specificati. Nell'esempio seguente viene verificato se il valore dell' `key1` attributo nella `data` sezione è `exact` o `string` o `matches` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a,b,c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
L'operatore **StringNotIn** controlla se il valore della **chiave** non **corrisponde** ad alcuno dei valori di **filtro** specificati. Nell'esempio seguente viene verificato se il valore dell' `key1` attributo nella `data` sezione non è `aws` e `bridge` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

Se la chiave è una matrice, tutti i valori nella matrice vengono controllati rispetto alla matrice di valori di filtro. Ecco il codice pseudo con la chiave: `[v1, v2, v3]` e il filtro: `[a,b,c]` . Qualsiasi valore di chiave con tipi di dati che non corrispondono al tipo di dati del filtro viene ignorato.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


Per tutti i confronti di stringhe non vengono rilevate maiuscole.

> [!NOTE]
> Se il file JSON dell'evento non contiene la chiave di filtro avanzata, Filter è evaulated come **non corrispondente** per gli operatori seguenti: NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, numberin, BoolEquals, StringContains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringNotEndsWith, stringin.
> 
>Il filtro è evaulated come **corrispondente** per gli operatori seguenti: NumberNotIn, StringNotIn.


## <a name="isnullorundefined"></a>IsNullOrUndefined
L'operatore IsNullOrUndefined restituisce true se il valore della chiave è NULL o non definito. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

Nell'esempio seguente Key1 è mancante, quindi l'operatore restituisce true. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

Nell'esempio seguente Key1 è impostato su null, quindi l'operatore restituisce true.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

Se in questi esempi è presente un valore per Key1, l'operatore restituirà false. 

## <a name="isnotnull"></a>IsNotNull
L'operatore IsNotNull restituisce true se il valore della chiave non è NULL o non è definito. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>OR e AND
Se si specifica un singolo filtro con più valori, viene eseguita un'operazione **o** , pertanto il valore del campo chiave deve essere uno di questi valori. Ecco un esempio:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Se si specificano più filtri diversi, viene eseguita un'operazione and, quindi è necessario soddisfare ogni condizione **di** filtro. Ecco un esempio: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
Per gli eventi nello **schema CloudEvents**, usare i valori seguenti per la chiave, ovvero i `eventid` dati dell'evento,, `source` `eventtype` , `eventtypeversion` o (ad esempio `data.key1` ). 

È anche possibile usare [gli attributi del contesto di estensione in CloudEvents 1,0](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes). Nell'esempio seguente `comexampleextension1` e `comexampleothervalue` sono attributi del contesto di estensione. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Di seguito è riportato un esempio di utilizzo di un attributo di contesto di estensione in un filtro.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Limitazioni

I filtri avanzati presentano le limitazioni seguenti:

* 5 filtri avanzati e 25 valori di filtro in tutti i filtri per ogni sottoscrizione di griglia di eventi
* 512 caratteri per ogni valore stringa
* Cinque valori per gli operatori **in** e **not in**
* L' `StringNotContains` operatore non è attualmente disponibile nel portale.
* Chiavi con il carattere **`.` (punto)** . Ad esempio, `http://schemas.microsoft.com/claims/authnclassreference` o `john.doe@contoso.com`. Attualmente non è disponibile alcun supporto per i caratteri di escape nelle chiavi. 

È possibile usare l'elemento key in più filtri.





## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'applicazione di filtri agli eventi con PowerShell e l'interfaccia della riga di comando di Azure, vedere [Filtrare gli eventi per Griglia di eventi](how-to-filter-events.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
