---
title: Caricare un disco rigido virtuale in Azure o copiare un disco tra aree - Interfaccia della riga di comando di Azure
description: Informazioni su come caricare un disco rigido virtuale in un disco gestito di Azure e copiare un disco gestito tra aree, usando l'interfaccia della riga di comando di Azure, tramite caricamento diretto.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 285f0acd5097ce68cddee6f732b17944dffb0eba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762570"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Caricare un disco rigido virtuale in Azure o copiare un disco gestito in un'altra area - Interfaccia della riga di comando di Azure

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Prerequisiti

- Scaricare la versione [più recente di AzCopy v10.](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)
- [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
- Se si intende caricare un disco rigido virtuale dall'ambiente locale: un disco rigido virtuale di dimensioni fisse preparato per [Azure](../windows/prepare-for-upload-vhd-image.md), archiviato in locale.
- Oppure, un disco gestito in Azure, se si intende eseguire un'azione di copia.

## <a name="getting-started"></a>Guida introduttiva

Se si preferisce caricare i dischi tramite un'interfaccia utente grafica, è possibile farlo usando Azure Storage Explorer. Per informazioni dettagliate, vedere: [Usare Azure Storage Explorer per gestire i dischi gestiti di Azure](../disks-use-storage-explorer-managed-disks.md)

Per caricare il disco rigido virtuale in Azure, è necessario creare un disco gestito vuoto configurato per questo processo di caricamento. Prima di crearne uno, è necessario conoscere alcune informazioni aggiuntive su questi dischi.

Questo tipo di disco gestito ha due stati univoci:

- ReadToUpload, ovvero il disco è pronto per [](../../storage/common/storage-sas-overview.md) ricevere un caricamento, ma non è stata generata alcuna firma di accesso sicuro .
- ActiveUpload, ovvero il disco è pronto per ricevere un caricamento e la firma di accesso condiviso è stata generata.

> [!NOTE]
> In uno di questi stati, il disco gestito verrà fatturato con i prezzi [hdd standard,](https://azure.microsoft.com/pricing/details/managed-disks/)indipendentemente dal tipo effettivo di disco. Ad esempio, un P10 verrà fatturato come S10. Questo vale fino a quando non viene chiamato sul disco gestito, che è necessario per `revoke-access` collegare il disco a una macchina virtuale.

## <a name="create-an-empty-managed-disk"></a>Creare un disco gestito vuoto

Prima di poter creare un disco rigido standard vuoto per il caricamento, sono necessarie le dimensioni del file del disco rigido virtuale da caricare, in byte. Per ottenere questo, è possibile usare `wc -c <yourFileName>.vhd` o `ls -al <yourFileName>.vhd` . Questo valore viene usato quando si specifica il **parametro --upload-size-bytes.**

Creare un disco rigido standard vuoto per il caricamento specificando sia **il parametro --for-upload** che il **parametro --upload-size-bytes** in un cmdlet [disk create:](/cli/azure/disk#az_disk_create)

Sostituire `<yourdiskname>` , con i valori di propria `<yourresourcegroupname>` `<yourregion>` scelta. Il `--upload-size-bytes` parametro contiene un valore di esempio di , `34359738880` sostituirlo con un valore appropriato.

> [!TIP]
> Se si crea un disco del sistema operativo, aggiungere --hyper-v-generation <yourGeneration> a `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se si vuole caricare un SSD Premium o un SSD standard, sostituire standard_lrs **con** premium_LRS **o** **standardssd_lrs**. I dischi Ultra non sono attualmente supportati.

Dopo aver creato un disco gestito vuoto configurato per il processo di caricamento, è possibile caricarvi un disco rigido virtuale. Per caricare un disco rigido virtuale nel disco, è necessaria una firma di accesso condiviso scrivibile, in modo da potervi fare riferimento come destinazione per il caricamento.

Per generare una firma di accesso condiviso scrivibile del disco gestito vuoto, sostituire `<yourdiskname>` e , quindi usare il comando `<yourresourcegroupname>` seguente:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Valore restituito di esempio:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Caricare il VHD

Ora che si dispone di una firma di accesso condiviso per il disco gestito vuoto, è possibile usarlo per impostare il disco gestito come destinazione per il comando di caricamento.

Usare AzCopy v10 per caricare il file VHD locale in un disco gestito specificando l'URI di firma di accesso condiviso generato.

Questo caricamento ha la stessa velocità effettiva [dell'HDD standard equivalente.](../disks-types.md#standard-hdd) Ad esempio, se si ha una dimensione che equivale a S4, si avrà una velocità effettiva fino a 60 MiB/s. Tuttavia, se si ha una dimensione che equivale a S70, si avrà una velocità effettiva fino a 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Al termine del caricamento e non è più necessario scrivere altri dati sul disco, revocare la firma di accesso condiviso. La revoca della firma di accesso condiviso modificherà lo stato del disco gestito e consentirà di collegare il disco a una macchina virtuale.

Sostituire `<yourdiskname>` e , quindi usare il comando seguente per rendere `<yourresourcegroupname>` utilizzabile il disco:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Copiare un disco gestito

Il caricamento diretto semplifica anche il processo di copia di un disco gestito. È possibile copiare all'interno della stessa area o tra aree (in un'altra area).

Lo script seguente consente di eseguire questa operazione. Il processo è simile ai passaggi descritti in precedenza, con alcune differenze perché si sta lavorando con un disco esistente.

> [!IMPORTANT]
> È necessario aggiungere un offset di 512 quando si specificano le dimensioni del disco in byte di un disco gestito da Azure. Ciò è dovuto al fatto che Azure omette il piè di pagina quando restituisce le dimensioni del disco. In caso negativo, la copia avrà esito negativo. Lo script seguente esegue già questa operazione.

Sostituire , , , e (un esempio di valore di posizione è `<sourceResourceGroupHere>` `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` `<yourTargetLocationHere>` uswest2) con i valori, quindi eseguire lo script seguente per copiare un disco gestito.

> [!TIP]
> Se si sta creando un disco del sistema operativo, aggiungere --hyper-v-generation <yourGeneration> a `az disk create` .

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver caricato correttamente un disco rigido virtuale in un disco gestito, è possibile collegare il disco come disco dati [a](add-disk.md) una macchina virtuale esistente o collegare il disco [a](upload-vhd.md#create-the-vm)una macchina virtuale come disco del sistema operativo per creare una nuova macchina virtuale.