---
title: Configurare Attestazione di Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come impostare e configurare un provider di attestazioni con l'interfaccia della riga di comando di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ae283785b4d4dc80c6b9b6c3997aaf82c9ff0f2f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178712"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Avvio rapido: Configurare Attestazione di Azure con l'interfaccia della riga di comando di Azure

Introduzione all' [attestazione di Azure](/cli/azure/ext/attestation/attestation)tramite l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="get-started"></a>Introduzione

1. Installare questa estensione usando il comando dell'interfaccia della riga di comando seguente

   ```azurecli
   az extension add --name attestation
   ```
   
1. Controllare la versione

   ```azurecli
   az extension show --name attestation --query version
   ```

1. Usare il comando seguente per accedere ad Azure:

   ```azurecli
   az login
   ```

1. Se necessario, passare alla sottoscrizione per Attestazione di Azure:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Registrare il provider di risorse Microsoft.Attestation nella sottoscrizione con il comando [az provider register](/cli/azure/provider#az_provider_register):

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Per altre informazioni sui provider di risorse di Azure e su come configurarli e gestirli, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > È necessario registrare un provider di risorse una sola volta per una sottoscrizione.

1. Creare un gruppo di risorse per il provider di attestazioni. È possibile inserire altre risorse di Azure, inclusa una macchina virtuale con un'istanza dell'applicazione client, nello stesso gruppo di risorse. Eseguire il comando [az group create](/cli/azure/group#az_group_create) per creare un gruppo di risorse o usarne uno esistente:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Creare e gestire un provider di attestazioni

Ecco i comandi che è possibile usare per creare e gestire il provider di attestazioni:

1. Eseguire il comando [AZ Attestation create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) per creare un provider di attestazione senza requisito di firma dei criteri:

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Eseguire il comando [az attestation show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) per recuperare le proprietà del provider di attestazioni, ad esempio lo stato e AttestURI:

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   Questo comando visualizza valori simili all'output seguente:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

È possibile eliminare un provider di attestazioni usando il comando [az attestation delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete):

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Gestione dei criteri

Usare i comandi descritti qui per fornire la gestione dei criteri per un provider di attestazioni, un tipo di attestazione alla volta.

Il comando [az attestation policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) restituisce il criterio corrente per l'ambiente TEE specificato:

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> Il comando visualizza il criterio sia in formato testo che JWT.

Di seguito sono elencati i tipi di TEE supportati:

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Usare il comando [az attestation policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) per impostare un nuovo criterio per il tipo di attestazione specificato.

Per impostare i criteri in formato testo per un tipo di attestazione specificato usando il percorso del file:

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

Per impostare i criteri in formato JWT per un tipo di attestazione specificato usando il percorso del file:

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Passaggi successivi

- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Implementare l'attestazione con un'enclave SGX usando esempi di codice](/samples/browse/?expanded=azure&terms=attestation)
