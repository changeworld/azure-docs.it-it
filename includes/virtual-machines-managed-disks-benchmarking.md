---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094629"
---
## <a name="warm-up-the-cache"></a>Riscaldamento della cache

Il disco con memorizzazione nella cache dell'host ReadOnly è in grado di fornire un numero maggiore di IOPS rispetto al limite del disco. Per ottenere queste prestazioni di lettura massime dalla cache dell'host, è prima di tutto necessario preparare la cache del disco. Ciò assicura che le operazioni di I/O di lettura che lo strumento di benchmarking eseguirà sul volume CacheReads raggiungano effettivamente la cache e non direttamente il disco. I riscontri nella cache comportano un numero maggiore di IOPS dal disco single cache enabled.

> [!IMPORTANT]
> È necessario scaldare la cache prima di eseguire i benchmark ogni volta che viene riavviata la macchina virtuale.

## <a name="diskspd"></a>DISKSPD

[Scaricare lo strumento DISKSP](https://github.com/Microsoft/diskspd) nella macchina virtuale. DISKSPD è uno strumento che è possibile personalizzare per creare carichi di lavoro sintetici personalizzati. Per eseguire i test di benchmarking verrà utilizzata la stessa configurazione descritta in precedenza. È possibile modificare le specifiche per testare carichi di lavoro diversi.

In questo esempio viene usato il set di parametri di base seguente:

- -c200G: crea o ricrea il file di esempio usato nel test. Può essere impostata in byte, KiB, MiB, GiB o blocchi. In questo caso, viene usato un file di grandi dimensioni del file di destinazione 200-GiB per ridurre al minimo la memorizzazione nella cache della memoria.
- -W100: specifica la percentuale di operazioni che sono richieste di scrittura (-W0 equivale a 100% Read).
- -b4K: indica le dimensioni del blocco in byte, KiB, MiB o GiB. In questo caso, la dimensione del blocco 4K viene usata per simulare un test di I/O casuale.
- -F4: imposta un totale di quattro thread.
- -r: indica il test di I/O casuale (esegue l'override del parametro-s).
- -O128: indica il numero di richieste di I/O in attesa per ogni destinazione per thread. Questa operazione è nota anche come profondità della coda. In questo caso, viene usato 128 per sottolineare la CPU.
- -W7200: specifica la durata del periodo di riscaldamento prima dell'avvio delle misure.
- -D30: specifica la durata del test, escluso il riscaldamento.
- -SH: Disabilita la memorizzazione nella cache di scrittura software e hardware (equivalente a-SUW).

Per un elenco completo dei parametri, vedere il [repository GitHub](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

### <a name="maximum-write-iops"></a>IOPS massime di scrittura
Viene usata una profondità della coda elevata di 128, una dimensione del blocco ridotta di 8 KB e quattro thread di lavoro per la guida delle operazioni di scrittura. I ruoli di lavoro di scrittura indirizzano il traffico sul volume "NoCacheWrites", che include tre dischi con la cache impostata su "None".

Eseguire il comando seguente per 30 secondi di riscaldamento e 30 secondi di misurazione:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

I risultati mostrano che la macchina virtuale Standard_D8ds_v4 sta distribuendo il limite massimo di IOPS di scrittura di 12.800.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="Per 3208642560 byte totali, numero massimo di I/O totale di 391680, con un totale di 101,97 MiB/s e un totale di 13052,65 I/O al secondo.":::

### <a name="maximum-read-iops"></a>IOPS massime di lettura

Viene usata una profondità della coda elevata di 128, una dimensione del blocco ridotta di quattro KB e quattro thread di lavoro per guidare le operazioni di lettura. I thread di lavoro di lettura stanno indirizzando il traffico sul volume "CacheReads", che ha un disco con cache impostata su "ReadOnly".

Eseguire il comando seguente per due ore di riscaldamento e 30 secondi di misurazione:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

I risultati mostrano che la macchina virtuale Standard_D8ds_v4 sta distribuendo il limite massimo di IOPS di lettura di 77.000.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="Per 9652785152 byte totali, sono disponibili 2356637 I/o totali, al 306,72 MiB/s totale e un totale di 78521,23 I/o al secondo.":::

### <a name="maximum-throughput"></a>Velocità effettiva massima

Per ottenere la velocità effettiva massima di lettura e scrittura, è possibile passare a una dimensione del blocco maggiore di 64 KB.
## <a name="fio"></a>FIO

FIO è uno strumento popolare per il benchmarking dell'archiviazione sulle VM Linux. Offre la flessibilità necessaria per selezionare diverse dimensioni di I/O e letture e scritture sequenziali o casuali. Genera thread di lavoro o processi per l'esecuzione delle operazioni I/O specificate. È possibile specificare il tipo di operazioni I/O che ogni thread di lavoro deve eseguire usando i file processo. È stato creato un file processo per ogni scenario illustrato negli esempi seguenti. È possibile cambiare le specifiche di questi file processo per il benchmarking di diversi carichi di lavoro in esecuzione sull'Archiviazione Premium. Negli esempi viene usato un Standard_D8ds_v4 che esegue **Ubuntu**. Usare la stessa configurazione descritta all'inizio della sezione benchmark e scaldare la cache prima di eseguire i test di benchmark.

Prima di iniziare, [scaricare FIO](https://github.com/axboe/fio) e installarlo nella macchina virtuale.

Eseguire il comando seguente per Ubuntu:

```
apt-get install fio
```

Vengono usati quattro thread di lavoro per la gestione delle operazioni di scrittura e quattro thread di lavoro per la gestione delle operazioni di lettura sui dischi. I ruoli di lavoro di scrittura indirizzano il traffico sul volume "nocache", che include tre dischi con la cache impostata su "None". I thread di lavoro di lettura stanno indirizzando il traffico sul volume "readcache", che ha un disco con cache impostata su "ReadOnly".

### <a name="maximum-write-iops"></a>IOPS massime di scrittura

 Creare il file processo con le specifiche seguenti per ottenere il valore massimo per le operazioni IOPS di scrittura. Assegnare al file il nome "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Notare gli elementi chiave seguenti conformi alle indicazioni di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per ottenere il valore massimo per IOPS:  

* Profondità della coda elevata pari a 256.  
* Dimensione di blocco ridotta pari a 4 KB.  
* Più thread che eseguono scritture casuali.

Eseguire il comando seguente per attivare il test FIO per 30 secondi:  

```
sudo fio --runtime 30 fiowrite.ini
```

Durante l'esecuzione del test, è possibile visualizzare il numero di operazioni IOPS di scrittura gestite dalla macchina virtuale e dai dischi Premium. Come illustrato nell'esempio seguente, la macchina virtuale Standard_D8ds_v4 sta distribuendo il limite massimo di IOPS di scrittura di 12.800 IOPS.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Il numero di operazioni di i/o al secondo in scrittura e SSD Premium viene recapito, indica che le Scritture sono 13.1 k IOPS":::

### <a name="maximum-read-iops"></a>IOPS massime di lettura

 Creare il file processo con le specifiche seguenti per ottenere il valore massimo per le operazioni IOPS di lettura. Assegnare al file il nome "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Notare gli elementi chiave seguenti conformi alle indicazioni di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per ottenere il valore massimo per IOPS:

* Profondità della coda elevata pari a 256.  
* Dimensione di blocco ridotta pari a 4 KB.  
* Più thread che eseguono scritture casuali.

Eseguire il comando seguente per attivare il test FIO per 30 secondi:

```
sudo fio --runtime 30 fioread.ini
```

Durante l'esecuzione del test, è possibile visualizzare il numero di operazioni IOPS di lettura gestite dalla macchina virtuale e dai dischi Premium. Come illustrato nell'esempio seguente, la macchina virtuale Standard_D8ds_v4 sta distribuendo più di 77.000 operazioni di i/o al secondo. Ciò dipende da una combinazione delle prestazioni del disco e della cache.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Screenshot del numero di operazioni di i/o al secondo in scrittura di VM e SSD Premium, indica che le letture sono 78.6 k.":::

### <a name="maximum-read-and-write-iops"></a>IOPS massime di lettura e scrittura

 Creare il file processo con le specifiche seguenti per ottenere il valore massimo per le operazioni IOPS combinate di lettura e scrittura. Assegnare al file il nome "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

Notare gli elementi chiave seguenti conformi alle indicazioni di progettazione illustrate nelle sezioni precedenti. Queste specifiche sono essenziali per ottenere il valore massimo per IOPS:

* Profondità della coda elevata pari a 128.  
* Dimensione di blocco ridotta pari a 4 KB.  
* Più thread che eseguono scritture e letture casuali.

Eseguire il comando seguente per attivare il test FIO per 30 secondi:

```
sudo fio --runtime 30 fioreadwrite.ini
```

Durante l'esecuzione del test, è possibile visualizzare il numero di operazioni IOPS combinate di lettura e scrittura gestite dalla macchina virtuale e dai dischi Premium. Come illustrato nell'esempio seguente, la macchina virtuale Standard_D8ds_v4 distribuisce più di 90.000 operazioni di i/o di lettura e scrittura combinate. Ciò dipende da una combinazione delle prestazioni del disco e della cache.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="Operazioni di i/o di lettura e scrittura combinate, indica che le letture sono 78.3 k e le Scritture sono 12,6 k IOPS.":::

### <a name="maximum-combined-throughput"></a>Velocità effettiva massima combinata

 Per ottenere la velocità effettiva massima combinata di lettura e scrittura, usare una dimensione di blocco superiore e una profondità della coda elevata con più thread che eseguono letture e scritture. È possibile usare una dimensione di blocco pari a 64 KB e una profondità della coda pari a 128.