---
title: 'Esempio di PowerShell: esportare app con segreti e certificati che scadono oltre la data richiesta nel tenant Azure Active Directory.'
description: Esempio di PowerShell che esporta tutte le app con segreti e certificati che scadono oltre la data richiesta per le app specificate nel tenant di Azure Active Directory.
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
ms.openlocfilehash: 3572f481cc2cbcb1df73b33eb2543e32256ad9fb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584328"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Esporta app con segreti e certificati che scadono oltre la data richiesta

Questo esempio di script di PowerShell Esporta tutti i segreti e i certificati delle app che scadono oltre la data richiesta per le app specificate dalla directory in un file CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Per questo esempio è richiesto il [modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Spiegazione dello script

Il comando "Add-Member" è responsabile della creazione delle colonne nel file CSV.
È possibile modificare la variabile "$Path" direttamente in PowerShell, con un percorso di file CSV, nel caso in cui si preferisca che l'esportazione sia non interattiva.

| Comando | Note |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Recupera un'applicazione dalla directory. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Recupera i proprietari di un'applicazione dalla directory. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](/powershell/azure/active-directory/overview).

Per altri esempi di PowerShell per la gestione delle applicazioni, vedere [Azure ad esempi di PowerShell per la gestione delle applicazioni](../app-management-powershell-samples.md).
