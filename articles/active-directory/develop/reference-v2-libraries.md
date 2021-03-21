---
title: Librerie di autenticazione della piattaforma Microsoft Identity | Azure
description: Elenco delle librerie client e del middleware compatibili con la piattaforma di identità Microsoft. Usare queste librerie per aggiungere il supporto per l'accesso utente (autenticazione) e l'accesso all'API Web protetto (autorizzazione) alle applicazioni.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 590e57d587c8e6e254811892b5c5e740b511c302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104690654"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Librerie di autenticazione della piattaforma Microsoft Identity

Le tabelle seguenti illustrano il supporto della libreria di autenticazione Microsoft per diversi tipi di applicazioni. Includono collegamenti al codice sorgente della libreria, dove ottenere il pacchetto per il progetto dell'app e se la libreria supporta l'accesso utente (autenticazione), l'accesso alle API Web protette (autorizzazione) o entrambi.

La piattaforma Microsoft Identity è stata certificata da OpenID Foundation come [provider OpenID certificato](https://openid.net/certification/). Se si preferisce usare una libreria diversa da Microsoft Authentication Library (MSAL) o da un'altra libreria supportata da Microsoft, sceglierne una con un' [implementazione certificata di OpenID Connect](https://openid.net/developers/certified/).

Se si sceglie di scrivere manualmente l'implementazione a livello di protocollo di [OAuth 2,0 o OpenID Connect 1,0](active-directory-v2-protocols.md), prestare particolare attenzione alle considerazioni sulla sicurezza nella specifica di ogni standard e seguire una metodologia SDL (Software Development Lifecycle) come [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Applicazione a pagina singola (SPA)

Un'applicazione a singola pagina viene eseguita interamente sull'area del browser e recupera i dati della pagina (HTML, CSS e JavaScript) in modo dinamico o in fase di caricamento dell'applicazione. Può chiamare API Web per interagire con le origini dati back-end.

Poiché il codice di una SPA viene eseguito interamente nel browser, viene considerato un *client pubblico* che non è in grado di archiviare i segreti in modo sicuro.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Applicazione Web

Un'applicazione Web esegue il codice in un server che genera e Invia HTML, CSS e JavaScript al Web browser di un utente di cui eseguire il rendering. L'identità dell'utente viene mantenuta come una sessione tra il browser dell'utente (front-end) e il server Web (il back-end).

Poiché il codice di un'applicazione Web viene eseguito sul server Web, viene considerato un *client riservato* in grado di archiviare i segreti in modo sicuro.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Applicazione desktop

Un'applicazione desktop è in genere codice binario (compilato) che espone un'interfaccia utente ed è destinata all'esecuzione sul desktop di un utente.

Poiché un'applicazione desktop viene eseguita sul desktop dell'utente, viene considerato un *client pubblico* che non è in grado di archiviare in modo sicuro i segreti.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Applicazione per dispositivi mobili

Un'applicazione per dispositivi mobili è in genere codice binario (compilato) che espone un'interfaccia utente ed è destinata all'esecuzione sul dispositivo mobile di un utente.

Poiché un'applicazione per dispositivi mobili viene eseguita sul dispositivo mobile dell'utente, viene considerato un *client pubblico* che non è in grado di archiviare i segreti in modo sicuro.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Servizio/daemon

I servizi e i daemon sono comunemente usati per la comunicazione da server a server e altre comunicazioni automatiche (talvolta denominate senza *intestazioni*). Poiché non è presente alcun utente sulla tastiera per immettere le credenziali o consentire l'accesso alle risorse, queste applicazioni vengono autenticate come se stesse, non come utente, quando richiede l'accesso autorizzato alle risorse di un'API Web.

Un servizio o daemon eseguito in un server è considerato un *client riservato* in grado di archiviare i propri segreti in modo sicuro.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Microsoft Authentication Library, vedere la pagina relativa alla [Panoramica di Microsoft Authentication Library (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
