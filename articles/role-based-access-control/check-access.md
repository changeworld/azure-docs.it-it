---
title: Avvio rapido - Controllare l'accesso di un utente alle risorse di Azure - Controllo degli accessi in base al ruolo di Azure
description: Questa guida di avvio rapido illustra come controllare l'accesso di un utente alle risorse di Azure usando il portale di Azure e il controllo degli accessi in base al ruolo di Azure.
services: role-based-access-control
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5e4f3314ba580dddbd995855bc0f0512b7597107
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98115735"
---
# <a name="quickstart-check-access-for-a-user-to-azure-resources"></a>Avvio rapido: Controllare l'accesso di un utente alle risorse di Azure

In alcune situazioni può essere necessario controllare l'accesso di un utente a un set di risorse di Azure. A questo scopo occorre visualizzare l'elenco delle assegnazioni dell'utente. Un modo rapido per controllare l'accesso per un singolo utente è tramite la funzionalità **Verifica l'accesso** nella pagina **Controllo di accesso (IAM)** .

## <a name="step-1-open-the-azure-resources"></a>Passaggio 1: Aprire le risorse di Azure

Per verificare l'accesso per un utente, occorre prima di tutto aprire le risorse di Azure per cui si vuole controllare l'accesso. Le risorse di Azure sono organizzate in livelli che costituiscono l'*ambito*. In Azure è possibile specificare un ambito su quattro livelli, dal più ampio al più ristretto: gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa.

![Livelli di ambito per il controllo degli accessi in base al ruolo di Azure](../../includes/role-based-access-control/media/scope-levels.png)

Per aprire il set di risorse di Azure per cui si vuole controllare l'accesso, seguire questa procedura.

1. Aprire il [portale di Azure](https://portal.azure.com).

1. Aprire il set di risorse di Azure, ad esempio **Gruppi di gestione**, **Sottoscrizioni**, **Gruppi di risorse** o una determinata risorsa.

1. Fare clic sulla risorsa specifica in tale ambito.

    L'immagine seguente mostra un gruppo di risorse di esempio.

    ![Panoramica del gruppo di risorse](./media/shared/rg-overview.png)

## <a name="step-2-check-access-for-a-user"></a>Passaggio 2: Controllare l'accesso per un utente

Seguire questa procedura per controllare l'accesso di un singolo utente, gruppo, entità servizio o identità gestita alle risorse di Azure selezionate in precedenza.

1. Fare clic su **Controllo di accesso (IAM)**.

    L'immagine seguente mostra un esempio della pagina Controllo di accesso (IAM) per un gruppo di risorse.

    ![Scheda Verifica l'accesso della pagina Controllo di accesso per un gruppo di risorse](./media/shared/rg-access-control.png)

1. Nell'elenco **Trova** della scheda **Verifica l'accesso** selezionare l'utente, il gruppo, l'entità servizio o l'identità gestita per cui si vuole controllare l'accesso.

1. Nella casella di ricerca, immettere una stringa per eseguire ricerche nella directory in base ai nomi visualizzati, agli indirizzi di posta elettronica o agli identificatori di oggetto.

    ![Elenco di selezione per la verifica dell'accesso](./media/shared/rg-check-access-select.png)

1. Fare clic sull'entità di sicurezza per aprire il riquadro **Assegnazioni**.

    In questo riquadro è possibile visualizzare l'accesso per l'entità di sicurezza selezionata in questo ambito, oltre a quello ereditato da un altro ambito. Le assegnazioni in corrispondenza degli ambiti figlio non sono elencate. Vengono visualizzate le assegnazioni seguenti:

    - Assegnazioni di ruolo aggiunte con il controllo degli accessi in base al ruolo di Azure.
    - Assegnazioni di rifiuto aggiunte tramite Azure Blueprints o app gestite di Azure.
    - Assegnazioni classiche di amministratore del servizio e coamministratore per le distribuzioni classiche. 

    ![Riquadro delle assegnazioni di ruolo e delle assegnazioni di rifiuto per un utente](./media/shared/rg-check-access-assignments-user.png)

## <a name="step-3-check-your-access"></a>Passaggio 3: Verificare l'accesso

Seguire questa procedura per verificare il proprio accesso alle risorse di Azure selezionate in precedenza.

1. Fare clic su **Controllo di accesso (IAM)**.

1. Nella scheda **Verifica l'accesso** fare clic sul pulsante **Visualizzazione accesso personale**.

    Viene visualizzato un riquadro che mostra l'accesso dell'utente in questo ambito e quello ereditato da un altro ambito. Le assegnazioni in corrispondenza degli ambiti figlio non sono elencate.

    ![Riquadro delle assegnazioni di ruolo e delle assegnazioni di rifiuto](./media/check-access/rg-check-access-assignments.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Elencare le assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-list-portal.md)
