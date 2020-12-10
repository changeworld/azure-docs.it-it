---
title: Scenario di scambio di token della piattaforma Microsoft Identity con SAML e OIDC/OAuth in Azure Active Directory
description: Informazioni sugli scenari comuni di scambio di token quando si utilizzano SAML e OIDC/OAuth in Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 16de95e177f6be1b5bb2517b5ae3ca595cfb5766
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939170"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Scenari di scambio di token della piattaforma di identità Microsoft con SAML e OIDC/OAuth

SAML e OpenID Connect (OIDC)/OAuth sono protocolli diffusi usati per implementare Single Sign-On (SSO). Alcune app possono implementare solo SAML e altre possono implementare solo OIDC/OAuth. Entrambi i protocolli usano i token per comunicare i segreti. Per ulteriori informazioni su SAML, vedere il [protocollo Single Sign-On SAML](single-sign-on-saml-protocol.md). Per altre informazioni su OIDC/OAuth, vedere la pagina [relativa ai protocolli oauth 2,0 e OpenID Connect sulla piattaforma di identità Microsoft](active-directory-v2-protocols.md).

Questo articolo descrive uno scenario comune in cui un'app implementa SAML, ma è necessario effettuare una chiamata all'API Graph, che usa OIDC/OAuth. Le linee guida di base vengono fornite per gli utenti che utilizzano questo scenario.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Scenario: si ha un token SAML e si vuole chiamare il API Graph
Molte app sono implementate con SAML. Tuttavia, il API Graph usa i protocolli OIDC/OAuth. È possibile, anche se non banale, aggiungere la funzionalità OIDC/OAuth a un'app SAML. Quando la funzionalità OAuth è disponibile in un'app, è possibile usare la API Graph.

La strategia generale consiste nell'aggiungere lo stack OIDC/OAuth all'app. Con l'app che implementa entrambi gli standard è possibile usare un cookie di sessione. Non si scambia un token in modo esplicito. Si sta registrando un utente in con SAML, che genera un cookie di sessione. Quando il API Graph richiama un flusso OAuth, il cookie di sessione viene usato per l'autenticazione. Questa strategia presuppone il superamento dei controlli di accesso condizionale e l'autorizzazione dell'utente.

> [!NOTE]
> La libreria consigliata per l'aggiunta del comportamento OIDC/OAuth è Microsoft Authentication Library (MSAL). Per ulteriori informazioni su MSAL, vedere [Panoramica di Microsoft Authentication Library (MSAL)](msal-overview.md). La libreria precedente è stata chiamata Active Directory Authentication Library (ADAL), ma non è consigliata perché MSAL la sostituisce.

## <a name="next-steps"></a>Passaggi successivi
- [flussi di autenticazione e scenari di applicazioni](authentication-flows-app-scenarios.md)
