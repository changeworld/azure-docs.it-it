---
title: Eseguire la sincronizzazione con l'archiviazione BLOB di Azure usando AzCopy v10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che consentono di eseguire la sincronizzazione con l'archiviazione BLOB di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8b3340c00d856b13edefc7728d5baa327399a441
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502930"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy"></a>Eseguire la sincronizzazione con l'archiviazione BLOB di Azure con AzCopy

È possibile sincronizzare l'archiviazione locale con l'archiviazione BLOB di Azure usando l'utilità della riga di comando AzCopy v10. 

È possibile sincronizzare il contenuto di un file system locale con un contenitore BLOB. È anche possibile sincronizzare contenitori e directory virtuali tra loro. La sincronizzazione è un modo. In altre parole, è possibile scegliere quale di questi due endpoint è l'origine e quale è la destinazione. La sincronizzazione usa anche le API da server a server. Gli esempi presentati in questa sezione funzionano anche con gli account con uno spazio dei nomi gerarchico. 

> [!NOTE]
> La versione corrente di AzCopy non esegue la sincronizzazione tra altre origini e destinazioni(ad esempio, bucket S3 di archiviazione file o Amazon Web Services (AWS).

Per esempi per altri tipi di attività, ad esempio il caricamento di file, il download di BLOB o la copia di BLOB tra account, vedere i collegamenti presentati nella [sezione Passaggi](#next-steps) successivi di questo articolo.

## <a name="get-started"></a>Introduzione

Vedere [l'articolo Introduzione ad AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE] 
> Gli esempi in questo articolo presuppongono che siano stati forniti credenziali di autorizzazione usando Azure Active Directory (Azure AD).
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy. Ad esempio: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="guidelines"></a>Indicazioni

- Il [comando sync](storage-ref-azcopy-sync.md) confronta i nomi dei file e i timestamp dell'ultima modifica. Impostare il flag facoltativo su un valore o per eliminare i file nella directory di destinazione se tali file non esistono `--delete-destination` più nella directory di `true` `prompt` origine.

- Se si imposta il `--delete-destination` flag su `true` , AzCopy elimina i file senza fornire una richiesta. Se si vuole che venga visualizzata una richiesta prima che AzCopy elimini un file, impostare il `--delete-destination` flag su `prompt` .

- Se si prevede di impostare il flag su o , è consigliabile usare il comando copy anziché il comando `--delete-destination` sync e impostare il parametro su `prompt` `false` [](storage-ref-azcopy-copy.md) [](storage-ref-azcopy-sync.md) `--overwrite` `ifSourceNewer` . Il [comando copy](storage-ref-azcopy-copy.md) utilizza meno memoria e comporta costi di fatturazione inferiori perché un'operazione di copia non deve indicizzare l'origine o la destinazione prima di spostare i file. 

- Per evitare eliminazioni accidentali, assicurarsi di abilitare la [funzionalità di eliminazione](../blobs/soft-delete-blob-overview.md) automatica prima di usare il flag `--delete-destination=prompt|true` .

- Il computer in cui si esegue il comando sync deve avere un orologio di sistema accurato perché le ultime ore modificate sono fondamentali per determinare se un file deve essere trasferito. Se il sistema ha un'avalla dell'orologio significativa, evitare di modificare i file nella destinazione troppo vicino al momento in cui si prevede di eseguire un comando di sincronizzazione.

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>Aggiornare un contenitore con le modifiche a un file system

In questo caso, il contenitore è la destinazione e il file system locale è l'origine. 

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Esempio**

```azcopy
azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>Aggiornare un file system locale con le modifiche apportate a un contenitore

In questo caso, l'file system locale è la destinazione e il contenitore è l'origine.

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive`

**Esempio**

```azcopy
azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive
```

## <a name="update-a-container-with-changes-in-another-container"></a>Aggiornare un contenitore con modifiche in un altro contenitore

Il primo contenitore visualizzato in questo comando è l'origine. Il secondo è la destinazione.

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Esempio**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>Aggiornare una directory con modifiche a una directory in un altro contenitore

La prima directory visualizzata in questo comando è l'origine. Il secondo è la destinazione.

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive`

**Esempio**

```azcopy
azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive
```

## <a name="synchronize-with-optional-flags"></a>Sincronizzare con flag facoltativi

È possibile modificare l'operazione di sincronizzazione usando flag facoltativi. Ecco alcuni esempi.

|Scenario|Contrassegno|
|---|---|
|Specificare come convalidare rigorosamente gli hash MD5 durante il download.|**--check-md5** = \[ NoCheck \| LogOnly \| FailIfDifferent \| FailIfDifferentOrMissing\]|
|Escludere i file in base a un criterio.|**--exclude-path**|
|Specificare il livello di dettaglio delle voci di log correlate alla sincronizzazione.|**--log-level** = \[ INFORMAZIONI \| \| SULL'ERRORE \| DI AVVISO NESSUNA\]|

Per un elenco completo dei flag, vedere [opzioni](storage-ref-azcopy-sync.md#options).

> [!NOTE]
> Il `--recursive` flag è impostato su per impostazione `true` predefinita. I `--exclude-pattern` flag e si applicano solo ai nomi di file e non ad altre parti del percorso del `--include-pattern` file. 

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in questi articoli:

- [Esempi: Caricamento](storage-use-azcopy-blobs-upload.md)
- [Esempi: Download](storage-use-azcopy-blobs-download.md)
- [Esempi: Copiare tra account](storage-use-azcopy-blobs-copy.md)
- [Esempi: Bucket di Amazon S3](storage-use-azcopy-s3.md)
- [Esempi: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Vedere questi articoli per configurare le impostazioni, ottimizzare le prestazioni e risolvere i problemi:

- [Impostazioni di configurazione di AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Ottimizzare le prestazioni di AzCopy](storage-use-azcopy-optimize.md)
- [Risolvere i problemi di AzCopy V10 in Archiviazione di Azure usando i file di log](storage-use-azcopy-configure.md)

