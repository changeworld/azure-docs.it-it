---
title: includere file
description: includere file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 83d70a8d4c5806120ddb4ea776a8f4a6f7e63857
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799862"
---
## <a name="list-information"></a>Informazioni di un elenco

Ottenere il percorso, lo stato e altre informazioni sulle raccolte immagini disponibili tramite [elenco az sig](/cli/azure/sig#az_sig_list).

```azurecli-interactive 
az sig list -o table
```

Elencare le definizioni delle immagini in una raccolta, tra cui informazioni sul tipo di sistema operativo e sullo stato, usando [elenco di definizione dell'immagine di az sig](/cli/azure/sig/image-definition#az_sig_image_definition_list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Elencare le versioni immagine condivise in una raccolta, usando [elenco versione immagine di az sig](/cli/azure/sig/image-version#az_sig_image_version_list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Ottenere l'ID di una versione immagine usando [mostra versione dell'immagine di az sig](/cli/azure/sig/image-version#az_sig_image_version_show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
