---
title: Disabilitare gli account di accesso utente per un'app aziendale in Azure AD
description: Come disabilitare un'applicazione aziendale in modo che gli utenti non possano accedervi in Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/12/2019
ms.author: iangithinji
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9af580ef8b3691a5f188ac15069dda00a5f5802
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374251"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Disabilitare gli accessi utente per un'app aziendale in Azure Active Directory

È facile disabilitare un'applicazione aziendale in modo che nessun utente possa accedere all'applicazione Azure Active Directory (Azure AD). Sono necessarie le autorizzazioni appropriate per gestire l'app aziendale. È anche necessario essere amministratore globale per la directory.

## <a name="how-do-i-disable-user-sign-ins"></a>Come è possibile disabilitare l'accesso degli utenti?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale per la directory.
1. Selezionare **Tutti i** servizi, **Azure Active Directory** nella casella di testo e quindi premere **INVIO.**
1. Nel riquadro **Azure Active Directory** directoryname _ (ovvero, il riquadro Azure AD per la directory che si  -   ***sta gestendo), selezionare _* Applicazioni aziendali**.
1. Nel **riquadro Applicazioni aziendali - Tutte le** applicazioni viene visualizzato un elenco delle app che è possibile gestire. Selezionare un'app.
1. Nel riquadro ***appname** _, ovvero il riquadro con il nome dell'app selezionata nel titolo, selezionare _*Proprietà**.
1. Nel riquadro ***nome app** _ *-* Proprietà * selezionare **No** per Abilitato **per l'accesso degli utenti?**.
1. Selezionare il comando **Salva** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Usare Azure AD PowerShell per disabilitare un'app non in elenco

Se si conosce l'AppId di un'app che non viene visualizzata nell'elenco app aziendali ( ad esempio, perché l'app è stata eliminata o l'entità servizio non è ancora stata creata perché l'app è pre-autorizzata da Microsoft), è possibile creare manualmente l'entità servizio per l'app e quindi disabilitarla usando il cmdlet di [AzureAD PowerShell](/powershell/module/azuread/New-AzureADServicePrincipal).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](./assign-user-or-group-access-portal.md)
* [Modificare il nome o il logo di un'app aziendale](./add-application-portal-configure.md)
