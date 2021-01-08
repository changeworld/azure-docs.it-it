---
title: Pianificazione di attività e flussi di lavoro ricorrenti in app per la logica di Azure
description: Panoramica sulla pianificazione di attività, processi e flussi di lavoro automatizzati ricorrenti con le app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: fd0a779ec5ac5537dd3e3ed6a82cf818b42cff15
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018793"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Pianificare ed eseguire attività, processi e flussi di lavoro automatizzati ricorrenti con le app per la logica di Azure

App per la logica consente di creare ed eseguire attività e processi ricorrenti automatizzati in base a una pianificazione. Creando un flusso di lavoro dell'app per la logica che inizia con un trigger di ricorrenza incorporato o un trigger di finestra temporale scorrevole, ovvero trigger di tipo pianificazione, è possibile eseguire immediatamente le attività, in un secondo momento o in base a un intervallo ricorrente. È possibile chiamare i servizi all'interno e all'esterno di Azure, ad esempio endpoint HTTP o HTTPS, inviare messaggi ai servizi di Azure, ad esempio archiviazione di Azure e il bus di servizio di Azure, oppure recuperare i file caricati in una condivisione file. Con il trigger di ricorrenza, è inoltre possibile impostare pianificazioni complesse e ricorrenze avanzate per l'esecuzione di attività. Per altre informazioni sui trigger di pianificazione e sulle azioni predefinite, vedere [pianificare i trigger](#schedule-triggers) e [pianificare le azioni](#schedule-actions). 

> [!TIP]
> È possibile pianificare ed eseguire carichi di lavoro ricorrenti senza creare un'app per la logica separata per ogni processo pianificato e in base al limite dei flussi di lavoro [per area e sottoscrizione](../logic-apps/logic-apps-limits-and-config.md#definition-limits). È invece possibile usare il modello di app per la logica creato dal [modello di avvio rapido di Azure: utilità di pianificazione dei processi delle app](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/)per la logica.
>
> Il modello di pianificazione dei processi delle app per la logica crea un'app per la logica CreateTimerJob che chiama un'app per la logica TimerJob. È quindi possibile chiamare l'app per la logica CreateTimerJob come API effettuando una richiesta HTTP e passando una pianificazione come input per la richiesta. Ogni chiamata all'app per la logica CreateTimerJob chiama anche l'app per la logica TimerJob, che crea una nuova istanza di TimerJob che viene eseguita in modo continuo in base alla pianificazione specificata o fino al raggiungimento di un limite specificato. In questo modo, è possibile eseguire tutte le istanze di TimerJob desiderate senza doversi preoccupare dei limiti del flusso di lavoro, perché le istanze non sono singole definizioni del flusso di lavoro delle app per la logica

Questo elenco illustra alcune attività di esempio che è possibile eseguire con i trigger predefiniti della pianificazione:

* Ottenere dati interni, ad esempio eseguire un stored procedure SQL ogni giorno.

* Ottenere dati esterni, ad esempio i report Meteo pull da NOAA ogni 15 minuti.

* Inviare i dati del report, ad esempio tramite posta elettronica a un riepilogo per tutti gli ordini superiori a un importo specifico della settimana precedente.

* Elaborare i dati, ad esempio comprimere le immagini caricate di oggi ogni giorno feriale durante gli orari di minore traffico.

* Pulire i dati, ad esempio eliminare tutti i tweet più vecchi di tre mesi.

* I dati di archiviazione, ad esempio le fatture push a un servizio di backup alle 1:00 AM ogni giorno per i nove mesi successivi.

È anche possibile usare le azioni predefinite pianifica per sospendere il flusso di lavoro prima dell'esecuzione dell'azione successiva, ad esempio:

* Attendere fino a un determinato giorno della settimana per inviare un aggiornamento di stato tramite posta elettronica.

* Ritardare il flusso di lavoro fino a quando una chiamata HTTP viene completata prima di riprendere e recuperare i risultati.

Questo articolo descrive le funzionalità per la pianificazione di trigger e azioni predefiniti.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Pianifica trigger

È possibile avviare il flusso di lavoro dell'app per la logica usando il trigger di ricorrenza o il trigger della finestra temporale scorrevole, che non è associato ad alcun servizio o sistema specifico. Questi trigger avviano ed eseguono il flusso di lavoro in base alla ricorrenza specificata, in cui è possibile selezionare l'intervallo e la frequenza, ad esempio il numero di secondi, minuti, ore, giorni, settimane o mesi. È anche possibile impostare la data e l'ora di inizio insieme al fuso orario. Ogni volta che viene attivato un trigger, app per la logica crea ed esegue una nuova istanza del flusso di lavoro per l'app per la logica.

Ecco le differenze tra i trigger seguenti:

* **Ricorrenza**: esegue il flusso di lavoro a intervalli di tempo regolari in base alla pianificazione specificata. Se il trigger non riceve ricorrenze, ad esempio a causa di rotture o flussi di lavoro disabilitati, il trigger di ricorrenza non elabora le ricorrenze perse ma riavvia le ricorrenze con l'intervallo pianificato successivo.

  Se si seleziona **giorno** come frequenza, è possibile specificare le ore del giorno e i minuti dell'ora, ad esempio, ogni giorno alle 2:30. Se si seleziona **settimana** come frequenza, è anche possibile selezionare i giorni della settimana, ad esempio mercoledì e sabato. È anche possibile specificare una data e un'ora di inizio insieme a un fuso orario per la pianificazione della ricorrenza.

  > [!TIP]
  > Se una ricorrenza non specifica una [data e un'ora di inizio](#start-time)specifiche, la prima ricorrenza viene eseguita immediatamente quando si salva o si distribuisce l'app per la logica, nonostante l'installazione della ricorrenza del trigger. Per evitare questo comportamento, specificare una data e un'ora di inizio per il momento in cui si desidera che venga eseguita la prima ricorrenza.
  >
  > Se una ricorrenza non specifica altre opzioni di pianificazione avanzate, ad esempio orari specifici per l'esecuzione di ricorrenze future, tali ricorrenze si basano sull'ultima esecuzione. Di conseguenza, le ore di inizio per tali ricorrenze potrebbero andare alla deriva a causa di fattori quali la latenza durante le chiamate di archiviazione. Per assicurarsi che l'app per la logica non perda una ricorrenza, soprattutto quando la frequenza è espressa in giorni o più, provare le opzioni seguenti:
  >
  > * Specificare una data e un'ora di inizio per la ricorrenza più le ore specifiche in cui eseguire le ricorrenze successive usando le proprietà denominate **in queste ore** e **in questi minuti**, che sono disponibili solo per le frequenze di **giorno** e **settimana** .
  >
  > * Utilizzare il [trigger finestra temporale scorrevole](../connectors/connectors-native-sliding-window.md)anziché il trigger ricorrenza.

  Per altre informazioni, vedere [creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger di ricorrenza](../connectors/connectors-native-recurrence.md).

* **Finestra temporale scorrevole**: esegue il flusso di lavoro a intervalli di tempo regolari che gestiscono i dati in blocchi continui. Se il trigger non riceve ricorrenze, ad esempio a causa di rotture o flussi di lavoro disabilitati, il trigger della finestra temporale scorrevole torna indietro ed elabora le ricorrenze perse.

  È possibile specificare una data e un'ora di inizio, un fuso orario e una durata per ritardare ogni ricorrenza nel flusso di lavoro. Questo trigger non supporta le pianificazioni avanzate, ad esempio ore specifiche del giorno, minuti dell'ora e giorni della settimana. Per altre informazioni, vedere [creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger della finestra temporale scorrevole](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Pianifica azioni

Dopo qualsiasi azione nel flusso di lavoro dell'app per la logica, è possibile usare il ritardo e il ritardo fino a quando le azioni non consentono di attendere il flusso di lavoro prima dell'esecuzione dell'azione successiva.

* **Ritardo**: attendere l'esecuzione dell'azione successiva per il numero di unità di tempo specificato, ad esempio secondi, minuti, ore, giorni, settimane o mesi. Per ulteriori informazioni, vedere [ritardare l'azione successiva nei flussi di lavoro](../connectors/connectors-native-delay.md).

* **Ritardo fino** a: attendere l'esecuzione dell'azione successiva fino alla data e all'ora specificate. Per ulteriori informazioni, vedere [ritardare l'azione successiva nei flussi di lavoro](../connectors/connectors-native-delay.md).

<a name="start-time"></a>

## <a name="patterns-for-start-date-and-time"></a>Modelli per data e ora di inizio

Ecco alcuni modelli che illustrano come è possibile controllare la ricorrenza con la data e l'ora di inizio e come il servizio app per la logica esegue queste ricorrenze:

| Ora di inizio | Ricorrenza senza pianificazione | Ricorrenza con pianificazione (solo trigger di ricorrenza) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Esegue immediatamente il primo carico di lavoro. <p>Esegue i carichi di lavoro futuri in base all'ultima esecuzione. | Esegue immediatamente il primo carico di lavoro. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. |
| Ora di inizio nel passato | Trigger di **ricorrenza** : calcola le esecuzioni in base all'ora di inizio specificata e rimuove le esecuzioni precedenti. Esegue il primo carico di lavoro all'esecuzione successiva. <p>Esegue i carichi di lavoro futuri in base ai calcoli dell'ultima esecuzione. <p><p>Trigger **finestra temporale scorrevole** : calcola le esecuzioni in base all'ora di inizio specificata e rispetta i tempi di esecuzione precedenti. <p>Esegue carichi di lavoro futuri in base ai calcoli dall'ora di inizio specificata. <p><p>Per altri chiarimenti, vedere l'esempio dopo questa tabella. | Esegue il primo carico di lavoro *non prima* dell'ora di inizio, in base alla pianificazione calcolata dall'ora di inizio. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. <p>**Nota:** Se si specifica una ricorrenza con una pianificazione, ma non si specificano ore o minuti per la pianificazione, le app per la logica calcolano i tempi di esecuzione successivi usando rispettivamente le ore o i minuti dalla prima esecuzione. |
| Ora di inizio o in futuro | Esegue il primo carico di lavoro all'ora di inizio specificata. <p>Esegue i carichi di lavoro futuri in base ai calcoli dell'ultima esecuzione. | Esegue il primo carico di lavoro *non prima* dell'ora di inizio, in base alla pianificazione calcolata dall'ora di inizio. <p>Esegue i carichi di lavoro futuri in base alla pianificazione specificata. <p>**Nota:** Se si specifica una ricorrenza con una pianificazione, ma non si specificano ore o minuti per la pianificazione, le app per la logica calcolano i tempi di esecuzione successivi usando rispettivamente le ore o i minuti dalla prima esecuzione. |
||||

*Esempio per ora di inizio e ricorrenza, ma nessuna pianificazione*

Si supponga che la data e l'ora correnti siano 8 settembre 2017 alle 1:00. Specificare la data e l'ora di inizio come 7 settembre, 2017 alle 2:00 PM, che è nel passato, e una ricorrenza che viene eseguita ogni due giorni.

| Ora di inizio | Ora corrente | Ricorrenza | Pianifica |
|------------|--------------|------------|----------|
| 2017-09-**07** T14:00:00Z <br>(2017-09-**07** alle 2:00 PM) | 2017-09-**08** T13:00:00Z <br>(2017-09-**08** alle 1:00 PM) | Ogni due giorni | {none} |
|||||

Per il trigger di ricorrenza, il motore di app per la logica calcola le esecuzioni in base all'ora di inizio, rimuove le esecuzioni precedenti, usa l'ora di inizio successiva per la prima esecuzione e calcola le esecuzioni future in base all'ultima esecuzione.

Di seguito è illustrato l'aspetto di questa ricorrenza:

| Ora di inizio | Prima esecuzione | Esecuzioni future |
|------------|----------------|------------------|
| 2017-09-**07** alle 14:00 | 2017-09-**09** alle 14:00 | 2017-09-**11** alle 14:00 </br>2017-09-**13** alle 14:00 </br>2017-09-**15** alle 14:00 </br>e così via. |
||||

Quindi, indipendentemente dalla distanza in passato, si specifica l'ora di inizio, ad esempio 2017-09-**05** alle 2:00 pm o 2017-09-**01** alle 2:00 PM, la prima esecuzione usa sempre l'ora di inizio successiva.

Per il trigger della finestra temporale scorrevole, il motore delle app per la logica calcola le esecuzioni in base all'ora di inizio, rispetta le esecuzioni precedenti, usa l'ora di inizio per la prima esecuzione e calcola le esecuzioni future in base all'ora di inizio.

Di seguito è illustrato l'aspetto di questa ricorrenza:

| Ora di inizio | Prima esecuzione | Esecuzioni future |
|------------|----------------|------------------|
| 2017-09-**07** alle 14:00 | 2017-09-**08** alle 1:00 PM (ora corrente) | 2017-09-**09** alle 14:00 </br>2017-09-**11** alle 14:00 </br>2017-09-**13** alle 14:00 </br>2017-09-**15** alle 14:00 </br>e così via. |
||||

Quindi, indipendentemente dalla distanza in passato, si specifica l'ora di inizio, ad esempio 2017-09-**05** alle 2:00 pm o 2017-09-**01** alle 2:00 PM, la prima esecuzione usa sempre l'ora di inizio specificata.

<a name="daylight-saving-standard-time"></a>

## <a name="recurrence-for-daylight-saving-time-and-standard-time"></a>Ricorrenza per l'ora legale e l'ora solare

I trigger predefiniti ricorrenti rispettano la pianificazione impostata, incluso il fuso orario specificato. Se non si seleziona un fuso orario, l'ora legale (DST) può influire sul momento dell'esecuzione dei trigger, ad esempio, spostando l'ora di inizio un'ora in avanti quando l'ora legale viene avviata e un'ora indietro al termine dell'ora legale. Quando si pianificano processi, app per la logica inserisce il messaggio per l'elaborazione nella coda e specifica quando tale messaggio diventa disponibile, in base all'ora UTC in cui è stato eseguito l'ultimo processo e all'ora UTC in cui è pianificato l'esecuzione del processo successivo.

Per evitare questo spostamento in modo che l'app per la logica venga eseguita all'ora di inizio specificata, assicurarsi di selezionare un fuso orario. In questo modo, l'ora UTC per l'app per la logica viene spostata anche per contrastare il cambiamento di tempo stagionale.

<a name="dst-window"></a>

> [!NOTE]
> I trigger che iniziano tra 2:00 AM-3:00 AM potrebbero avere problemi perché le modifiche all'ora legale si verificano alle 2:00 AM, che potrebbero causare l'ora di inizio per diventare non valido o ambiguo. Se sono presenti più app per la logica nello stesso intervallo ambiguo, è possibile che si sovrappongano. Per questo motivo, è consigliabile evitare gli orari di inizio tra 2:00 AM-3:00 AM.

Si supponga, ad esempio, di avere due app per la logica che vengono eseguite ogni giorno. Un'app per la logica viene eseguita alle 1:30 dell'ora locale, mentre l'altra viene eseguita un'ora dopo le 2:30 ora locale. Cosa accade alle ore di inizio per queste app quando l'ora legale inizia e termina?

* I trigger vengono eseguiti quando il tempo si sposta un'ora in avanti?

* I trigger vengono eseguiti due volte quando il tempo si sposta un'ora indietro?

Se queste app per la logica usano la zona UTC-6:00 Central Time (US & Canada), questa simulazione Mostra come l'orario UTC si è spostato in 2019 per contrastare le modifiche dell'ora legale, spostando un'ora indietro o avanti in base alle esigenze, in modo che le app continuino a essere eseguite alle ore locali previste senza esecuzioni duplicate o ignorate.

* **03/10/2019: l'ora legale inizia alle 2:00 AM, spostando l'ora un'ora in poi**

  Per compensare una volta avviata l'ora legale, l'ora UTC viene spostata un'ora indietro in modo che l'app per la logica continui a funzionare nello stesso momento locale:

  * #1 app per la logica

    | Data | Ora (locale) | Ora (UTC) | Note |
    |------|--------------|------------|-------|
    | 03/09/2019 | 1:30:00 AM | 7:30:00 AM | UTC prima del giorno in cui viene applicata l'ora legale. |
    | 03/10/2019 | 1:30:00 AM | 7:30:00 AM | L'ora UTC è la stessa perché l'ora legale non è stata applicata. |
    | 03/11/2019 | 1:30:00 AM | 6:30:00 AM | L'ora UTC è stata spostata un'ora indietro dopo che è stata applicata la DST. |
    |||||

  * #2 app per la logica

    | Data | Ora (locale) | Ora (UTC) | Note |
    |------|--------------|------------|-------|
    | 03/09/2019 | 2:30:00 AM | 8:30:00 AM | UTC prima del giorno in cui viene applicata l'ora legale. |
    | 03/10/2019 | 3:30:00 AM * | 8:30:00 AM | L'ora legale è già attiva, quindi l'ora locale è stata spostata un'ora in poi perché il fuso orario UTC-6:00 diventa UTC-5:00. Per ulteriori informazioni, vedere [trigger che iniziano tra 2:00 AM-3:00 AM](#dst-window). |
    | 03/11/2019 | 2:30:00 AM | 7:30:00 AM | L'ora UTC è stata spostata un'ora indietro dopo che è stata applicata la DST. |
    |||||

* **11/03/2019: l'ora legale termina alle 2:00 AM e sposta l'ora indietro di un'ora**

  Per compensare, l'ora UTC viene spostata un'ora avanti in modo che l'app per la logica continui a funzionare nello stesso momento locale:

  * #1 app per la logica

    | Data | Ora (locale) | Ora (UTC) | Note |
    |------|--------------|------------|-------|
    | 11/02/2019 | 1:30:00 AM | 6:30:00 AM ||
    | 11/03/2019 | 1:30:00 AM | 6:30:00 AM ||
    | 11/04/2019 | 1:30:00 AM | 7:30:00 AM ||
    |||||

  * #2 app per la logica

    | Data | Ora (locale) | Ora (UTC) | Note |
    |------|--------------|------------|-------|
    | 11/02/2019 | 2:30:00 AM | 7:30:00 AM ||
    | 11/03/2019 | 2:30:00 AM | 8:30:00 AM ||
    | 11/04/2019 | 2:30:00 AM | 8:30:00 AM ||
    |||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Esegui solo una volta

Se si vuole eseguire l'app per la logica solo in un momento successivo, è possibile usare l' **utilità di pianificazione: Esegui una volta** il modello di processi. Dopo aver creato una nuova app per la logica, ma prima di aprire la finestra di progettazione di app per la logica, nella sezione **modelli** selezionare **pianificazione** nell'elenco **categoria** , quindi selezionare il modello seguente:

![Selezionare il modello "Scheduler: Esegui una volta processi"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

In alternativa, se è possibile avviare l'app per la logica con il trigger **quando viene ricevuta una richiesta http** e passare l'ora di inizio come parametro per il trigger. Per la prima azione, utilizzare l'azione **ritardo fino a pianificazione** e specificare l'ora di inizio dell'esecuzione dell'azione successiva.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Ricorrenze di esempio

Di seguito sono riportate diverse ricorrenze di esempio che è possibile configurare per i trigger che supportano le opzioni:

| Trigger | Ricorrenza | Interval | Frequenza | Ora di inizio | In questi giorni | A queste ore | A questi minuti | Nota |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni 15 minuti (senza data e ora di inizio) | 15 | Minuto | {none} | {non disponibile} | {none} | {none} | Questa pianificazione inizia immediatamente, quindi calcola le ricorrenze future in base all'ultima esecuzione. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni 15 minuti (con data e ora di inizio) | 15 | Minuto | *DataInizio* T *OraInizio* Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, quindi calcola le ricorrenze future in base all'ultima esecuzione. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni ora all'inizio dell'ora (con data e ora di inizio) | 1 | Ora | *DataInizio* Thh:00:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate. Le ricorrenze future vengono eseguite ogni ora al contrassegno dei minuti "00", che le app per la logica calcolano dall'ora di inizio. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni ora ogni giorno (senza data e ora di inizio) | 1 | Ora | {none} | {non disponibile} | {none} | {none} | Questa pianificazione inizia immediatamente, quindi calcola le ricorrenze future in base all'ultima esecuzione. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni ora ogni giorno (con data e ora di inizio) | 1 | Ora | *DataInizio* T *OraInizio* Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, quindi calcola le ricorrenze future in base all'ultima esecuzione. <p>Se la frequenza è "Settimana" o "Mese", la pianificazione viene eseguita rispettivamente un solo giorno a settimana o un solo giorno al mese. |
| Ricorrenza <br>Finestra temporale scorrevole | Eseguire ogni 15 minuti dopo l'ora, ogni ora (con data e ora di inizio) | 1 | Ora | *DataInizio* T00:15:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate. Le ricorrenze future vengono eseguite al contrassegno dei minuti "15", che le app per la logica calcolano dall'ora di inizio, quindi alle 00:15 AM, 1:15 AM, 2:15 AM e così via. |
| Ricorrenza | Eseguire ogni 15 minuti dopo l'ora, ogni ora (senza data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Questa pianificazione viene eseguita alle 00:15, 1:15, 2:15 e così via. Questa pianificazione è equivalente a una frequenza di "Ora" e un'ora di inizio con "15" minuti. |
| Ricorrenza | Viene eseguito ogni 15 minuti ai contrassegni dei minuti specificati (nessuna data e ora di inizio). | 1 | Giorno | {none} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | La pianificazione non inizia fino al raggiungimento dell'indicatore di 15 minuti successivo. |
| Ricorrenza | Eseguire ogni giorno alle 8.00 *oltre* ai minuti di contrassegno quando si salva l'app per la logica | 1 | Giorno | {none} | {non disponibile} | 8 | {none} | Senza una data e un'ora di inizio, questa pianificazione viene eseguita in base al momento in cui si salva l'app per la logica (operazione PUT). |
| Ricorrenza | Esecuzione giornaliera alle ore 8:00 (con data e ora di inizio) | 1 | Giorno | *DataInizio* T08:00:00Z | {non disponibile} | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate. Le occorrenze future vengono eseguite ogni giorno alle 8:00 AM. | 
| Ricorrenza | Esecuzione giornaliera alle 8:00 AM (nessuna data e ora di inizio) | 1 | Giorno | {none} | {non disponibile} | 8 | 00 | Questa pianificazione viene eseguita ogni giorno alle 8:00. |
| Ricorrenza | Esecuzione giornaliera alle 8:00 AM e 4:00 PM | 1 | Giorno | {none} | {non disponibile} | 8, 16 | 0 | |
| Ricorrenza | Esecuzione giornaliera alle 8:30 AM, 8:45 AM, 4:30 PM e 4:45 PM | 1 | Giorno | {none} | {non disponibile} | 8, 16 | 30, 45 | |
| Ricorrenza | Eseguire ogni sabato alle 5:00 PM (nessuna data e ora di inizio) | 1 | Settimana | {none} | "Sabato" | 17 | 0 | Questa pianificazione viene eseguita ogni sabato alle 17:00. |
| Ricorrenza | Eseguire ogni sabato alle ore 5:00 (con data e ora di inizio) | 1 | Settimana | *DataInizio* T17:00:00Z | "Sabato" | {none} | {none} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate, in questo caso il 9 settembre 2017 alle 17:00. Le ricorrenze future verranno eseguite ogni sabato alle 17:00. |
| Ricorrenza | Eseguire ogni martedì, giovedì alle 17.00, *oltre* ai minuti di contrassegno quando si salva l'app per la logica| 1 | Settimana | {none} | "Martedì", "Giovedì" | 17 | {none} | |
| Ricorrenza | Viene eseguito ogni ora durante le ore lavorative. | 1 | Settimana | {none} | Selezionare tutti i giorni, ad eccezione di sabato e domenica. | Selezionare le ore del giorno desiderate. | Selezionare i minuti dell'ora desiderati. | Ad esempio, se le ore lavorative sono 8:00 AM 5:00 PM, selezionare "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" come ore del giorno *più* "0" come minuti dell'ora. |
| Ricorrenza | Eseguire una volta al giorno nei fine settimana | 1 | Settimana | {none} | "Sabato", "Domenica" | Selezionare le ore del giorno desiderate. | Selezionare i minuti dell'ora desiderati. | Questa pianificazione verrà eseguita ogni sabato e domenica alla pianificazione specificata. |
| Ricorrenza | Eseguire ogni 15 minuti ogni 2 settimane solo il lunedì | 2 | Settimana | {none} | "Lunedì" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Questa pianificazione verrà eseguita un lunedì a settimane alterne, in corrispondenza dell'indicatore dei 15 minuti. |
| Ricorrenza | Esegui ogni mese | 1 | Month | *DataInizio* T *OraInizio* Z | {non disponibile} | {non disponibile} | {non disponibile} | Questa pianificazione non inizia *prima* della data e dell'ora di inizio specificate e calcola le ricorrenze future alla data e all'ora di inizio. Se non si specificano una data e un'ora di inizio, questa pianificazione usa la data e l'ora di creazione. |
| Ricorrenza | Eseguire ogni ora per un giorno al mese | 1 | Month | {vedere la nota} | {non disponibile} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {vedere la nota} | Se non si specificano una data e un'ora di inizio, questa pianificazione usa la data e l'ora di creazione. Per controllare i minuti della pianificazione di ricorrenza, specificare i minuti dell'ora, un'ora di inizio oppure usare l'ora di creazione. Ad esempio, se l'ora di inizio o creazione è 8:25, questa pianificazione verrà eseguita alle 8:25, 9:25, 10:25 e così via. |
|||||||||

## <a name="next-steps"></a>Passaggi successivi

* [Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger di ricorrenza](../connectors/connectors-native-recurrence.md)
* [Creare, pianificare ed eseguire attività e flussi di lavoro ricorrenti con il trigger finestra temporale scorrevole](../connectors/connectors-native-sliding-window.md)
* [Sospendere i flussi di lavoro con azioni di ritardo](../connectors/connectors-native-delay.md)
