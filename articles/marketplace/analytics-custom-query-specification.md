---
title: Specifica di query personalizzata
description: Informazioni su come usare le query personalizzate per estrarre a livello di codice i dati dalle tabelle di analisi per le offerte nel Marketplace commerciale Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4be063342a6c46d73c86f2d9dff1da5395328389
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584022"
---
# <a name="custom-query-specification"></a>Specifica di query personalizzata

I partner possono usare questa specifica di query per formulare facilmente query personalizzate per l'estrazione di dati dalle tabelle di analisi. È possibile usare le query per selezionare solo le colonne e le metriche desiderate corrispondenti a un determinato criterio. Il nucleo della specifica del linguaggio è la definizione del set di dati in cui è possibile scrivere una query personalizzata.

## <a name="datasets"></a>Set di dati

In modo analogo alle query eseguite su un database con tabelle e colonne, una query personalizzata funziona sui set di dati con colonne e metriche. L'elenco completo dei set di impostazioni disponibili per la formulazione di una query può essere ottenuto tramite l'API set di impostazioni.

Di seguito è riportato un esempio di un set di dati visualizzato come JSON.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Parti di un set di dati

- Il nome di un set di dati è simile a quello di una tabella di database. Ad esempio, ISVUsage. Un set di dati include un elenco di colonne che è possibile selezionare, ad esempio MarketplaceSubscriptionId.
- Un set di dati include anche metriche, che sono come funzioni di aggregazione in un database. Ad esempio, NormalizedUsage.
- Sono disponibili intervalli di tempo fissi sui quali è possibile esportare i dati.

### <a name="formulating-a-query-on-a-dataset"></a>Formulazione di una query su un set di dati

Di seguito sono riportate alcune query di esempio che illustrano come estrarre diversi tipi di dati.

| Query | Descrizione |
| ------------ | ------------- |
| **Seleziona** MarketplaceSubscriptionId, CustomerId **da** ISVUsage **TIMESPAN LAST_MONTH** | Questa query otterrà ogni univoco `MarketplaceSubscriptionId` e il corrispondente `CustomerId` nell'ultimo mese. |
| **Seleziona** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **da** ISVUSAGE **Order by** EstimatedExtendedChargeCC **limit** 10 | Questa query otterrà le prime 10 sottoscrizioni in ordine decrescente del numero di licenze vendute in ogni sottoscrizione. |
| **Seleziona** CustomerId, NormalizedUsage, RawUsage **da** ISVUSAGE **Order by** NormalizedUsage **dove** NormalizedUsage > 100000 **Order by** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | Questa query otterrà i NormalizedUsage e RawUsage di tutti i clienti con NormalizedUsage maggiore di 100.000. |
| **Seleziona** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **da** ISVUsage **dove** CustomerID in (' 2a31c234-1F4E-4c60-909E-76d234f93161',' 80780748-3f9a-11EB-b378-0242ac130002') | Questa query otterrà `MarketplaceSubscriptionId` e i ricavi generati per ogni mese dai due `CustomerId` valori: `2a31c234-1f4e-4c60-909e-76d234f93161` e `80780748-3f9a-11eb-b378-0242ac130002` . |
|||

## <a name="query-specification"></a>Specifica di query

In questa sezione vengono descritte la definizione della query e la struttura.

### <a name="grammar-reference"></a>Riferimento alla grammatica

Questa tabella descrive i simboli usati nelle query.

| Simbolo | Significato |
| ------------ | ------------- |
| ? | Facoltativo |
| * | Zero o più |
| + | Uno o più |
| &#124; | O/uno degli elenchi |
| | |

### <a name="query-definition"></a>Definizione query

L'istruzione di query include le clausole seguenti: SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause? e TimeSpan?.

- **SelectClause**: **Select** ColumOrMetricName (, ColumOrMetricName) *
    - **ColumOrMetricName**: colonne e metriche definite nel set di dati
- **FromClause**: **da** DataSetName
    - **DataSetName**: nome del set di dati definito nel set di dati
