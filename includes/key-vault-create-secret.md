---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6745d1f7d97a8f4d08078bf93b61762ab04aad46
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073274"
---
Creare un segreto denominato **mySecret**, con un valore **Success!** . Un segreto può essere una password, una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro e rendere allo stesso tempo disponibile per l'applicazione. 

Per aggiungere un segreto all'insieme di credenziali delle chiavi appena creato, usare il comando seguente:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$secret = ConvertTo-SecureString -String 'Success!' -AsPlainText
Set-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret -SecretValue $secret
```
---