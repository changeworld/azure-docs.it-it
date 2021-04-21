---
title: Copiare una versione dell'immagine da un'altra raccolta usando l'interfaccia della riga di comando
description: Copiare una versione dell'immagine da un'altra raccolta con l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e2cd885d886a0f13783e61a04c7243efdf12967e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784984"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Copiare un'immagine da un'altra raccolta usando l'interfaccia della riga di comando di Azure

Se nell'organizzazione sono presenti più raccolte, è anche possibile creare versioni delle immagini da versioni di immagini esistenti archiviate in altre raccolte. Ad esempio, si potrebbe avere una raccolta di sviluppo e test per la creazione e il test di nuove immagini. Quando sono pronti per l'uso nell'ambiente di produzione, è possibile copiarli in una raccolta di produzione usando questo esempio. È anche possibile creare un'immagine da un'immagine in un'altra raccolta [usando Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, è necessario avere una raccolta di origine, una definizione di immagine e una versione dell'immagine esistenti. È anche necessario disporre di una raccolta di destinazione. 

La versione dell'immagine di origine deve essere replicata nell'area in cui si trova la raccolta di destinazione. 

Quando si lavora con questo articolo, sostituire i nomi delle risorse dove necessario.



## <a name="get-information-from-the-source-gallery"></a>Ottenere informazioni dalla raccolta di origine

Saranno necessarie informazioni dalla definizione dell'immagine di origine per poterne creare una copia nella nuova raccolta.

Elencare le informazioni sulle raccolte di immagini disponibili usando [az sig list](/cli/azure/sig#az_sig_list) per trovare informazioni sulla raccolta di origine.

```azurecli-interactive 
az sig list -o table
```

Elencare le definizioni delle immagini in una raccolta usando [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list). In questo esempio si cercano le definizioni delle immagini nella raccolta *denominata myGallery* nel gruppo di risorse *myGalleryRG.*

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Elencare le versioni di un'immagine in una raccolta usando [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) per trovare la versione dell'immagine che si vuole copiare nella nuova raccolta. In questo esempio si cercano tutte le versioni dell'immagine che fanno parte della definizione dell'immagine *myImageDefinition.*

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Dopo aver creato tutte le informazioni necessarie, è possibile ottenere l'ID della versione dell'immagine di origine [usando az sig image-version show.](/cli/azure/sig/image-version#az_sig_image_version_show)

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Creare la definizione dell'immagine 

È necessario creare una definizione di immagine che corrisponda alla definizione dell'immagine della versione dell'immagine di origine. È possibile visualizzare tutte le informazioni necessarie per ricreare la definizione dell'immagine nella nuova raccolta usando [az sig image-definition show](/cli/azure/sig/image-definition#az_sig_image_definition_show).

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

Verrà visualizzato un risultato simile al seguente:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Creare una nuova definizione di immagine nella nuova raccolta usando le informazioni dell'output precedente.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>Creare una versione di immagine

Creare versioni usando [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). È necessario passare l'ID dell'immagine gestita da usare come baseline per creare la versione dell'immagine. È possibile usare [elenco di immagini di az](/cli/azure/image?view#az_image_list) per ottenere informazioni sulle immagini in un gruppo di risorse. 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio la versione dell'immagine è *1.0.0* e si  creerà 1 replica nell'area Stati Uniti centro-meridionali e 1 replica nell'area Stati Uniti orientali usando l'archiviazione con ridondanza della zona. 


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> È necessario attendere che la creazione della versione dell'immagine venga interamente completata e replicata prima di poter usare la stessa immagine gestita o creare un'altra versione di immagine.
>
> È anche possibile archiviare l'immagine nell'archiviazione Premium aggiungendo o Archiviazione con ridondanza della zona aggiungendo `--storage-account-type  premium_lrs` quando si crea la versione [](../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` dell'immagine.
>

## <a name="next-steps"></a>Passaggi successivi

Creare una macchina virtuale da [una versione generalizzata](vm-generalized-image-version-cli.md) o [specializzata](vm-specialized-image-version-cli.md) dell'immagine.

Provare anche [Azure Image Builder (anteprima)](./image-builder-overview.md) per automatizzare la creazione della versione dell'immagine. È anche possibile usarla per aggiornare e creare una nuova versione dell'immagine da una versione [dell'immagine esistente.](./linux/image-builder-gallery-update-image-version.md) 

Per informazioni su come fornire informazioni sul piano di acquisto, vedere Fornire Azure Marketplace informazioni sul piano di acquisto [durante la creazione di immagini.](marketplace-images.md)