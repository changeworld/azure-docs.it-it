---
title: Aggiungere, rimuovere ed elencare gruppi in un'unità amministrativa - Azure Active Directory | Microsoft Docs
description: Gestire i gruppi e le relative autorizzazioni di ruolo in un'unità amministrativa in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 03/10/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad8cce8375ecd670a481541a091e36aacb41240
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505293"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Aggiungere e gestire gruppi in un'unità amministrativa in Azure Active Directory

In Azure Active Directory (Azure AD), è possibile aggiungere gruppi a un'unità amministrativa per un ambito amministrativo di controllo più granulare.

Per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione.](admin-units-manage.md#get-started)

## <a name="add-groups-to-an-administrative-unit"></a>Aggiungere gruppi a un'unità amministrativa

È possibile aggiungere gruppi a un'unità amministrativa usando portale di Azure, PowerShell o Microsoft Graph.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile assegnare a un'unità amministrativa solo singoli gruppi. Non è possibile assegnare gruppi come operazione in blocco. Nel portale di Azure, è possibile assegnare un gruppo a un'unità amministrativa in uno dei due modi seguenti:

* Nel **riquadro** Gruppi:

  1. Nel portale di Azure passare **a** Azure AD .
  1. Selezionare **Gruppi** e quindi selezionare il gruppo da assegnare all'unità amministrativa. 
  1. Nel riquadro sinistro selezionare **Unità amministrative per** visualizzare un elenco delle unità amministrative a cui è assegnato il gruppo. 

     ![Screenshot del collegamento "Assegna a unità amministrativa" nel riquadro "Unità amministrative".](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Selezionare **Assegna a unità amministrativa**.
  1. Nel riquadro destro selezionare l'unità amministrativa.

* Nel riquadro **Unità amministrative Tutti**  >  **i** gruppi:

  1. Nel portale di Azure passare **a** Azure AD .
  
  1. Nel riquadro sinistro selezionare **Unità amministrative** e quindi Tutti **i gruppi**. 
     Tutti i gruppi già assegnati all'unità amministrativa vengono visualizzati nel riquadro di destra. 

  1. Nel riquadro **Gruppi** selezionare **Aggiungi.**
    Nel riquadro destro sono elencati tutti i gruppi disponibili nell'Azure AD lavoro. 

     ![Screenshot del pulsante "Aggiungi" per l'aggiunta di un gruppo a un'unità amministrativa.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Selezionare uno o più gruppi da assegnare all'unità amministrativa e quindi selezionare il **pulsante** Seleziona.

### <a name="use-powershell"></a>Usare PowerShell

Nell'esempio seguente usare il `Add-AzureADMSAdministrativeUnitMember` cmdlet per aggiungere il gruppo all'unità amministrativa. L'ID oggetto dell'unità amministrativa e l'ID oggetto del gruppo da aggiungere vengono utilizzati come argomenti. Modificare la sezione evidenziata in base alle esigenze per l'ambiente specifico.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Eseguire i comandi seguenti:

Richiesta

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Corpo

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

Esempio

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Visualizzare un elenco di gruppi in un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nel portale di Azure passare a **Azure AD**.

1. Nel riquadro sinistro selezionare **Unità** amministrative e quindi selezionare l'unità amministrativa di cui si desidera visualizzare i gruppi. Per impostazione predefinita, **nel riquadro** sinistro è selezionato Tutti gli utenti. 

1. Nel riquadro sinistro selezionare **Gruppi.** Nel riquadro destro viene visualizzato un elenco di gruppi membri dell'unità amministrativa selezionata.

   ![Screenshot del riquadro "Gruppi" che mostra un elenco di gruppi in un'unità amministrativa.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Usare PowerShell

Per visualizzare un elenco di tutti i membri dell'unità amministrativa, eseguire il comando seguente: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id
```

Per visualizzare tutti i gruppi membri dell'unità amministrativa, usare il frammento di codice seguente:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Eseguire il comando seguente:

Richiesta

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Corpo

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Visualizzare un elenco di unità amministrative per un gruppo

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nel portale di Azure passare **a** Azure AD .

1. Nel riquadro sinistro selezionare Gruppi **per** visualizzare un elenco di gruppi.

1. Selezionare un gruppo per aprire il profilo del gruppo. 

1. Nel riquadro sinistro selezionare Unità amministrative **per** elencare tutte le unità amministrative di cui il gruppo è membro.

   ![Screenshot del riquadro "Unità amministrative", che visualizza un elenco di unità amministrative a cui è assegnato un gruppo.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>Usare PowerShell

Eseguire il comando seguente:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Eseguire il comando seguente:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Rimuovere un gruppo da un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile rimuovere un gruppo da un'unità amministrativa nel portale di Azure in uno dei due modi seguenti:

- Rimuoverlo da una panoramica del gruppo:

  1. Nel portale di Azure passare **a** Azure AD .
  1. Nel riquadro sinistro selezionare **Gruppi** e quindi aprire il profilo per il gruppo da rimuovere da un'unità amministrativa.
  1. Nel riquadro sinistro selezionare Unità amministrative **per** elencare tutte le unità amministrative a cui è assegnato il gruppo. 
  1. Selezionare l'unità amministrativa da cui si vuole rimuovere il gruppo, quindi selezionare **Rimuovi da unità amministrativa**.

     ![Screenshot del riquadro "Unità amministrative", che visualizza un elenco di gruppi assegnati all'unità amministrativa selezionata.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Rimuoverlo da un'unità amministrativa:

  1. Nel portale di Azure passare **a** Azure AD .
  1. Nel riquadro sinistro selezionare **Unità amministrative** e quindi selezionare l'unità amministrativa a cui è assegnato il gruppo.
  1. Nel riquadro sinistro selezionare Gruppi **per** elencare tutti i gruppi assegnati all'unità amministrativa.
  1. Selezionare il gruppo da rimuovere e quindi **selezionare Rimuovi gruppi**.

    ![Screenshot del riquadro "Gruppi", che visualizza un elenco dei gruppi in un'unità amministrativa.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Usare PowerShell

Eseguire il comando seguente:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Eseguire il comando seguente:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un'unità amministrativa](admin-units-assign-roles.md)
- [Gestire gli utenti in un'unità amministrativa](admin-units-add-manage-users.md)
