---
title: Trasferire dati da o verso File di Azure usando AzCopy v10 | Microsoft Docs
description: Trasferire i dati con AzCopy e l'archiviazione file. AzCopy è uno strumento da riga di comando per la copia di BLOB o file da e verso un account di archiviazione. Usare AzCopy con File di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 04bf84c5dc8a63929900f1bc95d7274074ef9d5a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498357"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Trasferire dati con AzCopy e l'archivio file 

AzCopy è un'utilità della riga di comando che è possibile usare per copiare file da o verso un account di archiviazione. Questo articolo contiene comandi di esempio che funzionano con File di Azure.

Prima di iniziare, vedere [l'articolo Introduzione ad AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e acquisire familiarità con lo strumento.

> [!TIP]
> Gli esempi in questo articolo racchiudno gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

## <a name="create-file-shares"></a>Creare condivisioni file

È possibile usare il [comando azcopy make](storage-ref-azcopy-make.md) per creare una condivisione file. L'esempio in questa sezione crea una condivisione file denominata `myfileshare` .

**Sintassi**

`azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'`

**Esempio**

```azcopy
azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

Per una documentazione di riferimento dettagliata, vedere [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Caricare i file

È possibile usare il [comando azcopy copy](storage-ref-azcopy-copy.md) per caricare file e directory dal computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Caricare un file
> * Caricare una directory
> * Caricare il contenuto di una directory
> * Caricare un file specifico

> [!TIP]
> È possibile modificare l'operazione di caricamento usando flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Contrassegno|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--preserve-smb-info** = \[ true \| false\]|
> 
> Per un elenco completo, vedere [opzioni](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> AzCopy non calcola e archivia automaticamente il codice hash md5 del file. Per eseguire questa operazione, aggiungere il `--put-md5` flag a ogni comando di copia. In questo modo, quando il file viene scaricato, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà del file corrisponda `Content-md5` all'hash calcolato.

### <a name="upload-a-file"></a>Caricare un file

**Sintassi**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'`

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

È anche possibile caricare un file usando un simbolo jolly (*) in un punto qualsiasi del percorso o del nome file. Ad esempio: `'C:\myDirectory\*.txt'` , o `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Caricare una directory

Questo esempio copia una directory, e tutti i file in essa contenuti, in una condivisione file. Il risultato è una directory con lo stesso nome nella condivisione file.

**Sintassi**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Per copiare in una directory all'interno della condivisione file, è sufficiente specificare il nome di tale directory nella stringa di comando.

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive
```

Se si specifica il nome di una directory che non esiste nella condivisione file, AzCopy crea una nuova directory con tale nome.

### <a name="upload-the-contents-of-a-directory"></a>Caricare il contenuto di una directory

È possibile caricare il contenuto di una directory senza copiare la directory che lo contiene usando il simbolo jolly (*).

**Sintassi**

`azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>`

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"
```

> [!NOTE]
> Aggiungere il `--recursive` flag per caricare i file in tutte le sottodirectory.

### <a name="upload-specific-files"></a>Caricare file specifici

È possibile caricare file specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o usando date e ore.

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-path` . Separare i singoli nomi di file usando un punto e virgola ( `;` ).

**Sintassi**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>`

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'
```

In questo esempio AzCopy trasferisce `C:\myDirectory\photos` la directory e il `C:\myDirectory\documents\myFile.txt` file. È necessario includere `--recursive` l'opzione per trasferire tutti i file nella `C:\myDirectory\photos` directory.

È anche possibile escludere i file usando `--exclude-path` l'opzione . Per altre informazioni, vedere la documentazione di riferimento [di azcopy copy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Usare caratteri jolly

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-pattern` . Specificare nomi parziali che includono i caratteri jolly. Separare i nomi usando un punto e virgola ( `;` ).

**Sintassi**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'
```

È anche possibile escludere i file usando `--exclude-pattern` l'opzione . Per altre informazioni, vedere la documentazione di riferimento [di azcopy copy.](storage-ref-azcopy-copy.md)

Le `--include-pattern` opzioni e si applicano solo ai nomi file e non al `--exclude-pattern` percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, usare l'opzione per ottenere l'intero albero di directory e quindi usare e specificare per ottenere tutti i file `--recursive` `--include-pattern` di `*.txt` testo.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>Caricare i file modificati dopo una data e un'ora 

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-after` . Specificare una data e un'ora in formato ISO 8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

**Sintassi**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>'  --include-after <Date-Time-in-ISO-8601-format>`

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-after '2020-08-19T15:04:00Z'
```

Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento [azcopy copy.](storage-ref-azcopy-copy.md)

## <a name="download-files"></a>Scaricare i file

È possibile usare il [comando azcopy copy](storage-ref-azcopy-copy.md) per scaricare file, directory e condivisioni file nel computer locale.

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Scaricare un file
> * Scaricare una directory
> * Scaricare il contenuto di una directory
> * Scaricare file specifici

> [!TIP]
> È possibile modificare l'operazione di download usando flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Contrassegno|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--preserve-smb-info** = \[ true \| false\]|
> |Decomprimere automaticamente i file.|**--decompress**|
> 
> Per un elenco completo, vedere [le opzioni](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Se il valore della proprietà di un file contiene un hash, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà del file corrisponda `Content-md5` `Content-md5` all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si eserviti questo comportamento aggiungendo `--check-md5=NoCheck` o `--check-md5=LogOnly` al comando copy.

### <a name="download-a-file"></a>Scaricare un file

**Sintassi**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'
```

### <a name="download-a-directory"></a>Scaricare una directory

**Sintassi**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive
```

In questo esempio viene restituita una directory `C:\myDirectory\myFileShareDirectory` denominata che contiene tutti i file scaricati.

### <a name="download-the-contents-of-a-directory"></a>Scaricare il contenuto di una directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

**Sintassi**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'
```

> [!NOTE]
> Aggiungere il `--recursive` flag per scaricare i file in tutte le sottodirectory.

### <a name="download-specific-files"></a>Scaricare file specifici

È possibile scaricare file specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o date e ore.

#### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-path` . Separare i singoli nomi di file usando un punto e virgola ( `;` ).

**Sintassi**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive
```

In questo esempio AzCopy trasferisce `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` la directory e il `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` file. Includere `--recursive` l'opzione per trasferire tutti i file nella `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` directory.

È anche possibile escludere i file usando `--exclude-path` l'opzione . Per altre informazioni, vedere la documentazione di riferimento [di azcopy copy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Usare caratteri jolly

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-pattern` . Specificare nomi parziali che includono i caratteri jolly. Separare i nomi usando un punto e virgola ( `;` ).

**Sintassi**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'
```

È anche possibile escludere i file usando `--exclude-pattern` l'opzione . Per altre informazioni, vedere la documentazione di riferimento [di azcopy copy.](storage-ref-azcopy-copy.md)

Le `--include-pattern` opzioni e si applicano solo ai nomi file e non al `--exclude-pattern` percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, usare l'opzione per ottenere l'intero albero di directory e quindi usare e specificare per ottenere tutti i file `--recursive` `--include-pattern` di `*.txt` testo.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>Scaricare i file modificati dopo una data e un'ora 

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-after` . Specificare una data e un'ora in formato ISO-8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

**Sintassi**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>/*<SAS-token>' '<local-directory-path>'  --include-after <Date-Time-in-ISO-8601-format>`

**Esempio**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/*?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory' --include-after '2020-08-19T15:04:00Z'
```

Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento [azcopy copy.](storage-ref-azcopy-copy.md)

#### <a name="download-from-a-share-snapshot"></a>Scaricare da uno snapshot di condivisione

È possibile scaricare una versione specifica di un file o di una directory facendo riferimento al **valore DateTime** di uno snapshot di condivisione. Per altre informazioni sugli snapshot di condivisione, vedere [Panoramica degli snapshot di condivisione](../files/storage-snapshots-files.md)per File di Azure . 

**Sintassi**

`azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path-or-directory-name><SAS-token>&sharesnapshot=<DateTime-of-snapshot>' '<local-file-or-directory-path>'`

**Esempio (Scaricare un file)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'
```

**Esempio (Scaricare una directory)**

```azcopy
azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory'  --recursive
```

## <a name="copy-files-between-storage-accounts"></a>Copiare file tra account di archiviazione

È possibile usare AzCopy per copiare file in altri account di archiviazione. L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

AzCopy usa LE [API](/rest/api/storageservices/put-page-from-url) [da server a server,](/rest/api/storageservices/put-block-from-url) quindi i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non usano la larghezza di banda di rete del computer. È possibile aumentare la velocità effettiva di queste operazioni impostando il valore della variabile `AZCOPY_CONCURRENCY_VALUE` di ambiente. Per altre informazioni, vedere [Aumentare la concorrenza.](storage-use-azcopy-optimize.md#increase-concurrency)

È anche possibile copiare versioni specifiche di un file facendo riferimento al **valore DateTime** di uno snapshot di condivisione. Per altre informazioni sugli snapshot di condivisione, vedere [Panoramica degli snapshot di condivisione](../files/storage-snapshots-files.md)per File di Azure . 

Questa sezione contiene gli esempi seguenti:

> [!div class="checklist"]
> * Copiare un file in un altro account di archiviazione
> * Copiare una directory in un altro account di archiviazione
> * Copiare una condivisione file in un altro account di archiviazione
> * Copiare tutte le condivisioni file, le directory e i file in un altro account di archiviazione

> [!TIP]
> È possibile modificare l'operazione di copia usando flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Contrassegno|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--preserve-smb-info** = \[ true \| false\]|
> 
> Per un elenco completo, vedere [opzioni](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Copiare un file in un altro account di archiviazione

**Sintassi**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'`

**Esempio**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

**Esempio (snapshot di condivisione)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'
```

### <a name="copy-a-directory-to-another-storage-account"></a>Copiare una directory in un altro account di archiviazione

**Sintassi**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Esempio**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Esempio (snapshot di condivisione)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/myFileShare/myFileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-a-file-share-to-another-storage-account"></a>Copiare una condivisione file in un altro account di archiviazione

**Sintassi**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Esempio**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D
```

**Esempio (snapshot di condivisione)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copiare tutte le condivisioni file, le directory e i file in un altro account di archiviazione

**Sintassi**

`azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'`

**Esempio**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

**Esempio (snapshot di condivisione)**

```azcopy
azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

## <a name="synchronize-files"></a>Sincronizzare i file

È possibile sincronizzare il contenuto di una condivisione file con un'altra condivisione file. È anche possibile sincronizzare il contenuto di una directory in una condivisione file con il contenuto di una directory che si trova in un'altra condivisione file. La sincronizzazione è un modo. In altre parole, è possibile scegliere quale di questi due endpoint è l'origine e quale è la destinazione. La sincronizzazione usa anche le API da server a server.

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non dispongono di uno spazio dei nomi gerarchico. La versione corrente di AzCopy non esegue la sincronizzazione tra File di Azure archiviazione BLOB.

Il [comando sync](storage-ref-azcopy-sync.md) confronta i nomi dei file e i timestamp dell'ultima modifica. Impostare il flag facoltativo su un valore o per eliminare i file nella directory di destinazione se tali file non esistono `--delete-destination` più nella directory di `true` `prompt` origine.

Se si imposta il `--delete-destination` flag su `true` , AzCopy elimina i file senza fornire una richiesta. Se si vuole che venga visualizzata una richiesta prima che AzCopy elimini un file, impostare il `--delete-destination` flag su `prompt` .

> [!TIP]
> È possibile modificare l'operazione di sincronizzazione usando flag facoltativi. Ecco alcuni esempi.
>
> |Scenario|Contrassegno|
> |---|---|
> |Copiare gli elenchi di controllo di accesso (ACL) insieme ai file.|**--preserve-smb-permissions** = \[ true \| false\]|
> |Copiare le informazioni sulle proprietà SMB insieme ai file.|**--preserve-smb-info** = \[ true \| false\]|
> |Escludere i file in base a un criterio.|**--exclude-path**|
> |Specificare il livello di dettaglio delle voci di log correlate alla sincronizzazione.|**--log-level** = \[ INFORMAZIONI \| \| SULL'ERRORE \| DI AVVISO NESSUNA\]|
> 
> Per un elenco completo, vedere [opzioni](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Aggiornare una condivisione file con le modifiche apportate a un'altra condivisione file

La prima condivisione file visualizzata in questo comando è l'origine. Il secondo è la destinazione.

**Sintassi**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Esempio**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Aggiornare una directory con modifiche a una directory in un'altra condivisione file

La prima directory visualizzata in questo comando è l'origine. Il secondo è la destinazione.

**Sintassi**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive`

**Esempio**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
```

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Aggiornare una condivisione file in modo che corrisponda al contenuto di uno snapshot di condivisione

La prima condivisione file visualizzata in questo comando è l'origine. Alla fine dell'URI aggiungere la stringa `&sharesnapshot=` seguita dal **valore DateTime** dello snapshot.

**Sintassi**

`azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive`

**Esempio**

```azcopy
azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive
``` 

Per altre informazioni sugli snapshot di condivisione, vedere [Panoramica degli snapshot di condivisione per File di Azure](../files/storage-snapshots-files.md).

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati](storage-use-azcopy-v10.md#transfer-data)

Vedere questi articoli per configurare le impostazioni, ottimizzare le prestazioni e risolvere i problemi:

- [Impostazioni di configurazione di AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Ottimizzare le prestazioni di AzCopy](storage-use-azcopy-optimize.md)
- [Risolvere i problemi di AzCopy V10 in Archiviazione di Azure usando i file di log](storage-use-azcopy-configure.md)

