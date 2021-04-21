---
title: Come trovare l'ID tenant - Azure Active Directory
description: Istruzioni su come trovare e Azure Active Directory'ID tenant in una sottoscrizione di Azure esistente.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: cba823775849fdad8407c7bb697a53761e8ccbcd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764352"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Come trovare l'ID Azure Active Directory tenant

Le sottoscrizioni di Azure hanno una relazione di trust con Azure Active Directory (Azure AD). Azure AD è attendibile per autenticare utenti, servizi e dispositivi per la sottoscrizione. A ogni sottoscrizione è associato un ID tenant ed è possibile trovare l'ID tenant per la sottoscrizione in diversi modi.

## <a name="find-tenant-id-through-the-azure-portal"></a>Trovare l'ID tenant tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
 
1. Selezionare **Azure Active Directory**.

1. Selezionare **Proprietà**.

1. Scorrere quindi verso il basso fino al **campo ID tenant.** L'ID tenant sarà nella casella .

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory - Proprietà - ID tenant - Campo ID tenant":::

## <a name="find-tenant-id-with-powershell"></a>Trovare l'ID tenant con PowerShell

È anche possibile trovare il tenant a livello di codice. Per trovare l'ID tenant con Azure PowerShell, usare il cmdlet `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Per altre informazioni, vedere questo Azure PowerShell cmdlet per [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Trovare l'ID tenant con l'interfaccia della riga di comando
Se si vuole usare un'interfaccia della riga di comando per trovare l'ID tenant, è possibile farlo con l'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o [Microsoft 365'interfaccia della riga di comando.](https://pnp.github.io/cli-microsoft365/) 

Per l'interfaccia della riga di comando di Azure, usare uno dei comandi **az login**, **az account list** o **az account tenant list,** come illustrato nell'esempio seguente. Si noti **la proprietà tenantId** per ogni sottoscrizione nell'output di ogni comando.

```azurecli-interactive
az login
az account list
az account tenant list
```

Per altre informazioni, vedere [az login](/cli/azure/reference-index#az_login) command reference, [az account](/cli/azure/ext/account/account) command reference o [az account tenant](/cli/azure/ext/account/account/tenant) command reference.


Per Microsoft 365'interfaccia della riga di comando, usare **l'ID tenant del** cmdlet, come illustrato nell'esempio seguente:
 
```cli
m365 tenant id get
```

Per altre informazioni, vedere le informazioni di riferimento Microsoft 365 [comando get dell'ID](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) tenant.


## <a name="next-steps"></a>Passaggi successivi

- Per creare un nuovo tenant Azure AD, vedere [Avvio rapido: Creare un nuovo tenant in Azure Active Directory](active-directory-access-create-new-tenant.md).

- Per informazioni su come associare o aggiungere una sottoscrizione a un tenant, vedere Associare o aggiungere una sottoscrizione di [Azure al tenant Azure Active Directory.](active-directory-how-subscriptions-associated-directory.md)

- Per informazioni su come trovare l'ID oggetto, vedere [Trovare l'ID oggetto utente.](/partner-center/find-ids-and-domain-names#find-the-user-object-id)
