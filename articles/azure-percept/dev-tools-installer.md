---
title: Panoramica del programma di installazione di Azure Percept Dev Tools Pack
description: Altre informazioni sull'uso del programma di installazione di Dev Tools Pack per accelerare lo sviluppo avanzato con Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 66498fabadc0784a4a4ab1c3762daaaa9a5738c4
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503216"
---
# <a name="dev-tools-pack-installer-overview"></a>Panoramica del programma di installazione di Dev Tools Pack

Il programma di installazione di Dev Tools Pack è una soluzione unica che consente di installare e configurare tutti gli strumenti necessari per sviluppare una soluzione Edge intelligente. Se è già stato installato uno dei pacchetti software elencati di seguito, il programma di installazione di Dev Tools Pack reinstalla i pacchetti in modo che gli strumenti siano coerenti con le versioni del software del programma di installazione.

## <a name="mandatory-tools-installed"></a>Strumenti obbligatori installati

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 (Windows) o 3,5 (Linux)](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [SDK di Azure Machine Learning 1,1](https://docs.microsoft.com/python/api/overview/azure/ml/)

## <a name="optional-tools-available-for-installation"></a>Strumenti facoltativi disponibili per l'installazione

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

- L'installazione facoltativa di caffe potrebbe non riuscire se Docker non viene eseguito correttamente nel sistema. Se si vuole installare caffe, assicurarsi che Docker sia installato e in esecuzione prima di provare l'installazione di caffe tramite il programma di installazione di Dev Tools Pack. 

- L'installazione CUDA facoltativa non riesce nei sistemi incompatibili. Prima di provare a installare [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) tramite il programma di installazione di Dev Tools Pack, verificare la compatibilità del sistema.

## <a name="minimum-requirements"></a>Requisiti minimi

* Requisiti minimi di Docker:

    * Windows:
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64-bit: Pro, Enterprise o Education (Build 16299 o versione successiva).

             Per Windows 10 Home, vedere Install Docker desktop in Windows Home.
           - È necessario abilitare le funzionalità di Windows Hyper-V e contenitori.
           - Per eseguire correttamente Client Hyper-V in Windows 10 sono necessari i seguenti prerequisiti hardware:

              - processore a 64 bit con [conversione di indirizzi di secondo livello](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
              - RAM di sistema da 4 GB
              - Il supporto della virtualizzazione hardware a livello di BIOS deve essere abilitato nelle impostazioni del BIOS. Per ulteriori informazioni, vedere la pagina relativa alla virtualizzazione.

        > [!NOTE]
        > Docker supporta Docker desktop in Windows in base al ciclo di vita del supporto di Microsoft per il sistema operativo Windows 10. Per ulteriori informazioni, vedere la pagina dei [fatti del ciclo](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)di vita di Windows.

    * Mac:
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        Per installare correttamente Docker desktop, il Mac deve soddisfare i requisiti seguenti:
         
         - **L'hardware Mac deve essere un modello 2010 o un modello più recente con un processore Intel**, con supporto hardware di Intel per la virtualizzazione dell'unità di gestione della memoria (MMU), incluse le tabelle di pagine estese (EPT) e la modalità senza restrizioni. È possibile verificare se il computer è dotato di questo supporto eseguendo il comando seguente in un terminale: ```sysctl kern.hv_support```

        Se il Mac supporta il Framework hypervisor, il comando stampa ```kern.hv_support: 1``` .

         - **MacOS deve avere la versione 10,14 o successiva**. Ovvero Mojave, Catalina o Big Sur. Si consiglia di eseguire l'aggiornamento alla versione più recente di macOS.

        Se si verificano problemi dopo l'aggiornamento di macOS alla versione 10,15, è necessario installare la versione più recente di Docker desktop in modo che sia compatibile con questa versione di macOS.

        - Almeno 4 GB di RAM.
        - VirtualBox prima della versione 4.3.30 non deve essere installato perché non è compatibile con Docker desktop.

        > [!NOTE]
        > Docker supporta Docker desktop nelle versioni più recenti di macOS. Ovvero la versione corrente di macOS e le due versioni precedenti. Poiché le nuove versioni principali di macOS vengono rese disponibili a livello generale, Docker smette di supportare la versione meno recente e supporta la versione più recente di macOS (oltre alle due versioni precedenti). Docker desktop supporta attualmente macOS Mojave, macOS Catalina e macOS Big Sur.
        > 
        - Il programma di installazione non è supportato in Apple M1.

## <a name="instructions"></a>Istruzioni

1. Scaricare il programma di installazione di Dev Tools Pack per [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)e [Mac](https://go.microsoft.com/fwlink/?linkid=2132296).

1. A seconda della piattaforma, si verificano alcune differenze nell'avvio del programma di installazione.

    1. Per Windows:
    
        1. Fare clic su **dev-Tools-Pack-Installer** per aprire l'installazione guidata.
        
    1. Per Mac:
    
        1. Dopo il download, spostare il file Dev-Tools-Pack-Installer. app nella cartella applicazioni.
        
        1. Fare clic su **dev-Tools-Pack-Installer. app** per aprire l'installazione guidata.
        
        1. Se viene visualizzata una finestra di dialogo di sicurezza per sviluppatori non identificati:
        
            1. Passare a preferenze di sistema-> sicurezza & privacy-> generale e fare clic sul pulsante "Apri comunque" accanto a "Dev-Tools-Pack-Installer. app".
        
            1. Fare di nuovo clic sull'icona dell'elettrone sul Dock
        
            1. Fare clic sul pulsante "Apri" nella finestra di dialogo sicurezza.
    
    1. Per Linux:
    
        1. Quando richiesto dal browser, fare clic su "Salva" per completare il download del programma di installazione
        
        1. Aggiungere le autorizzazioni di esecuzione al metodo **appimage** 1 (riga di comando):
            
            1. Aprire il terminale Linux
            
            1. Digitare quanto segue nel terminale per passare alla cartella Downloads
            
                1. CD ~/downloads/
                
            1. Digitare quanto segue nel terminale per creare l'eseguibile AppImage
            
                1. chmod + x **dev-Tools-Pack-Installer. AppImage**
                
            1. Digitare quanto segue nel terminale per eseguire il programma di installazione
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. Aggiungere le autorizzazioni di esecuzione al metodo del file **. appimage** 2 (UI):
        
            1. Fare clic con il pulsante destro del mouse sul file con estensione appimage e scegliere Proprietà.
            
            1. Apri scheda autorizzazioni
            
            1. Casella di controllo ' Consenti esecuzione file come programma '
            
            1. Chiudere le proprietà e aprire il file con estensione appimage

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

   1. Windows:
   
      1. Espandere le icone nascoste della barra di sistema:
      
         1. Espandere le icone nascoste della barra di sistema se nascoste:

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Vassoio di sistema.":::
         
         1. Verificare che l'icona del desktop Docker indichi ' Docker Desktop is running ':

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Stato di Docker.":::
         
         1. Se l'icona sopra indicata non è visualizzata nella barra delle applicazioni, avviare Docker desktop dal menu Start.
         
         1. Se Docker richiede il riavvio, è possibile chiudere il programma di installazione e riavviarlo dopo il completamento di un riavvio e Docker si trova in uno stato di esecuzione. Tutte le applicazioni di terze parti installate correttamente dovrebbero essere rilevate e non verranno reinstallate automaticamente.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare lo sviluppo avanzato per Azure Percept DK, vedere il [repository di sviluppo avanzato di Azure Percept](https://github.com/microsoft/azure-percept-advanced-development) .