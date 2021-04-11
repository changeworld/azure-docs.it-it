---
title: Ridimensionare le macchine virtuali in Azure Stack GPU Pro Edge, Pro R, mini R tramite il portale di Azure
description: Informazioni su come ridimensionare le macchine virtuali (VM) in esecuzione sul Azure Stack GPU Pro Edge, Azure Stack Edge Pro R, Azure Stack Edge Mini R tramite il portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080515"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Usare il portale di Azure per ridimensionare le macchine virtuali sulla GPU di Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo illustra come ridimensionare le macchine virtuali (VM) distribuite nel dispositivo GPU Azure Stack Edge Pro.

       
## <a name="about-vm-sizing"></a>Informazioni sul dimensionamento delle macchine virtuali

Le dimensioni della macchina virtuale determinano la quantità di risorse di calcolo, ad esempio CPU, GPU e memoria, rese disponibili per la VM. È necessario creare macchine virtuali usando le dimensioni della VM appropriate per il carico di lavoro dell'applicazione. 

Anche se tutti i computer verranno eseguiti sullo stesso hardware, le dimensioni dei computer hanno limiti diversi per l'accesso al disco. Ciò consente di gestire l'accesso globale al disco tra le macchine virtuali. Se un carico di lavoro aumenta, è anche possibile ridimensionare una macchina virtuale esistente.

Per altre informazioni, vedere [dimensioni delle macchine virtuali supportate per il dispositivo](azure-stack-edge-gpu-virtual-machine-sizes.md).


## <a name="prerequisites"></a>Prerequisiti

Prima di ridimensionare una macchina virtuale in esecuzione nel dispositivo tramite il portale di Azure, verificare che:

1. Nel dispositivo è stata distribuita almeno una macchina virtuale. Per creare questa macchina virtuale, vedere le istruzioni in [distribuire una macchina virtuale in Azure stack Edge Pro tramite il portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. La macchina virtuale deve essere nello stato **interrotto** . Per arrestare la macchina virtuale, passare a **macchine virtuali > Panoramica** e selezionare la VM che si vuole arrestare. Nella pagina Panoramica selezionare **Arresta** , quindi selezionare **Sì** quando viene richiesta la conferma. Prima di ridimensionare la VM, è necessario arrestare la macchina virtuale.

    ![Pagina arresta macchina virtuale dalla panoramica](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>Ridimensionare una VM

Per ridimensionare una macchina virtuale distribuita nel dispositivo, seguire questa procedura. 

1. Passare alla macchina virtuale che è stata arrestata e quindi andare alla pagina **Overview (panoramica** ). Selezionare **dimensioni macchina virtuale (modifica)**.
    
    ![Selezionare Modifica dimensioni macchina virtuale nella pagina Panoramica](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. Nella barra dei comandi del pannello **modifica dimensioni VM** selezionare le **dimensioni della macchina virtuale** e quindi selezionare **Cambia**.

    ![Selezionare le nuove dimensioni della macchina virtuale](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Verrà visualizzata una notifica che indica che la macchina virtuale è in fase di aggiornamento. Dopo che la macchina virtuale è stata aggiornata correttamente, la pagina **Panoramica** viene aggiornata per visualizzare la VM ridimensionata.

    ![VM ridimensionata ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire le macchine virtuali nel dispositivo Azure Stack Edge Pro, vedere [distribuire macchine virtuali tramite l'portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
