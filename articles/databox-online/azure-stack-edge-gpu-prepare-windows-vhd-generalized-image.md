---
title: Creare immagini di VM dall'immagine generalizzata del disco rigido virtuale di Windows per il dispositivo GPU Pro Azure Stack Edge
description: Viene descritto come VM immagini da immagini generalizzate a partire da un disco rigido virtuale Windows o da un VHDX. Usare questa immagine generalizzata per creare immagini di VM da usare con le macchine virtuali distribuite nel dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962614"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Usare un'immagine generalizzata del disco rigido virtuale di Windows per creare un'immagine di macchina virtuale per il dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Per distribuire macchine virtuali nel dispositivo Azure Stack Edge Pro, è necessario poter creare immagini personalizzate utilizzabili per creare macchine virtuali. Questo articolo descrive i passaggi necessari per preparare un disco rigido virtuale Windows o VHDX per creare un'immagine generalizzata. Questa immagine generalizzata viene quindi usata per creare un'immagine di macchina virtuale per il dispositivo Azure Stack Edge Pro. 

## <a name="about-preparing-windows-vhd"></a>Informazioni sulla preparazione del disco rigido virtuale Windows

Per creare un'immagine *generalizzata* o un'immagine *specializzata* , è possibile usare un disco rigido virtuale o VHDX di Windows. Nella tabella seguente sono riepilogate le differenze principali tra le immagini *generalizzate* e *specializzate* .


|Tipo di immagine  |Generalizzata  |Specializzata  |
|---------|---------|---------|
|Destinazione     |Distribuito in qualsiasi sistema         | Destinato a un sistema specifico        |
|Installazione dopo l'avvio     | Il programma di installazione è necessario al primo avvio della macchina virtuale.          | Il programma di installazione non è necessario. <br> La piattaforma attiva la macchina virtuale.        |
|Configurazione     |Sono necessari il nome host, l'utente amministratore e altre impostazioni specifiche per le macchine virtuali.         |Completamente preconfigurato.         |
|Utilizzato quando     |Creazione di più macchine virtuali nuove dalla stessa immagine.         |Migrazione di un computer specifico o ripristino di una macchina virtuale dal backup precedente.         |


Questo articolo illustra i passaggi necessari per eseguire la distribuzione da un'immagine generalizzata. Per eseguire la distribuzione da un'immagine specializzata, vedere [usare un disco rigido virtuale Windows specializzato](azure-stack-edge-placeholder.md) per il dispositivo.

> [!IMPORTANT]
> Questa procedura non copre i casi in cui il disco rigido virtuale di origine è configurato con configurazioni e impostazioni personalizzate. Ad esempio, potrebbe essere necessario eseguire azioni aggiuntive per generalizzare un disco rigido virtuale contenente regole del firewall o impostazioni proxy personalizzate. Per altre informazioni su queste azioni aggiuntive, vedere [preparare un disco rigido virtuale Windows da caricare in Azure-macchine virtuali di Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).


## <a name="vm-image-workflow"></a>Flusso di lavoro delle immagini di VM

Il flusso di lavoro di alto livello per preparare un disco rigido virtuale Windows da utilizzare come immagine generalizzata prevede i passaggi seguenti:

1. Convertire il disco rigido virtuale di origine o VHDX in un disco rigido virtuale a dimensione fissa.
1. Creare una macchina virtuale in Hyper-V usando il disco rigido virtuale fisso.
1. Connettersi alla macchina virtuale Hyper-V.
1. Generalizzare il disco rigido virtuale utilizzando l'utilità *Sysprep* . 
1. Copiare l'immagine generalizzata nell'archivio BLOB.
1. Usare un'immagine generalizzata per distribuire le macchine virtuali nel dispositivo. Per altre informazioni, vedere come [distribuire una macchina virtuale tramite portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) o [distribuire una VM tramite PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="prerequisites"></a>Prerequisiti

Prima di preparare un disco rigido virtuale Windows da utilizzare come immagine generalizzata in Azure Stack Edge, verificare che:

- Si dispone di un disco rigido virtuale o di un VHDX contenente una versione supportata di Windows. Vedere [sistemi operativi guest supportati]() per il Azure stack Edge Pro. 
- Si ha accesso a un client Windows con la console di gestione di Hyper-V installata. 
- È possibile accedere a un account di archiviazione BLOB di Azure per archiviare il disco rigido virtuale dopo che è stato preparato.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>Preparare un'immagine di Windows generalizzata dal disco rigido virtuale

## <a name="convert-to-a-fixed-vhd"></a>Convertire in un disco rigido virtuale fisso 

Per il dispositivo sono necessari VHD a dimensione fissa per creare immagini di VM. È necessario convertire il disco rigido virtuale Windows di origine o VHDX in un disco rigido virtuale fisso. A tale scopo, seguire questa procedura:

