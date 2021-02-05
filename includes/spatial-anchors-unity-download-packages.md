---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569570"
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

Il pacchetto Azure Spatial Anchors Core verrà scaricato nella cartella in cui è stato eseguito il comando.

Ripetere questo passaggio per scaricare il pacchetto per ogni piattaforma (Android/iOS/HoloLens) che si desidera supportare nel progetto.

| Piattaforma | Nome del pacchetto                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. Spatial-Anchors-sdk.android@ <version_number> |
| iOS      | com. Microsoft. Azure. Spatial-Anchors-sdk.ios@ <version_number>     |
| HoloLens | com. Microsoft. Azure. Spatial-Anchors-sdk.windows@ <version_number> |