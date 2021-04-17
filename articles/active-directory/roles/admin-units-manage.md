---
title: Aggiungere e rimuovere unità amministrative - Azure Active Directory | Microsoft Docs
description: Usare le unità amministrative per limitare l'ambito delle autorizzazioni del ruolo in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fad9356d3379e76aa259d67711d18f14a4e266f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505276"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Gestire le unità amministrative in Azure Active Directory

Per un controllo amministrativo più granulare in Azure Active Directory (Azure AD), è possibile assegnare gli utenti a un ruolo Azure AD con un ambito limitato a una o più unità amministrative.

## <a name="get-started"></a>Introduzione

1. Per eseguire query dalle istruzioni seguenti tramite [Graph Explorer,](https://aka.ms/ge)eseguire le operazioni seguenti:

    a. Nel portale di Azure passare ad Azure AD. 
    
    b. Nell'elenco delle applicazioni selezionare **Graph explorer**.
    
    c. Nel riquadro **Autorizzazioni** selezionare Concedi **il consenso dell'amministratore per Graph explorer**.

    ![Screenshot che mostra il collegamento "Grant admin consent for Graph explorer" (Concedi il Graph explorer amministratore).](./media/admin-units-manage/select-graph-explorer.png)


1. Usare [Azure AD PowerShell.](https://www.powershellgallery.com/packages/AzureAD/)

## <a name="add-an-administrative-unit"></a>Aggiungere un'unità amministrativa

È possibile aggiungere un'unità amministrativa usando il portale di Azure o PowerShell.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nel portale di Azure passare ad Azure AD. Quindi, nel riquadro sinistro selezionare **Unità amministrative**.

    ![Screenshot del collegamento "Unità amministrative" in Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. Selezionare il **pulsante** Aggiungi nella parte superiore del riquadro e quindi nella casella **Nome** immettere il nome dell'unità amministrativa. Facoltativamente, aggiungere una descrizione dell'unità amministrativa.

    ![Screenshot che mostra il pulsante Aggiungi e la casella Nome per l'immissione del nome dell'unità amministrativa.](./media/admin-units-manage/add-new-admin-unit.png)

1. Selezionare il pulsante blu **Aggiungi** per finalizzare l'unità amministrativa.

### <a name="use-powershell"></a>Usare PowerShell

Installare [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) prima di provare a eseguire i comandi seguenti:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

È possibile modificare i valori racchiusi tra virgolette, in base alle esigenze.

### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Richiesta

```http
POST /administrativeUnits
```

Corpo

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Rimuovere un'unità amministrativa

In Azure AD è possibile rimuovere un'unità amministrativa che non è più necessaria come unità di ambito per i ruoli amministrativi.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nel portale di Azure passare a **Azure AD** e quindi selezionare **Unità amministrative**. 
1. Selezionare l'unità amministrativa da eliminare e quindi selezionare **Elimina.** 
1. Per confermare che si vuole eliminare l'unità amministrativa, selezionare **Sì.** L'unità amministrativa viene eliminata.

![Screenshot del pulsante Elimina dell'unità amministrativa e della finestra di conferma.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Usare PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

È possibile modificare i valori racchiusi tra virgolette, come richiesto per l'ambiente specifico.

### <a name="use-the-graph-api"></a>Usare l'API Graph

Richiesta

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Corpo

```http
{}
```

## <a name="next-steps"></a>Passaggi successivi

* [Gestire gli utenti in un'unità amministrativa](admin-units-add-manage-users.md)
* [Gestire i gruppi in un'unità amministrativa](admin-units-add-manage-groups.md)
