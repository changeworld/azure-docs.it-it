---
title: Esempio di PowerShell - Visualizzare le informazioni estese per le app di Application Proxy
description: Esempio di PowerShell che elenca tutte le applicazioni di Azure Active Directory (Azure AD) Application Proxy unitamente all'ID applicazione (AppId), al nome (DisplayName), all'URL esterno (ExternalUrl), all'URL interno (InternalUrl) e al tipo di autenticazione (ExternalAuthenticationType).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: ccd0c7be7fd0dd533028faa0dc2bbdad30d74c79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258694"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Recuperare tutte le app di Application Proxy e visualizzare le informazioni estese

Questo esempio di script di PowerShell elenca le informazioni su tutte le applicazioni proxy di applicazione Azure Active Directory (Azure AD), tra cui ID applicazione (AppId), nome (DisplayName), URL esterno (ExternalUrl), URL interno (InternalUrl), tipo di autenticazione (ExternalAuthenticationType), modalità SSO e altre impostazioni.

La modifica del valore della variabile $ssoMode Abilita un output filtrato in base alla modalità SSO. Ulteriori dettagli sono documentati nello script.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Questo esempio richiede il [modulo AzureAD V2 PowerShell for Graph](/powershell/azure/active-directory/install-adv2) (AzureAD).

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
