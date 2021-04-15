---
title: Informazioni sull'accesso Single Sign-On (SSO) basato su OIDC per le app in Azure Active Directory
description: Informazioni sull'accesso Single Sign-On (SSO) basato su OIDC per le app in Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: iangithinji
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: 990e0c09f8a49b83bc68d7123f5f8146a3551575
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374540"
---
# <a name="understand-oidc-based-single-sign-on"></a>Informazioni sull'accesso Single Sign-On basato su OIDC
Nella serie [di guide introduttive](view-applications-portal.md) sulla gestione delle applicazioni si è appreso come usare Azure AD come provider di identità (IdP) per un'applicazione. Questo articolo illustra in dettaglio le app che usano lo standard OpenID Connect per implementare l'accesso Single Sign-On. 

## <a name="before-you-begin"></a>Prima di iniziare
Il processo di aggiunta di un'app al tenant Azure Active Directory dipende dal tipo di accesso Single Sign-On implementato dall'applicazione. Per altre informazioni sulle opzioni di Single Sign-On disponibili per le app che possono usare Azure AD per la gestione delle identità, vedere [Opzioni di Single Sign-On.](sso-options.md) Questo articolo illustra le app basate su OIDC.


## <a name="basic-oidc-configuration"></a>Configurazione OIDC di base
Nella serie [di guide introduttive](add-application-portal-setup-oidc-sso.md)è presente un articolo sulla configurazione dell'accesso Single Sign-On. In questa unità si apprenderà come aggiungere un'app basata su OIDC al tenant di Azure.

L'aspetto interessante dell'aggiunta di un'app che usa lo standard OIDC per l'accesso Single Sign-On è che la configurazione è minima. Ecco un breve video che illustra come aggiungere un'app basata su OIDC al tenant.

Aggiunta di un'app basata su OIDC in Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Per altre informazioni sul consenso utente e amministratore, vedere [Informazioni sul consenso utente e amministratore](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Passaggi successivi

- [Serie di guide di avvio rapido sulla gestione delle applicazioni](add-application-portal-setup-oidc-sso.md)
- [Opzioni di accesso Single Sign-On](sso-options.md)
- [Procedura: Consentire l'accesso a qualsiasi utente di Azure Active Directory usando il modello di applicazione multi-tenant](../develop/howto-convert-app-to-be-multi-tenant.md)
