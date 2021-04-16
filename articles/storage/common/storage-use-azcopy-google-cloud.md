---
title: Copiare da Google Cloud Storage Archiviazione di Azure con AzCopy | Microsoft Docs
description: Usare AzCopy per copiare i dati da Google Cloud Storage Archiviazione di Azure. AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 3b2ad11abb7d1a3e64deef1ca49d9f84f03e5879
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498340"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Copiare dati da Google Cloud Storage Archiviazione di Azure usando AzCopy (anteprima)

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di copiare oggetti, directory e bucket da Google Cloud Storage Archiviazione BLOB di Azure usando AzCopy.

> [!IMPORTANT]
> La copia dei dati da Google Cloud Storage Archiviazione di Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Scegliere il modo in cui fornire le credenziali di autorizzazione

* Per autorizzare con Archiviazione di Azure, usare Azure Active Directory (AD) o un token di firma di accesso condiviso.

* Per autorizzare con Google Cloud Storage, usare una chiave dell'account del servizio.

### <a name="authorize-with-azure-storage"></a>Autorizzare con Archiviazione di Azure

Vedere [l'articolo Introduzione ad AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE] 
> Gli esempi in questo articolo presuppongono che siano stati forniti credenziali di autorizzazione usando Azure Active Directory (Azure AD).
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy. Ad esempio: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autorizzare con Google Cloud Storage

Per autorizzare con Google Cloud Storage, si userà una chiave dell'account del servizio. Per informazioni su come creare una chiave dell'account del servizio, vedere [Creazione e gestione delle chiavi dell'account del servizio](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Dopo aver ottenuto una chiave del servizio, impostare la variabile di ambiente sul `GOOGLE_APPLICATION_CREDENTIALS` percorso assoluto del file di chiave dell'account del servizio:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Copiare oggetti, directory e bucket

AzCopy usa l'API [Put Block From URL,](/rest/api/storageservices/put-block-from-url) quindi i dati vengono copiati direttamente tra Google Cloud Storage e i server di archiviazione. Queste operazioni di copia non usano la larghezza di banda di rete del computer.

> [!TIP]
> Gli esempi in questa sezione racchiudno gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

 Questi esempi funzionano anche con account con uno spazio dei nomi gerarchico. [L'accesso multi-protocollo Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) consente di usare la stessa sintassi url ( `blob.core.windows.net` ) per tali account. 

### <a name="copy-an-object"></a>Copiare un oggetto

Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Esempio**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```


### <a name="copy-a-directory"></a>Copiare una directory

Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> In questo esempio viene aggiunto il `--recursive` flag per copiare i file in tutte le sottodirectory.

### <a name="copy-the-contents-of-a-directory"></a>Copiare il contenuto di una directory

È possibile copiare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

**Sintassi**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-cloud-storage-bucket"></a>Copiare un bucket di archiviazione cloud

Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Copiare tutti i bucket in un progetto Google Cloud 

Per prima cosa, impostare `GOOGLE_CLOUD_PROJECT` sull'ID progetto del progetto Google Cloud.

Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Copiare un subset di bucket in un progetto Google Cloud 

Per prima cosa, impostare `GOOGLE_CLOUD_PROJECT` sull'ID progetto del progetto Google Cloud.

Copiare un subset di bucket usando un carattere jolly (*) nel nome del bucket. Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-bucket-naming-rules"></a>Gestire le differenze nelle regole di denominazione dei bucket

Google Cloud Storage ha un set diverso di convenzioni di denominazione per i nomi di bucket rispetto ai contenitori BLOB di Azure. Per altre informazioni, vedere [qui](https://cloud.google.com/storage/docs/naming-buckets). Se si sceglie di copiare un gruppo di bucket in un account di archiviazione di Azure, l'operazione di copia potrebbe non riuscire a causa di differenze di denominazione.

AzCopy gestisce tre dei problemi più comuni che possono verificarsi; bucket contenenti punti, bucket contenenti trattini consecutivi e bucket contenenti caratteri di sottolineatura. I nomi dei bucket di Google Cloud Storage possono contenere punti e trattini consecutivi, ma un contenitore in Azure non può. AzCopy sostituisce i punti con trattini e trattini consecutivi con un numero che rappresenta il numero di trattini consecutivi (ad esempio, un bucket denominato `my----bucket` diventa `my-4-bucket` . Se il nome del bucket ha un carattere di sottolineatura ( ), AzCopy sostituisce il carattere di sottolineatura con un trattino `_` (ad esempio, un bucket denominato diventa `my_bucket` `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Gestire le differenze nelle regole di denominazione degli oggetti

Google Cloud Storage ha un set diverso di convenzioni di denominazione per i nomi degli oggetti rispetto ai BLOB di Azure. Per altre informazioni, vedere [qui](https://cloud.google.com/storage/docs/naming-objects).

Archiviazione di Azure non consente ai nomi di oggetto (o a qualsiasi segmento nel percorso della directory virtuale) di terminare con punti finali (ad esempio `my-bucket...` ). I punti finali vengono tagliati quando viene eseguita l'operazione di copia. 

## <a name="handle-differences-in-object-metadata"></a>Gestire le differenze nei metadati degli oggetti

Google Cloud Storage e Azure consentono set diversi di caratteri nei nomi delle chiavi oggetto. Per informazioni sui metadati in Google Cloud [Storage, vedere qui](https://cloud.google.com/storage/docs/metadata). Sul lato Azure, le chiavi oggetto BLOB rispettano le regole di denominazione per [gli identificatori C#.](/dotnet/csharp/language-reference/)

Come parte di un comando AzCopy, è possibile specificare un valore per il flag facoltativo che specifica come gestire i file in cui i metadati del file contengono nomi di chiave `copy` incompatibili. `s2s-handle-invalid-metadata` Nella tabella seguente viene descritto ogni valore di flag.

| Valore del flag | Descrizione  |
|--------|-----------|
| **ExcludeIfInvalid** | (opzione predefinita) I metadati non sono inclusi nell'oggetto trasferito. AzCopy registra un avviso. |
| **FailIfInvalid** | Gli oggetti non vengono copiati. AzCopy registra un errore e lo include nel conteggio degli errori visualizzato nel riepilogo del trasferimento.  |
| **RenameIfInvalid**  | AzCopy risolve la chiave di metadati non valida e copia l'oggetto in Azure usando la coppia chiave-valore dei metadati risolta. Per informazioni sui passaggi esemisi da AzCopy per rinominare le chiavi oggetto, vedere la sezione Come [AzCopy rinomina le](#rename-logic) chiavi oggetto più avanti. Se AzCopy non riesce a rinominare la chiave, l'oggetto non verrà copiato. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Come AzCopy rinomina le chiavi degli oggetti

AzCopy esegue questi passaggi:

1. Sostituisce i caratteri non validi con '_'.

2. Aggiunge la stringa `rename_` all'inizio di una nuova chiave valida.

   Questa chiave verrà usata per salvare il valore dei metadati **originale**.

3. Aggiunge la stringa `rename_key_` all'inizio di una nuova chiave valida.
   Questa chiave verrà usata per salvare la chiave non valida dei metadati **originali.**
   È possibile usare questa chiave per provare a recuperare i metadati sul lato Azure, perché la chiave dei metadati viene mantenuta come valore nel servizio di archiviazione BLOB.

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in questi articoli:

- [Esempi: Caricamento](storage-use-azcopy-blobs-upload.md)
- [Esempi: Download](storage-use-azcopy-blobs-download.md)
- [Esempi: Copiare tra account](storage-use-azcopy-blobs-copy.md)
- [Esempi: Sincronizza](storage-use-azcopy-blobs-synchronize.md)
- [Esempi: Bucket di Amazon S3](storage-use-azcopy-s3.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Vedere questi articoli per configurare le impostazioni, ottimizzare le prestazioni e risolvere i problemi:

- [Impostazioni di configurazione di AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Ottimizzare le prestazioni di AzCopy](storage-use-azcopy-optimize.md)
- [Risolvere i problemi di AzCopy V10 in Archiviazione di Azure usando i file di log](storage-use-azcopy-configure.md)