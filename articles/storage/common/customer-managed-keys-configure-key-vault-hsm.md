---
title: Configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito (anteprima)
titleSuffix: Azure Storage
description: Informazioni su come configurare la Archiviazione di Azure con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito (anteprima) tramite l'interfaccia della riga di comando di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9b40c934cb428a31a3feb77195518d5351818d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785362"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito (anteprima)

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un controllo aggiuntivo sulle chiavi di crittografia, è possibile gestire le proprie chiavi. Le chiavi gestite dal cliente devono essere archiviate in Azure Key Vault o Key Vault HSM (Managed Hardware Security Model) (anteprima). Un Azure Key Vault HSM gestito è un HSM convalidato FIPS 140-2 livello 3.

Questo articolo illustra come configurare la crittografia con chiavi gestite dal cliente archiviate in un modulo di protezione hsm gestito tramite l'interfaccia della riga di comando di Azure. Per informazioni su come configurare la crittografia con chiavi gestite dal cliente archiviate in un insieme di credenziali delle chiavi, vedere Configurare la crittografia con chiavi gestite dal cliente [archiviate in Azure Key Vault](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> La crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito è attualmente disponibile in **ANTEPRIMA.** Per le condizioni legali applicabili alle funzionalità di Azure in versione beta, in anteprima o altrimenti non ancora rilasciate nella disponibilità [generale, vedere](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Le condizioni per l'utilizzo supplementari per le Microsoft Azure preview.
>
> Azure Key Vault e Azure Key Vault HSM gestito supportano le stesse API e le stesse interfacce di gestione per la configurazione.

## <a name="assign-an-identity-to-the-storage-account"></a>Assegnare un'identità all'account di archiviazione

Assegnare prima un'identità gestita assegnata dal sistema all'account di archiviazione. Si userà questa identità gestita per concedere all'account di archiviazione le autorizzazioni per accedere al servizio HSM gestito. Per altre informazioni sulle identità gestite assegnate dal sistema, vedere [Che cosa sono le identità gestite per le risorse di Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

Per assegnare un'identità gestita tramite l'interfaccia della riga di comando di Azure, chiamare [az storage account update](/cli/azure/storage/account#az_storage_account_update). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Assegnare un ruolo all'account di archiviazione per l'accesso al servizio HSM gestito

Assegnare quindi il **ruolo Managed HSM Crypto Service Encryption** all'identità gestita dell'account di archiviazione in modo che l'account di archiviazione abbia le autorizzazioni per il servizio HSM gestito. Microsoft consiglia di assegnare l'ambito dell'assegnazione di ruolo al livello della singola chiave per concedere il numero più basso possibile di privilegi all'identità gestita.

Per creare l'assegnazione di ruolo per l'account di archiviazione, chiamare [az key vault role assignment create](/cli/azure/role/assignment#az_role_assignment_create). Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Configurare la crittografia con una chiave nel HSM gestito

Infine, configurare Archiviazione di Azure crittografia con chiavi gestite dal cliente per l'uso di una chiave archiviata nel HSM gestito. I tipi di chiave supportati includono chiavi RSA-HSM di dimensioni 2048, 3072 e 4096. Per informazioni su come creare una chiave in un HSM gestito, vedere [Creare una chiave HSM.](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key)

Installare l'interfaccia della riga di comando di Azure 2.12.0 o versione successiva per configurare la crittografia per l'uso di una chiave gestita dal cliente in un modulo di protezione software gestito. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per aggiornare automaticamente la versione della chiave per una chiave gestita dal cliente, omettere la versione della chiave quando si configura la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Chiamare [az storage account update per](/cli/azure/storage/account#az_storage_account_update) aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente. Includere e `--encryption-key-source parameter` impostarlo su per `Microsoft.Keyvault` abilitare le chiavi gestite dal cliente per l'account. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Per aggiornare manualmente la versione per una chiave gestita dal cliente, includere la versione della chiave quando si configura la crittografia per l'account di archiviazione:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Quando si aggiorna manualmente la versione della chiave, è necessario aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione. Per prima cosa, eseguire una query per l'URI dell'insieme di credenziali delle chiavi chiamando [az keyvault show](/cli/azure/keyvault#az_keyvault_show)e per la versione della chiave chiamando [az keyvault key list-versions.](/cli/azure/keyvault/key#az_keyvault_key_list_versions) Chiamare quindi [az storage account update](/cli/azure/storage/account#az_storage_account_update) per aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione della chiave, come illustrato nell'esempio precedente.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Chiavi gestite dal cliente per la Archiviazione di Azure dati](customer-managed-keys-overview.md)
