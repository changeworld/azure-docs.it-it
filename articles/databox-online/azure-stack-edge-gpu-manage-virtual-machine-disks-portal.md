---
title: Gestire i dischi delle macchine virtuali nella GPU Pro Azure Stack Edge, Pro R, mini R tramite portale di Azure
description: Informazioni su come gestire i dischi, tra cui aggiungere o scollegare un disco dati in macchine virtuali distribuite nella GPU Pro Azure Stack Edge, Azure Stack Edge Pro R e Azure Stack mini R Edge tramite il portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080555"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Usare il portale di Azure per gestire i dischi nelle macchine virtuali nella GPU di Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

È possibile eseguire il provisioning dei dischi nelle macchine virtuali (VM) distribuite nel dispositivo Azure Stack Edge Pro usando il portale di Azure. Il provisioning dei dischi viene effettuato sul dispositivo tramite il Azure Resource Manager locale e utilizza la capacità del dispositivo. Le operazioni quali l'aggiunta di un disco, lo scollegamento di un disco possono essere eseguite tramite la portale di Azure, che a sua volta effettua chiamate al Azure Resource Manager locale per eseguire il provisioning dell'archiviazione. 

Questo articolo illustra come aggiungere un disco dati a una macchina virtuale esistente, scollegare un disco dati e infine ridimensionare la macchina virtuale tramite il portale di Azure. 

        
## <a name="about-disks-on-vms"></a>Informazioni sui dischi nelle macchine virtuali

La macchina virtuale può avere un disco del sistema operativo e un disco dati. Ogni macchina virtuale distribuita nel dispositivo dispone di un disco del sistema operativo collegato. Questo disco del sistema operativo ha un sistema operativo preinstallato, che è stato selezionato al momento della creazione della macchina virtuale. Questo disco contiene il volume di avvio.

> [!NOTE]
> Non è possibile modificare le dimensioni del disco del sistema operativo per la macchina virtuale nel dispositivo. Le dimensioni del disco del sistema operativo sono determinate dalle dimensioni della macchina virtuale selezionate. 


Un disco dati, invece, è un disco gestito collegato alla macchina virtuale in esecuzione nel dispositivo. Viene usato un disco dati per archiviare i dati dell'applicazione. I dischi dati sono in genere unità SCSI. Le dimensioni della macchina virtuale determinano il numero di dischi dati che è possibile connettersi a una macchina virtuale. Per impostazione predefinita, viene usato archiviazione Premium per ospitare i dischi.

Una macchina virtuale distribuita nel dispositivo può talvolta contenere un disco temporaneo. Il disco temporaneo fornisce l'archiviazione a breve termine per le applicazioni e i processi ed è destinato solo a archiviare i dati, ad esempio file di paging o di scambio. I dati presenti nel disco temporaneo potrebbero andare persi durante un evento di manutenzione o la ridistribuzione di una VM. Durante un riavvio standard della VM, i dati sul disco temporaneo rimarranno in modo permanente. 


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a gestire i dischi delle macchine virtuali in esecuzione nel dispositivo tramite il portale di Azure, assicurarsi che:


1. È possibile accedere a un dispositivo GPU Pro Azure Stack Edge attivato. È stata anche abilitata un'interfaccia di rete per il calcolo nel dispositivo. Questa azione crea un commutire virtuale nell'interfaccia di rete della macchina virtuale. 
    1. Nell'interfaccia utente locale del dispositivo passare a **calcolo**. Selezionare l'interfaccia di rete che si userà per creare uno switch virtuale.

        > [!IMPORTANT] 
        > È possibile configurare una sola porta per il calcolo.

    1. Abilitare il calcolo nell'interfaccia di rete. Azure Stack GPU Pro Edge crea e gestisce un commutire virtuale corrispondente a tale interfaccia di rete.

1. Nel dispositivo è stata distribuita almeno una macchina virtuale. Per creare questa macchina virtuale, vedere le istruzioni in [distribuire una macchina virtuale in Azure stack Edge Pro tramite il portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).



## <a name="add-a-data-disk"></a>Aggiungere un disco dati

Per aggiungere un disco a una macchina virtuale distribuita nel dispositivo, seguire questa procedura. 

