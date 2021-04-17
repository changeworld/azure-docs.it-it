---
title: Caricare file nell'archiviazione BLOB di Azure usando AzCopy v10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che consentono di caricare file nell'archivio BLOB di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 352497f0f4d23250abe9f84121f358589664002b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502913"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy"></a>Caricare file nell'archiviazione BLOB di Azure con AzCopy

È possibile caricare file e directory nell'archivio BLOB usando l'utilità della riga di comando AzCopy v10. 

Per esempi per altri tipi di attività, ad esempio il download di BLOB, la sincronizzazione con l'archiviazione BLOB o la copia di BLOB tra account, vedere i collegamenti presentati nella sezione [Passaggi](#next-steps) successivi di questo articolo.

## <a name="get-started"></a>Introduzione

Vedere [l'articolo Introduzione ad AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE] 
> Gli esempi in questo articolo presuppongono che siano stati forniti credenziali di autorizzazione usando Azure Active Directory (Azure AD).
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy. Ad esempio: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Creare un contenitore

È possibile usare il [comando azcopy make](storage-ref-azcopy-make.md) per creare un contenitore.

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

Di seguito è riportato un esempio di tabella:

**Sintassi**

`azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'`

**Esempio**

```azcopy
https://mystorageaccount.blob.core.windows.net/mycontainer
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
https://mystorageaccount.dfs.core.windows.net/mycontainer
```

Per una documentazione di riferimento dettagliata, vedere [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Caricare un file

Caricare un file usando il [comando azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'`

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'
```

È anche possibile caricare un file usando un simbolo jolly (*) in un punto qualsiasi del percorso o del nome file. Ad esempio: `'C:\myDirectory\*.txt'` o `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Caricare una directory

Caricare una directory usando il [comando azcopy copy.](storage-ref-azcopy-copy.md) 

Questo esempio copia una directory (e tutti i file in tale directory) in un contenitore BLOB. Il risultato è una directory nel contenitore con lo stesso nome.

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive`

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive
```

Per copiare in una directory all'interno del contenitore, è sufficiente specificare il nome della directory nella stringa di comando.

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive
```

Se si specifica il nome di una directory che non esiste nel contenitore, AzCopy crea una nuova directory con tale nome.

## <a name="upload-directory-contents"></a>Caricare il contenuto della directory

Caricare il contenuto di una directory usando il [comando azcopy copy.](storage-ref-azcopy-copy.md) Usare il simbolo jolly (*) per caricare il contenuto senza copiare la directory che lo contiene.

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` 

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'
```

Aggiungere il `--recursive` flag per caricare i file in tutte le sottodirectory.

## <a name="upload-specific-files"></a>Caricare file specifici

È possibile caricare file specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o usando date e ore.

> [!TIP]
> Questi esempi racchiudno gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-path` . Separare i singoli nomi di file usando un punto e virgola ( `;` ).

**Sintassi** 

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` 

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive'
```

In questo esempio AzCopy trasferisce `C:\myDirectory\photos` la directory e il `C:\myDirectory\documents\myFile.txt` file. Includere `--recursive` l'opzione per trasferire tutti i file nella `C:\myDirectory\photos` directory.

È anche possibile escludere i file usando `--exclude-path` l'opzione . Per altre informazioni, vedere la documentazione di riferimento [di azcopy copy.](storage-ref-azcopy-copy.md)

### <a name="use-wildcard-characters"></a>Usare caratteri jolly

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con l'opzione `--include-pattern` . Specificare nomi parziali che includono i caratteri jolly. Separare i nomi usando un punto e virgola ( `;` ). 

**Sintassi**

`azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` 

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'
```

È anche possibile escludere i file usando `--exclude-pattern` l'opzione . Per altre informazioni, vedere la documentazione di riferimento [di azcopy copy.](storage-ref-azcopy-copy.md)

Le `--include-pattern` opzioni e si applicano solo ai nomi file e non al `--exclude-pattern` percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, usare l'opzione per ottenere l'intero albero di directory e quindi usare e specificare per ottenere tutti i file `–recursive` `–include-pattern` di `*.txt` testo.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Caricare i file modificati prima o dopo una data e un'ora 

Usare il [comando azcopy copy](storage-ref-azcopy-copy.md) con `--include-before` l'opzione o `--include-after` . Specificare una data e un'ora in formato ISO-8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

Negli esempi seguenti vengono caricati i file modificati il giorno o dopo la data specificata.

**Sintassi**

`azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` 

**Esempio**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'
```

**Esempio (spazio dei nomi gerarchico)**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'
```

Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento [azcopy copy.](storage-ref-azcopy-copy.md)

## <a name="upload-with-index-tags"></a>Caricare con tag di indice

È possibile caricare un file e aggiungere tag di [indice BLOB (anteprima)](../blobs/storage-manage-find-blobs.md) al BLOB di destinazione.  

Se si usa un'autorizzazione Azure AD, all'entità [](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) di sicurezza deve essere assegnato il ruolo Proprietario dati BLOB di archiviazione oppure deve essere concessa l'autorizzazione per l'operazione del provider di risorse di Azure tramite un ruolo `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) di Azure personalizzato. Se si usa un token di firma di accesso condiviso, tale token deve fornire l'accesso ai tag del BLOB tramite l'autorizzazione `t` di firma di accesso condiviso.

Per aggiungere tag, usare `--blob-tags` l'opzione insieme a una coppia chiave-valore codificata in URL. Ad esempio, per aggiungere la chiave `my tag` e un valore , è necessario aggiungere al parametro di `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` destinazione. 

Separare più tag di indice usando una e commerciale ( `&` ).  Ad esempio, se si desidera aggiungere una chiave `my second tag` e un valore , la stringa di opzione completa sarà `my second tag value` `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Negli esempi seguenti viene illustrato come usare `--blob-tags` l'opzione .

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Caricare un file**

```azcopy
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Caricare una directory**

```azcopy
azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

**Caricare il contenuto della directory**

```azcopy
azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'
```

> [!NOTE]
> Se si specifica una directory per l'origine, tutti i BLOB copiati nella destinazione avranno gli stessi tag specificati nel comando .

## <a name="upload-with-optional-flags"></a>Caricare con flag facoltativi

È possibile modificare l'operazione di caricamento usando flag facoltativi. Ecco alcuni esempi.

|Scenario|Contrassegno|
|---|---|
|Caricare i file come BLOB di accodamento o BLOB di pagine.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Effettuare il caricamento in un livello di accesso specifico, ad esempio il livello archivio.|**--block-blob-tier** = \[ None \| Hot \| Cool \| Archive\]|

Per un elenco completo, vedere [opzioni](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in questi articoli:

- [Esempi: Download](storage-use-azcopy-blobs-download.md)
- [Esempi: Copiare tra account](storage-use-azcopy-blobs-copy.md)
- [Esempi: Sincronizza](storage-use-azcopy-blobs-synchronize.md)
- [Esempi: Bucket di Amazon S3](storage-use-azcopy-s3.md)
- [Esempi: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Vedere questi articoli per configurare le impostazioni, ottimizzare le prestazioni e risolvere i problemi:

- [Impostazioni di configurazione di AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Ottimizzare le prestazioni di AzCopy](storage-use-azcopy-optimize.md)
- [Risolvere i problemi di AzCopy V10 in Archiviazione di Azure usando i file di log](storage-use-azcopy-configure.md)