1. Aprire la console di gestione di Hyper-V nel sistema client. Passare a **modifica disco**.

    ![Aprire la console di gestione di Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. Nella pagina **prima di iniziare** selezionare **Avanti>**.

1. Nella pagina **Individua disco rigido virtuale** passare al percorso del file VHD di Windows di origine o VHDX che si desidera convertire. Selezionare **Next>**.

    ![Pagina Individua disco rigido virtuale](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. Nella pagina **Scegli azione** selezionare **Converti** e selezionare **Avanti>**.

    ![Pagina Scegli azione](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. Nella pagina Selezione **formato disco** selezionare formato **VHD** , quindi selezionare **Avanti>**.

   ![Pagina Selezione formato disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. Nella pagina **Selezione tipo di disco** scegliere **dimensioni fisse** e selezionare **Avanti>**.

   ![Pagina Selezione tipo di disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. Nella pagina **Configura disco** individuare il percorso e specificare un nome per il disco rigido virtuale a dimensione fissa. Selezionare **Next>**.

   ![Pagina Configura disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. Rivedere il riepilogo e selezionare **Fine**. La conversione del disco rigido virtuale o del VHDX richiede alcuni minuti. Il tempo per la conversione dipende dalle dimensioni del disco di origine. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Questo VHD fisso verrà usato per tutti i passaggi successivi di questo articolo.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Creare una macchina virtuale Hyper-V da un disco rigido virtuale fisso

1. Nella console di **gestione di Hyper-V**, nel riquadro ambito, fare clic con il pulsante destro del mouse sul nodo del sistema per aprire il menu di scelta rapida, quindi scegliere **nuova**  >  **macchina virtuale**.

    ![Seleziona nuova macchina virtuale nel riquadro ambito](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Nella pagina **prima di iniziare** della procedura guidata nuova macchina virtuale selezionare **Avanti**.

1. Nella pagina **impostazione nome e percorso specificare** un **nome** e un **percorso** per la macchina virtuale. Selezionare **Avanti**.

    ![Specificare il nome e il percorso della macchina virtuale](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. Nella pagina **impostazione generazione** scegliere **generazione 1** per il tipo di immagine del dispositivo VHD e quindi fare clic su **Avanti**.    

    ![Specifica generazione](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Assegnare le configurazioni di rete e di memoria desiderate.

1. Nella pagina **Connessione disco rigido virtuale** scegliere **utilizza un disco rigido virtuale esistente**, specificare il percorso del VHD fisso di Windows creato in precedenza e quindi fare clic su **Avanti**.

    ![Pagina Connessione disco rigido virtuale](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Esaminare il **Riepilogo** e quindi fare clic su **fine** per creare la macchina virtuale.

La creazione della macchina virtuale richiede alcuni minuti.
     

## <a name="connect-to-the-hyper-v-vm"></a>Connettersi alla macchina virtuale Hyper-V

La VM viene visualizzata nell'elenco delle macchine virtuali nel sistema client. 


1. Selezionare la macchina virtuale e quindi fare clic con il pulsante destro del mouse e scegliere **Avvia**. 

    ![Selezionare la macchina virtuale e avviarla](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. La VM Mostra come **in esecuzione**. Selezionare la macchina virtuale e quindi fare clic con il pulsante destro del mouse e scegliere **Connetti**.

    ![Connettersi alla macchina virtuale](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

Dopo aver eseguito la connessione alla macchina virtuale, completare l'installazione guidata del computer e quindi accedere alla macchina virtuale.


## <a name="generalize-the-vhd"></a>Generalizzare il disco rigido virtuale  

Utilizzare l'utilità *Sysprep* per generalizzare il disco rigido virtuale. 

1. All'interno della macchina virtuale aprire un prompt dei comandi.
1. Eseguire il comando seguente per generalizzare il disco rigido virtuale. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Per informazioni dettagliate, vedere  [Panoramica di Sysprep (preparazione del sistema)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).
1.  Al termine del comando, la macchina virtuale verrà arrestata. Non **riavviare la macchina virtuale**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Caricare il disco rigido virtuale nell'archiviazione BLOB di Azure

È ora possibile usare il disco rigido virtuale per creare un'immagine generalizzata in Azure Stack Edge. 

1. Caricare il disco rigido virtuale nell'archiviazione BLOB di Azure. Vedere le istruzioni dettagliate in [caricare un disco rigido virtuale con Azure Storage Explorer](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).
1. Al termine del caricamento, è possibile usare l'immagine caricata per creare immagini di VM e macchine virtuali. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Passaggi successivi

A seconda della natura della distribuzione, è possibile scegliere una delle procedure riportate di seguito.

- [Distribuire una macchina virtuale da un'immagine generalizzata tramite portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Distribuire una macchina virtuale da un'immagine generalizzata tramite Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
