---
title: Panoramica delle VM serie modello HBV3-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle dimensioni delle macchine virtuali della serie modello HBV3 in Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 94e4590c66cdee74555611302f4f9228a755c76a
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472864"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Panoramica delle macchine virtuali serie modello HBV3 

Un server della [serie modello HBV3](../../hbv3-series.md) include 2 * 64-core CPU 7V13 EPYC per un totale di 128 core fisici "Zen3". Il multithreading simultaneo (SMT) è disabilitato in modello HBV3. Questi 128 core sono divisi in 16 sezioni (8 per socket), ciascuna delle quali contiene 8 core del processore con accesso uniforme a una cache L3 di 32 MB. I server Azure modello HBV3 eseguono anche le seguenti impostazioni del BIOS AMD:

```bash
Nodes per Socket =2
L3 as NUMA = Disabled
```

Di conseguenza, il server viene avviato con 4 domini NUMA (2 per socket) ciascuno 32-core di dimensioni. Ogni NUMA ha accesso diretto a 4 canali di DRAM fisico che operano a 3200 MT/s.

Per consentire il funzionamento dell'hypervisor di Azure senza interferire con la macchina virtuale, si riservano 8 core fisici per ogni server. 

Si noti che le dimensioni della VM Core vincolati riducono solo il numero di core fisici esposti alla macchina virtuale. Tutte le risorse globali condivise (RAM, larghezza di banda della memoria, cache L3, connettività GMI e xGMI, InfiniBand, rete Ethernet di Azure, unità SSD locale) rimanere costanti. Questo consente a un cliente di scegliere le dimensioni della macchina virtuale più adattate a un determinato set di carico di lavoro o alle esigenze di licenza software.

## <a name="infiniband-networking"></a>Rete InfiniBand
Le macchine virtuali modello HBV3 includono anche schede di rete NVIDIA Mellanox HDR InfiniBand (ConnectX-6) che funzionano fino a 200 Gigabit al secondo. La scheda di interfaccia di rete viene passata alla macchina virtuale tramite SRIOV, consentendo al traffico di rete di ignorare l'hypervisor. Di conseguenza, i clienti caricano i driver Mellanox OFED standard in macchine virtuali modello HBV3 come un ambiente bare metal.

Le macchine virtuali modello HBV3 supportano il routing adattivo, il trasporto con connessione dinamica (DCT, in aggiunta ai trasporti standard RC e UD) e l'offload basato su hardware dei collettivi MPI al processore onboarding dell'adapter ConnectX-6. Queste funzionalità migliorano le prestazioni, la scalabilità e la coerenza delle applicazioni e l'utilizzo di tali funzionalità è fortemente consigliato.

## <a name="temporary-storage"></a>Archiviazione temporanea
VM modello HBV3 funzionalità 3 dispositivi SSD fisicamente locali. Un dispositivo è preformattato per fungere da file di paging e verrà visualizzato all'interno della macchina virtuale come dispositivo "SSD" generico.

Altre due SSD di dimensioni maggiori vengono fornite come dispositivi NVMe Block non formattati tramite NVMeDirect. Poiché il dispositivo NVMe di blocco ignora l'hypervisor, avrà una larghezza di banda superiore, IOPS più elevata e una latenza inferiore per IOP.

Una volta abbinato in una matrice con striping, l'unità SSD NVMe fornisce fino a 7 GB/s di letture e 3 GB/s di Scritture e fino a 186.000 IOPS (letture) e 201.000 IOPS (scritture) per profondità della coda completa.

## <a name="hardware-specifications"></a>Specifiche hardware 

| Specifiche hardware          | Macchine virtuali serie modello HBV3              |
|----------------------------------|----------------------------------|
| Core                            | 120, 96, 64, 32 o 16 (SMT disabilitato)               | 
| CPU                              | AMD EPYC 7V13                   | 
| Frequenza CPU (non-AVX)          | 3,1 GHz (tutti i core), 3,675 GHz (fino a 10 core)    | 
| Memoria                           | 448 GB (RAM per core dipende dalle dimensioni della macchina virtuale)         | 
| Disco locale                       | 2 * 960 GB NVMe (Block), 480 GB SSD (file di paging) | 
| Infiniband                       | 200 GB/s Mellanox ConnectX-6 HDR InfiniBand | 
| Rete                          | 50 GB/s Ethernet (40 GB/s utilizzabile) Azure Second gen SmartNIC | 

## <a name="software-specifications"></a>Specifiche del software 

| Specifiche del software        | Macchine virtuali serie modello HBV3                                            | 
|--------------------------------|-----------------------------------------------------------|
| Dimensioni massime del processo MPI               | 36.000 Core (300 VM in un singolo set di scalabilità di macchine virtuali con singlePlacementGroup = true) |
| Supporto MPI                    | HPC-X, Intel MPI, OpenMPi, MVAPICH2, MPICH  |
| Framework aggiuntivi          | Comunicazione unificata X, libfabric, PGA                  |
| Supporto per archiviazione di Azure          | Dischi standard e Premium (massimo 32 dischi)              |
| Supporto del sistema operativo per SRIOV RDMA      | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +           |
| Sistema operativo consigliato per le prestazioni | CentOS 8,1, Windows Server 2019 +
| Supporto per l'agente di orchestrazione           | Azure CycleCloud, Azure Batch, servizio Azure Kubernetes                      | 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli annunci più recenti e su alcuni esempi HPC, vedere i [Blog della community tecnica di calcolo di Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).