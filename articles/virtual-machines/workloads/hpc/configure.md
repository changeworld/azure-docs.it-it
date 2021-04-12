---
title: Configurazione e ottimizzazione delle macchine virtuali di Azure di serie H e serie N abilitate per InfiniBand
description: Informazioni sulla configurazione e l'ottimizzazione delle macchine virtuali serie H e serie N abilitate per InfiniBand per HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0c6f5dc55f7406aba7d6e3dc1a278b57fe4ec9ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721269"
---
# <a name="configure-and-optimize-vms"></a>Configurare e ottimizzare le macchine virtuali

Questo articolo condivide alcune indicazioni sulla configurazione e l'ottimizzazione delle macchine virtuali serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) abilitate per InfiniBand per HPC.

## <a name="vm-images"></a>Immagini di macchine virtuali
Nelle VM con InfiniBand abilitata, i driver appropriati sono necessari per abilitare RDMA.
- Le [Immagini di VM CentOS-HPC](#centos-hpc-vm-images) nel Marketplace sono preconfigurate con i driver IB appropriati e rappresentano il modo più semplice per iniziare.
- Le [Immagini di VM Ubuntu](#ubuntu-vm-images) possono essere configurate con i driver IB corretti. È consigliabile creare immagini di [VM personalizzate](../../linux/tutorial-custom-images.md) con i driver e la configurazione appropriati e riutilizzarle in modo ricorrente.

Nelle macchine virtuali [serie N](../../sizes-gpu.md) abilitate per la GPU sono necessari anche i driver GPU appropriati che possono essere aggiunti tramite le [estensioni della macchina virtuale](../../extensions/hpccompute-gpu-linux.md) o [manualmente](../../linux/n-series-driver-setup.md). Alcune immagini di VM nel Marketplace sono preinstallate anche con i driver NVIDIA GPU, incluse alcune immagini di VM di NVIDIA.

### <a name="centos-hpc-vm-images"></a>Immagini di VM CentOS-HPC

#### <a name="sr-iov-enabled-vms"></a>VM abilitate per SR-IOV
Per le [macchine virtuali](../../sizes-hpc.md#rdma-capable-instances)abilitate per SR-IOV abilitate per RDMA, sono adatte [le immagini CentOS-HPC VM in Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) versione 7,6 e successive. Queste immagini di VM vengono ottimizzate e precaricate con i driver OFED per RDMA e le librerie MPI e i pacchetti di calcolo scientifici usati comunemente e rappresentano il modo più semplice per iniziare.
- Per informazioni dettagliate sugli elementi inclusi nelle immagini di VM CentOS-HPC versione 7,6 e successive, fare parte di un [articolo di TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557).
- Gli script usati per la creazione di immagini di VM CentOS-HPC versione 7,6 e successive da un'immagine di base di CentOS Marketplace si trovano nel [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
  
> [!NOTE] 
> Le immagini più recenti di Azure HPC Marketplace hanno Mellanox OFED 5,1 e versioni successive, che non supportano le schede InfiniBand ConnectX3-Pro. Le dimensioni delle macchine virtuali serie N abilitate per SR-IOV con InfiniBand InfiniBand, ad esempio NCv3 e versioni precedenti, saranno in grado di usare l'immagine di macchina virtuale CentOS-HPC seguente o le versioni precedenti del Marketplace:
>- OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
>- OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
>- OpenLogic: CentOS-HPC: 7,7:7.7.2020062600
>- OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
>- OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
>- OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>VM non abilitate per SR-IOV
Per le VM con supporto di [RDMA](../../sizes-hpc.md#rdma-capable-instances)non SR-IOV abilitate, CentOS-HPC versione 6,5 o successiva, fino a 7,4 nel Marketplace sono adatte. Ad esempio, per le [macchine virtuali della serie H16](../../h-series.md), sono consigliate le versioni 7,1 e 7,4. Queste immagini di macchina virtuale vengono precaricate con i driver diretti di rete per RDMA e Intel MPI versione 5,1.

> [!NOTE]
> In queste immagini HPC basate su CentOS per le macchine virtuali abilitate per non SR-IOV, gli aggiornamenti del kernel sono disabilitati nel file di configurazione **yum** . Ciò è dovuto al fatto che i driver RDMA di NetworkDirect Linux sono distribuiti come un pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.

### <a name="rhelcentos-vm-images"></a>Immagini di VM RHEL/CentOS
Le immagini di VM non HPC basate su RHEL o CentOS sul Marketplace possono essere configurate per l'uso nelle [macchine virtuali](../../sizes-hpc.md#rdma-capable-instances)abilitate per RDMA SR-IOV abilitate. Altre informazioni sull' [Abilitazione di InfiniBand](enable-infiniband.md) e sulla [configurazione di MPI](setup-mpi.md) nelle VM.
- È anche possibile usare gli script usati per la creazione di immagini di VM CentOS-HPC versione 7,6 e successive da un'immagine di base CentOS Marketplace del [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos) .
  
> [!NOTE]
> Mellanox OFED 5,1 e versioni successive non supportano le schede InfiniBand ConnectX3-Pro sulle dimensioni delle macchine virtuali serie N abilitate per SR-IOV con FDR InfiniBand (ad esempio, NCv3). Usare LTS Mellanox OFED versione 4.9-0.1.7.0 o precedente nelle macchine virtuali serie N con le schede ConnectX3-Pro. Per altri dettagli, vedere [qui](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="ubuntu-vm-images"></a>Immagini di VM Ubuntu
Le immagini di VM Ubuntu Server 16,04 LTS, 18,04 LTS e 20,04 LTS nel Marketplace sono supportate sia per le VM SR-IOV che per quelle non SR-IOV [in grado](../../sizes-hpc.md#rdma-capable-instances)di supportare RDMA. Altre informazioni sull' [Abilitazione di InfiniBand](enable-infiniband.md) e sulla [configurazione di MPI](setup-mpi.md) nelle VM.
- Le istruzioni per l'abilitazione di InfiniBand sulle immagini della macchina virtuale Ubuntu sono disponibili in un [articolo di TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).
- Gli script usati per la creazione di immagini di VM HPC basate su Ubuntu 18,04 e 20,04 LTS da un'immagine di base di Ubuntu Marketplace si trovano nel [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu).

### <a name="suse-linux-enterprise-server-vm-images"></a>Immagini di macchine virtuali SUSE Linux Enterprise Server
Sono supportate le immagini SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium), SLES 12 SP4 e SLES 15 VM nel Marketplace. Queste immagini di macchina virtuale vengono precaricate con i driver diretti di rete per RDMA e Intel MPI versione 5,1. Altre informazioni sulla [configurazione di MPI](setup-mpi.md) nelle VM.

## <a name="optimize-vms"></a>Ottimizzare le macchine virtuali

Di seguito sono riportate alcune impostazioni di ottimizzazione facoltative per migliorare le prestazioni della macchina virtuale.

### <a name="update-lis"></a>Aggiorna LIS

Se necessario per le funzionalità o le prestazioni, [i driver di Linux Integration Services (LIS)](../../linux/endorsed-distros.md) possono essere installati o aggiornati nelle distribuzioni del sistema operativo supportate, in particolare per la distribuzione usando un'immagine personalizzata o una versione precedente del sistema operativo, ad esempio CentOS/RHEL 6. x o versione precedente 7. x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Recupera memoria

Migliorare le prestazioni recuperando automaticamente la memoria per evitare l'accesso remoto alla memoria.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Per renderlo permanente dopo il riavvio della macchina virtuale:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Disabilitare il firewall e SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Disabilitare cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Configurare WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Facoltativamente, il WALinuxAgent può essere disabilitato come passaggio di pre-processo e abilitato di nuovo dopo il processo per la massima disponibilità delle risorse delle macchine virtuali per il carico di lavoro HPC.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull' [Abilitazione di InfiniBand](enable-infiniband.md) sulle VM serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) abilitate per InfiniBand.
- Altre informazioni sull'installazione di varie [librerie MPI supportate](setup-mpi.md) e sulla relativa configurazione ottimale nelle macchine virtuali.
- Vedere [Panoramica della serie modello HBV3](hbv3-series-overview.md) e [Panoramica della serie HC](hc-series-overview.md).
- Per informazioni sugli annunci più recenti, sugli esempi di carico di lavoro HPC e sui risultati delle prestazioni, vedere i [Blog della community tecnica di Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
