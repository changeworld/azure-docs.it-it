---
title: Assegnare un utente come amministratore di una sottoscrizione di Azure-controllo degli accessi in base al ruolo di Azure
description: Informazioni su come rendere un utente un amministratore di una sottoscrizione di Azure usando il portale di Azure e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556842"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Assegnare un utente come amministratore di una sottoscrizione di Azure

Per rendere un utente amministratore per una sottoscrizione di Azure, assegnargli il ruolo di [Proprietario](built-in-roles.md#owner) nell'ambito della sottoscrizione. Il ruolo proprietario concede all'utente l'accesso completo a tutte le risorse nella sottoscrizione, inclusa l'autorizzazione per concedere l'accesso ad altri utenti. Questi passaggi sono gli stessi di qualsiasi altra assegnazione di ruoli.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Passaggio 1: aprire la sottoscrizione

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella casella di ricerca nella parte superiore cercare sottoscrizioni.

    ![portale di Azure ricerca di un gruppo di risorse](./media/shared/sub-portal-search.png)

1. Fare clic sulla sottoscrizione che si vuole usare.

    Di seguito viene illustrata una sottoscrizione di esempio.

    ![Panoramica del gruppo di risorse](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Passaggio 2: aprire il riquadro Aggiungi assegnazione ruolo

Il **controllo di accesso (IAM)** è la pagina che in genere si usa per assegnare i ruoli per concedere l'accesso alle risorse di Azure. È noto anche come gestione delle identità e degli accessi e viene visualizzato in diverse posizioni nel portale di Azure.

1. Fare clic su **Controllo di accesso (IAM)**.

    Di seguito viene illustrato un esempio della pagina controllo di accesso (IAM) per una sottoscrizione.

    ![Pagina controllo di accesso (IAM) per un gruppo di risorse](./media/shared/sub-access-control.png)

1. Fare clic sulla scheda **assegnazioni di ruolo** per visualizzare le assegnazioni di ruolo in questo ambito.

1. Fare clic su **Aggiungi**  >  **Aggiungi assegnazione ruolo**.
   Se non si dispone delle autorizzazioni per assegnare ruoli, l'opzione Aggiungi assegnazione di ruolo verrà disabilitata.

   ![Menu Aggiungi assegnazione di ruolo](./media/shared/add-role-assignment-menu.png)

    Viene visualizzato il riquadro Aggiungi assegnazione di ruolo.

   ![Riquadro Aggiungi assegnazione di ruolo](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Passaggio 3: selezionare il ruolo proprietario

Il ruolo [proprietario](built-in-roles.md#owner) concede l'accesso completo per gestire tutte le risorse, inclusa la possibilità di assegnare i ruoli in controllo degli accessi in base al ruolo di Azure È necessario disporre di un massimo di 3 proprietari di sottoscrizioni per ridurre il rischio di violazione da parte di un proprietario compromesso.

- Nell'elenco **ruolo** selezionare il ruolo **proprietario** .

   ![Selezionare il ruolo proprietario nel riquadro Aggiungi assegnazione ruolo](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Passaggio 4: selezionare chi necessita di accesso

1. Nell'elenco **assegna accesso a** selezionare **utente, gruppo o entità servizio**.

1. Nella sezione **Seleziona** cercare l'utente immettendo una stringa o scorrendo l'elenco.

   ![Selezionare un utente in Aggiungi assegnazione ruolo](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Dopo aver trovato l'utente, fare clic per selezionarlo.

## <a name="step-5-assign-role"></a>Passaggio 5: assegnare un ruolo

1. Per assegnare il ruolo, fare clic su **Salva**.

   Dopo alcuni istanti, all'utente viene assegnato il ruolo nell'ambito selezionato.

1. Nella scheda **assegnazioni di ruolo** verificare che nell'elenco sia visualizzata l'assegnazione di ruolo.

    ![Aggiunta dell'assegnazione di ruolo salvata](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare i ruoli di Azure usando il portale di Azure](role-assignments-portal.md)
- [Elencare le assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-list-portal.md)
- [Organizzare le risorse con i gruppi di gestione di Azure ](../governance/management-groups/overview.md)
