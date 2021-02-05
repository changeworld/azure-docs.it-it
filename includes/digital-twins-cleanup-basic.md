---
author: baanders
description: file di inclusione per la pulizia di un'istanza di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575688"
---
* **Se non è necessaria alcuna delle risorse create in questa esercitazione**, è possibile eliminare l'istanza di Azure Digital Twins e tutte le altre risorse di questo articolo con il comando [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . Verranno eliminate tutte le risorse di Azure in un gruppo di risorse, nonché il gruppo di risorse stesso.
    
    > [!IMPORTANT]
    > L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate.
    
    Aprire [Azure cloud Shell](https://shell.azure.com)ed eseguire il comando seguente per eliminare il gruppo di risorse e tutto ciò che contiene.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```