---
title: 'Procedura: sviluppare applicazioni di comandi personalizzati-servizio di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questa procedura viene illustrato come sviluppare e personalizzare le applicazioni di comandi personalizzati. I comandi personalizzati semplificano la creazione di app avanzate per l'esecuzione di comandi vocali ottimizzate per esperienze di interazione vocali ed è la soluzione più adatta per gli scenari di completamento delle attività, di comando e di controllo, in particolare per i dispositivi Internet delle cose (Internet), l'ambiente e i dispositivi senza intestazioni.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632957"
---
# <a name="develop-custom-commands-applications"></a>Sviluppare applicazioni di comandi personalizzati

In questa procedura si apprenderà come sviluppare e configurare le applicazioni di comandi personalizzati. I comandi personalizzati semplificano la creazione di app avanzate per l'esecuzione di comandi vocali ottimizzate per esperienze di interazione vocali ed è la soluzione più adatta per gli scenari di completamento delle attività, di comando e di controllo, in particolare per i dispositivi Internet delle cose (Internet), l'ambiente e i dispositivi senza intestazioni.

In questo articolo viene creata un'applicazione in grado di attivare e disattivare una TV, impostare la temperatura e impostare un allarme. Dopo aver creato questi comandi di base, vengono analizzate le opzioni seguenti per la personalizzazione dei comandi:

* Aggiunta di parametri ai comandi
* Aggiunta di configurazioni ai parametri del comando
* Creazione di regole di interazione
* Creazione di modelli di generazione della lingua per risposte vocali
* Uso della voce personalizzata 

## <a name="create-application-with-simple-commands"></a>Creare un'applicazione con comandi semplici

Per prima cosa, iniziare creando un'applicazione di comandi personalizzati vuota. Per informazioni dettagliate, vedere la [Guida introduttiva](quickstart-custom-commands-application.md). Questa volta, invece di importare un progetto, viene creato un progetto vuoto.

