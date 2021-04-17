---
title: 'Esempio di PowerShell: esportare app con segreti e certificati che scadono oltre la data richiesta Azure Active Directory tenant.'
description: Esempio di PowerShell che esporta tutte le app con segreti e certificati che scadono oltre la data richiesta per le app specificate nel tenant Azure Active Directory servizio.
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
ms.openlocfilehash: 692ab2cfd480fd15760c13c63922fe76d23058ea
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375390"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Esportare app con segreti e certificati che scadono oltre la data richiesta

Questo esempio di script di PowerShell esporta tutti i segreti e i certificati di registrazione delle app che scadono oltre un periodo necessario per le app specificate dalla directory in un file CSV in modo non interattivo.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script funziona in modo non interattivo. L'amministratore che lo usa dovrà modificare i valori nella sezione "#PARAMETERS TO CHANGE" con il proprio ID app, il segreto dell'applicazione, il nome del tenant, il periodo di scadenza delle credenziali delle app e il percorso in cui verrà esportato il file CSV.
Questo script usa il [flusso Client_Credential Oauth](../../develop/v2-oauth2-client-creds-grant-flow.md) La funzione "RefreshToken" compila il token di accesso in base ai valori dei parametri modificati dall'amministratore.

Il comando "Add-Member" è responsabile della creazione delle colonne nel file CSV.

| Comando | Note |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Invia richieste HTTP e HTTPS a una pagina Web o a un servizio Web. Analizza la risposta e restituisce raccolte di collegamenti, immagini e altri elementi HTML significativi. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure AD PowerShell, vedere la [panoramica del modulo di Azure AD PowerShell](/powershell/azure/active-directory/overview).

Per altri esempi di PowerShell per la gestione delle applicazioni, [Azure AD esempi di PowerShell per la gestione delle applicazioni](../app-management-powershell-samples.md).
