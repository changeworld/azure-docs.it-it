---
title: Serie HB
description: Specifiche per le VM serie HB.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: f0f5d8f5e5e7077c3438005b58d6ed1a132219f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566023"
---
# <a name="hb-series"></a>Serie HB

Le VM serie HB sono ottimizzate per le applicazioni basate sulla larghezza di banda della memoria, ad esempio fluidodinamica, analisi degli elementi finiti espliciti e modellazione meteorologica. Macchine virtuali HB funzionalità 60 processori AMD EPYC 7551 core, 4 GB di RAM per core CPU e nessun multithreading simultaneo. Una macchina virtuale HB fornisce fino a 260 GB al secondo di larghezza di banda di memoria.

VM serie HB funzionalità 100 GB/sec Mellanox EDR InfiniBand. Queste VM sono connesse in un albero Fat senza blocco per prestazioni RDMA ottimizzate e coerenti. Queste macchine virtuali supportano il routing adattivo e il trasporto con connessione dinamica (DCT, oltre ai trasporti standard RC e UD). Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e il loro utilizzo è consigliato.

[ACU](acu.md): 199-216<br>
[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): non supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): non supportato<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>
[Rete accelerata](../virtual-network/create-vm-accelerated-networking-cli.md): supportata ([altre](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) informazioni sulle prestazioni e sui potenziali problemi) <br>
[Dischi del sistema operativo temporaneo](ephemeral-os-disks.md): non supportati <br>
<br>

| Dimensione | vCPU | Processore | Memoria (GiB) | Larghezza di banda di memoria (GB/sec) | Frequenza CPU di base (GHz) | Frequenza di tutti i core (GHz, picco) | Frequenza a core singolo (GHz, picco) | Prestazioni RDMA (GB/sec) | Supporto MPI | Spazio di archiviazione temp (GiB) | Numero massimo di dischi dati | Numero massimo di schede Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 228 | 263 | 2.0 | 2.55 | 2.55 | 100 | Tutti | 700 | 4 | 8 |

Scopri di più sull' [architettura](./workloads/hpc/hb-series-overview.md)sottostante e [sulle prestazioni](./workloads/hpc/hb-series-performance.md) previste della VM serie HB.

[!INCLUDE [hpc-include.md](./workloads/hpc/includes/hpc-include.md)]

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
