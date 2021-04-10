---
title: Linguaggio di query
titleSuffix: Azure Digital Twins
description: Informazioni sulle nozioni di base del linguaggio di query di Azure Digital gemelli.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: fc9cd95063f84a9af7f989af9a65ce8f99852dc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490977"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Informazioni sul linguaggio di query per i dispositivi gemelli digitali di Azure

Tenere presente che il centro dei dispositivi gemelli digitali di Azure è il [grafo gemello](concepts-twins-graph.md), costruito dai gemelli digitali e dalle relazioni. 

È possibile eseguire query su questo grafico per ottenere informazioni sui gemelli digitali e sulle relazioni in esso contenute. Queste query vengono scritte in un linguaggio di query di tipo SQL personalizzato, chiamato **linguaggio di query di Gemelli digitali di Azure**. Questa operazione è simile al [linguaggio di query dell'hub](../iot-hub/iot-hub-devguide-query-language.md) Internet con molte funzionalità confrontabili.

Questo articolo descrive le nozioni di base del linguaggio di query e delle relative funzionalità. Per esempi più dettagliati della sintassi di query e per informazioni su come eseguire le richieste di query, vedere [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).

## <a name="about-the-queries"></a>Informazioni sulle query

È possibile usare il linguaggio di query di Azure Digital gemelli per recuperare i dispositivi gemelli digitali in base ai rispettivi...
* Proprietà (incluse le [proprietà del tag](how-to-use-tags.md))
* modelli
* relationships
  - Proprietà delle relazioni

Per inviare una query al servizio da un'app client, si userà l' [**API di query**](/rest/api/digital-twins/dataplane/query)dei dispositivi digitali gemelli di Azure. Un modo per usare l'API è tramite uno degli [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) per i dispositivi gemelli digitali di Azure.

### <a name="considerations-for-querying"></a>Considerazioni sull'esecuzione di query

Quando si scrivono query per i dispositivi gemelli digitali di Azure, tenere presenti le considerazioni seguenti:
* **Ricordare** la distinzione tra maiuscole e minuscole: tutte le operazioni di query di Azure Digital Twins fanno distinzione tra maiuscole e minuscole, quindi prestare attenzione a usare i nomi esatti definiti nei modelli. Se i nomi delle proprietà non sono stati digitati in modo errato o sono stati erroneamente configurati, il set di risultati è vuoto e non vengono restituiti errori.
* **Virgolette singole di escape**: se il testo della query include un carattere virgoletta singola nei dati, l'offerta deve essere preceduta dal `\` carattere di escape. Di seguito è riportato un esempio in cui viene illustrato il valore di una proprietà di *Dani*:

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>Riferimento: espressioni e condizioni

Questa sezione descrive gli operatori e le funzioni disponibili per la scrittura di query dei dispositivi gemelli digitali di Azure. Per le query di esempio che illustrano l'uso di queste funzionalità, vedere [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).

### <a name="operators"></a>Operatori

Sono supportati gli operatori seguenti:

| Famiglia | Operatori |
| --- | --- |
| Logico |`AND`, `OR`, `NOT` |
| Confronto | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Contiene | `IN`, `NIN` |

### <a name="functions"></a>Funzioni

Sono supportate le funzioni di controllo del tipo e di cast seguenti:

| Funzione | Descrizione |
| -------- | ----------- |
| `IS_DEFINED` | Restituisce un valore booleano che indica se alla proprietà è stata assegnato un valore. Questa operazione è supportata solo quando il valore è un tipo primitivo. I tipi primitivi includono stringa, valore booleano, numerico o `null` . `DateTime`, i tipi di oggetto e le matrici non sono supportati. |
| `IS_OF_MODEL` | Restituisce un valore booleano che indica se il gemello specificato corrisponde al tipo di modello specificato |
| `IS_BOOL` | Restituisce un valore booleano che indica se il tipo di espressione specificata è un valore booleano. |
| `IS_NUMBER` | Restituisce un valore booleano che indica se il tipo di espressione specificata è un numero. |
| `IS_STRING` | Restituisce un valore booleano che indica se il tipo di espressione specificata è una stringa. |
| `IS_NULL` | Restituisce un valore booleano che indica se il tipo di espressione specificata è nulla. |
| `IS_PRIMITIVE` | Restituisce un valore booleano che indica se il tipo dell'espressione specificata è primitivo (stringa, valore booleano, numerico o `null`). |
| `IS_OBJECT` | Restituisce un valore booleano che indica se il tipo di espressione specificata è un oggetto JSON. |

Sono supportate le funzioni stringa seguenti:

| Funzione | Descrizione |
| -------- | ----------- |
| `STARTSWITH(x, y)` | Restituisce un valore booleano che indica se la prima espressione stringa inizia con il secondo. |
| `ENDSWITH(x, y)` | Restituisce un valore booleano che indica se la prima espressione stringa termina con il secondo. |

## <a name="query-limitations"></a>Limitazioni delle query

In questa sezione vengono descritte le limitazioni del linguaggio di query.

* Temporizzazione: potrebbe verificarsi un ritardo di un massimo di 10 secondi prima che le modifiche nell'istanza vengano riflesse nelle query. Se, ad esempio, si completa un'operazione come la creazione o l'eliminazione di dispositivi gemelli con l'API DigitalTwins, il risultato potrebbe non essere immediatamente riflesso nelle richieste dell'API di query. È sufficiente attendere un breve periodo di tempo per la risoluzione.
* Nessuna sottoquery supportata nell' `FROM` istruzione.
* `OUTER JOIN` la semantica non è supportata, ovvero se la relazione ha un rango pari a zero, l'intera "riga" viene eliminata dal set di risultati di output.
* La profondità di attraversamento del grafico è limitata a cinque `JOIN` livelli per ogni query.
* Le relazioni nei dispositivi gemelli digitali di Azure non possono essere sottoposte a query come entità indipendenti. è anche necessario fornire informazioni sul dispositivo gemello di origine da cui deriva la relazione. Ciò significa che esistono alcune restrizioni sull' `JOIN` operazione, che viene utilizzata per eseguire query sulle relazioni, per assicurarsi che la query dichiari i gemelli in cui inizia la query. Per esempi, vedere [*eseguire query in base*](how-to-query-graph.md#query-by-relationship) a una relazione nell'articolo *procedura: eseguire una query sul grafico a gemelli* .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come scrivere query e vedere esempi di codice client in [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).