---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d06a6ecd8af16da3e6df21e984fbf6a727fbc27e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104227"
---
### <a name="upload-your-model"></a>Caricare il modello

Se non si dispone già di un modello di ancoraggio degli oggetti, seguire le istruzioni riportate in [creare un modello](../articles/object-anchors/quickstarts/get-started-model-conversion.md) per crearne uno. Quindi, tornare qui.

Con la HoloLens connessa al portale per dispositivi Windows, seguire questa procedura per caricare un modello per l'app da usare:

1. Nel portale per i dispositivi Windows passare a **System > Esplora File > LocalAppData**. Nell'elenco delle app dovrebbe essere visualizzata l'applicazione.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="Esplora file":::

2. Aprire l'applicazione e fare clic sulla `LocalState` cartella.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Aprire la cartella LocalState":::

3. Caricare il file del modello nella `LocalState` cartella.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="caricare il modello nel portale":::

    Avviare nuovamente l'applicazione dal HoloLens. È ora possibile rilevare oggetti corrispondenti al modello.