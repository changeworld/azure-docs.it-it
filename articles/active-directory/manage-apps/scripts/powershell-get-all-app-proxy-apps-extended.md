---
title: Esempio di PowerShell - Visualizzare le informazioni estese per le app di Application Proxy
description: Esempio di PowerShell che elenca tutte le applicazioni di Azure Active Directory (Azure AD) Application Proxy unitamente all'ID applicazione (AppId), al nome (DisplayName), all'URL esterno (ExternalUrl), all'URL interno (InternalUrl) e al tipo di autenticazione (ExternalAuthenticationType).
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 1b3234a13a0e3fac760a899ce66e7faa7e7b642c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377362"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Recuperare tutte le app di Application Proxy e visualizzare le informazioni estese

Questo esempio di script di PowerShell elenca informazioni su tutte le applicazioni Application Proxy Azure Active Directory (Azure AD), inclusi l'ID applicazione (AppId), il nome (DisplayName), l'URL esterno (ExternalUrl), l'URL interno (InternalUrl), il tipo di autenticazione (ExternalAuthenticationType), la modalità SSO e altre impostazioni.

La modifica del valore della variabile $ssoMode abilita un output filtrato in base alla modalità SSO. Altri dettagli sono documentati nello script.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Questo esempio richiede il [modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2) (AzureAD).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Recupera un'entità servizio. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Recupera un'applicazione Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Recupera un'applicazione configurata per Application Proxy in Azure AD. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](/powershell/azure/active-directory/overview).

Per altri esempi di PowerShell relativi ad Application Proxy, vedere [Esempi di Azure AD PowerShell per Azure AD Application Proxy](../application-proxy-powershell-samples.md).
