---
title: Assegnare Azure AD ruoli predefiniti agli utenti - Azure Active Directory | Microsoft Docs
description: Istruzioni su come assegnare ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f49e15984b7a673de1e7d1607f4802c17ebef4e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531851"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Assegnazione di ruoli di amministratore e senza privilegi di amministratore agli utenti con Azure Active Directory

In Azure Active Directory (Azure AD), se uno degli utenti deve disporre dell'autorizzazione per gestire le risorse Azure AD, è necessario assegnarle a un ruolo che fornisce le autorizzazioni necessarie. Per informazioni sui ruoli che gestiscono le risorse di Azure e sui ruoli che gestiscono le risorse Azure AD, vedere Ruoli di amministratore della sottoscrizione classica, Ruoli di Azure e ruoli Azure AD [ruolo.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

Per altre informazioni sui ruoli di Azure AD disponibili, vedere Assegnazione di ruoli [di amministratore in Azure Active Directory](../roles/permissions-reference.md). Per aggiungere utenti, vedere [Aggiungere nuovi utenti Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Assegnare ruoli

Un modo comune per assegnare Azure AD ruoli a un utente è nella **pagina Ruoli assegnati** per un utente. È anche possibile configurare l'idoneità dell'utente per l'elevazione just-in-time in un ruolo usando Privileged Identity Management (PIM). Per altre informazioni su come usare PIM, [vedere](../privileged-identity-management/index.yml)Privileged Identity Management .

Se un ruolo della directory viene assegnato a un utente guest, all'utente guest verranno concesse autorizzazioni aggiuntive incluse nel ruolo, incluse le autorizzazioni di lettura di base. Vedere [Azure AD ruoli predefiniti.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference)

> [!Note]
> Se si dispone di un piano Azure AD Premium licenza P2 e si usa già [](../roles/manage-roles-portal.md)PIM, tutte le attività di gestione dei ruoli vengono eseguite nell'Privileged Identity Management. Questa funzionalità è attualmente limitata all'assegnazione di un solo ruolo alla volta. Attualmente non è possibile selezionare più ruoli e assegnarli tutti contemporaneamente a un utente.
>
> ![Azure AD ruoli gestiti in PIM per gli utenti che usano già PIM e hanno una licenza P2 Premium](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Assegnare un ruolo a un utente

1. Passare al [portale di Azure](https://portal.azure.com/) e accedere usando un account amministratore globale per la directory.

2. Cercare e selezionare **Azure Active Directory**.

      ![Ricerca di Azure Active Directory nel portale di Azure](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Selezionare **Utenti**.

4. Cercare e selezionare l'utente che riceve l'assegnazione di ruolo. Ad esempio, _Alain Charon_.

      ![Pagina Tutti gli utenti: selezionare l'utente](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Nella pagina **Alain Charon - Profile (Alain Charon - Profilo)** selezionare **Assigned roles (Ruoli assegnati).**

    Verrà **visualizzata la pagina Alain Charon - Ruoli amministrativi.**

6. Selezionare **Aggiungi assegnazioni,** selezionare il ruolo da assegnare ad Alain ( ad esempio, Amministratore _applicazione_) e quindi scegliere **Seleziona**.

    ![Pagina Ruoli assegnati- che mostra il ruolo selezionato](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Il ruolo Amministratore applicazione viene assegnato ad Alain Charon e viene visualizzato nella **pagina Alain Charon - Ruoli amministrativi.**

## <a name="remove-a-role-assignment"></a>Rimuovere un'assegnazione di ruolo

Se è necessario rimuovere l'assegnazione di ruolo da un utente, è anche possibile farlo dalla pagina **Alain Charon - Ruoli amministrativi.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Per rimuovere un'assegnazione di ruolo a un utente

1. Selezionare **Azure Active Directory**, **Utenti** e quindi cercare e selezionare l'utente per il quale si vuole rimuovere l'assegnazione del ruolo. Ad esempio, _Alain Charon_.

2. Selezionare **Ruoli assegnati,** amministratore **dell'applicazione** e quindi Selezionare **Rimuovi assegnazione.**

    ![Pagina Ruoli assegnati, che mostra il ruolo selezionato e l'opzione di rimozione](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Il ruolo Amministratore applicazione viene rimosso da Alain Charon e non viene più visualizzato nella **pagina Alain Charon - Ruoli amministrativi.**

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o eliminare utenti](add-users-azure-active-directory.md)

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Aggiungere utenti guest da un'altra directory](../external-identities/what-is-b2b.md)

Altre attività di gestione degli utenti che è possibile estrarre sono disponibili nella [documentazione Azure Active Directory sulla gestione degli utenti.](../enterprise-users/index.yml)
