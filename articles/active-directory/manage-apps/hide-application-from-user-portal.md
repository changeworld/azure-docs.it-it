---
title: Nascondi un'applicazione aziendale dall'esperienza dell'utente in Azure AD
description: Come nascondere un'applicazione aziendale dall'esperienza dell'utente in Azure Active Directory i pannelli di accesso o i Microsoft 365 di avvio.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8469b48b92f3f9a645a0c05441e6c1943b02e16f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258881"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Nascondi le applicazioni aziendali dagli utenti finali nel Azure Active Directory

Istruzioni per nascondere le applicazioni dal pannello app di utenti finali o dall'utilità di avvio Microsoft 365. Quando un'applicazione è nascosta, gli utenti continuano a disporre delle autorizzazioni relative all'applicazione. 

## <a name="prerequisites"></a>Prerequisiti

I privilegi di amministratore dell'applicazione sono necessari per nascondere un'applicazione dal pannello app e Microsoft 365 avvio.

Per nascondere tutte le applicazioni Microsoft 365 sono necessari i privilegi di amministratore globale.


## <a name="hide-an-application-from-the-end-user"></a>Nascondere un'applicazione per gli utenti finali
Usare la procedura seguente per nascondere un'applicazione dal pannello app e Microsoft 365 l'utilità di avvio dell'applicazione.

1.  Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale della directory.
2.  Selezionare **Azure Active Directory**.
3.  Selezionare **Applicazioni aziendali**. Verrà visualizzato il pannello **Applicazioni aziendali - Tutte le applicazioni**.
4.  In **Tipo di applicazione** selezionare **Applicazioni aziendali** se l'opzione non è già selezionata.
5.  Cercare l'applicazione che si desidera nascondere e fare clic su di essa.  Viene visualizzata la panoramica dell'applicazione.
6.  Scegliere **Proprietà**. 
7.  Nel campo **Visibile agli utenti?** fare clic su **No**.
8.  Fare clic su **Salva**.

> [!NOTE]
> Queste istruzioni sono valide solo per le applicazioni aziendali.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Usare Azure AD PowerShell per nascondere un'applicazione

Per nascondere un'applicazione dal pannello app, è possibile aggiungere manualmente il tag HideApp all'entità servizio per l'applicazione. Eseguire i seguenti comandi di [PowerShell per AzureAD](/powershell/module/azuread/#service_principals) per impostare la proprietà Visible dell'applicazione **per gli utenti?** su **No**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Nascondi applicazioni Microsoft 365 dal pannello app

Usare la procedura seguente per nascondere tutte le applicazioni Microsoft 365 dal pannello app. Le applicazioni continueranno a essere visibili nel portale di Office 365.

1.  Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale della directory.
2.  Selezionare **Azure Active Directory**.
3.  Selezionare **Utenti**.
4.  Selezionare **impostazioni utente**.
5.  In **Applicazioni aziendali** fare clic su **Gestire il modo in cui gli utenti finali avviano e visualizzano le applicazioni**.
6.  In **Gli utenti possono visualizzare le app di Office 365 solo nel portale di Office 365** fare clic su **Sì**.
7.  Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](./assign-user-or-group-access-portal.md)
* [Modificare il nome o il logo di un'app aziendale](./add-application-portal-configure.md)
