---
title: Configurare endpoint Web
titleSuffix: Azure Cognitive Services
description: configurare endpoint Web per comandi personalizzati
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 5f1d5318140dd14c5024e8dd3ad0def0afc7f378
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725909"
---
# <a name="set-up-web-endpoints"></a>Configurare endpoint Web

Questo articolo illustra come configurare gli endpoint Web in un'applicazione di comandi personalizzati che consentono di effettuare richieste HTTP da un'applicazione client. Verranno completate le attività seguenti:

- Configurare gli endpoint Web in un'applicazione di comandi personalizzati
- Chiamare gli endpoint Web in un'applicazione di comandi personalizzati
- Ricevere la risposta degli endpoint Web
- Integrare la risposta degli endpoint Web in un payload JSON personalizzato, inviarla e visualizzarla in un'applicazione client Speech SDK della piattaforma UWP in C#

## <a name="prerequisites"></a>Prerequisiti

> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Una chiave di sottoscrizione di Azure per il servizio Voce: È possibile [ottenerne una gratuitamente](overview.md#try-the-speech-service-for-free) o crearla nel [portale di Azure](https://portal.azure.com)
> * Un'app comandi personalizzati (vedere [Creare un assistente vocale usando comandi personalizzati](quickstart-custom-commands-application.md))
> * Un'app client abilitata per Speech SDK (vedere [Integrare un'applicazione client con Speech SDK)](how-to-custom-commands-setup-speech-sdk.md)

## <a name="deploy-an-external-web-endpoint-using-azure-function-app"></a>Distribuire un endpoint Web esterno usando l'app per le funzioni di Azure

Per questa esercitazione è necessario un endpoint HTTP che mantenga gli stati per tutti i dispositivi impostati nel **comando TurnOnOff** dell'comandi personalizzati appliazione.

