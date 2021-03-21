---
title: Creare e recuperare gli attributi di una chiave in Azure Key Vault - Azure PowerShell
description: Avvio rapido che illustra come impostare e recuperare una chiave da Azure Key Vault con Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: adbf3080367e54147c981c8ccf0bb6236111b8c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071206"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Avvio rapido: Impostare e recuperare una chiave da Azure Key Vault con Azure PowerShell

In questa guida di avvio rapido viene creato un insieme di credenziali delle chiavi in Azure Key Vault con Azure PowerShell. Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. Per altre informazioni su Key Vault, vedere la [panoramica](../general/overview.md). Azure PowerShell viene usato per creare e gestire le risorse di Azure con comandi o script. Successivamente verrà archiviata una chiave.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Digitare `$PSVersionTable.PSVersion` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Aggiungere una chiave a Key Vault

Per aggiungere una chiave a Key Vault, sono sufficienti un paio di passaggi aggiuntivi. Questa chiave può essere usata da un'applicazione. 

Digitare i comandi seguenti per creare una chiave denominata **ExampleKey**:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "ExampleKey" -Destination "Software"
```

È ora possibile fare riferimento a questa chiave aggiunta ad Azure Key Vault usando il relativo URI. Per ottenere la versione corrente, usare **"https://<il nome univoco** dell'insieme di credenziali delle credenziali di>. Vault.Azure.NET/Keys/ExampleKey". 

Per visualizzare la chiave archiviata in precedenza:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -KeyName "ExampleKey"
```

A questo punto è stata creata un'istanza di Key Vault nella quale è stata archiviata e recuperata una chiave.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un certificato. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere le informazioni di riferimento sui [cmdlet di Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Vedere [Panoramica della sicurezza di Azure Key Vault](../general/security-overview.md)
