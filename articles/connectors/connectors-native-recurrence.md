---
title: Pianificare attività e flussi di lavoro ricorrenti
description: Pianificare ed eseguire attività e flussi di lavoro automatizzati ricorrenti con il trigger di ricorrenza nelle app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 3749a7080bf17c020b48ae3ebc3cff3aa998eeef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382294"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger di ricorrenza nelle app per la logica di Azure

Per eseguire regolarmente attività, processi o processi in base a una pianificazione specifica, è possibile avviare il flusso di lavoro dell'app per la logica con il trigger di **ricorrenza** incorporato, che viene eseguito in modo nativo in app per la logica di Azure. È possibile impostare una data e un'ora, nonché un fuso orario per l'avvio del flusso di lavoro e una ricorrenza per la ripetizione del flusso di lavoro. Se il trigger non riceve ricorrenze per qualsiasi motivo, ad esempio a causa di rotture o flussi di lavoro disabilitati, questo trigger non elabora le ricorrenze perse ma riavvia le ricorrenze al successivo intervallo pianificato. Per altre informazioni sui trigger di pianificazione e sulle azioni predefinite, vedere [pianificare ed eseguire attività ricorrenti automatizzate, attività e flussi di lavoro con app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Ecco alcuni modelli supportati da questo trigger insieme a ricorrenze più avanzate e pianificazioni complesse:

* Esecuzione immediata e ripetizione ogni *n* secondi, minuti, ore, giorni, settimane o mesi.

* Iniziare da una data e un'ora specifiche, quindi eseguire e ripetere ogni *n* secondi, minuti, ore, giorni, settimane o mesi.

* Esecuzione e ripetizione a uno o più orari ogni giorno, ad esempio alle 8:00 e alle 17:00.

* Esecuzione e ripetizione ogni settimana, ma solo in giorni specifici, ad esempio sabato e domenica.

* Esecuzione e ripetizione ogni settimana, ma solo in ore e giorni specifici, ad esempio dal lunedì al venerdì alle 8:00 e alle 17:00.

