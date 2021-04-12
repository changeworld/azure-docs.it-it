---
title: Sintassi della query di ricerca di grafi
titleSuffix: Azure Machine Learning
description: Informazioni su come usare la sintassi di query di ricerca in progettazione Azure Machine Learning per cercare nodi in Graph pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259226"
---
# <a name="graph-search-query-syntax"></a>Sintassi della query di ricerca di grafi

In questo articolo vengono fornite informazioni sulla funzionalità di ricerca Graph in Azure Machine Learning. 

La ricerca Graph consente di spostarsi rapidamente in un nodo durante il debug o la creazione di una pipeline. È possibile digitare la parola chiave o la query nella casella di input della barra degli strumenti o nella scheda Cerca nel pannello sinistro per attivare la ricerca. Tutti i risultati corrispondenti verranno evidenziati in giallo nell'area di disegno e, se si seleziona un risultato nel riquadro sinistro, il nodo nell'area di disegno verrà evidenziato in rosso.

![Screenshot che illustra l'esperienza di ricerca di un grafo di esempio](media/search/graph-search-0322.png)

La ricerca Graph supporta la ricerca di parole chiave full-text sul nome e sui commenti del nodo. È anche possibile filtrare in una proprietà del nodo, ad esempio runStatus, Duration, computeTarget. La ricerca di parole chiave è basata sulla query Lucene. Una query di ricerca completa ha un aspetto simile al seguente:  

**[[query Lucene] | [query di filtro]]** 

È possibile usare una query Lucene o un filtro. Per usare entrambi, usare il **|** separatore. La sintassi della query di filtro è più restrittiva della query Lucene. Quindi, se l'input del cliente può essere analizzato come entrambi, viene applicata la query del filtro.

Ad esempio, per `data OR model | compute in {cpucluster}` cercare i nodi in cui il nome o il commento contiene `data` o `model` e COMPUTE è cpucluster.
 

## <a name="lucene-query"></a>Query Lucene

La ricerca Graph usa la query semplice Lucene come sintassi della ricerca full-text nel nodo "Name" e "comment". Sono supportati gli operatori Lucene seguenti:

 
- AND/OR
- Corrispondenza con caratteri jolly con **?** **\*** operatori e.

### <a name="examples"></a>Esempio

- Ricerca semplice: `JSON Data`

- E/O: `JSON AND Validation`

- Più e/o: `(JSON AND Validation) OR (TSV AND Training)`

 
- Corrispondenza con caratteri jolly: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Non è possibile avviare una query Lucene con un carattere "*".

##  <a name="filter-query"></a>Filtra query

 
Le query di filtro usano il modello seguente:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
È possibile utilizzare le seguenti proprietà del nodo come chiavi:

- runStatus
- compute
- duration
- riutilizzare
- Pubblica
- tags

E usare gli operatori seguenti:

- Maggiore o uguale a: `>=`
- Minore o uguale a: `<=`
- Maggiore `>`
- Meno `<`
- Uguale `==`
- Contenere `=`
- NotEqual `!=`
- In `in`

 
 

### <a name="example"></a>Esempio

- durata > 100;
- stato in {failed, NotStarted}
- calcolo in {GPU-cluster}; runStatus in {Completed}

## <a name="technical-notes"></a>Note tecniche

- La relazione tra più filtri è "e"
- Se `>=,  >,  <, or  <=` si sceglie, i valori verranno convertiti automaticamente in tipo di numero. In caso contrario, vengono utilizzati i tipi stringa per il confronto.
- Per tutti i valori di tipo stringa, case è insensitive nel confronto.
- L'operatore "in" prevede una raccolta come valore. la sintassi della raccolta è `{name1, name2, name3}`
- Lo spazio verrà ignorato tra le parole chiave