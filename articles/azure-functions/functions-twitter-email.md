---
title: Creare una funzione che si integra con le app per la logica di Azure
description: Creare un'integrazione di funzione con App per la logica di Azure e Servizi cognitivi di Azure. Il flusso di lavoro risultante classifica i sentiment dei tweet inviando notifiche tramite posta elettronica.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 3517835859de82117de07ad67cdf8027960ab777
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388694"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Esercitazione: Creare una funzione da integrare con App per la logica di Azure

Funzioni di Azure si integra con App per la logica di Azure in Progettazione app per la logica. Questa integrazione consente di usare la potenza di calcolo di Funzioni nelle orchestrazioni con altri servizi di Azure e di terze parti.

Questa esercitazione illustra come creare un flusso di lavoro per analizzare l'attività di Twitter. Quando i tweet vengono valutati, il flusso di lavoro invia notifiche quando vengono rilevati sentimenti positivi.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una risorsa API per Servizi cognitivi.
> * Creare una funzione che classifica i sentiment nei tweet.
> * Creare un'app per la logica che si connette a Twitter.
> * Aggiungere il rilevamento dei sentiment all'app per la logica.
> * Connettere l'app per la logica alla funzione.
> * Inviare un messaggio di posta elettronica in base alla risposta dalla funzione.

## <a name="prerequisites"></a>Prerequisiti

