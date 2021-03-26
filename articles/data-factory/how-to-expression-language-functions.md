---
title: Come utilizzare parametri ed espressioni in Azure Data Factory
description: In questo articolo vengono fornite informazioni sulle espressioni e sulle funzioni che è possibile utilizzare per la creazione di entità data factory.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: c9e1abc5fb6f66981f56bc262319587d9fc4265e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566660"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Come utilizzare parametri, espressioni e funzioni in Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-functions-variables.md)
> * [Versione corrente](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In questo documento si incentrerà principalmente sull'apprendimento di concetti fondamentali con diversi esempi per esplorare la possibilità di creare pipeline di dati con parametri all'interno Azure Data Factory. La parametrizzazione e le espressioni dinamiche sono aggiunte significative ad ADF, perché possono risparmiare molto tempo e consentire una soluzione di estrazione, trasformazione, caricamento (ETL) o estrazione, caricamento e trasformazione (ELT) molto più flessibile, riducendo in modo significativo il costo della manutenzione della soluzione e velocizzando l'implementazione delle nuove funzionalità nelle pipeline esistenti. Questi miglioramenti sono dovuti al fatto che la parametrizzazione riduce al minimo la quantità di codice hardware e aumenta il numero di oggetti e processi riutilizzabili in una soluzione.

## <a name="azure-data-factory-ui-and-parameters"></a>Interfaccia utente e parametri di Azure data factory

Se non si ha familiarità con l'uso dei parametri di data factory di Azure nell'interfaccia utente di ADF, vedere interfaccia utente di [Data Factory per i servizi collegati con parametri](./parameterize-linked-services.md#data-factory-ui)  e [interfaccia utente di data factory per la pipeline basata sui metadati con parametri](./how-to-use-trigger-parameterization.md#data-factory-ui) per la spiegazione visiva.

## <a name="parameter-and-expression-concepts"></a>Concetti relativi a parametri ed espressioni 

È possibile usare i parametri per passare i valori esterni in pipeline, set di dati, servizi collegati e flussi di dati. Una volta passato il parametro nella risorsa, non è possibile modificarlo. Le risorse di parametrizzazione possono essere riutilizzate ogni volta con valori diversi. I parametri possono essere usati singolarmente o come parte di espressioni. I valori JSON nella definizione possono essere letterali o espressioni che vengono valutate in fase di esecuzione.

Ad esempio:  
  
```json
"name": "value"
```

 o  
  
```json
"name": "@pipeline().parameters.password"
```

Le espressioni possono trovarsi in qualsiasi punto in un valore stringa JSON e restituiscono sempre un altro valore JSON. Qui, *password* è un parametro della pipeline nell'espressione. Se un valore JSON è un'espressione, il corpo dell'espressione viene estratto rimuovendo il simbolo di chiocciola (\@). Se è necessaria una stringa letterale che inizia con \@, occorre che la stringa sia preceduta da un carattere di escape \@\@. L'esempio seguente illustra la modalità di valutazione delle espressioni.  
  
|Valore JSON|Risultato|  
|----------------|------------|  
|"parameters"|Vengono restituiti i caratteri di tipo 'parameters'.|  
|"parameters[1]"|Vengono restituiti i caratteri di tipo 'parameters[1]'.|  
|"\@\@"|Viene restituita una stringa da 1 caratteri che contiene "\@".|  
|" \@"|Viene restituita una stringa da 2 caratteri che contiene "\@".|  
  
 Tramite una funzionalità denominata *interpolazione delle stringhe*, è possibile inserire le espressioni anche all'interno delle stringhe in cui viene eseguito il wrapping delle espressioni in `@{ ... }`. Ad esempio: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Usando l'interpolazione delle stringhe, il risultato è sempre una stringa. Si supponga di aver definito `myNumber` come `42` e `myString` come `foo`:  
  
|Valore JSON|Risultato|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Restituisce `foo` come stringa.|  
|"\@{pipeline().parameters.myString}"| Restituisce `foo` come stringa.|  
|"\@pipeline().parameters.myNumber"| Restituisce `42` come *numero*.|  
|"\@{pipeline().parameters.myNumber}"| Restituisce `42` come *stringa*.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Restituisce la stringa `Answer is: 42`.|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Restituisce la stringa `Answer is: 42`.|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| Restituisce la stringa `Answer is: @{pipeline().parameters.myNumber}`.|  

## <a name="examples-of-using-parameters-in-expressions"></a>Esempi di utilizzo di parametri nelle espressioni 

### <a name="complex-expression-example"></a>Esempio di espressione complessa
Nell'esempio seguente viene illustrato un esempio complesso che fa riferimento a un campo secondario avanzato dell'output dell'attività. Per fare riferimento a un parametro della pipeline che restituisce un campo secondario, usare la sintassi [] anziché l'operatore punto (.) (come nel caso di subfield1 e subfield2)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Editor contenuto dinamico

L'editor di contenuto dinamico esegue automaticamente il escape dei caratteri nel contenuto al termine della modifica. Ad esempio, il contenuto seguente nell'editor del contenuto è un'interpolazione di stringhe con due funzioni di espressione. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

Il contenuto dell'editor di contenuto dinamico viene convertito in espressione `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` . Il risultato di questa espressione è una stringa di formato JSON mostrata di seguito.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>Un set di dati con parametri

Nell'esempio seguente BlobDataset accetta un parametro denominato **path**. Questo valore viene usato per impostare un valore per la proprietà **folderPath** usando l'espressione seguente: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with--parameters"></a>Una pipeline con parametri

Nell'esempio seguente la pipeline accetta i parametri **inputPath** e **outputPath**. Il parametro **path** per il set di dati del BLOB con parametri viene impostato usando i valori di questi parametri. La sintassi usata è: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```

  
## <a name="calling-functions-within-expressions"></a>Chiamata di funzioni all'interno di espressioni 

È possibile chiamare le funzioni all'interno delle espressioni. Le sezioni seguenti forniscono informazioni sulle funzioni che è possibile usare in un'espressione.  

### <a name="string-functions"></a>Funzioni per i valori stringa  

Per eseguire operazioni con le stringhe, è possibile usare queste funzioni di stringa e alcune [funzioni di raccolta](#collection-functions).
Le funzioni di stringa funzionano solo sulle stringhe.

| Funzione di stringa | Attività |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Combina due o più stringhe e restituisce la stringa combinata. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Verifica se una stringa termina con la sottostringa specificata. |
| [guid](control-flow-expression-language-functions.md#guid) | Generare un identificatore univoco globale (GUID) sotto forma di stringa. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Restituisce la posizione iniziale di una sottostringa. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Restituisce la posizione iniziale o il valore di indice per l'ultima occorrenza di una sottostringa. |
| [replace](control-flow-expression-language-functions.md#replace) | Sostituisce una sottostringa con la stringa specificata e restituisce la stringa aggiornata. |
| [split](control-flow-expression-language-functions.md#split) | Restituisce una matrice che contiene le sottostringhe, separate da virgole, da una stringa più grande in base al carattere delimitatore specificato nella stringa originale. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Verifica se una stringa inizia con una sottostringa specifica. |
| [substring](control-flow-expression-language-functions.md#substring) | Restituisce i caratteri di una stringa, partendo dalla posizione specificata. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Restituisce una stringa in formato minuscolo. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Restituisce una stringa in formato maiuscolo. |
| [Trim](control-flow-expression-language-functions.md#trim) | Rimuove gli spazi iniziali e finali da una stringa e restituisce la stringa aggiornata. |

### <a name="collection-functions"></a>Funzioni di raccolta

Per eseguire operazioni con le raccolte, generalmente matrici, stringhe e talvolta dizionari, è possibile usare queste funzioni di raccolta.

| Funzione di raccolta | Attività |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | Verifica se una raccolta include un elemento specifico. |
| [empty](control-flow-expression-language-functions.md#empty) | Verifica se una raccolta è vuota. |
| [first](control-flow-expression-language-functions.md#first) | Restituisce il primo elemento di una raccolta. |
| [intersection](control-flow-expression-language-functions.md#intersection) | Restituisce una raccolta che contiene *solo* gli elementi comuni alle raccolte specificate. |
| [join](control-flow-expression-language-functions.md#join) | Restituisce una stringa con *tutti* gli elementi di una matrice, separati dal carattere specificato. |
| [last](control-flow-expression-language-functions.md#last) | Restituisce l'ultimo elemento di una raccolta. |
| [length](control-flow-expression-language-functions.md#length) | Restituisce il numero di elementi in una stringa o matrice. |
| [skip](control-flow-expression-language-functions.md#skip) | Rimuove gli elementi dall'inizio di una raccolta e restituisce *tutti gli altri* elementi. |
| [take](control-flow-expression-language-functions.md#take) | Restituisce gli elementi dall'inizio di una raccolta. |
| [union](control-flow-expression-language-functions.md#union) | Restituisce una raccolta che contiene *tutti* gli elementi delle raccolte specificate. | 

### <a name="logical-functions"></a>Funzioni logiche  

Queste funzioni sono utili all'interno delle condizioni e possono essere usate per valutare qualsiasi tipo di logica.  
  
| Funzione di confronto logico | Attività |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Verifica se tutte le espressioni sono true. |
| [equals](control-flow-expression-language-functions.md#equals) | Verifica se entrambi i valori sono equivalenti. |
| [greater](control-flow-expression-language-functions.md#greater) | Verifica se il primo valore è maggiore del secondo valore. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Verifica se il primo valore è maggiore o uguale al secondo valore. |
| [if](control-flow-expression-language-functions.md#if) | Verifica se un'espressione è true o false. In base al risultato, restituisce un valore specificato. |
| [less](control-flow-expression-language-functions.md#less) | Verifica se il primo valore è minore del secondo valore. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Verifica se il primo valore è minore o uguale al secondo valore. |
| [not](control-flow-expression-language-functions.md#not) | Verifica se un'espressione è false. |
| [or](control-flow-expression-language-functions.md#or) | Verifica se almeno un'espressione è true. |
  
### <a name="conversion-functions"></a>Funzioni di conversione  

 Queste funzioni vengono usate per la conversione tra ogni tipo nativo nel linguaggio:  
-   string
-   integer
-   float
-   boolean
-   matrici
-   dizionari

| Funzione di conversione | Attività |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Restituisce una matrice da un singolo input specificato. Per più input, vedere [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Restituisce la versione di una stringa con codifica base64. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Restituisce la versione binaria di una stringa con codifica base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Restituisce la versione stringa di una stringa con codifica base64. |
| [binary](control-flow-expression-language-functions.md#binary) | Restituisce la versione binaria di un valore di input. |
| [bool](control-flow-expression-language-functions.md#bool) | Restituisce la versione booleana di un valore di input. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Restituisce il primo valore non Null da uno o più parametri. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Restituisce una matrice da più input. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Restituisce l'URI dati di un valore di input. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Restituisce la versione binaria di un URI dati. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Restituisce la versione stringa di un URI dati. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Restituisce la versione stringa di una stringa con codifica base64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Restituisce la versione binaria di un URI dati. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Restituisce una stringa che sostituisce i caratteri di escape con le versioni decodificate. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Restituisce una stringa che sostituisce i caratteri non sicuri dell'URL con caratteri di escape. |
| [float](control-flow-expression-language-functions.md#float) | Restituisce un numero a virgola mobile per un valore di input. |
| [int](control-flow-expression-language-functions.md#int) | Restituisce la versione integer di una stringa. |
| [json](control-flow-expression-language-functions.md#json) | Restituisce il valore o l'oggetto di tipo JSON (JavaScript Object Notation ) per una stringa o un elemento XML. |
| [string](control-flow-expression-language-functions.md#string) | Restituisce la versione stringa di un valore di input. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Restituisce la versione codificata in formato URI per un valore di input sostituendo i caratteri non sicuri dell'URL con caratteri di escape. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Restituisce la versione binaria di una stringa con codifica URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Restituisce la versione stringa di una stringa con codifica URI. |
| [xml](control-flow-expression-language-functions.md#xml) | Restituisce la versione XML di una stringa. |
| [xpath](control-flow-expression-language-functions.md#xpath) | Verifica nel codice XML la presenza di nodi o valori che corrispondono a un'espressione XPath (XML Path Language) e restituisce i nodi o i valori corrispondenti. |

### <a name="math-functions"></a>Funzioni matematiche  
 Queste funzioni possono essere usate per qualsiasi tipo di numero, ovvero **numeri interi** e **numeri a virgola mobile**.  

| Funzione matematica | Attività |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Restituisce il risultato della somma di due numeri. |
| [div](control-flow-expression-language-functions.md#div) | Restituisce il risultato della divisione di due numeri. |
| [max](control-flow-expression-language-functions.md#max) | Restituisce il valore più alto di un set di numeri o una matrice. |
| [min](control-flow-expression-language-functions.md#min) | Restituisce il valore più basso di un set di numeri o una matrice. |
| [mod](control-flow-expression-language-functions.md#mod) | Restituisce il resto della divisione di due numeri. |
| [mul](control-flow-expression-language-functions.md#mul) | Restituisce il prodotto della moltiplicazione di due numeri. |
| [rand](control-flow-expression-language-functions.md#rand) | Restituisce un numero intero casuale da un intervallo specificato. |
| [range](control-flow-expression-language-functions.md#range) | Restituisce una matrice di valori interi che inizia da un valore intero specificato. |
| [sub](control-flow-expression-language-functions.md#sub) | Restituisce il risultato della sottrazione del secondo numero dal primo. |
  
### <a name="date-functions"></a>Funzioni di data  

| Funzione di data e ora | Attività |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Aggiunge un numero di giorni a un timestamp. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Aggiunge un numero di ore a un timestamp. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Aggiunge un numero di minuti a un timestamp. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Aggiunge un numero di secondi a un timestamp. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Aggiunge un numero di unità di tempo a un timestamp. Vedere anche [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Converte un timestamp da UTC (Universal Time Coordinated) al fuso orario di destinazione. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Converte un timestamp dal fuso orario di origine al fuso orario di destinazione. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Converte un timestamp dal fuso orario di origine a UTC (Universal Time Coordinated). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Restituisce il giorno del componente di mese di un timestamp. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Restituisce il giorno del componente settimana di un timestamp. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Restituisce il giorno del componente anno di un timestamp. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Restituisce il timestamp come stringa in formato facoltativo. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Restituisce il timestamp corrente più le unità di tempo specificate. Vedere anche [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Restituisce il timestamp corrente meno le unità di tempo specificate. Vedere anche [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Restituisce l'inizio del giorno per un timestamp. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Restituisce l'inizio dell'ora per un timestamp. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Restituisce l'inizio del mese per un timestamp. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Sottrae un numero di unità di tempo da un timestamp. Vedere anche [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | Restituisce il valore della proprietà `ticks` per un timestamp specificato. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Restituisce il timestamp corrente come stringa. |

## <a name="detailed-examples-for-practice"></a>Esempi dettagliati per le procedure

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Pipeline di copia dettagliata di Azure data factory con parametri 

Questa esercitazione relativa al passaggio di un [parametro di copia della pipeline di Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) illustra come passare i parametri tra una pipeline e un'attività, nonché tra le attività.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Mappa dettagliata della pipeline del flusso di dati con parametri 

Per un esempio completo su come usare i parametri nel flusso di dati, seguire il [mapping del flusso di dati con parametri](./parameters-data-flow.md) .

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Pipeline dettagliata basata sui metadati con parametri

Per altre informazioni su come usare i parametri per progettare pipeline basate sui metadati, seguire la [pipeline basata sui metadati con parametri](./how-to-use-trigger-parameterization.md) . Si tratta di un caso d'uso comune per i parametri.


## <a name="next-steps"></a>Passaggi successivi
Per un elenco di variabili di sistema che è possibile usare nelle espressioni, vedere [Variabili di sistema](control-flow-system-variables.md).