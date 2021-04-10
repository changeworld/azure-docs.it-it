---
title: Assegnare ruoli Azure AD agli utenti-Azure Active Directory
description: Informazioni su come concedere l'accesso agli utenti in Azure Active Directory assegnando ruoli di Azure AD.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466645"
---
# <a name="assign-azure-ad-roles-to-users"></a>Assegnare ruoli di Azure AD agli utenti

È ora possibile visualizzare e gestire tutti i membri dei ruoli di amministratore nell'interfaccia di amministrazione di Azure AD. Se si gestiscono frequentemente le assegnazioni di ruolo, questa esperienza risulterà particolarmente interessante. Questo articolo descrive come assegnare ruoli Azure AD usando l'interfaccia di amministrazione di Azure AD.

## <a name="assign-a-role"></a>Assegnare un ruolo

1. Accedere al centro di [amministrazione di Azure ad](https://aad.portal.azure.com) con autorizzazioni di amministratore globale o di amministratore del ruolo con privilegi.

1. Selezionare **Azure Active Directory**.

1. Selezionare **ruoli e amministratori** per visualizzare l'elenco di tutti i ruoli disponibili.

    ![Screenshot della pagina ruoli e amministratori](./media/manage-roles-portal/roles-and-administrators.png)

1. Selezionare un ruolo per visualizzare le relative assegnazioni.

    Per individuare il ruolo necessario, Azure AD possibile visualizzare i subset dei ruoli in base alle categorie di ruoli. Estrarre il filtro dei **tipi** per visualizzare solo i ruoli nel tipo selezionato.

1. Selezionare **Aggiungi assegnazioni** e quindi selezionare gli utenti che si desidera assegnare a questo ruolo.

    Se viene visualizzato un elemento diverso dall'immagine seguente, leggere la nota in [Privileged Identity Management (PIM)](#privileged-identity-management-pim) per verificare se si usa PIM.

    ![elenco di autorizzazioni per un ruolo di amministratore](./media/manage-roles-portal/add-assignments.png)

1. Selezionare **Aggiungi** per assegnare il ruolo.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

È possibile selezionare **Gestisci in PIM** per funzionalità di gestione aggiuntive usando [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Gli amministratori dei ruoli con privilegi possono trasformare le assegnazioni "permanenti" (sempre attive nel ruolo) in "idonee" (nel ruolo solo con privilegi elevati). Se non si dispone di Privileged Identity Management, è comunque possibile selezionare **Gestisci in PIM** per iscriversi a una versione di valutazione. Privileged Identity Management richiede un [piano di licenza di Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![Screenshot che mostra la pagina "amministratori utente-assegnazioni" con l'azione "Gestisci in PIM" selezionata](./media/manage-roles-portal/member-list-pim.png)

Se l'utente è un amministratore globale o un amministratore di ruoli con privilegi, può aggiungere o rimuovere facilmente i membri, filtrare l'elenco o selezionare un membro per visualizzare i ruoli assegnati attivi.

> [!Note]
> Se si dispone di una licenza Azure AD Premium P2 e si usano già Privileged Identity Management, tutte le attività di gestione dei ruoli vengono eseguite in Privileged Identity Management e non in Azure AD.
>
> ![Azure AD ruoli gestiti in PIM per gli utenti che usano già PIM e hanno una licenza Premium P2](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Passaggi successivi

* Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Per ulteriori informazioni sui ruoli, vedere [Azure ad ruoli predefiniti](permissions-reference.md).
* Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
