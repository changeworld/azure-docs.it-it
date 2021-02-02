---
title: Informazioni sulle Single Sign-On basate su OIDC (SSO) per le app in Azure Active Directory
description: Informazioni sulle Single Sign-On basate su OIDC (SSO) per le app in Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: ffaa55d4aa482e8f0eda93b1b32db6310d17e2a3
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255250"
---
# <a name="understand-oidc-based-single-sign-on"></a>Informazioni Single Sign-On basate su OIDC
Nella [serie di guide introduttive](view-applications-portal.md) sulla gestione delle applicazioni si è appreso come usare Azure ad come provider di identità (IDP) per un'applicazione. Questo articolo illustra in modo più dettagliato le app che usano lo standard OpenID Connect per implementare Single Sign-On. 

## <a name="before-you-begin"></a>Prima di iniziare
Il processo di aggiunta di un'app al tenant di Azure Active Directory dipende dal tipo di Single Sign-On implementata dall'applicazione. Per altre informazioni sulle opzioni di Single Sign-On disponibili per le app che possono usare Azure AD per la gestione delle identità, vedere [Opzioni di Single Sign-on](sso-options.md). Questo articolo illustra le app basate su OIDC.


## <a name="basic-oidc-configuration"></a>Configurazione OIDC di base
Nella [serie di guide introduttive](add-application-portal-setup-oidc-sso.md)è disponibile un articolo sulla configurazione di Single Sign-on. Si apprenderà come aggiungere un'app basata su OIDC al tenant di Azure.

L'aspetto interessante dell'aggiunta di un'app che usa lo standard OIDC per Single Sign-On è che la configurazione è minima. Ecco un breve video che illustra come aggiungere un'app basata su OIDC al tenant.

Aggiunta di un'app basata su OIDC in Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Per altre informazioni sul consenso utente e amministratore, vedere [Informazioni sul consenso utente e amministratore](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Passaggi successivi

- [Serie di guide di avvio rapido sulla gestione delle applicazioni](add-application-portal-setup-oidc-sso.md)
- [Opzioni di accesso Single Sign-On](sso-options.md)
- [Procedura: Consentire l'accesso a qualsiasi utente di Azure Active Directory usando il modello di applicazione multi-tenant](../develop/howto-convert-app-to-be-multi-tenant.md)
