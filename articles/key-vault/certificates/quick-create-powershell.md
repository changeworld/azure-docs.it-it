---
title: 'Avvio rapido: Impostare & visualizzare Azure Key Vault certificati con Azure PowerShell'
description: Avvio rapido che illustra come impostare e recuperare un certificato da Azure Key Vault con Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 32150fb4cce76d5a2537c5ec969f265e0d8aae20
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816189"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Avvio rapido: Impostare e recuperare un certificato da Azure Key Vault con Azure PowerShell

In questa guida di avvio rapido viene creato un insieme di credenziali delle chiavi in Azure Key Vault con Azure PowerShell. Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. Per altre informazioni su Key Vault, vedere la [panoramica](../general/overview.md). Azure PowerShell viene usato per creare e gestire le risorse di Azure con comandi o script. Successivamente verrà archiviato un certificato.

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

## <a name="add-a-certificate-to-key-vault"></a>Aggiungere un certificato in Key Vault

Per aggiungere un certificato a Key Vault, sono sufficienti un paio di passaggi aggiuntivi. Questo certificato può essere usato da un'applicazione. 

Digitare i comandi seguenti per creare un certificato autofirmato con un criterio denominato **ExampleCertificate**:

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

È ora possibile fare riferimento a questo certificato aggiunto ad Azure Key Vault usando il relativo URI. Usare **"https://<your-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate"** per ottenere la versione corrente. 

Per visualizzare il certificato archiviato in precedenza:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

A questo punto è stata creata un'istanza di Key Vault nella quale è stato archiviato e recuperato un certificato.

**Risoluzione dei problemi:**

L'operazione ha restituito un codice di stato non valido 'Forbidden'

Se viene visualizzato questo errore, l'account che accede Azure Key Vault non dispone delle autorizzazioni appropriate per creare i certificati.

Eseguire il comando Azure PowerShell seguente per assegnare le autorizzazioni appropriate:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> -ObjectId <AzureObjectID> -PermissionsToCertificates get,list,update,create
```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un certificato. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere le informazioni di riferimento sui [cmdlet di Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Vedere [Panoramica della sicurezza di Azure Key Vault](../general/security-features.md)
