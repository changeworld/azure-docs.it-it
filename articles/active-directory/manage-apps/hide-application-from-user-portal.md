---
title: Nascondere un'applicazione aziendale dall'esperienza utente in Azure AD
description: Come nascondere un'applicazione aziendale dall'esperienza dell'utente nei Azure Active Directory di accesso o nelle Microsoft 365 di avvio.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90f3e3aeb1d68c6c6e6eeea29c04ff7880dccd3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374200"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Nascondere le applicazioni aziendali agli utenti finali in Azure Active Directory

Istruzioni su come nascondere le applicazioni dal pannello MyApps o dall'utilità di avvio Microsoft 365 utenti finali. Quando un'applicazione è nascosta, gli utenti continuano a disporre delle autorizzazioni relative all'applicazione. 

## <a name="prerequisites"></a>Prerequisiti

I privilegi di amministratore dell'applicazione sono necessari per nascondere un'applicazione dal pannello MyApps e Microsoft 365 di avvio.

amministratore globale necessari per nascondere tutte le Microsoft 365 applicazioni.


## <a name="hide-an-application-from-the-end-user"></a>Nascondere un'applicazione per gli utenti finali
Usare la procedura seguente per nascondere un'applicazione nel pannello MyApps e Microsoft 365 di avvio dell'applicazione.

1.  Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale della directory.
2.  Selezionare **Azure Active Directory**.
3.  Selezionare **Applicazioni aziendali**. Verrà visualizzato il pannello **Applicazioni aziendali - Tutte le applicazioni**.
4.  In **Tipo di applicazione** selezionare **Applicazioni aziendali** se l'opzione non è già selezionata.
5.  Cercare l'applicazione che si desidera nascondere e fare clic su di essa.  Viene visualizzata la panoramica dell'applicazione.
6.  Scegliere **Proprietà**. 
7.  Nel campo **Visibile agli utenti?** fare clic su **No**.
8.  Fare clic su **Salva**.

> [!NOTE]
> Queste istruzioni si applicano solo alle applicazioni aziendali.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Usare Azure AD PowerShell per nascondere un'applicazione

Per nascondere un'applicazione dal pannello MyApps, è possibile aggiungere manualmente il tag HideApp all'entità servizio per l'applicazione. Eseguire i comandi [di AzureAD PowerShell](/powershell/module/azuread/#service_principals) seguenti per impostare la proprietà **Visible to Users?** dell'applicazione su **No.** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Nascondere Microsoft 365 applicazioni nel pannello MyApps

Usare la procedura seguente per nascondere tutte Microsoft 365 applicazioni nel pannello MyApps. Le applicazioni continueranno a essere visibili nel portale di Office 365.

1.  Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale della directory.
2.  Selezionare **Azure Active Directory**.
3.  Selezionare **Utenti**.
4.  Selezionare **Impostazioni utente.**
5.  In **Applicazioni aziendali** fare clic su **Gestire il modo in cui gli utenti finali avviano e visualizzano le applicazioni**.
6.  In **Gli utenti possono visualizzare le app di Office 365 solo nel portale di Office 365** fare clic su **Sì**.
7.  Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](./assign-user-or-group-access-portal.md)
* [Modificare il nome o il logo di un'app aziendale](./add-application-portal-configure.md)
