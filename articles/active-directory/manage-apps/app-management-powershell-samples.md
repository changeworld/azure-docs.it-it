---
title: Esempi di PowerShell per la gestione Azure Active Directory applicazioni
description: Questi esempi di PowerShell vengono usati per le app gestite nel tenant Azure Active Directory tenant. È possibile usare questi script di esempio per trovare informazioni sulla scadenza su segreti e certificati.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 6b6314935bfafc2fe6288c30619e1d01242a991d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378823"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory esempi di PowerShell per la gestione delle applicazioni

La tabella seguente include collegamenti ad esempi di script di PowerShell per Azure AD gestione delle applicazioni. Questi esempi richiedono:
- Il [modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2) o,
- Versione [di anteprima del modulo AzureAD V2 PowerShell per Graph,](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)se non diversamente specificato.

Per altre informazioni sui cmdlet usati in questi esempi, vedere [Applicazioni](/powershell/module/azuread/#applications).

| Collegamento | Descrizione |
|---|---|
|**Script di gestione delle applicazioni**||
| [Esportare segreti e certificati (registrazioni dell'app)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Esportare segreti e certificati per le registrazioni delle app Azure Active Directory tenant. |
| [Esportare segreti e certificati (app aziendali)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Esportare segreti e certificati per le app aziendali nel tenant Azure Active Directory aziendale. |
| [Esportare certificati e segreti in scadenza](scripts/powershell-export-apps-with-expriring-secrets.md) | Esportare le registrazioni delle app con i segreti e i certificati in scadenza e i relativi proprietari nel tenant Azure Active Directory tenant. |
| [Esportare segreti e certificati che scadono oltre la data richiesta](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Esportare le registrazioni delle app con segreti e certificati che scadono oltre la data richiesta Azure Active Directory tenant. In questo modo viene utilizzato il flusso Client_Credentials Oauth non interattivo. |
