---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: c97067c66296e8980a36b21298a7b2061e0f9b4c
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550389"
---
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