- **WhereClause**: **dove** FilterCondition (**e** FilterCondition) *
    - Valore dell'operatore **FilterCondition**: ColumOrMetricName
        - **Operatore**: = | > | < | >= | <= |! = | LIKE | NON LIKE | IN | NON IN
        - **Valore**: numero | StringLiteral | Multinumero | Multistringa 
            - **Numero**:-? [0-9] + (. [0-9] [0-9] *)?
            - **StringLiteral**:' [a-Za-z0-9_] *'
            - **Multinumero**: (numero (, numero) *)
            - **Multistringa**: (StringLiteral (, StringLiteral) *)
- **OrderClause**: **ORDER BY** OrderCondition (, OrderCondition) *
    - **OrderCondition**: ColumOrMetricName (**ASC**  |  **desc**) *
    - **LimitClause**: **limite** [0-9] +
    - **TimeSpan**: **TimeSpan** (Today | IERI | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | VITA

### <a name="query-structure"></a>Struttura della query

Una query di report è costituita da più parti:

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- TIMESPAN

Ogni parte è descritta di seguito.

#### <a name="select"></a>SELECT

Questa parte della query specifica le colonne che vengono esportate. Le colonne che è possibile selezionare sono i campi elencati nelle `selectableColumns` `availableMetrics` sezioni e di un set di dati. Le righe esportate finale conterranno sempre valori distinti nelle colonne selezionate. Non saranno ad esempio presenti righe duplicate nel file esportato. Le metriche verranno calcolate per ogni combinazione distinta delle colonne selezionate.

**Esempio**:
- **Selezionare** `OfferName` , `NormalizedUsage`

#### <a name="from"></a>FROM

Questa parte della query indica il set di dati da cui devono essere esportati i dati. Il nome del set di dati specificato qui deve essere un nome di set di dati valido restituito dall'API set di dati.

**Esempio**:
- FROM `ISVUsage`
- FROM `ISVOrder`

#### <a name="where"></a>WHERE

Questa parte della query viene usata per specificare le condizioni di filtro nel set di dati. Solo le righe che corrispondono a tutte le condizioni elencate in questa clausola saranno presenti nel file finale esportato. La condizione di filtro può trovarsi in qualsiasi colonna elencata in `selectableColumns` e `availableMetrics` . I valori specificati nella condizione di filtro possono essere un elenco di numeri o un elenco di stringhe solo se l'operatore è `IN` o `NOT IN` . I valori possono essere sempre specificati come stringa letterale e verranno convertiti in tipi nativi di colonne. È necessario separare più condizioni di filtro con un' `AND` operazione.

**Esempio**:

- MarketplaceSubscriptionId =' 868368da-957d-4959-8992-3c12dc7e6260'
- CustomerName, ad **esempio** '% Contosso%'
- CustomerId **non in** (1000, 1001, 1002)
- OrderQuantity = 100
- OrderQuantity =' 100'
    - MarketplaceSubscriptionId =' 7b487ac0-CE12-B732-dcd6-91a1e4e74a50' e CustomerId =' 0f8b7fa0-eb83-A183-1225-ca153ef807aa '

#### <a name="order-by"></a>ORDER BY

Questa parte della query specifica i criteri di ordinamento per le righe esportate. Le colonne in cui è possibile definire l'ordinamento devono essere da `selectableColumns` e `availableMetrics` del set di dati. Se non è stata specificata alcuna direzione per l'ordinamento, verrà impostata per impostazione predefinita `DESC` sulla colonna. L'ordinamento può essere definito su più colonne separando i criteri con una virgola.

**Esempio**:

- **Ordina per** NormalizedUsage **ASC**, ESTIMATEDEXTENDEDCHARGE (CC) **desc**
- **Ordina per** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

Questa parte della query specifica il numero di righe che verranno esportate. Il numero specificato deve essere un numero intero positivo diverso da zero.

#### <a name="timespan"></a>TIMESPAN

Questa parte della query specifica la durata del periodo di tempo in cui i dati devono essere esportati. I valori possibili devono provenire dal `availableDateRanges` campo nella definizione del set di dati.

### <a name="case-sensitivity-in-query-specification"></a>Distinzione maiuscole/minuscole nella specifica di query

La specifica non fa distinzione tra maiuscole e minuscole. Le parole chiave predefinite, i nomi e i valori delle colonne possono essere specificati utilizzando lettere maiuscole o minuscole.

## <a name="see-also"></a>Vedi anche

- [Elenco di query di sistema](analytics-system-queries.md)
