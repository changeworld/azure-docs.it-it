---
title: Gestire le chiavi di accesso dell'account
titleSuffix: Azure Storage
description: Informazioni su come visualizzare, gestire e ruotare le chiavi di accesso dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 82d272f22295a5b68d1e8de3fb5a70c45d4c14a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791212"
---
# <a name="manage-storage-account-access-keys"></a>Gestire le chiavi di accesso dell'account di archiviazione

Quando si crea un account di archiviazione, Azure genera due chiavi di accesso dell'account di archiviazione a 512 bit. Queste chiavi possono essere usate per autorizzare l'accesso ai dati nell'account di archiviazione tramite l'autorizzazione chiave condivisa.

Microsoft consiglia di usare Azure Key Vault le chiavi di accesso e di ruotare e rigenerare regolarmente le chiavi. L Azure Key Vault rende più semplice ruotare le chiavi senza interruzioni per le applicazioni. È anche possibile ruotare manualmente le chiavi.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Visualizzare le chiavi di accesso dell'account

È possibile visualizzare e copiare le chiavi di accesso dell'account con portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Il portale di Azure fornisce anche una stringa di connessione per l'account di archiviazione che è possibile copiare.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per visualizzare e copiare le chiavi di accesso o la stringa di connessione dell'account di archiviazione dal portale di Azure:

1. Passare all'account di archiviazione nella [portale di Azure](https://portal.azure.com).
1. In **Impostazioni** selezionare **Chiavi di accesso**. Verranno visualizzate le chiavi di accesso dell'account, con la stringa di connessione completa per ogni chiave.
1. Individuare il **valore chiave** in **key1** e fare clic sul **pulsante Copia** per copiare la chiave dell'account.
1. In alternativa, è possibile copiare l'intera stringa di connessione. Trovare il valore **Stringa di connessione** in **key1** e fare clic sul pulsante **Copia** per copiare la stringa di connessione.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Screenshot che mostra come visualizzare le chiavi di accesso nel portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per recuperare le chiavi di accesso dell'account con PowerShell, chiamare il [comando Get-AzStorageAccountKey.](/powershell/module/az.Storage/Get-azStorageAccountKey)

Nell'esempio seguente viene recuperata la prima chiave. Per recuperare la seconda chiave, usare `Value[1]` invece di `Value[0]` . Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per elencare le chiavi di accesso dell'account con l'interfaccia della riga di comando di Azure, chiamare il [comando az storage account keys list,](/cli/azure/storage/account/keys#az_storage_account_keys_list) come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

È possibile usare una delle due chiavi per accedere Archiviazione di Azure, ma in generale è consigliabile usare la prima chiave e riservare l'uso della seconda chiave per quando si ruotano le chiavi.

