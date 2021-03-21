---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719916"
---
Per scaricare i pacchetti necessari, è necessario che <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> sia installato.

Eseguire il comando seguente sostituendo `<version_number>` con la versione degli ancoraggi spaziali di Azure che si vuole scaricare nella cartella corrente:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Per elencare le versioni disponibili del pacchetto di ancoraggi spaziali di Azure, eseguire il comando seguente:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

> [!WARNING]
> ASA SDK 2.7.0 è la versione minima supportata. Se si usa Unity 2020, ASA SDK 2.9.0 è la versione minima supportata.

Il pacchetto Azure Spatial Anchors Core verrà scaricato nella cartella in cui è stato eseguito il comando.

Ripetere questo passaggio per scaricare il pacchetto per ogni piattaforma (Android/iOS/HoloLens) che si desidera supportare nel progetto.

| Piattaforma | Nome del pacchetto                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. Spatial-Anchors-sdk.android@ <version_number> |
| iOS      | com. Microsoft. Azure. Spatial-Anchors-sdk.ios@ <version_number>     |
| HoloLens | com. Microsoft. Azure. Spatial-Anchors-sdk.windows@ <version_number> |