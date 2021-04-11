---
title: Sincronizzare con archiviazione BLOB di Azure tramite AzCopy V10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che consentono di eseguire la sincronizzazione con archiviazione BLOB di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ec341243811eaa271511baba04ea1c48a4fefdab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728896"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>Sincronizzare con archiviazione BLOB di Azure tramite AzCopy V10

È possibile sincronizzare l'archiviazione locale con archiviazione BLOB di Azure usando l'utilità da riga di comando AzCopy V10. 

È possibile sincronizzare il contenuto di un file system locale con un contenitore BLOB. È anche possibile sincronizzare i contenitori e le directory virtuali tra loro. La sincronizzazione è unidirezionale. In altre parole, è possibile scegliere quali di questi due endpoint sono l'origine e quella di destinazione. La sincronizzazione usa anche API da server a server. Gli esempi presentati in questa sezione funzionano anche con gli account che hanno uno spazio dei nomi gerarchico. 

> [!NOTE]
> La versione corrente di AzCopy non esegue la sincronizzazione tra altre origini e destinazioni, ad esempio file storage o Amazon Web Services (AWS) S3 bucket).

Per visualizzare esempi per altri tipi di attività, ad esempio il caricamento di file, il download di BLOB o la copia di BLOB tra account, vedere i collegamenti presentati nella sezione [passaggi successivi](#next-steps) di questo articolo.

## <a name="get-started"></a>Introduzione

Vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE] 
> Gli esempi in questo articolo presuppongono che siano state fornite le credenziali di autorizzazione usando Azure Active Directory (Azure AD).
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy. Ad esempio: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Indicazioni

- Il comando di [sincronizzazione](storage-ref-azcopy-sync.md) Confronta i nomi di file e i timestamp dell'Ultima modifica. Impostare il `--delete-destination` flag facoltativo sul valore `true` o `prompt` per eliminare i file nella directory di destinazione se tali file non sono più presenti nella directory di origine.

- Se si imposta il `--delete-destination` flag su `true` , AzCopy Elimina i file senza fornire un messaggio di richiesta. Se si desidera che venga visualizzata una richiesta prima che AzCopy elimini un file, impostare il `--delete-destination` flag su `prompt` .

- Per evitare eliminazioni accidentali, assicurarsi di abilitare la funzionalità di [eliminazione](../blobs/soft-delete-blob-overview.md) temporanea prima di usare il `--delete-destination=prompt|true` flag.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aggiornare un contenitore con modifiche a una file system locale

In questo caso, il contenitore è la destinazione e il file system locale è l'origine. 

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aggiornare un file system locale con le modifiche apportate a un contenitore

In questo caso, il file system locale è la destinazione e il contenitore è l'origine.

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Esempio** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>Aggiornare un contenitore con le modifiche in un altro contenitore

Il primo contenitore visualizzato in questo comando è l'origine. Il secondo è la destinazione.

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Aggiornare una directory con le modifiche apportate a una directory in un altro contenitore

La prima directory visualizzata in questo comando è l'origine. Il secondo è la destinazione.

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

| Sintassi/esempio  |  Codice |
|--------|-----------|
| **Sintassi** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Esempio** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>Sincronizza con flag facoltativi

È possibile modificare l'operazione di sincronizzazione usando i flag facoltativi. Ecco alcuni esempi.

|Scenario|Contrassegno|
|---|---|
|Consente di specificare il modo in cui devono essere convalidati gli hash MD5 durante il download.|**--Check-MD5** = \[ NOCHECK \| loginly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Escludere i file in base a un modello.|**--Exclude-Path**|
|Specificare il modo in cui si desidera che le voci di log correlate alla sincronizzazione siano disponibili.|**--livello** = \[ di log informazioni sull'errore di avviso \| \| \| nessuno\]|

Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-sync.md#options).

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere questi articoli:

- [Esempi: Caricamento](storage-use-azcopy-blobs-upload.md)
- [Esempi: Download](storage-use-azcopy-blobs-download.md)
- [Esempi: Copiare tra account](storage-use-azcopy-blobs-copy.md)
- [Esempi: Bucket di Amazon S3](storage-use-azcopy-s3.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)