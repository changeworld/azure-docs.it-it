---
title: Aggiungere un utente di Collaborazione B2B a un ruolo - Azure Active Directory
description: Aggiungere un utente guest a un ruolo in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a931fe43b6be406f0b2a4b8193c1631261f7e5
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575667"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Concedere autorizzazioni agli utenti di organizzazioni partner nel tenant di Azure Active Directory

Gli utenti di Collaborazione B2B di Azure Active Directory (Azure AD) vengono aggiunti come utenti guest alla directory e le autorizzazioni guest nella directory sono limitate per impostazione predefinita. Può essere necessario definire alcuni utenti guest per assegnare ruoli con privilegi elevati nell'organizzazione. Per supportare la definizione di ruoli con privilegi più elevati, gli utenti guest possono essere aggiunti a qualsiasi ruolo, in base alle esigenze dell'organizzazione.

Se un ruolo della directory viene assegnato a un utente guest, all'utente guest verranno concesse autorizzazioni aggiuntive incluse nel ruolo, incluse le autorizzazioni di lettura di base. Vedere [Azure AD ruoli predefiniti.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference)

## <a name="default-role"></a>Ruolo predefinito

![Screenshot che mostra il ruolo della directory predefinito](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Ruolo Amministratore globale

![Screenshot che mostra il ruolo di amministratore globale](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Ruolo Amministratore con limitazioni

![Screenshot che mostra il ruolo di amministratore limitato](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Proprietà dell'utente di Collaborazione B2B](user-properties.md)
