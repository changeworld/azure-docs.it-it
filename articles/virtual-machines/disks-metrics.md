---
title: Metriche del disco
description: Esempi di metriche di espansione del disco
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674982"
---
# <a name="disk-performance-metrics"></a>Metriche delle prestazioni del disco
Azure offre metriche nella portale di Azure che forniscono informazioni dettagliate sulle prestazioni delle macchine virtuali e dei dischi. Le metriche possono anche essere recuperate tramite una chiamata API. Questo articolo è suddiviso in 3 sottosezioni:

- I/o **su disco, velocità effettiva e profondità della coda** : queste metriche consentono di visualizzare le prestazioni di archiviazione dal punto di vista di un disco e di una macchina virtuale.
- **Metriche** di espansione del disco: si tratta della metrica che garantisce l'osservabilità della [nostra funzionalità di espansione sui nostri dischi](disk-bursting.md) Premium.
- **Metriche di utilizzo io di archiviazione** : queste metriche consentono di diagnosticare i colli di bottiglia nelle prestazioni di archiviazione con i dischi. 

Tutte le metriche vengono emesse ogni minuto, ad eccezione della metrica di percentuale del credito di espansione, che viene emessa ogni 5 minuti.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Operazioni di i/o su disco, velocità effettiva e profondità della coda
Sono disponibili le metriche seguenti per ottenere informazioni sulle prestazioni di i/o su disco e VM, velocità effettiva e profondità della coda:

- **Profondità coda del disco del sistema operativo**: numero di richieste di i/o in attesa correnti in attesa di lettura o scrittura nel disco del sistema operativo.
- **Byte letti da disco del sistema operativo/sec**: numero di byte letti in un secondo dal disco del sistema operativo.
- **Operazioni di lettura disco del sistema operativo/sec**: numero di operazioni di input lette in un secondo dal disco del sistema operativo.
- **Byte scritti disco del sistema operativo/sec**: numero di byte scritti in un secondo dal disco del sistema operativo.
- **Operazioni di scrittura disco del sistema operativo/sec**: numero di operazioni di output scritte in un secondo dal disco del sistema operativo.
- **Profondità della coda del disco dati**: numero di richieste di i/o in attesa correnti in attesa di lettura o scrittura nei dischi dati.
- **Byte di lettura disco dati/sec**: numero di byte letti in un secondo dai dischi dati.
- **Operazioni di lettura disco dati/sec**: numero di operazioni di input lette in un secondo da dischi dati.
- **Byte scrittura disco dati/sec**: numero di byte scritti in un secondo dai dischi dati.
- **Operazioni di scrittura disco dati/sec**: numero di operazioni di output scritte in un secondo da dischi dati.
- **Byte letti da disco/sec**: numero totale di byte letti in un secondo da tutti i dischi collegati a una macchina virtuale.
- **Operazioni di lettura disco/sec**: numero di operazioni di input lette in un secondo da tutti i dischi collegati a una macchina virtuale.
- **Byte scritti su disco/sec**: numero di byte scritti in un secondo da tutti i dischi collegati a una macchina virtuale.
- **Operazioni di scrittura su disco/sec**: numero di operazioni di output scritte in un secondo da tutti i dischi collegati a una macchina virtuale.

## <a name="bursting-metrics"></a>Metriche di espansione
Le metriche seguenti consentono di osservare la funzionalità di espansione [sui nostri dischi](disk-bursting.md) Premium:

- **Larghezza di banda massima del disco dati**: limite di velocità effettiva per cui è possibile aumentare il numero di dischi di dati.
- **Disco del sistema operativo max picchi di banda**: il limite di velocità effettiva a cui il disco del sistema operativo può aumentare.
- **Numero massimo** di operazioni di i/o al secondo del disco dati: limite IOPS a cui i dischi dati possono aumentare.
- **Numero massimo di IOPS del disco del sistema operativo**: limite IOPS a cui il disco del sistema operativo può aumentare.
- **Larghezza di banda di destinazione del disco dati**: il limite di velocità effettiva che il disco dati può raggiungere senza picchi.
- **Larghezza di banda di destinazione del disco del sistema operativo**: il limite di velocità effettiva che il disco del sistema operativo può raggiungere senza picchi.
- **IOPS per destinazione disco dati**: limite IOPS che i dischi dati possono raggiungere senza picchi.
- **IOPS del disco del sistema operativo**: il limite di IOPS che i dischi dati possono raggiungere senza picchi.
- **Percentuale di crediti bps con picchi di dati usati**: percentuale accumulata del volume di velocità effettiva usato per i dischi dati. Generato in un intervallo di 5 minuti.
- Il **disco del sistema operativo ha usato i crediti bps in percentuale**: la percentuale accumulata della velocità effettiva utilizzata per il disco del sistema operativo. Generato in un intervallo di 5 minuti.
- **Percentuale di crediti di i/o su disco usato**: percentuale accumulata del tempo di IOPS usato per i dischi dati. Generato in un intervallo di 5 minuti.
- Percentuale di crediti di i/o **su disco del sistema operativo**: percentuale accumulata del tempo di IOPS usato per il disco del sistema operativo. Generato in un intervallo di 5 minuti.

