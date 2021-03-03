---
title: Supportare l'autenticazione senza password con chiavi di FIDO2 nelle app sviluppate | Azure
titleSuffix: Microsoft identity platform
description: Questa guida alla distribuzione spiega come supportare l'autenticazione senza password con le chiavi di sicurezza FIDO2 nelle applicazioni sviluppate
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: e7193ee5d076538689078ce634b8170926df20b6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653253"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>Supporto dell'autenticazione senza password con chiavi di FIDO2 nelle app sviluppate

Queste configurazioni e procedure consigliate consentono di evitare scenari comuni che impediscono la disponibilità dell' [autenticazione con password FIDO2](../../active-directory/authentication/concept-authentication-passwordless.md) per gli utenti delle applicazioni.

## <a name="general-best-practices"></a>Procedure consigliate generali

### <a name="domain-hints"></a>Hint di dominio

Non usare un hint di dominio per ignorare l' [individuazione dell'area di autenticazione principale](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md). Questa funzionalità è progettata per semplificare l'accesso, ma il provider di identità federato potrebbe non supportare l'autenticazione senza password.

### <a name="requiring-specific-credentials"></a>Richiesta di credenziali specifiche

Se si utilizza SAML, non specificare che sia necessaria una password [utilizzando l'elemento RequestedAuthnContext](single-sign-on-saml-protocol.md#requestauthncontext).

L'elemento RequestedAuthnContext è facoltativo, quindi per risolvere il problema è possibile rimuoverlo dalle richieste di autenticazione SAML. Si tratta di una procedura consigliata generale, in quanto l'utilizzo di questo elemento può impedire il corretto funzionamento di altre opzioni di autenticazione, ad esempio l'autenticazione a più fattori.

### <a name="using-the-most-recently-used-authentication-method"></a>Uso del metodo di autenticazione usato più di recente

Il metodo di accesso usato più di recente da un utente verrà visualizzato per primo. Questo può causare confusione quando gli utenti ritengono che debbano usare la prima opzione presentata. Tuttavia, possono scegliere un'altra opzione selezionando "altri modi per accedere" come illustrato di seguito.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="Immagine dell'esperienza di autenticazione utente che evidenzia il pulsante che consente all'utente di modificare il metodo di autenticazione.":::

## <a name="platform-specific-best-practices"></a>Procedure consigliate specifiche della piattaforma

### <a name="desktop"></a>Desktop

Le opzioni consigliate per l'implementazione dell'autenticazione sono, nell'ordine:

- Le applicazioni desktop .NET che utilizzano Microsoft Authentication Library (MSAL) devono utilizzare Windows Authentication Manager (WAM). Questa integrazione e i relativi vantaggi sono [documentati in GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam).
- Usare [WebView2](/microsoft-edge/webview2/) per supportare FIDO2 in un browser incorporato.
- Usare il browser del sistema. Per impostazione predefinita, le librerie MSAL per le piattaforme desktop usano questo metodo. È possibile consultare la pagina relativa alla compatibilità del browser FIDO2 per verificare che il browser usato supporti l'autenticazione FIDO2.

### <a name="mobile"></a>Dispositivi mobili

A partire dal 2020 febbraio, FIDO2 non è attualmente supportato per le app native iOS o Android, ma è in fase di sviluppo.

Per preparare le applicazioni per la relativa disponibilità e, come procedura consigliata, le applicazioni iOS e Android devono usare MSAL con la configurazione predefinita di usando il Web browser di sistema.

Se non si usa MSAL, è comunque consigliabile usare il Web browser di sistema per l'autenticazione. Funzionalità come Single Sign-On e accesso condizionale si basano su una superficie Web condivisa fornita dal Web browser di sistema. Ciò significa usare [schede personalizzate Chrome](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) o [autenticare un utente tramite un servizio Web | Documentazione per sviluppatori Apple](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (iOS).

### <a name="web-and-single-page-apps"></a>App Web e a singola pagina

La disponibilità dell'autenticazione con password FIDO2 per le applicazioni in esecuzione in un Web browser dipende dalla combinazione di browser e piattaforma. È possibile consultare la [matrice di compatibilità di FIDO2](../authentication/fido2-compatibility.md) per verificare se la combinazione degli utenti verrà supportata.

## <a name="next-steps"></a>Passaggi successivi

[Opzioni di autenticazione con password per Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)