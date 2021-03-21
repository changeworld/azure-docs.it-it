---
title: Spostare un'app a singola pagina in produzione
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'applicazione a singola pagina (passare alla produzione)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9656da8be086724482f129efab323e02b73e117e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954962"
---
# <a name="single-page-application-move-to-production"></a>Applicazione a pagina singola: sposta in produzione

Ora che si è appreso come acquisire un token per chiamare le API Web, di seguito sono riportati alcuni aspetti da considerare quando si trasferisce l'applicazione in produzione.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>Distribuire l'app

Vedere un [esempio di distribuzione](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3) per apprendere come distribuire i progetti di API Web e di Spa con archiviazione di Azure e i servizi di app Azure, rispettivamente. 

## <a name="code-samples"></a>Esempi di codice

Questi esempi di codice illustrano diverse operazioni chiave per un'app a singola pagina.
- [Spa con un back-end ASP.NET](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): come ottenere i token per la propria API Web back-end (ASP.NET Core) usando **MSAL.js**.

- [Node.js API Web (Azure ad](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): come convalidare i token di accesso per l'API Web back-end (Node.js) usando **Passport-Azure-ad**.

- [Spa con Azure ad B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp): come usare **MSAL.js** per l'accesso degli utenti in un'app registrata con **Azure Active Directory B2C** (Azure ad B2C).

- [ API Web diNode.js (Azure ad B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): come usare **Passport-Azure-ad** per convalidare i token di accesso per le app registrate con **Azure Active Directory B2C** (Azure ad B2C).

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione su JavaScript Spa](./tutorial-v2-javascript-spa.md): approfondimento su come eseguire l'accesso agli utenti e ottenere un token di accesso per chiamare l' **API Microsoft Graph** usando **MSAL.js**.