## <a name="storage-io-utilization-metrics"></a>Metriche di utilizzo IO di archiviazione
Le metriche seguenti consentono di diagnosticare il collo di bottiglia nella combinazione di macchina virtuale e disco. Queste metriche sono disponibili solo quando si usa una macchina virtuale abilitata per Premium. Queste metriche sono disponibili per tutti i tipi di disco, ad eccezione di ultra. 

Metriche che consentono di diagnosticare la limitazione di i/o del disco:

- **Percentuale di IOPS del disco dati utilizzata**: la percentuale calcolata dal IOPS del disco dati completata rispetto al IOPS del disco dati di cui è stato effettuato il provisioning. Se questa quantità è pari al 100%, l'applicazione in esecuzione è IO limitata dal limite IOPS del disco dati.
- Percentuale di utilizzo della **larghezza di banda del disco dati**: percentuale calcolata dalla velocità effettiva del disco dati completata sulla velocità effettiva del disco dati sottoposta a provisioning. Se questa quantità è pari al 100%, l'applicazione in esecuzione è IO limitata dal limite di larghezza di banda del disco dati.
- **Percentuale di IOPS del disco del sistema operativo**: la percentuale calcolata dal IOPS del disco del sistema operativo è stata completata sul disco del sistema operativo con provisioning. Se questa quantità è pari al 100%, l'applicazione in esecuzione è IO limitata dal limite IOPS del disco del sistema operativo.
- **Percentuale** di utilizzo della larghezza di banda del disco del sistema operativo: percentuale calcolata dalla velocità effettiva del disco del sistema operativo completata rispetto alla velocità effettiva del disco del sistema operativo Se questa quantità è pari al 100%, l'applicazione in esecuzione è IO limitata dal limite di larghezza di banda del disco del sistema operativo.

Metriche che consentono di diagnosticare la limitazione delle operazioni di i/o della macchina virtuale:

- **Percentuale di IOPS utilizzata nella cache della VM**: percentuale calcolata in base al numero totale di IOPS completati nel limite massimo di IOPS della macchina virtuale memorizzato nella cache. Se questa quantità è pari al 100%, l'applicazione in esecuzione è IO limitata dal limite di IOPS memorizzati nella cache della VM.
- Percentuale di utilizzo della **larghezza di banda della VM memorizzata nella** cache: percentuale calcolata in base alla velocità effettiva totale del disco completata sulla velocità effettiva massima della macchina virtuale Se questa quantità è pari al 100%, l'applicazione in esecuzione è IO limitata dal limite di larghezza di banda memorizzato nella cache della macchina virtuale.
- Percentuale di IOPS non **memorizzata nella cache della VM utilizzata**: la percentuale calcolata in base al numero totale di IOPS in una macchina virtuale è stata completata sul limite massimo di IOPS della macchina virtuale non memorizzato nella cache. Se questa quantità è pari al 100%, l'applicazione in esecuzione è IO limitata dal limite di IOPS non memorizzato nella cache della macchina virtuale.
- Percentuale di utilizzo della **larghezza di banda non memorizzata nella cache**: la percentuale calcolata in base alla velocità effettiva totale del disco in una macchina virtuale è stata completata sulla velocità effettiva massima di provisioning della macchina virtuale. Se questa quantità è pari al 100%, l'applicazione in esecuzione è IO limitata dal limite di larghezza di banda non memorizzato nella cache della macchina virtuale.

## <a name="storage-io-metrics-example"></a>Esempio di metriche di i/o di archiviazione

