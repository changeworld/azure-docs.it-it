---
title: Creare un set di scalabilità da una versione di immagine specializzata usando l'interfaccia della riga di comando di Azure
description: Creare un set di scalabilità usando una versione di immagine specializzata in una raccolta di immagini condivise usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5fc88c00d548c0a034984976557d316fdac7620f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792346"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Creare un set di scalabilità usando una versione di immagine specializzata con l'interfaccia della riga di comando di Azure

Creare un set di scalabilità da [una versione di immagine specializzata](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) archiviata in una raccolta di immagini condivise. Se si vuole creare un set di scalabilità usando una versione di immagine generalizzata, vedere [Creare un set di scalabilità da un'immagine generalizzata.](instance-generalized-image-version-cli.md)

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa esercitazione è necessario eseguire la versione 2.4.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

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

Creare un set di [`az vmss create`](/cli/azure/vmss#az_vmss_create) scalabilità usando il `--specialized` parametro per indicare che l'immagine è un'immagine specializzata.

Usare l'ID definizione immagine per `--image` per creare le istanze del set di scalabilità dalla versione più recente dell'immagine disponibile. È anche possibile creare le istanze del set di scalabilità da una versione specifica fornendo l'ID versione dell'immagine per `--image`. Tenere presente che l'uso di una versione specifica dell'immagine significa che l'automazione potrebbe non riuscire se la versione specifica dell'immagine non è disponibile perché è stata eliminata o rimossa dall'area. È consigliabile usare l'ID di definizione dell'immagine per creare la nuova macchina virtuale, a meno che non sia necessaria una versione specifica dell'immagine.

In questo esempio vengono create istanze dalla versione più recente *dell'immagine myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## <a name="next-steps"></a>Passaggi successivi
[Azure Image Builder (anteprima)](../virtual-machines/image-builder-overview.md) consente di automatizzare la creazione della versione dell'immagine. È anche possibile usarla per aggiornare e creare una nuova versione dell'immagine da una [versione dell'immagine esistente.](../virtual-machines/linux/image-builder-gallery-update-image-version.md) 

È anche possibile creare una risorsa di raccolta di immagini condivise usando i modelli. Sono disponibili diversi modelli di avvio rapido di Azure: 

- [Creare una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Creare una definizione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Creare una versione dell'immagine in una raccolta di immagini condivise](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)