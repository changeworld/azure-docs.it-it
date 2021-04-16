---
title: Ottimizzare le prestazioni di AzCopy v10 con Archiviazione di Azure | Microsoft Docs
description: Questo articolo consente di ottimizzare le prestazioni di AzCopy v10 con Archiviazione di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b60fc4b1fc20c455c2c409f544a8af16f1dbf8d1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509134"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Ottimizzare le prestazioni di AzCopy con Archiviazione di Azure

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di ottimizzare le prestazioni.

> [!NOTE]
> Per informazioni su come iniziare a usare AzCopy, vedere Introduzione ad [AzCopy](storage-use-azcopy-v10.md)

È possibile eseguire il benchmark delle prestazioni e quindi usare comandi e variabili di ambiente per trovare un compromesso ottimale tra prestazioni e utilizzo delle risorse.

## <a name="run-benchmark-tests"></a>Eseguire test benchmark

È possibile eseguire un test benchmark delle prestazioni in contenitori BLOB o condivisioni file specifici per visualizzare statistiche generali sulle prestazioni e identificare colli di bottiglia delle prestazioni. È possibile eseguire il test caricando o scaricando i dati di test generati. 

Usare il comando seguente per eseguire un test di benchmark delle prestazioni.

**Sintassi**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Esempio**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

Questo comando esegue un benchmark delle prestazioni caricando i dati di test in una destinazione specificata. I dati di test vengono generati in memoria, caricati nella destinazione e quindi eliminati dalla destinazione al termine del test. È possibile specificare il numero di file da generare e le dimensioni desiderate usando parametri di comando facoltativi.

Se si preferisce eseguire questo test scaricando i dati, impostare il `mode` parametro su `download` . Per una documentazione di riferimento dettagliata, vedere [azcopy benchmark](storage-ref-azcopy-bench.md). 

## <a name="optimize-for-large-numbers-of-small-files"></a>Ottimizzare per un numero elevato di file di piccole dimensioni

La velocità effettiva può diminuire quando si trasferiscono file di piccole dimensioni, soprattutto quando si trasferiscono un numero elevato di file. Per ottimizzare le prestazioni, ridurre le dimensioni di ogni processo. Per le operazioni di download e caricamento, aumentare la concorrenza, ridurre l'attività del log e disattivare le funzionalità che comportano costi elevati per le prestazioni.

#### <a name="reduce-the-size-of-each-job"></a>Ridurre le dimensioni di ogni processo

Per ottenere prestazioni ottimali, assicurarsi che ogni processo trasferisca meno di 10 milioni di file. I processi che trasferiscono più di 50 milioni di file possono avere prestazioni scarse perché il meccanismo di rilevamento dei processi AzCopy comporta un sovraccarico significativo. Per ridurre il sovraccarico, è consigliabile suddividere i processi di grandi dimensioni in processi più piccoli. 

Un modo per ridurre le dimensioni di un processo è limitare il numero di file interessati da un processo. A tale scopo, è possibile usare i parametri di comando. Ad esempio, un processo può copiare solo un subset di directory usando il `include path` parametro come parte del comando [azcopy copy.](storage-ref-azcopy-copy.md) 

Usare il `include-pattern` parametro per copiare i file con un'estensione specifica , ad esempio `*.pdf` . In un processo separato usare il `exclude-pattern` parametro per copiare tutti i file che non hanno `*.pdf` estensione. Per [esempi, vedere Caricare file](storage-use-azcopy-blobs-upload.md#upload-specific-files) specifici e Scaricare [BLOB](storage-use-azcopy-blobs-download.md#download-specific-blobs) specifici.

Dopo aver deciso come dividere i processi di grandi dimensioni in processi più piccoli, è consigliabile eseguire i processi in più macchine virtuali.

#### <a name="increase-concurrency"></a>Aumentare la concorrenza

