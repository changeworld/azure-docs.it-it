---
title: Elencare gruppi di connettori di Azure AD Application Proxy
description: Esempio di PowerShell che elenca tutti i gruppi di connettori di Azure Active Directory (Azure AD) Application Proxy con le applicazioni assegnate.
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
ms.openlocfilehash: 1adb576e751d93dc4a9d240546325d7e671d0892
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377011"
---
# <a name="get-all-application-proxy-apps-and-list-by-connector-group"></a>Recuperare tutte le app di Application Proxy ed elencarle in base al gruppo di connettori

Questo script di esempio di PowerShell visualizza informazioni su tutti i gruppi di connettori di Azure Active Directory (Azure AD) Application Proxy con le applicazioni assegnate.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Per questo esempio è richiesto il [modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-by-connectorgroup.ps1 "Get all Application Proxy Connector groups with the assigned applications")]

## <a name="script-explanation"></a>Spiegazione dello script

| Comando | Note |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Recupera un'entità servizio. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Recupera un'applicazione Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Recupera un'applicazione configurata per Application Proxy in Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Recupera un elenco di tutti i gruppi di connettori oppure, se specificati, i dettagli del gruppo di connettori indicato. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](/powershell/azure/active-directory/overview).

Per altri esempi di PowerShell relativi ad Application Proxy, vedere [Esempi di Azure AD PowerShell per Azure AD Application Proxy](../application-proxy-powershell-samples.md).
