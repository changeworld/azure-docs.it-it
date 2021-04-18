---
title: Ridimensionamento di applicazioni HPC - Macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come ridimensionare le applicazioni HPC in macchine virtuali di Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f81d40abdf402b1e19090c5375dfa8c335418248
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600965"
---
# <a name="scaling-hpc-applications"></a>Ridimensionamento di applicazioni HPC

Le prestazioni ottimali di scalabilità verticale e orizzontale delle applicazioni HPC in Azure richiedono esperimenti di ottimizzazione e ottimizzazione delle prestazioni per il carico di lavoro specifico. Questa sezione e le pagine specifiche della serie di macchine virtuali offrono indicazioni generali per il ridimensionamento delle applicazioni.

## <a name="application-setup"></a>Configurazione dell'applicazione
Il [repo azurehpc](https://github.com/Azure/azurehpc) contiene molti esempi di:
- Configurazione ed esecuzione ottimale [delle](https://github.com/Azure/azurehpc/tree/master/apps) applicazioni.
- Configurazione di [file system e cluster](https://github.com/Azure/azurehpc/tree/master/examples).
- [Esercitazioni su](https://github.com/Azure/azurehpc/tree/master/tutorials) come iniziare facilmente a usare alcuni flussi di lavoro comuni delle applicazioni.

## <a name="optimally-scaling-mpi"></a>Scalabilità ottimale di MPI 

I suggerimenti seguenti si applicano per garantire efficienza, prestazioni e coerenza ottimali per il ridimensionamento delle applicazioni:

- Per i processi di scalabilità ridotta ,ad esempio < 256.000 connessioni, usare l'opzione :
   ```bash
   UCX_TLS=rc,sm
   ```

- Per i processi su larga scala (ad esempio, > 256.000 connessioni) usare l'opzione :
   ```bash
   UCX_TLS=dc,sm
   ```

- Nell'esempio precedente, per calcolare il numero di connessioni per il processo MPI, usare:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Routing adattivo
Il routing adattivo consente alle macchine virtuali di Azure (VM) che eseguono EDR e HDR InfiniBand di rilevare ed evitare automaticamente la congestione della rete selezionando dinamicamente percorsi di rete più ottimali. Di conseguenza, AR offre una latenza e una larghezza di banda migliorate nella rete InfiniBand, che a sua volta determinano prestazioni più elevate ed efficienza di ridimensionamento. Per altre informazioni, vedere [l'articolo techcommunity](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217).

## <a name="process-pinning"></a>Blocco dei processi

- Aggiungere i processi ai core usando un approccio di blocco sequenziale (anziché un approccio di bilanciamento automatico). 
- L'associazione tramite Numa/Core/HwThread è migliore dell'associazione predefinita.
- Per le applicazioni parallele ibride (OpenMP+MPI), usare 4 thread e 1 classificazione MPI per CCX nelle dimensioni delle macchine virtuali HB e HBv2.
- Per le applicazioni MPI pure, provare con 1-4 classificazioni MPI per CCX per ottenere prestazioni ottimali nelle dimensioni delle macchine virtuali HB e HBv2.
- Alcune applicazioni con estrema sensibilità alla larghezza di banda della memoria possono trarre vantaggio dall'uso di un numero ridotto di core per CCX. Per queste applicazioni, l'uso di 3 o 2 core per CCX può ridurre i problemi di larghezza di banda della memoria e produrre prestazioni reali più elevate o una scalabilità più coerente. In particolare, MPI Allreduce può trarre vantaggio da questo approccio.
- Per le esecuzioni di scalabilità significativamente più grandi, è consigliabile usare i trasporti UD o RC+UD ibridi. Molte librerie MPI/librerie di runtime lo fanno internamente,ad esempio UCX o MVAPICH2. Controllare le configurazioni del trasporto per le esecuzioni su larga scala.

## <a name="compiling-applications"></a>Compilazione di applicazioni
<br>
<details>
<summary>Fare clic per espandere</summary>

Anche se non è necessario, la compilazione di applicazioni con flag di ottimizzazione appropriati offre le migliori prestazioni di scalabilità verticale nelle macchine virtuali HB e serie HC.

### <a name="amd-optimizing-cc-compiler"></a>Ottimizzazione amD del compilatore C/C++

Il sistema del compilatore AMD Optimizing C/C++ Compiler (AOCC) offre un elevato livello di ottimizzazioni avanzate, multithreading e supporto del processore che include ottimizzazione globale, vettorizzazione, analisi inter-procedurali, trasformazioni del ciclo e generazione di codice. I file binari del compilatore AOCC sono adatti per i sistemi Linux con GNU C Library (glibc) versione 2.17 e successive. La suite di compilatori è costituita da un compilatore C/C++ (clang), un compilatore Fortran (FLANG) e un front-end di Fortran per Clang (Dragon Egg).

### <a name="clang"></a>Clang

Clang è un compilatore C, C++ e Objective-C che gestisce la pre-elaborazione, l'analisi, l'ottimizzazione, la generazione del codice, l'assembly e il collegamento. Clang supporta il flag per abilitare la generazione e l'ottimizzazione del codice migliori per  `-march=znver1` l'architettura x86 basata su Zen di AMD.

### <a name="flang"></a>FLANG

Il compilatore FLANG è una recente aggiunta alla suite AOCC (aggiunta ad aprile 2018) ed è attualmente in versione non definitiva per il download e il test da parte degli sviluppatori. In base a Fortran 2008, AMD estende la versione GitHub di FLANG ( https://github.com/flang-compiler/flang) . Il compilatore FLANG supporta tutte le opzioni del compilatore Clang e un numero aggiuntivo di opzioni del compilatore specifiche di FLANG.

### <a name="dragonegg"></a>DragonEgg

DragonEgg è un plug-in gcc che sostituisce gli ottimizzatori e i generatori di codice di GCC con quelli del progetto LLVM. DragonEgg fornito con AOCC funziona con gcc-4.8.x, è stato testato per destinazioni x86-32/x86-64 ed è stato usato correttamente in diverse piattaforme Linux.

GFortran è il front-end effettivo per i programmi Fortran responsabili della pre-elaborazione, dell'analisi e dell'analisi semantica che genera la rappresentazione intermedia GCC GIMPLE. DragonEgg è un plug-in GNU che collega il flusso di compilazione GFortran. Implementa l'API del plug-in GNU. Con l'architettura del plug-in, DragonEgg diventa il driver del compilatore, guidando le diverse fasi della compilazione.  Dopo aver seguito le istruzioni di download e installazione, Dragon Egg può essere richiamato usando: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Compilatore PGI
PGI Community Edition ver. 17 è confermato per l'utilizzo con AMD EPYC. Una versione di STREAM compilata con PGI offre larghezza di banda di memoria completa della piattaforma. Anche la nuova Community Edition 18.10 (novembre 2018) dovrebbe funzionare correttamente. Di seguito è riportato un esempio di interfaccia della riga di comando per eseguire il compilatore in modo ottimale con il compilatore Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Compilatore Intel
Intel Compiler ver. 18 è confermato per l'utilizzo con AMD EPYC. Di seguito è riportato un esempio di interfaccia della riga di comando per il compilatore in modo ottimale con il compilatore Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Compilatore GCC 
Per HPC, AMD consiglia il compilatore GCC 7.3 o versione più recente. Le versioni precedenti, ad esempio la 4.8.5 inclusa in RHEL/CentOS 7.4, non sono consigliate. GCC 7.3 e versioni successive offrono prestazioni significativamente più elevate nei test HPL, HPCG e DGEMM.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Passaggi successivi

- Testare le proprie conoscenze con [un modulo di apprendimento sull'ottimizzazione delle applicazioni HPC in Azure.](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/)
- Esaminare la [panoramica della serie HBv3 e](hbv3-series-overview.md) la panoramica della serie [HC.](hc-series-overview.md)
- Per informazioni sugli annunci più recenti, sugli esempi di carichi di lavoro HPC e sui risultati [delle prestazioni, Calcolo di Azure Blog della community tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Altre informazioni su [HPC](/azure/architecture/topics/high-performance-computing/) in Azure.