Se si caricano o si scaricano file, usare la variabile di ambiente per aumentare il numero di richieste simultanee che `AZCOPY_CONCURRENCY_VALUE` possono verificarsi nel computer. Impostare questa variabile il più alto possibile senza compromettere le prestazioni del computer. Per altre informazioni su questa variabile, vedere la sezione [Aumentare il](#increase-the-number-of-concurrent-requests) numero di richieste simultanee di questo articolo.

Se si copiano BLOB tra account di archiviazione, è consigliabile impostare il valore della variabile di ambiente `AZCOPY_CONCURRENCY_VALUE` su un valore maggiore di `1000` . È possibile impostare questa variabile su un valore elevato perché AzCopy usa API da server a server, quindi i dati vengono copiati direttamente tra i server di archiviazione e non usano la potenza di elaborazione del computer.  

#### <a name="decrease-the-number-of-logs-generated"></a>Ridurre il numero di log generati

È possibile migliorare le prestazioni riducendo il numero di voci di log create da AzCopy durante il completamento di un'operazione. Per impostazione predefinita, AzCopy registra tutte le attività correlate a un'operazione. Per ottenere prestazioni ottimali, è consigliabile impostare `log-level` il parametro del comando di copia, sincronizzazione o rimozione su `ERROR` . In questo modo, AzCopy registra solo gli errori. Per impostazione predefinita, il livello di log dei valori è impostato su `INFO` . 

#### <a name="turn-off-length-checking"></a>Disattivare il controllo della lunghezza 

Se si caricano o scaricano file, è consigliabile impostare i comandi di copia `--check-length` e sincronizzazione su `false` . Ciò impedisce ad AzCopy di verificare la lunghezza di un file dopo un trasferimento. Per impostazione predefinita, AzCopy controlla la lunghezza per assicurarsi che i file di origine e di destinazione corrispondano al termine di un trasferimento. AzCopy esegue questo controllo dopo ogni trasferimento di file. Questo controllo può ridurre le prestazioni quando i processi trasferiscono un numero elevato di file di piccole dimensioni. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Attivare l'analisi locale simultanea (Linux)

Le analisi dei file in alcuni sistemi Linux non vengono eseguite abbastanza velocemente da saturare tutte le connessioni di rete parallele. In questi casi, è possibile impostare su `AZCOPY_CONCURRENT_SCAN` `true` . 

## <a name="increase-the-number-of-concurrent-requests"></a>Aumentare il numero di richieste simultanee

È possibile aumentare la velocità effettiva impostando la `AZCOPY_CONCURRENCY_VALUE` variabile di ambiente . Questa variabile specifica il numero di richieste simultanee che possono verificarsi.  

Se il computer dispone di meno di 5 CPU, il valore di questa variabile viene impostato su `32` . Altrimenti, il valore predefinito è pari a 16 moltiplicato per il numero di CPU. Il valore predefinito massimo di questa variabile è , ma è possibile impostare manualmente questo valore su un valore `3000` superiore o inferiore. 

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Usare `azcopy env` per controllare il valore corrente di questa variabile. Se il valore è vuoto, è possibile leggere il valore usato esaminando l'inizio di qualsiasi file di log AzCopy. Il valore selezionato e il motivo per cui è stato selezionato vengono segnalati qui.

Prima di impostare questa variabile, è consigliabile eseguire un test benchmark. Il processo di test benchmark segnala il valore di concorrenza consigliato. In alternativa, se le condizioni di rete e i payload variano, impostare questa variabile sulla parola `AUTO` anziché su un numero specifico. In questo modo AzCopy eseguirà sempre lo stesso processo di ottimizzazione automatica utilizzato nei test benchmark.

## <a name="limit-the-throughput-data-rate"></a>Limitare la velocità effettiva dei dati

È possibile usare il `cap-mbps` flag nei comandi per posizionare un limite massimo alla velocità effettiva dei dati. Ad esempio, il comando seguente riprende un processo e imposta il limite di velocità effettiva a `10` megabit (Mb) al secondo. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Ottimizzare l'uso della memoria

Impostare la variabile di ambiente per specificare la quantità massima di memoria di sistema che AzCopy deve usare per il buffer durante il download e `AZCOPY_BUFFER_GB` il caricamento di file. Esprimere questo valore in gigabyte (GB).

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> Il rilevamento dei processi comporta sempre un sovraccarico aggiuntivo nell'utilizzo della memoria. L'importo varia in base al numero di trasferimenti in un processo. I buffer sono il componente più grande dell'utilizzo della memoria. È possibile controllare l'overhead usando per soddisfare approssimativamente i requisiti, ma non è disponibile alcun flag per ridurre rigorosamente `AZCOPY_BUFFER_GB` l'utilizzo complessivo della memoria.

## <a name="optimize-file-synchronization"></a>Ottimizzare la sincronizzazione dei file

Il [comando sync](storage-ref-azcopy-sync.md) identifica tutti i file nella destinazione e quindi confronta i nomi dei file e i timestamp dell'ultima modifica prima dell'avvio dell'operazione di sincronizzazione. Se si dispone di un numero elevato di file, è possibile migliorare le prestazioni eliminando questa elaborazione front-front. 

A tale scopo, usare invece [il comando azcopy copy](storage-ref-azcopy-copy.md) e impostare il flag su `--overwrite` `ifSourceNewer` . AzCopy confronta i file durante la copia senza eseguire analisi e confronti in anticipo. Ciò offre un vantaggio in termini di prestazioni nei casi in cui è presente un numero elevato di file da confrontare.

Il [comando azcopy copy](storage-ref-azcopy-copy.md) non elimina i file dalla destinazione, quindi se si desidera eliminare i file nella destinazione quando non esistono più nell'origine, usare il comando [azcopy sync](storage-ref-azcopy-sync.md) con il flag impostato su un valore o `--delete-destination` `true` `prompt` .

## <a name="see-also"></a>Vedi anche

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)