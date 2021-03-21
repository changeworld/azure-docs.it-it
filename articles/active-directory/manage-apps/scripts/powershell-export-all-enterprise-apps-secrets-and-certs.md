---
title: 'Esempio di PowerShell: esportare i segreti e i certificati per le app aziendali in Azure Active Directory tenant.'
description: Esempio di PowerShell che esporta tutti i segreti e i certificati per le app aziendali specificate nel tenant del Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 20caefe74a7c047fb8690bb1d9e6f4eb9da7e9b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635196"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Esportare i segreti e i certificati per le app aziendali
Questo esempio di script di PowerShell Esporta tutti i segreti, i certificati e i proprietari per le app aziendali specificate dalla directory in un file CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Per questo esempio è richiesto il [modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Spiegazione dello script

Il comando "Add-Member" è responsabile della creazione delle colonne nel file CSV.
È possibile modificare la variabile "$Path" direttamente in PowerShell, con un percorso di file CSV, nel caso in cui si preferisca che l'esportazione sia non interattiva.

| Comando | Note |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Recupera un'applicazione aziendale dalla directory. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Recupera i proprietari di un'applicazione aziendale dalla directory. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](/powershell/azure/active-directory/overview).

Per altri esempi di PowerShell per la gestione delle applicazioni, vedere [Azure ad esempi di PowerShell per la gestione delle applicazioni](../app-management-powershell-samples.md).
