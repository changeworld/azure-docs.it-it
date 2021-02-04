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
ms.openlocfilehash: e058186d8848256bf97d99ee1b8b1ddae7d78383
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550624"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configurazione di ancoraggi spaziali di Azure in un progetto Unity

Questa guida illustra come iniziare a usare Azure Spatial Anchors SDK nel progetto Unity.

## <a name="requirements"></a>Requisiti

Gli ancoraggi spaziali di Azure attualmente supportano Unity 2019,4 (LTS) con le configurazioni seguenti.

* Unity 2019,4 con AR Foundation 3,1 è supportato negli ancoraggi spaziali di Azure 2.4.0 +.

## <a name="configuring-a-project"></a>Configurazione di un progetto

### <a name="download-packages"></a>Download dei pacchetti
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>Importare pacchetti
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
