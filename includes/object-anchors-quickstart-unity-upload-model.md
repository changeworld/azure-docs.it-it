---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: 8b7ab183dfb7b6721beae7835acd75bc7a05e72c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044776"
---
### <a name="upload-your-model"></a>Caricare il modello

Se non si dispone già di un modello di ancoraggio degli oggetti, seguire le istruzioni riportate in [creare un modello](/object-anchors/quickstarts/get-started-model-conversion.md) per crearne uno. Quindi, tornare qui.

Con la HoloLens connessa al portale per dispositivi Windows, seguire questa procedura per caricare un modello per l'app da usare:

1. Nel portale per i dispositivi Windows passare a **System > Esplora File > LocalAppData**. Nell'elenco delle app dovrebbe essere visualizzata l'applicazione.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="Esplora file":::

2. Aprire l'applicazione e fare clic sulla `LocalState` cartella.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Aprire la cartella LocalState":::

3. Caricare il file del modello nella `LocalState` cartella.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="caricare il modello nel portale":::

    Avviare nuovamente l'applicazione dal HoloLens. È ora possibile rilevare oggetti corrispondenti al modello.
