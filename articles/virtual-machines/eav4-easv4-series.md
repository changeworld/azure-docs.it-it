---
title: Serie Eav4 e serie Easv4
description: Specifiche per le macchine virtuali serie Eav4 e Easv4.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: fc370de11ca429d760ac0440d2bd6ec81044b263
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390148"
---
# <a name="eav4-and-easv4-series"></a>Serie Eav4 e Easv4

Le serie Eav4 e Easv4 usano il processore AMD 2.35 GHz EPYC<sup>TM</sup> 7452 in una configurazione multithread con una cache L3 fino a 256 MB, aumentando le opzioni per l'esecuzione della maggior parte dei carichi di lavoro con ottimizzazione per la memoria. La serie Eav4 e Easv4 hanno le stesse configurazioni di memoria e disco della serie EV3 & Esv3.

## <a name="eav4-series"></a>Serie Eav4

[ACU](acu.md): 230-260<br>
[Archiviazione Premium](premium-storage-performance.md): non supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): non supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazioni 1 e 2<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): supportata<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): supportati <br>
<br>

Le dimensioni della serie Eav4 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere una frequenza massima con boosting di 3.35 GHz. Le dimensioni della serie Eav4 sono ideali per applicazioni aziendali a uso intensivo di memoria. L'archiviazione su disco dati viene fatturata separatamente dalle macchine virtuali. Per usare l'unità SSD Premium, usare le dimensioni della serie Easv4. I prezzi e i contatori di fatturazione per le dimensioni Easv4 sono uguali a quelli della serie Eav3.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea: IOPS/MBps di lettura/MBps di scrittura | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| \_E2a standard \_ V4|2|16|50|4|3000 / 46 / 23|2 | 800 |
| \_E4a standard \_ V4|4|32|100|8|6000 / 93 / 46|2 | 1600 |
| \_E8a standard \_ V4|8|64|200|16|12000 / 187 / 93|4 | 3200 |
| \_E16a standard \_ V4|16|128|400|32|24000 / 375 / 187|8 | 6400 |
| \_E20a standard \_ V4|20|160|500|32|30000/468/234|8 | 8000 |
| \_E32a standard \_ V4|32|256|800|32|48000 / 750 / 375|8 | 12800 |
| \_E48a standard \_ V4|48|384|1200|32|96000/1000 (500)|8 | 19200 |
| \_E64a standard \_ V4|64|512|1600|32|96000/1000 (500)|8 | 25600 |
| \_E96a standard \_ V4|96|672|2400|32|96000/1000 (500)|8 | 32000 |

## <a name="easv4-series"></a>Serie Easv4

[ACU](acu.md): 230-260<br>
[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazioni 1 e 2<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): supportata<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): supportati <br>
<br>

Le dimensioni della serie Easv4 sono basate sul processore 2.35 GHz AMD EPYC<sup>TM</sup> 7452 che può ottenere una frequenza massima con boosting di 3.35 GHz e usare l'unità SSD Premium. Le dimensioni della serie Easv4 sono ideali per applicazioni aziendali a uso intensivo di memoria.

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e memorizzazione nella cache: IOPS/MBps (dimensione della cache espressa in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max | Larghezza di banda di rete prevista (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 | 800 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 | 1600 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 | 3200 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 | 6400 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 | 8000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200)|76800/1148|8 | 19200 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600)|80000/1200|8 | 25600 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000/1020 (2400)|80000/1200|8 | 32000 |

<sup>1</sup> [dimensioni core vincolate disponibili](./constrained-vcpu.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Altre dimensioni e informazioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

Calcolatore prezzi: [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/)

Altre informazioni sui tipi di dischi: [tipi di disco](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
