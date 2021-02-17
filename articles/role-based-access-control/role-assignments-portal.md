---
title: Assegnare i ruoli di Azure usando l'portale di Azure-RBAC di Azure
description: Informazioni su come concedere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando il portale di Azure e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: e25bbe4e1a96e4efaaa13732aea571d26d4b006e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555283"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Assegnare i ruoli di Azure usando il portale di Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Questo articolo descrive come assegnare i ruoli usando il portale di Azure.

Se è necessario assegnare ruoli di amministratore in Azure Active Directory, vedere [visualizzare e assegnare ruoli di amministratore in Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-identify-the-needed-scope"></a>Passaggio 1: identificare l'ambito necessario

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Per ulteriori informazioni sull'ambito, vedere [comprendere l'ambito](scope-overview.md).

![Livelli di ambito per il controllo degli accessi in base al ruolo di Azure](../../includes/role-based-access-control/media/scope-levels.png)

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella casella di ricerca nella parte superiore cercare l'ambito a cui si vuole concedere l'accesso. Ad esempio, cercare **gruppi di gestione**, **sottoscrizioni**, **gruppi di risorse** o una risorsa specifica.

    ![portale di Azure ricerca di un gruppo di risorse](./media/shared/rg-portal-search.png)

1. Fare clic sulla risorsa specifica per tale ambito.

    L'immagine seguente mostra un gruppo di risorse di esempio.

    ![Panoramica del gruppo di risorse](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Passaggio 2: aprire il riquadro Aggiungi assegnazione ruolo

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

## <a name="step-3-select-the-appropriate-role"></a>Passaggio 3: selezionare il ruolo appropriato

1. Nell'elenco **ruolo** cercare o scorrere per trovare il ruolo che si desidera assegnare.

    Per determinare il ruolo appropriato, è possibile passare il mouse sull'icona info per visualizzare una descrizione del ruolo. Per altre informazioni, vedere l'articolo [ruoli predefiniti di Azure](built-in-roles.md) .

   ![Selezione ruolo in Aggiungi assegnazione ruolo](./media/role-assignments-portal/add-role-assignment-role.png)

1. Fare clic per selezionare il ruolo.

## <a name="step-4-select-who-needs-access"></a>Passaggio 4: selezionare chi necessita di accesso

1. Nell'elenco **assegna accesso a** selezionare il tipo di entità di sicurezza a cui assegnare l'accesso.

    | Tipo | Descrizione |
    | --- | --- |
    | **Utente, gruppo o entità servizio** | Se si desidera assegnare il ruolo a un utente, a un gruppo o a un'entità servizio (applicazione), selezionare questo tipo. |
    | **Identità gestita assegnata dall'utente** | Se si desidera assegnare il ruolo a un' [identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/overview.md), selezionare questo tipo. |
    | *Identità gestita assegnata dal sistema* | Se si vuole assegnare il ruolo a un' [identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md), selezionare l'istanza del servizio di Azure in cui si trova l'identità gestita. |

   ![Selezionare il tipo di entità di sicurezza in Aggiungi assegnazione ruolo](./media/role-assignments-portal/add-role-assignment-type.png)

1. Se è stata selezionata un'identità gestita assegnata dall'utente o un'identità gestita assegnata dal sistema, selezionare la **sottoscrizione** in cui si trova l'identità gestita.

1. Nella sezione **Seleziona** cercare l'entità di sicurezza immettendo una stringa o scorrendo l'elenco.

   ![Selezionare un utente in Aggiungi assegnazione ruolo](./media/role-assignments-portal/add-role-assignment-user.png)

1. Dopo aver trovato l'entità di sicurezza, fare clic per selezionarla.

## <a name="step-5-assign-role"></a>Passaggio 5: assegnare un ruolo

1. Per assegnare il ruolo, fare clic su **Salva**.

   Dopo qualche istante, all'entità di sicurezza verrà assegnato il ruolo per l'ambito selezionato.

1. Nella scheda **assegnazioni di ruolo** verificare che nell'elenco sia visualizzata l'assegnazione di ruolo.

    ![Aggiunta dell'assegnazione di ruolo salvata](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un utente come amministratore di una sottoscrizione di Azure](role-assignments-portal-subscription-admin.md)
- [Rimuovere le assegnazioni di ruolo di Azure](role-assignments-remove.md)
- [Risolvere i problemi relativi a RBAC di Azure](troubleshooting.md)
