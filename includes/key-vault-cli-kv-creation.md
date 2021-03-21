---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: a87fbbb276fd8813492cc293bc08b958ee3d6b7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070283"
---
Usare l'interfaccia della riga di comando di Azure [AZ le create](/cli/azure/keyvault#az_keyvault_create) per creare una Key Vault nel gruppo di risorse del passaggio precedente. Sarà necessario specificare alcune informazioni:

- Nome dell'insieme di credenziali delle chiavi: Stringa composta da 3 a 24 caratteri che può contenere solo numeri (0-9), lettere (a-z, A-Z) e trattini (-)

  > [!Important]
  > Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

- Nome del gruppo di risorse: **myResourceGroup**.
- Posizione: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

L'output di questo comando Mostra le proprietà dell'insieme di credenziali delle chiavi appena creato. Prendere nota delle due proprietà elencate di seguito:

- **Vault Name**: Il nome specificato per il parametro --name precedente.
- **Vault URI** (URI dell'insieme di credenziali): In questo esempio è https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.
