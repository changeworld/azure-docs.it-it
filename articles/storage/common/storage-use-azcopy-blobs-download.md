---
title: Scaricare BLOB da Archiviazione BLOB di Azure usando AzCopy v10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che consentono di scaricare BLOB da Archiviazione BLOB di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 56bb36cfda9d0cf1a8882950c862a73ad1e77898
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502947"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy"></a>Scaricare BLOB dall'archiviazione BLOB di Azure usando AzCopy

È possibile scaricare BLOB e directory dall'archivio BLOB usando l'utilità della riga di comando AzCopy v10. 

Per esempi per altri tipi di attività, ad esempio il caricamento di file, la sincronizzazione con l'archiviazione BLOB o la copia di BLOB tra account, vedere i collegamenti presentati nella sezione [Passaggi](#next-steps) successivi di questo articolo.

## <a name="get-started"></a>Introduzione

Vedere [l'articolo Introduzione ad AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE] 
> Gli esempi in questo articolo presuppongono che siano stati forniti credenziali di autorizzazione usando Azure Active Directory (Azure AD).
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy. Ad esempio: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Scaricare un BLOB

Scaricare un BLOB usando il [comando azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

``azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'``

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Se il valore della proprietà di un BLOB contiene un hash, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà del BLOB corrisponda `Content-md5` `Content-md5` all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si eseezioni l'override di questo comportamento aggiungendo `--check-md5=NoCheck` o al comando di `--check-md5=LogOnly` copia.

## <a name="download-a-directory"></a>Scaricare una directory

Scaricare una directory usando il [comando azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive
```

In questo esempio viene restituita una directory `C:\myDirectory\myBlobDirectory` denominata che contiene tutti i BLOB scaricati.

## <a name="download-directory-contents"></a>Scaricare il contenuto della directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non hanno uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'
```

Aggiungere il `--recursive` flag per scaricare i file in tutte le sottodirectory.

## <a name="download-specific-blobs"></a>Scaricare BLOB specifici

È possibile scaricare BLOB specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o date e ore. 

> [!TIP]
> Questi esempi racchiudno gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

#### <a name="specify-multiple-complete-blob-names"></a>Specificare più nomi di BLOB completi

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-path` . Separare i singoli nomi di BLOB usando un semicolin ( `;` ).

**Sintassi**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive
```

In questo esempio AzCopy trasferisce `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` la directory e il `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` file. Includere `--recursive` l'opzione per trasferire tutti i BLOB nella `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` directory.

È anche possibile escludere i BLOB usando `--exclude-path` l'opzione . Per altre informazioni, vedere la documentazione di riferimento [di azcopy copy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Usare caratteri jolly

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-pattern` . Specificare nomi parziali che includono i caratteri jolly. Separare i nomi usando un punto e virgola ( `;` ).

**Sintassi**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

È anche possibile escludere i BLOB usando `--exclude-pattern` l'opzione . Per altre informazioni, vedere la documentazione di riferimento [di azcopy copy.](storage-ref-azcopy-copy.md)

Le `--include-pattern` opzioni e si applicano solo ai nomi dei BLOB e non al `--exclude-pattern` percorso.  Se si desidera copiare tutti i file di testo (BLOB) presenti in un albero di directory, usare l'opzione per ottenere l'intero albero di directory e quindi usare e specificare per ottenere tutti i file di `–recursive` `–include-pattern` `*.txt` testo.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Scaricare i BLOB modificati prima o dopo una data e un'ora 

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con `--include-before` l'opzione o `--include-after` . Specificare una data e un'ora in formato ISO-8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

Negli esempi seguenti vengono scaricati i file modificati il giorno o dopo la data specificata.

**Sintassi**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'
```

Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento [azcopy copy.](storage-ref-azcopy-copy.md)

#### <a name="download-previous-versions-of-a-blob"></a>Scaricare le versioni precedenti di un BLOB

Se è stato abilitato il [controllo delle versioni dei BLOB,](../blobs/versioning-enable.md)è possibile scaricare una o più versioni precedenti di un BLOB. 

Creare prima di tutto un file di testo contenente un elenco di [ID versione.](../blobs/versioning-overview.md) Ogni ID versione deve essere visualizzato in una riga separata. Ad esempio: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Usare quindi il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--list-of-versions` . Specificare il percorso del file di testo che contiene l'elenco di versioni (ad esempio: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Scaricare uno snapshot del BLOB

È possibile scaricare uno [snapshot BLOB](../blobs/snapshots-overview.md) facendo riferimento al **valore DateTime** di uno snapshot BLOB. 

**Sintassi**

`azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

> [!NOTE]
> Se si usa un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, aggiungere lo snapshot **DateTime** dopo il token di firma di accesso condiviso. Ad esempio: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Scaricare con flag facoltativi

È possibile modificare l'operazione di download usando flag facoltativi. Ecco alcuni esempi.

|Scenario|Contrassegno|
|---|---|
|Decomprimere automaticamente i file.|**--decompress**|
|Specificare la descrizione dettagliata delle voci di log correlate alla copia.|**--log-level** = \[ INFORMAZIONI \| \| SULL'ERRORE \| DI AVVISO NESSUNA\]|
|Specificare se e come sovrascrivere i file e i BLOB in conflitto nella destinazione.|**--overwrite** = \[ true \| false \| seSourceNewer \| prompt\]|

Per un elenco completo, vedere [opzioni](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in questi articoli:

- [Esempi: Caricamento](storage-use-azcopy-blobs-upload.md)
- [Esempi: Copia tra account](storage-use-azcopy-blobs-copy.md)
- [Esempi: Sincronizza](storage-use-azcopy-blobs-synchronize.md)
- [Esempi: Bucket di Amazon S3](storage-use-azcopy-s3.md)
- [Esempi: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Vedere questi articoli per configurare le impostazioni, ottimizzare le prestazioni e risolvere i problemi:

- [Impostazioni di configurazione di AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Ottimizzare le prestazioni di AzCopy](storage-use-azcopy-optimize.md)
- [Risolvere i problemi di AzCopy V10 in Archiviazione di Azure usando i file di log](storage-use-azcopy-configure.md)
