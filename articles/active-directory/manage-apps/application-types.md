---
title: Visualizzazione delle app usando il tenant Azure Active Directory per la gestione delle identità
description: Informazioni su come visualizzare tutte le applicazioni che usano il tenant Azure Active Directory per la gestione delle identità.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: b11f2dab97b3c45502c6050e06f0f39312f8c3c0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378074"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Visualizzazione delle app usando il tenant Azure AD per la gestione delle identità
La [serie di guide introduttive sulla gestione delle](view-applications-portal.md) applicazioni illustra le nozioni di base. Viene illustrato come visualizzare tutte le app usando il tenant Azure AD per la gestione delle identità. Questo articolo approfondirà i tipi di app disponibili.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Perché viene visualizzata un'applicazione specifica nell'elenco di tutte le applicazioni?
Se filtrato per **Tutte le applicazioni,** **l'elenco Tutte le**  applicazioni mostra tutti gli oggetti entità servizio nel tenant. Gli oggetti entità servizio possono essere visualizzati in questo elenco in diversi modi:
- Quando si aggiunge un'applicazione dalla raccolta di applicazioni, incluse:
   - **Azure AD - Applicazioni aziendali** : app aggiunte al tenant usando **l'opzione** Applicazioni aziendali nel portale Azure AD aziendale. In genere le app integrate con lo standard SAML.
   - **Azure AD - Registrazioni app:** app aggiunte al tenant usando  l'opzione Registrazioni app nel portale Azure AD. App sviluppate in genere personalizzate che usano gli standard Open ID Connect e OAuth.
   - **Applicazioni del proxy di applicazione**: un'applicazione in esecuzione nell'ambiente locale per cui si vuole offrire l'accesso Single Sign-On sicuro all'esterno.
- Quando ci si iscrive o si accede a un'applicazione di terze parti integrata con Azure Active Directory. ad esempio [Smartsheet](https://app.smartsheet.com/b/home) o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- App Microsoft come Microsoft 365.
- Quando si aggiunge una nuova registrazione di applicazione creando un'applicazione personalizzata con il [Registro applicazioni](../develop/quickstart-register-app.md).
- Quando si aggiunge una nuova registrazione di applicazione creando un'applicazione personalizzata con il [portale di registrazione delle applicazioni V2.0](../develop/quickstart-register-app.md).
- Quando si aggiunge un'applicazione, lo sviluppo viene Visual Studio metodi di [](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) autenticazione ASP.NET o [Servizi connessi](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
- Quando si crea un oggetto entità servizio usando il [modulo Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).
- Quando un amministratore [consente a un'applicazione](../develop/howto-convert-app-to-be-multi-tenant.md) di usare i dati nel tenant.
- Quando un [utente consente a un'applicazione](../develop/howto-convert-app-to-be-multi-tenant.md) di usare i dati nel tenant.
- Quando si abilitano determinati servizi che archiviano dati nel tenant, Un esempio è la reimpostazione della password, che è modellata come un'entità servizio per archiviare in modo sicuro i criteri di reimpostazione password.

Per altre informazioni su come e perché le app vengono aggiunte alla directory, vedere Come [vengono aggiunte le](../develop/active-directory-how-applications-are-added.md)applicazioni Azure AD .

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](what-is-application-management.md)
