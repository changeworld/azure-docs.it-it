---
title: Passare alla produzione di un'app Web che chiama le API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come passare alla produzione di un'app Web che chiama le API Web.
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
ms.openlocfilehash: cf32274a49cb1b790e9d872efe36f2e1cb188d1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675941"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>Un'app Web che chiama le API Web: passa all'ambiente di produzione

Ora che si è appreso come acquisire un token per chiamare le API Web, di seguito sono riportati alcuni aspetti da considerare quando si trasferisce l'applicazione in produzione.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, provare l'esercitazione completa e progressiva per ASP.NET Core app Web. L'esercitazione:

- Mostra come consentire agli utenti di accedere a più destinatari o a cloud nazionali oppure usando identità di social networking.
- Chiama Microsoft Graph.
- Chiama diverse API Microsoft.
- Gestisce il consenso incrementale.
- Chiama un'API Web personalizzata.

[Esercitazione sulle app Web ASP.NET Core](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
