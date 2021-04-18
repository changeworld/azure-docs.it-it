---
title: Configurare Message Passing Interface (MPI) per HPC - Macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come configurare MPI per HPC in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f43fc94174ebdcfdf447d3635a696193959849fa
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600294"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurare la Message Passing Interface per HPC

Il [Message Passing Interface (MPI) è](https://en.wikipedia.org/wiki/Message_Passing_Interface) una libreria aperta e uno standard di fatto per la parallelizzazione della memoria distribuita. Viene comunemente usato in molti carichi di lavoro HPC. I carichi di lavoro HPC nelle macchine virtuali serie [H](../../sizes-hpc.md) e [serie N](../../sizes-gpu.md) con supporto [per RDMA](../../sizes-hpc.md#rdma-capable-instances) possono usare MPI per comunicare tramite la rete InfiniBand a bassa latenza e larghezza di banda elevata.
- Le dimensioni delle macchine virtuali abilitate per SR-IOV in Azure consentono di usare quasi tutte le tipi di MPI con Mellanox OFED.
- Nelle macchine virtuali non abilitate per SR-IOV, le implementazioni MPI supportate usano l'interfaccia ND (Microsoft Network Direct) per comunicare tra le macchine virtuali. Di conseguenza, sono supportate solo le versioni Microsoft MPI (MS-MPI) 2012 R2 o successive e Intel MPI 5.x. Le versioni successive (2017, 2018) della libreria di runtime Intel MPI potrebbero o meno essere compatibili con i driver RDMA di Azure.

Per le macchine virtuali con supporto [per RDMA](../../sizes-hpc.md#rdma-capable-instances)abilitate per SR-IOV, sono adatte le immagini di vm [CentOS-HPC](configure.md#centos-hpc-vm-images) versione 7.6 e successive. Queste immagini di macchine virtuali sono ottimizzate e precaricate con i driver OFED per RDMA e varie librerie MPI di uso comune e pacchetti di calcolo scientifico e sono il modo più semplice per iniziare.

Anche se gli esempi sono per RHEL/CentOS, ma i passaggi sono generali e possono essere usati per qualsiasi sistema operativo Linux compatibile, ad esempio Ubuntu (16.04, 18.04 19.04, 20.04) e SLES (12 SP4 e 15). Altri esempi per la configurazione di altre implementazioni MPI in altre distribuzione sono disponibili nel [repo azhpc-images.](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)

> [!NOTE]
> L'esecuzione di processi MPI in macchine virtuali abilitate per SR-IOV con determinate librerie MPI (ad esempio Platform MPI) può richiedere la configurazione di chiavi di partizione (p-keys) in un tenant per l'isolamento e la sicurezza. Seguire la procedura descritta [nella](#discover-partition-keys) sezione Individuare le chiavi di partizione per informazioni dettagliate su come determinare i valori p-key e impostarli correttamente per un processo MPI con tale libreria MPI.

> [!NOTE]
> I frammenti di codice seguenti sono esempi. È consigliabile usare le versioni stabili più recenti dei pacchetti o fare riferimento al [repo azhpc-images.](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)

## <a name="choosing-mpi-library"></a>Scelta della libreria MPI
Se un'applicazione HPC consiglia una specifica libreria MPI, provare prima di tutto tale versione. Se si ha flessibilità riguardo a quale MPI è possibile scegliere e si vogliono ottenere prestazioni ottimali, provare HPC-X. In generale, HPC-X MPI offre le prestazioni migliori usando il framework UCX per l'interfaccia InfiniBand e sfrutta tutte le funzionalità hardware e software di Mellanox InfiniBand. Inoltre, HPC-X e OpenMPI sono compatibili con ABI, quindi è possibile eseguire dinamicamente un'applicazione HPC con HPC-X compilata con OpenMPI. Analogamente, Intel MPI, MVAPICH e MPICH supportano ABI.

La figura seguente illustra l'architettura per le librerie MPI più diffusi.

![Architettura per le librerie MPI più popolari](./media/mpi-architecture.png)

## <a name="ucx"></a>Ucx

[Unified Communication X (UCX)](https://github.com/openucx/ucx) è un framework di API di comunicazione per HPC. È ottimizzato per la comunicazione MPI su InfiniBand e funziona con molte implementazioni MPI, ad esempio OpenMPI e MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> Le build recenti di UCX hanno risolto un problema a causa del quale viene scelta l'interfaccia InfiniBand corretta in presenza di più interfacce NIC. [](https://github.com/openucx/ucx/pull/5965) Altre informazioni [sull'esecuzione](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) di MPI su InfiniBand quando la rete accelerata è abilitata nella macchina virtuale.

## <a name="hpc-x"></a>HPC-X

Il [toolkit software HPC-X](https://www.mellanox.com/products/hpc-x-toolkit) contiene UCX e HCOLL e può essere compilato su UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Il comando seguente illustra alcuni argomenti mpirun consigliati per HPC-X e OpenMPI.
```bash
mpirun -n $NPROCS --hostfile $HOSTFILE --map-by ppr:$NUMBER_PROCESSES_PER_NUMA:numa:pe=$NUMBER_THREADS_PER_PROCESS -report-bindings $MPI_EXECUTABLE
```
dove:

|Parametro|Descrizione                                        |
|---------|---------------------------------------------------|
|`NPROCS`   |Specifica il numero di processi MPI. Ad esempio: `-n 16`.|
|`$HOSTFILE`|Specifica un file che contiene il nome host o l'indirizzo IP, per indicare la posizione in cui verranno eseguiti i processi MPI. Ad esempio: `--hostfile hosts`.|
|`$NUMBER_PROCESSES_PER_NUMA`   |Specifica il numero di processi MPI che verranno eseguiti in ogni dominio NUMA. Per specificare, ad esempio, quattro processi MPI per NUMA, usare `--map-by ppr:4:numa:pe=1`.|
|`$NUMBER_THREADS_PER_PROCESS`  |Specifica il numero di thread per ogni processo MPI. Per specificare, ad esempio, un processo MPI e quattro thread per NUMA, usare `--map-by ppr:1:numa:pe=4`.|
|`-report-bindings` |Stampa il mapping tra processi MPI e core, che risulta utile per verificare che l'aggiunta dei processi MPI sia corretta.|
|`$MPI_EXECUTABLE`  |Specifica il collegamento creato dall'eseguibile MPI nelle librerie MPI. I wrapper del compilatore MPI eseguono questa operazione automaticamente. Ad esempio, `mpicc` o `mpif90`.|

Di seguito è riportato un esempio di esecuzione del microbenchmark di latenza OSU:
```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>Ottimizzazione dei collettivi MPI

Le primitive di comunicazione collettiva MPI offrono un modo flessibile e portabile per implementare le operazioni di comunicazione di gruppo. Sono ampiamente usati in varie applicazioni parallele scientifiche e hanno un impatto significativo sulle prestazioni complessive dell'applicazione. Per informazioni dettagliate sui parametri di configurazione per ottimizzare le prestazioni di comunicazione collettiva tramite HPC-X e la libreria HCOLL per la comunicazione collettiva, vedere l'articolo [TechCommunity.](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740)

Ad esempio, se si sospetta che l'applicazione MPI strettamente abbinata stia effettuando una quantità eccessiva di comunicazioni collettive, è possibile provare ad abilitare i collettivi gerarchici (HCOLL). Per abilitare tali funzionalità, usare i parametri seguenti.
```bash
-mca coll_hcoll_enable 1 -x HCOLL_MAIN_IB=<MLX device>:<Port>
```

> [!NOTE] 
> Con HPC-X 2.7.4+, potrebbe essere necessario passare in modo esplicito LD_LIBRARY_PATH se la versione UCX in MOFED e quella in HPC-X sono diverse.

## <a name="openmpi"></a>OpenMPI

Installare UCX come descritto in precedenza. HCOLL fa parte del [toolkit software HPC-X](https://www.mellanox.com/products/hpc-x-toolkit) e non richiede un'installazione speciale.

OpenMPI può essere installato dai pacchetti disponibili nel repo.

```bash
sudo yum install –y openmpi
```

È consigliabile creare una versione stabile più recente di OpenMPI con UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Per ottenere prestazioni ottimali, eseguire OpenMPI con `ucx` e `hcoll` . Vedere anche l'esempio con [HPC-X.](#hpc-x)

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Controllare la chiave di partizione come indicato in precedenza.

## <a name="intel-mpi"></a>Intel MPI

Scaricare la versione scelta di [Intel MPI.](https://software.intel.com/mpi-library/choose-download) La versione Intel MPI 2019 è stata spostata dal framework Open Fabrics Alliance (OFA) al framework di Open Fabric Interfaces (OFI) e attualmente supporta libfabric. Sono disponibili due provider per il supporto InfiniBand: mlx e verbs.
Modificare la I_MPI_FABRICS di ambiente a seconda della versione.
- Intel MPI 2019 e 2021: usare `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . Il `mlx` provider usa UCX. È stato rilevato che l'utilizzo dei verbi è instabile e meno performante. Per altri dettagli, vedere l'articolo [TechCommunity.](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149)
- Intel MPI 2018: usare `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: usare `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

Ecco alcuni argomenti mpirun consigliati per Intel MPI 2019 update 5+.
```bash
export FI_PROVIDER=mlx
export I_MPI_DEBUG=5
export I_MPI_PIN_DOMAIN=numa

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
dove:

|Parametro|Descrizione                                        |
|---------|---------------------------------------------------|
|`FI_PROVIDER`  |Specifica il provider libfabric da usare, che influirà sull'API, sul protocollo e sulla rete usata. Verbs è un'altra opzione, ma in genere mlx offre prestazioni migliori.|
|`I_MPI_DEBUG`|Specifica il livello di output di debug aggiuntivo, che può fornire informazioni dettagliate sulla posizione in cui vengono aggiunti i processi e su quale protocollo e rete vengono usati.|
|`I_MPI_PIN_DOMAIN` |Specifica il modo in cui si vogliono aggiungere i processi. È, ad esempio, possibile aggiungerli a core, socket o domini NUMA. In questo esempio questa variabile di ambiente viene impostata su numa, ovvero i processi verranno aggiunti ai domini del nodo NUMA.|

### <a name="optimizing-mpi-collectives"></a>Ottimizzazione dei collettivi MPI

Sono disponibili altre opzioni che è possibile provare, soprattutto se le operazioni collettive utilizzano tempo notevole. Intel MPI 2019 update5+ supporta il provider mlx e usa il framework UCX per comunicare con InfiniBand. Supporta anche HCOLL.
```bash
export FI_PROVIDER=mlx
export I_MPI_COLL_EXTERNAL=1
```

### <a name="non-sr-iov-vms"></a>Macchine virtuali non SR-IOV

Per le macchine virtuali non SR-IOV, un esempio di download della versione di valutazione gratuita del [runtime](https://registrationcenter.intel.com/en/forms/?productid=1740) 5.x è il seguente:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Per la procedura di installazione, vedere [Intel MPI Library installation guide](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) (Guida all'installazione di Intel MPI Library).
Facoltativamente, è possibile abilitare ptrace per i processi non radice non del debugger (necessari per le versioni più recenti di Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Per le versioni dell'immagine di macchina virtuale SUSE Linux Enterprise Server - SLES 12 SP3 per HPC, SLES 12 SP3 per HPC (Premium), SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium), SLES 12 SP4 e SLES 15, i driver RDMA vengono installati e i pacchetti Intel MPI vengono distribuiti nella macchina virtuale. Installare Intel MPI eseguendo il comando seguente:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich"></a>MVAPICH

Di seguito è riportato un esempio di compilazione di MVAPICH2. Si noti che potrebbero essere disponibili versioni più recenti rispetto a quelle usate di seguito.
```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Un esempio di esecuzione del microbenchmark di latenza OSU è il seguente:
```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

L'elenco seguente contiene alcuni argomenti consigliati di `mpirun`.
```bash
export MV2_CPU_BINDING_POLICY=scatter
export MV2_CPU_BINDING_LEVEL=numanode
export MV2_SHOW_CPU_BINDING=1
export MV2_SHOW_HCA_BINDING=1

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
dove:

|Parametro|Descrizione                                        |
|---------|---------------------------------------------------|
|`MV2_CPU_BINDING_POLICY`   |Specifica i criteri di associazione da usare, che influiscono sull'aggiunta dei processi agli ID core. In questo caso, si specifica la dispersione, in modo che i processi siano distribuiti uniformemente tra i domini NUMA.|
|`MV2_CPU_BINDING_LEVEL`|Specifica la posizione in cui aggiungere i processi. In questo caso, impostarlo su numanode, ovvero i processi vengono aggiunti alle unità dei domini NUMA.|
|`MV2_SHOW_CPU_BINDING` |Specifica se si vogliono ottenere informazioni di debug sulla posizione in cui sono stati aggiunti i processi.|
|`MV2_SHOW_HCA_BINDING` |Specifica se si vogliono ottenere informazioni di debug sulla scheda del canale host usata da ogni processo.|

## <a name="platform-mpi"></a>MPI della piattaforma

Installare i pacchetti necessari per Platform MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Seguire il processo di installazione.

I comandi seguenti sono esempi di esecuzione di pingpong MPI e allreduce usando Platform MPI in macchine virtuali HBv3 che usano le immagini di macchine virtuali CentOS-HPC 7.6, 7.8 e 8.1.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Installare UCX come descritto in precedenza. MPICH di compilazione.

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

## <a name="osu-mpi-benchmarks"></a>Benchmark OSU MPI

Scaricare [OSU MPI Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) e untar.

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

I benchmark MPI sono disponibili nella `mpi/` cartella .


## <a name="discover-partition-keys"></a>Individuare le chiavi di partizione

Individuare le chiavi di partizione (p-keys) per la comunicazione con altre macchine virtuali all'interno dello stesso tenant (set di disponibilità o set di scalabilità di macchine virtuali).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Il più grande dei due è la chiave del tenant che deve essere usata con MPI. Esempio: se le chiavi p sono le seguenti, 0x800b devono essere usate con MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Usare la partizione diversa dalla chiave di partizione predefinita (0x7fff). UCX richiede che il valore MSB di p-key sia cancellato. Ad esempio, impostare UCX_IB_PKEY come 0x000b per 0x800b.

Si noti anche che, finché il tenant (set di disponibilità o set di scalabilità di macchine virtuali) esiste, i PKEY rimangono invariati. Questo vale anche quando i nodi vengono aggiunti/eliminati. I nuovi tenant ottengono PKEY diversi.


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

La sintassi precedente presuppone una home directory condivisa, altrimenti la directory .ssh deve essere copiata in ogni nodo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle macchine virtuali serie [H](../../sizes-hpc.md) e serie [N](../../sizes-gpu.md) abilitate per [InfiniBand](../../sizes-hpc.md#rdma-capable-instances)
- Esaminare la [panoramica della serie HBv3](hbv3-series-overview.md) e [la panoramica della serie HC.](hc-series-overview.md)
- Per informazioni sugli annunci più recenti, sugli esempi di carichi di lavoro HPC e sui risultati delle [prestazioni, Calcolo di Azure Blog della community tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Per un quadro generale sull'architettura per l'esecuzione di carichi di lavoro HPC, vedere [HPC (High Performance Computing) in Azure](/azure/architecture/topics/high-performance-computing/).
