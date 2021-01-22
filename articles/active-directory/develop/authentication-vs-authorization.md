---
title: Autenticazione e autorizzazione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle nozioni di base sull'autenticazione e l'autorizzazione nella piattaforma di identità Microsoft (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5087278e5c89514cd43b7ca871a58f18e0fa98f2
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678147"
---
# <a name="authentication-vs-authorization"></a>Confronto tra autenticazione e autorizzazione

Questo articolo descrive l'autenticazione e l'autorizzazione. Illustra anche brevemente come usare la piattaforma Microsoft Identity per autenticare e autorizzare gli utenti nelle app Web, nelle API Web o nelle app che chiamano API Web protette. Se viene visualizzato un termine con cui non si ha familiarità, provare il [Glossario](developer-glossary.md) o i [video della piattaforma Microsoft Identity](identity-videos.md), che coprono i concetti di base.

## <a name="authentication"></a>Authentication

L' *autenticazione* è il processo di dimostrazione dell'utente. Viene talvolta abbreviato in *AuthN*. La piattaforma Microsoft Identity usa il protocollo [OpenID Connect](https://openid.net/connect/) per gestire l'autenticazione.

## <a name="authorization"></a>Autorizzazione

L'*autorizzazione* è la concessione del permesso di eseguire determinate operazioni a un'entità autenticata. Indica a quali dati può accedere e cosa può fare con tali dati. L'autorizzazione viene talvolta abbreviata in *Authz*. La piattaforma Microsoft Identity usa il protocollo [OAuth 2,0](https://oauth.net/2/) per gestire l'autorizzazione.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Autenticazione e autorizzazione con la piattaforma di identità Microsoft

La creazione di app che gestiscono le proprie informazioni relative a nome utente e password comporta un carico amministrativo elevato quando è necessario aggiungere o rimuovere utenti in più app. Al contrario, le app possono delegare tale responsabilità a un provider di identità centralizzato.

Azure Active Directory (Azure AD) è un provider di identità centralizzato su cloud. Delegare l'autenticazione e l'autorizzazione ad essa consente scenari come:

- Criteri di accesso condizionale che richiedono che un utente si trovi in una posizione specifica.
- L'uso di [autenticazione](../authentication/concept-mfa-howitworks.md)a più fattori, a volte definito autenticazione a due fattori o 2FA.
- Consentire a un utente di accedere una sola volta e quindi di accedere automaticamente a tutte le app Web che condividono la stessa directory centralizzata. Questa funzionalità è denominata *Single Sign-on (SSO)*.

La piattaforma di identità Microsoft semplifica l'autorizzazione e l'autenticazione per gli sviluppatori di applicazioni fornendo identità come servizio. Supporta i protocolli standard del settore e le librerie open source per piattaforme diverse che consentono di iniziare rapidamente a scrivere codice. Consente agli sviluppatori di creare applicazioni che accedono a tutte le identità Microsoft, ricevono i token per chiamare [Microsoft Graph](https://developer.microsoft.com/graph/), accedere alle API Microsoft o accedere ad altre API create dagli sviluppatori.

In questo video viene illustrata la piattaforma di identità Microsoft e le nozioni di base sull'autenticazione moderna: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Ecco un confronto tra i protocolli usati dalla piattaforma di identità Microsoft:

* **OAuth e OpenID Connect**: la piattaforma USA OAuth per l'autorizzazione e OpenID Connect (OIDC) per l'autenticazione. OpenID Connect si basa su OAuth 2.0, quindi la terminologia e il flusso di entrambi sono simili. È anche possibile autenticare un utente (tramite OpenID Connect) e ottenere l'autorizzazione per accedere a una risorsa protetta di proprietà dell'utente (tramite OAuth 2,0) in un'unica richiesta. Per altre informazioni vedere [Protocolli OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) e [Protocollo OpenID Connect](v2-protocols-oidc.md).
* **OAuth rispetto a SAML**: la piattaforma usa OAuth 2,0 per l'autorizzazione e SAML per l'autenticazione. Per altre informazioni su come usare questi protocolli insieme per autenticare un utente e ottenere l'autorizzazione per l'accesso a una risorsa protetta, vedere [Microsoft Identity Platform e OAuth 2,0 SAML Bearer Assert Flow](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect e SAML**: la piattaforma usa OpenID Connect e SAML per autenticare un utente e abilitare Single Sign-on. L'autenticazione SAML viene comunemente usata con i provider di identità, ad esempio Active Directory Federation Services (AD FS) federati per Azure AD, quindi viene spesso usata nelle applicazioni aziendali. OpenID Connect viene comunemente usato per le app che sono esclusivamente nel cloud, ad esempio app per dispositivi mobili, siti Web e API Web.

## <a name="next-steps"></a>Passaggi successivi

Per altri argomenti che riguardano le nozioni di base sull'autenticazione e sull'autorizzazione:

* Per informazioni sull'uso di token di accesso, token di aggiornamento e token ID nell'autorizzazione e nell'autenticazione, vedere [token di sicurezza](security-tokens.md).
* Per informazioni sul processo di registrazione dell'applicazione in modo che possa integrarsi con la piattaforma di identità Microsoft, vedere [modello di applicazione](application-model.md).