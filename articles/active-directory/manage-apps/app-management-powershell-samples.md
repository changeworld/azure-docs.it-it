---
title: Esempi di PowerShell per la gestione delle applicazioni Azure Active Directory
description: Questi esempi di PowerShell vengono usati per le app gestite nel tenant del Azure Active Directory. È possibile usare questi script di esempio per trovare informazioni sulla scadenza di segreti e certificati.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: f5f7ec8245a43440a400b9ca6b55bf1093eb62cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636189"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Esempi di Azure Active Directory PowerShell per la gestione delle applicazioni

La tabella seguente include collegamenti a esempi di script di PowerShell per la gestione delle applicazioni Azure AD. Questi esempi richiedono:
- [PowerShell per AzureAD V2 per il modulo Graph](/powershell/azure/active-directory/install-adv2) o,
- [AzureAD V2 PowerShell per la versione di anteprima del modulo Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), salvo diversa indicazione.

Per ulteriori informazioni sui cmdlet utilizzati in questi esempi, vedere [applicazioni](/powershell/module/azuread/#applications).

| Collegamento | Descrizione |
|---|---|
|**Script di gestione delle applicazioni**||
| [Esportare segreti e certificati (registrazioni per l'app)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Esportare i segreti e i certificati per le registrazioni di app in Azure Active Directory tenant. |
| [Esportare segreti e certificati (app aziendali)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Esportare i segreti e i certificati per le app aziendali in Azure Active Directory tenant. |
| [Esporta segreti e certificati in scadenza](scripts/powershell-export-apps-with-expriring-secrets.md) | Esportare le registrazioni di app con segreti e certificati in scadenza e i relativi proprietari nel tenant Azure Active Directory. |
| [Esporta segreti e certificati che scadono oltre la data richiesta](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Esporta le registrazioni di app con segreti e certificati che scadono oltre la data richiesta nel tenant Azure Active Directory. Viene usato il flusso di Client_Credentials OAuth non interattivo. |
