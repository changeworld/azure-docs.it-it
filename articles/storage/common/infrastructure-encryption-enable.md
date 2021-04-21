---
title: Creare un account di archiviazione con la crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati
titleSuffix: Azure Storage
description: I clienti che richiedono livelli più elevati di sicurezza dei dati possono anche abilitare la crittografia AES a 256 bit a Archiviazione di Azure livello di infrastruttura. Quando la crittografia dell'infrastruttura è abilitata, i dati in un account di archiviazione vengono crittografati due volte con due diversi algoritmi di crittografia e due chiavi diverse.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 23b3ca919be030490cca06f31dac623d7f80be44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790384"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Creare un account di archiviazione con la crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati

Archiviazione di Azure crittografa automaticamente tutti i dati in un account di archiviazione a livello di servizio usando la crittografia AES a 256 bit, una delle crittografie a blocchi più potenti disponibili ed è conforme a FIPS 140-2. I clienti che richiedono livelli più elevati di sicurezza dei dati possono anche abilitare la crittografia AES a 256 bit a Archiviazione di Azure livello di infrastruttura. Quando la crittografia dell'infrastruttura è abilitata, i dati in un account di archiviazione vengono crittografati due volte a livello di servizio e una volta a livello di infrastruttura con due diversi algoritmi di crittografia e &mdash; &mdash; due chiavi diverse. La doppia crittografia Archiviazione di Azure dati consente di proteggersi da uno scenario in cui uno degli algoritmi o delle chiavi di crittografia può essere compromesso. In questo scenario, il livello aggiuntivo di crittografia continua a proteggere i dati.

La crittografia a livello di servizio supporta l'uso di chiavi gestite da Microsoft o chiavi gestite dal cliente con Azure Key Vault o Key Vault Managed Hardware Security Model (HSM) (anteprima). La crittografia a livello di infrastruttura si basa su chiavi gestite da Microsoft e usa sempre una chiave separata. Per altre informazioni sulla gestione delle chiavi con Archiviazione di Azure crittografia, vedere [Informazioni sulla gestione delle chiavi di crittografia.](storage-service-encryption.md#about-encryption-key-management)

Per crittografare i dati in modo raddoppiato, è prima necessario creare un account di archiviazione configurato per la crittografia dell'infrastruttura. Questo articolo descrive come creare un account di archiviazione che abilita la crittografia dell'infrastruttura.

## <a name="register-to-use-infrastructure-encryption"></a>Registrarsi per usare la crittografia dell'infrastruttura

Per creare un account di archiviazione con la crittografia dell'infrastruttura abilitata, è prima necessario registrarsi per usare questa funzionalità con Azure usando PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

N/D

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per eseguire la registrazione con PowerShell, chiamare [il comando Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Per controllare lo stato della registrazione con PowerShell, chiamare il [comando Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Dopo l'approvazione della registrazione, è necessario registrare nuovamente il provider Archiviazione di Azure risorse. Per registrare nuovamente il provider di risorse con PowerShell, chiamare il [comando Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire la registrazione con l'interfaccia della riga di comando di Azure, [chiamare il comando az feature register.](/cli/azure/feature#az_feature_register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Per controllare lo stato della registrazione con l'interfaccia della riga di comando di Azure, chiamare [il comando az feature.](/cli/azure/feature#az_feature_show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Dopo l'approvazione della registrazione, è necessario registrare nuovamente il provider Archiviazione di Azure risorse. Per registrare nuovamente il provider di risorse con l'interfaccia della riga di comando di Azure, chiamare [il comando az provider register.](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Creare un account con la crittografia dell'infrastruttura abilitata

È necessario configurare un account di archiviazione per l'uso della crittografia dell'infrastruttura al momento della creazione dell'account. L'account di archiviazione deve essere di tipo utilizzo generico v2.

La crittografia dell'infrastruttura non può essere abilitata o disabilitata dopo la creazione dell'account.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per usare PowerShell per creare un account di archiviazione con la crittografia dell'infrastruttura abilitata, seguire questa procedura:

1. Nel portale di Azure passare alla pagina **Account di** archiviazione.
1. Scegliere il **pulsante** Aggiungi per aggiungere un nuovo account di archiviazione per utilizzo generico v2.
1. Nella scheda **Avanzate** individuare Crittografia **dell'infrastruttura** e selezionare **Abilitato.**
1. Selezionare **Rivedi e crea per** completare la creazione dell'account di archiviazione.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Screenshot che mostra come abilitare la crittografia dell'infrastruttura durante la creazione dell'account":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per usare PowerShell per creare un account di archiviazione con la crittografia dell'infrastruttura abilitata, assicurarsi di aver installato il modulo [Az.Storage di PowerShell,](https://www.powershellgallery.com/packages/Az.Storage)versione 2.2.0 o successiva. Per altre informazioni, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

Creare quindi un account di archiviazione per utilizzo generico v2 chiamando il [comando New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Includere `-RequireInfrastructureEncryption` l'opzione per abilitare la crittografia dell'infrastruttura.

L'esempio seguente illustra come creare un account di archiviazione per utilizzo generico v2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e con la crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per usare l'interfaccia della riga di comando di Azure per creare un account di archiviazione con la crittografia dell'infrastruttura abilitata, assicurarsi di aver installato l'interfaccia della riga di comando di Azure versione 2.8.0 o successiva. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Creare quindi un account di archiviazione per utilizzo generico v2 chiamando il [comando az storage account create](/cli/azure/storage/account#az_storage_account_create) e includere per abilitare la crittografia `--require-infrastructure-encryption option` dell'infrastruttura.

L'esempio seguente illustra come creare un account di archiviazione per utilizzo generico v2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e con la crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Modello](#tab/template)

L'esempio JSON seguente crea un account di archiviazione per utilizzo generico v2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e con la crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Verificare che la crittografia dell'infrastruttura sia abilitata

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per verificare che la crittografia dell'infrastruttura sia abilitata per un account di archiviazione con portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione nel portale di Azure.
1. In **Impostazioni** scegliere **Crittografia.**

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Screenshot che mostra come verificare che la crittografia dell'infrastruttura sia abilitata per l'account":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare che la crittografia dell'infrastruttura sia abilitata per un account di archiviazione con PowerShell, chiamare [il comando Get-AzStorageAccount.](/powershell/module/az.storage/get-azstorageaccount) Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Recuperare il `RequireInfrastructureEncryption` campo all'interno `Encryption` della proprietà e verificare che sia impostato su `True` .

Nell'esempio seguente viene recuperato il valore della `RequireInfrastructureEncryption` proprietà . Ricordarsi di sostituire i valori segnaposto tra parentesi angolari con valori personalizzati:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare che la crittografia dell'infrastruttura sia abilitata per un account di archiviazione con l'interfaccia della riga di comando di Azure, chiamare [il comando az storage account show.](/cli/azure/storage/account#az_storage_account_show) Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il campo `requireInfrastructureEncryption` all'interno `encryption` della proprietà e verificare che sia impostato su `true` .

Nell'esempio seguente viene recuperato il valore della `requireInfrastructureEncryption` proprietà . Ricordarsi di sostituire i valori segnaposto tra parentesi angolari con valori personalizzati:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Chiavi gestite dal cliente per Archiviazione di Azure crittografia](customer-managed-keys-overview.md)