1. Nella casella **nome** immettere il nome `Smart-Room-Lite` del progetto (o un altro elemento di propria scelta).
1. Nell'elenco **lingua** selezionare **inglese (Stati Uniti)**.
1. Selezionare o creare una risorsa LUIS di propria scelta.

   > [!div class="mx-imgBorder"]
   > ![Creare un progetto](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Aggiornare le risorse LUIS (facoltativo)

È possibile aggiornare la risorsa di creazione selezionata nella finestra **nuovo progetto** e impostare una risorsa di stima. La risorsa di stima viene utilizzata per il riconoscimento quando viene pubblicata l'applicazione dei comandi personalizzati. Non è necessaria una risorsa di stima durante le fasi di sviluppo e test.

### <a name="add-turnon-command"></a>Aggiungi comando accendere

Nell'applicazione dei comandi personalizzati **Smart Room-Lite** vuota appena creata, aggiungere un semplice comando che elabora un enunciato, `turn on the tv` e risponde con il messaggio `Ok, turning the tv on` .

1. Creare un nuovo comando selezionando **nuovo comando** nella parte superiore del riquadro sinistro. Verrà visualizzata la finestra **nuovo comando** .
1. Specificare il valore per il campo **nome** come **accendere**.
1. Selezionare **Crea**.

Il riquadro centrale elenca le diverse proprietà del comando. È possibile configurare le proprietà seguenti del comando. Per la spiegazione di tutte le proprietà di configurazione di un comando, vedere [riferimenti](./custom-commands-references.md).

| Configurazione            | Descrizione                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Frasi di esempio** | Espressioni di esempio che l'utente può pronunciare per attivare questo comando                                                                 |
| **Parameters**       | Informazioni necessarie per completare il comando                                                                                |
| **Regole di completamento** | Azioni da intraprendere per completare il comando. Ad esempio, per rispondere all'utente o comunicare con un altro servizio Web. |
| **Regole di interazione**   | Regole aggiuntive per la gestione di situazioni più specifiche o complesse                                                              |


> [!div class="mx-imgBorder"]
> ![Creazione di un comando](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Aggiungi frasi di esempio

Si inizierà con la sezione **frasi di esempio** e si fornirà un esempio di ciò che l'utente può pronunciare.

1. Selezionare la sezione **frasi di esempio** nel riquadro centrale.
1. Nel riquadro più a destra aggiungere esempi:

    ```
    turn on the tv
    ```

1.  Selezionare **Salva** nella parte superiore del riquadro.

Per il momento non sono disponibili parametri, quindi è possibile passare alla sezione **regole di completamento** .

#### <a name="add-a-completion-rule"></a>Aggiungere una regola di completamento

Successivamente, il comando deve avere una regola di completamento. Questa regola indica all'utente che viene eseguita un'azione di evasione. Per altre informazioni sulle regole e sulle regole di completamento, vedere [riferimenti](./custom-commands-references.md).

1. Selezionare la regola di completamento predefinita **eseguita** e modificarla come indicato di seguito:

    
    | Impostazione    | Valore consigliato                          | Descrizione                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nome**       | ConfirmationResponse                  | Nome che descrive lo scopo della regola          |
    | **Condizioni** | Nessuno                                     | Condizioni che determinano quando la regola può essere eseguita    |
    | **Actions**    | Inviare risposta vocale > semplice editor > prima variazione > `Ok, turning the tv on` | Azione da eseguire quando la condizione della regola è true |

   > [!div class="mx-imgBorder"]
   > ![Creare una risposta vocale](media/custom-commands/create-speech-response-action.png)

1. Selezionare **Save (Salva** ) per salvare l'azione.
1. Nella sezione **regole di completamento** selezionare **Salva** per salvare tutte le modifiche. 

    > [!NOTE]
    > Non è necessario usare la regola di completamento predefinita fornita con il comando. Se necessario, è possibile eliminare la regola di completamento predefinita esistente e aggiungere una regola personalizzata.

### <a name="add-settemperature-command"></a>Aggiungi comando setemperature

A questo punto, aggiungere un altro comando **setemperature** che accettano un singolo enunciato, `set the temperature to 40 degrees` e rispondono con il messaggio `Ok, setting temperature to 40 degrees` .

Seguire i passaggi illustrati per il comando **accendere** per creare un nuovo comando usando la frase di esempio "**impostare la temperatura su 40 gradi**".

Modificare quindi le regole di completamento **eseguite** esistenti come indicato di seguito:

| Impostazione    | Valore consigliato                          |
| ---------- | ---------------------------------------- |
| Nome  | ConfirmationResponse                  |
| Condizioni | Nessuno                                     |
| Actions    | Inviare risposta vocale > semplice editor > prima variazione > `Ok, setting temperature to 40 degrees` |

Selezionare **Save (Salva** ) per salvare tutte le modifiche apportate al comando.

### <a name="add-setalarm-command"></a>Aggiungi comando sealarm

Creare un nuovo comando **sealarm** usando la frase di esempio "**impostare un allarme per le 9.00 domani**". Modificare quindi le regole di completamento **eseguite** esistenti come indicato di seguito:

| Impostazione    | Valore consigliato                          |
| ---------- | ---------------------------------------- |
| Nome regola  | ConfirmationResponse                  |
| Condizioni | Nessuno                                     |
| Actions    | Inviare risposta vocale > semplice editor > prima variazione >`Ok, setting an alarm for 9 am tomorrow` |

Selezionare **Save (Salva** ) per salvare tutte le modifiche apportate al comando.

### <a name="try-it-out"></a>Provare questa operazione

Testare il comportamento usando il pannello test chat. Selezionare l'icona del **Training** presente nella parte superiore del riquadro destro. Al termine del training, selezionare **test**. Provare gli esempi di espressioni seguenti tramite voce o testo:

- Digitare: impostare la temperatura su 40 gradi
- Risposta prevista: OK, impostazione della temperatura su 40 gradi
- Digitare: accendere la TV
- Risposta prevista: OK, attivazione della TV
- Digitare: impostare un allarme per le 09:00 domani
- Risposta prevista: OK, impostazione di un allarme per le 09:00 domani

> [!div class="mx-imgBorder"]
> ![Test con web chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> Nel pannello test è possibile selezionare **Turn Details (dettagli** ) per informazioni su come è stato elaborato questo input voce/testo.

## <a name="add-parameters-to-commands"></a>Aggiungere parametri ai comandi

In questa sezione viene illustrato come aggiungere parametri ai comandi. I parametri sono le informazioni richieste dai comandi per completare un'attività. In scenari complessi, i parametri possono essere usati anche per definire le condizioni che attivano azioni personalizzate.

### <a name="configure-parameters-for-turnon-command"></a>Configurare i parametri per il comando accendere

Per iniziare, modificare il comando **accendere** esistente per attivare e disattivare più dispositivi.

1. Ora che il comando gestirà gli scenari sia on che off, rinominare il comando in **TurnOnOff**.
   1. Nel riquadro sinistro selezionare il comando **accendere** , quindi fare clic sul pulsante con i puntini di sospensione (...) accanto a **nuovo comando** nella parte superiore del riquadro.
   
   1. Selezionare **Rinomina**. Nella finestra **Rinomina comando** modificare **nome** in **TurnOnOff**.

1. Successivamente, si aggiunge un nuovo parametro a questo comando che indica se l'utente vuole attivare o disattivare il dispositivo.
   1. Selezionare  **Aggiungi** presente nella parte superiore del riquadro centrale. Dall'elenco a discesa selezionare Parameter ( **parametro**).
   1. Nel riquadro di destra, nella sezione **parametri** , aggiungere il valore nella casella **nome** come **OnOff**.
   1. Selezionare **required**. Nella finestra **Aggiungi risposta per un parametro obbligatorio** selezionare **Editor semplice**. Nella **prima variante** aggiungere
        ```
        On or Off?
        ```
   1. Selezionare **Aggiorna**.

       > [!div class="mx-imgBorder"]
       > ![Crea risposta parametro obbligatoria](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. A questo punto vengono configurate le proprietà dei parametri. Per la spiegazione di tutte le proprietà di configurazione di un comando, vedere [riferimenti](./custom-commands-references.md). Configurare le proprietà del parametro come indicato di seguito:
      

       | Configurazione      | Valore consigliato     | Descrizione                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nome               | `OnOff`           | Nome descrittivo per il parametro                                                                           |
       | È globale          | unchecked       | Casella di controllo che indica se un valore per questo parametro viene applicato globalmente a tutti i comandi nell'applicazione|
       | Obbligatorio           | checked         | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
       | Risposta per il parametro obbligatorio      |Editor semplice > `On or Off?`      | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto |
       | Type               | string          | Tipo di parametro, ad esempio numero, stringa, data/ora o geografia   |
       | Configurazione      | Accetta valori di input predefiniti dal catalogo interno | Per le stringhe, questo limita gli input a un set di valori possibili. |
       | Valori di input predefiniti     | `on`, `off`           | Set di valori possibili e relativi alias         |
       
        
   1. Per aggiungere valori di input predefiniti, selezionare **Aggiungi un input predefinito** e nella finestra **nuovo elemento**  digitare **nome** come indicato nella tabella precedente. In questo caso, gli alias non vengono usati, quindi è possibile lasciarli vuoti.
   
      > [!div class="mx-imgBorder"]
      > ![Crea parametro](media/custom-commands/create-on-off-parameter.png)

   1. Selezionare **Save (Salva** ) per salvare tutte le configurazioni del parametro.
 
#### <a name="add-subjectdevice-parameter"></a>Aggiungi parametro SubjectDevice 

1. Quindi, selezionare di nuovo **Aggiungi** per aggiungere un secondo parametro per rappresentare il nome dei dispositivi che possono essere controllati con questo comando. Utilizzare la configurazione seguente.


    | Impostazione            | Valore consigliato       |
    | ------------------ | --------------------- |
    | Nome               | `SubjectDevice`         |
    | È globale          | unchecked             |
    | Obbligatorio           | checked               |
    | Risposta per il parametro obbligatorio     | Editor semplice > `Which device do you want to control?`    | 
    | Type               | string                |          |
    | Configurazione      | Accetta valori di input predefiniti dal catalogo interno | 
    | Valori di input predefiniti | `tv`, `fan`               |
    | Alias ( `tv` )      | `television`, `telly`     |

1. Selezionare **Salva**

#### <a name="modify-example-sentences"></a>Modificare le frasi di esempio

Per i comandi con parametri, è utile aggiungere frasi di esempio che coprono tutte le possibili combinazioni. Ad esempio:

* Informazioni complete sui parametri- `turn {OnOff} the {SubjectDevice}`
* Informazioni sui parametri parziali- `turn it {OnOff}`
* Nessuna informazione sui parametri- `turn something`

Le frasi di esempio con diversi livelli di informazioni consentono all'applicazione di comandi personalizzati di risolvere sia le risoluzioni unidirezionali sia le risoluzioni a più turni con informazioni parziali.

Tenendo presente questo aspetto, modificare le frasi di esempio in modo da usare i parametri come indicato di seguito:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Selezionare **Salva**.

> [!TIP]
> Nell'editor frasi di esempio usare le parentesi graffe per fare riferimento ai parametri. - `turn {OnOff} the {SubjectDevice}` Usare TAB per il completamento automatico supportato da parametri creati in precedenza.

#### <a name="modify-completion-rules-to-include-parameters"></a>Modificare le regole di completamento per includere i parametri

Modificare la regola di completamento esistente **ConfirmationResponse**.

1. Nella sezione **condizioni** selezionare **Aggiungi una condizione**.
1. Nella finestra **nuova condizione** selezionare **parametri obbligatori** nell'elenco **tipo** . Nell'elenco di controllo seguente selezionare sia **OnOff** che **SubjectDevice**.
1. Lasciare **l'** opzione come deselezionata.
1. Selezionare **Crea**.
1. Nella sezione **azioni** modificare l'azione **Invia risposta vocale** esistente passando il puntatore del mouse sull'azione e selezionando il pulsante modifica. Questa volta, usare i parametri **OnOff** e **SubjectDevice** appena creati

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Selezionare **Salva**.

Provare le modifiche selezionando l'icona del **Training** nella parte superiore del riquadro destro. Al termine del training, selezionare **test**. Verrà visualizzato un test della finestra **dell'applicazione** . Provare le interazioni seguenti.

- Input: spegnere la TV
- Output: OK, spegnimento della TV
- Input: spegnere la televisione
- Output: OK, spegnimento della TV
- Input: Disattiva
- Output: quale dispositivo si vuole controllare?
- Input: TV
- Output: OK, spegnimento della TV

### <a name="configure-parameters-for-settemperature-command"></a>Configurare i parametri per il comando setemperature

Modificare il comando **setemperature** per abilitarlo a impostare la temperatura come indicato dall'utente.

Aggiungere la **temperatura** del nuovo parametro con la configurazione seguente

| Configurazione      | Valore consigliato     |
| ------------------ | ----------------|
| Nome               | `Temperature`           |
| Obbligatorio           | checked         |
| Risposta per il parametro obbligatorio      | Editor semplice > `What temperature would you like?`
| Tipo               | Numero          |


Modificare le espressioni di esempio con i valori seguenti.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Modificare le regole di completamento esistenti in base alla configurazione seguente.

| Configurazione      | Valore consigliato     |
| ------------------ | ----------------|
| Condizioni         | Temperatura > parametro obbligatorio           |
| Actions           | Invia risposta vocale > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-to-the-setalarm-command"></a>Configurare i parametri per il comando sealarm

Aggiungere un parametro denominato **DateTime** con la configurazione seguente.

   | Impostazione                           | Valore consigliato                     | 
   | --------------------------------- | ----------------------------------------|
   | Nome                              | `DateTime`                               |
   | Obbligatorio                          | checked                                 |
   | Risposta per il parametro obbligatorio   | Editor semplice > `For what time?`            | 
   | Tipo                              | Datetime                                |
   | Impostazioni predefinite data                     | Se la data non è presente, usare oggi            |
   | Impostazioni predefinite temporali                     | Se l'ora non è presente, usare l'inizio della giornata     |


> [!NOTE]
> In questo articolo sono stati usati principalmente i tipi di parametro String, Number e DateTime. Per un elenco di tutti i tipi di parametro supportati e delle relative proprietà, passare a [References](./custom-commands-references.md).


Modificare le espressioni di esempio con i valori seguenti.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Modificare le regole di completamento esistenti in base alla configurazione seguente.

   | Impostazione    | Valore consigliato                               |
   | ---------- | ------------------------------------------------------- |
   | Actions    | Invia risposta vocale- `Ok, alarm set for {DateTime}`  |

Testare tutti e tre i comandi insieme usando le espressioni correlate a comandi diversi. Si noti che è possibile passare da un comando all'altro.

- Input: impostare un allarme
- Output: per quanto tempo?
- Input: accendere la TV
- Output: OK, attivazione della TV
- Input: impostare un allarme
- Output: per quanto tempo?
- Input: 17.00
- Output: OK, set di avvisi per 2020-05-01 17:00:00

## <a name="add-configurations-to-commands-parameters"></a>Aggiungere configurazioni ai parametri dei comandi

In questa sezione vengono fornite ulteriori informazioni sulla configurazione dei parametri avanzati, tra cui:

 - Come i valori dei parametri possono appartenere a un set definito esternamente all'applicazione di comandi personalizzati
 - Come aggiungere clausole di convalida sul valore dei parametri

### <a name="configure-parameter-as-external-catalog-entity"></a>Configurare il parametro come entità del catalogo esterno

I comandi personalizzati consentono di configurare parametri di tipo stringa per fare riferimento a cataloghi esterni ospitati su un endpoint Web. Ciò consente di aggiornare il catalogo esterno in modo indipendente senza apportare modifiche all'applicazione comandi personalizzati. Questo approccio è utile nei casi in cui le voci del catalogo possono essere di grandi dimensioni.

Riutilizzare il parametro **SubjectDevice** del comando **TurnOnOff** . La configurazione corrente per questo parametro è **accettare gli input predefiniti dal catalogo interno**. Si riferisce all'elenco statico di dispositivi, come definito nella configurazione dei parametri. Si vuole spostare questo contenuto in un'origine dati esterna che può essere aggiornata in modo indipendente.

Per eseguire questa operazione, iniziare aggiungendo un nuovo endpoint Web. Passare alla sezione **endpoint Web** nel riquadro a sinistra e aggiungere un nuovo endpoint Web con la configurazione seguente.

| Impostazione | Valore consigliato |
|----|----|
| Nome | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| Metodo | GET |


Se il valore suggerito per l'URL non è corretto, è necessario configurare e ospitare un semplice endpoint Web che restituisce un JSON costituito dall'elenco dei dispositivi che possono essere controllati. L'endpoint Web deve restituire un formato JSON come segue:
    
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

Passare quindi alla pagina delle impostazioni dei parametri **SubjectDevice** e modificare le proprietà nel modo seguente.

| Impostazione | Valore consigliato |
| ----| ---- |
| Configurazione | Accetta input predefiniti dal catalogo esterno |                               
| Endpoint Catalogo | getDevices |
| Metodo | GET |

Selezionare quindi **Salva**.

> [!IMPORTANT]
> Non verrà visualizzata un'opzione per configurare un parametro per accettare gli input da un catalogo esterno, a meno che non sia stato impostato l'endpoint Web nella sezione **endpoint Web** del riquadro sinistro.

Per provarlo, selezionare **Train** e attendere il completamento del training. Al termine del training, selezionare **test** e provare alcune interazioni.

* Input: attiva
* Output: quale dispositivo si vuole controllare?
* Input: luci
* Output: OK, accensione delle luci

> [!NOTE]
> Si noti che è possibile controllare ora tutti i dispositivi ospitati nell'endpoint Web. È comunque necessario eseguire il training dell'applicazione per testare le nuove modifiche e ripubblicare l'applicazione.

### <a name="add-validation-to-parameters"></a>Aggiungere la convalida ai parametri

Le **convalide** sono costrutti applicabili a determinati tipi di parametro che consentono di configurare vincoli sul valore del parametro e di richiedere la correzione se i valori non rientrano nei vincoli. Per un elenco completo dei tipi di parametro che estendono il costrutto di convalida, vedere [riferimenti](./custom-commands-references.md)

Testare le convalide usando il comando **setemperature** . Usare la procedura seguente per aggiungere una convalida per il parametro **temperature** .

1. Selezionare il comando **setemperature** nel riquadro sinistro.
1. Selezionare  **temperatura** nel riquadro centrale.
1. Selezionare **Aggiungi una convalida** presente nel riquadro di destra.
1. Nella finestra **nuova convalida** configurare la convalida come indicato di seguito e selezionare **Crea**.


    | Configurazione parametri | Valore consigliato | Descrizione |
    | ---- | ---- | ---- |
    | Min Value (Valore minimo) | `60` | Per i parametri number, il valore minimo che questo parametro può assumere |
    | Max Value (Valore massimo) | `80` | Per i parametri number, il valore massimo che questo parametro può assumere |
    | Risposta non riuscita |  Editor semplice > prima variante > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Richiedi un nuovo valore se la convalida ha esito negativo |

    > [!div class="mx-imgBorder"]
    > ![Aggiungere una convalida dell'intervallo](media/custom-commands/add-validations-temperature.png)

Provare a eseguire l'esercitazione selezionando l'icona del **Training** presente nella parte superiore del riquadro destro. Al termine del training, selezionare **test** e provare alcune interazioni:

- Input: impostare la temperatura su 72 gradi
- Output: OK, impostazione della temperatura su 72 gradi
- Input: impostare la temperatura su 45 gradi
- Output: è possibile impostare la temperatura solo tra 60 e 80 gradi
- Input: renderlo 72 gradi
- Output: OK, impostazione della temperatura su 72 gradi

## <a name="add-interaction-rules"></a>Aggiungere regole di interazione

Le regole di interazione sono *regole aggiuntive* per gestire situazioni specifiche o complesse. Sebbene sia possibile creare regole di interazione personalizzate, in questo esempio si usano le regole di interazione per gli scenari di destinazione seguenti:

* Conferma di comandi
* Aggiunta di una correzione in un passaggio ai comandi

Per altre informazioni sulle regole di interazione, vedere la sezione [riferimenti](./custom-commands-references.md) .

### <a name="add-confirmations-to-a-command"></a>Aggiungere conferme a un comando

Per aggiungere una conferma, utilizzare il comando **setemperature** . Per ottenere la conferma, è possibile creare regole di interazione attenendosi alla procedura seguente.

1. Selezionare il comando **setemperature** nel riquadro sinistro.
1. Aggiungere regole di interazione selezionando **Aggiungi** nel riquadro centrale. Quindi selezionare il  >  **comando conferma** regole di interazione.

    Questa azione aggiunge tre regole di interazione che chiederanno all'utente di confermare la data e l'ora dell'allarme ed è prevista una conferma (sì/no) per la prossima volta.

    1. Modificare la regola di interazione del **comando Confirm** in base alla configurazione seguente:
        1. Rinominare il **nome** per **confermare la temperatura**.
        1. Aggiungere una nuova condizione come temperatura dei **parametri obbligatori**  >  .
        1. Aggiungere una nuova azione come **tipo**  >  **Invia risposta vocale**  >  **impostare la temperatura come {temperatura} gradi?**
        1. Lasciare il valore predefinito della **conferma prevista dall'utente** nella sezione delle **aspettative** .
      
         > [!div class="mx-imgBorder"]
         > ![Crea risposta parametro obbligatoria](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modificare la regola di interazione **conferma riuscita** per gestire una conferma riuscita (utente detto sì).
      
          1. Modificare il **nome** in **temperatura di conferma completata**.
          1. Lasciare la condizione di **conferma** già esistente riuscita.
          1. Aggiungere una nuova condizione come **tipo**  >  **temperatura parametri richiesta**  >  .
          1. Lasciare il valore predefinito **stato post-esecuzione** come **regole di completamento esecuzione**.

    1. Modificare la regola di interazione di **conferma negata** per gestire gli scenari quando la conferma viene negata (l'utente ha detto no).

          1. Modificare il **nome** in **temperatura di conferma negata**.
          1. Lasciare la condizione di **conferma già esistente negata** .
          1. Aggiungere una nuova condizione come **tipo**  >  **temperatura parametri richiesta**  >  .
          1. Aggiungere una nuova azione come **tipo**  >  **Invia risposta vocale**  >  **nessun problema. Quale temperatura?**
          1. Lasciare il valore predefinito di **stato post-esecuzione** in **attesa dell'input dell'utente**.

> [!IMPORTANT]
> In questo articolo è stata usata la funzionalità di conferma predefinita. È anche possibile aggiungere manualmente le regole di interazione una alla volta.
   
Provare le modifiche selezionando **Train**, attendi il completamento del training e selezionare **test**.

- **Input**: impostare la temperatura su 80 gradi
- **Output**: impostare la temperatura su 80 gradi?
- **Input**: No
- **Output**: nessun problema. Quale temperatura?
- **Input**: 72 gradi
- **Output**: impostare la temperatura su 72 gradi?
- **Input**: Sì
- **Output**: OK, impostazione della temperatura su 83 gradi

### <a name="implement-corrections-in-a-command"></a>Implementare le correzioni in un comando

In questa sezione viene configurata una correzione in un solo passaggio, che viene usata dopo che l'azione di evasione è già stata eseguita. Viene inoltre visualizzato un esempio di come una correzione è abilitata per impostazione predefinita, nel caso in cui il comando non venga ancora completato. Per aggiungere una correzione quando il comando non è stato completato, aggiungere il nuovo parametro **AlarmTone**.

Selezionare il comando **sealarm** dal riquadro sinistro e aggiungere il nuovo parametro **AlarmTone**.
        
- **Nome**  >  **AlarmTone**
- **Tipo**  >  di **Stringa** di
- **Valore predefinito**  >  **Chimes**
- **Configurazione**  >  di **Accetta valori di input predefiniti dal catalogo interno**
- Valori di input **predefiniti**  >  **Chimes**, **Jingle** e **echo** come singoli input predefiniti


Aggiornare quindi la risposta per il parametro **DateTime** per **impostare la sveglia con Tone As {AlarmTone}. Per quanto tempo?**. Modificare quindi la regola di completamento come indicato di seguito:

1. Selezionare la regola di completamento esistente **ConfirmationResponse**.
1. Nel riquadro destro passare il puntatore del mouse sull'azione esistente e selezionare **modifica**.
1. Aggiornare la risposta vocale a **OK, set di allarme per {DateTime}. Il tono di allarme è {AlarmTone}.**

> [!IMPORTANT]
> Il tono di allarme potrebbe essere modificato senza alcuna configurazione esplicita in un comando continuo, ad esempio quando il comando non è stato ancora completato. *Una correzione è abilitata per impostazione predefinita per tutti i parametri del comando, indipendentemente dal numero di turni se il comando è ancora da soddisfare.*

#### <a name="correction-when-command-is-completed"></a>Correzione quando il comando viene completato

La piattaforma Custom Commands fornisce anche la funzionalità per la correzione di un solo passaggio anche quando il comando è stato completato. Questa funzionalità non è abilitata per impostazione predefinita. Deve essere configurata in modo esplicito. Usare la procedura seguente per configurare una correzione in un solo passaggio.

1. Nel comando **sealarm** aggiungere una regola di interazione del tipo **Aggiorna comando precedente** per aggiornare l'allarme impostato in precedenza. Rinominare il **nome** predefinito della regola di interazione per **aggiornare la sveglia precedente**.
1. Lascia la condizione predefinita il **comando precedente deve essere aggiornato** così com'è.
1. Aggiungere una nuova condizione come **tipo** di  >  **parametro**  >  **DateTime** obbligatorio.
1. Aggiungere una nuova azione come **tipo**  >  **Invia risposta vocale**  >  **semplice editor** che  >  **Aggiorna l'ora di allarme precedente a {DateTime}.**
1. Lasciare il valore predefinito **stato post-esecuzione** come **comando completato**.

Provare le modifiche selezionando **Train**, attendi il completamento del training e selezionare **test**.

- **Input**: impostare un allarme.
- **Output**: pronto per impostare l'allarme con un segnale acustico. Per quanto tempo?
- **Input**: impostare un allarme con il tono come jingle per le 9.00 di domani.
- **Output**: OK, l'avviso è impostato su 2020-05-21 09:00:00. Il tono di allarme è Jingle.
- **Input**: No, 08:00.
- **Output**: aggiornamento dell'ora di allarme precedente a 2020-05-29 08:00.

> [!NOTE]
> In un'applicazione reale, nella sezione **azioni** di questa regola di correzione, sarà anche necessario restituire un'attività al client o chiamare un endpoint HTTP per aggiornare l'ora di allarme nel sistema. Questa azione deve essere responsabile solo dell'aggiornamento dell'ora di allarme e non di altri attributi del comando. In questo caso, si tratta del segnale acustico.

## <a name="add-language-generation-templates-for-speech-responses"></a>Aggiungere modelli di generazione della lingua per le risposte vocali

I modelli di generazione del linguaggio consentono di personalizzare le risposte inviate al client e di introdurre la varianza nelle risposte. La personalizzazione della generazione della lingua può essere eseguita tramite:

* Uso dei modelli di generazione del linguaggio
* Utilizzo di espressioni adattive

I modelli di comandi personalizzati sono basati sui [modelli LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)di botframework (. Poiché i comandi personalizzati creano un nuovo modello LG quando necessario, ovvero per le risposte vocali nei parametri o nelle azioni, non è necessario specificare il nome del modello LG. Quindi, invece di definire il modello come:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

È sufficiente definire il corpo del modello senza il nome, come indicato di seguito.

> [!div class="mx-imgBorder"]
> ![esempio di editor di modelli](./media/custom-commands/template-editor-example.png)


Questa modifica introduce la variazione delle risposte vocali inviate al client. Quindi, per lo stesso enunciato, la risposta vocale corrispondente verrebbe selezionata in modo casuale dalle opzioni fornite.

Sfruttare i vantaggi dei modelli LG consente inoltre di definire risposte vocali complesse per i comandi mediante espressioni adattive. Per altri dettagli, vedere il [formato dei modelli LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) . Per impostazione predefinita, i comandi personalizzati supportano tutte le funzionalità con le differenze minime seguenti:

* Nelle entità dei modelli LG sono rappresentate come $ {EntityName}. Nei comandi personalizzati non vengono usate entità, ma i parametri possono essere usati come variabili con una delle rappresentazioni $ {parameterName} o {ParameterName}
* La composizione e l'espansione del modello non sono supportate nei comandi personalizzati. Questo perché non si modifica mai `.lg` direttamente il file, ma solo le risposte dei modelli creati automaticamente.
* Le funzioni personalizzate inserite da LG non sono supportate nei comandi personalizzati. Le funzioni predefinite sono ancora supportate.
* Le opzioni (Strict, replaceNull & lineBreakStyle) non sono supportate nei comandi personalizzati.

### <a name="add-template-responses-to-turnonoff-command"></a>Aggiungi risposte modello al comando TurnOnOff

Modificare il comando **TurnOnOff** per aggiungere un nuovo parametro con la configurazione seguente:

| Impostazione            | Valore consigliato       | 
| ------------------ | --------------------- | 
| Nome               | `SubjectContext`         | 
| È globale          | unchecked             | 
| Obbligatorio           | unchecked               | 
| Type               | string                |
| Valore predefinito      | `all` |
| Configurazione      | Accetta valori di input predefiniti dal catalogo interno | 
| Valori di input predefiniti | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>Modifica regola di completamento

Modificare la sezione **azioni** della regola di completamento esistente **ConfirmationResponse**. Nella finestra popup **Modifica azione** passare a **Editor modelli** e sostituire il testo con l'esempio seguente.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Eseguire il **Training** e il **test** dell'applicazione come indicato di seguito. Si noti la variazione delle risposte a causa dell'utilizzo di più alternative del valore del modello e anche dell'utilizzo di espressioni adattive.

* Input: accendere la TV
* Output: OK, attivazione della TV
* Input: accendere la TV
* Output: completato, acceso alla TV
* Input: spegnere le spie
* Output: OK, spegnimento di tutte le luci
* Input: disattiva le luci dello spazio
* Output: OK, spegnimento delle luci dello spazio

## <a name="use-custom-voice"></a>Usare la voce personalizzata

Un altro modo per personalizzare le risposte ai comandi personalizzati consiste nel selezionare una voce di output personalizzata. Usare la procedura seguente per passare la voce predefinita a una voce personalizzata.

1. Nell'applicazione comandi personalizzati selezionare **Impostazioni** dal riquadro sinistro.
1. Selezionare **voce personalizzata** dal riquadro centrale.
1. Selezionare la voce personalizzata o pubblica desiderata dalla tabella.
1. Selezionare **Salva**.

> [!div class="mx-imgBorder"]
> ![Frasi di esempio con parametri](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Per le **voci pubbliche**, i **tipi neurali** sono disponibili solo per aree specifiche. Per verificare la disponibilità, vedere [le voci standard e neurali per area/endpoint](./regions.md#standard-and-neural-voices).
> - Per le **voci personalizzate**, è possibile crearle dalla pagina del progetto Voice personalizzato. Vedere [Introduzione alla voce personalizzata](./how-to-custom-voice.md).

A questo punto, l'applicazione risponderà alla voce selezionata, anziché alla voce predefinita.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [integrare l'applicazione dei comandi personalizzati](how-to-custom-commands-setup-speech-sdk.md) con un'app client usando l'SDK di riconoscimento vocale.
* [Configurare la distribuzione continua](how-to-custom-commands-deploy-cicd.md) per l'applicazione di comandi personalizzati con Azure DevOps. 
                      