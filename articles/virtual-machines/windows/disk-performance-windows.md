---
title: Prestazioni delle macchine virtuali e del disco-Windows
description: Altre informazioni sul funzionamento delle macchine virtuali e dei dischi collegati in combinazione con le prestazioni di Windows.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584120"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Prestazioni delle macchine virtuali e del disco (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Macchine virtuali non memorizzate nella cache rispetto ai limiti memorizzati nella cache
 Le macchine virtuali che sono abilitate per l'archiviazione Premium e la memorizzazione nella cache di archiviazione Premium hanno due limiti di larghezza di banda di archiviazione diversi. Si procederà con la ricerca della macchina virtuale Standard_D8s_v3 come esempio. Di seguito è illustrata la documentazione relativa alla [serie Dsv3](../dv3-dsv3-series.md) e al Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Eseguiamo un test di benchmarking sulla macchina virtuale e sulla combinazione di dischi che creerà l'attività di i/o e potrai scoprire tutte le informazioni su come eseguire il benchmarking di i/o di archiviazione in Azure [qui](disks-benchmarks.md). Dallo strumento di benchmarking è possibile vedere che la combinazione di VM e dischi è in grado di raggiungere 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]