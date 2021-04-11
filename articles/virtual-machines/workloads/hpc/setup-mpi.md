---
title: Configurare MPI (Message Passing Interface) per HPC-macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come configurare MPI per HPC in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 66de34c43ab1b3a6b4245f77196793bf9ad8530c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606641"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurare l'interfaccia di passaggio dei messaggi per HPC

L' [interfaccia MPI (Message Passing Interface)](https://en.wikipedia.org/wiki/Message_Passing_Interface) è una libreria aperta ed è di norma per la parallelizzazione della memoria distribuita. Viene comunemente usato in molti carichi di lavoro HPC. I carichi di lavoro HPC sulle VM serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) [compatibili con RDMA](../../sizes-hpc.md#rdma-capable-instances) possono usare MPI per comunicare attraverso la rete InfiniBand a bassa latenza e larghezza di banda elevata.
- Le dimensioni delle VM abilitate per SR-IOV in Azure consentono di usare praticamente qualsiasi versione di MPI con Mellanox OFED.
- Nelle VM abilitate per non SR-IOV, le implementazioni MPI supportate usano l'interfaccia di Microsoft Network Direct (ND) per la comunicazione tra le macchine virtuali. Di conseguenza, sono supportate solo le versioni Microsoft MPI (MS-MPI) 2012 R2 o versioni successive e Intel MPI 5. x. Le versioni successive (2017, 2018) della libreria di runtime Intel MPI possono o non essere compatibili con i driver RDMA di Azure.

