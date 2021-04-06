---
title: Serie modello HBV3-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie modello HBV3.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: a03eac3969e8918c8da20b90d0dcf8b60b39b8ee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800840"
---
# <a name="hbv3-series"></a>Serie modello HBV3

Le macchine virtuali della serie modello HBV3 sono ottimizzate per le applicazioni HPC, ad esempio fluidodinamica, analisi degli elementi finiti esplicita e implicita, modellazione meteorologica, elaborazione sismica, simulazione del serbatoio e simulazione RTL. Le macchine virtuali modello HBV3 presentano fino a 120 AMD EPYC™ core CPU della serie 7003 (Milano), 448 GB di RAM e senza hyperthreading. Le macchine virtuali della serie modello HBV3 offrono anche 350 GB al secondo di larghezza di banda di memoria, fino a 32 MB di cache L3 per core, fino a 7 GB/s di prestazioni SSD del dispositivo a blocchi e frequenze di clock fino a 3,675 GHz. 

Tutte le macchine virtuali della serie modello HBV3 includono 200 GB/sec HDR InfiniBand da NVIDIA networking per abilitare i carichi di lavoro MPI con scalabilità a livello di computer. Queste VM sono connesse in un albero Fat senza blocco per prestazioni RDMA ottimizzate e coerenti. L'infrastruttura InfiniBand HDR supporta anche il routing adattivo e il trasporto con connessione dinamica (DCT, in aggiunta a trasporti standard RC e UD). Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e il loro utilizzo è fortemente consigliato.

[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): presto disponibile<br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): non supportati <br>
<br>

|Dimensione |vCPU |Processore |Memoria (GiB) |Larghezza di banda di memoria (GB/sec) |Frequenza CPU di base (GHz) |Frequenza di tutti i core (GHz, picco) |Frequenza a core singolo (GHz, picco) |Prestazioni RDMA (GB/sec) |Supporto MPI |Spazio di archiviazione temp (GiB) |Numero massimo di dischi dati |Numero massimo di schede Ethernet |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tutti |2 * 960 |32 |8 |
|Standard_HB120 96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tutti |2 * 960 |32 |8 |
|Standard_HB120 64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tutti |2 * 960 |32 |8 |
|Standard_HB120 32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tutti |2 * 960 |32 |8 |
|Standard_HB120 16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Tutti |2 * 960 |32 |8 |

Ulteriori informazioni su:
- [Architettura e topologia di VM](./workloads/hpc/hbv3-series-overview.md)
- [Stack software](./workloads/hpc/hbv3-series-overview.md#software-specifications) supportato, incluso il sistema operativo supportato
- [Prestazioni](./workloads/hpc/hbv3-performance.md) previste della macchina virtuale serie modello HBV3

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli annunci più recenti, sugli esempi di carico di lavoro HPC e sui risultati delle prestazioni, vedere i [Blog della community tecnica di Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
