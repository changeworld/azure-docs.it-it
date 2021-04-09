---
title: Esempio di PowerShell - Elencare utenti e gruppi per l'app di Application Proxy
description: Esempio di PowerShell che elenca tutti gli utenti e i gruppi assegnati a un'applicazione specifica di Azure Active Directory (Azure AD) Application Proxy.
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
ms.openlocfilehash: 540df7cbe2d35cd705e9d2b88f66a82604951957
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548544"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Visualizzare utenti e gruppi assegnati a un'applicazione di Application Proxy

Questo script di esempio di PowerShell elenca gli utenti e i gruppi assegnati a un'applicazione specifica di Azure Active Directory (Azure AD) Application Proxy.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Per questo esempio è richiesto il [modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| Ottiene un utente. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| Recupera un gruppo. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Recupera un'entità servizio. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | Recupera l'assegnazione di ruolo applicazione di un utente. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | Recupera l'assegnazione di ruolo applicazione di un gruppo. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](/powershell/azure/active-directory/overview).

Per altri esempi di PowerShell relativi ad Application Proxy, vedere [Esempi di Azure AD PowerShell per Azure AD Application Proxy](../application-proxy-powershell-samples.md).
