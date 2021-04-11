---
title: includere file
description: includere file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: bd8b37620336e595cdd95945fcf012f6b07e0afe
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "106072860"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-android-ios.md)]


Il gruppo di servizi **di comunicazione di Azure che chiama l'esempio Hero per Android** illustra il modo in cui i servizi di comunicazione che chiamano Android SDK possono essere usati per creare un'esperienza di chiamata di gruppo che include voce e video. In questa Guida introduttiva di esempio si apprenderà come impostare ed eseguire l'esempio. Per il contesto viene fornita una panoramica dell'esempio.

## <a name="download-code"></a>Scaricare il codice

Trovare il codice finalizzato per questa Guida introduttiva su [GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero).

## <a name="overview"></a>Panoramica

L'esempio è un'applicazione Android nativa che usa i servizi di comunicazione di Azure Android SDK per creare un'esperienza di chiamata che include chiamate vocali e video. L'applicazione usa un componente lato server per eseguire il provisioning dei token di accesso che vengono quindi usati per inizializzare Azure Communication Services SDK. Per configurare questo componente lato server, è possibile seguire l'esercitazione [servizio trusted con funzioni di Azure](../../tutorials/trusted-service-tutorial.md) .

L'esempio ha l'aspetto seguente:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Screenshot che mostra la pagina di destinazione dell'applicazione di esempio.":::

Quando si preme il pulsante "Avvia nuova chiamata", l'applicazione Android crea una nuova chiamata e la aggiunge. L'applicazione consente anche di partecipare a una chiamata esistente di servizi di comunicazione di Azure specificando l'ID della chiamata esistente.

Dopo aver partecipato a una chiamata, verrà richiesto di concedere all'applicazione l'autorizzazione per accedere alla fotocamera e al microfono. Verrà inoltre richiesto di specificare un nome visualizzato.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Screenshot che mostra la schermata di pre-chiamata dell'applicazione di esempio.":::

Una volta configurati il nome visualizzato e i dispositivi, è possibile partecipare alla chiamata. Verrà visualizzata l'area di disegno principale in cui risiede l'esperienza di chiamata principale.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Screenshot che mostra la schermata principale dell'applicazione di esempio.":::

Componenti della schermata principale della chiamata:

- **Raccolta multimediale**: l'area principale in cui vengono visualizzati i partecipanti. Se un partecipante ha la fotocamera abilitata, il suo feed video viene visualizzato qui. Ogni partecipante ha un singolo riquadro che mostra il nome visualizzato e il flusso video (quando ne esiste uno). La raccolta supporta più partecipanti e viene aggiornata quando i partecipanti vengono aggiunti o rimossi alla chiamata.
- **Barra delle azioni**: questa è la posizione in cui si trovano i controlli delle chiamate primarie. Questi controlli consentono di attivare o disattivare il video e il microfono, condividere lo schermo e lasciare la chiamata.

Di seguito sono disponibili ulteriori informazioni sui prerequisiti e i passaggi da seguire per configurare l'esempio.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) in esecuzione nel computer
- Una risorsa di servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di Servizi di comunicazione di Azure](../../quickstarts/create-communication-resource.md).
- Funzione di Azure che esegue l' [endpoint di autenticazione](../../tutorials/trusted-service-tutorial.md) per recuperare i token di accesso.

## <a name="running-sample-locally"></a>Esecuzione di esempio localmente

L'esempio di chiamata di gruppo può essere eseguito localmente utilizzando Android Studio. Gli sviluppatori possono usare il proprio dispositivo fisico o un emulatore per testare l'applicazione.

### <a name="before-running-the-sample-for-the-first-time"></a>Prima di eseguire l'esempio per la prima volta

1. Aprire Android Studio e selezionare `Open an Existing Project`
2. Aprire la `AzureCalling` cartella all'interno della versione scaricata per l'esempio.
3. Espandi app/asset da aggiornare `appSettings.properties` . Impostare il valore della chiave `communicationTokenFetchUrl` in modo che sia l'URL dell'endpoint di autenticazione configurato come prerequisito.

### <a name="run-sample"></a>Eseguire l'esempio

Compilare ed eseguire l'esempio in Android Studio.

## <a name="optional-securing-an-authentication-endpoint"></a>Opzionale Protezione di un endpoint di autenticazione

A scopo dimostrativo, per impostazione predefinita in questo esempio viene usato un endpoint accessibile pubblicamente per recuperare un token di servizi di comunicazione di Azure. Per gli scenari di produzione, è consigliabile usare un endpoint protetto per eseguire il provisioning di token personalizzati.

Con la configurazione aggiuntiva, questo esempio supporta la connessione a un endpoint protetto **Azure Active Directory** (Azure ad), in modo che l'accesso utente sia necessario per l'app per recuperare un token di servizi di comunicazione di Azure. Vedere i passaggi seguenti:

1. Abilitare l'autenticazione Azure Active Directory nell'app.  
   - [Registrare l'app in Azure Active Directory (usando le impostazioni della piattaforma Android)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Configurare un'app del servizio app o di Funzioni di Azure per l'uso dell'account di accesso di Azure AD](../../../app-service/configure-authentication-provider-aad.md)

2. Passare alla pagina di panoramica dell'app registrata in Azure Active Directory registrazioni per l'app. Prendere nota del `Package name` , `Signature hash` , `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Azure Active Directory configurazione su portale di Azure.":::

3. Modificare `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` e impostare `isAADAuthEnabled` per abilitare Azure Active Directory
4. Modificare `AndroidManifest.xml` e impostare sull' `android:path` hash della firma dell'archivio chiavi. (Facoltativo. Il valore corrente usa hash da debug. keystore in bundle. Se si usa un archivio chiavi diverso, è necessario aggiornarlo.
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Copiare la configurazione di MSAL Android da portale di Azure e incollare in `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` . Includi "account_mode": "singolo"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Modificare `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` e impostare il valore della chiave `communicationTokenFetchUrl` in modo che sia l'URL per l'endpoint di autenticazione sicura.
7. Modificare `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` e impostare il valore per la chiave `aadScopes` dagli `Azure Active Directory` `Expose an API` ambiti

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

>[!div class="nextstepaction"]
>[È possibile scaricare l'esempio da GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero)

Per altre informazioni, vedere gli articoli seguenti:

- Familiarizzare con [l'uso dell'SDK chiamante](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Leggere altre informazioni sul [funzionamento delle chiamate](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Altre letture

- [Pagina GitHub dei servizi di comunicazione di Azure](https://github.com/Azure/communication) - Nella pagina ufficiale di GitHub sono disponibili altri esempi e informazioni
- [Esempi: trovare](./../overview.md) altri esempi ed esempi nella pagina Panoramica degli esempi.
- [Funzionalità di chiamata della comunicazione di Azure](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features) -per altre informazioni su come chiamare Android SDK-[Azure Communication Android Calling SDK](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)
