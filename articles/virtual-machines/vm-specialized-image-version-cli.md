---
title: Creare una macchina virtuale da una versione specializzata dell'immagine usando l'interfaccia della riga di comando di Azure
description: Creare una macchina virtuale usando una versione specializzata dell'immagine in una raccolta di immagini condivise usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3498037f3d2088459784ab066b8e94ba344a275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792184"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Creare una macchina virtuale usando una versione specializzata dell'immagine con l'interfaccia della riga di comando di Azure

Creare una macchina virtuale da una [versione specializzata dell'immagine](./shared-image-galleries.md#generalized-and-specialized-images) archiviata in una raccolta di immagini condivise. Per creare una macchina virtuale usando una versione generalizzata dell'immagine, vedere Creare una [macchina virtuale da una versione generalizzata dell'immagine](vm-generalized-image-version-cli.md).

Sostituire i nomi delle risorse di questo esempio secondo necessità. 

Elencare le definizioni di immagini in una raccolta usando [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) per visualizzare il nome e l'ID delle definizioni.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Per creare la macchina virtuale, usare [az vm create](/cli/azure/vm#az_vm_create) con il parametro --specialized per indicare che si tratta di un'immagine specializzata. 

Usare l'ID definizione immagine per `--image` per creare la macchina virtuale dalla versione più recente dell'immagine disponibile. È anche possibile creare la macchina virtuale da una versione specifica fornendo l'ID versione dell'immagine per `--image`. 

In questo esempio viene creata una macchina virtuale dalla versione più recente dell'immagine *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Passaggi successivi
[Azure Image Builder (anteprima)](./image-builder-overview.md) consente di automatizzare la creazione della versione dell'immagine. È anche possibile usarla per aggiornare e creare una nuova versione dell'immagine da [una versione di immagine esistente.](./linux/image-builder-gallery-update-image-version.md) 

È anche possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Creare una macchina virtuale dalla versione dell'immagine](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)