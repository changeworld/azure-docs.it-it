---
title: Modifiche imminenti alle regole di inserimento e Flat in Azure Time Series Insights Gen2 | Microsoft Docs
description: Modifiche alle regole di inserimento
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 44553e2a57f796dcf48561ff3b273e25514247ae
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504533"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Modifiche imminenti alle regole di escape e Flat JSON per i nuovi ambienti

> [!IMPORTANT]
> Queste modifiche verranno applicate solo agli ambienti *appena creati* Microsoft Azure Time Series Insights Gen2. Le modifiche non si applicano agli ambienti Gen1.

Il Azure Time Series Insights ambiente Gen2 crea dinamicamente le colonne di archiviazione, seguendo un particolare set di convenzioni di denominazione. Quando si inserisce un evento, Time Series Insights applica un set di regole al payload JSON e ai nomi di proprietà. Le modifiche apportate al modo in cui i dati JSON sono bidimensionali e archiviati diventano effettivi per i nuovi Azure Time Series Insights ambienti Gen2 nel 2020 luglio. Questa modifica ha un effetto sui casi seguenti:

* Il payload JSON contiene oggetti annidati.
* Il payload JSON contiene matrici.
* Usare uno dei quattro caratteri speciali seguenti in un nome di proprietà JSON: `[` `\` `.``'`
* Una o più proprietà dell'ID Time Series (TS) sono incluse in un oggetto annidato.

Se si crea un nuovo ambiente e uno o più di questi casi si applicano al payload dell'evento, i dati verranno resi bidimensionali e archiviati in modo diverso. Nella tabella seguente sono riepilogate le modifiche:

| Regola corrente | Nuova regola | JSON di esempio | Nome precedente della colonna | Nome nuova colonna
|---|---| ---| ---|  ---|
| Il codice JSON annidato è bidimensionale usando un carattere di sottolineatura come delimitatore. |Il codice JSON annidato è bidimensionale usando un punto come delimitatore.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| I caratteri speciali non sono preceduti da caratteri di escape. | I nomi delle proprietà JSON che includono i caratteri speciali `.` `[`   `\` e sono preceduti da un carattere di `'` escape con `['` e `']` . In `['` e `']` è presente un ulteriore escape di virgolette singole e barre rovesciate. Una virgoletta singola verrà scritta come `\'` e una barra rovesciata come `\\` .  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Le matrici di primitive vengono archiviate come una stringa. | Le matrici di tipi primitivi vengono archiviate come tipo dinamico.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Le matrici di oggetti sono sempre bidimensionali e producono più eventi. | Se gli oggetti all'interno di una matrice non dispongono delle proprietà ID TS o timestamp, la matrice di oggetti viene archiviata come un tipo dinamico. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Modifiche consigliate per i nuovi ambienti

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Se l'ID TS e/o la proprietà timestamp sono annidati all'interno di un oggetto

Tutte le nuove distribuzioni devono corrispondere alle nuove regole di inserimento. Se, ad esempio, l'ID TS è `telemetry_tagId` , è necessario aggiornare i modelli di Azure Resource Manager o gli script di distribuzione automatica per configurare `telemetry.tagId` come ID TS dell'ambiente. Questa modifica è necessaria anche per i timestamp dell'origine evento in JSON annidato.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Se il payload contiene codice JSON annidato o caratteri speciali e si automatizza la creazione di espressioni variabili di [modello Time Series](./concepts-model-overview.md)

Aggiornare il codice client che esegue [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) in modo che corrisponda alle nuove regole di inserimento. Ad esempio, è necessario aggiornare un' [espressione Time Series](/rest/api/time-series-insights/reference-time-series-expression-syntax) precedente di `"value": {"tsx": "$event.series_value.Double"}` a una delle opzioni seguenti:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [Azure Time Series Insights archiviazione e ingresso Gen2](./concepts-ingestion-overview.md).

* Informazioni su come eseguire query sui dati usando le [API di query di serie temporali](./concepts-query-overview.md).

* Scopri di più sulla [nuova sintassi delle espressioni Time Series](/rest/api/time-series-insights/reference-time-series-expression-syntax).