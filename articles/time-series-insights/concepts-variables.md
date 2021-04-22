---
title: Variabili del modello - Azure Time Series Insights Gen2 | Microsoft Docs
description: Variabili del modello
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: 01184a4eb2aac81bbcabcebf89ef10afeabddbe8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872966"
---
# <a name="time-series-model-variables"></a>Variabili del modello Time Series

Questo articolo descrive le variabili del modello Time Series che specificano la formula e le regole di calcolo per gli eventi.

Ogni variabile può essere di tre tipi: *numeric,* *categorical* e *aggregate.*

* **I tipi** numerici funzionano con valori numerici continui.
* **I tipi categorici** funzionano con un set definito di valori discreti.
* **I** tipi di aggregazione combinano più variabili di un singolo tipo (tutte numeriche o tutte categoriche).

Nella tabella seguente vengono visualizzate le proprietà rilevanti per ogni tipo di variabile.

[![Tabella delle variabili del modello Time Series](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>Variabili numeriche

| Proprietà variable | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe considerate per il calcolo. |
| Valore di variabile | Valori di telemetria usati per il calcolo provenienti dal dispositivo o dai sensori o trasformati usando espressioni time series. Le variabili di tipo numerico devono essere `Double` o per corrispondere al tipo di dati dei dati in `Long` ingresso.|
| Interpolazione di variabili | L'interpolazione specifica come ricostruire un segnale usando i dati esistenti. *Le opzioni* Passaggio *e* Interpolazione lineare sono disponibili per le variabili numeriche. |
| Aggregazione di variabile | Eseguire calcoli tramite le funzioni di [aggregazione supportate per i tipi di variabile numerici](/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind). |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>Variabili categoriche

| Proprietà Variable | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe considerate per il calcolo. |
| Valore di variabile | Valori di telemetria usati per il calcolo provenienti dal dispositivo o dai sensori. Le variabili di tipo categorico devono essere o per corrispondere al tipo `Long` di dati dei dati in `String` ingresso. |
| Interpolazione di variabili | L'interpolazione specifica come ricostruire un segnale usando i dati esistenti. *L'opzione* Interpolazione passaggio è disponibile per le variabili categoriche. |
| Categorie di variabili | Le categorie creano un mapping tra i valori provenienti dal dispositivo o dai sensori a un'etichetta. |
| Categoria predefinita variabile | La categoria predefinita è per tutti i valori di cui non viene eseguito il mapping nella proprietà "categories". |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>Variabili di aggregazione

| Proprietà Variable | Descrizione |
| --- | ---|
| Filtro di variabile | I filtri sono clausole condizionali facoltative per limitare il numero di righe considerate per il calcolo. |
| Aggregazione di variabile | Eseguire calcoli tramite le funzioni di [aggregazione supportate per i tipi di variabili aggregate](/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind). |

Le variabili sono conformi all'esempio JSON seguente:

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

Le variabili vengono archiviate nella definizione del tipo di un modello time series e possono essere fornite inline tramite API per eseguire l'override o completare la definizione archiviata.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [modello Time Series.](./concepts-model-overview.md)

* Altre informazioni su come definire le variabili inline usando [le API di query](./concepts-query-overview.md).
