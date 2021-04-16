---
title: Copiare BLOB tra account di archiviazione di Azure con AzCopy v10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che consentono di copiare BLOB tra account di archiviazione.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: bfdc91ac8f4ce618052cc78e76b27e8bdeabeb77
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502981"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy"></a>Copiare BLOB tra account di archiviazione di Azure usando AzCopy

È possibile copiare BLOB, directory e contenitori tra account di archiviazione usando l'utilità della riga di comando AzCopy v10. 

Per esempi per altri tipi di attività, ad esempio il caricamento di file, il download di BLOB e la sincronizzazione con l'archiviazione BLOB, vedere i collegamenti presentati nella [sezione Passaggi](#next-steps) successivi di questo articolo.

AzCopy usa [LE API da server a server,](/rest/api/storageservices/put-block-from-url) [](/rest/api/storageservices/put-page-from-url)quindi i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non usano la larghezza di banda di rete del computer.

Per scaricare AzCopy e informazioni sui modi in cui è possibile fornire le credenziali di autorizzazione al servizio di archiviazione, vedere [Introduzione ad AzCopy.](storage-use-azcopy-v10.md) 

## <a name="guidelines"></a>Indicazioni

Applicare le linee guida seguenti ai comandi AzCopy. 

- Il client deve avere accesso alla rete per gli account di archiviazione di origine e di destinazione. Per informazioni su come configurare le impostazioni di rete per ogni account di archiviazione, vedere Configurare Archiviazione di Azure [firewall e reti virtuali.](storage-network-security.md?toc=/azure/storage/blobs/toc.json)

- Aggiungere un token di firma di accesso condiviso a ogni URL di origine. 

  Se si forniscono credenziali di autorizzazione usando Azure Active Directory (Azure AD), è possibile omettere il token di firma di accesso condiviso solo dall'URL di destinazione. Assicurarsi di aver configurato i ruoli adeguati nell'account di destinazione. Vedere [Opzione 1: Usare Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  Gli esempi in questo articolo presuppongono che l'identità sia stata autenticata usando Azure AD quindi gli esempi omettono i token di firma di accesso condiviso dall'URL di destinazione.

-  Se si copia in un account di archiviazione BLOB in blocchi Premium, omettere il livello di accesso di un BLOB dall'operazione di copia impostando `s2s-preserve-access-tier` su `false` (ad esempio: `--s2s-preserve-access-tier=false` ). Gli account di archiviazione BLOB in blocchi Premium non supportano i livelli di accesso. 

- Se si copia in o da un account con uno spazio dei nomi gerarchico, usare `blob.core.windows.net` invece di `dfs.core.windows.net` nella sintassi dell'URL. [L'accesso multi-protocollo Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) consente di usare ed è l'unica sintassi supportata per gli scenari di copia `blob.core.windows.net` da account a account. 

- È possibile aumentare la velocità effettiva delle operazioni di copia impostando il valore della variabile `AZCOPY_CONCURRENCY_VALUE` di ambiente. Per altre informazioni, vedere [Aumentare la concorrenza.](storage-use-azcopy-optimize.md#increase-concurrency) 

- Se i BLOB di origine hanno tag di indice e si vogliono conservare tali tag, sarà necessario riapplicarli ai BLOB di destinazione. Per informazioni su come impostare i tag di indice, vedere la sezione Copiare [BLOB in](#copy-between-accounts-and-add-index-tags) un altro account di archiviazione con tag di indice di questo articolo.

## <a name="copy-a-blob"></a>Copiare un BLOB

Copiare un BLOB in un altro account di archiviazione usando il [comando azcopy copy.](storage-ref-azcopy-copy.md) 

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'`

**Esempio**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati. 

## <a name="copy-a-directory"></a>Copiare una directory

Copiare una directory in un altro account di archiviazione usando il [comando azcopy copy.](storage-ref-azcopy-copy.md) 

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Esempio**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

## <a name="copy-a-container"></a>Copiare un contenitore

Copiare un contenitore in un altro account di archiviazione usando il [comando azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive`

**Esempio**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive
```

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

## <a name="copy-containers-directories-and-blobs"></a>Copiare contenitori, directory e BLOB

Copiare tutti i contenitori, le directory e i BLOB in un altro account di archiviazione usando il [comando azcopy copy.](storage-ref-azcopy-copy.md)

> [!TIP]
> Questo esempio racchiude gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Sintassi**

`azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive`

**Esempio**

```azcopy
azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive
```

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Copiare BLOB e aggiungere tag di indice

Copiare i BLOB in un altro account di archiviazione e aggiungere tag [di indice BLOB (anteprima)](../blobs/storage-manage-find-blobs.md) al BLOB di destinazione.

Se si usa un'autorizzazione Azure AD, all'entità [](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) di sicurezza deve essere assegnato il ruolo Proprietario dati BLOB di archiviazione oppure deve essere concessa l'autorizzazione per l'operazione del provider di risorse di Azure tramite un ruolo `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) di Azure personalizzato. Se si usa un token di firma di accesso condiviso, tale token deve fornire l'accesso ai tag del BLOB tramite l'autorizzazione `t` di firma di accesso condiviso.

Per aggiungere tag, usare `--blob-tags` l'opzione insieme a una coppia chiave-valore codificata in URL. 

Ad esempio, per aggiungere la chiave `my tag` e un valore , è necessario aggiungere al parametro di `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` destinazione. 

Separare più tag di indice usando una e commerciale ( `&` ).  Ad esempio, se si desidera aggiungere una chiave `my second tag` e un valore , la stringa di opzione completa sarà `my second tag value` `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Negli esempi seguenti viene illustrato come usare `--blob-tags` l'opzione .

> [!TIP]
> Questi esempi racchiudno gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

**Esempio di BLOB**

```azcopy

`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Esempio di directory**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

 **Esempio di contenitore**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

**Esempio di account**

```azcopy
`azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`
```

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

> [!NOTE]
> Se si specifica una directory, un contenitore o un account per l'origine, tutti i BLOB copiati nella destinazione avranno gli stessi tag specificati nel comando .

## <a name="copy-with-optional-flags"></a>Copia con flag facoltativi

È possibile modificare l'operazione di copia usando flag facoltativi. Ecco alcuni esempi.

|Scenario|Contrassegno|
|---|---|
|Copiare i BLOB come BLOB in blocchi, di pagine o di accodamento.|**--blob-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Copiare in un livello di accesso specifico, ad esempio il livello archivio.|**--block-blob-tier** = \[ None Hot Cool Archive (Nessuno \| archivio \| ad hot \| cool)\]|
|Decomprimere automaticamente i file.|**--decompress** = \[ gzip \| deflate\]|

Per un elenco completo, vedere [opzioni](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in questi articoli:

- [Esempi: Caricamento](storage-use-azcopy-blobs-upload.md)
- [Esempi: Download](storage-use-azcopy-blobs-download.md)
- [Esempi: Sincronizza](storage-use-azcopy-blobs-synchronize.md)
- [Esempi: Bucket di Amazon S3](storage-use-azcopy-s3.md)
- [Esempi: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Vedere questi articoli per configurare le impostazioni, ottimizzare le prestazioni e risolvere i problemi:

- [Impostazioni di configurazione di AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Ottimizzare le prestazioni di AzCopy](storage-use-azcopy-optimize.md)
- [Risolvere i problemi di AzCopy V10 in Archiviazione di Azure usando i file di log](storage-use-azcopy-configure.md)