Per le [macchine virtuali](../../sizes-hpc.md#rdma-capable-instances)abilitate per SR-IOV abilitate per RDMA, sono adatte le [Immagini di VM CentOS-HPC](configure.md#centos-hpc-vm-images) versione 7,6 e successive. Queste immagini di VM vengono ottimizzate e precaricate con i driver OFED per RDMA e le librerie MPI e i pacchetti di calcolo scientifici usati comunemente e rappresentano il modo più semplice per iniziare.

Sebbene gli esempi siano per RHEL/CentOS, ma i passaggi sono generali e possono essere usati per qualsiasi sistema operativo Linux compatibile, ad esempio Ubuntu (16,04, 18,04 19,04, 20,04) e SLES (12 SP4 e 15). Altri esempi per la configurazione di altre implementazioni MPI in altre distribuzioni sono disponibili nel [repository azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> L'esecuzione di processi MPI su macchine virtuali abilitate per SR-IOV con determinate librerie MPI (ad esempio, MPI della piattaforma) può richiedere l'impostazione di chiavi di partizione (p-Keys) in un tenant per l'isolamento e la sicurezza. Per informazioni dettagliate su come determinare i valori della chiave p e impostarli correttamente per un processo MPI con tale libreria MPI, attenersi alla procedura descritta nella sezione [Discover Partition Keys](#discover-partition-keys) .

> [!NOTE]
> I frammenti di codice riportati di seguito sono esempi. Si consiglia di usare le versioni stabili più recenti dei pacchetti o di fare riferimento al [repository azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) è un Framework di API di comunicazione per HPC. È ottimizzato per la comunicazione MPI su InfiniBand e funziona con molte implementazioni MPI come OpenMPi e MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> Nelle build recenti di UCX è stato risolto un [problema](https://github.com/openucx/ucx/pull/5965) in base al quale viene scelta l'interfaccia InfiniBand corretta in presenza di più interfacce nic. Altre informazioni su come eseguire [MPI su InfiniBand](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) quando è abilitata la rete accelerata nella macchina virtuale.

## <a name="hpc-x"></a>HPC-X

[HPC-X software Toolkit](https://www.mellanox.com/products/hpc-x-toolkit) contiene UCX e HCOLL e può essere compilato con UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Eseguire HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>Ottimizzazione di collettivi MPI

Le primitive di comunicazione collettiva MPI offrono un metodo flessibile e portatile per implementare operazioni di comunicazione del gruppo. Sono ampiamente usati in varie applicazioni scientifiche parallele e hanno un impatto significativo sulle prestazioni complessive dell'applicazione. Vedere l' [articolo TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) per informazioni dettagliate sui parametri di configurazione per ottimizzare le prestazioni di comunicazione collettiva usando HPC-X e la libreria HCOLL per la comunicazione collettiva.

> [!NOTE] 
> Con HPC-X 2.7.4 +, potrebbe essere necessario passare in modo esplicito LD_LIBRARY_PATH se la versione UCX in MOFED e in HPC-X è diversa.

## <a name="openmpi"></a>OpenMPI

Installare UCX come descritto in precedenza. HCOLL fa parte del [Toolkit di software HPC-X](https://www.mellanox.com/products/hpc-x-toolkit) e non richiede un'installazione speciale.

OpenMPi può essere installato dai pacchetti disponibili nel repository.

```bash
sudo yum install –y openmpi
```

È consigliabile creare una versione stabile più recente di OpenMPi con UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Per prestazioni ottimali, eseguire OpenMPi con `ucx` e `hcoll` .

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="intel-mpi"></a>Intel MPI

Scaricare la versione di [Intel MPI](https://software.intel.com/mpi-library/choose-download)scelta. Modificare la variabile di ambiente I_MPI_FABRICS a seconda della versione.
- Intel MPI 2019 e 2021: usare `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . Il `mlx` provider utilizza UCX. È stato rilevato che l'utilizzo dei verbi è instabile e meno efficiente. Per ulteriori informazioni, vedere l'articolo relativo a [TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) .
- Intel MPI 2018: uso `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: uso `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

### <a name="non-sr-iov-vms"></a>VM non SR-IOV
Per le macchine virtuali non SR-IOV, un esempio di download della versione di [valutazione gratuita](https://registrationcenter.intel.com/en/forms/?productid=1740) del runtime 5. x è il seguente:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Per la procedura di installazione, vedere [Intel MPI Library installation guide](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) (Guida all'installazione di Intel MPI Library).
Facoltativamente, potrebbe essere necessario abilitare ptrace per i processi non del debugger non radice (necessari per le versioni più recenti di Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Per SUSE Linux Enterprise Server le versioni di immagini di macchina virtuale: SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium), SLES 12 SP4 e SLES 15, i driver RDMA sono installati e i pacchetti Intel MPI vengono distribuiti nella macchina virtuale. Installare Intel MPI eseguendo il comando seguente:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich2"></a>MVAPICH2

Compila MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Esecuzione di MVAPICH2.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi"></a>MPI della piattaforma

Installare i pacchetti necessari per Platform MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Seguire il processo di installazione.

I comandi seguenti sono esempi di esecuzione di MPI pingpong e allreduce con Platform MPI in macchine virtuali modello HBV3 con immagini di VM CentOS-HPC 7,6, 7,8 e 8,1.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Installare UCX come descritto in precedenza. Compila MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Esecuzione di MPICH.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="osu-mpi-benchmarks"></a>Benchmark MPI OSU

Scaricare [OSU MPI benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) e untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Creare benchmark usando una particolare libreria MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

I benchmark MPI si trovano nella `mpi/` cartella.


## <a name="discover-partition-keys"></a>Individua chiavi di partizione

Individuare le chiavi di partizione (p-Keys) per la comunicazione con altre macchine virtuali nello stesso tenant (set di disponibilità o set di scalabilità di macchine virtuali).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Il numero maggiore dei due è la chiave del tenant da utilizzare con MPI. Esempio: se le chiavi p sono le seguenti, 0x800b deve essere usato con MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Utilizzare la partizione diversa dalla chiave di partizione predefinita (0x7FFF). Per UCX è necessario cancellare il MSB della chiave p. Ad esempio, impostare UCX_IB_PKEY come 0x000B per 0x800b.

Si noti anche che, a condizione che il tenant (set di disponibilità o set di scalabilità di macchine virtuali) esista, il PKEYs rimane invariato. Questo vale anche quando i nodi vengono aggiunti o eliminati. I nuovi tenant ottengono PKEYs diversi.


## <a name="set-up-user-limits-for-mpi"></a>Configurare i limiti utente per MPI

Configurare i limiti utente per MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Configurare le chiavi SSH per MPI

Configurare le chiavi SSH per i tipi MPI che lo richiedono.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

La sintassi precedente presuppone una home directory condivisa. in caso contrario, è necessario copiare la directory SSH in ogni nodo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle macchine virtuali serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) [abilitate per InfiniBand](../../sizes-hpc.md#rdma-capable-instances)
- Vedere [Panoramica della serie modello HBV3](hbv3-series-overview.md) e [Panoramica della serie HC](hc-series-overview.md).
- Per informazioni sugli annunci più recenti, sugli esempi di carico di lavoro HPC e sui risultati delle prestazioni, vedere i [Blog della community tecnica di Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
