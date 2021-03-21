---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d8dfc3d4b7a8447250481b98c1adadc865a29da1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102532645"
---
### <a name="upload-your-model"></a>Caricare il modello

Se non si dispone già di un modello di ancoraggio degli oggetti, seguire le istruzioni riportate in [creare un modello](/azure/object-anchors/quickstarts/get-started-model-conversion) per crearne uno. Quindi, tornare qui.

Con la HoloLens connessa al portale per dispositivi Windows, seguire questa procedura per caricare un modello per l'app da usare:

1. Nel portale per i dispositivi Windows passare a **System > Esplora File > LocalAppData**. Nell'elenco delle app dovrebbe essere visualizzata l'applicazione.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="Esplora file":::

2. Aprire l'applicazione e fare clic sulla `LocalState` cartella.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Aprire la cartella LocalState":::

3. Caricare il file del modello nella `LocalState` cartella.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="caricare il modello nel portale":::

    Avviare nuovamente l'applicazione dal HoloLens. È ora possibile rilevare oggetti corrispondenti al modello.
