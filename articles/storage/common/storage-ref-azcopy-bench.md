---
title: azcopy bench | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy bench.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1e49e787854069c2fcea30df7a43c3aacdd21b9e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502029"
---
# <a name="azcopy-benchmark"></a>azcopy benchmark

Esegue un benchmark delle prestazioni caricando o scaricando i dati di test in o da una destinazione specificata. Per i caricamenti, i dati di test vengono generati automaticamente.

Il comando benchmark esegue lo stesso processo di "copia", ad eccezione del seguente: 

  - Invece di richiedere parametri di origine e di destinazione, benchmark ne accetta solo uno. Si tratta del contenitore BLOB, File di Azure condivisione o Azure Data Lake Storage Gen2 file system da cui si vuole eseguire il caricamento o il download.

  - Il parametro 'mode' descrive se AzCopy deve testare i caricamenti in o i download da una determinata destinazione. I valori validi sono 'Upload' e 'Download'. Il valore predefinito è 'Upload'.

  - Per i benchmark di caricamento, il payload è descritto dai parametri della riga di comando, che controllano il numero di file che vengono rigenerati automaticamente e il livello di significativo dei file. Il processo di generazione avviene interamente in memoria. Il disco non viene usato.

  - Per i download, il payload è costituito dai file già presenti nell'origine. Vedere l'esempio seguente su come generare file di test, se necessario.
  
  - Sono supportati solo alcuni dei parametri facoltativi disponibili per il comando copy.
  
  - La diagnostica aggiuntiva viene misurata e segnalata.
  
  - Per i caricamenti, il comportamento predefinito è eliminare i dati trasferiti alla fine dell'esecuzione del test.  Per i download, i dati non vengono mai salvati in locale.

La modalità benchmark si ottimizza automaticamente al numero di connessioni TCP parallele che offre la velocità effettiva massima. Il numero verrà visualizzato alla fine. Per impedire l'aggiornamento automatico, impostare la AZCOPY_CONCURRENCY_VALUE di ambiente su un numero specifico di connessioni. 

Sono supportati tutti i tipi di autenticazione consueti. Tuttavia, l'approccio più pratico per il benchmarking del caricamento consiste in genere nel creare un contenitore vuoto con un token di firma di accesso condiviso e usare l'autenticazione di firma di accesso condiviso. La modalità di download richiede che nel contenitore di destinazione sia presente un set di dati di test.

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Ottimizzare le prestazioni di AzCopy v10 con Archiviazione di Azure](storage-use-azcopy-optimize.md)


## <a name="examples"></a>Esempio

```azcopy
azcopy benchmark [destination] [flags]
```

Eseguire un test benchmark con parametri predefiniti (idonei per il benchmarking delle reti fino a 1 Gbps):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Eseguire un test benchmark che carica 100 file, ogni 2 GiB di dimensioni: (adatto per il benchmarking in una rete veloce, ad esempio, 10 Gbps):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Eseguire un test benchmark, ma usare 50.000 file, ogni 8 MiB di dimensioni e calcolare gli hash MD5 (nello stesso modo in cui il flag esegue questa operazione nel comando `--put-md5` di copia). Lo scopo del benchmarking è verificare se il calcolo MD5 influisce sulla `--put-md5` velocità effettiva per il numero di file e le dimensioni selezionati:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Eseguire un test benchmark che scarica i file esistenti da una destinazione

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Eseguire un caricamento che non elimini i file trasferiti. Questi file possono quindi fungere da payload per un test di download.

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Opzioni

**--blob-type** string Definisce il tipo di BLOB nella destinazione. Usato per consentire il benchmarking di tipi di BLOB diversi. Identico al parametro con lo stesso nome nel comando copy (impostazione predefinita "Detect").

**--block-size-mb** float Usare questa dimensione del blocco (specificata in MiB). Il valore predefinito viene calcolato automaticamente in base alle dimensioni del file. Sono consentite frazioni decimali, ad esempio 0,25. Identico al parametro con lo stesso nome nel comando copy.

**--check-length**  Controllare la lunghezza di un file nella destinazione dopo il trasferimento. Se è presente una mancata corrispondenza tra l'origine e la destinazione, il trasferimento viene contrassegnato come non riuscito. (valore predefinito true)

**--delete-test-data**  Se true, i dati di benchmark verranno eliminati alla fine dell'esecuzione del benchmark.  Impostare su false se si desidera mantenere i dati nella destinazione, ad esempio per usarli per i test manuali al di fuori della modalità benchmark (impostazione predefinita true).

**--file-count** uint.  Numero di file di dati rigenerati automaticamente da usare (valore predefinito 100).

**--help**  Guida per bench

**--log-level** string Consente di definire il livello di dettaglio del log per il file di log, i livelli disponibili: INFO(tutte le richieste/risposte), WARNING(risposte lente), ERROR (solo richieste non riuscite) e NONE (nessun log di output). (impostazione predefinita "INFO")

**--mode** string definisce se Azcopy deve testare i caricamenti o i download da questa destinazione. I valori validi sono 'upload' e 'download'. L'opzione predefinita è 'upload'. (caricamento predefinito)

**--number-of-folders** uint Se è maggiore di 0, creare cartelle per dividere i dati.

**--put-md5**  Creare un hash MD5 di ogni file e salvarlo come proprietà Content-MD5 del BLOB/file di destinazione. Per impostazione predefinita, l'hash NON viene creato. Identico al parametro con lo stesso nome nel comando copy.

**--size-per-file** string Dimensione di ogni file di dati rigenerato automaticamente. Deve essere un numero immediatamente seguito da K, M o G. Ad esempio 12.000 o 200G (valore predefinito "250M").

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

**--cap-mbps float**  Consente di massimare la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non viene impostata.

**--output-type** string Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text". (valore predefinito "text").

**--trusted-microsoft-suffixes** string Specifica suffissi di dominio aggiuntivi in Azure Active Directory i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati vengono aggiunti al valore predefinito. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.


## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
