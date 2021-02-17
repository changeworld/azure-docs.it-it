---
title: Rimuovere le assegnazioni di ruolo di Azure-RBAC di Azure
description: Informazioni su come rimuovere l'accesso alle risorse di Azure per utenti, gruppi, entità servizio o identità gestite usando portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561506"
---
# <a name="remove-azure-role-assignments"></a>Rimuovere le assegnazioni di ruolo di Azure

Il [controllo degli accessi in base al ruolo di Azure](../../articles/role-based-access-control/overview.md) è il sistema di autorizzazione che si usa per gestire l'accesso alle risorse di Azure. Per rimuovere l'accesso da una risorsa di Azure, è necessario rimuovere un'assegnazione di ruolo. Questo articolo descrive come rimuovere le assegnazioni di ruoli usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.

## <a name="prerequisites"></a>Prerequisiti

Per rimuovere le assegnazioni di ruolo, è necessario disporre di:

- `Microsoft.Authorization/roleAssignments/delete` autorizzazioni, ad esempio [amministratore accesso utenti](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator) o [proprietario](../../articles/role-based-access-control/built-in-roles.md#owner)

## <a name="azure-portal"></a>Portale di Azure

1. Aprire **Controllo di accesso (IAM)** sull'ambito, come gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa, in cui si intende rimuovere l'accesso.

1. Fare clic sulla scheda **Assegnazioni di ruolo** per visualizzare tutte le assegnazioni di ruolo in questo ambito.

1. Nell'elenco delle assegnazioni di ruolo aggiungere un segno di spunta accanto all'entità di sicurezza con l'assegnazione di ruolo che si vuole rimuovere.

   ![Assegnazione di ruolo selezionata per la rimozione](./media/role-assignments-remove/rg-role-assignments-select.png)

1. Scegliere **Rimuovi**.

   ![Messaggio di rimozione assegnazione di ruolo](./media/role-assignments-remove/remove-role-assignment.png)

1. Nella finestra con il messaggio di rimozione dell'assegnazione di ruolo fare clic su **Sì**.

    Se viene visualizzato un messaggio in cui non è possibile rimuovere le assegnazioni di ruolo ereditate, si sta tentando di rimuovere un'assegnazione di ruolo in un ambito figlio. È necessario aprire il controllo di accesso (IAM) nell'ambito in cui è stato assegnato il ruolo e riprovare. Un modo rapido per aprire il controllo di accesso (IAM) nell'ambito corretto consiste nell'esaminare la colonna **ambito** e fare clic sul collegamento accanto a **(ereditato)**.

   ![Rimuovere un messaggio di assegnazione di ruolo per le assegnazioni di ruolo ereditate](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

In Azure PowerShell si rimuove un'assegnazione di ruolo usando [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

Nell'esempio seguente viene rimossa l'assegnazione di ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) dall'utente *patlong \@ contoso.com* nel gruppo di risorse *Pharma-Sales* :

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Rimuove il ruolo [lettore](built-in-roles.md#reader) dal gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito della sottoscrizione.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Rimuove il ruolo [Lettura fatturazione](built-in-roles.md#billing-reader) dall'utente *Alain \@ example.com* nell'ambito del gruppo di gestione.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Se viene visualizzato il messaggio di errore: "le informazioni fornite non sono mappate a un'assegnazione di ruolo", assicurarsi di specificare anche `-Scope` i `-ResourceGroupName` parametri o. Per altre informazioni, vedere [risolvere i problemi relativi a RBAC di Azure](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Nell'interfaccia della riga di comando di Azure è possibile rimuovere un'assegnazione di ruolo usando [AZ Role Assignment Delete](/cli/azure/role/assignment#az_role_assignment_delete).

Nell'esempio seguente viene rimossa l'assegnazione di ruolo [collaboratore macchina virtuale](built-in-roles.md#virtual-machine-contributor) dall'utente *patlong \@ contoso.com* nel gruppo di risorse *Pharma-Sales* :

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Rimuove il ruolo [lettore](built-in-roles.md#reader) dal gruppo *Ann Mack Team* con ID 22222222-2222-2222-2222-222222222222 in un ambito della sottoscrizione.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Rimuove il ruolo [Lettura fatturazione](built-in-roles.md#billing-reader) dall'utente *Alain \@ example.com* nell'ambito del gruppo di gestione.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>API REST

Nell'API REST rimuovere un'assegnazione di ruolo usando le [assegnazioni di ruolo-Delete](/rest/api/authorization/roleassignments/delete).

1. Ottenere l'identificatore di assegnazione di ruolo (GUID). Questo identificatore viene restituito quando si crea l'assegnazione di ruolo per la prima volta oppure è possibile ottenerlo elencando le assegnazioni di ruolo.

1. Iniziare con la richiesta seguente:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. All'interno dell'URI sostituire *{scope}* con l'ambito per la rimozione dell'assegnazione di ruolo.

    > [!div class="mx-tableFixed"]
    > | Ambito | Tipo |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Gruppo di gestione |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Risorsa |

1. Sostituire *{roleAssignmentId}* con l'identificatore GUID dell'assegnazione di ruolo.

La richiesta seguente rimuove l'assegnazione di ruolo specificata nell'ambito della sottoscrizione:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Il testo seguente è un esempio di output:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>Modello ARM

Non esiste un modo per rimuovere un'assegnazione di ruolo usando un modello di Azure Resource Manager (modello ARM). Per rimuovere un'assegnazione di ruolo, è necessario usare altri strumenti, ad esempio portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

## <a name="next-steps"></a>Passaggi successivi

- [Elencare le assegnazioni di ruolo di Azure usando il portale di Azure](role-assignments-list-portal.md)
- [Elencare le assegnazioni di ruolo di Azure usando Azure PowerShell](role-assignments-list-powershell.md)
- [Risolvere i problemi relativi a RBAC di Azure](troubleshooting.md)
