---
title: Configurazione del driver GPU serie N di Azure per Linux
description: Informazioni su come installare driver GPU NVIDIA per macchine virtuali serie N che eseguono Linux in Azure
services: virtual-machines
author: vikancha-MSFT
ms.service: virtual-machines
ms.subervice: vm-sizes-gpu
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: dd9461e30138ee1a59a93db45aa5f739bfe88f94
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565305"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installare i driver GPU NVIDIA in VM serie N che eseguono Linux

Per usufruire delle funzionalità GPU delle macchine virtuali serie N di Azure che si servono di GPU NVIDIA, è necessario installare i driver GPU NVIDIA. L'[estensione del driver NVIDIA GPU](../extensions/hpccompute-gpu-linux.md) consente di installare i driver NVIDIA CUDA o GRID appropriati in una macchina virtuale serie N. Installare o gestire l'estensione usando il portale di Azure o strumenti come l'interfaccia della riga di comando di Azure o Azure Resource Manager. Vedere le [documentazione dell'estensione dei driver GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) per le distribuzioni supportate e i passaggi di distribuzione.

Se si sceglie di installare manualmente i driver GPU NVIDIA, questo articolo descrive i driver e le distribuzioni supportate, nonché passaggi di installazione e verifica. Le informazioni di configurazione manuale dei driver sono disponibili anche per le [macchine virtuali Windows](../windows/n-series-driver-setup.md).