Per visualizzare o leggere le chiavi di accesso di un account, l'utente deve essere un amministratore del servizio o deve avere un ruolo di Azure che include **Microsoft.Storage/storageAccounts/listkeys/action.** Alcuni ruoli predefiniti di Azure che includono questa azione sono **proprietario,** collaboratore e ruolo del servizio Operatore chiave **dell'account di** archiviazione. Per altre informazioni sul ruolo Amministratore del servizio, vedere Ruoli di amministratore della sottoscrizione [classica, Ruoli di Azure e ruoli Azure AD servizio.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Per informazioni dettagliate sui ruoli predefiniti per il  controllo degli accessi in Archiviazione di Azure, vedere la sezione Archiviazione in Ruoli predefiniti di Azure per il controllo degli accessi in base al ruolo [di Azure.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Usare Azure Key Vault per gestire le chiavi di accesso

Microsoft consiglia di usare Azure Key Vault per gestire e ruotare le chiavi di accesso. L'applicazione può accedere in modo sicuro alle chiavi in Key Vault, in modo da evitare di archiviarle con il codice dell'applicazione. Per altre informazioni sull'uso Key Vault per la gestione delle chiavi, vedere gli articoli seguenti:

- [Gestire le chiavi dell'account di archiviazione con Azure Key Vault e PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Gestire le chiavi degli account di archiviazione con Azure Key Vault e l'interfaccia della riga di comando di Azure](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ruotare manualmente le chiavi di accesso

Microsoft consiglia di ruotare periodicamente le chiavi di accesso per proteggere l'account di archiviazione. Se possibile, usare Azure Key Vault per gestire le chiavi di accesso. Se non si usa Key Vault, è necessario ruotare le chiavi manualmente.

Vengono assegnate due chiavi di accesso che è quindi possibile ruotare. La presenza di due chiavi garantisce che l'applicazione mantenga l'accesso Archiviazione di Azure durante il processo.

> [!WARNING]
> La rigenerazione delle chiavi di accesso può influire sulle applicazioni o sui servizi di Azure che dipendono dalla chiave dell'account di archiviazione. I client che usano la chiave dell'account per accedere all'account di archiviazione devono essere aggiornati per usare la nuova chiave, inclusi servizi multimediali, applicazioni cloud, desktop e per dispositivi mobili e applicazioni di interfaccia utente grafica per Archiviazione di Azure, ad esempio [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per ruotare le chiavi di accesso dell'account di archiviazione nel portale di Azure:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per fare riferimento alla chiave di accesso secondaria per l'account di archiviazione.
1. Passare all'account di archiviazione nella [portale di Azure](https://portal.azure.com).
1. In **Impostazioni** selezionare **Chiavi di accesso**.
1. Per rigenerare la chiave di accesso primaria per l'account di archiviazione, selezionare il pulsante **Rigenera** accanto alla chiave di accesso primaria.
1. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.
1. Rigenerare la chiave di accesso secondaria nello stesso modo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per ruotare le chiavi di accesso dell'account di archiviazione con PowerShell:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per fare riferimento alla chiave di accesso secondaria per l'account di archiviazione.
1. Chiamare il [comando New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) per rigenerare la chiave di accesso primaria, come illustrato nell'esempio seguente:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.
1. Rigenerare la chiave di accesso secondaria nello stesso modo. Per rigenerare la chiave secondaria, usare `key2` come nome della chiave anziché `key1` .

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per ruotare le chiavi di accesso dell'account di archiviazione con l'interfaccia della riga di comando di Azure:

1. Aggiornare le stringhe di connessione nel codice dell'applicazione per fare riferimento alla chiave di accesso secondaria per l'account di archiviazione.
1. Chiamare il [comando az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) per rigenerare la chiave di accesso primaria, come illustrato nell'esempio seguente:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Aggiornare le stringhe di connessione nel codice in modo che facciano riferimento alla nuova chiave di accesso primaria.
1. Rigenerare la chiave di accesso secondaria nello stesso modo. Per rigenerare la chiave secondaria, usare `key2` come nome della chiave anziché `key1` .

---

> [!NOTE]
> Microsoft consiglia di usare solo una delle chiavi in tutte le applicazioni contemporaneamente. Se si usa la Chiave 1 in alcune posizioni e la Chiave 2 in altre, non si potranno ruotare le chiavi senza quale applicazione perda l'accesso.

Per ruotare le chiavi di accesso di un account, l'utente deve essere un amministratore del servizio o deve essere assegnato un ruolo di Azure che include **Microsoft.Storage/storageAccounts/regeneratekey/action.** Alcuni ruoli predefiniti di Azure che includono questa azione sono i ruoli Del servizio **Proprietario,** **Collaboratore** e **Operatore** chiave account di archiviazione. Per altre informazioni sul ruolo Amministratore del servizio, vedere Ruoli di amministratore della sottoscrizione [classica,](../../role-based-access-control/rbac-and-directory-admin-roles.md)Ruoli di Azure e ruoli Azure AD servizio . Per informazioni dettagliate sui ruoli predefiniti di Azure per  Archiviazione di Azure, vedere la sezione Archiviazione in Ruoli predefiniti di Azure per il controllo degli accessi in base al [ruolo di Azure.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md)
- [Creare un account di archiviazione](storage-account-create.md)
