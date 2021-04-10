---
title: Caricare i file nell'archiviazione BLOB di Azure usando AzCopy V10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che consentono di caricare i file nell'archiviazione BLOB di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7cdc10720b6fa93cf1893d2040fd1c1f3e9e1f02
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728863"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>Caricare i file nell'archiviazione BLOB di Azure usando AzCopy V10

È possibile caricare file e directory nell'archivio BLOB usando l'utilità da riga di comando AzCopy V10. 

Per visualizzare esempi per altri tipi di attività, ad esempio il download di BLOB, la sincronizzazione con archiviazione BLOB o la copia di BLOB tra account, vedere i collegamenti presentati nella sezione [passaggi successivi](#next-steps) di questo articolo.

## <a name="get-started"></a>Introduzione

Vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE] 
> Gli esempi in questo articolo presuppongono che siano state fornite le credenziali di autorizzazione usando Azure Active Directory (Azure AD).
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy. Ad esempio: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Creare un contenitore

È possibile usare il comando [azcopy make](storage-ref-azcopy-make.md) per creare un contenitore.

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

| Sintassi/esempio | Codice |
|--------|-----------|
| **Sintassi** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Esempio** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Per informazioni dettagliate sulla documentazione di riferimento, vedere [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-a-file"></a>Caricare un file

Caricare un file usando il comando [copy di azcopy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Esempio** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

È anche possibile caricare un file usando un carattere jolly (*) in qualsiasi punto del percorso o del nome file. Ad esempio: `'C:\myDirectory\*.txt'` o `C:\my*\*.txt` .

## <a name="upload-a-directory"></a>Caricare una directory

Caricare una directory usando il comando [copy di azcopy](storage-ref-azcopy-copy.md) . 

Questo esempio copia una directory (e tutti i file in tale directory) in un contenitore BLOB. Il risultato è una directory nel contenitore con lo stesso nome.

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Per eseguire la copia in una directory all'interno del contenitore, è sufficiente specificare il nome della directory nella stringa di comando.

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Se si specifica il nome di una directory che non esiste nel contenitore, AzCopy crea una nuova directory con tale nome.

## <a name="upload-directory-contents"></a>Caricare il contenuto della directory

Caricare il contenuto di una directory usando il comando [copy di azcopy](storage-ref-azcopy-copy.md) . Usare il carattere jolly (*) per caricare il contenuto senza copiare la directory che lo contiene.

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Esempio** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


Aggiungere il `--recursive` flag per caricare i file in tutte le sottodirectory.

## <a name="upload-specific-files"></a>Carica file specifici

È possibile caricare file specifici usando nomi di file completi, nomi parziali con caratteri jolly (*) o con date e ore.

> [!TIP]
> Questi esempi racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

### <a name="specify-multiple-complete-file-names"></a>Specificare più nomi di file completi

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-path` opzione. Separare i singoli nomi di file usando un punto e virgola ( `;` ).

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

In questo esempio, AzCopy trasferisce la `C:\myDirectory\photos` Directory e il `C:\myDirectory\documents\myFile.txt` file. Includere l' `--recursive` opzione per trasferire tutti i file nella `C:\myDirectory\photos` Directory.

È anche possibile escludere i file utilizzando l' `--exclude-path` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

### <a name="use-wildcard-characters"></a>Usa caratteri jolly

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-pattern` opzione. Specificare i nomi parziali che includono i caratteri jolly. Separare i nomi con semicolin ( `;` ). 

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Esempio** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

È anche possibile escludere i file utilizzando l' `--exclude-pattern` opzione. Per altre informazioni, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

Le `--include-pattern` `--exclude-pattern` Opzioni e si applicano solo ai nomi file e non al percorso.  Se si desidera copiare tutti i file di testo presenti in un albero di directory, utilizzare l' `–recursive` opzione per ottenere l'intero albero di directory e quindi utilizzare `–include-pattern` e specificare `*.txt` per ottenere tutti i file di testo.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>Caricare i file che sono stati modificati prima o dopo una data e un'ora 

Usare il comando [azcopy Copy](storage-ref-azcopy-copy.md) con l' `--include-before` `--include-after` opzione o. Specificare una data e un'ora in formato ISO-8601 (ad esempio: `2020-08-19T15:04:00Z` ). 

Negli esempi seguenti vengono caricati i file che sono stati modificati dopo la data specificata.

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Esempio** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Per informazioni di riferimento dettagliate, vedere la documentazione di riferimento per la [copia di azcopy](storage-ref-azcopy-copy.md) .

## <a name="upload-with-index-tags"></a>Carica con tag di indice

È possibile caricare un file e aggiungere [tag di indice BLOB (anteprima)](../blobs/storage-manage-find-blobs.md) al BLOB di destinazione.  

Se si usa Azure AD autorizzazione, all'entità di sicurezza deve essere assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) oppure è necessario concedere l'autorizzazione per l' `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operazione del provider di risorse di Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) tramite un ruolo di Azure personalizzato. Se si usa un token di firma di accesso condiviso (SAS), il token deve fornire l'accesso ai tag del BLOB tramite l' `t` autorizzazione SAS.

Per aggiungere tag, usare l' `--blob-tags` opzione insieme a una coppia chiave-valore codificata in URL. Ad esempio, per aggiungere la chiave `my tag` e un valore `my tag value` , è necessario aggiungere `--blob-tags='my%20tag=my%20tag%20value'` al parametro Destination. 

Separare più tag di indice usando una e commerciale ( `&` ).  Se ad esempio si desidera aggiungere una chiave `my second tag` e un valore `my second tag value` , la stringa di opzione completa sarà `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Negli esempi seguenti viene illustrato come utilizzare l' `--blob-tags` opzione.

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Caricare un file** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Caricare una directory** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **Caricare il contenuto della directory** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> Se si specifica una directory per l'origine, tutti i BLOB copiati nella destinazione avranno gli stessi tag specificati nel comando.

## <a name="upload-with-optional-flags"></a>Carica con flag facoltativi

È possibile modificare l'operazione di caricamento usando i flag facoltativi. Ecco alcuni esempi.

|Scenario|Contrassegno|
|---|---|
|Caricare i file come BLOB di accodamento o BLOB di pagine.|**--BLOB-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Effettuare il caricamento in un livello di accesso specifico, ad esempio il livello archivio.|**--Block-BLOB-Tier** = \[ Nessun \| \| Archivio Hot Cool \|\]|

Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-copy.md#options).

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere questi articoli:

- [Esempi: Download](storage-use-azcopy-blobs-download.md)
- [Esempi: Copiare tra account](storage-use-azcopy-blobs-copy.md)
- [Esempi: Sincronizza](storage-use-azcopy-blobs-synchronize.md)
- [Esempi: Bucket di Amazon S3](storage-use-azcopy-s3.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)