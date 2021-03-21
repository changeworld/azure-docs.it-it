---
title: Inviare notifiche push a iOS con Hub di notifica di Azure e iOS SDK versione 3.0.0 Preview 1
description: Questa esercitazione illustra come usare Hub di notifica di Azure e il servizio Apple Push Notification per inviare notifiche push ai dispositivi iOS (versione 3.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: f905bfa830bfc78caa6ebb02ae49d4839168367b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100627815"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-sdk-for-apple"></a>Esercitazione: inviare notifiche push alle app iOS usando l'SDK di hub di notifica di Azure per Apple

Questa esercitazione illustra come usare hub di notifica di Azure per inviare notifiche push a un'applicazione iOS usando l'SDK di hub di notifica di Azure per Apple.

Questa esercitazione illustra i passaggi seguenti:

- Creare un'app iOS di esempio.
- Connettere l'app iOS a Hub di notifica di Azure.
- Inviare notifiche push di prova.
- Verificare che l'app riceva le notifiche.

Il codice completo per questa esercitazione può essere scaricato da [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/main/SampleNHAppObjC).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione sono necessari i prerequisiti seguenti:

- Un Mac che esegue [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), insieme a un certificato dello sviluppatore valido installato nel portachiavi.
- Un iPhone o iPad con iOS versione 10 o successiva.
- Il dispositivo fisico registrato nel [portale Apple](https://developer.apple.com/) e associato al certificato.

Prima di procedere è importante seguire l'esercitazione precedente che spiega come iniziare a usare [Hub di notifica di Azure per le app iOS](ios-sdk-get-started.md) per configurare le credenziali push nell'hub di notifica. Anche se non si ha alcuna esperienza con lo sviluppo di app iOS, si dovrebbe essere in grado di seguire questa procedura.

> [!NOTE]
> Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push in un dispositivo iOS fisico (iPhone o iPad), anziché in un emulatore iOS.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Connettere l'app iOS all'hub di notifica

1. In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application** .

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="Selezionare il modello":::

2. Quando si configurano le opzioni per il nuovo progetto, assicurarsi di usare lo stesso **nome prodotto** e lo stesso **identificatore organizzazione** usati quando è stato impostato l'identificatore del bundle nel portale Apple Developer.

3. In Project Navigator (Esplorazione progetto) selezionare il nome del progetto in **Targets** (Destinazioni), quindi selezionare la scheda **Signing & Capabilities** (Firma e funzionalità). Assicurarsi di selezionare il **Team** appropriato per l'account Apple Developer. XCode recupererà automaticamente il profilo di provisioning creato in precedenza in base all'identificatore del bundle.

   Se il profilo di provisioning creato in Xcode non viene visualizzato, provare ad aggiornare i profili per l'identità di firma. Fare clic su **Xcode** nella barra dei menu, fare clic su **Preferences** (Preferenze), selezionare la scheda **Account**, fare clic sul pulsante **View Details** (Visualizza dettagli), selezionare la propria identità di firma e infine fare clic sul pulsante di aggiornamento nell'angolo in basso a destra.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="Visualizzare i dettagli":::

4. Nella scheda **Signing & Capabilities** (Firma e funzionalità) selezionare **+ Capability** (+ Funzionalità). Fare doppio clic su **Push Notifications**(Notifiche push) per abilitarla.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="Funzionalità":::

5. Aggiungere i moduli dell'SDK di Hub di notifica di Azure.

   È possibile integrare l'SDK di Hub di notifica di Azure nell'app usando [Cocoapods](https://cocoapods.org/) o aggiungendo manualmente i file binari al progetto.

   - Integrazione tramite Cocoapods: Aggiungere le dipendenze seguenti al podfile per includere l'SDK di Hub di notifica di Azure nell'app:

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - Eseguire pod install per installare il pod appena definito e aprire il file con estensione .xcworkspace.

         Se viene visualizzato un errore che indica ad esempio che **non è possibile trovare una specifica per AzureNotificationHubs-iOS**, durante l'esecuzione di pod install, eseguire `pod repo update` per ottenere i pod più recenti dal repository CocoaPods, quindi eseguire pod install.

   - Integrazione tramite Carthage: Aggiungere le dipendenze seguenti al Cartfile per includere l'SDK di Hub di notifica di Azure nell'app:

      ```ruby
      github "Azure/azure-notificationhubs-ios"
      ```

      - Quindi, aggiornare e compilare le dipendenze:

      ```shell
      $ carthage update
      ```

      Per altre informazioni sull'uso di Carthage, vedere il [repository GitHub di Carthage](https://github.com/Carthage/Carthage).

   - Integrazione tramite copia dei file binari nel progetto:

      È possibile eseguire l'integrazione copiando i file binari nel progetto, come segue:

        - Scaricare il framework dell'[SDK di Hub di notifica di Azure](https://github.com/Azure/azure-notificationhubs-iOS/releases/) fornito come file ZIP e decomprimerlo.

        - In Xcode fare clic con il pulsante destro del mouse sul progetto e scegliere l'opzione **Add Files to** (Aggiungi file a) per aggiungere la cartella **WindowsAzureMessaging.framework** al progetto Xcode. Selezionare **Options** (Opzioni) e assicurarsi che sia selezionato **Copy items if needed** (Copia elementi se necessario), quindi fare clic su **Add** (Aggiungi).

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="Aggiungere il framework":::

6. Aggiungere o modificare un file denominato **DevSettings. plist** che contiene due proprietà, `CONNECTION_STRING` per la stringa di connessione all'hub di notifica di Azure e `HUB_NAME` per il nome dell'hub di notifica di Azure.

7. Aggiungere le informazioni per la connessione a hub di notifica di Azure nella `<string></string>` sezione appropriata.  Sostituire i segnaposto dei valori letterali stringa `--HUB-NAME--` e `--CONNECTION-STRING--` rispettivamente con il nome dell'hub e la firma **DefaultListenSharedAccessSignature** ottenuti in precedenza dal portale:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>HUB_NAME</key>
        <string>--HUB-NAME--</string>
        <key>CONNECTION_STRING</key>
        <string>--CONNECTION-STRING--</string>
    </dict>
    </plist>
    ```

8. Nello stesso file **AppDelegate.m**, sostituire tutto il codice dopo `didFinishLaunchingWithOptions` con il codice seguente:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>

    // Extend the AppDelegate to listen for messages using MSNotificationHubDelegate and User Notification Center
    @interface AppDelegate () <MSNotificationHubDelegate>

    @end

    @implementation AppDelegate
    
    @synthesize notificationPresentationCompletionHandler;
    @synthesize notificationResponseCompletionHandler;

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

        NSString *path = [[NSBundle mainBundle] pathForResource:@"DevSettings" ofType:@"plist"];
        NSDictionary *configValues = [NSDictionary dictionaryWithContentsOfFile:path];
        
        NSString *connectionString = [configValues objectForKey:@"CONNECTION_STRING"];
        NSString *hubName = [configValues objectForKey:@"HUB_NAME"];

        if([connectionString length] != 0 && [hubName length] != 0) {
            [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
            [MSNotificationHub setDelegate:self];
            [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];
    
            return YES;
        }

        NSLog(@"Please setup CONNECTION_STRING and HUB_NAME in DevSettings.plist and restart application");

        exit(-1);
    }

    - (void)notificationHub:(MSNotificationHub *)notificationHub didReceivePushNotification:(MSNotificationHubMessage *)message {
        // Send message using NSNotificationCenter with the message
        NSDictionary *userInfo = [NSDictionary dictionaryWithObject:message forKey:@"message"];
        [[NSNotificationCenter defaultCenter] postNotificationName:@"MessageReceived" object:nil userInfo:userInfo];
    }

    @end
    ```

    Questo codice si connette all'hub di notifica usando le informazioni di connessione specificate in **DevSettings. plist**. Fornisce quindi il token del dispositivo all'hub di notifica in modo che quest'ultimo possa inviare notifiche.

### <a name="create-notificationdetailviewcontroller-header-file"></a>Creare il file di intestazione NotificationDetailViewController

1. Analogamente alle istruzioni precedenti, aggiungere un altro file di intestazione denominato **SetupViewController. h**. Sostituire il contenuto del file di intestazione con il codice seguente:

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface SetupViewController : UIViewController

   @end

   NS_ASSUME_NONNULL_END
   ```

2. Aggiungere il file di implementazione **SetupViewController. m**. Sostituire il contenuto del file con il codice seguente, che implementa i metodi UIViewController:

   ```objc
   #import "SetupViewController.h"

    static NSString *const kNHMessageReceived = @"MessageReceived";
    
    @interface SetupViewController ()
    
    @end

    @implementation SetupViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        
        // Listen for messages using NSNotificationCenter
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didReceivePushNotification:) name:kNHMessageReceived object:nil];
    }

    - (void)dealloc {
        // Clean up subscription to NSNotificationCenter
        [[NSNotificationCenter defaultCenter] removeObserver:self name:kNHMessageReceived object:nil];
    }

    - (void)didReceivePushNotification:(NSNotification *)notification {
        MSNotificationHubMessage *message = [notification.userInfo objectForKey:@"message"];

        // Create UI Alert controller with message title and body
        UIAlertController *alertController = [UIAlertController alertControllerWithTitle:message.title
                             message:message.body
                      preferredStyle:UIAlertControllerStyleAlert];
        [alertController addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleCancel handler:nil]];
        [self presentViewController:alertController animated:YES completion:nil];
        
        // Dismiss after 2 seconds
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            [alertController dismissViewControllerAnimated:YES completion: nil];
        });

    }

    @end
   ```

3. Per verificare che non vi siano errori, compilare ed eseguire l'app nel dispositivo.

## <a name="send-test-push-notifications"></a>Inviare notifiche push di prova

È possibile testare rapidamente la ricezione di notifiche nell'app con l'opzione **Invio di prova** nel [Azure portal](https://portal.azure.com/). Verrà inviata una notifica push di prova al dispositivo.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="Invio di prova":::

Le notifiche push vengono in genere inviate in un servizio back-end come App per dispositivi mobili o ASP.NET usando una libreria compatibile. Se non è disponibile una libreria per il back-end è anche possibile usare direttamente l'API REST per inviare messaggi di notifica.

Di seguito è riportato un elenco di altre esercitazioni, che è possibile esaminare per l'invio di notifiche:

- App per dispositivi mobili di Azure: per un esempio di come inviare notifiche dal back-end di un'app per dispositivi mobili integrata con Hub di notifica, vedere [Aggiungere notifiche push all'app iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).
- ASP.NET: [usare Hub di notifica per inviare notifiche push agli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
- Azure Notification Hubs Java SDK: vedere [Come usare Hub di notifica da Java](notification-hubs-java-push-notification-tutorial.md) per l'invio di notifiche da Java. È stato testato in Eclipse per lo sviluppo per Android.
- PHP: [Come usare Hub di notifica da PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="verify-that-your-app-receives-push-notifications"></a>Verificare che l'app riceve le notifiche push

Per testare le notifiche push in iOS, è necessario distribuire l'app in un dispositivo fisico iOS. Non è possibile inviare notifiche push Apple con il simulatore iOS.

1. Eseguire l'app e verificare che la registrazione riesca e quindi scegliere **OK**.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="Registra":::

2. Inviare ora una notifica push di prova dal [portale di Azure](https://portal.azure.com/), come illustrato nella sezione precedente.

3. La notifica push viene inviata a tutti i dispositivi registrati per la ricezione di notifiche dall'hub di notifica specificato.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="Invio di prova":::

## <a name="next-steps"></a>Passaggi successivi

In questo semplice esempio le notifiche push vengono trasmesse a tutti i dispositivi iOS registrati. Per informazioni su come inviare notifiche push a dispositivi iOS specifici, passare all'esercitazione seguente:

[Esercitazione: Inviare notifiche push a specifici dispositivi](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di Hub di notifica di Azure](notification-hubs-push-notification-overview.md)
- [API REST di Hub di notifica](/rest/api/notificationhubs/)
- [SDK di Hub di notifica per le operazioni back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [SDK di Hub di notifica su GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrazione con il back-end dell'applicazione](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Uso dei tag](notification-hubs-tags-segment-push-message.md)
- [Uso di modelli personalizzati](notification-hubs-templates-cross-platform-push-messages.md)
- [Controllo degli accessi del bus di servizio con firme di accesso condiviso](../service-bus-messaging/service-bus-sas.md)
- [Generare token di firma di accesso condiviso a livello di codice](/rest/api/eventhub/generate-sas-token)
- [Sicurezza di Apple: crittografia comune](https://developer.apple.com/security/)
- [Ora del periodo UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)