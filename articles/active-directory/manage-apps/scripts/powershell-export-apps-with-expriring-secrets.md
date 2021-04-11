---
title: Esempio di PowerShell-esportare app con segreti e certificati in scadenza nel tenant Azure Active Directory.
description: Esempio di PowerShell che esporta tutte le app con segreti e certificati scaduti per le app specificate nel tenant del Azure Active Directory.
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
ms.openlocfilehash: def9b55a1d873cccda5d1c48921e3f098beeced1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149716"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Esportare app con segreti e certificati in scadenza

Questo esempio di script di PowerShell Esporta tutte le registrazioni di app con segreti scaduti, certificati e proprietari per le app specificate dalla directory in un file CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Per questo esempio è richiesto il [modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) o la [versione di anteprima del modulo AzureAD V2 PowerShell per Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Spiegazione dello script

Lo script può essere usato direttamente senza alcuna modifica. All'amministratore verrà richiesto di specificare la data di scadenza e se si desidera visualizzare i segreti o i certificati già scaduti.

Il comando "Add-Member" è responsabile della creazione delle colonne nel file CSV.
Il comando "New-Object" crea un oggetto da usare per le colonne nell'esportazione di file CSV.
È possibile modificare la variabile "$Path" direttamente in PowerShell, con un percorso di file CSV, nel caso in cui si preferisca che l'esportazione sia non interattiva.

| Comando | Note |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Recupera un'applicazione dalla directory. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Recupera i proprietari di un'applicazione dalla directory. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](/powershell/azure/active-directory/overview).

Per altri esempi di PowerShell per la gestione delle applicazioni, vedere [Azure ad esempi di PowerShell per la gestione delle applicazioni](../app-management-powershell-samples.md).
