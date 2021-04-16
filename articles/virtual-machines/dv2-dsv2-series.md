---
title: Serie Dv2 e DSv2 - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie Dv2 e Dsv2.
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 71a4cebcc11657566f65f53508df18efe822c409
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514785"
---
# <a name="dv2-and-dsv2-series"></a>Serie Dv2 e DSv2

Le serie Dv2 e DSv2, che seguono la serie D originale, presentano una CPU più potente e una configurazione ottimale da CPU a memoria che le rende adatte alla maggior parte dei carichi di lavoro di produzione. La serie Dv2 è circa il 35% più veloce rispetto alla serie D. Serie Dv2 eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® ® E5-2673 v4 da 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) con Intel Turbo Boost Technology 2.0. La serie Dv2 ha le stesse configurazioni di memoria e disco della serie D.

## <a name="dv2-series"></a>Serie Dv2

Le dimensioni della serie Dv2 vengono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon®® <2> <2> processori E5-2673 v4 a 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 v3 da 2,4 GHz (Haswell) con tecnologia Intel Turbo Boost 2.0.

[ACU](acu.md): 210-250<br>
[Archiviazione Premium](premium-storage-performance.md): Non supportato<br>
[Archiviazione Premium memorizzazione nella cache:](premium-storage-performance.md)non supportata<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Mantenimento della memoria degli aggiornamenti](maintenance-and-updates.md): supportato<br>
[Supporto per la generazione di](generation-2.md)macchine virtuali: generazione 1<br>
[Rete accelerata:](../virtual-network/create-vm-accelerated-networking-cli.md)supportato (*richiede almeno 2 vCPU)*<br>
[Dischi del sistema operativo ffimeri:](ephemeral-os-disks.md)non supportati <br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Velocità effettiva massima di archiviazione temporanea: operazioni di I/O al secondo/MBps di lettura/scrittura MBps | Numero massimo di dischi dati | Velocità effettiva: operazioni di I/O al secondo | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4x500  | 2|750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2|1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4|3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8|6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8|12000 |

## <a name="dsv2-series"></a>Serie DSv2

Le dimensioni della serie DSv2 vengono eseguite su Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2,1 GHz (Skylake) o Intel® Xeon® ® processori E5-2673 v4 da 2,3 GHz (Broadwell) o Intel® Xeon® E5-2673 v3 da 2,4 GHz (Haswell) con Intel Turbo Boost Technology 2.0 e usare l'archiviazione Premium.

[ACU](acu.md): 210-250<br>
[Archiviazione Premium](premium-storage-performance.md): supportato<br>
[Archiviazione Premium memorizzazione nella cache:](premium-storage-performance.md)supportata<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria:](maintenance-and-updates.md)supportati<br>
[Supporto per la generazione di](generation-2.md)macchine virtuali: generazione 1 e 2<br>
[Rete accelerata:](../virtual-network/create-vm-accelerated-networking-cli.md)supportata *(richiede almeno 2 vCPU)*<br>
[Dischi del sistema operativo phemeral:](ephemeral-os-disks.md)supportati <br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzata nella cache: IOPS/MBps (dimensioni della cache in GiB) | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3,5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2|750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2|1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4|3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8|6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8|12000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Altre dimensioni e informazioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

Calcolatore prezzi: [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)

Altre informazioni sui tipi di dischi: [Tipi di disco](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
