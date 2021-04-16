---
title: Copiare dati da Amazon S3 Archiviazione di Azure usando AzCopy | Microsoft Docs
description: Usare AzCopy per copiare i dati da Amazon S3 a Archiviazione di Azure. AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: ac73d0e57377a8922691ea06c8de3df5ef577680
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502437"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Copiare dati da Amazon S3 a Archiviazione di Azure usando AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di copiare oggetti, directory e bucket da Amazon Web Services (AWS) S3 Archiviazione BLOB di Azure usando AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Scegliere il modo in cui fornire le credenziali di autorizzazione

* Per autorizzare con il Archiviazione di Azure, usare Azure Active Directory (AD) o un token di firma di accesso condiviso (SAS).

* Per autorizzare con AWS S3, usare una chiave di accesso AWS e una chiave di accesso privata.

### <a name="authorize-with-azure-storage"></a>Autorizzare con Archiviazione di Azure

Vedere [l'articolo Introduzione ad AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e scegliere come fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE]
> Gli esempi in questo articolo presuppongono che l'identità sia stata autenticata usando il `AzCopy login` comando . AzCopy usa quindi l'account Azure AD per autorizzare l'accesso ai dati nell'archivio BLOB.
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy.
>
> Ad esempio: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizzare con AWS S3

Raccogliere la chiave di accesso AWS e la chiave di accesso privata e quindi impostare queste variabili di ambiente:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **macOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Copiare oggetti, directory e bucket

AzCopy usa l'API [Put Block From URL,](/rest/api/storageservices/put-block-from-url) quindi i dati vengono copiati direttamente tra AWS S3 e i server di archiviazione. Queste operazioni di copia non usano la larghezza di banda di rete del computer.

> [!TIP]
> Gli esempi in questa sezione racchiudno gli argomenti di percorso tra virgolette singole (''). Usare virgolette singole in tutte le shell dei comandi ad eccezione della shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

 Questi esempi funzionano anche con gli account con uno spazio dei nomi gerarchico. [L'accesso multi-protocollo Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) consente di usare la stessa sintassi url ( `blob.core.windows.net` ) per tali account. 

### <a name="copy-an-object"></a>Copiare un oggetto

Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'`

**Esempio**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'
```

> [!NOTE]
> Gli esempi in questo articolo usano URL di tipo percorso per bucket AWS S3 (ad esempio: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> È anche possibile usare URL di tipo hosted virtuale (ad esempio: `http://bucket.s3.amazonaws.com` ). 
>
> Per altre informazioni sull'hosting virtuale di bucket, vedere [Hosting virtuale di bucket.](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)

### <a name="copy-a-directory"></a>Copiare una directory

Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

> [!NOTE]
> In questo esempio viene aggiunto il `--recursive` flag per copiare i file in tutte le sottodirectory.

### <a name="copy-the-contents-of-a-directory"></a>Copiare il contenuto di una directory

È possibile copiare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

**Sintassi**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true
```

### <a name="copy-a-bucket"></a>Copiare un bucket

Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true
```

### <a name="copy-all-buckets-in-all-regions"></a>Copiare tutti i bucket in tutte le aree

Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Copiare tutti i bucket in un'area S3 specifica

Usare la stessa sintassi dell'URL ( `blob.core.windows.net` ) per gli account con uno spazio dei nomi gerarchico.

**Sintassi**

`azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true`

**Esempio**

```azcopy
azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true
```

## <a name="handle-differences-in-object-naming-rules"></a>Gestire le differenze nelle regole di denominazione degli oggetti

AWS S3 ha un set diverso di convenzioni di denominazione per i nomi di bucket rispetto ai contenitori BLOB di Azure. Per altre informazioni, vedere [qui](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Se si sceglie di copiare un gruppo di bucket in un account di archiviazione di Azure, l'operazione di copia potrebbe non riuscire a causa di differenze di denominazione.

AzCopy gestisce due dei problemi più comuni che possono verificarsi; bucket contenenti periodi e bucket contenenti trattini consecutivi. I nomi dei bucket AWS S3 possono contenere punti e trattini consecutivi, ma non un contenitore in Azure. AzCopy sostituisce i punti con trattini e trattini consecutivi con un numero che rappresenta il numero di trattini consecutivi (ad esempio, un bucket denominato `my----bucket` diventa `my-4-bucket` . 

Inoltre, durante la copia di AzCopy sui file, verifica la presenza di conflitti di denominazione e tenta di risolverli. Ad esempio, se sono presenti bucket con il nome `bucket-name` e `bucket.name` , AzCopy risolve un bucket denominato prima `bucket.name` in e quindi in `bucket-name` `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Gestire le differenze nei metadati degli oggetti

AWS S3 e Azure consentono set diversi di caratteri nei nomi delle chiavi oggetto. Per informazioni sui caratteri utilizzati da AWS S3, vedere [qui](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Sul lato Azure, le chiavi oggetto BLOB rispettano le regole di denominazione per [gli identificatori C#.](/dotnet/csharp/language-reference/)

Come parte di un comando AzCopy, è possibile specificare un valore per il flag facoltativo che specifica come gestire i file in cui i metadati del file contengono nomi di chiave `copy` incompatibili. `s2s-handle-invalid-metadata` Nella tabella seguente viene descritto ogni valore di flag.

| Valore del flag | Descrizione  |
|--------|-----------|
| **ExcludeIfInvalid** | (opzione predefinita) I metadati non sono inclusi nell'oggetto trasferito. AzCopy registra un avviso. |
| **FailIfInvalid** | Gli oggetti non vengono copiati. AzCopy registra un errore e lo include nel conteggio degli errori visualizzato nel riepilogo del trasferimento.  |
| **RenameIfInvalid**  | AzCopy risolve la chiave di metadati non valida e copia l'oggetto in Azure usando la coppia di valori della chiave dei metadati risolta. Per informazioni sui passaggi esersi da AzCopy per rinominare le chiavi oggetto, vedere la sezione [Come AzCopy rinomina le](#rename-logic) chiavi oggetto di seguito. Se AzCopy non riesce a rinominare la chiave, l'oggetto non verrà copiato. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Come AzCopy rinomina le chiavi oggetto

AzCopy esegue questi passaggi:

1. Sostituisce i caratteri non validi con '_'.

2. Aggiunge la stringa `rename_` all'inizio di una nuova chiave valida.

   Questa chiave verrà usata per salvare il valore dei metadati **originale.**

3. Aggiunge la stringa `rename_key_` all'inizio di una nuova chiave valida.
   Questa chiave verrà usata per salvare la chiave non valida dei metadati **originali.**
   È possibile usare questa chiave per provare a recuperare i metadati sul lato Azure, poiché la chiave dei metadati viene mantenuta come valore nel servizio di archiviazione BLOB.

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in questi articoli:

- [Esempi: Caricamento](storage-use-azcopy-blobs-upload.md)
- [Esempi: Download](storage-use-azcopy-blobs-download.md)
- [Esempi: Copiare tra account](storage-use-azcopy-blobs-copy.md)
- [Esempi: Sincronizza](storage-use-azcopy-blobs-synchronize.md)
- [Esempi: Google Cloud Storage](storage-use-azcopy-google-cloud.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

Vedere questi articoli per configurare le impostazioni, ottimizzare le prestazioni e risolvere i problemi:

- [Impostazioni di configurazione di AzCopy](storage-ref-azcopy-configuration-settings.md)
- [Ottimizzare le prestazioni di AzCopy](storage-use-azcopy-optimize.md)
- [Risolvere i problemi di AzCopy V10 in Archiviazione di Azure usando i file di log](storage-use-azcopy-configure.md)
