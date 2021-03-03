---
title: "Guida introduttiva: Creare un'app HoloLens con Unity"
description: Questa Guida introduttiva illustra come creare un'app HoloLens Unity usando gli ancoraggi degli oggetti.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: caf64883635d7fa1746d12caf24333a22ba2fa98
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748586"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>Guida introduttiva: creare un'app HoloLens con gli ancoraggi degli oggetti di Azure in Unity

In questa Guida introduttiva verrà creata un'app HoloLens Unity che usa gli [ancoraggi degli oggetti di Azure](../overview.md). L'ancoraggio di oggetti di Azure è un servizio cloud gestito che converte asset 3D in modelli di intelligenza artificiale che consentono esperienze di realtà miste compatibili con gli oggetti per HoloLens. Al termine, si avrà un'app HoloLens compilata con Unity in grado di rilevare oggetti nel mondo fisico.

Si apprenderà come:

> [!div class="checklist"]
> * Preparare le impostazioni di compilazione di Unity.
> * Esportare il progetto Visual Studio HoloLens.
> * Distribuire l'app ed eseguirla in un dispositivo HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Aprire il progetto di esempio

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

In Unity aprire il `quickstarts/apps/unity/basic` progetto.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build and run](../../../includes/object-anchors-quickstart-unity-build-run.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Accendere il dispositivo, selezionare **tutte le app**, quindi individuare e avviare l'app. Dopo la schermata iniziale di Unity verrà visualizzato un messaggio che indica che l'osservatore oggetti è stato inizializzato. Tuttavia, sarà necessario aggiungere il modello all'app.

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

L'app cerca gli oggetti nel campo di visualizzazione corrente e li rileva una volta rilevati. Un'istanza verrà rimossa quando risale a 6 metri dalla posizione dell'utente. Il testo di debug Mostra i dettagli relativi a un'istanza, ad esempio ID, timestamp aggiornato e rapporto di copertura della superficie.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Unity HoloLens con MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Concetti: Panoramica dell'SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Domande frequenti](../faq.md)
