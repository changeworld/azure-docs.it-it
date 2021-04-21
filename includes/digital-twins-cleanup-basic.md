---
author: baanders
description: File di inclusione per la pulizia di un'Gemelli digitali di Azure esistente
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800303"
---
* **Se non sono necessarie le** risorse create in questa esercitazione, è possibile eliminare l'istanza di Gemelli digitali di Azure e tutte le altre risorse da questo articolo con il [comando az group delete.](/cli/azure/group#az_group_delete) In questo modo vengono eliminate tutte le risorse di Azure in un gruppo di risorse, nonché il gruppo di risorse stesso.
    
    > [!IMPORTANT]
    > L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate.
    
    Aprire [Azure Cloud Shell](https://shell.azure.com)ed eseguire il comando seguente per eliminare il gruppo di risorse e tutti gli elementi in esso contenuti.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```