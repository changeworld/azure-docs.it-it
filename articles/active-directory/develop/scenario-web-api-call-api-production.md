---
title: Spostare l'API Web che chiama le API Web nell'ambiente di produzione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come spostare un'API Web che chiama API Web nell'ambiente di produzione.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675873"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Un'API Web che chiama le API Web: passa all'ambiente di produzione

Dopo aver acquisito un token per chiamare le API Web, di seguito sono riportati alcuni aspetti da considerare quando si trasferisce l'applicazione in produzione.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono le nozioni di base su come chiamare le API Web dalla propria API Web, si potrebbe essere interessati all'esercitazione seguente, in cui viene descritto il codice usato per creare un'API Web protetta che chiama le API Web.

| Esempio | Piattaforma | Descrizione |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebAPI-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) capitolo 1 | API Web di ASP.NET Core, desktop (WPF) | ASP.NET Core le chiamate API Web Microsoft Graph, che viene chiamato da un'applicazione WPF usando la piattaforma di identità Microsoft. |
