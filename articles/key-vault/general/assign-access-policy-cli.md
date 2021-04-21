---
title: Assegnare un criterio Azure Key Vault di accesso remoto (CLI)
description: Come usare l'interfaccia della riga di comando di Azure per assegnare un criterio Key Vault di accesso a un'entità di sicurezza o a un'identità dell'applicazione.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 349d7453962a736c9f15bb7d31d5a44098f463a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791950"
---
# <a name="assign-a-key-vault-access-policy"></a>Assegnare un criterio Key Vault di accesso

Un Key Vault di accesso remoto determina se una determinata entità di sicurezza, ad esempio un utente, [](../keys/index.yml)un'applicazione o un gruppo di utenti, può eseguire operazioni diverse su [segreti,](../secrets/index.yml)chiavi e certificati [Key Vault.](../certificates/index.yml) È possibile assegnare criteri di accesso usando portale di Azure [,](assign-access-policy-portal.md)l'interfaccia della riga di comando di Azure [(questo](assign-access-policy-powershell.md)articolo) o Azure PowerShell .

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Per altre informazioni sulla creazione di gruppi in Azure Active Directory'interfaccia della riga di comando di Azure, vedere [az ad group create](/cli/azure/ad/group#az_ad_group_create) e [az ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Configurare l'interfaccia della riga di comando di Azure e accedere

1. Per eseguire i comandi dell'interfaccia della riga di comando di Azure in locale, installare l'interfaccia [della riga di comando di Azure.](/cli/azure/install-azure-cli)
 
    Per eseguire i comandi direttamente nel cloud, usare l'Azure Cloud Shell [.](../../cloud-shell/overview.md)

1. Solo interfaccia della riga di comando locale: accedere ad Azure usando `az login` :

    ```bash
    az login
    ```

    Il `az login` comando apre una finestra del browser per raccogliere le credenziali, se necessario.

## <a name="acquire-the-object-id"></a>Acquisire l'ID oggetto

Determinare l'ID oggetto dell'applicazione, del gruppo o dell'utente a cui si desidera assegnare i criteri di accesso:

- Applicazioni e altre entità servizio: usare il [comando az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) per recuperare le entità servizio. Esaminare l'output del comando per determinare l'ID oggetto dell'entità di sicurezza a cui si desidera assegnare i criteri di accesso.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Gruppi: usare il [comando az ad group list,](/cli/azure/ad/group#az_ad_group_list) filtrando i risultati con il `--display-name` parametro :

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Utenti: usare il [comando az ad user show,](/cli/azure/ad/user#az_ad_user_show) passando l'indirizzo di posta elettronica dell'utente nel `--id` parametro :

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Assegnare i criteri di accesso
    
Usare il [comando az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) per assegnare le autorizzazioni desiderate:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Sostituire `<object-id>` con l'ID oggetto dell'entità di sicurezza.

È necessario includere solo `--secret-permissions` , e quando si `--key-permissions` `--certificate-permissions` assegnano autorizzazioni a questi tipi specifici. I valori consentiti per `<secret-permissions>` , e sono disponibili nella documentazione di `<key-permissions>` `<certificate-permissions>` [az keyvault set-policy.](/cli/azure/keyvault#az_keyvault_set_policy)

## <a name="next-steps"></a>Passaggi successivi

- [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](security-overview.md#identity-management)
- [Proteggere l'insieme di credenziali delle chiavi.](security-overview.md)
- [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)