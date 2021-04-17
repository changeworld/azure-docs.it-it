---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 26ae372b6e431247d2038445daafcb3c997a232d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513067"
---
<!--Create a media services account -->

Il comando seguente dell'interfaccia della riga di comando di Azure consente di creare un nuovo account di Servizi multimediali. È possibile sostituire i valori seguenti: (deve corrispondere al valore fornito per l'account di archiviazione) e (deve corrispondere al valore fornito `amsaccount` `storageaccountforams` per il gruppo di `amsResourceGroup` risorse).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```
