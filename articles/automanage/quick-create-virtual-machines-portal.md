---
title: Avvio rapido - Abilitare Gestione automatica di Azure per le macchine virtuali nel portale di Azure
description: Informazioni su come abilitare rapidamente Gestione automatica per le macchine virtuali in una VM nuova o esistente nel portale di Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: d00a9c6012da7ad8d1566ef82bce628c7d47e7a7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687005"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Avvio rapido: Abilitare Gestione automatica di Azure per le macchine virtuali nel portale di Azure

Questo argomento di avvio rapido spiega come usare il portale di Azure per abilitare Gestione automatica di Azure per le macchine virtuali in una VM nuova o esistente.


## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) prima di iniziare.

> [!NOTE]
> Gli account di valutazione gratuiti non hanno accesso alle macchine virtuali usate in questa esercitazione. Eseguire l'aggiornamento a una sottoscrizione con pagamento in base al consumo.

> [!IMPORTANT]
> Per abilitare Gestione automatica usando un account di Gestione automatica esistente, è necessario avere il ruolo **Collaboratore** nel gruppo di risorse che contiene le VM. Se si abilita Gestione automatica con un nuovo account di Gestione automatica, è necessario avere le autorizzazioni seguenti: ruolo **Proprietario** o **Collaboratore** insieme al ruolo **Amministratore Accesso utenti** nella sottoscrizione.


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Abilitare la gestione di una singola macchina virtuale

1. Passare alla macchina virtuale che si vuole abilitare.

2. Fare clic sulla voce **gestione automatica (anteprima)** nel sommario in **operazioni**.

3. Selezionare **Attività iniziali**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Introduzione a una singola macchina virtuale.":::

4. Scegliere le impostazioni di gestione di automanage (ambiente, preferenze, account di gestione autogestito) e fare clic su **Abilita**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Abilitare su una singola macchina virtuale.":::

## <a name="enable-automanage-for-multiple-vms"></a>Abilitare la gestione autogestita per più macchine virtuali

1. Nella barra di ricerca cercare e selezionare **automanage-procedure consigliate per Azure Machine**.

2. Selezionare **Abilita nella macchina virtuale esistente**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Abilita nella macchina virtuale esistente.":::

3. Nel pannello **Seleziona macchine virtuali**:
    1. Filtrare le VM in base alla **sottoscrizione** e al **gruppo di risorse**.
    1. Selezionare la casella di controllo di ogni macchina virtuale di cui si vuole eseguire l'onboarding.
    1. Fare clic sul pulsante **Seleziona**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Selezionare la VM esistente dall'elenco delle VM disponibili.":::

4. In **ambiente** selezionare il tipo di ambiente: **sviluppo/test** o **produzione**. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Selezionare ambienti.":::

   Fare clic su **Confronta dettagli ambiente** per visualizzare le differenze tra gli ambienti.
    1. Selezionare un ambiente nell'elenco a discesa: *sviluppo/test* per test, *produzione* per la produzione.
    1. Fare clic sul pulsante **OK** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Esplorare l'ambiente di produzione.":::

5. Per impostazione predefinita, la preferenza per le **procedure consigliate di Azure** è selezionata per le preferenze di configurazione. Per modificare questa opzione, creare una nuova preferenza o selezionarne una esistente. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Crea preferenza.":::

6. Fare clic sul pulsante **Abilita**.


## <a name="enable-automanage-for-a-new-vm"></a>Abilitare la gestione per una nuova macchina virtuale

Accedere al portale di Azure [qui](https://aka.ms/AzureAutomanagePreview) per creare una nuova VM e abilitare Gestione automatica.

1. Compilare la scheda **nozioni di base** con i dettagli della macchina virtuale.

> [!NOTE]
> Controllare le [aree supportate](automanage-virtual-machines#supported-regions) da automanage e le [distribuzioni Linux](automanage-linux.md#supported-linux-distributions-and-versions) supportate da automanage e le [versioni di Windows Server](automanage-windows-server.md#supported-windows-server-versions).

2. Passare alla scheda **gestione** e scegliere l' **ambiente automanage**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Abilitare la gestione di automanage nella scheda gestione.":::

3. Lasciare invariate le impostazioni predefinite rimanenti, quindi selezionare il pulsante **Rivedi e crea** nella parte inferiore della pagina.

4. Quando viene visualizzato il messaggio che la convalida è stata superata, selezionare **Crea**.

## <a name="disable-automanage-for-vms"></a>Disabilitare Gestione automatica per le VM

Per smettere di usare Gestione automatica di Azure, basta disabilitare la funzionalità.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Disabilitazione di Gestione automatica in una macchina virtuale.":::

1. Passare alla pagina **Gestione automatica - Procedure consigliate per le macchine virtuali di Azure** in cui sono elencate tutte le VM gestite automaticamente.
1. Selezionare la casella di controllo accanto alla macchina virtuale da disabilitare.
1. Fare clic sul pulsante **Disabilita gestione automatica**.
1. Leggere attentamente i messaggi nella finestra popup visualizzata prima di fare clic su **Disabilita**.


## <a name="clean-up-resources"></a>Pulire le risorse

Se per provare Gestione automatica di Azure per le macchine virtuali è stato creato un nuovo gruppo di risorse che ora non serve più, è possibile eliminarlo. Eliminando il gruppo di risorse vengono eliminate anche la VM e tutte le risorse all'interno del gruppo.

Gestione automatica di Azure crea gruppi di risorse predefiniti in cui archiviare le risorse. Selezionare i gruppi di risorse con la convenzione di denominazione "DefaultResourceGroupRegionName" e "AzureBackupRGRegionName" per pulire tutte le risorse.

1. Selezionare **Gruppo di risorse**.
1. Nella pagina del gruppo di risorse selezionare **Elimina**.
1. Quando richiesto, confermare il nome del gruppo di risorse e quindi selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata abilitata la funzionalità Gestione automatica di Azure per le macchine virtuali.

A questo punto è possibile apprendere come creare e applicare preferenze personalizzate quando si abilita Gestione automatica nella macchina virtuale.

> [!div class="nextstepaction"]
> [Gestione automatica di Azure per le macchine virtuali-preferenze di configurazione personalizzate](virtual-machines-custom-preferences.md)
