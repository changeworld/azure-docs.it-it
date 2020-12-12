---
title: Scaricare i BLOB dall'archiviazione BLOB di Azure usando AzCopy V10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che consentono di scaricare i BLOB dall'archiviazione BLOB di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ea8300447b9aa596e8678038982771263a4c76f6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358776"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>Scaricare i BLOB dall'archiviazione BLOB di Azure usando AzCopy V10

È possibile scaricare BLOB e directory dall'archiviazione BLOB usando l'utilità della riga di comando AzCopy V10. 

Per visualizzare esempi per altri tipi di attività, ad esempio il caricamento di file, la sincronizzazione con archiviazione BLOB o la copia di BLOB tra account, vedere i collegamenti presentati nella sezione [passaggi successivi](#next-steps) di questo articolo.

## <a name="get-started"></a>Introduzione

Vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE] 
> Gli esempi in questo articolo presuppongono che siano state fornite le credenziali di autorizzazione usando Azure Active Directory (Azure AD).
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy. Ad esempio: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="download-a-blob"></a>Scaricare un BLOB

Scaricare un BLOB usando il comando [azcopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Se il `Content-md5` valore della proprietà di un BLOB contiene un hash, AzCopy calcola un hash MD5 per i dati scaricati e verifica che l'hash MD5 archiviato nella proprietà del BLOB `Content-md5` corrisponda all'hash calcolato. Se questi valori non corrispondono, il download ha esito negativo a meno che non si esegua l'override di questo comportamento accodando `--check-md5=NoCheck` o `--check-md5=LogOnly` al comando copy.

## <a name="download-a-directory"></a>Scaricare una directory

Scaricare una directory usando il comando [azcopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Questo esempio genera una directory denominata `C:\myDirectory\myBlobDirectory` che contiene tutti i BLOB scaricati.

## <a name="download-directory-contents"></a>Scaricare il contenuto della directory

È possibile scaricare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

> [!NOTE]
> Attualmente, questo scenario è supportato solo per gli account che non dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

Aggiungere il `--recursive` flag per scaricare i file in tutte le sottodirectory.

## <a name="download-specific-blobs"></a>Scarica BLOB specifici

È possibile scaricare BLOB specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o con date e ore. 

> [!TIP]
> Questi esempi racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

#### <a name="specify-multiple-complete-blob-names"></a>Specificare più nomi di BLOB completi

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-path` opzione. Separare i singoli nomi di BLOB utilizzando semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

In questo esempio, AzCopy trasferisce la `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` Directory e il `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` file. Includere l' `--recursive` opzione per trasferire tutti i BLOB nella `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` Directory.

È anche possibile escludere i BLOB usando l' `--exclude-path` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

#### <a name="use-wildcard-characters"></a>Usa caratteri jolly

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-pattern` opzione. Specificare i nomi parziali che includono i caratteri jolly. Separare i nomi con semicolin ( `;` ).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

È anche possibile escludere i BLOB usando l' `--exclude-pattern` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

Le `--include-pattern` `--exclude-pattern` Opzioni e si applicano solo ai nomi di BLOB e non al percorso.  Se si desidera copiare tutti i file di testo (BLOB) presenti in un albero di directory, utilizzare l' `–recursive` opzione per ottenere l'intero albero di directory e quindi utilizzare `–include-pattern` e specificare `*.txt` per ottenere tutti i file di testo.

#### <a name="download-blobs-that-were-modified-before-or-after-a-date-and-time"></a>Scaricare i BLOB modificati prima o dopo una data e un'ora 

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-before` `--include-after` opzione o. Specificare una data e un'ora in formato ISO-8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

Gli esempi seguenti scaricano i file che sono stati modificati dopo la data specificata.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

#### <a name="download-previous-versions-of-a-blob"></a>Scaricare le versioni precedenti di un BLOB

Se è stato abilitato il [controllo delle versioni dei BLOB](../blobs/versioning-enable.md), è possibile scaricare una o più versioni precedenti di un BLOB. 

Per prima cosa, creare un file di testo contenente un elenco di [ID versione](../blobs/versioning-overview.md). Ogni ID versione deve essere visualizzato in una riga separata. Ad esempio: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Usare quindi il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--list-of-versions` opzione. Specificare il percorso del file di testo che contiene l'elenco di versioni (ad esempio: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Scaricare uno snapshot del BLOB

È possibile scaricare uno [snapshot BLOB](/azure/storage/blobs/snapshots-overview.md) facendo riferimento al valore **DateTime** di uno snapshot BLOB. 

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **Esempio** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> Se si usa un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, aggiungere il valore **DateTime** snapshot dopo il token SAS. Ad esempio: `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`.

## <a name="download-with-optional-flags"></a>Scarica con flag facoltativi

È possibile modificare l'operazione di download usando i flag facoltativi. Ecco alcuni esempi.

|Scenario|Flag|
|---|---|
|Decomprime automaticamente i file.|**--Decomprimi**|
|Specificare il modo in cui si desidera che le voci di log relative alla copia siano disponibili.|**--livello** = \[ di log informazioni sull'errore di avviso \| \| \| nessuno\]|
|Specificare se e come sovrascrivere i BLOB e i file in conflitto nella destinazione.|**--Sovrascrivi** = \[ \| \| prompt ifSourceNewer true \| false\]|

Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere questi articoli:

- [Esempi: Caricamento](storage-use-azcopy-blobs-upload.md)
- [Esempi: copia tra account](storage-use-azcopy-blobs-copy.md)
- [Esempi: Sincronizza](storage-use-azcopy-blobs-synchronize.md)
- [Esempi: Bucket di Amazon S3](storage-use-azcopy-s3.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)