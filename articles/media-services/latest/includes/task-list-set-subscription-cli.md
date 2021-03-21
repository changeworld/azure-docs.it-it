---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94885350"
---
<!-- List and set subscriptions -->

1. Ottenere un elenco di sottoscrizioni con il comando [az account list](/cli/azure/account#az-account-list):

    ```
    az account list --output table
    ```

2. Usare `az account set` con l'ID o il nome della sottoscrizione alla quale si intende passare.

    ```
    az account set --subscription "My Demos"
    ```
