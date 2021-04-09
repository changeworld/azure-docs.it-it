---
title: "Guida introduttiva: aggiungere un team che partecipa a un'app iOS usando i servizi di comunicazione di Azure"
description: In questa Guida introduttiva si apprenderà come usare la libreria di incorporamento dei team di servizi di comunicazione di Azure per iOS.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104804008"
---
In questa Guida introduttiva si apprenderà come partecipare a una riunione di team con i team di servizi di comunicazione Azure incorporare la libreria per iOS.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa Guida introduttiva, sono necessari i prerequisiti seguenti:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un Mac che esegue [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), insieme a un certificato dello sviluppatore valido installato nel portachiavi.
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un [token di accesso utente](../../access-tokens.md) per il servizio di comunicazione di Azure.
- [CocoaPods](https://cocoapods.org/) installato per l'ambiente di compilazione.

## <a name="setting-up"></a>Configurazione

### <a name="creating-the-xcode-project"></a>Creazione del progetto Xcode

In Xcode creare un nuovo progetto iOS e selezionare il modello **app** . Verranno utilizzati gli storyboard UIKit. Non verranno creati test durante questa Guida introduttiva. È possibile deselezionare **Includi test**.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Screenshot che mostra la selezione del nuovo modello di progetto in Xcode.":::

Assegnare al progetto il nome `TeamsEmbedGettingStarted`.

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Screenshot che mostra i dettagli del nuovo progetto in Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installare il pacchetto e le dipendenze con CocoaPods

1. Creare un Podfile per l'applicazione:

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. Eseguire `pod install`.
3. Aprire la generata `.xcworkspace` con Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Richiedere l'accesso al microfono, alla fotocamera e così via.

Per accedere all'hardware del dispositivo, aggiornare l'elenco delle proprietà delle informazioni dell'app. Impostare il valore associato su un oggetto `string` che verrà incluso nella finestra di dialogo utilizzata dal sistema per richiedere l'accesso all'utente.

Fare clic con il pulsante destro del mouse sulla voce `Info.plist` dell'albero del progetto e scegliere **Open As** > **Source Code** (Apri come > Codice sorgente). Aggiungere le righe seguenti nella sezione `<dict>` di primo livello e quindi salvare il file.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Aggiungere il Framework di incorporamento dei team

1. Scaricare il Framework.
2. Creare una `Frameworks` cartella nella radice del progetto. Ex. `\TeamsEmbedGettingStarted\Frameworks\`
3. Copiare i `TeamsAppSDK.framework` Framework scaricati e scaricati `MeetingUIClient.framework` in questa cartella.
4. Aggiungere `TeamsAppSDK.framework` e `MeetingUIClient.framework` a la destinazione del progetto nella scheda generale. utilizzare `Add Other`  ->  `Add Files...` per passare ai file del Framework e aggiungerli.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Screenshot che illustra i Framework aggiunti in Xcode.":::

5. Se non è già stato fatto, aggiungere `$(PROJECT_DIR)/Frameworks` a `Framework Search Paths` nella scheda Impostazioni di compilazione di destinazione progetto. Per trovare l'impostazione, è possibile modificare il filtro da `basic` a `all` . è anche possibile usare la barra di ricerca a destra.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Screenshot che illustra il percorso di ricerca del Framework in Xcode.":::

### <a name="turn-off-bitcode"></a>Disattiva bitcode

Impostare `Enable Bitcode` l'opzione su `No` nelle impostazioni di compilazione del progetto. Per trovare l'impostazione, è possibile modificare il filtro da `basic` a `all` . è anche possibile usare la barra di ricerca a destra.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Screenshot che mostra l'opzione BitCode in Xcode.":::

### <a name="add-framework-signing-script"></a>Aggiungi script di firma del Framework

Selezionare la destinazione dell'app e scegliere la `Build Phases` scheda.  Fare quindi clic su `+` , seguito da `New Run Script Phase` . Verificare che la nuova fase avvenga dopo le `Embed Frameworks` fasi.



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Screenshot che illustra l'aggiunta dello script di compilazione in Xcode.":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>Attivare la modalità in background IP.

Selezionare la destinazione dell'app e fare clic sulla scheda funzionalità.

Attivare `Background Modes` facendo clic sulla parte `+ Capabilities` superiore e selezionare `Background Modes` .

Selezionare la casella di controllo per `Voice over IP` .

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Screenshot che mostra il VOIP abilitato in Xcode.":::

### <a name="add-a-window-reference-to-appdelegate"></a>Aggiungere un riferimento alla finestra a AppDelegate

Aprire il file **AppDelegate. Swift** del progetto e aggiungere un riferimento per "window".

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>Aggiungere un pulsante a ViewController

Creare un pulsante nel `viewDidLoad` callback in **ViewController. Swift**.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

Creare un'outlet per il pulsante in **ViewController. Swift**.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Aprire il file **ViewController. Swift** del progetto e aggiungere una `import` dichiarazione all'inizio del file per importare `AzureCommunication library` e `MeetingUIClient` . 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

Sostituire l'implementazione della `ViewController` classe con un pulsante semplice per consentire all'utente di partecipare a una riunione. La logica di business verrà collegata al pulsante in questa Guida introduttiva.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria di incorporamento dei team di servizi di comunicazione di Azure:

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | MeetingUIClient è il punto di ingresso principale della libreria di incorporamento dei team. |
| MeetingUIClientDelegate | MeetingUIClientDelegate viene usato per ricevere eventi, ad esempio modifiche nello stato della chiamata. |
| MeetingJoinOptions | MeetingJoinOptions vengono utilizzati per le opzioni configurabili, ad esempio il nome visualizzato. | 
| CallState | CallState viene usato per la segnalazione delle modifiche allo stato di chiamata. Le opzioni sono le seguenti: Connecting, waitingInLobby, connected e ended. |

## <a name="create-and-authenticate-the-client"></a>Creare e autenticare il client

Inizializzare un' `MeetingUIClient` istanza con un token di accesso utente che ci consentirà di partecipare alle riunioni. Aggiungere il codice seguente al `viewDidLoad` callback in **ViewController. Swift**:

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

Sostituire `<USER_ACCESS_TOKEN>` con un token di accesso utente valido per la risorsa. Se non è disponibile un token, vedere la documentazione dei [token di accesso utente](../../access-tokens.md).

### <a name="setup-token-refreshing"></a>Aggiornamento del token di installazione

Creare un metodo `fetchTokenAsync`. Quindi aggiungere la `fetchToken` logica per ottenere il token utente.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

Sostituire `<USER_ACCESS_TOKEN>` con un token di accesso utente valido per la risorsa.

## <a name="join-a-meeting"></a>Partecipa a una riunione

Il `joinMeeting` metodo viene impostato come azione che verrà eseguita quando viene toccato il pulsante *Unisci riunione* . Aggiornare l'implementazione di per partecipare a una riunione con `MeetingUIClient` :

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Sostituire `<MEETING URL>` con un collegamento per la riunione dei team.

### <a name="get-a-teams-meeting-link"></a>Ottenere un collegamento per la riunione di Team

Un collegamento per la riunione di team può essere recuperato usando le API Graph. Questa operazione è descritta in dettaglio nella [documentazione di Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
L'SDK per le chiamate di Servizi di comunicazione accetta un collegamento alla riunione di Teams completo. Questo collegamento viene restituito come parte della risorsa `onlineMeeting`, accessibile nella [proprietà `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). È anche possibile ottenere le informazioni sulla riunione necessarie dall'URL di **Partecipa alla riunione** nell'invito alla riunione di Teams stesso.

