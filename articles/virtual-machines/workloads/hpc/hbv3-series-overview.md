---
title: Panoramica della VM serie modello HBV3, architettura, topologia-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle dimensioni delle macchine virtuali della serie modello HBV3 in Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f78420a65cd9c2402266eb9ba973eabe758d7ee5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608243"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Panoramica delle macchine virtuali serie modello HBV3 

Un server della [serie modello HBV3](../../hbv3-series.md) include 2 * 64-core CPU 7V13 EPYC per un totale di 128 core fisici "Zen3". Il multithreading simultaneo (SMT) è disabilitato in modello HBV3. Questi 128 core sono divisi in 16 sezioni (8 per socket), ciascuna delle quali contiene 8 core del processore con accesso uniforme a una cache L3 di 32 MB. I server Azure modello HBV3 eseguono anche le seguenti impostazioni del BIOS AMD:

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

Di conseguenza, il server viene avviato con 4 domini NUMA (2 per socket) ciascuno 32-core di dimensioni. Ogni NUMA ha accesso diretto a 4 canali di DRAM fisico che operano a 3200 MT/s.

Per consentire il funzionamento dell'hypervisor di Azure senza interferire con la macchina virtuale, si riservano 8 core fisici per ogni server.

## <a name="vm-topology"></a>Topologia VM

Nel diagramma seguente viene illustrata la topologia del server. Questi 8 core host hypervisor sono riservati (giallo) simmetricamente tra entrambi i socket della CPU, prendendo i primi 2 core da specifici matrici di core complessi (CCD) in ogni dominio NUMA, con i core rimanenti per la macchina virtuale della serie modello HBV3 (verde).

![Topologia del server della serie modello HBV3](./media/architecture/hbv3/hbv3-topology-server.png)

Si noti che il limite del CCD non è equivalente a un limite NUMA. In modello HBV3 un gruppo di quattro CCD consecutivi (4) viene configurato come dominio NUMA, sia a livello di server host che all'interno di una macchina virtuale guest. Quindi, tutte le dimensioni delle VM modello HBV3 espongono 4 domini NUMA che verranno visualizzati in un sistema operativo e in un'applicazione come illustrato di seguito, 4 domini NUMA uniformi, ognuno con un numero diverso di core a seconda delle dimensioni specifiche della [macchina virtuale modello HBV3](../../hbv3-series.md).

![Topologia della macchina virtuale della serie modello HBV3](./media/architecture/hbv3/hbv3-topology-vm.png)

Ogni dimensione della macchina virtuale modello HBV3 è simile in layout fisico, funzionalità e prestazioni di una CPU diversa dalla serie AMD EPYC 7003, come indicato di seguito:

| Dimensioni macchina virtuale serie modello HBV3             | Domini NUMA | Core per dominio NUMA  | Somiglianza con AMD EPYC         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | EPYC dual socket 7713            |
Standard_HB120r 96s_v3            | 4            | 24                     | EPYC dual socket 7643            |
Standard_HB120r 64s_v3            | 4            | 16                     | EPYC dual socket 7543            |
Standard_HB120r 32s_v3            | 4            | 8                      | EPYC dual socket 7313            |
Standard_HB120r 16s_v3            | 4            | 4                      | 72F3 EPYC dual socket            |

> [!NOTE]
> Le dimensioni della VM Core vincolati riducono solo il numero di core fisici esposti alla macchina virtuale. Tutte le risorse globali condivise (RAM, larghezza di banda della memoria, cache L3, connettività GMI e xGMI, InfiniBand, rete Ethernet di Azure, unità SSD locale) rimanere costanti. Questo consente a un cliente di scegliere le dimensioni della macchina virtuale più adattate a un determinato set di carico di lavoro o alle esigenze di licenza software.

Il mapping NUMA virtuale di ogni dimensione della VM modello HBV3 viene mappato alla topologia NUMA fisica sottostante. Non esiste alcuna astrazione potenzialmente fuorviante della topologia hardware. 

La topologia esatta per le varie [dimensioni della macchina virtuale modello HBV3](../../hbv3-series.md) viene visualizzata come segue usando l'output di [lstopo](https://linux.die.net/man/1/lstopo):
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>Fare clic per visualizzare l'output di lstopo per Standard_HB120rs_v3</summary>

![output di lstopo per modello HBV3-120 VM](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>Fare clic per visualizzare l'output di lstopo per Standard_HB120rs-96_v3</summary>

![output lstopo per modello HBV3-96 VM](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>Fare clic per visualizzare l'output di lstopo per Standard_HB120rs-64_v3</summary>

![output di lstopo per la macchina virtuale modello HBV3-64](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>Fare clic per visualizzare l'output di lstopo per Standard_HB120rs-32_v3</summary>

![output di lstopo per la VM modello HBV3-32](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>Fare clic per visualizzare l'output di lstopo per Standard_HB120rs-16_v3</summary>

![output di lstopo per la macchina virtuale modello HBV3-16](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

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
| Framework aggiuntivi          | UCX, libfabric, PGA                  |
| Supporto per archiviazione di Azure          | Dischi standard e Premium (massimo 32 dischi)              |
| Supporto del sistema operativo per SRIOV RDMA      | CentOS/RHEL 7.6 +, Ubuntu 18.04 +, SLES 12 SP4 +, WinServer 2016 +           |
| Sistema operativo consigliato per le prestazioni | CentOS 8,1, Windows Server 2019 +
| Supporto per l'agente di orchestrazione           | Azure CycleCloud, Azure Batch, AKS; [Opzioni di configurazione del cluster](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Windows Server 2012 R2 non è supportato in modello HBV3 e in altre VM con più di 64 core (virtuali o fisici). Per altri dettagli, vedere [qui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli annunci più recenti, sugli esempi di carico di lavoro HPC e sui risultati delle prestazioni, vedere i [Blog della community tecnica di Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
