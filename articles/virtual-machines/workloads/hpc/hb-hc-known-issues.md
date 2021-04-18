---
title: Risoluzione dei problemi noti relativi alle macchine virtuali HPC e GPU - Macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulla risoluzione dei problemi noti relativi alle dimensioni delle macchine virtuali HPC e GPU in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f5bdae17126048da153f70bf27609bcc4b92fe21
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599588"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Problemi noti delle VM serie H e serie N

Questo articolo tenta di elencare i problemi comuni recenti e le relative soluzioni quando si usano le macchine virtuali [HPC](../../sizes-gpu.md) e GPU serie [H](../../sizes-hpc.md) e N.

## <a name="qp0-access-restriction"></a>Restrizione di accesso qp0

Per impedire l'accesso hardware di basso livello che può causare vulnerabilità di sicurezza, la coppia di code 0 non è accessibile alle macchine virtuali guest. Questo dovrebbe influire solo sulle azioni in genere associate all'amministrazione della scheda di interfaccia di rete ConnectX InfiniBand e all'esecuzione di alcune operazioni di diagnostica InfiniBand come ibdiagnet, ma non delle applicazioni degli utenti finali.

## <a name="mofed-installation-on-ubuntu"></a>Installazione di MOFED in Ubuntu
In Ubuntu-18.04 le immagini di macchine virtuali del Marketplace con versione del kernel e versioni più recenti, alcune versioni precedenti di Mellanox OFED non sono compatibili, causando in alcuni casi un aumento del tempo di avvio della macchina virtuale fino `5.4.0-1039-azure #42` a 30 minuti. Questo problema è stato segnalato per entrambe le versioni di Mellanox OFED 5.2-1.0.4.0 e 5.2-2.2.0.0. Il problema è stato risolto con Mellanox OFED 5.3-1.0.0.1.
Se è necessario usare l'OFED incompatibile, una soluzione consiste nell'usare l'immagine di macchina virtuale **Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** del Marketplace o versione precedente e non aggiornare il kernel.

## <a name="mpi-qp-creation-errors"></a>Errori di creazione del QP MPI
Se durante l'esecuzione di carichi di lavoro MPI vengono generati errori di creazione del QP InfiniBand, come illustrato di seguito, è consigliabile riavviare la macchina virtuale e provare di nuovo il carico di lavoro. Questo problema verrà risolto in futuro.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

È possibile verificare i valori del numero massimo di coppie di code quando il problema viene osservato come segue.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Rete accelerata in HB, HC, HBv2 e NDv2

[La rete accelerata](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) di Azure è ora disponibile nelle macchine virtuali con supporto per RDMA e InfiniBand e con SR-IOV con dimensioni [HB,](../../hb-series.md) [HC,](../../hc-series.md) [HBv2](../../hbv2-series.md)e [NDv2.](../../ndv2-series.md) Questa funzionalità consente ora un miglioramento in tutto (fino a 30 Gbps) e latenze sulla rete Ethernet di Azure. Anche se questa funzionalità è separata dalle funzionalità RDMA sulla rete InfiniBand, alcune modifiche della piattaforma per questa funzionalità possono influire sul comportamento di determinate implementazioni MPI durante l'esecuzione di processi su InfiniBand. In particolare, l'interfaccia InfiniBand in alcune macchine virtuali può avere un nome leggermente diverso (mlx5_1 anziché mlx5_0 precedente) e ciò potrebbe richiedere la modifica delle righe di comando MPI, in particolare quando si usa l'interfaccia UCX (in genere con OpenMPI e HPC-X). La soluzione più semplice attualmente può consistere nell'usare la versione più recente di HPC-X nelle immagini della macchina virtuale CentOS-HPC o disabilitare La rete accelerata, se non necessario.
Altri dettagli sono disponibili in questo articolo [di TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) con istruzioni su come risolvere eventuali problemi osservati.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>Installazione del driver InfiniBand in macchine virtuali non SR-IOV

