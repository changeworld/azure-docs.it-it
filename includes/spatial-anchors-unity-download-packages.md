---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076702"
---
Il passaggio successivo consiste nel scaricare i pacchetti di ancoraggi spaziali di Azure per Unity. 

> [!WARNING]
> ASA SDK 2.7.0 è la versione minima supportata. Se si usa Unity 2020, ASA SDK 2.9.0 è la versione minima supportata.

Per usare gli ancoraggi spaziali di Azure in Unity, è necessario scaricare il pacchetto principale, oltre a un pacchetto specifico della piattaforma per ogni piattaforma (Android/iOS/HoloLens) che si intende supportare.

| Piattaforma | Nome del pacchetto                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com. Microsoft. Azure. Spatial-Anchors-sdk.core@ <version_number> |
| Android  | com. Microsoft. Azure. Spatial-Anchors-sdk.android@ <version_number> |
| iOS      | com. Microsoft. Azure. Spatial-Anchors-sdk.ios@ <version_number>     |
| HoloLens | com. Microsoft. Azure. Spatial-Anchors-sdk.windows@ <version_number> |

# <a name="download-with-web-browser"></a>[Scaricare con il Web browser](#tab/unity-package-web-ui)

Per [Unity](https://aka.ms/aoa/unity-sdk/package), vedere il pacchetto Azure Spatial Anchors core per Unity. Selezionare la versione desiderata e scaricare il pacchetto usando il pulsante **download** . Ripetere questo passaggio per scaricare il pacchetto per ogni piattaforma che si prevede di supportare.

# <a name="download-with-npm"></a>[Scaricare con NPM](#tab/unity-package-npm)

Per questo passaggio è necessario che <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> sia installato e disponibile.

Eseguire il comando seguente sostituendo `<version_number>` con la versione degli ancoraggi spaziali di Azure che si vuole scaricare nella cartella corrente:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Per elencare le versioni disponibili del pacchetto di ancoraggi spaziali di Azure, eseguire il comando seguente:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Il pacchetto Azure Spatial Anchors Core verrà scaricato nella cartella in cui è stato eseguito il comando. Ripetere questo passaggio per scaricare il pacchetto per ogni piattaforma che si prevede di supportare.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Eseguire l'installazione con lo strumento di funzionalità di realtà mista (beta)](#tab/unity-package-mixed-reality-feature-tool)

Continuare con il passaggio successivo. Si userà lo <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">strumento della funzionalità di realtà mista</a> in un passaggio successivo.

---