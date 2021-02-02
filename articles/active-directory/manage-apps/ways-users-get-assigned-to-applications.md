---
title: Informazioni sul modo in cui gli utenti vengono assegnati alle app in Azure Active Directory
description: Informazioni sul modo in cui gli utenti vengono assegnati a un'app che usa Azure Active Directory per la gestione delle identità.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 161df0446c9478ca0f2b135c1e426f3786b164fc
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257441"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Informazioni sul modo in cui gli utenti vengono assegnati alle app in Azure Active Directory
Questo articolo consente di comprendere come vengono assegnati gli utenti a un'applicazione nel tenant.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Come vengono assegnati gli utenti a un'applicazione in Azure AD?
Un utente deve prima essere assegnato a un'applicazione per potervi accedere. L'assegnazione può essere eseguita da un amministratore, da un delegato aziendale o, talvolta, dall'utente stesso. Di seguito vengono descritti i modi in cui gli utenti possono essere assegnati alle applicazioni:

*  Un amministratore [assegna un utente](./assign-user-or-group-access-portal.md) direttamente all'applicazione
*  Un amministratore [assegna un gruppo](./assign-user-or-group-access-portal.md), del quale l'utente è membro, all'applicazione, inclusi:
    * Un gruppo che è stato sincronizzato da locale
    * Un gruppo di sicurezza statico creato nel cloud
    * Un [gruppo di sicurezza dinamico](../enterprise-users/groups-dynamic-membership.md) creato nel cloud
    * Un gruppo Microsoft 365 creato nel cloud
    * Il gruppo [Tutti gli utenti](../fundamentals/active-directory-groups-create-azure-portal.md)
*  Un amministratore abilita [l'accesso alle applicazioni self-service](./manage-self-service-access.md) per consentire a un utente di aggiungere un'applicazione usando la funzionalità app [personali](../user-help/my-apps-portal-end-user-access.md) **Aggiungi app** **senza approvazione aziendale**
*  Un amministratore abilita [l'accesso alle applicazioni self-service](./manage-self-service-access.md) per consentire a un utente di aggiungere un'applicazione usando la funzionalità app [personali](../user-help/my-apps-portal-end-user-access.md) **Aggiungi app** , ma solo **con l'approvazione precedente da un set selezionato di responsabili approvazione aziendali**
*  Un amministratore abilita la [Gestione gruppi self-service](../enterprise-users/groups-self-service-management.md) per consentire a un utente di partecipare a un gruppo assegnato a un'applicazione **senza l'approvazione aziendale**
*  Un amministratore abilita la [Gestione gruppi self-service](../enterprise-users/groups-self-service-management.md) per consentire a un utente di partecipare a un gruppo assegnato a un'applicazione, ma solo **con l'approvazione previa di una serie di responsabili approvazione aziendali selezionati**
*  Un amministratore assegna una licenza direttamente a un utente per un'applicazione di prima entità, ad esempio [Microsoft 365](https://products.office.com/)
*  Un amministratore assegna una licenza a un gruppo di cui l'utente è membro in un'applicazione di prima entità, ad esempio [Microsoft 365](https://products.office.com/)
*  Un [amministratore dà il consenso a un'applicazione](../develop/howto-convert-app-to-be-multi-tenant.md) per essere usata da tutti gli utenti e quindi un utente effettua l'accesso all'applicazione
* Un utente [dà il consenso a un'applicazione](../develop/howto-convert-app-to-be-multi-tenant.md) da solo effettuando l'accesso all'applicazione

## <a name="next-steps"></a>Passaggi successivi
* [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
* [Cos'è la gestione delle applicazioni?](what-is-application-management.md)
* [Che cos'è Single Sign-On?](what-is-single-sign-on.md)