---
title: Creare un account che supporti le chiavi gestite dal cliente per tabelle e code
titleSuffix: Azure Storage
description: Informazioni su come creare un account di archiviazione che supporta la configurazione delle chiavi gestite dal cliente per tabelle e code. Usare l'interfaccia della riga di comando di Azure o un modello Azure Resource Manager per creare un account di archiviazione che si basa sulla chiave di crittografia dell'account Archiviazione di Azure crittografia. È quindi possibile configurare le chiavi gestite dal cliente per l'account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4c86811ee72d2713fced6320a17d1ccde1866d99
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769950"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Creare un account che supporti le chiavi gestite dal cliente per tabelle e code

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, l'archiviazione code e l'archiviazione tabelle usano una chiave con ambito del servizio e gestita da Microsoft. È anche possibile scegliere di usare chiavi gestite dal cliente per crittografare i dati della coda o della tabella. Per usare le chiavi gestite dal cliente con code e tabelle, è innanzitutto necessario creare un account di archiviazione che usa una chiave di crittografia con ambito per l'account, anziché per il servizio. Dopo aver creato un account che usa la chiave di crittografia dell'account per i dati della coda e della tabella, è possibile configurare le chiavi gestite dal cliente per tale account di archiviazione.

Questo articolo descrive come creare un account di archiviazione che si basa su una chiave con ambito per l'account. Quando l'account viene creato per la prima volta, Microsoft usa la chiave dell'account per crittografare i dati nell'account e Microsoft gestisce la chiave. Successivamente, è possibile configurare le chiavi gestite dal cliente per l'account per sfruttare tali vantaggi, inclusa la possibilità di fornire le proprie chiavi, aggiornare la versione della chiave, ruotare le chiavi e revocare i controlli di accesso.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Creare un account che usa la chiave di crittografia dell'account

È necessario configurare un nuovo account di archiviazione per usare la chiave di crittografia dell'account per code e tabelle al momento della creazione dell'account di archiviazione. Non è possibile modificare l'ambito della chiave di crittografia dopo la creazione dell'account.

L'account di archiviazione deve essere di tipo utilizzo generico v2. È possibile creare l'account di archiviazione e configurarlo in modo che si basa sulla chiave di crittografia dell'account usando l'interfaccia della riga di comando di Azure o un modello Azure Resource Manager archiviazione.

> [!NOTE]
> È possibile configurare facoltativamente solo l'archiviazione tabelle e code per crittografare i dati con la chiave di crittografia dell'account quando viene creato l'account di archiviazione. L'archiviazione BLOB e File di Azure usano sempre la chiave di crittografia dell'account per crittografare i dati.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per usare PowerShell per creare un account di archiviazione basato sulla chiave di crittografia dell'account, assicurarsi di aver installato il modulo Azure PowerShell, versione 3.4.0 o successiva. Per altre informazioni, vedere [Installare il modulo Azure PowerShell .](/powershell/azure/install-az-ps)

Creare quindi un account di archiviazione per utilizzo generico v2 chiamando il [comando New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) con i parametri appropriati:

- Includere `-EncryptionKeyTypeForQueue` l'opzione e impostarne il valore su `Account` per usare la chiave di crittografia dell'account per crittografare i dati nell'archiviazione code.
- Includere `-EncryptionKeyTypeForTable` l'opzione e impostarne il valore su `Account` per usare la chiave di crittografia dell'account per crittografare i dati in Archiviazione tabelle.

L'esempio seguente illustra come creare un account di archiviazione per utilizzo generico v2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e che usa la chiave di crittografia dell'account per crittografare i dati sia per l'archiviazione code che per l'archiviazione tabelle. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per usare l'interfaccia della riga di comando di Azure per creare un account di archiviazione basato sulla chiave di crittografia dell'account, assicurarsi di aver installato l'interfaccia della riga di comando di Azure versione 2.0.80 o successiva. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Creare quindi un account di archiviazione per utilizzo generico v2 chiamando il [comando az storage account create](/cli/azure/storage/account#az_storage_account_create) con i parametri appropriati:

- Includere `--encryption-key-type-for-queue` l'opzione e impostarne il valore su `Account` per usare la chiave di crittografia dell'account per crittografare i dati nell'archiviazione code.
- Includere `--encryption-key-type-for-table` l'opzione e impostarne il valore su `Account` per usare la chiave di crittografia dell'account per crittografare i dati in Archiviazione tabelle.

L'esempio seguente illustra come creare un account di archiviazione per utilizzo generico v2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e che usa la chiave di crittografia dell'account per crittografare i dati sia per l'archiviazione code che per l'archiviazione tabelle. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Modello](#tab/template)

L'esempio JSON seguente crea un account di archiviazione per utilizzo generico v2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e che usa la chiave di crittografia dell'account per crittografare i dati per l'archiviazione code e tabelle. Ricordarsi di sostituire i valori segnaposto tra parentesi angolari con valori personalizzati:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Dopo aver creato un account basato sulla chiave di crittografia dell'account, è possibile configurare le chiavi gestite dal cliente archiviate in Azure Key Vault o nel modello di protezione hardware gestito (HSM) di Key Vault (anteprima). Per informazioni su come archiviare le chiavi gestite dal cliente in un insieme di credenziali delle chiavi, vedere Configurare la crittografia con chiavi gestite dal cliente [archiviate in Azure Key Vault](customer-managed-keys-configure-key-vault.md). Per informazioni su come archiviare le chiavi gestite dal cliente in un modulo di protezione HSM gestito, vedere Configurare la crittografia con chiavi gestite dal cliente archiviate in un modulo di protezione Azure Key Vault [gestito (anteprima).](customer-managed-keys-configure-key-vault-hsm.md)

## <a name="verify-the-account-encryption-key"></a>Verificare la chiave di crittografia dell'account

Per verificare che un servizio in un account di archiviazione utilizzi la chiave di crittografia dell'account, chiamare il comando [az storage account](/cli/azure/storage/account#az_storage_account_show) dell'interfaccia della riga di comando di Azure. Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il campo `keyType` per ogni servizio all'interno della proprietà di crittografia e verificare che sia impostato su `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare che un servizio in un account di archiviazione utilizzi la chiave di crittografia dell'account, chiamare [il comando Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il campo `KeyType` per ogni servizio all'interno della `Encryption` proprietà e verificare che sia impostato su `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare che un servizio in un account di archiviazione utilizzi la chiave di crittografia dell'account, chiamare il [comando az storage account show.](/cli/azure/storage/account#az_storage_account_show) Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il campo `keyType` per ogni servizio all'interno della proprietà di crittografia e verificare che sia impostato su `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Un account di archiviazione creato per usare una chiave di crittografia con ambito per l'account viene fatturato per la capacità di archiviazione tabelle e le transazioni a una tariffa diversa rispetto a un account che usa la chiave predefinita con ambito di servizio. Per informazioni dettagliate, vedere [Prezzi di Archiviazione tabelle di Azure](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Chiavi gestite dal cliente per la Archiviazione di Azure dati](customer-managed-keys-overview.md)
- [Informazioni su Azure Key Vault?](../../key-vault/general/overview.md)