Attualmente H16r, H16mr e NC24r non sono abilitati per SR-IOV. Alcuni dettagli sulla biforcazione dello stack InfiniBand sono [disponibili qui.](../../sizes-hpc.md#rdma-capable-instances)
InfiniBand può essere configurato nelle dimensioni delle macchine virtuali abilitate per SR-IOV con i driver OFED, mentre le dimensioni delle macchine virtuali non SR-IOV richiedono driver ND. Questo supporto IB è disponibile in modo appropriato per [CentOS, RHEL e Ubuntu.](configure.md)

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplicare MAC con cloud-init con Ubuntu in macchine virtuali serie H e serie N

Si è verificato un problema noto con cloud-init nelle immagini di macchine virtuali Ubuntu durante il tentativo di visualizzare l'interfaccia IB. Ciò può verificarsi al riavvio della macchina virtuale o quando si tenta di creare un'immagine di macchina virtuale dopo la generalizzazione. I log di avvio della macchina virtuale possono visualizzare un errore simile al seguente:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Questo "MAC duplicato con cloud-init in Ubuntu" è un problema noto. Questo problema verrà risolto nei kernel più nuovi. Se si verifica il problema, la soluzione alternativa è:
1) Distribuire l'immagine della macchina virtuale del marketplace (Ubuntu 18.04)
2) Installare i pacchetti software necessari per abilitare IB ([istruzioni qui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Modificare waagent.conf per modificare EnableRDMA=y
4) Disabilitare la rete in cloud-init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Modificare il file di configurazione di rete di netplan generato da cloud-init per rimuovere il MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>DRAM in macchine virtuali serie HB

Le macchine virtuali serie HB possono esporre solo 228 GB di RAM alle macchine virtuali guest al momento. Analogamente, 458 GB in HBv2 e 448 GB nelle macchine virtuali HBv3. Ciò è dovuto a una limitazione nota dell'hypervisor di Azure per impedire l'assegnazione di pagine alla DRAM locale dei domini CCX (NUMA) di AMD riservati per la macchina virtuale guest.

## <a name="gss-proxy"></a>GSS Proxy

Il proxy GSS presenta un bug noto in CentOS/RHEL 7.5 che può manifestarsi come una riduzione significativa delle prestazioni e della velocità di risposta quando viene usato con NFS. Questa soluzione può essere mitigata con:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Pulizia della cache

Nei sistemi HPC è spesso utile pulire la memoria al termine di un processo prima che all'utente successivo venga assegnato lo stesso nodo. Dopo aver eseguito le applicazioni in Linux, è possibile che la memoria disponibile si riduca mentre la memoria del buffer aumenta, nonostante non venga eseguita alcuna applicazione.

![Screenshot del prompt dei comandi prima della pulizia](./media/known-issues/cache-cleaning-1.png)

L'uso di mostrerà i nodi NUMAnode con cui la memoria viene memorizzata nel `numactl -H` buffer (possibilmente tutti). In Linux gli utenti possono pulire le cache in tre modi per restituire la memoria memorizzata nel buffer o memorizzata nella cache a "libera". È necessario essere radice o avere autorizzazioni sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Screenshot del prompt dei comandi dopo la pulizia](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avvisi del kernel

È possibile ignorare i messaggi di avviso del kernel seguenti quando si avvia una macchina virtuale serie HB in Linux. Ciò è dovuto a una limitazione nota dell'hypervisor di Azure che verrà affrontata nel tempo.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Passaggi successivi

- Esaminare la [panoramica della serie HB](hb-series-overview.md) e la [panoramica della serie HC](hc-series-overview.md) per informazioni su come configurare in modo ottimale i carichi di lavoro ai fini delle prestazioni e della scalabilità.
- Per informazioni sugli annunci più recenti, sugli esempi di carichi di lavoro HPC e sui risultati [delle prestazioni, Calcolo di Azure Blog della community tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Per una panoramica dell'architettura di livello superiore per l'esecuzione di carichi di lavoro [HPC, vedere HpC (High Performance Computing) in Azure.](/azure/architecture/topics/high-performance-computing/)
