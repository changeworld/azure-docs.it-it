---
title: Ruotare la protezione TDE (PowerShell &'interfaccia della riga di comando di Azure)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Informazioni su come ruotare la protezione Transparent Data Encryption (TDE) per un server in Azure usato da database SQL di Azure e Azure Synapse Analytics usando PowerShell e l'interfaccia della riga di comando di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 67bcd8597314530f26481ef840644ffbc056b033
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777564"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Ruotare la Transparent Data Encryption (TDE)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Questo articolo descrive la rotazione delle chiavi per [un server usando](logical-servers.md) una protezione TDE da Azure Key Vault. Ruotare la protezione TDE logica per un server significa passare a una nuova chiave asimmetrica che protegge i database nel server. La rotazione delle chiavi è un'operazione online e il completamento dovrebbe richiedere solo alcuni secondi, perché questa operazione decrittografa e crittografa nuovamente la chiave di crittografia dei dati del database, non l'intero database.

In questa guida vengono illustrate due opzioni per ruotare la protezione TDE nel server.

> [!NOTE]
> È necessario riprendere un pool SQL dedicato in Azure Synapse Analytics prima delle rotazioni delle chiavi.

> [!IMPORTANT]
> Non eliminare le versioni precedenti della chiave dopo un rollover. Quando le chiavi vengono ruotate, alcuni dati vengono comunque crittografati con le chiavi precedenti, ad esempio, i backup meno recenti del database.

## <a name="prerequisites"></a>Prerequisiti

- Questa guida alle procedura presuppone che si sta già usando una chiave di Azure Key Vault come protezione TDE per database SQL di Azure o Azure Synapse Analytics. Vedere [Transparent Data Encryption con supporto BYOK](transparent-data-encryption-byok-overview.md).
- È necessario aver installato Azure PowerShell e in esecuzione.
- [Consigliata ma facoltativa] Creare innanzitutto il materiale della chiave per la protezione TDE in un modulo di protezione hardware (HSM) o in un archivio chiavi locale e importare il materiale della chiave in Azure Key Vault. Seguire le [Istruzioni per l'uso di un modulo di protezione hardware (HSM) e di Key Vault](../../key-vault/general/overview.md) per altre informazioni.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per istruzioni sull'installazione del modulo Az, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps). Per cmdlet specifici, vedere [AzureRM.Sql.](/powershell/module/AzureRM.Sql/)

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Il modulo AzureRM continuerà a ricevere correzioni di bug almeno fino a dicembre 2020.  Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici. Per altre informazioni sulla compatibilità, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'installazione, vedere Installare [l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli)

* * *

## <a name="manual-key-rotation"></a>Rotazione manuale delle chiavi

La rotazione manuale delle chiavi usa i comandi seguenti per aggiungere una chiave completamente nuova, che potrebbe essere in un nuovo nome di chiave o anche in un altro insieme di credenziali delle chiavi. Questo approccio consente di aggiungere la stessa chiave a diversi insiemi di credenziali delle chiavi per supportare scenari di ripristino di emergenza geografico a disponibilità elevata.

> [!NOTE]
> La lunghezza combinata per il nome dell'insieme di credenziali delle chiavi non può superare 94 caratteri.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare i cmdlet [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/Add-AzKeyVaultKey) [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare i [comandi az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create), [az sql server key create](/cli/azure/sql/server/key#az_sql_server_key_create)e [az sql server tde-key set.](/cli/azure/sql/server/tde-key#az_sql_server_tde_key_set)

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Passare alla modalità di protezione TDE

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Per passare dalla modalità gestita da Microsoft alla modalità BYOK per la protezione TDE, usare il cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Per passare dalla modalità BYOK alla protezione TDE gestita da Microsoft, usare il cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Gli esempi seguenti usano [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Per cambiare la protezione TDE dalla modalità gestita da Microsoft alla modalità BYOK,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Per passare la protezione TDE dalla modalità BYOK alla modalità gestita da Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Passaggi successivi

- In caso di rischio per la sicurezza, informazioni su come rimuovere una protezione TDE potenzialmente compromessa: Rimuovere una chiave [potenzialmente compromessa.](transparent-data-encryption-byok-remove-tde-protector.md)

- Introduzione all'integrazione Azure Key Vault e Bring Your Own Key supporto per TDE: attivare TDE usando la propria chiave da Key Vault [usando PowerShell.](transparent-data-encryption-byok-configure.md)