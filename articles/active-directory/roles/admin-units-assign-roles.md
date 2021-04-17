---
title: Assegnare ed elencare ruoli con ambito di unità amministrativa - Azure Active Directory | Microsoft Docs
description: Usare le unità amministrative per limitare l'ambito delle assegnazioni di ruolo in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24bf5df84015ada6b62c35fdd29571c66e06ebd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505259"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Assegnare ruoli con ambito a un'unità amministrativa

In Azure Active Directory (Azure AD), per un controllo amministrativo più granulare, è possibile assegnare gli utenti a un ruolo Azure AD con un ambito limitato a una o più unità amministrative.

Per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione a](admin-units-manage.md#get-started).

## <a name="available-roles"></a>Ruoli disponibili

Ruolo  |  Descrizione
----- |  -----------
Amministratore dell'autenticazione  |  Ha accesso per visualizzare, impostare e reimpostare le informazioni sul metodo di autenticazione solo per qualsiasi utente non amministratore nell'unità amministrativa assegnata.
Amministratore di gruppi  |  Può gestire tutti gli aspetti delle impostazioni dei gruppi e dei gruppi, ad esempio i criteri di denominazione e scadenza, solo nell'unità amministrativa assegnata.
Amministratore del supporto tecnico  |  Può reimpostare le password solo per gli amministratori non amministratori e del supporto help desk nell'unità amministrativa assegnata.
Amministratore licenze  |  Può assegnare, rimuovere e aggiornare le assegnazioni di licenza solo all'interno dell'unità amministrativa.
Amministratore password  |  Può reimpostare le password solo per gli utenti non amministratori e gli amministratori delle password all'interno dell'unità amministrativa assegnata.
Amministratore utenti  |  Può gestire tutti gli aspetti di utenti e gruppi, inclusa la reimpostazione delle password per gli amministratori limitati solo all'interno dell'unità amministrativa assegnata.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Entità di sicurezza che possono essere assegnate a un ruolo con ambito

Le entità di sicurezza seguenti possono essere assegnate a un ruolo con un ambito di unità amministrativa:

* Utenti
* Gruppi cloud assegnabili al ruolo (anteprima)
* Nome dell'entità servizio (SPN)

## <a name="assign-a-scoped-role"></a>Assegnare un ruolo con ambito

È possibile assegnare un ruolo con ambito usando il portale di Azure, PowerShell o Microsoft Graph.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nel portale di Azure passare a **Azure AD**.

1. Selezionare **Unità amministrative** e quindi selezionare l'unità amministrativa a cui si vuole assegnare un ambito del ruolo utente. 

1. Nel riquadro sinistro selezionare Ruoli e **amministratori per** elencare tutti i ruoli disponibili.

   ![Screenshot del riquadro "Ruolo e amministratori" per la selezione di un'unità amministrativa di cui assegnare l'ambito del ruolo.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Selezionare il ruolo da assegnare e quindi **selezionare Aggiungi assegnazioni.** 

1. Nel riquadro **Aggiungi assegnazioni** selezionare uno o più utenti da assegnare al ruolo.

   ![Selezionare il ruolo per l'ambito e quindi selezionare Aggiungi assegnazioni](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Per assegnare un ruolo in un'unità amministrativa usando Azure AD Privileged Identity Management (PIM), vedere Assegnare Azure AD [ruoli in PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Usare PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

È possibile modificare la sezione evidenziata in base alle esigenze per l'ambiente specifico.

### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Richiesta

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
Corpo

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Visualizzare un elenco degli amministratori con ambito in un'unità amministrativa

È possibile visualizzare un elenco di amministratori con ambito usando portale di Azure, PowerShell o Microsoft Graph.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile visualizzare tutte le assegnazioni di ruolo create con un ambito di unità amministrativa nella [sezione Unità amministrative di Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. Nel portale di Azure passare **a** Azure AD .

1. Nel riquadro sinistro selezionare **Unità** amministrative e quindi selezionare l'unità amministrativa per l'elenco di assegnazioni di ruolo da visualizzare. 

1. Selezionare **Ruoli e amministratori** e quindi aprire un ruolo per visualizzare le assegnazioni nell'unità amministrativa.

### <a name="use-powershell"></a>Usare PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

È possibile modificare la sezione evidenziata in base alle esigenze per l'ambiente specifico.

### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Richiesta

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

Corpo

```http
{}
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi cloud per gestire le assegnazioni di ruoli](groups-concept.md)
- [Risolvere i problemi relativi ai ruoli assegnati ai gruppi cloud](groups-faq-troubleshooting.md)
