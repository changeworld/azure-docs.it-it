---
title: Configurare la crittografia con le chiavi gestite dal cliente archiviate in Azure Key Vault
titleSuffix: Azure Storage
description: Informazioni su come configurare la Archiviazione di Azure con chiavi gestite dal cliente archiviate in Azure Key Vault usando l'portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 77a01a270f47ddacb71962188e7fedd0a0a9f6d0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790438"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Configurare la crittografia con le chiavi gestite dal cliente archiviate in Azure Key Vault

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un controllo aggiuntivo sulle chiavi di crittografia, è possibile gestire le proprie chiavi. Le chiavi gestite dal cliente devono essere archiviate in Azure Key Vault o Key Vault HSM (Managed Hardware Security Model) (anteprima).

Questo articolo illustra come configurare la crittografia con chiavi gestite dal cliente archiviate in un insieme di credenziali delle chiavi usando portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per informazioni su come configurare la crittografia con chiavi gestite dal cliente archiviate in un modulo di protezione HSM gestito, vedere Configurare la crittografia con chiavi gestite dal cliente archiviate [in Azure Key Vault Managed HSM (anteprima)](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> Azure Key Vault e Azure Key Vault HSM gestito supportano le stesse API e le stesse interfacce di gestione per la configurazione.

## <a name="configure-a-key-vault"></a>Configurare un insieme di credenziali delle chiavi

È possibile usare un insieme di credenziali delle chiavi nuovo o esistente per archiviare le chiavi gestite dal cliente. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse.

L'uso di chiavi gestite dal cliente Archiviazione di Azure la crittografia dei dati richiede che sia l'eliminazione soft che la protezione dall'eliminazione siano abilitate per l'insieme di credenziali delle chiavi. L'eliminazione soft è abilitata per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi e non può essere disabilitata. È possibile abilitare la protezione dall'eliminazione quando si crea l'insieme di credenziali delle chiavi o dopo la creazione.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per informazioni su come creare un insieme di credenziali delle chiavi con portale di Azure, vedere Avvio [rapido:](../../key-vault/general/quick-create-portal.md)Creare un insieme di credenziali delle chiavi usando l'portale di Azure . Quando si crea l'insieme di credenziali delle chiavi, selezionare **Abilita protezione ripulitura**, come illustrato nell'immagine seguente.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Screenshot che mostra come abilitare la protezione dall'eliminazione durante la creazione di un insieme di credenziali delle chiavi":::

Per abilitare la protezione dall'eliminazione in un insieme di credenziali delle chiavi esistente, seguire questa procedura:

1. Passare all'insieme di credenziali delle chiavi nel portale di Azure.
1. In **Impostazioni** scegliere **Proprietà**.
1. Nella sezione **Protezione ripulitura** scegliere **Abilita protezione da ripulitura**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per creare un nuovo insieme di credenziali delle chiavi con PowerShell, installare la versione 2.0.0 o successiva del modulo [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell. Chiamare quindi [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) per creare un nuovo insieme di credenziali delle chiavi. Con la versione 2.0.0 e successive del modulo Az.KeyVault, l'eliminazione software è abilitata per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi.

L'esempio seguente crea un nuovo insieme di credenziali delle chiavi con sia l'eliminazione soft che la protezione dall'eliminazione abilitata. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Per informazioni su come abilitare la protezione dall'eliminazione in un insieme di credenziali delle chiavi esistente con PowerShell, vedere Come usare l'eliminazione [soft con PowerShell.](../../key-vault/general/key-vault-recovery.md)

Assegnare quindi un'identità gestita assegnata dal sistema all'account di archiviazione. Questa identità gestita verrà utilizzata per concedere all'account di archiviazione le autorizzazioni per accedere all'insieme di credenziali delle chiavi. Per altre informazioni sulle identità gestite assegnate dal sistema, vedere Informazioni sulle [identità gestite per le risorse di Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Per assegnare un'identità gestita tramite PowerShell, [chiamare Set-AzStorageAccount:](/powershell/module/az.storage/set-azstorageaccount)

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Infine, configurare i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione abbia le autorizzazioni per accedervi. In questo passaggio si userà l'identità gestita assegnata in precedenza all'account di archiviazione.

Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, [chiamare Set-AzKeyVaultAccessPolicy:](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare un nuovo insieme di credenziali delle chiavi usando l'interfaccia della riga di comando di Azure, chiamare [az keyvault create](/cli/azure/keyvault#az_keyvault_create). Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Per informazioni su come abilitare la protezione dall'eliminazione in un insieme di credenziali delle chiavi esistente con l'interfaccia della riga di comando di Azure, vedere Come usare l'eliminazione soft con l'interfaccia [della riga di comando.](../../key-vault/general/key-vault-recovery.md)

Assegnare quindi un'identità gestita assegnata dal sistema all'account di archiviazione. Questa identità gestita verrà utilizzata per concedere all'account di archiviazione le autorizzazioni per accedere all'insieme di credenziali delle chiavi. Per altre informazioni sulle identità gestite assegnate dal sistema, vedere Informazioni sulle [identità gestite per le risorse di Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Per assegnare un'identità gestita tramite l'interfaccia della riga di comando di Azure, [chiamare az storage account update:](/cli/azure/storage/account#az_storage_account_update)

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Infine, configurare i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione abbia le autorizzazioni per accedervi. In questo passaggio si userà l'identità gestita assegnata in precedenza all'account di archiviazione.

Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy):

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>Aggiungere una chiave

Aggiungere quindi una chiave nell'insieme di credenziali delle chiavi.

Archiviazione di Azure crittografia supporta chiavi RSA e RSA-HSM di dimensioni 2048, 3072 e 4096. Per altre informazioni sulle chiavi, vedere [Informazioni sulle chiavi](../../key-vault/keys/about-keys.md).

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per informazioni su come aggiungere una chiave con il portale di Azure, vedere Avvio [rapido:](../../key-vault/keys/quick-create-portal.md)Impostare e recuperare una chiave Azure Key Vault usando il portale di Azure .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per aggiungere una chiave con PowerShell, [chiamare Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati e di usare le variabili definite negli esempi precedenti.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per aggiungere una chiave con l'interfaccia della riga di comando di Azure, chiamare [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal cliente

Configurare quindi l'account Archiviazione di Azure per usare le chiavi gestite dal cliente con Azure Key Vault, quindi specificare la chiave da associare all'account di archiviazione.

Quando si configura la crittografia con chiavi gestite dal cliente, è possibile scegliere di aggiornare automaticamente la versione della chiave usata per la crittografia Archiviazione di Azure ogni volta che è disponibile una nuova versione nell'insieme di credenziali delle chiavi associato. In alternativa, è possibile specificare in modo esplicito una versione della chiave da usare per la crittografia fino a quando la versione della chiave non viene aggiornata manualmente.

> [!NOTE]
> Per ruotare una chiave, creare una nuova versione della chiave in Azure Key Vault. Archiviazione di Azure non gestisce la rotazione della chiave in Azure Key Vault, quindi sarà necessario ruotare la chiave manualmente o creare una funzione per ruotarla in base a una pianificazione.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Configurare la crittografia per l'aggiornamento automatico delle versioni delle chiavi

Archiviazione di Azure aggiornare automaticamente la chiave gestita dal cliente usata per la crittografia per usare la versione più recente della chiave. Quando la chiave gestita dal cliente viene ruotata in Azure Key Vault, Archiviazione di Azure verrà avviata automaticamente l'uso della versione più recente della chiave per la crittografia.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per configurare le chiavi gestite dal cliente con l'aggiornamento automatico della versione della chiave nel portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione.
1. Nel pannello **Impostazioni** relativo all'account di archiviazione fare clic su **Crittografia**. Per impostazione predefinita, la gestione delle chiavi è impostata su **Chiavi gestite da Microsoft**, come illustrato nell'immagine seguente.

    ![Screenshot del portale che mostra l'opzione di crittografia](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. Selezionare **l'opzione Chiavi gestite dal** cliente.
1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
1. Selezionare **Selezionare un insieme di credenziali delle chiavi e una chiave.**
1. Selezionare l'insieme di credenziali delle chiavi contenente la chiave da usare.
1. Selezionare la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot che mostra come selezionare l'insieme di credenziali delle chiavi e la chiave](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Salvare le modifiche.

Dopo aver specificato la chiave, il portale di Azure indica che l'aggiornamento automatico della versione della chiave è abilitato e visualizza la versione della chiave attualmente in uso per la crittografia.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Screenshot che mostra l'aggiornamento automatico della versione della chiave abilitata":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per configurare le chiavi gestite dal cliente con l'aggiornamento automatico della versione della chiave con PowerShell, installare il modulo [Az.Storage,](https://www.powershellgallery.com/packages/Az.Storage) versione 2.0.0 o successiva.

Per aggiornare automaticamente la versione della chiave per una chiave gestita dal cliente, omettere la versione della chiave quando si configura la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Chiamare [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente, e includere l'opzione **-KeyvaultEncryption** per abilitare le chiavi gestite dal cliente per l'account di archiviazione.

Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare le chiavi gestite dal cliente con l'aggiornamento automatico della versione della chiave con l'interfaccia della riga di comando di Azure, installare l'interfaccia della riga di comando di [Azure versione 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) o successiva. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per aggiornare automaticamente la versione della chiave per una chiave gestita dal cliente, omettere la versione della chiave quando si configura la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Chiamare [az storage account update](/cli/azure/storage/account#az_storage_account_update) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente. Includere il `--encryption-key-source` parametro e impostarlo su per `Microsoft.Keyvault` abilitare le chiavi gestite dal cliente per l'account.

Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Configurare la crittografia per l'aggiornamento manuale delle versioni delle chiavi

Se si preferisce aggiornare manualmente la versione della chiave, specificare in modo esplicito la versione al momento della configurazione della crittografia con chiavi gestite dal cliente. In questo caso, Archiviazione di Azure la versione della chiave non verrà aggiornata automaticamente quando viene creata una nuova versione nell'insieme di credenziali delle chiavi. Per usare una nuova versione della chiave, è necessario aggiornare manualmente la versione usata per Archiviazione di Azure crittografia.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per configurare le chiavi gestite dal cliente con l'aggiornamento manuale della versione della chiave nel portale di Azure, specificare l'URI della chiave, inclusa la versione. Per specificare una chiave come URI, seguire questa procedura:

1. Per individuare l'URI della chiave nel portale di Azure, passare all'insieme di credenziali delle chiavi e selezionare **l'impostazione** Chiavi. Selezionare la chiave desiderata, quindi fare clic sulla chiave per visualizzarne le versioni. Selezionare una versione chiave per visualizzare le impostazioni per tale versione.
1. Copiare il valore del campo **Identificatore di** chiave, che fornisce l'URI.

    ![Screenshot che mostra l'URI della chiave dell'insieme di credenziali delle chiavi](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. Nelle impostazioni **della chiave di crittografia** per l'account di archiviazione scegliere l'opzione Immettere **l'URI della** chiave.
1. Incollare l'URI copiato nel **campo URI chiave.** Omettere la versione della chiave dall'URI per abilitare l'aggiornamento automatico della versione della chiave.

   ![Screenshot che mostra come immettere l'URI della chiave](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Specificare la sottoscrizione che contiene l'insieme di credenziali delle chiavi.
1. Salvare le modifiche.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per configurare le chiavi gestite dal cliente con l'aggiornamento manuale della versione della chiave, specificare in modo esplicito la versione della chiave quando si configura la crittografia per l'account di archiviazione. Chiamare [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente, e includere l'opzione **-KeyvaultEncryption** per abilitare le chiavi gestite dal cliente per l'account di archiviazione.

Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Quando si aggiorna manualmente la versione della chiave, è necessario aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione. Chiamare prima [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) per ottenere la versione più recente della chiave. Chiamare quindi [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) per aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione della chiave, come illustrato nell'esempio precedente.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per configurare le chiavi gestite dal cliente con l'aggiornamento manuale della versione della chiave, specificare in modo esplicito la versione della chiave quando si configura la crittografia per l'account di archiviazione. Chiamare [az storage account update](/cli/azure/storage/account#az_storage_account_update) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente. Includere il `--encryption-key-source` parametro e impostarlo su per `Microsoft.Keyvault` abilitare le chiavi gestite dal cliente per l'account.

Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

Quando si aggiorna manualmente la versione della chiave, è necessario aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione. Per prima cosa, eseguire una query per l'URI dell'insieme di credenziali delle chiavi chiamando [az keyvault show](/cli/azure/keyvault#az_keyvault_show)e per la versione della chiave chiamando [az keyvault key list-versions](/cli/azure/keyvault/key#az_keyvault_key_list-versions). Chiamare quindi [az storage account update](/cli/azure/storage/account#az_storage_account_update) per aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione della chiave, come illustrato nell'esempio precedente.

---

## <a name="change-the-key"></a>Modificare la chiave

È possibile modificare la chiave in uso per la crittografia Archiviazione di Azure in qualsiasi momento.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per modificare la chiave con il portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione e visualizzare le impostazioni **di** crittografia.
1. Selezionare l'insieme di credenziali delle chiavi e scegliere una nuova chiave.
1. Salvare le modifiche.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per modificare la chiave con PowerShell, chiamare [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) come illustrato [in](#configure-encryption-with-customer-managed-keys) Configurare la crittografia con chiavi gestite dal cliente e specificare il nuovo nome e la nuova versione della chiave. Se la nuova chiave si trova in un insieme di credenziali delle chiavi diverso, è necessario aggiornare anche l'URI dell'insieme di credenziali delle chiavi.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per modificare la chiave con l'interfaccia della riga di comando di Azure, chiamare [az storage account update](/cli/azure/storage/account#az_storage_account_update) come illustrato in [Configurare](#configure-encryption-with-customer-managed-keys) la crittografia con chiavi gestite dal cliente e specificare il nuovo nome e la nuova versione della chiave. Se la nuova chiave si trova in un insieme di credenziali delle chiavi diverso, è necessario aggiornare anche l'URI dell'insieme di credenziali delle chiavi.

---

## <a name="revoke-customer-managed-keys"></a>Revocare le chiavi gestite dal cliente

La revoca di una chiave gestita dal cliente rimuove l'associazione tra l'account di archiviazione e l'insieme di credenziali delle chiavi.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per revocare le chiavi gestite dal cliente con portale di Azure, disabilitare la chiave come descritto in [Disabilitare le chiavi gestite dal cliente.](#disable-customer-managed-keys)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

È possibile revocare le chiavi gestite dal cliente rimuovendo i criteri di accesso dell'insieme di credenziali delle chiavi. Per revocare una chiave gestita dal cliente con PowerShell, chiamare il comando [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati e di usare le variabili definite negli esempi precedenti.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile revocare le chiavi gestite dal cliente rimuovendo i criteri di accesso dell'insieme di credenziali delle chiavi. Per revocare una chiave gestita dal cliente con l'interfaccia della riga di comando di Azure, chiamare il [comando az keyvault delete-policy,](/cli/azure/keyvault#az_keyvault_delete_policy) come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati e di usare le variabili definite negli esempi precedenti.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Disabilitare le chiavi gestite dal cliente

Quando si disabilitano le chiavi gestite dal cliente, l'account di archiviazione viene nuovamente crittografato con le chiavi gestite da Microsoft.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per disabilitare le chiavi gestite dal cliente nel portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione e visualizzare le **impostazioni di** crittografia.
1. Deselezionare la casella di controllo **accanto all'impostazione Usa chiave** personale.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per disabilitare le chiavi gestite dal cliente con PowerShell, chiamare [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) con l'opzione `-StorageEncryption` , come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati e di usare le variabili definite negli esempi precedenti.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per disabilitare le chiavi gestite dal cliente con l'interfaccia della riga di comando di Azure, chiamare [az storage account update](/cli/azure/storage/account#az_storage_account_update) e impostare `--encryption-key-source parameter` su , come illustrato `Microsoft.Storage` nell'esempio seguente. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati e di usare le variabili definite negli esempi precedenti.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Chiavi gestite dal cliente per la Archiviazione di Azure crittografia](customer-managed-keys-overview.md)
- [Configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito (anteprima)](customer-managed-keys-configure-key-vault-hsm.md)