Per conoscere le specifiche, le capacità di archiviazione e i dettagli dei dischi delle macchine virtuali serie N, vedere [Dimensioni delle macchine virtuali Linux GPU](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Installare i driver CUDA nelle macchine virtuali serie N

Di seguito sono indicati i passaggi per installare i driver CUDA nelle VM Serie N dal Toolkit di NVIDIA CUDA. 

Gli sviluppatori C++ e C possono facoltativamente installare il toolkit completo per creare applicazioni con accelerazione GPU. Per altre informazioni, vedere la [guida di installazione di CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Per installare i driver di CUDA, stabilire una connessione SSH a ogni VM. Per verificare che nel sistema sia presente una GPU con supporto per core CUDA, eseguire il comando seguente:

```bash
lspci | grep -i NVIDIA
```
Verrà visualizzato un output simile all'esempio seguente (che rappresenta una scheda NVIDIA Tesla K80):

![Output del comando Ispci](./media/n-series-driver-setup/lspci.png)

lspci elenca i dispositivi PCIe nella macchina virtuale, incluse la scheda di interfaccia di rete e le GPU InfiniBand, se presenti. Se lspci non viene restituito correttamente, potrebbe essere necessario installare LIS in CentOS/RHEL (istruzioni riportate di seguito).
Quindi eseguire i comandi di installazione specifici per la distribuzione.

### <a name="ubuntu"></a>Ubuntu 

1. Scaricare e installare i driver CUDA dal sito Web NVIDIA. 
    > [!NOTE]
   >  L'esempio seguente mostra il percorso del pacchetto CUDA per Ubuntu 16.04. Sostituire il percorso specifico della versione che si prevede di usare. 
   >  
   >  Visitare l'[Area download Nvidia] ( https://developer.download.nvidia.com/compute/cuda/repos/) per il percorso completo specifico di ogni versione. 
   > 
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}
   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update
   sudo apt-get install cuda-drivers
   ```

   L'installazione può richiedere alcuni minuti.
 

2. Per installare facoltativamente il toolkit di CUDA completo, digitare:

   ```bash
   sudo apt-get install cuda
   ```

3. Riavviare la VM e procedere a verificare l'installazione.

#### <a name="cuda-driver-updates"></a>Aggiornamenti dei driver CUDA

È consigliabile aggiornare periodicamente i driver CUDA dopo la distribuzione.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS o Red Hat Enterprise Linux

1. Aggiornare il kernel (consigliato). Se si sceglie di non aggiornare il kernel, assicurarsi che le versioni di `kernel-devel` e `dkms` siano adeguate per il kernel.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. Installare la versione [più recente Integration Services Linux per Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106). Controllare se è necessario lis verificando i risultati di lspci. Se tutti i dispositivi GPU sono elencati come previsto (e documentati in precedenza), l'installazione di LIS non è necessaria.

   Si noti che LIS è applicabile a Red Hat Enterprise Linux, CentOS e al kernel compatibile con Oracle Linux Red Hat 5.2-5.11, 6.0-6.10 e 7.0-7.7. Per altre informazioni, vedere la [documentazione Integration Services https://www.microsoft.com/en-us/download/details.aspx?id=55106) Linux]. 
   Ignorare questo passaggio se si prevede di usare CentOS/RHEL 7.8 (o versioni successive) perché LIS non è più necessario per queste versioni.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot
      ```

3. Riconnettersi alla macchina virtuale e continuare l'installazione con i comandi seguenti:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   L'installazione può richiedere alcuni minuti. 
   
    > [!NOTE]
   >  Visitare [il repo Fedora](https://dl.fedoraproject.org/pub/epel/) e [Nvidia CUDA](https://developer.download.nvidia.com/compute/cuda/repos/) per selezionare il pacchetto corretto per la versione centOS o RHEL che si vuole usare.
   >  

Ad esempio, CentOS 8 e RHEL 8 dovranno seguire questa procedura.

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel8-10.2.89-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

4. Per installare facoltativamente il toolkit di CUDA completo, digitare:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Se viene visualizzato un messaggio di errore relativo a pacchetti mancanti come vulkan-filesystem, potrebbe essere necessario modificare /etc/yum.repos.d/rh-cloud, cercare optional-rpms e impostare enabled su 1
   >  

5. Riavviare la VM e procedere a verificare l'installazione.

### <a name="verify-driver-installation"></a>Verificare l'installazione del driver

Per controllare lo stato del dispositivo GPU, eseguire una connessione SSH alla VM ed eseguire l'utilità della riga di comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver. 

Se il driver è installato, l'output sarà simile al seguente. Si noti che **GPU-Util** mostra 0% a meno che nella macchina virtuale non sia attualmente in esecuzione un carico di lavoro GPU. La versione del driver e i dettagli GPU possono essere diversi da quelli riportati.

![Stato del dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Connettività di rete RDMA

La connettività di rete RDMA può essere abilitata nelle macchine virtuali serie N con supporto per RDMA, ad esempio NC24r distribuite nello stesso set di disponibilità o in un singolo gruppo di posizionamento in un set di scalabilità di macchine virtuali. La rete RDMA supporta il traffico Message Passing Interface (MPI) per le applicazioni in esecuzione con Intel MPI 5.x o una versione più recente. Seguono i requisiti aggiuntivi:

### <a name="distributions"></a>Distribuzioni

Distribuire le VM serie N abilitate per RDMA da una delle immagini in Azure Marketplace che supporta la connettività RDMA sulle VM serie N:
  
* **Ubuntu 16.04 LTS** - Configurare i driver RDMA nella VM ed eseguire la registrazione con Intel per scaricare Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-based 7.4 HPC**: i driver RDMA e Intel MPI 5.1 vengono installati nella VM.

* **HPC basato su CentOS** - CentOS-HPC 7.6 e versioni successive (per gli SKU in cui InfiniBand è supportato su SR-IOV). Queste immagini hanno librerie Mellanox OFED e MPI preinstallate.

> [!NOTE]
> CX3-Pro sono supportate solo tramite le versioni LTS di Mellanox OFED. Usare la versione LTS Mellanox OFED (4.9-0.1.7.0) nelle macchine virtuali serie N con schede ConnectX3-Pro rete. Per altre informazioni, vedere [Driver Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).
>
> Inoltre, alcune delle immagini HPC Azure Marketplace più recenti hanno Mellanox OFED 5.1 e versioni successive, che non supportano ConnectX3-Pro schede. Controllare la versione di Mellanox OFED nell'immagine HPC prima di usarla nelle macchine virtuali con ConnectX3-Pro virtuali.
>
> Le immagini seguenti sono le immagini CentOS-HPC più recenti che supportano ConnectX3-Pro seguenti:
>
> - OpenLogic:CentOS-HPC:7.6:7.6.2020062900
> - OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
> - OpenLogic:CentOS-HPC:7.7:7.7.2020062600
> - OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
> - OpenLogic:CentOS-HPC:8_1:8.1.2020062400
> - OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Installare i driver GRID nelle macchine virtuali serie NV o NVv3

Per installare i driver NVIDIA GRID nelle macchine virtuali serie NV o NVv3, stabilire una connessione SSH a ogni macchina virtuale e seguire la procedura per la distribuzione di Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Eseguire il comando `lspci`. Verificare che la scheda o le schede NVIDIA M60 siano visualizzate come dispositivi PCI.

2. Installare gli aggiornamenti.

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. Disabilitare il driver del kernel Nouveau, che è incompatibile con il driver NVIDIA. Usare il driver NVIDIA solo nelle macchine virtuali NV o NVv2. A tale scopo, creare un file in `/etc/modprobe.d` denominato `nouveau.conf` con il contenuto seguente:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. Riavviare la macchina virtuale ed eseguire nuovamente la connessione. Uscire dal server X:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Scaricare e installare il driver GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run
   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Quando viene chiesto se si vuole eseguire l'utilità nvidia-xconfig per aggiornare il file di configurazione di X, selezionare **Yes** (Sì).

7. Al termine dell'installazione, copiare /etc/nvidia/gridd.conf.template in un nuovo file gridd.conf nel percorso /etc/nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Aggiungere quanto segue a `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Rimuovere quanto segue da `/etc/nvidia/gridd.conf` se presente:
 
   ```
   FeatureType=0
   ```
10. Riavviare la VM e procedere a verificare l'installazione.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS o Red Hat Enterprise Linux 

1. Aggiornare il kernel e il DKMS (consigliato). Se si sceglie di non aggiornare il kernel, assicurarsi che le versioni di `kernel-devel` e `dkms` siano adeguate per il kernel.
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. Disabilitare il driver del kernel Nouveau, che è incompatibile con il driver NVIDIA. Usare il driver NVIDIA solo nelle macchine virtuali NV o NV3. A tale scopo, creare un file in `/etc/modprobe.d` denominato con il contenuto `nouveau.conf` seguente:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. Riavviare la macchina virtuale e installare i [servizi di integrazione di Linux più recenti per Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106). Controllare se LIS è necessario verificando i risultati di lspci. Se tutti i dispositivi GPU sono elencati come previsto (e documentati in precedenza), l'installazione di LIS non è necessaria. 

   Ignorare questo passaggio se si prevede di usare CentOS/RHEL 7.8 (o versioni successive) perché LIS non è più necessario per queste versioni.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. Ristabilire la connessione alla macchina virtuale ed eseguire il comando `lspci`. Verificare che la scheda o le schede NVIDIA M60 siano visualizzate come dispositivi PCI.
 
5. Scaricare e installare il driver GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Quando viene chiesto se si vuole eseguire l'utilità nvidia-xconfig per aggiornare il file di configurazione di X, selezionare **Yes** (Sì).

7. Al termine dell'installazione, copiare /etc/nvidia/gridd.conf.template in un nuovo file gridd.conf nel percorso /etc/nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Aggiungere quanto segue a `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Rimuovere quanto segue da `/etc/nvidia/gridd.conf` se presente:
 
   ```
   FeatureType=0
   ```
10. Riavviare la VM e procedere a verificare l'installazione.


### <a name="verify-driver-installation"></a>Verificare l'installazione del driver


Per controllare lo stato del dispositivo GPU, eseguire una connessione SSH alla VM ed eseguire l'utilità della riga di comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installata con il driver. 

Se il driver è installato, l'output sarà simile al seguente. Si noti che **GPU-Util** mostra 0% a meno che nella macchina virtuale non sia attualmente in esecuzione un carico di lavoro GPU. La versione del driver e i dettagli GPU possono essere diversi da quelli riportati.

![Screenshot che mostra l'output quando viene eseguita una query sullo stato del dispositivo GPU.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Server X11
Se è necessario un server X11 per le connessioni da remoto a una macchina virtuale NV o nVv2, è consigliabile usare [x11vnc](http://www.karlrunge.com/x11vnc/) perché consente l'accelerazione hardware della grafica. Il BusID del dispositivo M60 deve essere aggiunto manualmente al file di configurazione X11 file (in genere `etc/X11/xorg.conf`). Aggiungere una sezione `"Device"` simile alla seguente:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Aggiornare anche la sezione `"Screen"` per l'uso del dispositivo.
 
Per individuare il BusID decimale, eseguire

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
Il BusID può cambiare quando una macchina virtuale viene riallocata o riavviata. Pertanto, è consigliabile creare uno script per aggiornare il BusID nella configurazione di X11 quando una macchina virtuale viene riavviata. Creare ad esempio uno script denominato `busidupdate.sh` (o un altro nome scelto) con contenuto simile al seguente:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Creare quindi una voce per lo script di aggiornamento in `/etc/rc.d/rc3.d`, in modo che venga richiamato come radice all'avvio.

## <a name="troubleshooting"></a>Risoluzione dei problemi

* È possibile impostare la modalità di persistenza tramite `nvidia-smi`. In questo modo l'output del comando sarà più veloce per l'esecuzione di query sulle schede. Per impostare la modalità di persistenza, eseguire `nvidia-smi -pm 1`. Si noti che se la macchina virtuale viene riavviata, l'impostazione della modalità scompare. È sempre possibile generare script che impostino la modalità affinché venga eseguita all'avvio.
* Se i driver NVIDIA CUDA sono stati aggiornati alla versione più recente e si rileva che la connettività RDMA non funziona più, [reinstallare i driver RDMA](#rdma-network-connectivity) per ristabilire la connettività. 
* Durante l'installazione di LIS, se una determinata versione del sistema operativo CentOS/RHEL (o kernel) non è supportata per LIS, viene generato un errore "Versione del kernel non supportata". Segnalare questo errore insieme alle versioni del sistema operativo e del kernel.

## <a name="next-steps"></a>Passaggi successivi

* Se si intende acquisire un'immagine di una VM Linux in cui sono installati driver NVIDIA, vedere [Come generalizzare e acquisire una macchina virtuale Linux](capture-image.md).
