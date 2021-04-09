---
title: Come gestire le interfacce di rete delle macchine virtuali in Azure Stack Edge Pro tramite il portale di Azure
description: Informazioni su come gestire le interfacce di rete nelle macchine virtuali distribuite nella GPU di Azure Stack Edge Pro tramite il portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage network interfaces on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 84077f174fabd02afcd5171e8d365e8cbd3a52c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027650"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Usare il portale di Azure per gestire le interfacce di rete nelle macchine virtuali nella GPU di Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

È possibile creare e gestire macchine virtuali (VM) in un dispositivo Azure Stack Edge usando portale di Azure, modelli, Azure PowerShell cmdlet e tramite gli script dell'interfaccia della riga di comando di Azure/Python. Questo articolo descrive come gestire le interfacce di rete in una macchina virtuale in esecuzione nel dispositivo Azure Stack Edge usando il portale di Azure. 

Quando si crea una VM, si specifica un'interfaccia di rete virtuale da creare. È possibile che si desideri aggiungere una o più interfacce di rete alla macchina virtuale dopo che è stata creata. È anche possibile modificare le impostazioni predefinite dell'interfaccia di rete per un'interfaccia di rete esistente.

Questo articolo illustra come aggiungere un'interfaccia di rete a una macchina virtuale esistente, modificare le impostazioni esistenti, ad esempio il tipo IP (statico e dinamico), e infine rimuovere o scollegare un'interfaccia esistente. 

        
## <a name="about-network-interfaces-on-vms"></a>Informazioni sulle interfacce di rete nelle macchine virtuali

Un'interfaccia di rete consente a una macchina virtuale (VM) in esecuzione sul dispositivo Azure Stack Edge Pro di comunicare con Azure e le risorse locali. Quando si abilita una porta per la rete di calcolo nel dispositivo, viene creato un commutire virtuale nell'interfaccia di rete. Questo commutire virtuale viene quindi usato per distribuire carichi di lavoro di calcolo, ad esempio macchine virtuali o applicazioni in contenitori nel dispositivo. 

Il dispositivo supporta solo un commutere virtuale ma più interfacce di rete virtuale. Ogni interfaccia di rete nella macchina virtuale ha un indirizzo IP statico o dinamico assegnato. Con gli indirizzi IP assegnati a più interfacce di rete nella macchina virtuale, alcune funzionalità sono abilitate nella macchina virtuale. Ad esempio, la macchina virtuale può ospitare più siti Web o servizi con indirizzi IP e certificati SSL diversi in un singolo server. Una VM del dispositivo può fungere da appliance virtuale di rete, ad esempio un firewall o un servizio di bilanciamento del carico. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a gestire le macchine virtuali nel dispositivo tramite il portale di Azure, assicurarsi che:

1. È stata abilitata un'interfaccia di rete per il calcolo nel dispositivo. Questa azione crea un commutire virtuale nell'interfaccia di rete della macchina virtuale. 
    1. Nell'interfaccia utente locale del dispositivo passare a **calcolo**. Selezionare l'interfaccia di rete che si userà per creare uno switch virtuale.

        > [!IMPORTANT] 
        > È possibile configurare una sola porta per il calcolo.

    1. Abilitare il calcolo nell'interfaccia di rete. Azure Stack GPU Pro Edge crea e gestisce un commutire virtuale corrispondente a tale interfaccia di rete.

