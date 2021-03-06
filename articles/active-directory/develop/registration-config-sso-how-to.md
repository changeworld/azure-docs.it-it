---
title: Configurare Single Sign-On applicazioni
description: Come configurare l'accesso Single Sign-on per un'applicazione personalizzata che si sta sviluppando e registrando con Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 60a07145e38c19f577ad9864efb599e7b244aa83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653712"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Come configurare Single Sign-On per un'applicazione

L'abilitazione dell'accesso Single Sign-on (SSO) federato nell'applicazione avviene automaticamente quando si esegue la federazione tramite Azure AD per OpenID Connect, SAML 2.0 o WS-Fed. Se gli utenti finali devono eseguire l'accesso nonostante dispongano già di una sessione esistente con Azure AD, è probabile che l'applicazione non sia configurata correttamente.

* Se si usa ADAL/MSAL, accertarsi di avere impostato **PromptBehavior** su **Auto** anziché su **Sempre**.

* Se si sta creando un'applicazione per dispositivi mobili, potrebbe essere necessaria della configurazione aggiuntiva per abilitare l'accesso Single Sign-On negoziato o non negoziato.

Per Android, vedere [Abilitare l'accesso Single Sign-On tra app in Android](../azuread-dev/howto-v1-enable-sso-android.md).<br>

Per iOS, vedere [Abilitare l'accesso Single Sign-On tra app in iOS](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Passaggi successivi

[Azure AD SSO](../manage-apps/what-is-single-sign-on.md)<br>

[Abilitare l'accesso Single Sign-On tra app in Android](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Abilitare l'accesso Single Sign-On tra app in iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Integrazione di applicazioni con Azure Active Directory](./quickstart-register-app.md)<br>

[Autorizzazioni e consenso in Microsoft Identity Platform](./v2-permissions-and-consent.md)<br>

[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)