---
title: Come eseguire il mapping di dischi di Azure a dischi Guest di macchine virtuali Linux
description: Come determinare i dischi di Azure che hanno sottoposto a dischi Guest di una macchina virtuale Linux.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.collection: linux
ms.openlocfilehash: bc6c6273ab3d1a4403763e4ed0a8c491995fb2df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556724"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Come eseguire il mapping di dischi di Azure a dischi Guest di macchine virtuali Linux

Potrebbe essere necessario determinare i dischi di Azure che eseguono il backup dei dischi Guest di una macchina virtuale. In alcuni scenari è possibile confrontare le dimensioni del disco o del volume con le dimensioni dei dischi di Azure collegati. Negli scenari in cui sono presenti più dischi di Azure della stessa dimensione collegati alla macchina virtuale, è necessario usare il numero di unità logica (LUN) dei dischi dati. 

## <a name="what-is-a-lun"></a>Che cos'è un LUN?

Un numero di unità logica (LUN) è un numero usato per identificare un dispositivo di archiviazione specifico. A ogni dispositivo di archiviazione viene assegnato un identificatore numerico univoco, a partire da zero. Il percorso completo di un dispositivo è rappresentato dal numero di bus, dal numero di ID di destinazione e dal numero di unità logica (LUN). 

Ad esempio: ***numero bus 0, ID destinazione 0, lun 3***

Per questo esercizio, è sufficiente usare il LUN.

## <a name="finding-the-lun"></a>Ricerca del LUN

Di seguito sono elencati due metodi per trovare il LUN di un disco in Linux.

### <a name="lsscsi"></a>lsscsi

1. Connettersi alla VM
1. `sudo lsscsi`

La prima colonna elencata conterrà il LUN, il formato è [host: Channel: target:**lun**].

### <a name="listing-block-devices"></a>Elenco dei dispositivi a blocchi

1. Connettersi alla VM
1. `sudo ls -l /sys/block/*/device`

L'ultima colonna elencata conterrà il LUN, il formato è [host: Channel: target:**lun**]

## <a name="finding-the-lun-for-the-azure-disks"></a>Ricerca del LUN per i dischi di Azure

È possibile individuare il LUN per un disco di Azure usando il portale di Azure, l'interfaccia della riga di comando di Azure.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Ricerca di un LUN del disco di Azure nell'portale di Azure

1. Nella portale di Azure selezionare "macchine virtuali" per visualizzare un elenco delle macchine virtuali
1. Selezionare la macchina virtuale.
1. Selezionare "dischi"
1. Selezionare un disco dati dall'elenco dei dischi collegati.
1. Il LUN del disco verrà visualizzato nel riquadro dei dettagli del disco. Il LUN visualizzato qui è correlato ai Lun cercati nel guest usando lsscsi o elencando i dispositivi a blocchi.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Trovare un LUN del disco di Azure con l'interfaccia della riga di comando di Azure

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
