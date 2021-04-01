---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244816"
---
Creare un segreto denominato **mySecret**, con un valore **Success!** . Un segreto può essere una password, una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro e rendere allo stesso tempo disponibile per l'applicazione. 

Per aggiungere un segreto all'insieme di credenziali delle chiavi appena creato, usare il comando [az keyvault secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) dell'interfaccia della riga di comando di Azure:

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```