---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044667"
---
### <a name="build-and-deploy-the-app"></a>Compilare e distribuire l'app

Aprire il `.sln` file generato da Unity. Modificare la configurazione della build come segue.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="configurazione della build":::

Successivamente, è necessario configurare l' **indirizzo IP del computer remoto** per distribuire ed eseguire il debug dell'app.

Fare clic con il pulsante destro del mouse sul progetto dell'app e selezionare **Proprietà**. Nella pagina Proprietà selezionare proprietà di **configurazione-> debug**. Modificare il valore del **nome del computer** nell'indirizzo IP del dispositivo HoloLens e fare clic su **applica**.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="debug remoto":::

Chiudere la pagina delle proprietà. Fare clic su **computer remoto**. L'app dovrebbe iniziare a compilare e distribuire nel dispositivo remoto. Verificare che il dispositivo sia attivo.
