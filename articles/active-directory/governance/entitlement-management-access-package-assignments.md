---
title: Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso Azure AD gestione entitlement - Azure Active Directory
description: Informazioni su come visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso in Azure Active Directory entitlement.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e00fe3761824462252ce4984beb754385f3eca9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532148"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Visualizzare, aggiungere e rimuovere assegnazioni per un pacchetto di accesso nella Azure AD entitlement

Nella Azure AD entitlement è possibile vedere chi è stato assegnato ai pacchetti di accesso, i relativi criteri e lo stato. Se un pacchetto di accesso dispone di criteri appropriati, è anche possibile assegnare direttamente l'utente a un pacchetto di accesso. Questo articolo descrive come visualizzare, aggiungere e rimuovere assegnazioni per i pacchetti di accesso.

## <a name="prerequisites"></a>Prerequisiti

Per usare Azure AD entitlement e assegnare gli utenti ai pacchetti di accesso, è necessario disporre di una delle licenze seguenti:

- Azure AD Premium P2
- Licenza di Enterprise Mobility + Security (EMS) E5

## <a name="view-who-has-an-assignment"></a>Visualizzare chi ha un'assegnazione

**Ruolo prerequisito:** amministratore globale, Amministratore utenti, Proprietario del catalogo, Gestione pacchetti di accesso o Gestione assegnazioni pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Pacchetti di accesso** e quindi aprire il pacchetto di accesso.

1. Fare **clic su Assegnazioni** per visualizzare un elenco di assegnazioni attive.

    ![Elenco di assegnazioni a un pacchetto di accesso](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Fare clic su un'assegnazione specifica per visualizzare altri dettagli.

1. Per visualizzare un elenco di assegnazioni per cui non è stato eseguito correttamente il provisioning di tutti i ruoli delle risorse, fare clic sullo stato del filtro e selezionare **Recapito.**

    È possibile visualizzare altri dettagli sugli errori di recapito individuando la richiesta corrispondente dell'utente nella **pagina** Richieste.

1. Per visualizzare le assegnazioni scadute, fare clic sullo stato del filtro e selezionare **Scaduto.**

1. Per scaricare un file CSV dell'elenco filtrato, fare clic su **Scarica**.

### <a name="viewing-assignments-programmatically"></a>Visualizzazione delle assegnazioni a livello di codice

È anche possibile recuperare le assegnazioni in un pacchetto di accesso usando Microsoft Graph.  Un utente con un ruolo appropriato con un'applicazione che dispone dell'autorizzazione delegata può chiamare l'API per elencare `EntitlementManagement.ReadWrite.All` [accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true).

## <a name="directly-assign-a-user"></a>Assegnare direttamente un utente

In alcuni casi, potrebbe essere necessario assegnare direttamente utenti specifici a un pacchetto di accesso in modo che gli utenti non devono eseguire il processo di richiesta del pacchetto di accesso. Per assegnare direttamente gli utenti, il pacchetto di accesso deve avere un criterio che consenta le assegnazioni dirette dell'amministratore.

**Ruolo prerequisito:** amministratore globale, Amministratore utente, Proprietario del catalogo, Gestione pacchetti di Accesso o Gestione assegnazioni pacchetti di Access

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Accedi ai pacchetti** e quindi aprire il pacchetto di accesso.

1. Nel menu a sinistra fare clic **su Assegnazioni**.

1. Fare **clic su Nuova assegnazione** per aprire Aggiungi utente per accedere al pacchetto.

    ![Assegnazioni- Aggiungere l'utente per accedere al pacchetto](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Fare **clic su Aggiungi** utenti per selezionare gli utenti a cui si vuole assegnare questo pacchetto di accesso.

1. **Nell'elenco Seleziona criteri** selezionare un criterio da cui verranno regolate e monitorate le richieste e il ciclo di vita futuri degli utenti. Se si desidera che gli utenti selezionati abbia impostazioni dei criteri diverse, è possibile fare clic **su Crea** nuovo criterio per aggiungere un nuovo criterio.

1. Impostare la data e l'ora di inizio e fine dell'assegnazione degli utenti selezionati. Se non viene specificata una data di fine, verranno usate le impostazioni del ciclo di vita dei criteri.

1. Facoltativamente, fornire una giustificazione per l'assegnazione diretta per la conservazione dei record.

1. Fare **clic su** Aggiungi per assegnare direttamente gli utenti selezionati al pacchetto di accesso.

    Dopo alcuni istanti, fare clic **su Aggiorna** per visualizzare gli utenti nell'elenco Assegnazioni.

### <a name="directly-assigning-users-programmatically"></a>Assegnazione diretta degli utenti a livello di codice

È anche possibile assegnare direttamente un utente a un pacchetto di accesso usando Microsoft Graph.  Un utente con un ruolo appropriato con un'applicazione con l'autorizzazione delegata può chiamare l'API per creare `EntitlementManagement.ReadWrite.All` [un oggetto accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true).

## <a name="remove-an-assignment"></a>Rimuovere un'assegnazione

**Ruolo prerequisito:** amministratore globale, Amministratore utenti, Proprietario del catalogo, Gestione pacchetti di accesso o Gestione assegnazioni pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Pacchetti di accesso** e quindi aprire il pacchetto di accesso.

1. Nel menu a sinistra fare clic **su Assegnazioni.**
 
1. Fare clic sulla casella di controllo accanto all'utente di cui si desidera rimuovere l'assegnazione dal pacchetto di accesso. 

1. Fare clic **sul pulsante** Rimuovi nella parte superiore del riquadro sinistro. 
 
    ![Assegnazioni - Rimuovere l'utente dal pacchetto di accesso](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Verrà visualizzata una notifica che informa che l'assegnazione è stata rimossa. 

## <a name="next-steps"></a>Passaggi successivi

- [Modificare la richiesta e le impostazioni per un pacchetto di accesso](entitlement-management-access-package-request-policy.md)
- [Visualizzare report e log](entitlement-management-reports.md)