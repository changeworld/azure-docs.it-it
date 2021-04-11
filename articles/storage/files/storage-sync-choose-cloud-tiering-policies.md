---
title: Scegliere Sincronizzazione file di Azure criteri di suddivisione in livelli cloud | Microsoft Docs
description: Informazioni dettagliate sugli elementi da tenere presenti quando si sceglie Sincronizzazione file di Azure criteri di suddivisione in livelli cloud.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/24/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 01bf9ac6a3bfcb30fb6e6a6f9d56de3f9f516f03
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059276"
---
# <a name="choose-cloud-tiering-policies"></a>Scegliere i criteri di suddivisione in livelli cloud

Questo articolo fornisce indicazioni per gli utenti che selezionano e modificano i criteri di suddivisione in livelli nel cloud. Prima di leggere questo articolo, assicurarsi di comprendere come funziona la suddivisione in livelli nel cloud. Per informazioni di base sulla suddivisione in livelli nel cloud, vedere [comprendere sincronizzazione file di Azure](storage-sync-cloud-tiering-overview.md)suddivisione in livelli nel cloud. Per una spiegazione approfondita dei criteri di suddivisione in livelli nel cloud con esempi, vedere [sincronizzazione file di Azure criteri](storage-sync-cloud-tiering-policy.md)di suddivisione in livelli nel cloud.

## <a name="limitations"></a>Limitazioni
- La suddivisione in livelli nel cloud non è supportata nel volume di sistema Windows.

- È comunque possibile abilitare la suddivisione in livelli nel cloud se si dispone di una quota FSRM a livello di volume. Una volta impostata una quota FSRM, le API di query di spazio libero che vengono chiamate segnalano automaticamente lo spazio disponibile nel volume in base all'impostazione della quota. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Dimensioni minime del file per un file a livello

Per gli agenti versione 9 e successive, le dimensioni minime del file per un file a livello sono basate sulle dimensioni del cluster file system. Le dimensioni minime dei file idonee per la suddivisione in livelli nel cloud vengono calcolate in base alla dimensione del cluster e a un minimo di 8 KB. La tabella seguente illustra le dimensioni minime dei file che possono essere suddivise a livelli, in base alle dimensioni del cluster di volumi:

|Dimensioni cluster del volume (byte) |I file di questa dimensione o di dimensioni maggiori possono essere a livelli  |
|----------------------------|---------|
|4 KB o inferiore (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 kB   |
|64 KB (65536)    | 128 KB  |
|128 KB (131072) | 256 KB |
|256 KB (262144) | 512 KB |
|512 KB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

Le dimensioni del cluster fino a 2 MB sono supportate con Sincronizzazione file di Azure agente versione 12, ma per dimensioni maggiori la suddivisione in livelli cloud non funziona.

Tutti i file System usati da Windows, organizzano il disco rigido in base alle dimensioni del cluster, note anche come dimensioni dell'unità di allocazione. Dimensioni del cluster rappresenta la quantità minima di spazio su disco che può essere usata per contenere un file. Quando le dimensioni dei file non vengono riportate a un multiplo pari delle dimensioni del cluster, è necessario usare spazio aggiuntivo per contenere il file fino al multiplo successivo delle dimensioni del cluster.

Sincronizzazione file di Azure è supportato nei volumi NTFS con Windows Server 2012 R2 e versioni successive. Nella tabella seguente vengono descritte le dimensioni predefinite del cluster quando si crea un nuovo volume NTFS con Windows Server 2019.

|Dimensioni del volume    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB – 16 TB   | 4 KB                |
|16TB-32 TB   | 8 KB                |
|32 TB-64 TB   | 16 KB               |
|64 TB-128 TB  | 32 KB               |
|128TB-256 TB | 64 KB (in precedenza, dimensione max) |
|256 TB – 512 TB| 128 KB              |
|512 TB – 1 PB  | 256 KB              |
|1 PB – 2 PB    | 512 KB              |
|2 TB – 4 PB    | 1024 KB             |
|DA 4 TB A 8 TB    | 2048 KB (dimensione max)  |
|> 8 TB         | non supportato       |

Al momento della creazione del volume, è possibile che il volume sia stato formattato manualmente con dimensioni del cluster diverse. Se il volume deriva da una versione precedente di Windows, le dimensioni predefinite del cluster possono essere diverse. [Questo articolo contiene informazioni più dettagliate sulle dimensioni predefinite del cluster.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Anche se si sceglie una dimensione del cluster inferiore a 4 KB, viene applicato un limite di 8 KB come dimensione del file più piccola che può essere suddivisa a livelli. (Anche se le dimensioni del cluster tecnicamente 2x equivalgono a meno di 8 KB).

Il motivo del valore minimo assoluto è il modo in cui NTFS archivia i file di dimensioni molto ridotte da 1 KB a 4 KB. A seconda degli altri parametri del volume, è possibile che i file di piccole dimensioni non vengano archiviati in un cluster su disco. È possibilmente più efficiente archiviare tali file direttamente nella tabella dei file master del volume o nel "record MFT". Il punto di analisi a livelli cloud viene sempre archiviato su disco e occupa esattamente un cluster. La suddivisione in livelli di file di questo tipo può finire senza risparmiare spazio. Casi estremi possono anche finire usando più spazio con la suddivisione in livelli nel cloud abilitata. Per salvaguardarsi da tale dimensione, le dimensioni minime di un file a livello di suddivisione in livelli nel cloud sono pari a 8 KB in una dimensione del cluster di 4 KB o inferiore. 

## <a name="selecting-your-initial-policies"></a>Selezione dei criteri iniziali

In genere, quando si Abilita la suddivisione in livelli nel cloud in un endpoint server, è necessario creare un'unità virtuale locale per ogni singolo endpoint server. L'isolamento dell'endpoint server rende più semplice comprendere il funzionamento della suddivisione in livelli nel cloud e modificare di conseguenza i criteri. Tuttavia, Sincronizzazione file di Azure funziona anche se sono presenti più endpoint server nella stessa unità. per informazioni dettagliate, vedere la sezione [più endpoint server in un volume locale](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) . Quando si Abilita per la prima volta la suddivisione in livelli nel cloud, è consigliabile lasciare i criteri per la data disabilitati e lo spazio disponibile sul volume in circa il 10% al 20%. Per la maggior parte dei volumi file server, lo spazio disponibile nel volume del 20% è in genere l'opzione migliore.

Per semplicità e per avere una chiara comprensione del modo in cui gli elementi verranno suddivisi in livelli, è consigliabile modificare principalmente i criteri di spazio disponibile nel volume e tenere disabilitati i criteri di data, a meno che non sia necessario. Questa operazione è consigliata perché la maggior parte dei clienti è utile per riempire la cache locale con il maggior numero possibile di file ad accesso frequente e per ottenere un livello superiore per il cloud. Tuttavia, i criteri relativi alla data possono essere utili se si vuole liberare in modo proattivo lo spazio su disco locale e si conoscono i file in tale endpoint server a cui si accede dopo il numero di giorni specificato nei criteri di data non devono essere conservati localmente. L'impostazione dei criteri di data consente di liberare la capacità del disco locale utile per altri endpoint nello stesso volume per memorizzare nella cache più file.

Dopo aver impostato i criteri, monitorare in uscita e modificare di conseguenza entrambi i criteri. Si consiglia di esaminare in modo specifico le dimensioni di richiamo della suddivisione in **livelli nel cloud** e le **dimensioni di richiamo del cloud in base alle metriche dell'applicazione** in monitoraggio di Azure. Per informazioni su come monitorare l'uscita, vedere monitorare la suddivisione in [livelli nel cloud](storage-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>Modifica dei criteri

Se la quantità di file richiamati costantemente da Azure è più grande del necessario, è possibile che siano presenti più file di accesso frequente rispetto a quanto spazio per salvarli nel volume del server locale. Aumentare le dimensioni del volume locale se possibile e/o ridurre la percentuale di criteri di spazio disponibile nel volume in piccoli incrementi. La riduzione della percentuale di spazio disponibile nel volume può avere anche conseguenze negative. Una varianza più elevata nel set di dati richiede una maggiore quantità di spazio disponibile per i nuovi file e il richiamo di file "freddi". La suddivisione in livelli inizia con un ritardo fino a un'ora e quindi richiede tempi di elaborazione, motivo per cui è necessario disporre sempre di spazio libero nel volume.

Il mantenimento di un numero maggiore di dati locali comporta costi in uscita inferiori, in quanto un minor numero di file verrà richiamato da Azure, ma richiede anche una quantità maggiore di spazio di archiviazione locale che comporta costi propri. 

Quando si modificano i criteri di spazio libero del volume, la quantità di dati che si deve tenere locale è determinata dai fattori seguenti: la larghezza di banda, il modello di accesso del set di dati e il budget. Con una connessione a larghezza di banda ridotta, è possibile che si desiderino più dati locali per garantire un ritardo minimo per gli utenti. Altrimenti è possibile basarsi sulla percentuale di varianza durante un determinato periodo. Ad esempio, se si è certi che il 10% del set di dati da 1 TB viene modificato o a cui si accede attivamente ogni mese, è possibile che si vogliano 100 GB locali in modo che non si richiamino spesso i file. Se il volume è 2 TB, è necessario mantenere il 5% (o 100 GB) locale, ovvero il 95% rimanente è la percentuale di spazio disponibile nel volume. Tuttavia, è necessario aggiungere un buffer per i periodi di varianza più elevata, in altre parole, a partire da una percentuale di spazio libero del volume maggiore e quindi modificarla, se necessario in un secondo momento.

## <a name="standard-operating-procedures"></a>Procedure operative standard

- Quando si esegue la prima migrazione a File di Azure tramite Sincronizzazione file di Azure, la suddivisione in livelli cloud dipende dal caricamento iniziale
- La suddivisione in livelli nel cloud controlla la conformità con lo spazio disponibile nel volume e i criteri di data ogni 60 minuti
- L'uso dell'opzione/LFSM in Robocopy durante la migrazione dei file consentirà la sincronizzazione dei file e la suddivisione in livelli nel cloud per creare spazio durante il caricamento iniziale 
- Se si verifica la suddivisione in livelli prima del formato di un mappa termica, i file verranno suddivisi in livelli per ultimo timestamp modificato

## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
