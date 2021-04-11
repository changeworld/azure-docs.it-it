---
title: Archiviare un segreto su più righe in Azure Key Vault
description: Esercitazione che illustra come impostare i segreti multiriga da Azure Key Vault usando l'interfaccia della riga di comando di Azure e PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610285"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Archiviare un segreto a più righe in Azure Key Vault

La Guida [introduttiva dell'interfaccia](quick-create-cli.md) della riga di comando di Azure e [Azure PowerShell Guida introduttiva](quick-create-powershell.md) illustrano come archiviare un segreto a riga singola   È anche possibile usare Key Vault per archiviare un segreto a più righe, ad esempio un file JSON o una chiave privata RSA.

Non è possibile passare i segreti a più righe all'interfaccia della riga di comando di Azure [AZ chiavi Vault Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) o il cmdlet Azure PowerShell [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) tramite la riga di comando. È invece necessario archiviare il segreto a più righe come file di testo. 

Ad esempio, è possibile creare un file di testo denominato "secretfile.txt" contenente le righe seguenti:

```bash
This is my
multi-line
secret
```

È quindi possibile passare il file all'interfaccia della riga di comando di Azure [AZ chiave Vault Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) usando il `--file` parametro.

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

Con Azure PowerShell, è necessario innanzitutto leggere il file usando il cmdlet [Get-Content](/powershell/module/microsoft.powershell.management/get-content) , quindi convertirlo in una stringa sicura usando [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Infine, archiviare il segreto usando il cmdlet [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

In entrambi i casi, è possibile visualizzare il segreto archiviato usando l'interfaccia della riga di comando di Azure [AZ chiave Vault Secret Show](/cli/azure/keyvault/secret#az_keyvault_secret_show) oppure il cmdlet Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) .

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Il segreto verrà restituito con le nuove righe incorporate:

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Passaggi successivi

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere [avvio rapido dell'interfaccia](quick-create-cli.md) della riga di comando di Azure
- Vedere i comandi dell'interfaccia della riga di [comando di Azure AZ](/cli/azure/keyvault) .
- Vedere la [Guida introduttiva Azure PowerShell](quick-create-powershell.md)
- Vedere i [cmdlet di Azure PowerShell AZ. DataVault](/powershell/module/az.keyvault#key-vault)
