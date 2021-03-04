---
title: Creare un'API Web che chiama API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web che chiama le API Web downstream (panoramica).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 376c61f6a5ba94492cac26950465c61e3d8fe4ed
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038561"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenario: un'API Web che chiama le API Web

Informazioni su ciò che è necessario sapere per creare un'API Web che chiama le API Web.

## <a name="prerequisites"></a>Prerequisiti

Questo scenario, in cui un'API Web protetta chiama altre API Web, si basa sullo [scenario: API Web protetta](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Panoramica

- Un client di applicazioni Web, desktop, per dispositivi mobili o a singola pagina (non rappresentato nel diagramma associato) chiama un'API Web protetta e fornisce un token JSON Web (JWT) bearer token nell'intestazione HTTP "Authorization".
- L'API Web protetta convalida il token e usa il metodo Microsoft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` per richiedere un altro token da Azure Active Directory (Azure ad), in modo che l'API Web protetta possa chiamare una seconda API Web o un'API Web downstream per conto dell'utente. `AcquireTokenOnBehalfOf` Aggiorna il token quando necessario.
![Diagramma di un'API Web che chiama un'API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifiche

La parte relativa alla registrazione dell'app correlata alle autorizzazioni API è classica. La configurazione dell'app prevede l'uso del flusso OAuth 2,0 per lo scambio di JWT bearer token rispetto a un token per un'API downstream. Questo token viene aggiunto alla cache dei token, dove è disponibile nei controller dell'API Web e può quindi acquisire un token in modo invisibile all'utente per chiamare le API downstream.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, registrazione dell' [app](scenario-web-api-call-api-app-registration.md).
