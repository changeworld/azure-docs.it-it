---
title: Applicare un blocco Azure Resource Manager a un account di archiviazione
titleSuffix: Azure Storage
description: Informazioni su come applicare un blocco Azure Resource Manager a un account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9d80c0b8d4d913322c47d1ad278d6dbc033d2409
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620060"
---
# <a name="apply-an-azure-resource-manager-lock-to-a-storage-account"></a>Applicare un blocco Azure Resource Manager a un account di archiviazione

Microsoft consiglia di bloccare tutti gli account di archiviazione con un blocco Azure Resource Manager per evitare l'eliminazione accidentale o dannosa dell'account di archiviazione. Esistono due tipi di blocchi di risorse Azure Resource Manager:

- Un blocco **CannotDelete** impedisce agli utenti di eliminare un account di archiviazione, ma consente di leggere e modificare la configurazione.
- Un blocco **ReadOnly** impedisce agli utenti di eliminare un account di archiviazione o di modificarne la configurazione, ma consente di leggere la configurazione.

Per ulteriori informazioni sui blocchi di Azure Resource Manager, vedere [bloccare le risorse per impedire le modifiche](../../azure-resource-manager/management/lock-resources.md).

> [!IMPORTANT]
> Il blocco di un account di archiviazione non protegge i dati all'interno di tale account dall'aggiornamento o dall'eliminazione.

## <a name="configure-an-azure-resource-manager-lock"></a>Configurare un blocco di Azure Resource Manager

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per configurare un blocco su un account di archiviazione con la portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione nel portale di Azure.
1. Nella sezione **Impostazioni** selezionare **blocchi**.
1. Selezionare **Aggiungi**.
1. Fornire un nome per il blocco di risorsa e specificare il tipo di blocco. Se lo si desidera, aggiungere una nota sul blocco.

    :::image type="content" source="media/lock-account-resource/lock-storage-account.png" alt-text="Screenshot che illustra come bloccare un account di archiviazione con un blocco CannotDelete":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per configurare un blocco in un account di archiviazione con PowerShell, assicurarsi prima di aver installato il [modulo AZ PowerShell](https://www.powershellgallery.com/packages/Az). Chiamare quindi il comando [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) e specificare il tipo di blocco che si vuole creare, come illustrato nell'esempio seguente:

```azurepowershell
New-AzResourceLock -LockLevel CanNotDelete `
    -LockName <lock> `
    -ResourceName <storage-account> `
    -ResourceType Microsoft.Storage/storageAccounts `
    -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare un blocco su un account di archiviazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Lock create](/cli/azure/lock#az_lock_create) e specificare il tipo di blocco che si vuole creare, come illustrato nell'esempio seguente:

```azurecli
az lock create \
    --name <lock> \
    --resource-group <resource-group> \
    --resource <storage-account> \
    --lock-type CanNotDelete \
    --resource-type Microsoft.Storage/storageAccounts
```

---

## <a name="authorizing-data-operations-when-a-readonly-lock-is-in-effect"></a>Autorizzazione delle operazioni sui dati quando un blocco ReadOnly è attivo

Quando si applica un blocco **ReadOnly** a un account di archiviazione, l'operazione [list keys](/rest/api/storagerp/storageaccounts/listkeys) è bloccata per l'account di archiviazione. L'operazione **list keys** è un'operazione post HTTPS e tutte le operazioni post vengono impedite quando si configura un blocco **ReadOnly** per l'account. L'operazione **list keys** restituisce le chiavi di accesso dell'account, che possono quindi essere usate per leggere e scrivere i dati nell'account di archiviazione.

Se un client è in possesso delle chiavi di accesso all'account nel momento in cui il blocco viene applicato all'account di archiviazione, il client può continuare a usare le chiavi per accedere ai dati. Tuttavia, i client che non hanno accesso alle chiavi dovranno usare Azure Active Directory (Azure AD) credenziali per accedere ai dati di BLOB o di Accodamento nell'account di archiviazione.

Gli utenti del portale di Azure possono essere interessati quando viene applicato un blocco di sola **lettura** , se hanno precedentemente eseguito l'accesso ai dati di BLOB o di accodamento nel portale con le chiavi di accesso dell'account. Dopo l'applicazione del blocco, gli utenti del portale dovranno usare le credenziali Azure AD per accedere ai dati BLOB o di accodamento nel portale. A tale scopo, a un utente devono essere assegnati almeno due ruoli di controllo degli accessi in base al ruolo: il ruolo di lettore Azure Resource Manager come minimo e uno dei ruoli di accesso ai dati di archiviazione di Azure. Per altre informazioni, vedere uno degli articoli seguenti:

- [Scegliere come autorizzare l'accesso ai dati BLOB nel portale di Azure](../blobs/authorize-data-operations-portal.md)
- [Scegliere come autorizzare l'accesso ai dati della coda nel portale di Azure](../queues/authorize-data-operations-portal.md)

I dati in File di Azure o nel servizio tabelle potrebbero non essere accessibili ai client che hanno precedentemente eseguito l'accesso con le chiavi dell'account. Come procedura consigliata, se è necessario applicare un blocco **ReadOnly** a un account di archiviazione, spostare i carichi di lavoro del servizio file di Azure e tabella in un account di archiviazione che non è bloccato con un blocco **ReadOnly** .

## <a name="next-steps"></a>Passaggi successivi

[Bloccare le risorse per impedire modifiche](../../azure-resource-manager/management/lock-resources.md)
