---
title: Creare e recuperare attributi di una chiave gestita in Azure Key Vault - Azure PowerShell
description: Guida introduttiva che illustra come impostare e recuperare una chiave gestita da Azure Key Vault usando Azure PowerShell
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom:
- mode-api
ms.openlocfilehash: aa984a8f3899db72ead878e2c4381ea6a080e32d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815433"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Guida introduttiva: Impostare e recuperare una chiave gestita da Azure Key Vault tramite PowerShell

In questa guida di avvio rapido viene creato un insieme di credenziali delle chiavi in Azure Key Vault con Azure PowerShell. Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. Per altre informazioni su Key Vault, vedere la [panoramica](../general/overview.md). Azure PowerShell viene usato per creare e gestire le risorse di Azure con comandi o script. Successivamente verrà archiviata una chiave.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Digitare `$PSVersionTable.PSVersion` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Usare il Azure PowerShell [cmdlet New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) per creare un gruppo di risorse denominato *myResourceGroup* nella *posizione westus.* 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

L'ID entità verrà restituito nel formato "xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

## <a name="create-a-managed-hsm"></a>Creare un HSM gestito

Usare il Azure PowerShell [cmdlet New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) per creare un nuovo Key Vault HSM gestito. Sarà necessario specificare alcune informazioni:

- Nome HSM gestito: stringa di 3-24 caratteri che può contenere solo numeri (0-9), lettere (a-z, A-Z) e trattini (-)

  > [!Important]
  > Ogni HSM gestito deve avere un nome univoco. Sostituire <your-unique-managed-hsm-name> con il nome del servizio HSM gestito negli esempi seguenti.

- Nome del gruppo di risorse: **myResourceGroup**.
- Posizione: **EastUS**.
- ID entità: passare Azure Active Directory'ID entità di sicurezza ottenuto nell'ultima sezione al parametro "Administrator". 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

L'output di questo cmdlet mostra le proprietà del nuovo HSM gestito creato. Prendere nota delle due proprietà elencate di seguito:

- **Nome HSM gestito:** nome specificato al parametro --name precedente.
- **URI dell'insieme** di credenziali: nell'esempio https:// nome univoco-gestito-hsm &lt; &gt; .vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="activate-your-managed-hsm"></a>Attivare il modulo di protezione hardware gestito

Tutti i comandi del piano dati sono disabilitati fino a quando non viene attivato il servizio HSM. Non sarà possibile creare chiavi o assegnare ruoli. Solo gli amministratori designati assegnati durante il comando di creazione possono attivare il modulo di protezione hardware. Per attivare il modulo di protezione hardware, è necessario scaricare il [dominio di sicurezza](security-domain.md).

Per attivare il modulo di protezione hardware occorre:
- Almeno 3 coppie di chiavi RSA (al massimo 10)
- Specificare il numero minimo di chiavi necessarie per decrittografare il dominio di sicurezza (quorum)

Per attivare il modulo di protezione hardware è necessario inviare almeno 3 (al massimo 10) chiavi pubbliche RSA al modulo stesso. Il modulo di protezione hardware crittografa il dominio di sicurezza con queste chiavi e lo restituisce. Al termine del download del dominio di sicurezza, il modulo di protezione hardware è pronto per l'uso. Occorre anche specificare il quorum, ossia il numero minimo di chiavi private necessarie per decrittografare il dominio di sicurezza.

L'esempio seguente illustra come usare `openssl` (disponibile per Windows [qui)](https://slproweb.com/products/Win32OpenSSL.html)per generare 3 certificati autofirmati.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Creare e archiviare in una posizione sicura le coppie di chiavi RSA e il file del dominio di sicurezza generati in questo passaggio.

Usare il Azure PowerShell [cmdlet Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) per scaricare il dominio di sicurezza e attivare il servizio HSM gestito. L'esempio seguente usa 3 coppie di chiavi RSA (per questo comando sono necessarie solo le chiavi pubbliche) e imposta il quorum su 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Archiviare il file del dominio di sicurezza e le coppie di chiavi RSA in una posizione sicura. Serviranno per il ripristino di emergenza o per la creazione di un altro modulo di protezione hardware gestito che condivide lo stesso dominio di sicurezza, in modo che possano condividere le chiavi.

Dopo il download del dominio di sicurezza, il modulo di protezione hardware sarà attivo e pronto per l'uso.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un certificato. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere le informazioni di riferimento sui [cmdlet di Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Vedere [Panoramica della sicurezza di Azure Key Vault](../general/security-features.md)
