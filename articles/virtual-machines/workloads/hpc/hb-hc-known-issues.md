---
title: Risoluzione dei problemi noti con le VM HPC e GPU-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulla risoluzione dei problemi noti relativi alle dimensioni di VM HPC e GPU in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d8c3a2d961cc5b6fd719b77dae07b6e46c3d8b65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604839"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Problemi noti delle VM serie H e serie N

Questo articolo tenta di elencare i problemi comuni recenti e le relative soluzioni quando si usano le VM HPC e GPU serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) .

## <a name="mofed-installation-on-ubuntu"></a>Installazione di MOFED in Ubuntu
In Ubuntu-18,04, il OFED Mellanox ha mostrato l'incompatibilità con i kernel versione `5.4.0-1039-azure #42` e versioni successive, causando un aumento del tempo di avvio della macchina virtuale a circa 30 minuti. Questo report è stato segnalato sia per Mellanox OFED versioni 5.2-1.0.4.0 che per 5.2-2.2.0.0.
La soluzione temporanea consiste nell'usare l'immagine **canonica: UbuntuServer: 18_04-LTS-Gen2:18.04.202101290** Marketplace o versioni precedenti e non aggiornare il kernel.
Questo problema dovrebbe essere risolto con un MOFED più recente (TBD).

## <a name="mpi-qp-creation-errors"></a>Errori di creazione QP MPI
Se durante l'esecuzione di carichi di lavoro MPI si verificano errori di creazione di InfiniBand QP, come illustrato di seguito, è consigliabile riavviare la macchina virtuale e ritentare il carico di lavoro. Questo problema verrà risolto in futuro.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

È possibile verificare i valori del numero massimo di coppie di coda quando il problema viene osservato come indicato di seguito.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Rete accelerata su HB, HC, HBv2 e NDv2

La [funzionalità rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) è ora disponibile in RDMA e InfiniBand con supporto per le macchine virtuali con supporto per IOV e le dimensioni [HB](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md)e [NDv2](../../ndv2-series.md). Questa funzionalità consente ora un miglioramento in tutto (fino a 30 Gbps) e le latenze sulla rete Ethernet di Azure. Sebbene questo sia separato dalle funzionalità di RDMA sulla rete InfiniBand, alcune modifiche della piattaforma per questa funzionalità possono incorrere sul comportamento di determinate implementazioni MPI durante l'esecuzione di processi su InfiniBand. In particolare, l'interfaccia InfiniBand in alcune macchine virtuali può avere un nome leggermente diverso (mlx5_1 rispetto ai mlx5_0 precedenti) e potrebbe essere necessario modificare le righe di comando MPI soprattutto quando si usa l'interfaccia UCX (in genere con OpenMPi e HPC-X). La soluzione più semplice attualmente può consistere nell'usare la versione più recente di HPC-X sulle immagini di VM CentOS-HPC o disabilitare la rete accelerata, se non è necessario.
Altre informazioni su questo argomento sono disponibili in questo [articolo di TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) con istruzioni su come risolvere eventuali problemi osservati.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>Installazione del driver InfiniBand in macchine virtuali non SR-IOV

Attualmente H16r, H16mr e NC24r non sono compatibili con SR-IOV. Di [seguito](../../sizes-hpc.md#rdma-capable-instances)sono riportati alcuni dettagli sulla biforcazione dello stack InfiniBand.
InfiniBand può essere configurato nelle dimensioni delle VM abilitate per SR-IOV con i driver OFED, mentre le dimensioni delle macchine virtuali non SR-IOV richiedono driver ND. Questo supporto IB è disponibile in modo appropriato per [CentOS, RHEL e Ubuntu](configure.md).

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplicare MAC con cloud-init con Ubuntu sulle VM serie H e serie N

Si è verificato un problema noto con cloud-init sulle immagini di macchina virtuale Ubuntu durante il tentativo di visualizzare l'interfaccia IB. Questo problema può verificarsi al riavvio della macchina virtuale o quando si tenta di creare un'immagine di macchina virtuale dopo la generalizzazione. Nei log di avvio della macchina virtuale potrebbe essere visualizzato un errore simile al seguente:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Questo "MAC duplicato con cloud-init in Ubuntu" è un problema noto. Questa operazione verrà risolta nei kernel più recenti. Se si verifica il problema, la soluzione alternativa è la seguente:
1) Distribuire l'immagine di macchina virtuale del Marketplace (Ubuntu 18,04)
2) Installare i pacchetti software necessari per abilitare IB ([istruzioni qui](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Modificare waagent. conf per modificare EnableRDMA = y
4) Disabilitare la rete in cloud-init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Modificare il file di configurazione di rete di Netplan generato da cloud-init per rimuovere il MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="qp0-access-restriction"></a>Restrizione di accesso qp0

Per impedire l'accesso hardware di basso livello che può comportare vulnerabilità di sicurezza, la coppia di code 0 non è accessibile alle macchine virtuali guest. Questa operazione influisce solo sulle azioni generalmente associate all'amministrazione della scheda di interfaccia di rete ConnectX-5 e sull'esecuzione di una diagnostica InfiniBand come ibdiagnet, ma non sulle applicazioni dell'utente finale.

## <a name="dram-on-hb-series-vms"></a>DRAM sulle VM serie HB

Al momento, le VM serie HB possono esporre solo 228 GB di RAM alle macchine virtuali guest. Analogamente, 458 GB in HBv2 e 448 GB in macchine virtuali modello HBV3. Ciò è dovuto a una limitazione nota dell'hypervisor di Azure per impedire che le pagine vengano assegnate al DRAM locale di AMD CCX (domini NUMA) riservato per la VM guest.

## <a name="gss-proxy"></a>Proxy GSS

Il proxy GSS presenta un bug noto in CentOS/RHEL 7,5 che può manifestarsi come una significativa riduzione delle prestazioni e della velocità di risposta quando viene usato con NFS. Questo problema può essere risolto con:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Pulizia della cache

Nei sistemi HPC è spesso utile pulire la memoria al termine di un processo prima che all'utente successivo venga assegnato lo stesso nodo. Dopo l'esecuzione di applicazioni in Linux è possibile che la memoria disponibile si riduca mentre la memoria del buffer aumenta, nonostante non sia in esecuzione alcuna applicazione.

![Screenshot del prompt dei comandi prima della pulizia](./media/known-issues/cache-cleaning-1.png)

Con `numactl -H` verrà visualizzato il NUMAnode (probabilmente All) in cui è memorizzata la memoria. In Linux, gli utenti possono pulire le cache in tre modi per restituire la memoria memorizzata nel buffer o memorizzata nella cache su "Free". È necessario avere le autorizzazioni radice o sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Screenshot del prompt dei comandi dopo la pulizia](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avvisi del kernel

È possibile ignorare i messaggi di avviso del kernel seguenti quando si avvia una macchina virtuale della serie HB in Linux. Ciò è dovuto a un limite noto dell'hypervisor di Azure che verrà risolto nel corso del tempo.

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
- Per informazioni sugli annunci più recenti, sugli esempi di carico di lavoro HPC e sui risultati delle prestazioni, vedere i [Blog della community tecnica di Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per una visualizzazione architettonica di livello superiore dell'esecuzione di carichi di lavoro HPC, vedere [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
