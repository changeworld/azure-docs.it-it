---
title: Inviare comandi personalizzati'attività all'applicazione client
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come inviare attività da un'applicazione comandi personalizzati a un'applicazione client che esegue Speech SDK.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 52e0b750f02044afafe233a76e4f43755d9ed303
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725099"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Inviare comandi personalizzati'attività all'applicazione client

Questo articolo illustra come inviare attività da un'applicazione comandi personalizzati a un'applicazione client che esegue Speech SDK.

Completare le attività seguenti:

- Definire e inviare un payload JSON personalizzato dall'comandi personalizzati personalizzata
- Ricevere e visualizzare il contenuto del payload JSON personalizzato da un'applicazione client UWP Speech SDK C#

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * [Visual Studio 2019 o](https://visualstudio.microsoft.com/downloads/) versione successiva. Questa guida usa Visual Studio 2019
> * Una chiave di sottoscrizione di Azure per il servizio Voce: È possibile [ottenerne una gratuitamente](overview.md#try-the-speech-service-for-free) o crearla nel [portale di Azure](https://portal.azure.com)
> * Un'[app di comandi personalizzati creata](quickstart-custom-commands-application.md) in precedenza
> * Un'app client abilitata per Speech SDK: [Procedura: Eseguire l'integrazione con un'applicazione client con Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Configurare l'attività Invia al client 
1. Aprire l'comandi personalizzati creata in precedenza
1. Selezionare **il comando TurnOnOff,** selezionare **ConfirmationResponse sotto** la regola di completamento, quindi selezionare Aggiungi **un'azione**
1. In **New Action-Type (Nuovo tipo di azione)** **selezionare Send activity to client (Invia attività al client)**
1. Copiare il codice JSON seguente in **Contenuto attività**
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
1. Fare **clic su** Salva per creare una nuova regola con un'azione Invia attività, eseguire il training **e** **pubblicare** la modifica

   > [!div class="mx-imgBorder"]
   > ![Regola di completamento dell'attività di invio](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrazione con l'applicazione client

In Procedura: Configurare un'applicazione client con [Speech SDK (anteprima)](./how-to-custom-commands-setup-speech-sdk.md)è stata creata un'applicazione client UWP con Speech SDK che ha gestito comandi come `turn on the tv` , `turn off the fan` . Con alcuni oggetti visivi aggiunti, è possibile visualizzare il risultato di questi comandi.

Per aggiungere caselle etichettate con testo **che** indica che è stato o **meno,** aggiungere il blocco XML seguente di StackPanel a `MainPage.xaml` .

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Aggiungere librerie di riferimento

Poiché è stato creato un payload JSON, è necessario aggiungere un riferimento alla JSON.NET [per](https://www.newtonsoft.com/json) gestire la deserializzazione.

1. Fare clic con il pulsante destro del mouse sulla soluzione.
1. Scegliere **Gestisci pacchetti NuGet per la soluzione**, selezionare **Sfoglia** 
1. Se è già stato **installatoNewtonsoft.jsin**, verificare che la versione sia almeno 12.0.3. In caso contrario, passare **a Manage NuGet Packages for Solution - Updates** **(Gestisci** pacchetti NuGet per soluzione - Aggiornamenti) e cercareNewtonsoft.jssu per aggiornarlo. Questa guida usa la versione 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Payload dell'attività di invio](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Assicurarsi inoltre che il pacchetto NuGet **Microsoft.NETCore.UniversalWindowsPlatform** sia almeno 6.2.10. Questa guida usa la versione 6.2.10.

In 'MainPage.xaml.cs' aggiungere

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Gestire il payload ricevuto

In `InitializeDialogServiceConnector` sostituire il gestore eventi con il codice `ActivityReceived` seguente. Il gestore `ActivityReceived` dell'evento modificato estrarrà il payload dall'attività e modificherà rispettivamente lo stato visivo della tv o della ventola.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.value?.state != null ? activity.value.state.ToString() : string.Empty;
        var device = activity?.value?.device != null ? activity.value.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Procedura

1. Avviare l'applicazione
1. Selezionare Abilita microfono
1. Selezionare il pulsante Talk (Parla)
1. Pronunciare la frase `turn on the tv`
1. Lo stato di visualizzazione della tv deve cambiare in "on"
   > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra che lo stato di visualizzazione di T V è ora attivo.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: Configurare endpoint Web](./how-to-custom-commands-setup-web-endpoints.md)