1. Nel dispositivo è stata distribuita almeno una macchina virtuale. Per creare questa macchina virtuale, vedere le istruzioni in [distribuire una macchina virtuale in Azure stack Edge Pro tramite il portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. La macchina virtuale deve essere nello stato **interrotto** . Per arrestare la macchina virtuale, passare a **macchine virtuali > Panoramica** e selezionare la VM che si vuole arrestare. Nella pagina delle proprietà della macchina virtuale selezionare **Arresta** , quindi selezionare **Sì** quando viene richiesta la conferma. Prima di aggiungere, modificare o eliminare le interfacce di rete, è necessario arrestare la macchina virtuale.

    ![Pagina delle proprietà arresta macchina virtuale dalla macchina virtuale](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Aggiungere un'interfaccia di rete

Seguire questa procedura per aggiungere un'interfaccia di rete a una macchina virtuale distribuita nel dispositivo. 

1. Passare alla macchina virtuale arrestata e quindi andare alla pagina delle proprietà della **VM** . Selezionare **Rete**.
    
    ![Pagina Seleziona rete nella pagina delle proprietà della macchina virtuale](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. Nella barra dei comandi del pannello **rete** selezionare **+ Aggiungi interfaccia di rete**.

    ![Selezionare Aggiungi interfaccia di rete](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. Nel **Pannello Aggiungi interfaccia di rete** immettere i parametri seguenti:

    
    |Colonna 1  |Colonna 2  |
    |---------|---------|
    |Nome     | Nome univoco all'interno del gruppo di risorse. Il nome non può essere modificato dopo la creazione dell'interfaccia di rete. Per gestire facilmente più interfacce di rete, usare i suggerimenti forniti nelle [convenzioni di denominazione](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming).     |
    |Rete virtuale| Rete virtuale associata al commutire virtuale creato nel dispositivo quando è stato abilitato il calcolo nell'interfaccia di rete. Al dispositivo è associata una sola rete virtuale.         |         
    |Subnet     | Una subnet all'interno della rete virtuale selezionata. Questo campo viene popolato automaticamente con la subnet associata all'interfaccia di rete in cui è stata abilitata la funzionalità di calcolo.         |       
    |Assegnazione IP   | Un indirizzo IP statico o dinamico per l'interfaccia di rete. L'indirizzo IP statico deve essere un indirizzo IP libero disponibile dall'intervallo di subnet specificato. Scegliere dinamico se un server DHCP è presente nell'ambiente.        | 

    ![Aggiungere un pannello dell'interfaccia di rete](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Verrà visualizzata una notifica che indica che è in corso la creazione dell'interfaccia di rete.

    ![Notifica quando viene creata l'interfaccia di rete](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Al termine della creazione dell'interfaccia di rete, viene aggiornato l'elenco delle interfacce di rete per visualizzare l'interfaccia appena creata.

    ![Elenco aggiornato delle interfacce di rete](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Modificare un'interfaccia di rete

Seguire questa procedura per modificare un'interfaccia di rete associata a una macchina virtuale distribuita nel dispositivo.

1. Passare alla macchina virtuale arrestata e andare alla pagina delle proprietà della **VM** . Selezionare **Rete**.

1. Nell'elenco delle interfacce di rete selezionare l'interfaccia che si desidera modificare. Nell'estremità destra dell'interfaccia di rete selezionata selezionare l'icona di modifica (matita).  

    ![Selezionare un'interfaccia di rete da modificare](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. Nel **Pannello modifica interfaccia di rete** è possibile modificare solo l'assegnazione IP dell'interfaccia di rete. Il nome, la rete virtuale e la subnet associati all'interfaccia di rete non possono essere modificati dopo la creazione. Modificare l' **assegnazione IP** in static e salvare le modifiche.

    ![Modificare l'assegnazione IP per l'interfaccia di rete](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. L'elenco dell'interfaccia di rete viene aggiornato per visualizzare l'interfaccia di rete aggiornata.


## <a name="detach-a-network-interface"></a>Scollega un'interfaccia di rete

Seguire questa procedura per scollegare o rimuovere un'interfaccia di rete associata a una macchina virtuale distribuita nel dispositivo.

1. Passare alla macchina virtuale arrestata e andare alla pagina delle proprietà della **VM** . Selezionare **Rete**.

1. Nell'elenco delle interfacce di rete selezionare l'interfaccia che si desidera modificare. Nell'estremità destra dell'interfaccia di rete selezionata selezionare l'icona Disconnetti (Scollega).  

    ![Selezionare un'interfaccia di rete da scollegare](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Quando l'interfaccia viene scollegata completamente, viene aggiornato l'elenco delle interfacce di rete per visualizzare le interfacce rimanenti.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire le macchine virtuali nel dispositivo Azure Stack Edge Pro, vedere [distribuire macchine virtuali tramite l'portale di Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
