---
title: includere file
description: includere file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 1318c47bcded47159006977db09604bb53674973
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487932"
---
[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Il **gruppo di servizi di comunicazione di Azure che chiama l'esempio Hero per iOS** illustra come usare i servizi di comunicazione che chiamano la libreria client iOS per creare un'esperienza di chiamata di gruppo che includa voce e video. In questa Guida introduttiva di esempio si apprenderà come impostare ed eseguire l'esempio. Per il contesto viene fornita una panoramica dell'esempio.

## <a name="overview"></a>Panoramica

L'esempio è un'applicazione iOS nativa che usa le librerie client iOS dei servizi di comunicazione di Azure per creare un'esperienza di chiamata che include chiamate vocali e video. L'applicazione usa un componente lato server per eseguire il provisioning dei token di accesso che vengono quindi usati per inizializzare la libreria client dei servizi di comunicazione di Azure. Per configurare questo componente lato server, è possibile seguire l'esercitazione [servizio trusted con funzioni di Azure](../../tutorials/trusted-service-tutorial.md) .

L'esempio ha l'aspetto seguente:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Screenshot che mostra la pagina di destinazione dell'applicazione di esempio.":::

Quando si preme il pulsante "Avvia nuova chiamata", l'applicazione iOS crea una nuova chiamata e la aggiunge. L'applicazione consente anche di partecipare a una chiamata esistente di servizi di comunicazione di Azure specificando l'ID della chiamata esistente.

Dopo aver partecipato a una chiamata, verrà richiesto di concedere all'applicazione l'autorizzazione per accedere alla fotocamera e al microfono. Verrà inoltre richiesto di specificare un nome visualizzato.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Screenshot che mostra la schermata di pre-chiamata dell'applicazione di esempio.":::

Una volta configurati il nome visualizzato e i dispositivi, è possibile partecipare alla chiamata. Verrà visualizzata l'area di disegno principale in cui risiede l'esperienza di chiamata principale.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Screenshot che mostra la schermata principale dell'applicazione di esempio.":::

Componenti della schermata principale della chiamata:

- **Raccolta multimediale**: l'area principale in cui vengono visualizzati i partecipanti. Se un partecipante ha la fotocamera abilitata, il suo feed video viene visualizzato qui. Ogni partecipante ha un singolo riquadro che mostra il nome visualizzato e il flusso video (quando ne esiste uno). La raccolta supporta più partecipanti e viene aggiornata quando i partecipanti vengono aggiunti o rimossi alla chiamata.
- **Barra delle azioni**: questa è la posizione in cui si trovano i controlli delle chiamate primarie. Questi controlli consentono di attivare o disattivare il video e il microfono, condividere lo schermo e lasciare la chiamata.

Di seguito sono disponibili ulteriori informazioni sui prerequisiti e i passaggi da seguire per configurare l'esempio.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un Mac che esegue [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), insieme a un certificato dello sviluppatore valido installato nel portachiavi.
- Una risorsa di servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [Creare una risorsa di Servizi di comunicazione di Azure](../../quickstarts/create-communication-resource.md).
- Funzione di Azure che esegue la [logica del servizio attendibile](../../tutorials/trusted-service-tutorial.md) per recuperare i token di accesso.

## <a name="running-sample-locally"></a>Esecuzione di esempio localmente

Il gruppo chiamante di esempio può essere eseguito localmente con XCode. Gli sviluppatori possono usare il proprio dispositivo fisico o un emulatore per testare l'applicazione.

### <a name="before-running-the-sample-for-the-first-time"></a>Prima di eseguire l'esempio per la prima volta

1. Installare le dipendenze eseguendo `pod install` .
2. Apri `ACSCall.xcworkspace` in Xcode.
3. Aggiornare `AppSettings.plist`. Impostare il valore della `acsTokenFetchUrl` chiave in modo che sia l'URL dell'endpoint di autenticazione.

### <a name="run-sample"></a>Eseguire l'esempio

Compilare ed eseguire l'esempio in XCode.

## <a name="optional-securing-an-authentication-endpoint"></a>Opzionale Protezione di un endpoint di autenticazione

A scopo dimostrativo, per impostazione predefinita in questo esempio viene usato un endpoint accessibile pubblicamente per recuperare un token di servizi di comunicazione di Azure. Per gli scenari di produzione, è consigliabile usare un endpoint protetto per eseguire il provisioning di token personalizzati.

Con la configurazione aggiuntiva, questo esempio supporta la connessione a un endpoint protetto **Azure Active Directory** (Azure ad), in modo che l'accesso utente sia necessario per l'app per recuperare un token di servizi di comunicazione di Azure. Vedere i passaggi seguenti:

1. Abilitare l'autenticazione Azure Active Directory nell'app.  
   - [Registrare l'app in Azure Active Directory (usando le impostazioni della piattaforma iOS/macOS)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [Configurare un'app del servizio app o di Funzioni di Azure per l'uso dell'account di accesso di Azure AD](../../../app-service/configure-authentication-provider-aad.md)
2. Passare alla pagina di panoramica dell'app registrata in Azure Active Directory registrazioni per l'app. Prendere nota del `Application (client) ID` , `Directory (tenant) ID` , `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure Active Directory configurazione su portale di Azure.":::

3. Aprire `AppSettings.plist` in Xcode, aggiungere i valori chiave seguenti:
   - `acsTokenFetchUrl`: URL per richiedere il token dei servizi di comunicazione di Azure 
   - `isAADAuthEnabled`: Valore booleano che indica se l'autenticazione del token dei servizi di comunicazione di Azure è obbligatoria o meno
   - `aadClientId`: ID dell'applicazione (client)
   - `aadTenantId`: ID directory (tenant)
   - `aadRedirectURI`: L'URI di reindirizzamento deve avere il formato seguente: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: Matrice di ambiti di autorizzazione richiesta dagli utenti per l'autorizzazione. Aggiungere `<Application ID URI>/user_impersonation` alla matrice per concedere l'accesso all'endpoint di autenticazione

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- Acquisire familiarità con l'[uso della libreria client Chiamate](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Leggere altre informazioni sul [funzionamento delle chiamate](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Altre letture

- [Esempi: trovare](./../overview.md) altri esempi ed esempi nella pagina Panoramica degli esempi.