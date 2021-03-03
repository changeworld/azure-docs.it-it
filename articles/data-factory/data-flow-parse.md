---
title: Trasformazione analizza dati nel flusso di dati di mapping
description: Analizza documenti colonna incorporata
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710193"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Analizza trasformazione nel flusso di dati del mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione analizza per analizzare le colonne nei dati presenti nel form del documento. I tipi supportati correnti di documenti incorporati che possono essere analizzati sono JSON e testo delimitato.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Configurazione

Nel pannello analizza trasformazione configurazione è necessario innanzitutto selezionare il tipo di dati contenuti nelle colonne che si desidera analizzare inline. La trasformazione analisi contiene anche le impostazioni di configurazione seguenti.

![Impostazioni di analisi](media/data-flow/data-flow-parse-1.png "Analizza")

### <a name="column"></a>Colonna

In modo analogo alle colonne e alle aggregazioni derivate, è possibile modificare una colonna in uscita selezionandola dal selettore a discesa. In alternativa, è possibile digitare il nome di una nuova colonna qui. In ADF i dati di origine analizzati vengono archiviati in questa colonna.

### <a name="expression"></a>Expression

Utilizzare il generatore di espressioni per impostare l'origine per l'analisi. Questa operazione può essere semplice quanto semplicemente selezionare la colonna di origine con i dati autonomi che si desidera analizzare oppure è possibile creare espressioni complesse da analizzare.

### <a name="output-column-type"></a>Tipo di colonna di output

Qui è possibile configurare lo schema di output di destinazione dall'analisi che verrà scritta in un'unica colonna.

![Esempio di analisi](media/data-flow/data-flow-parse-2.png "Esempio di analisi")

In questo esempio è stata definita l'analisi del campo in ingresso "jsonString", che è testo normale, ma formattato come struttura JSON. I risultati analizzati verranno archiviati in formato JSON in una nuova colonna denominata "JSON" con questo schema:

```(trade as boolean, customers as string[])```

Vedere la scheda controlla e l'anteprima dati per verificare che l'output sia mappato correttamente.

## <a name="examples"></a>Esempio

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

### <a name="examples"></a>Esempio

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Passaggi successivi

* Utilizzare la [trasformazione Flat](data-flow-flatten.md) per trasformare le righe in colonne.
* Utilizzare la [trasformazione colonna derivata](data-flow-derived-column.md) per trasformare colonne tramite pivot in righe.
