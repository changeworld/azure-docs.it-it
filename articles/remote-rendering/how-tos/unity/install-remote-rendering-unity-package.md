---
title: Installare il pacchetto Rendering remoto per Unity
description: Spiega come installare le dll client per il rendering remoto per Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: bfb383a7079e98db1db1f9b5077558c187bcea96
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047729"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Installare il pacchetto Rendering remoto per Unity

Il rendering remoto di Azure usa un pacchetto Unity per incapsulare l'integrazione in Unity.
Questo pacchetto contiene l'intera API C# e tutti i file binari dei plug-in necessari per usare il rendering remoto di Azure con Unity.
In seguito allo schema di denominazione di Unity per i pacchetti, il pacchetto è denominato **com. Microsoft. Azure. Remote-rendering**.

È possibile scegliere una delle opzioni seguenti per installare il pacchetto Unity.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Installare il pacchetto di rendering remoto usando lo strumento per la funzionalità di realtà mista

[Lo strumento per la funzionalità di realtà mista](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) ([download](https://aka.ms/mrfeaturetool)) è uno strumento usato per integrare i pacchetti di funzionalità di realtà mista in progetti Unity. Il pacchetto non fa parte del [repository di esempi arr](https://github.com/Azure/azure-remote-rendering)e non è disponibile nel registro di sistema dei pacchetti interni di Unity.

Per aggiungere il pacchetto a un progetto, è necessario:
1. [Scaricare lo strumento per la funzionalità di realtà mista](https://aka.ms/mrfeaturetool)
1. Seguire le [istruzioni complete](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool) su come usare lo strumento.
1. Nella pagina **individua funzionalità** selezionare la casella per il Microsoft Azure pacchetto di **rendering remoto** e selezionare la versione del pacchetto che si desidera aggiungere al progetto

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

Per aggiornare il pacchetto locale è sufficiente selezionare una versione più recente dallo strumento funzionalità per la realtà mista e installarla. L'aggiornamento del pacchetto può talvolta causare errori nella console. In questo caso, provare a chiudere e riaprire il progetto.

## <a name="install-remote-rendering-package-manually"></a>Installare manualmente il pacchetto di rendering remoto

Per installare manualmente il pacchetto di rendering remoto, è necessario:

1. Scaricare il pacchetto dal feed NPM dei pacchetti di realtà mista all'indirizzo `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` .
    * È possibile usare [NPM](https://www.npmjs.com/get-npm) ed eseguire il comando seguente per scaricare il pacchetto nella cartella corrente.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * In alternativa, è possibile usare lo script di PowerShell in `Scripts/DownloadUnityPackages.ps1` dal [repository GitHub Azure-Remote-rendering](https://github.com/Azure/azure-remote-rendering).
        * Modificare il contenuto di `Scripts/unity_sample_dependencies.json` in
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Eseguire il comando seguente in PowerShell per scaricare il pacchetto nella directory di destinazione specificata.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [Installare il pacchetto scaricato](https://docs.unity3d.com/Manual/upm-ui-tarball.html) con gestione pacchetti di Unity.

Per aggiornare il pacchetto locale, è sufficiente eseguire nuovamente il rispettivo comando utilizzato e reimportare il pacchetto. L'aggiornamento del pacchetto può talvolta causare errori nella console. In questo caso, provare a chiudere e riaprire il progetto.

## <a name="unity-render-pipelines"></a>Pipeline di rendering Unity

Il rendering remoto funziona sia con sia con **:::no-loc text="Universal render pipeline":::** **:::no-loc text="Standard render pipeline":::** . Per motivi di prestazioni, è consigliabile usare la pipeline di rendering universale.

Per usare **:::no-loc text="Universal render pipeline":::** , il pacchetto deve essere installato in Unity. Questa operazione può essere eseguita nell'interfaccia utente di **Gestione pacchetti** di Unity (nome pacchetto **universale RP**, versione 7.3.1 o successiva) o tramite il `Packages/manifest.json` file, come descritto nell' [esercitazione sull'installazione del progetto Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Passaggi successivi

* [Oggetti e componenti di gioco Unity](objects-components.md)
* [Esercitazione: visualizzare i modelli remoti](../../tutorials/unity/view-remote-models/view-remote-models.md)