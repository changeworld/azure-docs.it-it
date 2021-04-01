---
author: baanders
description: file di inclusione per la pulizia di un'istanza di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102244469"
---
* **Se non è necessaria alcuna delle risorse create in questa esercitazione**, è possibile eliminare l'istanza di Azure Digital Twins e tutte le altre risorse di questo articolo con il comando [AZ Group Delete](/cli/azure/group#az-group-delete) . Verranno eliminate tutte le risorse di Azure in un gruppo di risorse, nonché il gruppo di risorse stesso.
    
    > [!IMPORTANT]
    > L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate.
    
    Aprire [Azure cloud Shell](https://shell.azure.com)ed eseguire il comando seguente per eliminare il gruppo di risorse e tutto ciò che contiene.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```