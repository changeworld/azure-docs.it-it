---
title: Interfaccia della riga di comando - Creare un'immagine da uno snapshot o da un disco gestito in una raccolta di immagini condivise
description: Informazioni su come creare un'immagine da uno snapshot o da un disco gestito in una raccolta di immagini condivise usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2dc6d99b8b1c913479fc584b52f6ff919dfac675
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792292"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Creare un'immagine da un disco gestito o uno snapshot in una raccolta di immagini condivise usando l'interfaccia della riga di comando di Azure

Se si dispone di uno snapshot o di un disco gestito esistente di cui si vuole eseguire la migrazione in una raccolta di immagini condivise, è possibile creare un'immagine della raccolta immagini condivise direttamente dal disco gestito o dallo snapshot. Dopo aver testato la nuova immagine, è possibile eliminare il disco gestito o lo snapshot di origine. È anche possibile creare un'immagine da un disco gestito o uno snapshot in una raccolta di immagini condivise usando il [Azure PowerShell](image-version-snapshot-powershell.md).

Le immagini in una raccolta di immagini hanno due componenti, che verranno creati in questo esempio:
- Una **definizione di immagine** contiene informazioni sull'immagine e i requisiti per usarla. Ciò include se l'immagine è Windows o Linux, specializzata o generalizzata, note sulla versione e requisiti minimi e massimi di memoria. Si tratta della definizione di un tipo di immagine. 
- Una **versione dell'immagine** viene usata per creare una macchina virtuale quando si usa una raccolta di immagini condivise. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Quando si crea una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte.


## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, è necessario disporre di uno snapshot o di un disco gestito. 

Se si vuole includere un disco dati, le dimensioni del disco dati non possono essere superiori a 1 TB.

Quando si lavora con questo articolo, sostituire i nomi delle risorse dove necessario.

## <a name="find-the-snapshot-or-managed-disk"></a>Trovare lo snapshot o il disco gestito 

È possibile visualizzare un elenco di snapshot disponibili in un gruppo di risorse usando [az snapshot list](/cli/azure/snapshot#az_snapshot_list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

È anche possibile usare un disco gestito anziché uno snapshot. Per ottenere un disco gestito, usare [az disk list](/cli/azure/disk#az_disk_list). 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Dopo aver creato l'ID dello snapshot o del disco gestito, assegnarlo a una variabile denominata `$source` da usare in un secondo momento.

È possibile usare lo stesso processo per ottenere tutti i dischi dati da includere nell'immagine. Assegnarle alle variabili, quindi usarle in un secondo momento quando si crea la versione dell'immagine.


## <a name="find-the-gallery"></a>Trovare la raccolta

Per creare la definizione dell'immagine, sono necessarie informazioni sulla raccolta immagini.

Elencare le informazioni sulle raccolte di immagini disponibili usando [az sig list](/cli/azure/sig#az_sig_list). Prendere nota del nome della raccolta in cui si trova il gruppo di risorse per usarlo in un secondo momento.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine

Le definizioni di immagini creano un raggruppamento logico per le immagini. Vengono usati per gestire le informazioni sull'immagine. I nomi delle definizioni di immagini possono essere costituiti da lettere maiuscole o minuscole, numeri, trattini e punti. 

Quando si crea la definizione dell'immagine, assicurarsi che abbia tutte le informazioni corrette. In questo esempio si presuppone che lo snapshot o il disco gestito sia stato creato da una macchina virtuale in uso e che non sia stata generalizzata. Se il disco gestito o lo snapshot è stato creato di un sistema operativo generalizzato (dopo l'esecuzione di Sysprep per Windows o [waagent](https://github.com/Azure/WALinuxAgent) o `-deprovision` per `-deprovision+user` Linux), modificare `-OsState` in `generalized` . 

Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](./shared-image-galleries.md#image-definitions).

Creare una definizione di immagine nella raccolta usando [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

In questo esempio la definizione di immagine è denominata *myImageDefinition* ed è relativa a un'immagine [specializzata](./shared-image-galleries.md#generalized-and-specialized-images) del sistema operativo Linux. Per creare una definizione per le immagini usando un sistema operativo Windows, usare `--os-type Windows`. 

In questo esempio la raccolta è denominata *myGallery*, si trova nel gruppo di risorse *myGalleryRG* e il nome della definizione dell'immagine sarà *mImageDefinition*.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Creare una versione di immagine

Creare una versione dell'immagine [usando az image gallery create-image-version.](/cli/azure/sig/image-version#az_sig_image_version_create) 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio la versione dell'immagine è *1.0.0* e si  creerà 1 replica nell'area Stati Uniti centro-meridionali e 1 replica nell'area Stati Uniti orientali *2* usando l'archiviazione con ridondanza della zona. Quando si scelgono le aree di destinazione per  la replica, tenere presente che è necessario includere anche l'area di origine del disco gestito o dello snapshot come destinazione per la replica.

Passare l'ID dello snapshot o del disco gestito nel `--os-snapshot` parametro .


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Se si vogliono includere dischi dati nell'immagine, è necessario includere sia il parametro impostato sul numero LUN che l'id del disco dati o `--data-snapshot-luns` `--data-snapshots` dello snapshot.

> [!NOTE]
> È necessario attendere che la creazione della versione dell'immagine venga interamente completata e replicata prima di poter usare la stessa immagine gestita o creare un'altra versione di immagine.
>
> È anche possibile archiviare tutte le [](../storage/common/storage-redundancy.md) repliche della versione dell'immagine nell'archiviazione con ridondanza della zona aggiungendo `--storage-account-type standard_zrs` quando si crea la versione dell'immagine.
>

## <a name="next-steps"></a>Passaggi successivi

Creare una macchina virtuale da una [versione di immagine specializzata.](vm-specialized-image-version-cli.md)

Per informazioni su come fornire informazioni sul piano di acquisto, vedere Fornire Azure Marketplace informazioni sul piano di acquisto [durante la creazione di immagini.](marketplace-images.md)