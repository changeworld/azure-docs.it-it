---
title: Spostare un'app daemon che chiama API Web nell'ambiente di produzione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come spostare un'app daemon che chiama API Web nell'ambiente di produzione
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8dc9bff86a07f3d4a0ec6fd224de6d5633165a6d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582842"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>App daemon che chiama le API Web-passa all'ambiente di produzione

Ora che si è appreso come acquisire e usare un token per una chiamata da servizio a servizio, vedere come spostare l'app nell'ambiente di produzione.

## <a name="deployment---multitenant-daemon-apps"></a>Distribuzione-app daemon multi-tenant

Se si è un ISV che crea un'applicazione daemon che può essere eseguita in più tenant, assicurarsi che l'amministratore del tenant:

- Effettua il provisioning di un'entità servizio per l'applicazione.
- Concede il consenso all'applicazione.

È necessario spiegare ai clienti come eseguire queste operazioni. Per altre informazioni, vedere [richiesta di consenso per un intero tenant](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Esempi di codice

# <a name="net"></a>[.NET](#tab/dotnet)

- Documentazione di riferimento per:
  - Creazione di un'istanza di [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Chiamata a [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Altri esempi/esercitazioni:
  - [Microsoft-Identity-Platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) include una piccola applicazione console di .NET Core daemon che visualizza gli utenti di un tenant che esegue una query Microsoft Graph.

    ![Topologia dell'app daemon di esempio](media/scenario-daemon-app/daemon-app-sample.svg)

    Nello stesso esempio viene inoltre illustrata una variante con i certificati:

    ![Esempio di topologia di app daemon-certificati](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) è dotato di un'applicazione web MVC ASP.NET che sincronizza i dati da Microsoft Graph usando l'identità dell'applicazione anziché per conto di un utente. Questo esempio illustra anche il processo di consenso dell'amministratore.

    ![Topologia](media/scenario-daemon-app/damon-app-sample-web.svg)

---

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati alcuni collegamenti che consentono di ottenere ulteriori informazioni:

# <a name="python"></a>[Python](#tab/python)

Provare la Guida introduttiva [acquisire un token e chiamare Microsoft Graph API da un'app console Python usando l'identità dell'app](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Provare la Guida introduttiva [acquisire un token e chiamare Microsoft Graph API da un'app console Java usando l'identità dell'app](./quickstart-v2-java-daemon.md).

---
