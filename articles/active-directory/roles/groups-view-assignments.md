---
title: Visualizzare i ruoli assegnati a un gruppo in Azure Active Directory | Microsoft Docs
description: Informazioni sul modo in cui i ruoli assegnati a un gruppo possono essere visualizzati usando Azure AD interfaccia di amministrazione. I gruppi di visualizzazione e i ruoli assegnati sono le autorizzazioni utente predefinite.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dac23897f8a17c9adb4ae78736a6a8afa85a18b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012004"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Visualizzare i ruoli assegnati a un gruppo in Azure Active Directory

Questa sezione descrive il modo in cui i ruoli assegnati a un gruppo possono essere visualizzati usando Azure AD interfaccia di amministrazione. I gruppi di visualizzazione e i ruoli assegnati sono le autorizzazioni utente predefinite.

1. Accedere al centro di [amministrazione Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con le credenziali di amministratore o non amministratore.

1. Selezionare il gruppo a cui si è interessati.

1. Selezionare **ruoli assegnati**. È ora possibile visualizzare tutti i ruoli di Azure AD assegnati a questo gruppo.

   ![Visualizza tutti i ruoli assegnati a un gruppo selezionato](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Utilizzo di PowerShell

### <a name="get-object-id-of-the-group"></a>Ottenere l'ID oggetto del gruppo

```powershell
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>Visualizzare l'assegnazione di ruolo a un gruppo

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Uso dell'API Microsoft Graph

### <a name="get-object-id-of-the-group"></a>Ottenere l'ID oggetto del gruppo

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>Ottenere le assegnazioni di ruolo a un gruppo

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi cloud per gestire le assegnazioni di ruoli](groups-concept.md)
- [Risoluzione dei problemi correlati ai ruoli assegnati ai gruppi cloud](groups-faq-troubleshooting.md)
