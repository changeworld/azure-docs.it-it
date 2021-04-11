---
title: "Esercitazione: Creare un'app Web sicura nel servizio app di Azure | Azure"
description: Questa esercitazione illustra come creare un'app Web usando il servizio app di Azure, abilitare l'autenticazione, chiamare Archiviazione di Azure e chiamare Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 5b94000ce59b3a115e53ecdcd0849b97aae552c5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221841"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Esercitazione: Abilitare l'autenticazione nel servizio app e accedere all'archiviazione e a Microsoft Graph

Questa esercitazione descrive uno scenario di applicazione comune con le procedure per:

- [Configurare l'autenticazione per un'app Web](scenario-secure-app-authentication-app-service.md) e limitare l'accesso agli utenti dell'organizzazione. Vedere A nel diagramma.
- [Accedere in modo sicuro ad Archiviazione di Azure](scenario-secure-app-access-storage.md) per l'applicazione Web usando le identità gestite. Vedere B nel diagramma.
- Accedere ai dati in Microsoft Graph [per l'utente connesso](scenario-secure-app-access-microsoft-graph-as-user.md) o [per l'applicazione Web](scenario-secure-app-access-microsoft-graph-as-app.md) usando le identità gestite. Vedere C nel diagramma.
- [Pulire le risorse](scenario-secure-app-clean-up-resources.md) create per questa esercitazione.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Diagramma che mostra gli scenari di applicazioni in Microsoft Identity Platform." border="false":::

Per iniziare, vedere come abilitare l'autenticazione per un'app Web.

> [!div class="nextstepaction"]
> [Configurare l'autenticazione per un'app Web](scenario-secure-app-authentication-app-service.md)