Verrà ora illustrato un esempio di come usare queste nuove metriche di utilizzo di i/o di archiviazione per consentire il debug di un collo di bottiglia nel sistema. L'installazione del sistema è identica a quella dell'esempio precedente, ma questa volta il disco del sistema operativo collegato *non* viene memorizzato nella cache.

**Installazione**

- Standard_D8s_v3
  - IOPS memorizzati nella cache: 16.000
  - IOPS non memorizzati nella cache: 12.800
- Disco del sistema operativo P30
  - IOPS: 5.000
  - Caching dell'host: **disabilitato**
- Due dischi dati P30 × 2
  - IOPS: 5.000
  - Caching dell'host: **lettura/scrittura**
- Due dischi dati P30 × 2
  - IOPS: 5.000
  - Caching dell'host: **disabilitato**

Eseguire un test di benchmarking sulla macchina virtuale e sulla combinazione di dischi che crea l'attività di i/o. Per informazioni su come eseguire il benchmarking di i/o di archiviazione in Azure, vedere [benchmark your application on archiviazione su disco di Azure](disks-benchmarks.md). Dallo strumento di benchmarking è possibile vedere che la combinazione di VM e dischi può raggiungere 22.800 IOPS:

![Screenshot dell'output f i o che Mostra r = 22.8 k evidenziato.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



Il Standard_D8s_v3 può ottenere un totale di 28.600 IOPS. Usando le metriche, analizziamo cosa sta succedendo e identifichiamo il collo di bottiglia di i/o di archiviazione. Nel riquadro sinistro selezionare **metrica**:

![Screenshot che mostra le metriche evidenziate nel riquadro sinistro.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Esaminiamo prima di tutto la metrica della **percentuale di IOPS utilizzata nella cache della macchina virtuale** :

![Screenshot che mostra la percentuale di utilizzo di V M memorizzato nella cache I/O.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Questa metrica indica che viene usato il 61% degli IOPS 16.000 assegnati ai IOPS memorizzati nella cache nella macchina virtuale. Questa percentuale indica che il collo di bottiglia di i/o di archiviazione non è con i dischi memorizzati nella cache perché non è al 100%. Si osserverà ora la metrica **della percentuale** di operazioni di i/o al secondo utilizzata per la macchina virtuale:

![Screenshot che mostra la percentuale di utilizzo della V M non memorizzata nella cache.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Questa metrica è al 100%. Indica che vengono utilizzati tutti gli IOPS 12.800 assegnati alle operazioni di i/o non memorizzate nella cache nella macchina virtuale. Un modo per correggere questo problema consiste nel modificare le dimensioni della macchina virtuale con una dimensione maggiore in grado di gestire i/o aggiuntivi. Ma prima di procedere, esaminiamo il disco collegato per individuare il numero di IOPS che vengono visualizzati. Controllare il disco del sistema operativo esaminando la **percentuale di IOPS utilizzata del disco del sistema operativo**:

![Screenshot che mostra la percentuale di utilizzo del disco I/o.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Questa metrica indica che è in uso circa il 90% del 5.000 IOPS di cui è stato effettuato il provisioning per questo disco del sistema operativo P30. Questa percentuale indica che non è presente alcun collo di bottiglia sul disco del sistema operativo. Controllare ora i dischi dati collegati alla VM esaminando la **percentuale di IOPS del disco dati utilizzata**:

![Screenshot che mostra la percentuale di utilizzo di I/O del disco dati.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Questa metrica indica che la percentuale di IOPS media utilizzata in tutti i dischi collegati è intorno al 42%. Questa percentuale viene calcolata in base ai IOPS usati dai dischi e non viene servita dalla cache dell'host. Si esaminerà in dettaglio questa metrica applicando la *suddivisione* in base a queste metriche e suddividendo il valore lun:

![Screenshot che mostra la percentuale di utilizzo dei dischi dati con suddivisione.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Questa metrica indica che i dischi dati collegati in LUN 3 e 2 usano circa il 85% delle operazioni di i/o al secondo di cui è stato effettuato il provisioning. Di seguito è riportato un diagramma di come appare l'i/o dell'architettura di VM e dischi:

![Diagramma dell'esempio di metrica di archiviazione O di archiviazione.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle metriche di monitoraggio di Azure](../azure-monitor/essentials/data-platform-metrics.md)
- [Spiegazione dell'aggregazione delle metriche](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../azure-monitor/alerts/alerts-metric.md)