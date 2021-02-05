---
title: Installare gli ancoraggi spaziali di Azure per Unity
description: Configurare un progetto Unity per l'uso di ancoraggi spaziali di Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56532c17c91e6c703a6acd7990bbae47cd248165
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576566"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurazione di ancoraggi spaziali di Azure in un progetto Unity

Questa guida illustra come iniziare a usare Azure Spatial Anchors SDK nel progetto Unity.

## <a name="project-requirements"></a>Requisiti del progetto

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Configurazione di un progetto

Prima di includere Azure Spatial Anchors SDK nel progetto Unity, assicurarsi di installare i pacchetti [necessari](#project-requirements) tramite Gestione pacchetti Unity.

### <a name="download-asa-packages"></a>Scarica i pacchetti ASA
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Importa pacchetti ASA
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Solo Android: configurare il file mainTemplate. Gradle

1. Andare a **Modifica** > **Impostazioni progetto** > **Lettore**.
2. Nel **pannello Inspector** per **le impostazioni del lettore** selezionare l'icona **Android** .
3. Nella sezione **compilazione** selezionare la casella di controllo **modello principale personalizzato Gradle** per generare un modello Gradle personalizzato in `Assets\Plugins\Android\mainTemplate.gradle` .
4. Apri il file `mainTemplate.gradle` in un editor di testo.
5. Nella `dependencies` sezione incollare le dipendenze seguenti:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Al termine, la `dependencies` sezione dovrebbe avere un aspetto simile al seguente:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: creare e individuare ancoraggi in Unity](./create-locate-anchors-unity.md)
