---
title: Registrare le app desktop che chiamano API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app desktop che chiama le API Web (registrazione dell'app)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 263397aa2cd09ba24fa750131b76047801869a65
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798936"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>App desktop che chiama le API Web: registrazione dell'app

Questo articolo illustra le specifiche di registrazione delle app per un'applicazione desktop.

## <a name="supported-account-types"></a>Tipi di account supportati

I tipi di account supportati in un'applicazione desktop dipendono dall'esperienza che si desidera attivare. A causa di questa relazione, i tipi di conto supportati dipendono dai flussi che si desidera utilizzare.

### <a name="audience-for-interactive-token-acquisition"></a>Destinatari per l'acquisizione di token interattivi

Se l'applicazione desktop usa l'autenticazione interattiva, è possibile accedere agli utenti da qualsiasi [tipo di account](quickstart-register-app.md).

### <a name="audience-for-desktop-app-silent-flows"></a>Destinatari per flussi invisibile all'app desktop

- Per usare l'autenticazione integrata di Windows o un nome utente e una password, l'applicazione deve eseguire l'accesso degli utenti nel proprio tenant, ad esempio, se si è uno sviluppatore line-of-business (LOB). In alternativa, in Azure Active Directory organizzazioni, l'applicazione deve eseguire l'accesso degli utenti nel proprio tenant se si tratta di uno scenario ISV. Questi flussi di autenticazione non sono supportati per gli account personali Microsoft.
- Se si esegue l'accesso agli utenti con identità di social networking che passano un'autorità e un criterio business-to-Commerce (B2C), è possibile usare solo l'autenticazione interattiva e nome utente-password.

## <a name="redirect-uris"></a>URI di reindirizzamento

Gli URI di Reindirizzamento da usare in un'applicazione desktop dipendono dal flusso che si vuole usare.

Specificare l'URI di reindirizzamento per l'app [configurando le impostazioni della piattaforma](quickstart-register-app.md#add-a-redirect-uri) per l'app in **registrazioni app** nel portale di Azure.

- Per le app che usano l'autenticazione interattiva:
  - App che usano browser incorporati: `https://login.microsoftonline.com/common/oauth2/nativeclient`
  - App che usano i browser di sistema: `http://localhost`

  > [!IMPORTANT]
  > Come procedura consigliata di sicurezza, è consigliabile impostare in modo esplicito `https://login.microsoftonline.com/common/oauth2/nativeclient` o `http://localhost` come URI di reindirizzamento. Alcune librerie di autenticazione, ad esempio MSAL.NET, usano un valore predefinito `urn:ietf:wg:oauth:2.0:oob` quando non viene specificato nessun altro URI di reindirizzamento, operazione sconsigliata. Questa impostazione predefinita verrà aggiornata come modifica sostanziale nella prossima versione principale.

- Se si compila un'app Objective-C o SWIFT nativa per macOS, registrare l'URI di reindirizzamento in base all'identificatore del bundle dell'applicazione nel formato seguente: `msauth.<your.app.bundle.id>://auth` . Sostituire `<your.app.bundle.id>` con l'identificatore del bundle dell'applicazione.
- Se si compila un'app Node.js Electron, usare un protocollo file personalizzato invece di un normale URI di reindirizzamento Web (https://) per gestire il passaggio di reindirizzamento del flusso di autorizzazione, ad esempio `msal://redirect` . Il nome del protocollo file personalizzato non dovrebbe essere ovvio e deve seguire i suggerimenti nella [specifica di OAuth 2.0 per le app native](https://tools.ietf.org/html/rfc8252#section-7.1).
- Se l'app usa solo l'autenticazione integrata di Windows o un nome utente e una password, non è necessario registrare un URI di reindirizzamento per l'applicazione. Questi flussi eseguono una round trip all'endpoint della piattaforma Microsoft Identity Platform v 2.0. L'applicazione non verrà richiamata su un URI specifico.
- Per distinguere [il flusso del codice del dispositivo](scenario-desktop-acquire-token.md#device-code-flow), [l'autenticazione integrata di Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication)e un [nome utente e una password](scenario-desktop-acquire-token.md#username-and-password) da un'applicazione client riservata usando un flusso di credenziali client usato nelle [applicazioni daemon](scenario-daemon-overview.md), nessuna delle quali richiede un URI di reindirizzamento, lo configura come applicazione client pubblica. Per ottenere questa configurazione:

    1. Nella <a href="https://portal.azure.com/" target="_blank">portale di Azure</a>selezionare l'app in **registrazioni app** e quindi selezionare **autenticazione**.
    1. In **Impostazioni avanzate**  >  **Consenti flussi client pubblici**  >  **Abilita i flussi di dispositivi mobili e desktop seguenti:** selezionare **Sì**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Abilitare l'impostazione client pubblica nel riquadro autenticazione in portale di Azure":::

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni desktop chiamano le API per l'utente che ha eseguito l'accesso. Devono richiedere autorizzazioni delegate. Non possono richiedere le autorizzazioni dell'applicazione, che vengono gestite solo nelle [applicazioni daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, configurazione del [codice dell'app](scenario-desktop-app-configuration.md).
