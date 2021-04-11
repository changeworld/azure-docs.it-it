---
title: High Performance Computing in macchine virtuali serie H e serie N abilitate per InfiniBand - Macchine virtuali di Azure
description: Informazioni sulle funzionalità e le caratteristiche delle VM serie H e serie N abilitate per InfiniBand e ottimizzate per HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 03/18/2021
ms.reviewer: cynthn
ms.openlocfilehash: 65b37a8c07e083f5e9809812e2d4446cc48717d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720594"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>High Performance Computing in macchine virtuali serie H e serie N abilitate per InfiniBand

Le macchine virtuali serie H e serie N abilitate per InfiniBand di Azure sono progettate per offrire prestazioni senza confronti, scalabilità MPI (Message Passing Interface) e convenienza per una varietà di carichi di lavoro HPC reali e di intelligenza artificiale. Queste VM ottimizzate per HPC (High Performance Computing) vengono usate per risolvere alcuni problemi che comportano un utilizzo elevato delle risorse di calcolo nel campo delle scienze e dell'ingegneria, come la fluidodinamica, la modellazione geologica, le simulazioni meteorologiche e così via.

Questi articoli forniscono informazioni introduttive sulle macchine virtuali serie H e serie N abilitate per InfiniBand in Azure e sulla configurazione ottimale dei carichi di lavoro HPC e di intelligenza artificiale nelle VM ai fini della scalabilità.

## <a name="features-and-capabilities"></a>Funzionalità e caratteristiche

Le macchine virtuali serie H e serie N abilitate per InfiniBand sono progettate per offrire le migliori prestazioni in termini di HPC, scalabilità MPI (Message Passing Interface) ed economicità per i carichi di lavoro HPC. Vedere gli articoli sulle VM [serie H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) per altre informazioni sulle funzionalità e caratteristiche di queste VM.

### <a name="rdma-and-infiniband"></a>RDMA e InfiniBand

Le macchine virtuali [serie H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) [con supporto RDMA](../../sizes-hpc.md#rdma-capable-instances) comunicano attraverso la rete InfiniBand a bassa latenza e larghezza di banda elevata. La funzionalità RDMA su un'interconnessione di questo tipo è fondamentale per migliorare la scalabilità e le prestazioni dei carichi di lavoro HPC e di intelligenza artificiale su nodi distribuiti. Le macchine virtuali serie H e serie N abilitate per InfiniBand sono connesse in strutture Fat Tree non bloccanti a diametro ridotto per prestazioni RDMA ottimizzate e coerenti.
Per altre informazioni sulla configurazione di InfiniBand nelle macchine virtuali abilitate per Infiniband, vedere [Abilitare InfiniBand](enable-infiniband.md).

### <a name="message-passing-interface"></a>Message Passing Interface

Le serie H e N abilitate per SR-IOV supportano quasi tutte le librerie e le versioni MPI. Di seguito sono riportate alcune delle librerie MPI più diffuse: Intel MPI, OpenMPi, HPC-X, MVAPICH2, MPICH, Platform MPI. Sono supportati tutti i verbi RDMA (Remote Direct Memory Access).
Per altre informazioni sull'installazione delle varie librerie MPI supportate e sulla relativa configurazione ottimale, vedere [Configurare MPI](setup-mpi.md).

## <a name="get-started"></a>Introduzione

Il primo passaggio consiste nel selezionare il tipo di VM [serie H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) ottimale per il carico di lavoro in base alle specifiche delle VM e alla [funzionalità RDMA](../../sizes-hpc.md#rdma-capable-instances).
Occorre poi configurare la VM abilitando InfiniBand. A questo scopo sono disponibili vari metodi, tra cui l'uso di immagini di VM ottimizzate con driver incorporati. Per altre informazioni, vedere [Ottimizzazione per Linux](configure.md) e [Abilitare InfiniBand](enable-infiniband.md).
In terzo luogo, per i carichi di lavoro dei nodi distribuiti è fondamentale scegliere e configurare MPI in modo appropriato. Per altre informazioni, vedere [Configurare MPI](setup-mpi.md).
Quarto, per prestazioni e scalabilità, configurare in modo ottimale i carichi di lavoro seguendo le istruzioni specifiche per la famiglia di macchine virtuali, ad esempio per la [Panoramica della serie modello HBV3](hbv3-series-overview.md) e la [Panoramica della serie HC](hc-series-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [configurazione e l'ottimizzazione](configure.md) delle VM [serie H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) abilitate per InfiniBand.
- Vedere Panoramica della [serie modello HBV3](hb-series-overview.md) e [Panoramica della serie HC](hc-series-overview.md) per informazioni sulla configurazione ottimale dei carichi di lavoro per prestazioni e scalabilità.
- Per informazioni sugli annunci più recenti, sugli esempi di carico di lavoro HPC e sui risultati delle prestazioni, vedere i [Blog della community tecnica di Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
