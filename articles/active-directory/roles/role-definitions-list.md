---
title: Elencare le definizioni dei ruoli Azure AD-Azure AD
description: Informazioni su come elencare i ruoli predefiniti e personalizzati di Azure.
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
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467888"
---
# <a name="list-azure-ad-role-definitions"></a>Elencare le definizioni dei ruoli Azure AD

Una definizione di ruolo è una raccolta di autorizzazioni che è possibile eseguire, ad esempio la lettura, la scrittura e l'eliminazione. generalmente chiamata ruolo. Azure Active Directory ha più di 60 ruoli predefiniti oppure è possibile creare ruoli personalizzati. Se ci si chiede "quali sono i ruoli effettivamente?", è possibile visualizzare un elenco dettagliato delle autorizzazioni per ognuno dei ruoli.

Questo articolo descrive come elencare i Azure AD ruoli predefiniti e personalizzati insieme alle relative autorizzazioni.

## <a name="list-all-roles"></a>Elencare tutti i ruoli

1. Accedere al centro di [amministrazione Azure ad](https://aad.portal.azure.com) e selezionare **Azure Active Directory**.

1. Selezionare **ruoli e amministratori** per visualizzare l'elenco di tutti i ruoli disponibili.

    ![elenco dei ruoli nel portale di Azure AD](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. A destra, selezionare i puntini di sospensione e quindi **Descrizione** per visualizzare l'elenco completo delle autorizzazioni per un ruolo.

    La pagina include collegamenti alla documentazione pertinente per semplificare la gestione dei ruoli.

    ![Screenshot che mostra la pagina "Global Administrator-Description".](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Passaggi successivi

* Condividere le informazioni nel [forum di Azure AD sui ruoli amministrativi](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Per altre informazioni sui ruoli e l'assegnazione del ruolo di amministratore, vedere l'articolo sull'[assegnazione dei ruoli di amministratore](permissions-reference.md).
* Per le autorizzazioni utente predefinite, vedere un [confronto tra le autorizzazioni predefinite per gli utenti guest e quelle per gli utenti membro](../fundamentals/users-default-permissions.md).
