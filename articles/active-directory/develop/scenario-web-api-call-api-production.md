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
ms.openlocfilehash: 370bedf04dc61e2a637f735580cd4df14061264a
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753331"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Un'API Web che chiama le API Web: passa all'ambiente di produzione

Dopo aver acquisito un token per chiamare le API Web, è possibile spostare l'app nell'ambiente di produzione.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>Altre informazioni

Ora che si conoscono le nozioni di base su come chiamare le API Web dalla propria API Web, si potrebbe essere interessati all'esercitazione seguente, in cui viene descritto il codice usato per creare un'API Web protetta che chiama le API Web.

| Esempio | Piattaforma | Descrizione |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-WebAPI-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) capitolo 1 | API Web di ASP.NET Core, desktop (WPF) | ASP.NET Core le chiamate API Web Microsoft Graph, che viene chiamato da un'applicazione WPF usando la piattaforma di identità Microsoft. |
