---
title: includere file
description: includere file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: b966f68e19794aadebff76e3e9b29ed79a32eebe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800076"
---
## <a name="update-resources"></a>Aggiornare le risorse

Esistono alcune limitazioni su ciò che è possibile aggiornare. È possibile aggiornare gli elementi seguenti: 

Raccolta di immagini condivise:
- Descrizione

Definizione delle immagini:
- VCPU consigliati
- Memoria consigliata
- Descrizione
- Data di scadenza

Versione immagine:
- Conteggio di repliche a livello di area
- Aree di destinazione
- Esclusione dalla versione più recente
- Data di scadenza

Se si prevede di aggiungere aree di replica, non eliminare l'immagine gestita di origine. L'immagine gestita di origine è necessaria per replicare la versione dell'immagine in aree aggiuntive. 

Aggiornare la descrizione di una raccolta usando ([az sig update](/cli/azure/sig#az_sig_update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Aggiornare la descrizione di una definizione di immagine [usando az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Aggiornare una versione dell'immagine per aggiungere un'area in cui eseguire la replica usando [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update). Questa modifica può richiedere del tempo quando l'immagine viene replicata nella nuova area.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Questo esempio illustra come usare [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) per escludere questa versione dell'immagine dall'uso come *immagine più* recente.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Questo esempio illustra come usare [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) per includere questa versione dell'immagine in da considerare per l'immagine *più* recente.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Eliminare le risorse

È necessario eliminare le risorse in ordine inverso, eliminando prima la versione dell'immagine. Dopo aver eliminato tutte le versioni dell'immagine, è possibile eliminare la definizione dell'immagine. Dopo aver eliminato tutte le definizioni dell'immagine, è possibile eliminare la raccolta. 

Eliminare una versione dell'immagine [usando az sig image-version delete](/cli/azure/sig/image-version#az_sig_image_version_delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Eliminare una definizione di immagine usando [az sig image-definition delete](/cli/azure/sig/image-definition#az_sig_image_definition_delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Eliminare una raccolta di immagini [usando az sig delete](/cli/azure/sig#az_sig_delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```