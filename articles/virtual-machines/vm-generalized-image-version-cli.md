---
title: Creare una macchina virtuale da un'immagine generalizzata usando l'interfaccia della riga di comando di Azure
description: Creare una macchina virtuale da una versione dell'immagine generalizzata usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5e0e5544c5e66f43b56de49beaa3ef3932d33f9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776880"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-azure-cli"></a>Creare una macchina virtuale da una versione di immagine generalizzata usando l'interfaccia della riga di comando di Azure

Creare una macchina virtuale da una [versione generalizzata dell'immagine](./shared-image-galleries.md#generalized-and-specialized-images) archiviata in una raccolta di immagini condivise. Se si vuole creare una macchina virtuale usando un'immagine specializzata, vedere [Creare una macchina virtuale da un'immagine specializzata.](vm-specialized-image-version-powershell.md) 


## <a name="get-the-image-id"></a>Ottenere l'ID immagine

Elencare le definizioni di immagini in una raccolta usando [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) per visualizzare il nome e l'ID delle definizioni.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Creare la macchina virtuale

Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm#az_vm_create). Per usare la versione più recente dell'immagine, `--image` impostare sull'ID della definizione dell'immagine. 

Sostituire i nomi delle risorse di questo esempio secondo necessità. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

È anche possibile usare una versione specifica usando l'ID versione dell'immagine per il `--image` parametro . Ad esempio, per usare la versione *1.0.0* dell'immagine, digitare: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Passaggi successivi

[Azure Image Builder (anteprima)](./image-builder-overview.md) consente di automatizzare la creazione della versione dell'immagine. È anche possibile usarla per aggiornare e creare una nuova versione dell'immagine da una [versione dell'immagine esistente.](./linux/image-builder-gallery-update-image-version.md)