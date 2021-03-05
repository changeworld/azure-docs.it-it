---
title: Serie HC-macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie HC.
author: ju-shim
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b0dfd1156e9cc46914f1cc72ce4975ab655855cb
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203088"
---
# <a name="hc-series"></a>Serie HC

Le macchine virtuali della serie HC sono ottimizzate per le applicazioni basate su un calcolo denso, ad esempio l'analisi degli elementi limitati implicita, le dinamiche molecolari e la chimica computazionale. Macchine virtuali HC funzionalità 44 Core processore Intel Xeon Platinum 8168, 8 GB di RAM per core CPU e nessun hyperthreading. La piattaforma Intel Xeon Platinum supporta il ricco ecosistema di strumenti software di Intel, ad esempio la libreria del kernel matematico di Intel e le funzionalità avanzate di elaborazione dei vettori, ad esempio AVX-512.

VM serie HC funzionalità 100 GB/sec Mellanox EDR InfiniBand. Queste VM sono connesse in un albero Fat senza blocco per prestazioni RDMA ottimizzate e coerenti. Queste macchine virtuali supportano il routing adattivo e il trasporto con connessione dinamica (DCT, in aggiunta ai trasporti standard RC e UD). Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e sono consigliate per l'utilizzo.

[ACU](acu.md): 297-315<br>
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
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3,7 | 100 | Tutti | 700 | 4 | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [configurazione delle macchine virtuali](./workloads/hpc/configure.md), sull' [Abilitazione di InfiniBand](./workloads/hpc/enable-infiniband.md), sulla configurazione di [MPI](./workloads/hpc/setup-mpi.md)e sull'ottimizzazione di applicazioni HPC per Azure in [carichi di lavoro HPC](./workloads/hpc/overview.md).
- Leggere gli ultimi annunci e alcuni esempi HPC e risultati nei [blog della community tecnica di Calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione dell'architettura di alto livello dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
- Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
