---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967124"
---
1. Usare il comando `az group create` per creare un gruppo di risorse:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    È possibile sostituire "eastus" con una località più vicina, se si preferisce.

1. Usare `az keyvault create` per creare l'insieme di credenziali delle chiavi:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Sostituire `<your-unique-keyvault-name>` con un nome univoco in tutto Azure. In genere si usa il nome personale o dell'azienda insieme ad altri numeri e identificatori. 

1. Creare una variabile di ambiente che fornisce il nome dell'insieme di credenziali delle chiavi al codice:

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```