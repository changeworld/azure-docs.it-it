---
title: Abilitare La gestione automatica per le macchine virtuali tramite Criteri di Azure
description: Informazioni su come abilitare Gestione automatica di Azure per le macchine virtuali tramite un Criteri di Azure incorporato nel portale di Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8846efa3619cec383809cdbd6efe70e3622fa007
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365196"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Abilitare La gestione automatica per le macchine virtuali tramite Criteri di Azure

Se si vuole abilitare La gestione automatica per molte macchine virtuali, è possibile farlo usando un'istanza [Criteri di Azure](..\governance\azure-management.md). Questo articolo illustra come trovare il criterio corretto e come assegnarlo per abilitare La gestione automatica nel portale di Azure.


## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) prima di iniziare.

> [!NOTE]
> Gli account di valutazione gratuiti non hanno accesso alle macchine virtuali usate in questa esercitazione. Eseguire l'aggiornamento a una sottoscrizione con pagamento in base al consumo.

> [!IMPORTANT]
> Per abilitare La gestione automatica è necessaria l'autorizzazione di Controllo degli accessi in base al ruolo di Azure **seguente:** Ruolo proprietario o **Collaboratore** insieme ai **ruoli Amministratore Accesso** utenti.

## <a name="direct-link-to-policy"></a>Collegamento diretto ai criteri
La definizione dei criteri Di gestione automatica è disponibile nella portale di Azure con il nome Configure [virtual machines to be onboarded to Gestione automatica di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3). Se si fa clic su questo collegamento, andare direttamente al passaggio 8 in [Individuare e assegnare i criteri seguenti.](#locate-and-assign-the-policy)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Individuare e assegnare i criteri

1. Passare a **Criteri** nel portale di Azure
1. Passare al riquadro **Definizioni**
1. Fare clic **sull'elenco** a discesa Categorie per visualizzare le opzioni disponibili
1. Selezionare **l'opzione Enable Automanage – Azure virtual machine best practices** (Abilita Gestione automatica - Procedure consigliate per le macchine virtuali di Azure)
1. A questo punto l'elenco verrà aggiornato per visualizzare un criterio predefinito con un nome che inizia con *Abilita gestione automatica...*
1. Fare clic sul *nome del criterio predefinito Enable Automanage - Azure virtual machine best practices* (Abilita Gestione automatica - Procedure consigliate per le macchine virtuali di Azure)
1. Dopo aver fatto clic sul criterio, è ora possibile visualizzare la **scheda Definizione**

    > [!NOTE]
    > La Criteri di Azure viene usata per impostare Parametri di gestione automatica come il profilo di configurazione o l'account. Imposta anche i filtri che assicurano che i criteri si applitino solo alle macchine virtuali corrette.

1. Fare clic **sul pulsante** Assegna per creare un'assegnazione
1. Nella scheda **Informazioni di base** compilare **Ambito** impostando *sottoscrizione* e gruppo *di risorse*

    > [!NOTE]
    > L'ambito consente di definire le macchine virtuali a cui si applicano i criteri. È possibile impostare l'applicazione a livello di sottoscrizione o di gruppo di risorse. Se si imposta un gruppo di risorse, tutte le macchine virtuali che si trova attualmente in tale gruppo di risorse o in qualsiasi vm futura aggiunta a tale gruppo avranno la gestione automatica abilitata automaticamente.

1. Fare clic sulla **scheda Parametri** e impostare **l'account di gestione automatica** e il profilo di configurazione **desiderato**
1. Nella scheda **Rivedi e crea** esaminare le impostazioni
1. Applicare l'assegnazione facendo clic su **Crea**
1. Visualizzare le assegnazioni nella **scheda Assegnazioni** accanto a **Definizione**

> [!NOTE]
> L'applicazione di questo criterio alle macchine virtuali attualmente nel gruppo di risorse o nella sottoscrizione potrebbe richiedere del tempo.


## <a name="next-steps"></a>Passaggi successivi

Informazioni su un altro modo per abilitare Gestione automatica di Azure per le macchine virtuali tramite portale di Azure.

> [!div class="nextstepaction"]
> [Abilitare la gestione automatica per le macchine virtuali nel portale di Azure](quick-create-virtual-machines-portal.md)