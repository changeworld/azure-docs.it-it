---
title: Assegnare un criterio Azure Key Vault di accesso remoto (interfaccia della riga di comando)
description: Come usare l'interfaccia della riga di comando di Azure per assegnare un criterio Key Vault di accesso a un'entità di sicurezza o a un'identità dell'applicazione.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 96b4daa027871201a201b253721114372e58f377
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751437"
---
# <a name="assign-a-key-vault-access-policy"></a>Assegnare un criterio Key Vault di accesso

I Key Vault di accesso di sicurezza determinano se un'entità di sicurezza specifica, in modo [](../secrets/index.yml)specifico [](../keys/index.yml)un utente, un'applicazione o un gruppo di utenti, può eseguire operazioni diverse su segreti, chiavi e certificati di [Key Vault.](../certificates/index.yml) È possibile assegnare i criteri di accesso usando [portale di Azure](assign-access-policy-portal.md), l'interfaccia della riga di comando di Azure (questo articolo) [o Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Per altre informazioni sulla creazione di gruppi in Azure Active Directory'interfaccia della riga di comando di Azure, vedere [az ad group create](/cli/azure/ad/group#az-ad-group-create) e [az ad group member add.](/cli/azure/ad/group/member#az-ad-group-member-add)

## <a name="configure-the-azure-cli-and-sign-in"></a>Configurare l'interfaccia della riga di comando di Azure ed eseguire l'accesso

1. Per eseguire i comandi dell'interfaccia della riga di comando di Azure in locale, installare l'interfaccia della [riga di comando di Azure.](/cli/azure/install-azure-cli)
 
    Per eseguire i comandi direttamente nel cloud, usare il [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Solo interfaccia della riga di comando locale: accedere ad Azure usando `az login` :

    ```bash
    az login
    ```

    Il `az login` comando apre una finestra del browser per raccogliere le credenziali, se necessario.

## <a name="acquire-the-object-id"></a>Acquisire l'ID oggetto

Determinare l'ID oggetto dell'applicazione, del gruppo o dell'utente a cui si vogliono assegnare i criteri di accesso:

- Applicazioni e altre entità servizio: usare [il comando az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) per recuperare le entità servizio. Esaminare l'output del comando per determinare l'ID oggetto dell'entità di sicurezza a cui si vogliono assegnare i criteri di accesso.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Gruppi: usare il [comando az ad group list,](/cli/azure/ad/group#az-ad-group-list) filtrando i risultati con il `--display-name` parametro :

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Utenti: usare il [comando az ad user show,](/cli/azure/ad/user#az-ad-user-show) passando l'indirizzo di posta elettronica dell'utente nel `--id` parametro :

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Assegnare i criteri di accesso
    
Usare il [comando az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) per assegnare le autorizzazioni desiderate:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Sostituire `<object-id>` con l'ID oggetto dell'entità di sicurezza.

È necessario includere solo `--secret-permissions` , e quando si `--key-permissions` `--certificate-permissions` assegnano autorizzazioni a questi tipi specifici. I valori consentiti per `<secret-permissions>` , e sono specificati nella documentazione di `<key-permissions>` `<certificate-permissions>` [az keyvault set-policy.](/cli/azure/keyvault#az-keyvault-set-policy)

## <a name="next-steps"></a>Passaggi successivi

- [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](security-overview.md#identity-management)
- [Proteggere l'insieme di credenziali delle chiavi.](security-overview.md)
- [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)