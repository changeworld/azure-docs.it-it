---
title: Panoramica del programma di installazione di Azure Percept Dev Tools Pack
description: Altre informazioni sull'uso del programma di installazione di Dev Tools Pack per accelerare lo sviluppo avanzato con Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: f81f7922431f85cfc2a98261a128ba66d23a984f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608596"
---
# <a name="dev-tools-pack-installer-overview"></a>Panoramica del programma di installazione di Dev Tools Pack

Il programma di installazione di Dev Tools Pack è una soluzione unica che consente di installare e configurare tutti gli strumenti necessari per lo sviluppo di una soluzione Edge intelligente avanzata.

## <a name="mandatory-tools"></a>Strumenti obbligatori

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 o versione successiva](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [SDK di Azure Machine Learning 1,1](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>Strumenti facoltativi

* [NVIDIA DEEPSTREAM SDK 5](https://developer.nvidia.com/deepstream-sdk) (Toolkit per lo sviluppo di soluzioni per gli acceleratori NVIDIA)
* [Intel OpenVino toolkit 2020,2](https://docs.openvinotoolkit.org/) (Toolkit per lo sviluppo di soluzioni per gli acceleratori Intel)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) o 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 5,2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [10.0.130 Toolkit CUDA](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Problemi noti

- L'installazione facoltativa di caffe potrebbe non riuscire se Docker non è in esecuzione correttamente. Se si vuole installare caffe, assicurarsi che Docker sia installato e in esecuzione prima di provare l'installazione di caffe tramite il programma di installazione di Dev Tools Pack.

- L'installazione CUDA facoltativa non riesce nei sistemi incompatibili. Prima di provare a installare [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) tramite il programma di installazione di Dev Tools Pack, verificare la compatibilità del sistema.

## <a name="docker-minimum-requirements"></a>Requisiti minimi di Docker

### <a name="windows"></a>Windows

- Windows 10 64-bit: Pro, Enterprise o Education (Build 16299 o versione successiva).

- È necessario abilitare le funzionalità di Windows Hyper-V e contenitori. Per eseguire Hyper-V in Windows 10 sono necessari i seguenti prerequisiti hardware:

    - processore a 64 bit con [conversione di indirizzi di secondo livello](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
    - RAM di sistema da 4 GB
    - Il supporto della virtualizzazione hardware a livello di BIOS deve essere abilitato nelle impostazioni del BIOS. Per ulteriori informazioni, vedere la pagina relativa alla virtualizzazione.

> [!NOTE]
> Docker supporta Docker desktop in Windows in base al ciclo di vita del supporto di Microsoft per il sistema operativo Windows 10. Per ulteriori informazioni, vedere la pagina dei [fatti del ciclo](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)di vita di Windows.

Altre informazioni sull' [installazione di Docker desktop in Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows).

### <a name="mac"></a>Mac

- Mac deve essere un modello 2010 o un modello più recente con gli attributi seguenti:
    - Processore Intel
    - Supporto hardware di Intel per la virtualizzazione di unità di gestione della memoria (MMU), incluse le tabelle di pagine estese (EPT) e la modalità senza restrizioni. È possibile verificare se il computer è dotato di questo supporto eseguendo il comando seguente in un terminale: ```sysctl kern.hv_support``` . Se il Mac supporta il Framework hypervisor, il comando stampa ```kern.hv_support: 1``` .

- macOS versione 10,14 o successiva (Mojave, Catalina o Big Sur). Si consiglia di eseguire l'aggiornamento alla versione più recente di macOS. Se si verificano problemi dopo l'aggiornamento di macOS alla versione 10,15, è necessario installare la versione più recente di Docker desktop in modo che sia compatibile con questa versione di macOS.

- Almeno 4 GB di RAM.

- Non installare VirtualBox prima della versione 4.3.30. non è compatibile con Docker desktop.

- Il programma di installazione non è supportato in Apple M1.

Altre informazioni sull' [installazione di Docker desktop in Mac](https://docs.docker.com/docker-for-mac/install/#system-requirements).

## <a name="launch-the-installer"></a>Avviare il programma di installazione

Scaricare il programma di installazione di Dev Tools Pack per [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)o [Mac](https://go.microsoft.com/fwlink/?linkid=2132296). Avviare il programma di installazione in base alla piattaforma, come descritto di seguito.

### <a name="windows"></a>Windows

1. Fare clic su **dev-Tools-Pack-Installer** per aprire l'installazione guidata.

### <a name="mac"></a>Mac

1. Al termine del download, spostare il file **dev-Tools-Pack-Installer.app** nella cartella **applicazioni** .

1. Fare clic su **dev-Tools-Pack-Installer.app** per aprire l'installazione guidata.

1. Se viene visualizzata una finestra di dialogo di sicurezza per sviluppatori non identificati:

    1. Passare a **Preferenze di sistema**  ->  **sicurezza & privacy**  ->  **generale** e fare clic su **Apri comunque** accanto a **dev-Tools-Pack-Installer.app**.
    1. Fare clic sull'icona dell'elettrone.
    1. Fare clic su **Apri** nella finestra di dialogo sicurezza.

### <a name="linux"></a>Linux

1. Quando richiesto dal browser, fare clic su **Salva** per completare il download del programma di installazione.

1. Aggiungere le autorizzazioni di esecuzione al file con **estensione appimage** :

    1. Aprire un terminale Linux.

    1. Immettere quanto segue nel terminale per passare alla cartella **Downloads** :

        ```bash
        cd ~/Downloads/
        ```

    1. Creare il file eseguibile AppImage:

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. Eseguire il programma di installazione:

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. Aggiungere le autorizzazioni di esecuzione al file con **estensione appimage** :

    1. Fare clic con il pulsante destro del mouse sul file con estensione appimage e scegliere **Proprietà**.
    1. Aprire la scheda **autorizzazioni** .
    1. Selezionare la casella accanto a **Consenti esecuzione file come programma**.
    1. Chiudere le **Proprietà** e aprire il file con **estensione appimage** .

## <a name="run-the-installer"></a>Esegui il programma di installazione

1. Nella pagina **install dev Tools Pack Installer** fare clic su **View License** per visualizzare i contratti di licenza di ogni pacchetto software incluso nel programma di installazione. Se si accettano i termini nei contratti di licenza, selezionare la casella e fare clic su **Avanti**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Schermata del contratto di licenza nel programma di installazione.":::

1. Fare clic su **informativa sulla privacy** per esaminare l'informativa sulla privacy Microsoft. Se si accettano i termini dell'informativa sulla privacy e si desidera inviare i dati di diagnostica a Microsoft, selezionare **Sì** e fare clic su **Avanti**. In caso contrario, selezionare **No** e fare clic su **Avanti**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Schermata del contratto informativa sulla privacy nel programma di installazione.":::

1. Nella pagina **Configura componenti** selezionare gli strumenti facoltativi che si desidera installare (gli strumenti obbligatori verranno installati per impostazione predefinita).

    1. Se si usa il servizio Azure Percept audio SoM, che fa parte di Azure Percept DK, assicurarsi di installare Intel OpenVino Toolkit e Miniconda3.

    1. Fare clic su **Installa** per procedere con l'installazione.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Schermata del programma di installazione che mostra i pacchetti software disponibili.":::

1. Una volta completata l'installazione di tutti i componenti selezionati, la procedura guidata consente di passare alla pagina **completamento dell'installazione guidata** . Fare clic su **fine** per uscire dal programma di installazione.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Schermata di completamento del programma di installazione.":::

## <a name="docker-status-check"></a>Controllo dello stato di Docker

Se il programma di installazione invia una notifica per verificare che Docker desktop sia in uno stato di esecuzione corretto, vedere i passaggi seguenti:

### <a name="windows"></a>Windows

1. Espandere icone nascoste della barra delle applicazioni.

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Vassoio di sistema.":::

1. Verificare che l'icona del desktop Docker mostri **che Docker desktop sia in esecuzione**.

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Stato di Docker.":::

1. Se l'icona sopra indicata non è visualizzata nella barra delle applicazioni, avviare Docker desktop dal menu Start.

1. Se Docker richiede il riavvio, è possibile chiudere il programma di installazione e riavviarlo dopo il completamento di un riavvio e Docker si trova in uno stato di esecuzione. Tutte le applicazioni di terze parti installate correttamente dovrebbero essere rilevate e non verranno reinstallate automaticamente.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare lo sviluppo avanzato per Azure Percept DK, vedere il [repository di sviluppo avanzato di Azure Percept](https://github.com/microsoft/azure-percept-advanced-development) .
