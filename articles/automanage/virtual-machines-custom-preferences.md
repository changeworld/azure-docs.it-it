---
title: Creare una preferenza personalizzata in Azure automanage per le macchine virtuali
description: Informazioni su come modificare la configurazione dell'ambiente in gestione automatica di Azure e impostare le proprie preferenze.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 584a3503bf736fcf727a169611e6c79e0c374c90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101647932"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Creare una preferenza personalizzata in Azure automanage per le macchine virtuali

Gestione automatica di Azure per le procedure consigliate per le macchine virtuali ha ambienti predefiniti che è possibile modificare se necessario. Questo articolo illustra come è possibile impostare le proprie preferenze quando si Abilita la gestione autonoma in una macchina virtuale nuova o esistente.

Attualmente sono supportate le personalizzazioni in [backup di Azure](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) e [Microsoft antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Non è possibile modificare l'ambiente o la preferenza nella macchina virtuale mentre è abilitata la funzionalità automanage. È necessario disabilitare la gestione automatica per tale macchina virtuale e quindi abilitare di nuovo la gestione automatica con l'ambiente e le preferenze di configurazione desiderate.


## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) prima di iniziare.

> [!NOTE]
> Gli account di valutazione gratuiti non hanno accesso alle macchine virtuali usate in questa esercitazione. Eseguire l'aggiornamento a una sottoscrizione con pagamento in base al consumo.

> [!IMPORTANT]
> L'autorizzazione RBAC di Azure seguente è necessaria per abilitare la gestione autogestita: ruolo **proprietario** o **collaboratore** insieme ai ruoli di **amministratore accesso utenti** .


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Abilitare Gestione automatica per le VM in una VM esistente

1. Nella barra di ricerca cercare e selezionare **automanage-procedure consigliate per Azure Machine**.

2. Selezionare **Abilita nella macchina virtuale esistente**.

3. Nel pannello **Seleziona macchine virtuali**:
    1. Filtrare le VM in base alla **sottoscrizione** e al **gruppo di risorse**.
    1. Selezionare la casella di controllo di ogni macchina virtuale di cui si vuole eseguire l'onboarding.
    1. Fare clic sul pulsante **Seleziona**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Selezionare la VM esistente dall'elenco delle VM disponibili.":::

    > [!NOTE]
    > Fare clic su **Mostra computer non idonei** per visualizzare l'elenco dei computer non supportati e la motivazione. 

4. In **configurazione** fare clic su **Confronta ambienti**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Confrontare gli ambienti.":::

5. Nel pannello dei **Dettagli dell'ambiente** selezionare un ambiente dal menu a discesa: *dev/test* for testing, *Prod* for Production e fare clic su **OK** .

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Esplorare l'ambiente di produzione.":::

6. Dopo aver selezionato l'ambiente, è possibile selezionare **Preferenze di configurazione**. Per impostazione predefinita, verrà usata la preferenza per le procedure consigliate di Azure. Questa preferenza contiene usa le impostazioni consigliate per ogni servizio. Modificare queste impostazioni creando una preferenza personalizzata: 
    1. Fare clic su **Crea nuove preferenze**.
    1. Nel pannello **Crea una preferenza di configurazione** compilare la scheda nozioni di base:
        1. Subscription
        1. Resource group
        1. Nome preferenza
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Compilare le preferenze di configurazione.":::

7. Modificare le preferenze di configurazione desiderate.
        
    > [!NOTE]
    > Quando si modificano le configurazioni dell'ambiente, saranno consentite solo le rettifiche che si adattano alle procedure consigliate per i limiti superiore e inferiore.

8. Esaminare i dettagli di configurazione.
9. Fare clic sul pulsante **Create** (Crea).

10. Fare clic sul pulsante **Abilita**.


## <a name="disable-automanage-for-vms"></a>Disabilitare Gestione automatica per le VM

Per smettere di usare Gestione automatica di Azure, basta disabilitare la funzionalità.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Disabilitazione di Gestione automatica in una macchina virtuale.":::

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

Per ottenere risposte alle domande più frequenti, vedere le domande frequenti. 

> [!div class="nextstepaction"]
> [Domande frequenti](faq.md)