1. Passare alla macchina virtuale a cui si vuole aggiungere un disco dati e quindi andare alla pagina Overview ( **Panoramica** ). Selezionare **Dischi**.
    
    ![Selezionare i dischi nella pagina Panoramica](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. Nel pannello **dischi** selezionare **Crea e Connetti un nuovo disco** in **dischi dati**.

    ![Creare e alleghi un nuovo disco](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. Nel pannello **Crea un nuovo disco** immettere i parametri seguenti:

    
    |Campo  |Descrizione  |
    |---------|---------|
    |Nome     | Nome univoco all'interno del gruppo di risorse. Il nome non può essere modificato dopo la creazione del disco dati.     |
    |Dimensione| Dimensioni del disco dati in GiB. Le dimensioni massime di un disco dati sono determinate dalle dimensioni della macchina virtuale selezionate. Quando si esegue il provisioning di un disco, è necessario considerare anche lo spazio effettivo sul dispositivo e gli altri carichi di lavoro della macchina virtuale in esecuzione che utilizzano la capacità.  |         

    ![Creare un nuovo pannello del disco](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Selezionare **OK** e continuare.

1. Nella pagina **Panoramica** , in **dischi**, viene visualizzata una voce corrispondente al nuovo disco. Accettare l'impostazione predefinita o assegnare un numero di unità logica (LUN) valido al disco e selezionare **Salva**. Un LUN è un identificatore univoco per un disco SCSI. Per ulteriori informazioni, vedere [che cos'è un lun?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Nuovo disco nella pagina Panoramica](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Verrà visualizzata una notifica che indica che è in corso la creazione del disco. Dopo la creazione del disco, la macchina virtuale viene aggiornata. 

    ![Notifica per la creazione del disco](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Tornare alla pagina **Overview (panoramica** ). L'elenco di dischi viene aggiornato per visualizzare il disco dati appena creato.

    ![Elenco aggiornato dei dischi dati](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Modificare un disco dati

Seguire questa procedura per modificare un disco associato a una macchina virtuale distribuita nel dispositivo.

1. Passare alla macchina virtuale in cui è presente il disco dati da modificare e passare alla pagina **Panoramica** . Selezionare **Dischi**.

1. Nell'elenco dei dischi dati selezionare il disco che si desidera modificare. Nell'estremità destra del disco selezionato selezionare l'icona di modifica (matita).  

    ![Selezionare un disco da modificare](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. Nel pannello **modifica disco** è possibile modificare solo le dimensioni del disco. Il nome associato al disco non può essere modificato dopo la creazione. Modificare le **dimensioni** e salvare le modifiche.

    ![Modificare le dimensioni del disco dati](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > È possibile espandere solo un disco dati, non è possibile compattare il disco.

1. Nella pagina **Overview** l'elenco dei dischi viene aggiornato per visualizzare il disco aggiornato.


## <a name="attach-an-existing-disk"></a>Collegare un disco esistente

Per aggiungere un disco esistente alla macchina virtuale distribuita nel dispositivo, seguire questa procedura.

1. Passare alla macchina virtuale a cui si vuole aggiungere il disco esistente, quindi passare alla pagina **Panoramica** . Selezionare **Dischi**.
    
    ![Selezione dischi ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Nel pannello **dischi** , in **dischi dati**, selezionare **Connetti un disco esistente**.

    ![Selezionare Connetti un disco esistente](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Accettare un LUN predefinito o assegnare un LUN valido. Scegliere un disco dati esistente dall'elenco a discesa. Selezionare Salva.

    ![Seleziona un disco esistente](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Selezionare **Salva** e continua.

1. Verrà visualizzata una notifica che indica che la macchina virtuale è stata aggiornata. Dopo l'aggiornamento della macchina virtuale, tornare alla pagina **Overview (panoramica** ). Aggiornare la pagina per visualizzare il disco appena collegato nell'elenco dei dischi dati.

    ![Visualizza l'elenco aggiornato dei dischi dati nella pagina Panoramica](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Scollegare un disco dati

Seguire questa procedura per scollegare o rimuovere un disco dati associato a una macchina virtuale distribuita nel dispositivo.

> [!NOTE]
> - È possibile rimuovere un disco dati mentre la macchina virtuale è in esecuzione. Assicurarsi che non venga usato attivamente il disco prima di scollegarlo dalla macchina virtuale.
> - Se si scollega un disco, non viene eliminato automaticamente.

1. Passare alla macchina virtuale da cui si vuole scollegare un disco dati e passare alla pagina **Panoramica** . Selezionare **Dischi**.

    ![Selezione dischi](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Nell'elenco dei dischi selezionare il disco che si desidera scollegare. Nell'estremità destra del disco selezionato selezionare l'icona Disconnetti (Cross). La voce selezionata verrà scollegata. Selezionare **Salva**. 

    ![Selezionare un disco da scollegare](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Una volta scollegato il disco, la macchina virtuale viene aggiornata. Aggiornare la pagina **Panoramica** per visualizzare l'elenco aggiornato dei dischi dati.

    ![Selezionare Salva](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire le macchine virtuali nel dispositivo Azure Stack Edge Pro, vedere [distribuire macchine virtuali tramite l'portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
