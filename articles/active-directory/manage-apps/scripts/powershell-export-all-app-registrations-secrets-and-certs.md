---
title: Esempio di PowerShell - Esportare segreti e certificati per le registrazioni di app Azure Active Directory tenant.
description: Esempio di PowerShell che esporta tutti i segreti e i certificati per le registrazioni di app specificate nel tenant Azure Active Directory servizio.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: b5cbb6b3843e81d9265405dcea24a092e57bf65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377044"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Esportare segreti e certificati per le registrazioni di app

Questo esempio di script di PowerShell esporta tutti i segreti e i certificati per le registrazioni di app specificate dalla directory in un file CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Per questo esempio è richiesto il [modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Spiegazione dello script

Il comando "Add-Member" è responsabile della creazione delle colonne nel file CSV.
È possibile modificare la variabile "$Path" direttamente in PowerShell, con un percorso di file CSV, nel caso in cui si preferisca che l'esportazione sia non interattiva.

| Comando | Note |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Recupera un'applicazione dalla directory. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Recupera i proprietari di un'applicazione dalla directory. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](/powershell/azure/active-directory/overview).

Per altri esempi di PowerShell per la gestione delle applicazioni, [Azure AD esempi di PowerShell per la gestione delle applicazioni.](../app-management-powershell-samples.md)
