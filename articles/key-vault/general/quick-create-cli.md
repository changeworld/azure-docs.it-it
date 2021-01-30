---
title: Guida introduttiva - Creare un'istanza di Azure Key Vault tramite l'interfaccia della riga di comando di Azure
description: Questa guida introduttiva mostra come creare un'istanza di Azure Key Vault usando l'interfaccia della riga di comando di Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: f7f6f5d82c5fda7101e80ddcb8b17dc6bdef6532
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070301"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Avvio rapido: Creare un insieme di credenziali delle chiavi usando l'interfaccia della riga di comando di Azure

Azure Key Vault è un servizio cloud che offre uno spazio di archiviazione protetto per [chiavi](../keys/index.yml), [segreti](../secrets/index.yml) e [certificati](../certificates/index.yml). Per altre informazioni sul servizio Key Vault, vedere [Informazioni su Azure Key Vault](overview.md). Per altre informazioni sugli elementi che è possibile archiviare in un insieme di credenziali delle chiavi, vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa guida di avvio rapido richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva viene creata e quindi eliminata un'istanza del servizio Key Vault. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](overview.md)
- Vedere [Panoramica della sicurezza di Azure Key Vault](security-overview.md)
- Vedere le informazioni di riferimento sui [comandi az keyvault dell'interfaccia della riga di comando di Azure](/cli/azure/keyvault)

