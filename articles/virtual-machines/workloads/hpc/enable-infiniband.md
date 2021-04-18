---
title: Abilitare InifinBand nelle macchine virtuali HPC - Macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come abilitare InfiniBand nelle macchine virtuali HPC di Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: dba336c8690bba2bb388a8b9ab2d52b651166da5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599605"
---
# <a name="enable-infiniband"></a>Abilitare InfiniBand

Le macchine virtuali [serie H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) [con supporto RDMA](../../sizes-hpc.md#rdma-capable-instances) comunicano attraverso la rete InfiniBand a bassa latenza e larghezza di banda elevata. La funzionalità RDMA su un'interconnessione di questo tipo è fondamentale per migliorare la scalabilità e le prestazioni dei carichi di lavoro HPC e di intelligenza artificiale su nodi distribuiti. Le macchine virtuali serie H e serie N abilitate per InfiniBand sono connesse in strutture Fat Tree non bloccanti a diametro ridotto per prestazioni RDMA ottimizzate e coerenti.

Esistono diversi modi per abilitare InfiniBand nelle dimensioni delle macchine virtuali idonee.

## <a name="vm-images-with-infiniband-drivers"></a>Immagini di vm con driver InfiniBand
Per [](configure.md#vm-images) un elenco delle immagini di vm supportate nel Marketplace, precaricate con driver InfiniBand (per macchine virtuali SR-IOV o non SR-IOV), vedere Immagini vm supportate nel Marketplace o che possono essere configurate con i driver appropriati per le macchine virtuali con supporto per [RDMA.](../../sizes-hpc.md#rdma-capable-instances)
- Le [immagini di VM CentOS-HPC](configure.md#centos-hpc-vm-images) nel Marketplace sono il modo più semplice per iniziare.
- Le [immagini di VM Ubuntu](configure.md#ubuntu-vm-images) possono essere configurate con i driver IB corretti.

## <a name="infiniband-driver-vm-extensions"></a>Estensioni macchina virtuale del driver InfiniBand
In Linux è possibile usare l'estensione vm [InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) per installare i driver Mellanox OFED e abilitare InfiniBand nelle macchine virtuali serie H e N abilitate per SR-IOV.

In Windows, l'estensione della macchina virtuale [InfiniBandDriverWindows](../../extensions/hpc-compute-infiniband-windows.md) installa i driver di Windows Network Direct (in macchine virtuali non SR-IOV) o i driver Mellanox OFED (nelle macchine virtuali SR-IOV) per la connettività RDMA. In alcune distribuzioni di istanze A8 e A9, l'estensione HpcVmDrivers viene aggiunta automaticamente. Si noti che l'estensione della macchina virtuale HpcVmDrivers è deprecata. non verrà aggiornato.

Per aggiungere l'estensione macchina virtuale a una macchina virtuale, è possibile usare i cmdlet di [Azure PowerShell](/powershell/azure/). Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale](../../extensions/overview.md). È anche possibile usare estensioni delle macchine virtuali nel [modello di distribuzione classico](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## <a name="manual-installation"></a>Installazione manuale
[I driver Mellanox OpenFabrics (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) possono essere installati manualmente nelle macchine virtuali serie [H](../../sizes-hpc.md) e [N](../../sizes-gpu.md) abilitate per [SR-IOV.](../../sizes-hpc.md#rdma-capable-instances)

### <a name="linux"></a>Linux
I [driver OFED per Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) possono essere installati con l'esempio seguente. Anche se l'esempio è per RHEL/CentOS, ma i passaggi sono generali e possono essere usati per qualsiasi sistema operativo Linux compatibile, ad esempio Ubuntu (16.04, 18.04 19.04, 20.04) e SLES (12 SP4 e 15). Altri esempi per altre distribuzione sono disponibili nel [repo azhpc-images.](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh) Anche i driver della posta in arrivo funzionano, ma i driver Mellanox OFED offrono più funzionalità.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Per Windows, scaricare e installare [i driver Mellanox OFED per Windows.](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)

## <a name="enable-ip-over-infiniband-ib"></a>Abilitare l'indirizzo IP su InfiniBand (IB)
Se si prevede di eseguire processi MPI, in genere non è necessario IPoIB. La libreria MPI userà l'interfaccia dei verbi per la comunicazione IB (a meno che non si usi in modo esplicito il canale TCP/IP della libreria MPI). Se tuttavia si ha un'app che usa TCP/IP per la comunicazione e si vuole eseguire su IB, è possibile usare IPoIB sull'interfaccia IB. Usare i comandi seguenti (per RHEL/CentOS) per abilitare l'indirizzo IP su InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'installazione e l'esecuzione [di varie librerie MPI](setup-mpi.md) supportate nelle macchine virtuali.
- Esaminare la [panoramica della serie HBv3](hbv3-series-overview.md) e [la panoramica della serie HC.](hc-series-overview.md)
- Per informazioni sugli annunci più recenti, sugli esempi di carichi di lavoro HPC e sui risultati delle [prestazioni, Calcolo di Azure Blog della community tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
