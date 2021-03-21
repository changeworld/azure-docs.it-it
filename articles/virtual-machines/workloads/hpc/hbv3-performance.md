---
title: Prestazioni dimensioni macchina virtuale serie modello HBV3
description: Informazioni sui risultati dei test delle prestazioni per le dimensioni delle macchine virtuali della serie modello HBV3 in Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 87c3e4e9b509589624a228ea2e1f4b68e86e3fa8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721134"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Prestazioni delle macchine virtuali serie modello HBV3

Gli utenti con accesso anticipato delle macchine virtuali modello HBV3 possono prevedere le seguenti cifre sulle prestazioni sui microbenchmark HPC comuni

| Carico di lavoro                                        | Modello HBV3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Triade di flusso                                    | 330-350 GB/s (~ 82-86 GB/s per NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) per le dimensioni della macchina virtuale 120-Core               |
| Latenza RDMA & larghezza di banda                        | 1,2 microsecondi (1 byte), 192 GB/s (unidirezionale)                                        |
| FIO in NVMe SSD locale (RAID0)                  | 7 GB/s di letture, 3 GB/s Scritture; letture IOPS 186K, scritture IOPS 201K |

## <a name="process-pinning"></a>Blocco processo

Il blocco dei processi funziona bene in macchine virtuali della serie modello HBV3, perché il silicio sottostante viene esposto così com'è alla macchina virtuale guest. Si consiglia vivamente di bloccare i processi per ottenere prestazioni e coerenza ottimali.

## <a name="mpi-latency"></a>Latenza MPI

È possibile eseguire il test della latenza MPI dalla suite di microbenchmark OSU per le versioni precedenti. Gli script di esempio sono disponibili su [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>Larghezza di banda MPI
È possibile eseguire il test della larghezza di banda MPI dalla suite di microbenchmark OSU per le versioni precedenti. Gli script di esempio sono disponibili su [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Perftest Mellanox
Il [pacchetto perftest di Mellanox](https://community.mellanox.com/s/article/perftest-package) include molti test InfiniBand, ad esempio latenza (ib_send_lat) e larghezza di banda (ib_send_bw). Di seguito è riportato un comando di esempio. 
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Passaggi successivi
- Informazioni sul [ridimensionamento delle applicazioni MPI](compiling-scaling-applications.md).
- Per informazioni sugli annunci più recenti, sugli esempi di carico di lavoro HPC e sui risultati delle prestazioni, vedere i [Blog della community tecnica di Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione architettonica di livello superiore dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).