Per le differenze tra questo trigger e il trigger della finestra temporale scorrevole o per altre informazioni sulla pianificazione di flussi di lavoro ricorrenti, vedere [pianificare ed eseguire attività, processi e flussi di lavoro automatizzati ricorrenti con app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se si vuole attivare l'app per la logica ed eseguire solo una volta in futuro, vedere [eseguire processi solo una volta](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Conoscenze di base di [app per la logica](../logic-apps/logic-apps-overview.md). Se non si ha familiarità con le app per la logica, vedere [come creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Aggiungere il trigger Ricorrenza

1. Accedere al [portale di Azure](https://portal.azure.com). Creare un'app per la logica vuota.

1. Dopo aver visualizzato Progettazione app per la logica, nella casella di ricerca immettere `recurrence` come filtro. Dall'elenco trigger selezionare questo trigger come primo passaggio nel flusso di lavoro dell'app per la logica: **ricorrenza**

   ![Selezionare il trigger "ricorrenza"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Impostare l'intervallo e la frequenza per la ricorrenza. In questo esempio impostare queste proprietà per eseguire il flusso di lavoro ogni settimana.

   ![Impostare intervallo e frequenza](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Proprietà | Nome JSON | Obbligatoria | Tipo | Descrizione |
   |----------|-----------|----------|------|-------------|
   | **Interval** | `interval` | Sì | Integer | Numero intero positivo che indica l'intervallo con cui viene eseguito il flusso di lavoro in base alla frequenza. Ecco gli intervalli minimo e massimo: <p>- Mese: 1-16 mesi <br>-Settimana: 1-71 settimane <br>- Giorno: 1-500 giorni <br>- Ora: 1-12.000 ore <br>- Minuto: 1-72.000 minuti <br>- Secondo: 1-9.999.999 secondi<p>Ad esempio, se l'intervallo è 6 e la frequenza è "Mese", la ricorrenza è ogni 6 mesi. |
   | **Frequenza** | `frequency` | Sì | string | L'unità di tempo per la ricorrenza: **Secondo**, **Minuto**, **Ora**, **Giorno**, **Settimana** o **Mese** |
   ||||||

   > [!IMPORTANT]
   > Se una ricorrenza non specifica una [data e un'ora di inizio](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)specifiche, la prima ricorrenza viene eseguita immediatamente quando si salva o si distribuisce l'app per la logica, nonostante l'installazione della ricorrenza del trigger. Per evitare questo comportamento, specificare una data e un'ora di inizio per il momento in cui si desidera che venga eseguita la prima ricorrenza.
   >
   > Se una ricorrenza non specifica altre opzioni di pianificazione avanzate, ad esempio orari specifici per l'esecuzione di ricorrenze future, tali ricorrenze si basano sull'ultima esecuzione. Di conseguenza, le ore di inizio per tali ricorrenze potrebbero andare alla deriva a causa di fattori quali la latenza durante le chiamate di archiviazione. 
   > Per assicurarsi che l'app per la logica non perda una ricorrenza, soprattutto quando la frequenza è espressa in giorni o più, provare le opzioni seguenti:
   > 
   > * Specificare una data e un'ora di inizio per la ricorrenza più le ore specifiche in cui eseguire le ricorrenze successive usando le proprietà denominate **in queste ore** e **in questi minuti**, che sono disponibili solo per le frequenze di **giorno** e **settimana** .
   > 
   > * Utilizzare il [trigger finestra temporale scorrevole](../connectors/connectors-native-sliding-window.md)anziché il trigger ricorrenza.

1. Per impostare le opzioni di pianificazione avanzate, aprire l'elenco **Aggiungi nuovo parametro** . Tutte le opzioni selezionate vengono visualizzate nel trigger dopo la selezione.

   ![Opzioni di pianificazione avanzata](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Proprietà | Nome JSON | Obbligatoria | Tipo | Descrizione |
   |----------|-----------|----------|------|-------------|
   | **Fuso orario** | `timeZone` | No | string | Valido solo quando si specifica un'ora di inizio, perché il trigger non accetta la [differenza dall'ora UTC](https://en.wikipedia.org/wiki/UTC_offset). Selezionare il fuso orario che si desidera applicare. |
   | **Ora di inizio** | `startTime` | No | string | Specificare una data e un'ora di inizio, con un massimo di 49 anni nel futuro e rispettare la specifica di data e ora [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) nel [formato di data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)e ora UTC, ma senza [offset UTC](https://en.wikipedia.org/wiki/UTC_offset): <p><p>AAAA-MM-GGThh:mm:ss se si seleziona un fuso orario <p>-oppure- <p>AAAA-MM-GGThh:mm:ssZ se non si seleziona un fuso orario <p>Quindi, ad esempio, se si desidera il 18 settembre 2020 alle 2:00 PM, specificare "2020-09-18T14:00:00" e selezionare un fuso orario, ad esempio ora solare Pacifico. In alternativa, specificare "2020-09-18T14:00:00Z" senza un fuso orario. <p><p>**Importante:** Se non si seleziona un fuso orario, è necessario aggiungere la lettera "Z" alla fine senza spazi. La lettera "Z" fa riferimento all'[ora nautica](https://en.wikipedia.org/wiki/Nautical_time) equivalente. Se si seleziona un valore di fuso orario, non è necessario aggiungere una "Z" alla fine del valore dell' **ora di inizio** . In caso contrario, app per la logica ignora il valore del fuso orario perché "Z" indica un formato di ora UTC. <p><p>Per le pianificazioni semplici, l'ora di inizio è la prima occorrenza, mentre per le pianificazioni complesse il trigger non viene attivato prima dell'ora di inizio. [*In quali modi posso usare la data e l'ora di inizio?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **In questi giorni** | `weekDays` | No | Stringa o matrice di stringhe | Se si seleziona "Settimana", è possibile selezionare uno o più giorni in cui eseguire il flusso di lavoro: **Lunedì**, **Martedì**, **Mercoledì**, **Giovedì**, **Venerdì**, **Sabato** e **Domenica** |
   | **A queste ore** | `hours` | No | Intero o matrice di intero | Se si seleziona "giorno" o "settimana", è possibile selezionare uno o più numeri interi da 0 a 23 come ore del giorno in cui si desidera eseguire il flusso di lavoro. <p><p>Se ad esempio si specifica "10", "12" e "14", si ottengono le 10:00, le 12.00 e le 14.00 per le ore del giorno, ma i minuti del giorno vengono calcolati in base all'inizio della ricorrenza. Per impostare minuti specifici del giorno, ad esempio 10:00 AM, 12:00 PM e 2:00 PM, specificare tali valori utilizzando la proprietà denominata **in questi minuti**. |
   | **A questi minuti** | `minutes` | No | Intero o matrice di intero | Se si seleziona "Giorno" o "Settimana", è possibile selezionare uno o più numeri interi da 0 a 59 come minuti dell'ora in cui si desidera eseguire il flusso di lavoro. <p>Ad esempio, è possibile specificare "30" come indicatore dei minuti e, usando l'esempio precedente per le ore del giorno, si otterranno le ore 10.30, 12.30 e 14.30. <p>**Nota**: a volte, il timestamp per l'esecuzione attivata potrebbe variare fino a 1 minuto dall'ora pianificata. Se è necessario passare il timestamp esattamente come pianificato per le azioni successive, è possibile usare le espressioni modello per modificare il timestamp di conseguenza. Per altre informazioni, vedere [funzioni di data e ora per le espressioni](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions). |
   |||||

   Si supponga, ad esempio, che oggi sia venerdì 4 settembre 2020. Il trigger di ricorrenza seguente non viene attivato *prima* della data e dell'ora di inizio, ovvero venerdì 18 settembre 2020 alle 8:00 PST. Tuttavia, la pianificazione di ricorrenza è impostata solo per i lunedì alle 10:30, 12:30 e 14:30. La prima volta che il trigger viene attivato e crea un'istanza del flusso di lavoro dell'app per la logica, il lunedì alle 10:30 AM. Per altre informazioni sul funzionamento degli orari di inizio, vedere questi [esempi](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Le esecuzioni future avranno luogo alle 12:30 e 14:30 nella stessa giornata. Ogni ricorrenza crea la propria istanza del flusso di lavoro. L'intera pianificazione verrà quindi ripetuta da capo il lunedì successivo. [*Quali sono altre occorrenze di esempio?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Esempio di pianificazione avanzata](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > Il trigger mostra un'anteprima della ricorrenza specificata solo quando si seleziona "Giorno" o "Settimana" come frequenza.

1. A questo punto, compilare il flusso di lavoro rimanente con altre azioni. Per altre azioni che è possibile aggiungere, vedere [connettori per app per la logica di Azure](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definizione del flusso di lavoro-ricorrenza

Nella definizione del flusso di lavoro sottostante dell'app per la logica, che usa JSON, è possibile visualizzare la [definizione del trigger di ricorrenza](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) con le opzioni selezionate. Per visualizzare questa definizione, scegliere **visualizzazione codice** sulla barra degli strumenti della finestra di progettazione. Per tornare alla finestra di progettazione, scegliere sulla barra degli strumenti della finestra di progettazione, **finestra** di progettazione.

Questo esempio illustra come una definizione di trigger di ricorrenza può apparire in una definizione del flusso di lavoro sottostante:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

<a name="daylight-saving-standard-time"></a>

## <a name="trigger-recurrence-shift-between-daylight-saving-time-and-standard-time"></a>Spostamento di ricorrenza del trigger tra l'ora legale e l'ora solare

I trigger predefiniti ricorrenti rispettano la pianificazione impostata, incluso il fuso orario specificato. Se non si seleziona un fuso orario, l'ora legale (DST) può influire sul momento dell'esecuzione dei trigger, ad esempio, spostando l'ora di inizio un'ora in avanti quando l'ora legale viene avviata e un'ora indietro al termine dell'ora legale.

Per evitare questo spostamento in modo che l'app per la logica venga eseguita all'ora di inizio specificata, assicurarsi di selezionare un fuso orario. In questo modo, l'ora UTC per l'app per la logica viene spostata anche per contrastare il cambiamento di tempo stagionale. Tuttavia, in alcuni casi è possibile che si verifichino problemi quando il tempo si sposta. Per ulteriori informazioni ed esempi, vedere [ricorrenza per l'ora legale e l'ora solare](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

## <a name="next-steps"></a>Passaggi successivi

* [Sospendere i flussi di lavoro con azioni di ritardo](../connectors/connectors-native-delay.md)
* [Connettori per App per la logica](../connectors/apis-list.md)
