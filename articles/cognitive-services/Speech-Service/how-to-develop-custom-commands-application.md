---
title: 'Procedura: sviluppare applicazioni di comandi personalizzati-servizio di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Informazioni su come sviluppare e personalizzare le applicazioni di comandi personalizzati. Queste app del comando vocale sono ideali per gli scenari di completamento delle attività o di comando e controllo.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 1a002b6efbe2603ae254c19f9e3cc7377198cea2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "97935819"
---
# <a name="develop-custom-commands-applications"></a>Sviluppare applicazioni di comandi personalizzati

In questo articolo sulle procedure viene illustrato come sviluppare e configurare le applicazioni di comandi personalizzati. La funzionalità comandi personalizzati ti aiuta a creare app avanzate per i comandi vocali ottimizzate per esperienze di interazione con la prima voce. La funzionalità è più adatta per gli scenari di completamento delle attività o di comando e controllo. È particolarmente adatto per i dispositivi di Internet delle cose (Internet) e per i dispositivi di ambiente e non Head.

In questo articolo viene creata un'applicazione in grado di attivare e disattivare una TV, impostare la temperatura e impostare un allarme. Dopo aver creato questi comandi di base, verranno fornite informazioni sulle opzioni seguenti per la personalizzazione dei comandi:

* Aggiunta di parametri ai comandi
* Aggiunta di configurazioni ai parametri del comando
* Creazione di regole di interazione
* Creazione di modelli di generazione della lingua per risposte vocali
* Uso di strumenti vocali personalizzati

## <a name="create-an-application-by-using-simple-commands"></a>Creare un'applicazione usando semplici comandi

Per iniziare, creare un'applicazione di comandi personalizzata vuota. Per informazioni dettagliate, vedere la [Guida introduttiva](quickstart-custom-commands-application.md). In questa applicazione, anziché importare un progetto, viene creato un progetto vuoto.

