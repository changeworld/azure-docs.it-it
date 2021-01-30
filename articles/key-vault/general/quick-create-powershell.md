---
title: Avvio rapido - Creare un'istanza di Azure Key Vault con Azure PowerShell
description: Argomento di avvio rapido che mostra come creare un'istanza di Azure Key Vault con Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: e77493bc73bc2d6f590d9bdcf891171fbd71f74e
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070233"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Avvio rapido: Creare un insieme di credenziali delle chiavi con PowerShell

Azure Key Vault è un servizio cloud che offre uno spazio di archiviazione protetto per [chiavi](../keys/index.yml), [segreti](../secrets/index.yml) e [certificati](../certificates/index.yml). Per altre informazioni sul servizio Key Vault, vedere [Informazioni su Azure Key Vault](overview.md). Per altre informazioni sugli elementi che è possibile archiviare in un insieme di credenziali delle chiavi, vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

In questo argomento di avvio rapido viene creato un insieme di credenziali delle chiavi con [Azure PowerShell](/powershell/azure/). Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Digitare `$PSVersionTable.PSVersion` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un insieme di credenziali delle chiavi usando Azure PowerShell. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](overview.md)
- Vedere le informazioni di riferimento sui [cmdlet di Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Vedere [Panoramica della sicurezza di Azure Key Vault](security-overview.md)

