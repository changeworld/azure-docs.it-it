---
title: Elencare le assegnazioni di ruolo di Azure tramite l'interfaccia della riga di comando di Azure - Controllo degli accessi in base al ruolo di Azure
description: Informazioni su come determinare a quali risorse gli utenti, i gruppi, le entità servizio o le identità gestite hanno accesso usando l'interfaccia della riga di comando di Azure e il controllo degli accessi in base al ruolo di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2d30571b68ba7e38e9960d1e434cf7844f6be852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780102"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Elencare le assegnazioni di ruolo di Azure tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] Questo articolo descrive come elencare le assegnazioni di ruolo usando l'interfaccia della riga di comando di Azure.

> [!NOTE]
> Se l'organizzazione ha esternalizzato le funzioni di gestione a un provider di servizi che usa la gestione risorse delegate di [Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)le assegnazioni di ruolo autorizzate da tale provider di servizi non verranno visualizzate qui.

## <a name="prerequisites"></a>Prerequisiti

- [Bash in Azure Cloud Shell](../cloud-shell/overview.md) o nell'interfaccia della riga [di comando di Azure](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Elencare i ruoli assegnati a un utente

Per elencare le assegnazioni di ruolo per un utente specifico, usare [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --assignee {assignee}
```

Per impostazione predefinita, verranno visualizzate solo le assegnazioni di ruolo per la sottoscrizione corrente. Per visualizzare le assegnazioni di ruolo per la sottoscrizione corrente e di seguito, aggiungere il `--all` parametro . Per visualizzare le assegnazioni di ruolo ereditate, aggiungere il `--include-inherited` parametro .

Nell'esempio seguente vengono elencate le assegnazioni di ruolo assegnate direttamente *all'utente contoso.com patlong: \@*

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>Elencare le assegnazioni di ruolo per un gruppo di risorse

Per elencare le assegnazioni di ruolo esistenti nell'ambito di un gruppo di risorse, [usare az role assignment list:](/cli/azure/role/assignment#az_role_assignment_list)

```azurecli
az role assignment list --resource-group {resourceGroup}
```

L'esempio seguente elenca le assegnazioni di ruolo per il gruppo di *risorse pharma-sales:*

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>Elencare i ruoli assegnati a una sottoscrizione

Per elencare tutte le assegnazioni di ruolo nell'ambito di una sottoscrizione, [usare az role assignment list](/cli/azure/role/assignment#az_role_assignment_list). Per ottenere l'ID sottoscrizione, è  possibile trovarlo nel pannello Sottoscrizioni nel portale di Azure oppure è possibile usare [az account list](/cli/azure/account#az_account_list).

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

Esempio:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>Elencare le assegnazioni di ruolo per un gruppo di gestione

Per elencare tutte le assegnazioni di ruolo nell'ambito di un gruppo di gestione, [usare az role assignment list](/cli/azure/role/assignment#az_role_assignment_list). Per ottenere l'ID del gruppo di  gestione, è possibile trovarlo nel pannello Gruppi di gestione nel portale di Azure oppure è possibile usare [az account management-group list](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

Esempio:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Elencare le assegnazioni di ruolo per un'identità gestita

1. Ottenere l'ID entità di sicurezza dell'identità gestita assegnata dal sistema o dall'utente.

    Per ottenere l'ID entità di un'identità gestita assegnata dall'utente, è possibile usare [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) o [az identity list](/cli/azure/identity#az_identity_list).

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    Per ottenere l'ID entità di un'identità gestita assegnata dal sistema, è possibile usare [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list).

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. Per elencare le assegnazioni di ruolo, usare [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list).

    Per impostazione predefinita, verranno visualizzate solo le assegnazioni di ruolo per la sottoscrizione corrente. Per visualizzare le assegnazioni di ruolo per la sottoscrizione corrente e di seguito, aggiungere il `--all` parametro . Per visualizzare le assegnazioni di ruolo ereditate, aggiungere il `--include-inherited` parametro .

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure tramite l'interfaccia della riga di comando di Azure](role-assignments-cli.md)