---
title: Token di sicurezza | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle nozioni di base dei token di sicurezza nella piattaforma di identità Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795636"
---
# <a name="security-tokens"></a>Token di sicurezza

Un provider di identità centralizzato è particolarmente utile per le app che hanno utenti dislocati in tutto il mondo che non eseguono necessariamente l'accesso dalla rete aziendale. La piattaforma Microsoft Identity autentica gli utenti e fornisce i token di sicurezza, ad esempio [token di accesso](developer-glossary.md#access-token), token di [aggiornamento](developer-glossary.md#refresh-token)e [token ID](developer-glossary.md#id-token). I token di sicurezza consentono a un' [applicazione client](developer-glossary.md#client-application) di accedere alle risorse protette in un [server di risorse](developer-glossary.md#resource-server).

**Token** di accesso: un token di accesso è un token di sicurezza emesso da un [server di autorizzazione](developer-glossary.md#authorization-server) come parte di un flusso [OAuth 2,0](active-directory-v2-protocols.md) . Contiene informazioni sull'utente e la risorsa per cui è previsto il token. Le informazioni possono essere usate per accedere alle API Web e ad altre risorse protette. I token di accesso vengono convalidati dalle risorse per concedere l'accesso a un'app client. Per altre informazioni sul modo in cui la piattaforma di identità Microsoft rilascia i token di accesso, vedere [token di accesso](access-tokens.md).

**Token di aggiornamento**: poiché i token di accesso sono validi solo per un breve periodo di tempo, i server di autorizzazione emetteranno a volte un token di aggiornamento nello stesso momento in cui viene emesso il token di accesso. Quando necessario, l'applicazione client può scambiare questo token di aggiornamento per un nuovo token di accesso. Per altre informazioni sul modo in cui la piattaforma di identità Microsoft usa i token di aggiornamento per revocare le autorizzazioni, vedere revoca dei [token](access-tokens.md#token-revocation).

**Token ID**: i token ID vengono inviati all'applicazione client come parte di un flusso di [OpenID Connect](v2-protocols-oidc.md) . Possono essere inviati insieme a un token di accesso anziché a un token di accesso. I token ID vengono utilizzati dal client per autenticare l'utente. Per altre informazioni sul modo in cui la piattaforma di identità Microsoft rilascia i token ID, vedere [token ID](id-tokens.md).

> [!NOTE]
> Questo articolo illustra i token di sicurezza usati dai protocolli OAuth2 e OpenID Connect. Molte applicazioni aziendali usano SAML per autenticare gli utenti. Per informazioni sulle asserzioni SAML, vedere [Azure Active Directory riferimento al token SAML](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Convalidare i token di sicurezza

È l'app per cui è stato generato il token, l'app Web che ha eseguito l'accesso all'utente o l'API Web chiamata per convalidare il token. Il token è firmato dal server di autorizzazione con una chiave privata. Il server di autorizzazione pubblica la chiave pubblica corrispondente. Per convalidare un token, l'app verifica la firma usando la chiave pubblica del server di autorizzazione per verificare che la firma sia stata creata usando la chiave privata.

I token sono validi solo per un periodo di tempo limitato. In genere, il server di autorizzazione fornisce una coppia di token, ad esempio:

* Un token di accesso, che accede all'applicazione o alla risorsa protetta.
* Un token di aggiornamento, che viene usato per aggiornare il token di accesso quando il token di accesso è prossimo alla scadenza.

I token di accesso vengono passati a un'API Web come bearer token nell' `Authorization` intestazione. Un'app può fornire un token di aggiornamento al server di autorizzazione. Se l'accesso dell'utente all'app non è stato revocato, verrà restituito un nuovo token di accesso e un nuovo token di aggiornamento. Questo è il modo in cui viene gestito lo scenario di un utente che lascia l'organizzazione. Quando il server di autorizzazione riceve il token di aggiornamento, non emette un altro token di accesso valido se l'utente non è più autorizzato.

## <a name="json-web-tokens-and-claims"></a>Attestazioni e token Web JSON

La piattaforma di identità Microsoft implementa i token di sicurezza come token Web JSON (token JWT) che contengono *attestazioni*. Poiché token JWT vengono usati come token di sicurezza, questa forma di autenticazione viene talvolta denominata *autenticazione JWT*.

Un' [attestazione](developer-glossary.md#claim) fornisce asserzioni su un'entità, ad esempio un'applicazione client o un [proprietario della risorsa](developer-glossary.md#resource-owner), a un'altra entità, ad esempio un server di risorse. Un'attestazione può anche essere indicata come attestazione JWT o richiesta di token Web JSON.

Le attestazioni sono coppie nome/valore che inoltrano informazioni sull'oggetto del token. Ad esempio, un'attestazione potrebbe contenere fact sull'entità di sicurezza autenticata dal server di autorizzazione. Le attestazioni presenti in un token specifico dipendono da molti elementi, ad esempio il tipo di token, il tipo di credenziale usato per autenticare l'oggetto e la configurazione dell'applicazione.

Le applicazioni possono utilizzare attestazioni per diverse attività, ad esempio:

* Convalidare il token.
* Identificare il [tenant](developer-glossary.md#tenant)del soggetto del token.
* Visualizzare le informazioni utente.
* Determinare l'autorizzazione dell'oggetto.

Un'attestazione è costituita da coppie chiave-valore che forniscono informazioni come:

* Server del token di sicurezza che ha generato il token.
* Data di generazione del token.
* Oggetto, ad esempio l'utente, ad eccezione dei daemon.
* Destinatari, ovvero l'app per cui è stato generato il token.
* App (il client) che ha richiesto il token. Nel caso di app Web, questa app potrebbe essere identica a quella dei destinatari.

Per altre informazioni sul modo in cui la piattaforma di identità Microsoft implementa i token e le informazioni sulle attestazioni, vedere token di [accesso](access-tokens.md) e [token ID](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Modo in cui ogni flusso emette token e codici

A seconda del modo in cui viene compilato il client, può usare uno o più dei flussi di autenticazione supportati dalla piattaforma di identità Microsoft. Questi flussi possono produrre diversi token (token ID, token di aggiornamento, token di accesso) e codici di autorizzazione. Richiedono token diversi per consentirne il funzionamento. Questa tabella fornisce una panoramica.

|Flusso | Richiede | token ID | Token di accesso | Token di aggiornamento | Codice di autorizzazione |
|-----|----------|----------|--------------|---------------|--------------------|
|[Flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Flusso implicito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Flusso OIDC ibrido](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Riscatto del token di aggiornamento](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Token di aggiornamento | x | x | x| |
|[Flusso on-behalf-of](v2-oauth2-on-behalf-of-flow.md) | Token di accesso| x| x| x| |
|[Credenziali del client](v2-oauth2-client-creds-grant-flow.md) | | | x (solo app)| | |

I token emessi tramite la modalità implicita hanno una limitazione di lunghezza perché vengono passati di nuovo al browser tramite l'URL, dove `response_mode` è `query` o `fragment` . Alcuni browser hanno un limite per le dimensioni dell'URL che possono essere inseriti nella barra del browser e hanno esito negativo quando è troppo lungo. Di conseguenza, questi token non dispongono di `groups` `wids` attestazioni o.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'autenticazione e l'autorizzazione nella piattaforma di identità Microsoft, vedere gli articoli seguenti:

* Per informazioni sui concetti di base di autenticazione e autorizzazione, vedere [autenticazione e autorizzazione](authentication-vs-authorization.md).
* Per informazioni sulla registrazione dell'applicazione per l'integrazione, vedere [modello di applicazione](application-model.md).
* Per informazioni sul flusso di accesso di app Web, desktop e per dispositivi mobili, vedere [flusso di accesso all'app](app-sign-in-flow.md).
