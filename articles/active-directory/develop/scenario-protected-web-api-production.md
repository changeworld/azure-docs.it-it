---
title: Spostare un'API Web protetta in produzione | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web protetta (passare alla produzione).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 98be47200c12ab3097f9416b5647abf6465a89e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675975"
---
# <a name="protected-web-api---move-to-production"></a>API Web protetta: passa all'ambiente di produzione

Ora che si è appreso come proteggere l'API Web, di seguito sono riportati alcuni aspetti da considerare quando si trasferisce l'applicazione in produzione.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come chiamare un'API downstream nello [scenario: un'API Web che chiama le API Web](scenario-web-api-call-api-overview.md).


Per altre informazioni, vedere Esercitazioni ed esempi su GitHub:

- [Chiamata di un'API protetta tramite un daemon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi)
- [Esercitazione sull'API Web di ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
- [Esempio di API Web ASP.NET](https://github.com/azureadquickstarts/appmodelv2-nativeclient-dotnet)
