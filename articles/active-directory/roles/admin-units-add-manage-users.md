---
title: Aggiungere, rimuovere ed elencare gli utenti in un'unità amministrativa - Azure Active Directory | Microsoft Docs
description: Gestire gli utenti e le relative autorizzazioni di ruolo in un'unità amministrativa in Azure Active Directory
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
ms.openlocfilehash: 101f1452f547f195288e2d22bc516880100c7735
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496827"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Aggiungere e gestire gli utenti in un'unità amministrativa in Azure Active Directory

In Azure Active Directory (Azure AD), è possibile aggiungere utenti a un'unità amministrativa per un ambito amministrativo di controllo più granulare.

Per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione.](admin-units-manage.md#get-started)

## <a name="add-users-to-an-administrative-unit"></a>Aggiungere utenti a un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile assegnare gli utenti alle unità amministrative singolarmente o come operazione in blocco.

- Assegnare singoli utenti da un profilo utente:

   1. Accedere all'interfaccia [di Azure AD con](https://portal.azure.com) autorizzazioni di amministratore dei ruoli con privilegi.

   1. Selezionare **Utenti** e quindi, per aprire il profilo dell'utente, selezionare l'utente da assegnare a un'unità amministrativa.
   
   1. Selezionare **Unità amministrative**. 
   
   1. Per assegnare l'utente a una  o più unità amministrative, selezionare Assegna a unità amministrativa e quindi nel riquadro destro selezionare le unità amministrative a cui si vuole assegnare l'utente.

       ![Screenshot del riquadro "Unità amministrative" per l'assegnazione di un utente a un'unità amministrativa.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Assegnare singoli utenti da un'unità amministrativa:

   1. Accedere all'interfaccia [di Azure AD con](https://portal.azure.com) autorizzazioni di amministratore dei ruoli con privilegi.
   1. Selezionare **Unità amministrative** e quindi selezionare l'unità amministrativa a cui assegnare l'utente.
   1. Selezionare **Tutti gli utenti,** selezionare **Aggiungi** membro e quindi nel riquadro Aggiungi membro selezionare uno o più utenti da assegnare all'unità amministrativa. 

        ![Screenshot del riquadro "Utenti" dell'unità amministrativa per l'assegnazione di un utente a un'unità amministrativa.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Assegnare gli utenti come operazione in blocco:

   1. Accedere all'interfaccia [di Azure AD con](https://portal.azure.com) autorizzazioni di amministratore dei ruoli con privilegi.

   1. Selezionare **Unità amministrative**.

   1. Selezionare l'unità amministrativa a cui si desidera aggiungere utenti.

   1. Selezionare **Utenti -** Attività in  >  **blocco**- Aggiungi membri in  >  **blocco**. È quindi possibile scaricare il modello con valori delimitati da virgole (CSV) e modificare il file. Il formato è semplice e richiede l'aggiunta di un singolo nome dell'entità utente in ogni riga. Quando il file è pronto, salvarlo in un percorso appropriato e quindi caricarlo come parte di questo passaggio.

      ![Screenshot del riquadro "Utenti" per l'assegnazione di utenti a un'unità amministrativa come operazione in blocco.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Usare PowerShell

In PowerShell usare il `Add-AzureADAdministrativeUnitMember` cmdlet nell'esempio seguente per aggiungere l'utente all'unità amministrativa. L'ID oggetto dell'unità amministrativa a cui si vuole aggiungere l'utente e l'ID oggetto dell'utente da aggiungere vengono utilizzati come argomenti. Modificare la sezione evidenziata in base alle esigenze per l'ambiente specifico.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Sostituire il segnaposto con le informazioni di test ed eseguire il comando seguente:

Richiesta

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Corpo

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

Esempio

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Visualizzare un elenco di unità amministrative per un utente

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Nel portale di Azure è possibile aprire il profilo di un utente seguendo questa procedura:

1. Passare a **Azure AD** e quindi selezionare **Utenti.**

1. Selezionare l'utente di cui si vuole visualizzare il profilo.

1. Selezionare **Unità amministrative** per visualizzare l'elenco delle unità amministrative a cui è stato assegnato l'utente.

   ![Screenshot delle unità amministrative a cui è stato assegnato un utente.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Usare PowerShell

Eseguire il comando seguente:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Per impostazione predefinita, `Get-AzureADAdministrativeUnitMember` restituisce solo 100 membri di un'unità amministrativa. Per recuperare più membri, è possibile aggiungere `"-All $true"` .

### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Sostituire il segnaposto con le informazioni di test ed eseguire il comando seguente:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Rimuovere un singolo utente da un'unità amministrativa

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile rimuovere un utente da un'unità amministrativa in uno dei due modi seguenti: 

* Nel portale di Azure passare **a** Azure AD e quindi selezionare **Utenti**. 
  1. Selezionare l'utente per aprire il profilo dell'utente. 
  1. Selezionare l'unità amministrativa da cui si vuole rimuovere l'utente e quindi **selezionare Rimuovi da unità amministrativa**.

     ![Screenshot che mostra come rimuovere un utente da un'unità amministrativa dal riquadro del profilo dell'utente.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Nel portale di Azure passare **a** Azure AD e quindi selezionare **Unità amministrative**.
  1. Selezionare l'unità amministrativa da cui si vuole rimuovere l'utente. 
  1. Selezionare l'utente e quindi Selezionare **Rimuovi membro**.
  
     ![Screenshot che mostra come rimuovere un utente a livello di unità amministrativa.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Usare PowerShell

Eseguire il comando seguente:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Usare Microsoft Graph

Sostituire i segnaposto con le informazioni di test ed eseguire il comando seguente:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Rimuovere più utenti come operazione in blocco

Per rimuovere più utenti da un'unità amministrativa, eseguire le operazioni seguenti:

1. Nel portale di Azure passare **a** Azure AD .

1. Selezionare **Unità amministrative** e quindi selezionare l'unità amministrativa da cui si vogliono rimuovere gli utenti. 

1. Selezionare **Rimozione in blocco dei** membri e quindi scaricare il modello CSV che verrà utilizzato per elencare gli utenti da rimuovere.

   ![Screenshot che mostra il collegamento "Rimozione in blocco dei membri" nel riquadro "Utenti".](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Modificare il modello CSV scaricato con le voci utente pertinenti. Non rimuovere le prime due righe del modello. Aggiungere un nome dell'entità utente (UPN) in ogni riga.

   ![Screenshot di un file CSV modificato per la rimozione di utenti da un'unità amministrativa in blocco.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Salvare le modifiche, caricare il file e quindi selezionare **Invia.**

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un'unità amministrativa](admin-units-assign-roles.md)
- [Aggiungere gruppi a un'unità amministrativa](admin-units-add-manage-groups.md)
