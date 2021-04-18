---
title: Configurazione e ottimizzazione delle macchine virtuali di Azure serie H e serie N abilitate per InfiniBand
description: Informazioni sulla configurazione e sull'ottimizzazione delle macchine virtuali serie H e N infiniBand abilitate per HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 470d5efae68366b5cc96243bab4ebb8552771650
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600880"
---
# <a name="configure-and-optimize-vms"></a>Configurare e ottimizzare le macchine virtuali

Questo articolo illustra alcune linee guida per la configurazione e l'ottimizzazione delle macchine virtuali serie [H](../../sizes-hpc.md) e [N](../../sizes-gpu.md) abilitate per InfiniBand per HPC.

## <a name="vm-images"></a>Immagini di macchine virtuali
Nelle macchine virtuali abilitate per InfiniBand sono necessari i driver appropriati per abilitare RDMA.
- Le immagini di macchine virtuali [CentOS-HPC](#centos-hpc-vm-images) nel Marketplace sono preconfigurato con i driver IB appropriati e sono il modo più semplice per iniziare.
- Le [immagini della macchina virtuale Ubuntu](#ubuntu-vm-images) possono essere configurate con i driver IB corretti. È consigliabile creare [immagini di vm personalizzate](../../linux/tutorial-custom-images.md) con i driver e la configurazione appropriati e riutilizzarle periodicamente.

Nelle macchine virtuali [della](../../sizes-gpu.md) serie N abilitate per GPU, sono necessari anche i driver GPU appropriati che possono essere aggiunti tramite le estensioni della macchina virtuale [o](../../extensions/hpccompute-gpu-linux.md) [manualmente.](../../linux/n-series-driver-setup.md) Alcune immagini di macchine virtuali nel Marketplace vengono anche preinstallato con i driver GPU Nvidia, incluse alcune immagini di macchine virtuali di Nvidia.

### <a name="centos-hpc-vm-images"></a>Immagini di macchine virtuali CentOS-HPC

#### <a name="sr-iov-enabled-vms"></a>Macchine virtuali abilitate per SR-IOV
Per le macchine virtuali [abilitate per RDMA](../../sizes-hpc.md#rdma-capable-instances)abilitate per SR-IOV, le immagini di macchine virtuali [CentOS-HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) nel Marketplace versione 7.6 e successive sono adatte. Queste immagini di vm vengono ottimizzate e precaricate con i driver OFED per RDMA e varie librerie MPI e pacchetti di calcolo scientifico di uso comune e sono il modo più semplice per iniziare.
- I dettagli sugli elementi inclusi nelle immagini delle macchine virtuali CentOS-HPC versione 7.6 e successive sono disponibili in [un articolo di TechCommunity.](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)
- Gli script usati per la creazione di immagini di macchine virtuali CentOS-HPC versione 7.6 e successive da un'immagine del Marketplace centOS di base sono disponibili nel [repo azhpc-images.](https://github.com/Azure/azhpc-images/tree/master/centos)
  
> [!NOTE] 
> Le immagini più recenti di Azure HPC Marketplace hanno Mellanox OFED 5.1 e versioni successive, che non supportano ConnectX3-Pro schede InfiniBand. Le dimensioni di VM serie N abilitate per SR-IOV con FDR InfiniBand (ad esempio NCv3 e versioni precedenti) saranno in grado di usare la seguente immagine di macchina virtuale CentOS-HPC o versioni precedenti del Marketplace:
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>Macchine virtuali non abilitate per SR-IOV
Per le macchine virtuali con supporto [per RDMA](../../sizes-hpc.md#rdma-capable-instances)non abilitate per SR-IOV, sono adatti CentOS-HPC versione 6.5 o successiva, fino alla versione 7.4 nel Marketplace. Ad esempio, per le macchine virtuali serie [H16,](../../h-series.md)sono consigliate le versioni da 7.1 a 7.4. Queste immagini di macchine virtuali vengono precaricate con i driver Di rete diretta per RDMA e Intel MPI versione 5.1.

> [!NOTE]
> In queste immagini HPC basate su CentOS per macchine virtuali non abilitate per SR-IOV, gli aggiornamenti del kernel sono disabilitati nel file **di configurazione yum.** Ciò è dovuto al fatto che i driver RDMA di NetworkDirect Linux vengono distribuiti come pacchetto RPM e gli aggiornamenti dei driver potrebbero non funzionare se il kernel viene aggiornato.

### <a name="rhelcentos-vm-images"></a>Immagini di VM RHEL/CentOS
Le immagini di macchine virtuali non HPC basate su RHEL o CentOS nel Marketplace possono essere configurate per l'uso nelle macchine virtuali con supporto [per RDMA](../../sizes-hpc.md#rdma-capable-instances)abilitate per SR-IOV. Altre informazioni [sull'abilitazione di InfiniBand](enable-infiniband.md) [e sulla configurazione di MPI](setup-mpi.md) nelle macchine virtuali.
- È anche possibile usare gli script usati nella creazione delle immagini di vm CentOS-HPC versione 7.6 e successive da un'immagine di base di CentOS Marketplace dal [repo azhpc-images.](https://github.com/Azure/azhpc-images/tree/master/centos)
  
> [!NOTE]
> Mellanox OFED 5.1 e versione precedente non supportano le schede ConnectX3-Pro InfiniBand nelle macchine virtuali serie N abilitate per SR-IOV con FDR InfiniBand (ad esempio NCv3). Usare LTS Mellanox OFED versione 4.9-0.1.7.0 o precedente nelle macchine virtuali serie N con schede ConnectX3-Pro. Per altri dettagli, [vedere qui](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="ubuntu-vm-images"></a>Immagini di macchine virtuali Ubuntu
Le immagini di VM Ubuntu Server 16.04 LTS, 18.04 LTS e 20.04 LTS nel Marketplace sono supportate sia per le macchine virtuali con supporto per SR-IOV che per le macchine virtuali [con supporto rdMA](../../sizes-hpc.md#rdma-capable-instances)non SR-IOV. Altre informazioni [sull'abilitazione di InfiniBand](enable-infiniband.md) [e sulla configurazione di MPI](setup-mpi.md) nelle macchine virtuali.
- Le istruzioni per l'abilitazione di InfiniBand nelle immagini della macchina virtuale Ubuntu sono disponibili in [un articolo di TechCommunity.](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)
- Gli script usati per la creazione delle immagini di VM HPC basate su Ubuntu 18.04 e 20.04 LTS da un'immagine del Marketplace di base di Ubuntu si basano sul [repo azhpc-images.](https://github.com/Azure/azhpc-images/tree/master/ubuntu)

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server di macchina virtuale
Sono supportate le immagini SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium), SLES 12 SP4 e SLES 15 VM nel Marketplace. Queste immagini di vm vengono precaricate con i driver Network Direct per RDMA e Intel MPI versione 5.1. Altre informazioni sulla [configurazione di MPI](setup-mpi.md) nelle macchine virtuali.

## <a name="optimize-vms"></a>Ottimizzare le macchine virtuali

Di seguito sono riportate alcune impostazioni di ottimizzazione facoltative per migliorare le prestazioni della macchina virtuale.

### <a name="update-lis"></a>Aggiornare lis

Se necessario per funzionalità o prestazioni, i driver [linux Integration Services (LIS)](../../linux/endorsed-distros.md) possono essere installati o aggiornati nelle distribuzioni del sistema operativo supportate, in particolare la distribuzione tramite un'immagine personalizzata o una versione precedente del sistema operativo, ad esempio CentOS/RHEL 6.x o versione precedente della versione 7.x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Recuperare memoria

Migliorare le prestazioni recuperando automaticamente la memoria per evitare l'accesso remoto alla memoria.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Per rendere persistente questa operazione dopo il riavvio della macchina virtuale:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Disabilitare firewall e SELinux

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
Facoltativamente, WALinuxAgent può essere disabilitato come passaggio di pre-processo e abilitato dopo il processo per la disponibilità massima delle risorse vm per il carico di lavoro HPC.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni [sull'abilitazione di InfiniBand](enable-infiniband.md) nelle macchine virtuali serie [H](../../sizes-hpc.md) e [N abilitate](../../sizes-gpu.md) per InfiniBand.
- Altre informazioni sull'installazione e l'esecuzione [di varie librerie MPI](setup-mpi.md) supportate nelle macchine virtuali.
- Esaminare la [panoramica della serie HBv3 e](hbv3-series-overview.md) la panoramica della serie [HC.](hc-series-overview.md)
- Per informazioni sugli annunci più recenti, sugli esempi di carichi di lavoro HPC e sui risultati [delle prestazioni, Calcolo di Azure Blog della community tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