* Un account [Twitter](https://twitter.com/) attivo.
* Un account [Outlook.com](https://outlook.com/) per l'invio delle notifiche.

> [!NOTE]
> Se si vuole usare il connettore Gmail, solo gli account G Suite Business possono usare questo connettore senza restrizioni nelle app per la logica. Se si dispone di un account Gmail consumer, è possibile usare il connettore Gmail solo con app e servizi approvati da Google specifici oppure è possibile [creare un'app client Google da usare per l'autenticazione nel connettore Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). <br><br>Per altre informazioni, vedere [Informative sulla privacy e sulla sicurezza dei dati per i connettori Google in App per la logica di Azure](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-text-analytics-resource"></a>Creare Analisi del testo risorsa

Le API Servizi cognitivi sono disponibili in Azure come singole risorse. Usare l'API Analisi del testo per rilevare il sentiment dei tweet pubblicati.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Crea risorsa** nell'angolo superiore sinistro del portale di Azure.

1. In _Categorie_ selezionare **AI + Machine Learning**

1. In _Analisi del testo_ selezionare **Crea**.

1. Immettere i valori seguenti nella _schermata Crea Analisi del testo_ dati.

    | Impostazione | Valore | Commenti |
    | ------- | ----- | ------- |
    | Subscription | Il nome della sottoscrizione di Azure | |
    | Resource group | Creare un nuovo gruppo di risorse denominato **tweet-sentiment-tutorial** | Successivamente, si elimina questo gruppo di risorse per rimuovere tutte le risorse create durante questa esercitazione. |
    | Region | Selezionare l'area più vicina | |
    | Nome | **TweetSentimentApp** | |
    | Piano tariffario | Selezionare **Free F0 (F0 gratuito)** | |

1. Selezionare **Rivedi e crea**.

1. Selezionare **Crea**.

1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

## <a name="get-text-analytics-settings"></a>Ottenere Analisi del testo predefinite

Con la Analisi del testo creata, si copieranno alcune impostazioni e le si rneranno da parte per un uso successivo.

1. Selezionare **Chiavi ed Endpoint**.

1. Copiare **la chiave 1** facendo clic sull'icona alla fine della casella di input.

1. Incollare il valore in un editor di testo.

1. Copiare **l'endpoint** facendo clic sull'icona alla fine della casella di input.

1. Incollare il valore in un editor di testo.

## <a name="create-the-function-app"></a>Creare l'app per le funzioni

1. Nella casella di ricerca in alto cercare e selezionare App **per le funzioni**.

1. Selezionare **Crea**.

1. Immettere i valori seguenti.

    | Impostazione | Valore consigliato | Commenti |
    | ------- | ----- | ------- |
    | Subscription | Il nome della sottoscrizione di Azure | |
    | Resource group | **tweet-sentiment-tutorial** | Usare lo stesso nome del gruppo di risorse in questa esercitazione. |
    | Nome dell'app per le funzioni | **TweetSentimentAPI** + un suffisso univoco | I nomi delle applicazioni per le funzioni sono univoci a livello globale. I caratteri validi sono `a-z` (senza distinzione tra maiuscole e minuscole), `0-9` e `-`. |
    | Pubblica | **Codice** | |
    | Stack di runtime | **.NET** | Il codice della funzione fornito è in C#. |
    | Versione | Selezionare il numero di versione più recente | |
    | Region | Selezionare l'area più vicina | |

1. Selezionare **Rivedi e crea**.

1. Selezionare **Crea**.

1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

## <a name="create-an-http-triggered-function"></a>Creare una funzione attivata da HTTP  

1. Dal menu a sinistra della _finestra_ Funzioni selezionare **Funzioni**.

1. Selezionare **Aggiungi** dal menu in alto e immettere i valori seguenti.

    | Impostazione | Valore | Commenti |
    | ------- | ----- | ------- |
    | Ambiente di sviluppo | **Sviluppare nel portale** | |
    | Modello | **HTTP Trigger** | |
    | Nuova funzione | **TweetSentimentFunction** | Questo è il nome della funzione. |
    | Livello di autorizzazione | **Funzione** | |

1. Fare clic sul pulsante **Aggiungi**.

1. Selezionare il **pulsante Codice e** test.

1. Incollare il codice seguente nella finestra dell'editor di codice.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    Un punteggio del sentiment viene passato alla funzione , che restituisce un nome di categoria per il valore.

1. Selezionare il **pulsante Salva** sulla barra degli strumenti per salvare le modifiche.

    > [!NOTE]
    > Per testare la funzione, selezionare **Test/Esegui** dal menu superiore. Nella scheda _Input_ immettere un valore nella casella `0.9` Input corpo e quindi selezionare **Esegui**.  Verificare che nella casella Contenuto risposta _HTTP_ della _sezione_ Output sia restituito il _valore_ Positivo.

Creare quindi un'app per la logica che si integra con Funzioni di Azure, Twitter e l'API Servizi cognitivi.

## <a name="create-a-logic-app"></a>Creare un'app per la logica

1. Nella casella di ricerca in alto cercare e selezionare App **per la logica**.

1. Selezionare **Aggiungi**.

1. Selezionare **Consumo** e immettere i valori seguenti.

    | Impostazione | Valore consigliato |
    | ------- | --------------- |
    | Subscription | Il nome della sottoscrizione di Azure |
    | Resource group | **tweet-sentiment-tutorial** |
    | Nome dell'app per la logica | **TweetSentimentApp** |
    | Region | Selezionare l'area più vicina, preferibilmente la stessa area selezionata nei passaggi precedenti. |

    Accettare i valori predefiniti per tutte le altre impostazioni.

1. Selezionare **Rivedi e crea**.

1. Selezionare **Crea**.

1. Al termine della distribuzione, selezionare **Vai alla risorsa**.

1. Selezionare il **pulsante App per la logica** vuota.

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Pulsante App per la logica vuota":::

1. Selezionare il **pulsante Salva** sulla barra degli strumenti per salvare lo stato di avanzamento.

È ora possibile usare Progettazione app per la logica per aggiungere servizi e trigger all'applicazione.

## <a name="connect-to-twitter"></a>Connettersi a Twitter

Creare una connessione a Twitter in modo che l'app possa eseguire il polling di nuovi tweet.

1. Cercare **Twitter nella** casella di ricerca superiore.

1. Selezionare **l'icona di Twitter.**

1. Selezionare il trigger **Quando viene pubblicato un nuovo tweet**.

1. Immettere i valori seguenti per configurare la connessione.

    | Impostazione |  Valore |
    | ------- | ---------------- |
    | Nome connessione | **MyTwitterConnection** |
    | Tipo di autenticazione | **Usare l'applicazione condivisa predefinita** |

1. Selezionare **Accedi**.

1. Seguire le istruzioni nella finestra popup per completare l'accesso a Twitter.

1. Immettere quindi i valori seguenti nella _casella Quando viene pubblicato un nuovo tweet._

    | Impostazione | Valore |
    | ------- | ----- |
    | Testo di ricerca | **#my-twitter-tutorial** |
    | In che modo si vogliono verificare gli elementi? | **15** nella casella di testo e <br> **Minuto nell'elenco** a discesa |

1. Selezionare il **pulsante Salva** sulla barra degli strumenti per salvare lo stato di avanzamento.

Connettersi quindi all'analisi del testo per rilevare il sentiment dei tweet raccolti.

## <a name="add-text-analytics-sentiment-detection"></a>Aggiungere il Analisi del testo del sentiment

1. Selezionare **Nuovo passaggio**.

1. Cercare **Analisi del testo** nella casella di ricerca.

1. Selezionare **l Analisi del testo** icona a forma di freccia.

1. Selezionare **Rileva sentiment** e immettere i valori seguenti.

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome connessione | **TextAnalyticsConnection** |
    | Chiave account | Incollare la chiave Analisi del testo'account di archiviazione impostata in precedenza. |
    | Site URL | Incollare l'endpoint Analisi del testo impostato in precedenza. |

1. Selezionare **Crea**.

1. Fare clic _all'interno della_ casella Aggiungi nuovo parametro e selezionare la casella **accanto** ai documenti visualizzati nel popup.

1. Fare clic _all'interno della casella di testo documents ID - 1 (ID - 1)_ per aprire il popup del contenuto dinamico.

1. Nella casella _di ricerca del contenuto_ dinamico cercare **id** e fare clic su **TWEET ID**.

1. Fare clic all'interno della casella di testo Text - 1 (Testo _- 1)_ per aprire il popup del contenuto dinamico.

1. Nella casella _di ricerca del contenuto_ dinamico cercare testo e fare clic su Testo **tweet**. 

1. In **Scegliere un'azione** digitare **Analisi del testo** e quindi scegliere l'azione **Rileva sentiment**.

1. Selezionare il **pulsante Salva** sulla barra degli strumenti per salvare lo stato di avanzamento.

La _casella Rileva sentiment_ dovrebbe essere simile allo screenshot seguente.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Rilevare le impostazioni del sentiment":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Connettere l'output del sentiment all'endpoint della funzione

1. Selezionare **Nuovo passaggio**.

1. Cercare **Funzioni di Azure** nella casella di ricerca.

1. Selezionare **l'Funzioni di Azure** di testo.

1. Cercare il nome della funzione nella casella di ricerca. Se sono stati seguiti i consigli precedenti, il nome della funzione inizia con **TweetSentimentAPI**.

1. Selezionare l'icona della funzione.

1. Selezionare **l'elemento TweetSentimentFunction.**

1. Fare clic _all'interno della_ casella Corpo richiesta e selezionare l'elemento _Rileva_ punteggio **sentiment** dalla finestra popup.

1. Selezionare il **pulsante Salva** sulla barra degli strumenti per salvare lo stato di avanzamento.

## <a name="add-conditional-step"></a>Aggiungere un passaggio condizionale

1. Selezionare il **pulsante Aggiungi un'azione.**

1. Fare clic _all'interno_ della casella Controllo e cercare **e selezionare Controllo** nella finestra popup.

1. Selezionare **Condizione**.

1. Fare clic _all'interno della casella Scegliere_ un valore e selezionare _l'elemento TweetSentimentFunction_ **Body** (Corpo funzione TweetSentiment) nella finestra popup.

1. Immettere **Positivo** nella _casella Scegliere un_ valore.

1. Selezionare il **pulsante Salva** sulla barra degli strumenti per salvare lo stato di avanzamento.

## <a name="add-email-notifications"></a>Aggiungere le notifiche di posta elettronica

1. Nella casella _True_ selezionare il pulsante **Aggiungi un'azione.**

1. Cercare e selezionare **Office 365 Outlook** nella casella di testo.

1. Cercare Invia **e** selezionare **Invia un messaggio di posta** elettronica nella casella di testo.

1. Selezionare il **pulsante Accedi.**

1. Seguire le istruzioni nella finestra popup per completare l'accesso a Office 365 Outlook.

1. Immettere l'indirizzo di posta elettronica nella _casella_ A.

1. Fare clic _all'interno della_ casella Oggetto e fare clic **sull'elemento Corpo** in _TweetSentimentFunction_. Se _l'elemento_ Corpo non viene visualizzato nell'elenco, fare clic sul **collegamento Altre** informazioni per espandere l'elenco di opzioni.

1. Dopo _l'elemento_ Body (Corpo) in _Subject (Oggetto),_ immettere il **testo Tweet from:**.

1. Dopo il _testo Tweet from:_ (Tweet da): fare di nuovo clic sulla casella e selezionare **User name** (Nome utente) nell'elenco di opzioni When a new tweet is posted (Quando viene pubblicato un nuovo _tweet)._

1. Fare clic _all'interno della casella_ Corpo e selezionare Testo **tweet** nell'elenco di opzioni Quando viene pubblicato un _nuovo tweet._ Se _l'elemento di testo Tweet_ non è visualizzato nell'elenco, fare clic sul **collegamento** Altre informazioni per espandere l'elenco di opzioni.

1. Selezionare il **pulsante Salva** sulla barra degli strumenti per salvare lo stato di avanzamento.

La casella di posta elettronica dovrebbe ora essere simile a questa schermata.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="Notifica di posta elettronica":::

## <a name="run-the-workflow"></a>Eseguire il flusso di lavoro

1. Dall'account Twitter tingere il testo seguente: **I'm enjoying #my-twitter-tutorial**.

1. Tornare a Progettazione app per la logica e selezionare il **pulsante** Esegui.

1. Controllare la posta elettronica per un messaggio dal flusso di lavoro.

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire tutti i servizi e gli account di Azure creati durante questa esercitazione, eliminare il gruppo di risorse.

1. Cercare Gruppi **di risorse** nella casella di ricerca in alto.

1. Selezionare **tweet-sentiment-tutorial.**

1. Selezionare **Elimina gruppo di risorse**

1. Immettere **tweet-sentiment-tutorial** nella casella di testo.

1. Selezionare il pulsante **Elimina**.

Facoltativamente, è possibile tornare all'account Twitter ed eliminare eventuali tweet di test dal feed.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'API senza server mediante Funzioni di Azure](functions-create-serverless-api.md)
