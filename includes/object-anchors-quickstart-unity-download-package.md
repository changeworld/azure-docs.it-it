---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: ada83d6263ef033208200d810c53c5f045acc9a7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105752"
---
Il passaggio successivo consiste nel scaricare il pacchetto di Azure Object Anchors per Unity.

# <a name="download-with-web-browser"></a>[Scaricare con il Web browser](#tab/unity-package-web-ui)

Individuare il pacchetto di ancoraggio di oggetti di Azure per Unity [qui](https://aka.ms/aoa/unity-sdk/package). Selezionare la versione desiderata e scaricare il pacchetto usando il pulsante **download** .

# <a name="download-with-npm"></a>[Scaricare con NPM](#tab/unity-package-npm)

Per questo passaggio è necessario che <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> sia installato e disponibile.

Eseguire il comando seguente sostituendo `<version_number>` con la versione degli ancoraggi degli oggetti di Azure che si vuole scaricare:

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Per elencare le versioni disponibili del pacchetto degli ancoraggi degli oggetti di Azure, eseguire il comando seguente:
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Il pacchetto degli ancoraggi degli oggetti di Azure verrà scaricato nella cartella in cui è stato eseguito il comando.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Eseguire l'installazione con lo strumento di funzionalità di realtà mista (beta)](#tab/unity-package-mixed-reality-feature-tool)

Continuare con il passaggio successivo. Si userà lo <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">strumento della funzionalità di realtà mista</a> in un passaggio successivo.

---