1. Nella casella **nome** immettere il nome del progetto *Smart-Room-Lite* (o un altro nome a scelta).
1. Nell'elenco **lingua** selezionare **inglese (Stati Uniti)**.
1. Selezionare o creare una risorsa LUIS.

   > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra la finestra "nuovo progetto".](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Aggiornare le risorse LUIS (facoltativo)

È possibile aggiornare la risorsa di creazione selezionata nella finestra **nuovo progetto** . È inoltre possibile impostare una risorsa di stima. 

Una risorsa di stima viene utilizzata per il riconoscimento quando viene pubblicata l'applicazione dei comandi personalizzati. Non è necessaria una risorsa di stima durante le fasi di sviluppo e test.

### <a name="add-a-turnon-command"></a>Aggiungere un comando accendere

Nell'applicazione dei comandi personalizzati Smart-Room-Lite vuota creata aggiungere un comando. Tramite il comando viene elaborato un enunciato, `Turn on the tv` . Verrà risposto con il messaggio `Ok, turning the tv on` .

1. Creare un nuovo comando selezionando **nuovo comando** nella parte superiore del riquadro sinistro. Verrà visualizzata la finestra **nuovo comando** .
1. Specificare il valore per il campo **nome** `TurnOn` .
1. Selezionare **Crea**.

Il riquadro centrale elenca le proprietà del comando. 

Nella tabella seguente vengono illustrate le proprietà di configurazione del comando. Per altre informazioni, vedere [concetti e definizioni dei comandi personalizzati](./custom-commands-references.md).

| Configurazione            | Descrizione                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Frasi di esempio | Espressioni di esempio che l'utente può pronunciare per attivare questo comando.                                                                 |
| Parametri       | Informazioni necessarie per completare il comando.                                                                                |
| Regole di completamento | Azioni da intraprendere per completare il comando. Esempi: risposta all'utente o comunicazione con un servizio Web. |
| Regole di interazione   | Altre regole per gestire situazioni più specifiche o complesse.                                                              |


> [!div class="mx-imgBorder"]
> ![Screenshot che mostra dove creare un comando.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Aggiungi frasi di esempio

Nella sezione **frasi di esempio** viene fornito un esempio di ciò che l'utente può pronunciare.

1. Nel riquadro centrale selezionare frasi di **esempio**.
1. Nel riquadro a destra aggiungere esempi:

    ```
    Turn on the tv
    ```

1.  Nella parte superiore della pagina selezionare **Salva**.

Non sono ancora disponibili parametri, quindi è possibile passare alla sezione **regole di completamento** .

#### <a name="add-a-completion-rule"></a>Aggiungere una regola di completamento

Successivamente, il comando richiede una regola di completamento. Questa regola indica all'utente che viene eseguita un'azione di evasione. 

Per altre informazioni sulle regole e sulle regole di completamento, vedere [concetti e definizioni dei comandi personalizzati](./custom-commands-references.md).

1. Selezionare la regola di completamento predefinita **completata**. Quindi modificarlo come segue:

    
    | Impostazione    | Valore consigliato                          | Descrizione                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nome**       | `ConfirmationResponse`                  | Nome che descrive lo scopo della regola          |
    | **Condizioni** | nessuno                                     | Condizioni che determinano quando la regola può essere eseguita    |
    | **Actions**    | **Invia risposta vocale**  >  **Editor semplice**  >  **Prima variazione** > `Ok, turning the tv on` | Azione da eseguire quando la condizione della regola è true |

   > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra dove creare una risposta vocale.](media/custom-commands/create-speech-response-action.png)

1. Selezionare **Save (Salva** ) per salvare l'azione.
1. Nella sezione **regole di completamento** selezionare **Salva** per salvare tutte le modifiche. 

    > [!NOTE]
    > Non è necessario usare la regola di completamento predefinita fornita con il comando. È possibile eliminare la regola di completamento predefinita e aggiungere una regola personalizzata.

### <a name="add-a-settemperature-command"></a>Aggiungere un comando setemperature

A questo punto aggiungere un altro comando `SetTemperature` . Questo comando prenderà un solo enunciato, `Set the temperature to 40 degrees` e risponderà con il messaggio `Ok, setting temperature to 40 degrees` .

Per creare il nuovo comando, attenersi alla procedura usata per il `TurnOn` comando, ma usare la frase di esempio `Set the temperature to 40 degrees` .

Modificare quindi le regole di completamento **eseguite** esistenti come segue:

| Impostazione    | Valore consigliato                          |
| ---------- | ---------------------------------------- |
| **Nome**  | `ConfirmationResponse`                  |
| **Condizioni** | nessuno                                     |
| **Actions**    | **Invia risposta vocale**  >  **Editor semplice**  >  **Prima variazione** > `Ok, setting temperature to 40 degrees` |

Selezionare **Save (Salva** ) per salvare tutte le modifiche apportate al comando.

### <a name="add-a-setalarm-command"></a>Aggiungere un comando sealarm

Creare un nuovo `SetAlarm` comando. Usare la frase di esempio `Set an alarm for 9 am tomorrow` . Modificare quindi le regole di completamento **eseguite** esistenti come segue:

| Impostazione    | Valore consigliato                          |
| ---------- | ---------------------------------------- |
| **Nome**  | `ConfirmationResponse`                  |
| **Condizioni** | nessuno                                     |
| **Actions**    | **Invia risposta vocale**  >  **Editor semplice**  >  **Prima variazione** > `Ok, setting an alarm for 9 am tomorrow` |

Selezionare **Save (Salva** ) per salvare tutte le modifiche apportate al comando.

### <a name="try-it-out"></a>Provare questa operazione

Testare il comportamento dell'applicazione tramite il riquadro Test: 

1. Nell'angolo in alto a destra del riquadro selezionare l'icona del **treno** . 
1. Al termine del training, selezionare **test**. 

Provare gli esempi di espressioni seguenti usando la voce o il testo:

- Digitare: *impostare la temperatura su 40 gradi*
- Risposta prevista: OK, impostazione della temperatura su 40 gradi
- Digitare: *accendere la TV*
- Risposta prevista: OK, attivazione della TV
- Digitare: *impostare un allarme per le 09:00 domani*
- Risposta prevista: OK, impostazione di un allarme per le 09:00 domani

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra il test in un'interfaccia di chat Web.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> Nel riquadro test è possibile selezionare **Turn Details (dettagli** ) per informazioni su come è stato elaborato l'input vocale o l'input di testo.

## <a name="add-parameters-to-commands"></a>Aggiungere parametri ai comandi

In questa sezione viene illustrato come aggiungere parametri ai comandi. I comandi richiedono parametri per completare un'attività. In scenari complessi, i parametri possono essere usati per definire le condizioni che attivano azioni personalizzate.

### <a name="configure-parameters-for-a-turnon-command"></a>Configurare i parametri per un comando accendere

Per iniziare, modificare il `TurnOn` comando esistente per attivare e disattivare più dispositivi.

1. Ora che il comando gestirà gli scenari sia on che off, rinominare il comando come *TurnOnOff*.
   1. Nel riquadro a sinistra selezionare il comando **accendere** . Accanto a **nuovo comando** nella parte superiore del riquadro selezionare il pulsante con i puntini di sospensione (**...**).
   
   1. Selezionare **Rinomina**. Nella finestra **Rinomina comando** modificare il nome in *TurnOnOff*.

1. Aggiungere un nuovo parametro al comando. Il parametro indica se l'utente vuole attivare o disattivare il dispositivo.
   1. Nella parte superiore del riquadro centrale selezionare  **Aggiungi**. Scegliere **Parameter** dal menu a discesa.
   1. Nel riquadro a destra, nella sezione **parametri** , nella casella **nome** aggiungere `OnOff` .
   1. Selezionare **required**. Nella finestra **Aggiungi risposta per un parametro obbligatorio** selezionare **Editor semplice**. Nel **primo campo variante** aggiungere *on o off?*.
   1. Selezionare **Aggiorna**.

       > [!div class="mx-imgBorder"]
       > ![Screenshot che mostra la sezione ' Aggiungi risposta per un parametro obbligatorio ' con la scheda ' editor semplice ' selezionata.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Configurare le proprietà del parametro usando la tabella seguente. Per informazioni su tutte le proprietà di configurazione di un comando, vedere [concetti e definizioni dei comandi personalizzati](./custom-commands-references.md).
      

       | Configurazione      | Valore consigliato     | Descrizione                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Nome**               | `OnOff`           | Nome descrittivo per il parametro                                                                           |
       | **È globale**          | Deselezionato       | Casella di controllo che indica se un valore per questo parametro viene applicato globalmente a tutti i comandi nell'applicazione.|
       | **Obbligatorio**           | Opzione selezionata         | Casella di controllo che indica se è necessario un valore per questo parametro prima del completamento del comando. |
       | **Risposta per il parametro obbligatorio**      |**Editor semplice** > `On or Off?`      | Messaggio che richiede il valore di questo parametro quando non è noto. |
       | **Tipo**               | **Stringa**          | Tipo di parametro, ad esempio numero, stringa, data/ora o geografia.   |
       | **Configuration**      | **Accetta valori di input predefiniti da un catalogo interno** | Per le stringhe, questa impostazione limita gli input a un set di valori possibili. |
       | **Valori di input predefiniti**     | `on`, `off`           | Set di valori possibili e relativi alias.         |
       
        
   1. Per aggiungere valori di input predefiniti, selezionare **Aggiungi un input predefinito**. Nella finestra **nuovo elemento**  digitare *Name* come illustrato nella tabella precedente. In questo caso, non si usano alias, pertanto è possibile lasciare vuoto questo campo.
   
      > [!div class="mx-imgBorder"]
      > ![Screenshot che illustra come creare un parametro.](media/custom-commands/create-on-off-parameter.png)

   1. Selezionare **Save (Salva** ) per salvare tutte le configurazioni del parametro.
 
#### <a name="add-a-subjectdevice-parameter"></a>Aggiungere un parametro SubjectDevice

1. Per aggiungere un secondo parametro per rappresentare il nome dei dispositivi che possono essere controllati utilizzando questo comando, selezionare **Aggiungi**. Utilizzare la configurazione seguente.


    | Impostazione            | Valore consigliato       |
    | ------------------ | --------------------- |
    | **Nome**               | `SubjectDevice`         |
    | **È globale**          | Deselezionato             |
    | **Obbligatorio**           | Opzione selezionata               |
    | **Risposta per il parametro obbligatorio**     | **Editor semplice** > `Which device do you want to control?`    | 
    | **Tipo**               | **Stringa**                |          |
    | **Configuration**      | **Accetta valori di input predefiniti da un catalogo interno** | 
    | **Valori di input predefiniti** | `tv`, `fan`               |
    | **Alias** ( `tv` )      | `television`, `telly`     |

1. Selezionare **Salva**.

#### <a name="modify-example-sentences"></a>Modificare le frasi di esempio

Per i comandi che usano parametri, è utile aggiungere frasi di esempio che coprono tutte le possibili combinazioni. Ad esempio:

* Informazioni complete sui parametri: `turn {OnOff} the {SubjectDevice}`
* Informazioni sul parametro parziale: `turn it {OnOff}`
* Nessuna informazione sui parametri: `turn something`

Le frasi di esempio che usano vari gradi di informazioni consentono all'applicazione di comandi personalizzati di risolvere le risoluzioni unidirezionali e a più turni usando le informazioni parziali.

Tenendo presenti queste informazioni, modificare le frasi di esempio in modo da usare questi parametri suggeriti:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Selezionare **Salva**.

> [!TIP]
> Nell'editor di frasi di esempio usare parentesi graffe per fare riferimento ai parametri. Ad esempio: `turn {OnOff} the {SubjectDevice}`.
> Usare una scheda per il completamento automatico supportato da parametri creati in precedenza.

#### <a name="modify-completion-rules-to-include-parameters"></a>Modificare le regole di completamento per includere i parametri

Modificare la regola di completamento esistente `ConfirmationResponse` .

1. Nella sezione **condizioni** selezionare **Aggiungi una condizione**.
1. Nella finestra **nuova condizione** selezionare **parametri obbligatori** nell'elenco **tipo** . Nell'elenco che segue selezionare sia **OnOff** che **SubjectDevice**.
1. Lasciare deselezionata l' **Anglobalità** .
1. Selezionare **Crea**.
1. Nella sezione **azioni** modificare l'azione **Invia risposta vocale** posizionando il puntatore del mouse su di essa e selezionando il pulsante modifica. Questa volta usare i `OnOff` parametri e appena creati `SubjectDevice` :

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Selezionare **Salva**.

Provare le modifiche selezionando l'icona di **Train** nella parte superiore del riquadro a destra. 

Al termine del training, selezionare **test**. Viene visualizzato un test della finestra **dell'applicazione** . Provare le interazioni seguenti:

- Input: *spegnere la TV*
- Output: OK, spegnimento della TV
- Input: *spegnere la televisione*
- Output: OK, spegnimento della TV
- Input: *Disattiva*
- Output: quale dispositivo si vuole controllare?
- Input: *TV*
- Output: OK, spegnimento della TV

### <a name="configure-parameters-for-a-settemperature-command"></a>Configurare i parametri per un comando setemperature

Modificare il `SetTemperature` comando per abilitarlo a impostare la temperatura durante l'indirizzamento dell'utente.

Aggiungere un `Temperature` parametro. Usare la configurazione seguente:

| Configurazione      | Valore consigliato     |
| ------------------ | ----------------|
| **Nome**               | `Temperature`           |
| **Obbligatorio**           | Opzione selezionata         |
| **Risposta per il parametro obbligatorio**      | **Editor semplice** > `What temperature would you like?`
| **Tipo**               | `Number`          |


Modificare le espressioni di esempio in modo da usare i valori seguenti.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Modificare le regole di completamento esistenti. Utilizzare la configurazione seguente.

| Configurazione      | Valore consigliato     |
| ------------------ | ----------------|
| **Condizioni**         | **Parametro obbligatorio**  >  **Temperatura** di           |
| **Actions**           | **Invia risposta vocale** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Configurare i parametri per un comando sealarm

Aggiungere un parametro denominato `DateTime` . Utilizzare la configurazione seguente.

   | Impostazione                           | Valore consigliato                     | 
   | --------------------------------- | ----------------------------------------|
   | **Nome**                              | `DateTime`                               |
   | **Obbligatorio**                          | Opzione selezionata                                 |
   | **Risposta per il parametro obbligatorio**   | **Editor semplice** > `For what time?`            | 
   | **Tipo**                              | **DateTime**                                |
   | **Impostazioni predefinite data**                     | Se la data non è presente, usare oggi.            |
   | **Impostazioni predefinite temporali**                     | Se manca l'ora, usare l'inizio della giornata.     |


> [!NOTE]
> Questo articolo usa principalmente i tipi di parametro String, Number e DateTime. Per un elenco di tutti i tipi di parametro supportati e delle relative proprietà, vedere [concetti e definizioni dei comandi personalizzati](./custom-commands-references.md).


Modificare le espressioni di esempio. Utilizzare i seguenti valori.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Modificare le regole di completamento esistenti. Utilizzare la configurazione seguente.

   | Impostazione    | Valore consigliato                               |
   | ---------- | ------------------------------------------------------- |
   | **Actions**    | **Invia risposta vocale** > `Ok, alarm set for {DateTime}`  |

Testare i tre comandi insieme usando espressioni correlate a comandi diversi. È possibile passare da un comando all'altro.

- Input: *impostare un allarme*
- Output: per quanto tempo?
- Input: *accendere la TV*
- Output: OK, attivazione della TV
- Input: *impostare un allarme*
- Output: per quanto tempo?
- Input: *17.00*
- Output: OK, set di avvisi per 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Aggiungere configurazioni ai parametri del comando

In questa sezione vengono fornite ulteriori informazioni sulla configurazione dei parametri avanzati, tra cui:

 - Il modo in cui i valori dei parametri possono appartenere a un set definito al di fuori dell'applicazione Commands personalizzata.
 - Come aggiungere clausole di convalida sui valori dei parametri.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Configurare un parametro come entità del catalogo esterno

La funzionalità comandi personalizzati consente di configurare i parametri di tipo stringa per fare riferimento a cataloghi esterni ospitati su un endpoint Web. È quindi possibile aggiornare il catalogo esterno in modo indipendente senza modificare l'applicazione dei comandi personalizzati. Questo approccio è utile nei casi in cui le voci di catalogo siano numerose.

Riutilizzare il `SubjectDevice` parametro dal `TurnOnOff` comando. La configurazione corrente per questo parametro è **accettare gli input predefiniti dal catalogo interno**. Questa configurazione fa riferimento a un elenco statico di dispositivi nella configurazione dei parametri. Spostare questo contenuto in un'origine dati esterna che può essere aggiornata in modo indipendente.

Per spostare il contenuto, iniziare aggiungendo un nuovo endpoint Web. Nel riquadro a sinistra passare alla sezione **endpoint Web** . Aggiungere un nuovo endpoint Web. Utilizzare la configurazione seguente.

| Impostazione | Valore consigliato |
|----|----|
| **Nome** | `getDevices` |
| **URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Metodo** | **GET** |


Se il valore suggerito per l'URL non funziona, configurare e ospitare un endpoint Web che restituisce un file JSON costituito dall'elenco dei dispositivi che possono essere controllati. L'endpoint Web deve restituire un file JSON formattato come segue:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Passare quindi alla pagina delle impostazioni dei parametri **SubjectDevice** . Impostare le proprietà seguenti.

| Impostazione | Valore consigliato |
| ----| ---- |
| **Configuration** | **Accetta input predefiniti dal catalogo esterno** |                               
| **Endpoint Catalogo** | `getDevices` |
| **Metodo** | **GET** |

Selezionare quindi **Salva**.

> [!IMPORTANT]
> Non verrà visualizzata un'opzione per configurare un parametro per accettare gli input da un catalogo esterno, a meno che l'endpoint Web non sia impostato nella sezione **endpoint Web** del riquadro a sinistra.

Per provarlo, selezionare **Train**. Al termine del training, selezionare **test** e provare alcune interazioni.

* Input: *attiva*
* Output: quale dispositivo si vuole controllare?
* Input: *luci*
* Output: OK, accensione delle luci

> [!NOTE]
> È ora possibile controllare tutti i dispositivi ospitati nell'endpoint Web. Tuttavia, è comunque necessario eseguire il training dell'applicazione per testare le nuove modifiche e quindi ripubblicare l'applicazione.

### <a name="add-validation-to-parameters"></a>Aggiungere la convalida ai parametri

Le *convalide* sono costrutti che si applicano a determinati tipi di parametro che consentono di configurare vincoli sul valore del parametro. Vengono richieste le correzioni se i valori non rientrano nei vincoli. Per un elenco di tipi di parametro che estendono il costrutto di convalida, vedere [concetti e definizioni dei comandi personalizzati](./custom-commands-references.md).

Testare le convalide tramite il `SetTemperature` comando. Usare la procedura seguente per aggiungere una convalida per il `Temperature` parametro.

1. Nel riquadro a sinistra selezionare il comando **setemperature** .
1. Nel riquadro centrale selezionare **temperatura**.
1. Nel riquadro a destra selezionare **Aggiungi una convalida**.
1. Nella nuova finestra di **convalida** configurare la convalida come illustrato nella tabella seguente. Quindi selezionare **Crea**


    | Configurazione parametri | Valore consigliato | Descrizione |
    | ---- | ---- | ---- |
    | **Min Value (Valore minimo)** | `60` | Per i parametri number, il valore minimo che questo parametro può assumere |
    | **Max Value (Valore massimo)** | `80` | Per i parametri number, il valore massimo che questo parametro può assumere |
    | **Risposta non riuscita** |  **Editor semplice**  >  **Prima variazione** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Messaggio di richiesta per richiedere un nuovo valore se la convalida ha esito negativo |

    > [!div class="mx-imgBorder"]
    > ![Screenshot che illustra come aggiungere la convalida di un intervallo.](media/custom-commands/add-validations-temperature.png)

Per provarlo, selezionare l'icona di **Train** nella parte superiore del riquadro a destra. Al termine del training, selezionare **test**. Provare alcune interazioni:

- Input: *impostare la temperatura su 72 gradi*
- Output: OK, impostazione della temperatura su 72 gradi
- Input: *impostare la temperatura su 45 gradi*
- Output: è possibile impostare la temperatura solo tra 60 e 80 gradi
- Input: *renderlo 72 gradi*
- Output: OK, impostazione della temperatura su 72 gradi

## <a name="add-interaction-rules"></a>Aggiungere regole di interazione

Le regole di interazione sono regole *aggiuntive* che gestiscono situazioni specifiche o complesse. Sebbene sia possibile creare regole di interazione personalizzate, in questo esempio si usano le regole di interazione per gli scenari seguenti:

* Conferma di comandi
* Aggiunta di una correzione in un passaggio ai comandi

Per altre informazioni sulle regole di interazione, vedere [concetti e definizioni dei comandi personalizzati](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Aggiungere conferme a un comando

Per aggiungere una conferma, utilizzare il `SetTemperature` comando. Per ottenere la conferma, creare regole di interazione attenendosi alla procedura seguente:

1. Nel riquadro a sinistra selezionare il comando **setemperature** .
1. Nel riquadro centrale aggiungere le regole di interazione selezionando **Aggiungi**. Quindi selezionare il  >  **comando conferma** regole di interazione.

    Questa azione aggiunge tre regole di interazione. Le regole chiedono all'utente di confermare la data e l'ora dell'allarme. Si aspettano una conferma (Sì o no) per la prossima volta.

    1. Modificare la regola di interazione del **comando Confirm** usando la configurazione seguente:
        1. Modificare il nome per **confermare la temperatura**.
        1. Aggiungere una nuova condizione: **parametri obbligatori**  >  **temperatura**.
        1. Aggiungere una nuova azione: **digitare**  >  la **risposta di invio vocale**  >  **impostare la temperatura come {temperatura} gradi?**
        1. Nella sezione **aspettative** lasciare il valore predefinito di **conferma prevista dall'utente**.
      
         > [!div class="mx-imgBorder"]
         > ![Screenshot che illustra come creare la risposta obbligatoria per il parametro.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modificare la regola di interazione **conferma riuscita** per gestire una conferma riuscita (l'utente ha detto sì).
      
          1. Modificare il nome in **temperatura di conferma completata**.
          1. Lasciare la condizione di **conferma esistente riuscita** .
          1. Aggiungere una nuova condizione: **digitare** la  >  **temperatura parametri richiesti**  >  .
          1. Lasciare il valore **dello stato di post-esecuzione** predefinito come **regole di completamento esecuzione**.

    1. Modificare la regola di interazione di **conferma negata** per gestire gli scenari quando la conferma viene negata (l'utente ha detto no).

          1. Modificare il nome in **temperatura di conferma negata**.
          1. Lasciare la condizione di **conferma negata** esistente.
          1. Aggiungere una nuova condizione: **digitare** la  >  **temperatura parametri richiesti**  >  .
          1. Aggiungere una nuova azione: **digitare**  >  **Invia risposta vocale**  >  **nessun problema. Qual è la temperatura?**
          1. Modificare il valore di **stato post-esecuzione** predefinito per **attendere l'input dell'utente**.

> [!IMPORTANT]
> In questo articolo viene usata la funzionalità di conferma predefinita. È anche possibile aggiungere manualmente le regole di interazione una alla volta.
   
Provare le modifiche selezionando **Train**. Al termine del training, selezionare **test**.

- **Input**: *impostare la temperatura su 80 gradi*
- **Output**: impostare la temperatura su 80 gradi?
- **Input**: *No*
- **Output**: nessun problema. Quale temperatura?
- **Input**: *72 gradi*
- **Output**: impostare la temperatura su 72 gradi?
- **Input**: *Sì*
- **Output**: OK, impostazione della temperatura su 72 gradi

### <a name="implement-corrections-in-a-command"></a>Implementare le correzioni in un comando

In questa sezione verrà configurata una correzione in un solo passaggio. Questa correzione viene utilizzata dopo l'esecuzione dell'azione di evasione. Viene inoltre visualizzato un esempio di come una correzione è abilitata per impostazione predefinita se il comando non è ancora stato completato. Per aggiungere una correzione quando il comando non è terminato, aggiungere il nuovo parametro `AlarmTone` .

Nel riquadro sinistro selezionare il comando **sealarm** . Quindi, aggiungere il nuovo parametro **AlarmTone**.
        
- **Nome** > `AlarmTone`
- **Tipo**  >  di **Stringa** di
- **Valore predefinito**  >  **Chimes**
- **Configurazione**  >  di **Accetta valori di input predefiniti dal catalogo interno**
- Valori di input **predefiniti**  >  **Chimes**, **Jingle** e **echo** (questi valori sono singoli input predefiniti).


Aggiornare quindi la risposta per il parametro **DateTime** per **impostare la sveglia con Tone As {AlarmTone}. Per quanto tempo?**. Modificare quindi la regola di completamento come indicato di seguito:

1. Selezionare la regola di completamento esistente **ConfirmationResponse**.
1. Nel riquadro a destra passare il puntatore del mouse sull'azione esistente e selezionare **modifica**.
1. Aggiornare la risposta vocale a `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` .

> [!IMPORTANT]
> Il segnale acustico può variare senza alcuna configurazione esplicita in un comando in corso. Ad esempio, può cambiare quando il comando non è stato ancora completato. Una correzione è abilitata per *impostazione predefinita* per tutti i parametri del comando, indipendentemente dal numero di giri, se il comando è ancora da soddisfare.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Implementare una correzione al termine di un comando

La piattaforma comandi personalizzati consente la correzione in un solo passaggio anche al termine del comando. Questa funzionalità non è abilitata per impostazione predefinita. Deve essere configurata in modo esplicito. 

Per configurare una correzione in un solo passaggio, attenersi alla procedura seguente:

1. Nel comando **sealarm** aggiungere una regola di interazione del tipo **Aggiorna comando precedente** per aggiornare l'allarme impostato in precedenza. Rinominare la regola di interazione come **aggiornamento precedente allarme**.
1. Lascia la condizione predefinita: **è necessario aggiornare il comando precedente**.
1. Aggiungere una nuova condizione: **digitare** un  >  **parametro obbligatorio**  >  **DateTime**.
1. Aggiungere una nuova azione: **digitare**  >  **Invia risposta vocale**  >  **Editor semplice**  >  **aggiornamento precedente tempo di allarme a {DateTime}**.
1. Lasciare il valore **dello stato di post-esecuzione** predefinito come **comando completato**.

Provare le modifiche selezionando **Train**. Attendere il completamento del training, quindi selezionare **test**.

- **Input**: *impostare un allarme.*
- **Output**: pronto per impostare l'allarme con un segnale acustico. Per quanto tempo?
- **Input**: *impostare un allarme con il tono come jingle per le 9.00 di domani.*
- **Output**: OK, l'avviso è impostato su 2020-05-21 09:00:00. Il tono di allarme è Jingle.
- **Input**: *No, 08:00.*
- **Output**: aggiornamento dell'ora di allarme precedente a 2020-05-29 08:00.

> [!NOTE]
> In un'applicazione reale, nella sezione **azioni** di questa regola di correzione, sarà anche necessario restituire un'attività al client o chiamare un endpoint HTTP per aggiornare l'ora di allarme nel sistema. Questa azione deve essere responsabile solo dell'aggiornamento dell'ora di allarme. Non dovrebbe essere responsabile di altri attributi del comando. In questo caso, l'attributo corrisponderà al tono di allarme.

## <a name="add-language-generation-templates-for-speech-responses"></a>Aggiungere modelli di generazione della lingua per le risposte vocali

I modelli di generazione della lingua (LG) consentono di personalizzare le risposte inviate al client. Introducono la varianza nelle risposte. È possibile ottenere la generazione della lingua usando:

* Modelli di generazione della lingua.
* Espressioni adattive.

I modelli di comandi personalizzati sono basati sui [modelli LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)di bot Framework. Poiché la funzionalità comandi personalizzati crea un nuovo modello LG quando richiesto (per risposte vocali in parametri o azioni), non è necessario specificare il nome del modello LG. 

Non è quindi necessario definire il modello come segue:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

È invece possibile definire il corpo del modello senza il nome, come indicato di seguito:

> [!div class="mx-imgBorder"]
> ![Screenshot che illustra un esempio di editor di modelli.](./media/custom-commands/template-editor-example.png)


Questa modifica introduce la variazione nelle risposte vocali inviate al client. Per un enunciato, la risposta vocale corrispondente viene selezionata in modo casuale dalle opzioni fornite.

Sfruttando i vantaggi dei modelli LG, è anche possibile definire risposte vocali complesse per i comandi usando espressioni adattive. Per ulteriori informazioni, vedere il [formato dei modelli LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). 

Per impostazione predefinita, la funzionalità Custom Commands supporta tutte le funzionalità, con le seguenti differenze minime:

* Nei modelli LG le entità sono rappresentate come `${entityName}` . La funzionalità comandi personalizzati non utilizza entità. È tuttavia possibile usare i parametri come variabili con la `${parameterName}` rappresentazione o la `{parameterName}` rappresentazione.
* La funzionalità comandi personalizzati non supporta la composizione e l'espansione dei modelli, perché non si modifica mai direttamente il file con *estensione LG* . Si modificano solo le risposte dei modelli creati automaticamente.
* La funzionalità comandi personalizzati non supporta le funzioni personalizzate inserite da LG. Sono supportate le funzioni predefinite.
* La funzionalità comandi personalizzati non supporta opzioni, ad esempio `strict` , `replaceNull` e `lineBreakStyle` .

### <a name="add-template-responses-to-a-turnonoff-command"></a>Aggiungere risposte al modello a un comando TurnOnOff

Modificare il `TurnOnOff` comando per aggiungere un nuovo parametro. Utilizzare la configurazione seguente.

| Impostazione            | Valore consigliato       | 
| ------------------ | --------------------- | 
| **Nome**               | `SubjectContext`         | 
| **È globale**          | Deselezionato             | 
| **Obbligatorio**           | Deselezionato               | 
| **Tipo**               | **Stringa**                |
| **Valore predefinito**      | `all` |
| **Configuration**      | **Accetta valori di input predefiniti dal catalogo interno** | 
| **Valori di input predefiniti** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Modificare una regola di completamento

Modificare la sezione **azioni** della regola di completamento esistente **ConfirmationResponse**. Nella finestra **Modifica azione** passare all' **Editor modello**. Sostituire quindi il testo con l'esempio seguente.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Eseguire il training e il test dell'applicazione usando l'input e l'output seguenti. Si noti la variazione delle risposte. La variazione viene creata da più alternative del valore del modello, nonché dall'utilizzo di espressioni adattive.

* Input: *accendere la TV*
* Output: OK, attivazione della TV
* Input: *accendere la TV*
* Output: completato, acceso alla TV
* Input: *spegnere le spie*
* Output: OK, spegnimento di tutte le luci
* Input: *Disattiva le luci dello spazio*
* Output: OK, spegnimento delle luci dello spazio

## <a name="use-a-custom-voice"></a>Usare una voce personalizzata

Un altro modo per personalizzare le risposte ai comandi personalizzati consiste nel selezionare una voce di output. Usare la procedura seguente per passare la voce predefinita a una voce personalizzata:

1. Nell'applicazione comandi personalizzati, nel riquadro a sinistra, selezionare **Impostazioni**.
1. Nel riquadro centrale selezionare **Voice personalizzata**.
1. Nella tabella selezionare una voce personalizzata o una voce pubblica.
1. Selezionare **Salva**.

> [!div class="mx-imgBorder"]
> ![Screenshot che illustra le frasi di esempio e i parametri.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> Per le voci pubbliche, i tipi neurali sono disponibili solo per aree specifiche. Per altre informazioni, vedere [aree supportate per il servizio vocale](./regions.md#standard-and-neural-voices).
>
> È possibile creare voci personalizzate nella pagina del progetto **Voice personalizzato** . Per altre informazioni, vedere [Introduzione alla voce personalizzata](./how-to-custom-voice.md).

A questo punto, l'applicazione risponderà alla voce selezionata, anziché alla voce predefinita.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [integrare l'applicazione dei comandi personalizzati](how-to-custom-commands-setup-speech-sdk.md) con un'app client usando l'SDK di riconoscimento vocale.
* [Configurare la distribuzione continua](how-to-custom-commands-deploy-cicd.md) per l'applicazione Commands personalizzata usando Azure DevOps. 
                      