Se si ha già un endpoint Web che si vuole chiamare, passare alla [sezione successiva.](#setup-web-endpoints-in-custom-commands) In alternativa, la sezione successiva fornisce informazioni dettagliate su un endpoint Web ospitato predefinito che è possibile usare se si vuole ignorare questa sezione.

### <a name="input-format-of-azure-function"></a>Formato di input della funzione di Azure

Successivamente, si distribuirà un endpoint [usando Funzioni di Azure](../../azure-functions/index.yml).
Di seguito è riportato il formato di un evento comandi personalizzati che viene passato alla funzione di Azure. Usare queste informazioni quando si scrive l'app per le funzioni di Azure.

```json
{
  "conversationId": "string",
  "currentCommand": {
    "name": "string",
    "parameters": {
      "SomeParameterName": "string",
      "SomeOtherParameterName": "string"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "string",
      "SomeOtherGlobalParameterName": "string"
  }
}
```

    
Nella tabella seguente vengono descritti gli attributi chiave di questo input:
        
| Attributo | Spiegazione |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Identificatore univoco della conversazione. Si noti che questo ID può essere generato dall'app client. |
| **currentCommand** | Comando attualmente attivo nella conversazione. |
| **nome** | Nome del comando. `parameters`L'attributo è una mappa con i valori correnti dei parametri. |
| **currentGlobalParameters** | Mappa come `parameters` , ma usata per i parametri globali. |


Per la funzione di Azure **DeviceState,** un esempio comandi personalizzati'evento sarà simile al seguente. Questo fungerà da **input per** l'app per le funzioni.
    
```json
{
  "conversationId": "someConversationId",
  "currentCommand": {
    "name": "TurnOnOff",
    "parameters": {
      "item": "tv",
      "value": "on"
    }
  }
}
```

### <a name="azure-function-output-for-a-custom-command-app"></a>Output delle funzioni di Azure per un'app per i comandi personalizzati

Se l'output della funzione di Azure viene utilizzato da un comandi personalizzati app, dovrebbe essere visualizzato nel formato seguente. Per [informazioni dettagliate, vedere Aggiornare un comando da un endpoint](./how-to-custom-commands-update-command-from-web-endpoint.md) Web.

```json
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

### <a name="azure-function-output-for-a-client-application"></a>Output delle funzioni di Azure per un'applicazione client

Se l'output della funzione di Azure viene utilizzato da un'applicazione client, l'output può assumere la forma richiesta dall'applicazione client.

Per **l'endpoint DeviceState,** l'output della funzione di Azure viene utilizzato da un'applicazione client anziché dall comandi personalizzati app. L'output di esempio della funzione di Azure dovrebbe essere simile al seguente:
    
```json
{
  "TV": "on",
  "Fan": "off"
}
``` 

Questo output deve essere scritto in un archivio esterno, in modo da poter mantenere lo stato dei dispositivi. Lo stato di archiviazione esterno verrà usato nella sezione [Integrare con l'applicazione client](#integrate-with-client-application) riportata di seguito.


### <a name="deploy-azure-function"></a>Distribuire la funzione di Azure

Viene fornito un esempio che è possibile configurare e distribuire come Funzioni di Azure app. Per creare un account di archiviazione per l'esempio, seguire questa procedura.
 
1. Creare l'archiviazione tabelle per salvare lo stato del dispositivo. Nel portale di Azure creare una nuova risorsa di tipo **Account di archiviazione** in base al nome **devicestate**.
1. Copiare **il valore Stringa di** connessione da **devicestate -> Chiavi di accesso**. È necessario aggiungere questo segreto stringa al codice dell'app per le funzioni di esempio scaricato.
1. Scaricare il codice [dell'app per le funzioni di esempio](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/custom-commands/quick-start).
1. Aprire la soluzione scaricata in Visual Studio 2019. In **Connections.jsin** sostituire **STORAGE_ACCOUNT_SECRET_CONNECTION_STRING** con il segreto del passaggio 2.
1.  Scaricare il **codice DeviceStateAzureFunction.**

Per distribuire l'app di esempio Funzioni di Azure, seguire questa procedura.

1. [Distribuire](../../azure-functions/index.yml) l Funzioni di Azure app.
1. Attendere che la distribuzione riesca e passare alla risorsa distribuita nel portale di Azure. 
1. Selezionare **Funzioni** nel riquadro sinistro e quindi **Selezionare DeviceState.**
1.  Nella nuova finestra selezionare **Code + Test** e quindi selezionare Get function URL **(Ottieni URL funzione).**
 
## <a name="setup-web-endpoints-in-custom-commands"></a>Configurare gli endpoint Web in comandi personalizzati

Associare la funzione di Azure all'applicazione comandi personalizzati esistente.
In questa sezione si userà un endpoint **DeviceState predefinito** esistente. Se è stato creato un endpoint Web personalizzato usando La funzione di Azure o altro, usarlo al posto del valore predefinito `https://webendpointexample.azurewebsites.net/api/DeviceState` .

1. Aprire l'applicazione di comandi personalizzati creata in precedenza.
1. Passare a **Endpoint Web e** fare clic su Nuovo endpoint **Web.**

   > [!div class="mx-imgBorder"]
   > ![Nuovo endpoint Web](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Nome | UpdateDeviceState | Il nome dell'endpoint Web. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | L'URL dell'endpoint da mettere in comunicazione con l'app di comandi personalizzati. |
   | Metodo | POST | Le interazioni consentite, ad esempio GET o POST, con l'endpoint.|
   | Intestazioni | Chiave: app, Valore: usare le prime 8 cifre dell'ID applicazione | I parametri dell'intestazione da includere nell'intestazione della richiesta.|

    > [!NOTE]
    > - L'endpoint Web di esempio creato [Funzioni di Azure](../../azure-functions/index.yml), che si collega al database che salva lo stato del dispositivo della tv e della ventola.
    > - L'intestazione suggerita è necessaria solo per l'endpoint di esempio.
    > - Per assicurarsi che il valore dell'intestazione sia univoco nell'endpoint di esempio, prendere le prime 8 cifre di **applicationId**.
    > - Nel mondo reale, l'endpoint Web può essere l'endpoint per [l'hub IoT](../../iot-hub/about-iot-hub.md) che gestisce i dispositivi.

1. Fare clic su **Salva**.

## <a name="call-web-endpoints"></a>Chiamare gli endpoint Web

1. Passare al comando **TurnOnOff**, selezionare **ConfirmationResponse** nella regola di completamento, quindi selezionare **Aggiungi un'azione**.
1. In **Nuova azione - Tipo** selezionare **Call web endpoint** (Chiamata a endpoint Web)
1. In **Modifica azione - Endpoint** selezionare **UpdateDeviceState**, ovvero l'endpoint Web creato.  
1. In **Configurazione** inserire i valori seguenti:
   > [!div class="mx-imgBorder"]
   > ![Parametri dell'azione di chiamata a endpoint Web](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Endpoint | UpdateDeviceState | L'endpoint Web da chiamare in questa azione. |
   | Parametri di query | item={SubjectDevice}&&value={OnOff} | I parametri di query da aggiungere alla fine dell'URL dell'endpoint.  |
   | Contenuto del corpo | N/D | Il contenuto del corpo della richiesta. |

    > [!NOTE]
    > - I parametri di query suggeriti sono necessari solo per l'endpoint di esempio

1. Per **In caso di esito positivo - Action to execute** (Azione da eseguire) selezionare **Send speech response** (Invia risposta vocale).

    In **Simple editor** (Editor semplice) immettere `{SubjectDevice} is {OnOff}`.

   > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra la schermata In esito positivo - Azione da eseguire.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Azione da eseguire | Send speech response (Invia risposta vocale) | Azione da eseguire in caso di esito positivo della richiesta effettuata all'endpoint Web |

   > [!NOTE]
   > - È anche possibile accedere direttamente ai campi della risposta HTTP usando `{YourWebEndpointName.FieldName}`. ad esempio `{UpdateDeviceState.TV}`

1. Per **In caso di esito negativo - Action to execute** (Azione da eseguire) selezionare **Send speech response** (Invia risposta vocale)

    In **Simple editor** (Editor semplice) immettere `Sorry, {WebEndpointErrorMessage}`.

   > [!div class="mx-imgBorder"]
   > ![Azione di chiamata a endpoint Web in caso di esito negativo](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Azione da eseguire | Send speech response (Invia risposta vocale) | Azione da eseguire in caso di esito negativo della richiesta all'endpoint Web |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` è facoltativo. Se non si vuole esporre alcun messaggio di errore, è possibile rimuoverlo.
   > - All'interno dell'endpoint di esempio viene restituita la risposta HTTP con messaggi dettagliati per gli errori comuni, ad esempio parametri di intestazione mancanti.

### <a name="try-it-out-in-test-portal"></a>Provare nel portale di test
- In Risposta con esito positivo salvare, eseguire il training e testare.
   > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra la risposta In esito positivo.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- In Risposta non riuscita rimuovere uno dei parametri di query, salvare, eseguire di nuovo il training e testare.
   > [!div class="mx-imgBorder"]
   > ![Azione di chiamata a endpoint Web in caso di esito positivo](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrazione con l'applicazione client

In [Invia comandi personalizzati'attività all'applicazione client](./how-to-custom-commands-send-activity-to-client.md)è stata aggiunta **un'azione Invia attività al client.** L'attività viene inviata all'applicazione client indipendentemente dall'esito positivo o negativo dell'azione **Call Web endpoint** (Chiamata a endpoint Web).
Tuttavia, in genere si vuole inviare attività all'applicazione client solo quando la chiamata all'endpoint Web ha esito positivo. In questo esempio si tratta del momento in cui lo stato del dispositivo viene aggiornato correttamente.

1. Eliminare l'azione **Send activity to client** (Invia attività a client) aggiunta in precedenza.
1. Modificare la chiamata all'endpoint Web:
    1. In **Configurazione** assicurarsi che l'opzione **Parametri di query** sia impostata su `item={SubjectDevice}&&value={OnOff}`
    1. Per **In caso di esito positivo** impostare **Action to execute** (Azione da eseguire) su **Send activity to client** (Invia attività a client)
    1. Copiare il codice JSON seguente in **Activity Content** (Contenuto attività)
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
A questo punto si invia l'attività al client solo quando la richiesta all'endpoint Web ha esito positivo.

### <a name="create-visuals-for-syncing-device-state"></a>Creare oggetti visivi per sincronizzare lo stato del dispositivo

Aggiungere il codice XML seguente `MainPage.xaml` a sopra il blocco **EnableMicrophoneButton.**

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Sincronizzare lo stato del dispositivo

In `MainPage.xaml.cs` aggiungere il riferimento `using Windows.Web.Http;`. Aggiungere il codice seguente alla classe `MainPage` . Questo metodo invierà una richiesta GET all'endpoint di esempio ed estrarrà lo stato corrente del dispositivo per l'app. Assicurarsi di passare a `<your_app_name>` quello  usato nell'intestazione nell'endpoint Web del comando personalizzato.

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request.
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Provare questa operazione

1. Avviare l’applicazione.
1. Fare clic su Sync Device State (Sincronizza stato dispositivo).\
Se l'app è stata testata con nella sezione precedente, i programmi TV vengono visualizzati `turn on tv` come **in**.
    > [!div class="mx-imgBorder"]
    > ![Sincronizzare lo stato del dispositivo](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selezionare **Abilita microfono**.
1. Selezionare il **pulsante Parla.**
1. Pronunciare `turn on the fan` . Lo stato di visualizzazione della ventola deve cambiare in **in**.
    > [!div class="mx-imgBorder"]
    > ![Attivare il ventilatore](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esportare comandi personalizzati'applicazione come competenza remota](./how-to-custom-commands-integrate-remote-skills.md)
