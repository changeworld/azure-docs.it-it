---
title: 'Guida introduttiva: impostare e recuperare un segreto da Azure Key Vault'
description: Guida introduttiva che illustra come impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia della riga di comando di Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: e84a9fe15f648311c22fb0f7e8f8374454f5668a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989165"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Guida introduttiva: Impostare e recuperare un segreto da Azure Key Vault usando l'interfaccia della riga di comando di Azure

In questo argomento di avvio rapido viene creato un insieme di credenziali delle chiavi in Azure Key Vault con l'interfaccia della riga di comando di Azure. Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. Per altre informazioni su Key Vault, vedere la [panoramica](../general/overview.md). L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure con comandi o script. Successivamente verrà archiviato un segreto.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault

Per aggiungere un segreto all'insieme di credenziali, sono sufficienti un paio di passaggi aggiuntivi. Questa password può essere usata da un'applicazione. La password sarà denominata **ExamplePassword** e al suo interno verrà archiviato il valore di **hVFkk965BuUv**.

Digitare i comandi seguenti per creare in Key Vault un segreto denominato **ExamplePassword** in cui verrà archiviato il valore **hVFkk965BuUv**:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

È ora possibile fare riferimento a questa password aggiunta ad Azure Key Vault usando il relativo URI. Per ottenere la versione corrente, usare **' https://<il nome-univoco di Vault-name>. Vault.Azure.NET/Secrets/ExamplePassword '** .

Per visualizzare il valore contenuto nel segreto come testo normale:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

A questo punto è stata creata un'istanza di Key Vault nella quale è stato archiviato e recuperato un segreto.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere le informazioni di riferimento sui [comandi az keyvault dell'interfaccia della riga di comando di Azure](/cli/azure/keyvault)
- Vedere [Panoramica della sicurezza di Azure Key Vault](../general/security-overview.md)
