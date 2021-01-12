---
title: Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il controllo degli accessi in base al ruolo portale di Azure
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando il portale di Azure e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: f1753e7bc50fa9ff2c5512696a37dae7578f23b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117445"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Questo articolo descrive come assegnare i ruoli usando il portale di Azure.

Se è necessario assegnare ruoli di amministratore in Azure Active Directory, vedere [visualizzare e assegnare ruoli di amministratore in Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="add-a-role-assignment"></a>Aggiungi un'assegnazione di ruolo

In controllo degli accessi in base al ruolo di Azure per concedere l'accesso a una risorsa di Azure è necessario aggiungere un'assegnazione di ruolo Per assegnare un ruolo, seguire questa procedura. Per una panoramica di alto livello dei passaggi, vedere [la procedura per aggiungere un'assegnazione di ruolo](role-assignments-steps.md).

### <a name="step-1-identify-the-needed-scope"></a>Passaggio 1: identificare l'ambito necessario

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Per ulteriori informazioni sull'ambito, vedere [comprendere l'ambito](scope-overview.md).

![Livelli di ambito per il controllo degli accessi in base al ruolo di Azure](../../includes/role-based-access-control/media/scope-levels.png)

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella casella di ricerca nella parte superiore cercare l'ambito a cui si vuole concedere l'accesso. Ad esempio, cercare **gruppi di gestione**, **sottoscrizioni**, **gruppi di risorse** o una risorsa specifica.

    ![portale di Azure ricerca di un gruppo di risorse](./media/shared/rg-portal-search.png)

1. Fare clic sulla risorsa specifica per tale ambito.

    L'immagine seguente mostra un gruppo di risorse di esempio.

    ![Panoramica del gruppo di risorse](./media/shared/rg-overview.png)

### <a name="step-2-open-the-add-role-assignment-pane"></a>Passaggio 2: aprire il riquadro Aggiungi assegnazione ruolo

Il **controllo di accesso (IAM)** è la pagina che in genere si usa per assegnare i ruoli per concedere l'accesso alle risorse di Azure. È noto anche come gestione delle identità e degli accessi e viene visualizzato in diverse posizioni nel portale di Azure.

1. Fare clic su **Controllo di accesso (IAM)**.

    L'immagine seguente mostra un esempio della pagina Controllo di accesso (IAM) per un gruppo di risorse.

    ![Pagina controllo di accesso (IAM) per un gruppo di risorse](./media/shared/rg-access-control.png)

1. Fare clic sulla scheda **assegnazioni di ruolo** per visualizzare le assegnazioni di ruolo in questo ambito.

1. Fare clic su **Aggiungi**  >  **Aggiungi assegnazione ruolo**.
   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![Menu Aggiungi assegnazione di ruolo](./media/shared/add-role-assignment-menu.png)

    Viene visualizzato il riquadro Aggiungi assegnazione di ruolo.

   ![Riquadro Aggiungi assegnazione di ruolo](./media/shared/add-role-assignment.png)

### <a name="step-3-select-the-appropriate-role"></a>Passaggio 3: selezionare il ruolo appropriato

1. Nell'elenco **ruolo** cercare o scorrere per trovare il ruolo che si desidera assegnare.

    Per determinare il ruolo appropriato, è possibile passare il mouse sull'icona info per visualizzare una descrizione del ruolo. Per altre informazioni, vedere l'articolo [ruoli predefiniti di Azure](built-in-roles.md) .

   ![Selezione ruolo in Aggiungi assegnazione ruolo](./media/role-assignments-portal/add-role-assignment-role.png)

1. Fare clic per selezionare il ruolo.

### <a name="step-4-select-who-needs-access"></a>Passaggio 4: selezionare chi necessita di accesso

1. Nell'elenco **assegna accesso a** selezionare il tipo di entità di sicurezza a cui assegnare l'accesso.

    | Type | Descrizione |
    | --- | --- |
    | **Utente, gruppo o entità servizio** | Se si desidera assegnare il ruolo a un utente, a un gruppo o a un'entità servizio (applicazione), selezionare questo tipo. |
    | **Identità gestita assegnata dall'utente** | Se si desidera assegnare il ruolo a un' [identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/overview.md), selezionare questo tipo. |
    | *Identità gestita assegnata dal sistema* | Se si vuole assegnare il ruolo a un' [identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md), selezionare l'istanza del servizio di Azure in cui si trova l'identità gestita. |

   ![Selezionare il tipo di entità di sicurezza in Aggiungi assegnazione ruolo](./media/role-assignments-portal/add-role-assignment-type.png)

1. Se è stata selezionata un'identità gestita assegnata dall'utente o un'identità gestita assegnata dal sistema, selezionare la **sottoscrizione** in cui si trova l'identità gestita.

1. Nella sezione **Seleziona** cercare l'entità di sicurezza immettendo una stringa o scorrendo l'elenco.

   ![Selezionare un utente in Aggiungi assegnazione ruolo](./media/role-assignments-portal/add-role-assignment-user.png)

1. Dopo aver trovato l'entità di sicurezza, fare clic per selezionarla.

### <a name="step-5-assign-role"></a>Passaggio 5: assegnare un ruolo

1. Per assegnare il ruolo, fare clic su **Salva**.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito selezionato.

1. Nella scheda **assegnazioni di ruolo** verificare che nell'elenco sia visualizzata l'assegnazione di ruolo.

    ![Aggiunta dell'assegnazione di ruolo salvata](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

In controllo degli accessi in base al ruolo di Azure è possibile rimuovere un'assegnazione di ruolo. Per rimuovere un'assegnazione di ruolo, attenersi alla seguente procedura.

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende rimuovere l'accesso.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

1. Nell'elenco delle assegnazioni di ruolo aggiungere un segno di spunta accanto all'entità di sicurezza con l'assegnazione di ruolo che si vuole rimuovere.

   ![Assegnazione di ruolo selezionata per la rimozione](./media/role-assignments-portal/rg-role-assignments-select.png)

1. Scegliere **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-portal/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo fare clic su **Sì**.

    Se viene visualizzato un messaggio in cui non è possibile rimuovere le assegnazioni di ruolo ereditate, si sta tentando di rimuovere un'assegnazione di ruolo in un ambito figlio. È necessario aprire il controllo di accesso (IAM) nell'ambito in cui è stato assegnato il ruolo e riprovare. Un modo rapido per aprire il controllo di accesso (IAM) nell'ambito corretto consiste nell'esaminare la colonna **ambito** e fare clic sul collegamento accanto a **(ereditato)**.

   ![Rimuovere un messaggio di assegnazione di ruolo per le assegnazioni di ruolo ereditate](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un utente come amministratore di una sottoscrizione di Azure](role-assignments-portal-subscription-admin.md)
- [Aggiungere un'assegnazione di ruolo per un'identità gestita](role-assignments-portal-managed-identity.md)
- [Risolvere i problemi relativi a RBAC di Azure](troubleshooting.md)
