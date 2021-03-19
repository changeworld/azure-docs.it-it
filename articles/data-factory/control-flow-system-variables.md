---
title: Variabili di sistema in Azure Data Factory
description: Questo articolo descrive le variabili di sistema supportate da Azure Data Factory. È possibile usare queste variabili nelle espressioni quando si definiscono le entità di Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: a5d2043c29db87876cc0d5ddb5b3708abad033c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591980"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variabili di sistema supportate da Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive le variabili di sistema supportate da Azure Data Factory. È possibile usare queste variabili nelle espressioni quando si definiscono le entità di Data Factory.

## <a name="pipeline-scope"></a>Ambito della pipeline

È possibile fare riferimento a queste variabili di sistema in un punto qualsiasi della pipeline JSON.

| Nome variabile | Descrizione |
| --- | --- |
| @pipeline().DataFactory |Nome del data factory in cui viene eseguita la pipeline |
| @pipeline().Pipeline |Nome della pipeline |
| @pipeline().RunId |ID dell'esecuzione della pipeline specifica |
| @pipeline().TriggerType |Tipo di trigger che ha richiamato la pipeline (ad esempio `ScheduleTrigger` , `BlobEventsTrigger` ). Per un elenco dei tipi di trigger supportati, vedere [esecuzione e trigger di pipeline in Azure Data Factory](concepts-pipeline-execution-triggers.md). Un tipo di trigger `Manual` indica che la pipeline è stata attivata manualmente. |
| @pipeline().TriggerId|ID del trigger che ha richiamato la pipeline |
| @pipeline().TriggerName|Nome del trigger che ha richiamato la pipeline |
| @pipeline().TriggerTime|Ora dell'esecuzione del trigger che ha richiamato la pipeline. Si tratta dell'ora in cui il trigger è stato **effettivamente** attivato per richiamare l'esecuzione della pipeline e potrebbe essere leggermente diverso dall'ora pianificata del trigger.  |

>[!NOTE]
>Le variabili di sistema di data e ora correlate al trigger (in entrambi gli ambiti di pipeline e trigger) restituiscono le date UTC in formato ISO 8601, ad esempio `2017-06-01T22:20:00.4061448Z` .

## <a name="schedule-trigger-scope"></a>Ambito del trigger di pianificazione

È possibile fare riferimento a queste variabili di sistema in qualsiasi punto del trigger JSON per i trigger di tipo [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger).

| Nome variabile | Descrizione |
| --- | --- |
| @trigger().scheduledTime |Ora in cui il trigger è stato pianificato per richiamare l'esecuzione della pipeline. |
| @trigger().startTime |Ora in cui il trigger è stato **effettivamente** attivato per richiamare l'esecuzione della pipeline. Questo può differire leggermente dall'ora pianificata del trigger. |

## <a name="tumbling-window-trigger-scope"></a>Ambito del trigger della finestra a cascata

È possibile fare riferimento a queste variabili di sistema in qualsiasi punto del trigger JSON per i trigger di tipo [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger).

| Nome variabile | Descrizione |
| --- | --- |
| @trigger().outputs.windowStartTime |Inizio della finestra associata all'esecuzione del trigger. |
| @trigger().outputs.windowEndTime |Fine della finestra associata all'esecuzione del trigger. |
| @trigger().scheduledTime |Ora in cui il trigger è stato pianificato per richiamare l'esecuzione della pipeline. |
| @trigger().startTime |Ora in cui il trigger è stato **effettivamente** attivato per richiamare l'esecuzione della pipeline. Questo può differire leggermente dall'ora pianificata del trigger. |

## <a name="storage-event-trigger-scope"></a>Ambito del trigger dell'evento di archiviazione

È possibile fare riferimento a queste variabili di sistema in qualsiasi punto del trigger JSON per i trigger di tipo [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

| Nome variabile | Descrizione |
| --- | --- |
| @triggerBody(). fileName  |Nome del file la cui creazione o eliminazione ha causato l'attivazione del trigger.   |
| @triggerBody(). FolderName  |Percorso della cartella che contiene il file specificato da `@triggerBody().fileName` . Il primo segmento del percorso della cartella è il nome del contenitore di archiviazione BLOB di Azure.  |
| @trigger().startTime |Ora in cui il trigger è stato attivato per richiamare l'esecuzione della pipeline. |

## <a name="custom-event-trigger-scope"></a>Ambito trigger evento personalizzato

È possibile fare riferimento a queste variabili di sistema in qualsiasi punto del trigger JSON per i trigger di tipo [CustomEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

>[!NOTE]
>Azure Data Factory prevede che l'evento personalizzato venga formattato con lo [schema di eventi di griglia di eventi di Azure](../event-grid/event-schema.md).

| Nome variabile | Descrizione
| --- | --- |
| @triggerBody(). event. eventType | Tipo di eventi che hanno attivato l'esecuzione del trigger di evento personalizzato. Il tipo di evento è il campo definito dal cliente e accetta tutti i valori di tipo stringa. |
| @triggerBody(). event. Subject | Oggetto dell'evento personalizzato che ha provocato l'attivazione del trigger. |
| @triggerBody(). event. Data. _nome_ della pagina | Il campo dati in un evento personalizzato è un BLOB JSON gratuito, che può essere usato dal cliente per inviare messaggi e dati. Usare i dati. _nome_ della pagina per fare riferimento a ogni campo. Ad esempio, @triggerBody (). event. Data. callback restituisce il valore per il campo di _callback_ archiviato in _Data_. |
| @trigger().startTime | Ora in cui il trigger è stato attivato per richiamare l'esecuzione della pipeline. |

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'uso di queste variabili nelle espressioni, vedere [Expression language & functions](control-flow-expression-language-functions.md) (Linguaggio e funzioni delle espressioni).
* Per usare le variabili di sistema dell'ambito del trigger nella pipeline, vedere [metadati del trigger di riferimento nella pipeline](how-to-use-trigger-parameterization.md)