## <a name="run-the-code"></a>Eseguire il codice

È possibile creare ed eseguire un'app nel simulatore iOS selezionando **Product** > **Run** (Prodotto > Esegui) o premendo i tasti di scelta rapida &#8984;+R.

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="Aspetto finale dell'app di avvio rapido":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Aspetto finale dopo che la riunione è stata unita in join":::

> [!NOTE]
> La prima volta che si partecipa a una riunione, il sistema richiederà l'accesso al microfono. In un'applicazione di produzione è consigliabile usare l'API `AVAudioSession` per [controllare lo stato delle autorizzazioni](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) e aggiornare normalmente il comportamento dell'applicazione quando l'autorizzazione non viene concessa.

## <a name="add-localization-support-based-on-your-app"></a>Aggiunta del supporto per la localizzazione basata sull'app

Microsoft teams SDK supporta più di 100 stringhe e risorse. Il bundle del Framework contiene le lingue base e inglese. Gli altri elementi sono inclusi nel `Localizations.zip` file incluso nel pacchetto.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Aggiungere le localizzazioni all'SDK in base alle funzionalità supportate dall'app

1. Determinare il tipo di localizzazione supportata dall'applicazione dal progetto di app Xcode > informazioni > elenco di localizzazione
2. Decomprimere il Localizations.zip incluso nel pacchetto
3. Copiare le cartelle di localizzazione dalla cartella decompressa in base a quanto supportato dall'app nella radice di TeamsAppSDK. Framework

## <a name="preparation-for-app-store-upload"></a>Preparazione per il caricamento dell'app Store

Rimuovere le architetture i386 e x86_64 dai Framework in caso di archiviazione.

Aggiungere le `i386` `x86_64` architetture e rimuovendo lo script nelle fasi di compilazione prima della fase di coprogettazione del Framework se si vuole archiviare l'applicazione.

In Project Navigator selezionare il progetto. Nel riquadro dell'editor passare a fasi di compilazione → fare clic su + segno → crea una nuova fase Esegui script.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>Codice di esempio

È possibile scaricare l'app di esempio da [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started)
