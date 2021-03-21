---
title: Inviare notifiche push alle app Xamarin.Android con Hub di notifica di Azure | Microsoft Docs
description: Questa esercitazione contiene informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin Android.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: e7d4206de1e097c30e9f5e96bbd935e94892ce0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221035"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Esercitazione: Inviare notifiche push alle app Xamarin.Android con Hub di notifica di Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione di Xamarin.Android. Viene creata un'app di Xamarin.Android vuota che riceve notifiche push tramite Firebase Cloud Messaging (FCM). È possibile usare l'hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app. Il codice compilato è disponibile nell'esempio di [app NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare un progetto Firebase e attivare Firebase Cloud Messaging
> * Creare un hub di notifica
> * Creare un'app Xamarin.Android e connetterla all'hub di notifica
> * Inviare notifiche di prova dal portale di Azure

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* [Visual Studio con Xamarin] in Windows o [Visual Studio per Mac] in OS X.
* Un account Google attivo

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Creare un progetto Firebase e attivare Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Creare un hub di notifica

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Configurare le impostazioni di GCM/FCM per l'hub di notifica

1. Selezionare **Google (GCM/FCM)** nella sezione **Settings** (Impostazioni) nel menu a sinistra.
2. Immettere la **chiave del server** annotata dalla Google Firebase Console.
3. Sulla barra degli strumenti selezionare **Salva**.

    ![Screenshot dell'hub di notifica nel portale di Azure con l'opzione Google G C M F C M evidenziata e con contorno rosso.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

L'hub di notifica è configurato per l'uso con FCM e sono disponibili le stringhe di connessione per registrare l'app in modo da ricevere notifiche e inviare notifiche push.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Creare un'app Xamarin.Android e connetterla all'hub di notifica

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Creare un progetto di Visual Studio e aggiungere i pacchetti NuGet

> [!NOTE]
> I passaggi descritti in questa esercitazione sono per Visual Studio 2017. 

1. In Visual Studio scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**. Nella finestra **New Project** (Nuovo progetto) seguire questa procedura:
    1. Espandere **Installed** (Installato), **Visual C#** , quindi fare clic su **Android**.
    2. Selezionare **Android App (Xamarin)** dall'elenco.
    3. Immettere un **nome** per il progetto.
    4. Selezionare una **località** per il progetto.
    5. Selezionare **OK**.

        ![Finestra di dialogo Nuovo progetto](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Nella finestra di dialogo **New Android App** (Nuova App per Android), selezionare **Blank App** (App vuota) e selezionare **OK**.

    ![Screenshot che evidenzia il modello App vuota.](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Nella finestra **Esplora soluzioni** espandere **Proprietà** e fare clic su **AndroidManifest.xml**. Aggiornare il nome del pacchetto in modo che corrisponda al nome del pacchetto immesso durante l'aggiunta di Firebase Cloud Messaging al progetto in Google Firebase Console.

    ![Nome del pacchetto in GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Impostare la versione di destinazione di Android per il progetto su **android 10,0** attenendosi alla procedura seguente: 
    1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Proprietà**. 
    1. Per il campo **Compila usando Android versione: (Framework di destinazione)** selezionare **Android 10,0**. 
    1. Selezionare **Sì** nella finestra di messaggio per continuare con la modifica del framework di destinazione.
1. Aggiungere i pacchetti NuGet necessari al progetto seguendo questa procedura:
    1. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Gestisci pacchetti NuGet**.
    1. Passare alla scheda **Installato**, selezionare **Xamarin.Android.Support.Design** e selezionare **Aggiorna** nel riquadro a destra per aggiornare il pacchetto alla versione più recente.
    1. Passare alla scheda **Sfoglia**. Cercare **Xamarin.GooglePlayServices.Base**. Selezionare **Xamarin.GooglePlayServices.Base** nell'elenco dei risultati. Quindi, selezionare **Installa**.

        ![NuGet Google Play Services](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Nella finestra **Gestione pacchetti NuGet** cercare il pacchetto **Xamarin.Firebase.Messaging**. Selezionare **Xamarin.Firebase.Messaging** nell'elenco dei risultati. Quindi, selezionare **Installa**.
    7. A questo punto, cercare **Xamarin.Azure.NotificationHubs.Android**. Selezionare **Xamarin.Azure.NotificationHubs.Android** nell'elenco dei risultati. Quindi, selezionare **Installa**.

### <a name="add-the-google-services-json-file"></a>Aggiungere il file JSON di Google Services

1. Copiare il file `google-services.json` scaricato da Google Firebase Console nella cartella del progetto.
2. Aggiungere `google-services.json` al progetto.
3. Selezionare `google-services.json` nella finestra **Esplora soluzioni**.
4. Nel riquadro **Proprietà** impostare l'azione di compilazione **GoogleServicesJson**. Se non viene visualizzato **GoogleServicesJson**, chiudere Visual Studio, riavviarlo, riaprire il progetto e riprovare.

    ![Azione di compilazione GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configurare Hub di notifica nel progetto

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrazione con Firebase Cloud Messaging

1. Se si esegue la migrazione da Google Cloud Messaging a Firebase, il file del progetto `AndroidManifest.xml` potrebbe contenere una configurazione GCM obsoleta, che potrebbe causare la duplicazione delle notifiche. Modificare il file e rimuovere le righe seguenti all'interno della `<application>` sezione, se presente:

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Aggiungere le istruzioni seguenti **prima dell'elemento applicazione**.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Raccogliere le informazioni seguenti per l'app Android e l'hub di notifica:

   * **Stringa di connessione per l'ascolto**: nel dashboard del [Azure portal] scegliere **Visualizza stringhe di connessione**. Copiare la stringa di connessione `DefaultListenSharedAccessSignature` per questo valore.
   * **Nome hub**: il nome dell'hub indicato nel [Azure portal]. Ad esempio, *mynotificationhub2*.
4. Nella finestra **Esplora soluzioni** fare clic con il pulsante destro del mouse sul **progetto**, scegliere **Aggiungi** e selezionare **Classe**.
5. Creare una classe `Constants.cs` per il progetto Xamarin e definire i valori costanti seguenti nella classe. Sostituire i segnaposto con i valori.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Aggiungere le istruzioni using seguenti a `MainActivity.cs`:

    ```csharp
    using Azure.Messaging.NotificationHubs;
    ```

7. Aggiungere le proprietà seguenti alla classe MainActivity:

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. Aggiungere al progetto una classe denominata `AzureListener`.
10. Aggiungere le istruzioni using seguenti a `AzureListener.cs`.

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. Aggiungere il codice seguente sopra la dichiarazione di classe e fare in che la classe erediti da `Java.Lang.Object` e implementi `INotificationListener` :

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. Aggiungere il codice seguente all'interno della classe `MyFirebaseMessagingService` per elaborare i messaggi ricevuti.

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. **Compilare** il progetto.
1. **Eseguire** l'app nel dispositivo o nell'emulatore caricato

## <a name="send-test-notification-from-the-azure-portal"></a>Inviare notifiche di prova dal portale di Azure

È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione **Invio di prova** nel [Azure portal]. Verrà inviata una notifica push di prova al dispositivo.

![Portale di Azure - Invio di prova](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Le notifiche push vengono in genere inviate in un servizio back-end come Servizi mobili o ASP.NET usando una libreria compatibile. Se non è disponibile una libreria per il back-end è anche possibile usare direttamente l'API REST per inviare messaggi di notifica.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, le notifiche sono state trasmesse a tutti i dispositivi Android registrati con il back-end. Per informazioni sulle procedure per eseguire il push di notifiche a dispositivi Android specifici, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a dispositivi specifici](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio con Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio per Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure portal]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android