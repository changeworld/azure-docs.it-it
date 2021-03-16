---
title: Copiare dati da Google Cloud storage ad archiviazione di Azure usando AzCopy | Microsoft Docs
description: Usare AzCopy per copiare dati da Google Cloud storage ad archiviazione di Azure. AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c0f030683954ede013f769bf8584e6cf82bab69f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555669"
---
# <a name="copy-data-from-google-cloud-storage-to-azure-storage-by-using-azcopy-preview"></a>Copiare dati da Google Cloud storage ad archiviazione di Azure usando AzCopy (anteprima)

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di copiare oggetti, directory e bucket da Google Cloud storage nell'archivio BLOB di Azure usando AzCopy.

> [!IMPORTANT]
> La copia dei dati da Google Cloud storage ad archiviazione di Azure è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="choose-how-youll-provide-authorization-credentials"></a>Scegliere il modo in cui fornire le credenziali di autorizzazione

* Per autorizzare l'archiviazione di Azure, usare Azure Active Directory (AD) o un token di firma di accesso condiviso (SAS).

* Per autorizzare con Google Cloud Storage, usare una chiave dell'account del servizio.

### <a name="authorize-with-azure-storage"></a>Autorizzare con archiviazione di Azure

Vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e informazioni sui modi in cui è possibile fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE] 
> Gli esempi in questo articolo presuppongono che siano state fornite le credenziali di autorizzazione usando Azure Active Directory (Azure AD).
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy. Ad esempio: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

### <a name="authorize-with-google-cloud-storage"></a>Autorizza con Google Cloud Storage

Per autorizzare con Google Cloud Storage, si userà una chiave dell'account del servizio. Per informazioni su come creare una chiave dell'account del servizio, vedere [creazione e gestione delle chiavi dell'account del servizio](https://cloud.google.com/iam/docs/creating-managing-service-account-keys).

Una volta ottenuta una chiave di servizio, impostare la `GOOGLE_APPLICATION_CREDENTIALS` variabile di ambiente sul percorso assoluto del file di chiave dell'account del servizio:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **Linux** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |
| **macOS** | `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-service-account-key>` |

## <a name="copy-objects-directories-and-buckets"></a>Copiare oggetti, directory e bucket

AzCopy usa il [blocco put dall'API URL](/rest/api/storageservices/put-block-from-url) , quindi i dati vengono copiati direttamente tra i server di archiviazione e archiviazione cloud di Google. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer.

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

 Questi esempi funzionano anche con gli account che hanno uno spazio dei nomi gerarchico. L'accesso a più [protocolli su data Lake storage](../blobs/data-lake-storage-multi-protocol-access.md) consente di usare la stessa sintassi URL ( `blob.core.windows.net` ) per tali account. 

### <a name="copy-an-object"></a>Copia di un oggetto

Utilizzare la stessa sintassi URL ( `blob.core.windows.net` ) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Esempio** | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://storage.cloud.google.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |


### <a name="copy-a-directory"></a>Copiare una directory

Utilizzare la stessa sintassi URL ( `blob.core.windows.net` ) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Esempio** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

> [!NOTE]
> In questo esempio viene aggiunto il `--recursive` flag per copiare i file in tutte le sottodirectory.

### <a name="copy-the-contents-of-a-directory"></a>Copiare il contenuto di una directory

È possibile copiare il contenuto di una directory senza copiare la directory che lo contiene usando il carattere jolly (*).

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>/<directory-name>/*' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Esempio** | `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://storage.cloud.google.com/mybucket/mydirectory/*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-cloud-storage-bucket"></a>Copiare un bucket di archiviazione cloud

Utilizzare la stessa sintassi URL ( `blob.core.windows.net` ) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://storage.cloud.google.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Esempio** | `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://storage.cloud.google.com/mybucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-google-cloud-project"></a>Copia tutti i bucket in un progetto Google Cloud 

Impostare prima di tutto `GOOGLE_CLOUD_PROJECT` su ID progetto di Google Cloud Project.

Utilizzare la stessa sintassi URL ( `blob.core.windows.net` ) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://storage.cloud.google.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Esempio** | `azcopy copy 'https://storage.cloud.google.com/' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://storage.cloud.google.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-a-subset-of-buckets-in-a-google-cloud-project"></a>Copiare un subset di bucket in un progetto di Google Cloud 

Impostare prima di tutto `GOOGLE_CLOUD_PROJECT` su ID progetto di Google Cloud Project.

Copiare un subset di bucket usando un carattere jolly (*) nel nome del bucket. Utilizzare la stessa sintassi URL ( `blob.core.windows.net` ) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://storage.cloud.google.com/<bucket*name>' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Esempio** | `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://storage.cloud.google.com/my*bucket' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-bucket-naming-rules"></a>Gestire le differenze nelle regole di denominazione dei bucket

Google Cloud Storage dispone di un set diverso di convenzioni di denominazione per i nomi di bucket rispetto ai contenitori BLOB di Azure. È possibile leggere le informazioni [qui](https://cloud.google.com/storage/docs/naming-buckets). Se si sceglie di copiare un gruppo di bucket in un account di archiviazione di Azure, l'operazione di copia potrebbe non riuscire a causa di differenze di denominazione.

AzCopy gestisce tre dei problemi più comuni che possono verificarsi. Bucket contenenti punti, bucket che contengono trattini consecutivi e bucket che contengono caratteri di sottolineatura. I nomi dei bucket di archiviazione cloud Google possono contenere punti e trattini consecutivi, ma un contenitore in Azure non può. AzCopy sostituisce i punti con trattini e trattini consecutivi con un numero che rappresenta il numero di trattini consecutivi (ad esempio, un bucket denominato `my----bucket` diventa `my-4-bucket` . Se il nome del bucket ha un carattere di sottolineatura ( `_` ), AzCopy sostituisce il carattere di sottolineatura con un trattino (ad esempio, un bucket denominato `my_bucket` diventa `my-bucket` . 

## <a name="handle-differences-in-object-naming-rules"></a>Gestire le differenze nelle regole di denominazione degli oggetti

Google Cloud Storage dispone di un set diverso di convenzioni di denominazione per i nomi di oggetti rispetto ai BLOB di Azure. È possibile leggere le informazioni [qui](https://cloud.google.com/storage/docs/naming-objects).

Archiviazione di Azure non consente la fine dei nomi di oggetti (o di qualsiasi segmento nel percorso della directory virtuale) con punti finali (ad esempio `my-bucket...` ). I punti finali vengono rimossi quando viene eseguita l'operazione di copia. 

## <a name="handle-differences-in-object-metadata"></a>Gestire le differenze nei metadati degli oggetti

Google Cloud storage e Azure consentono diversi set di caratteri nei nomi delle chiavi degli oggetti. Per informazioni sui metadati in Google Cloud Storage, vedere [qui](https://cloud.google.com/storage/docs/metadata). Sul lato Azure, le chiavi degli oggetti BLOB rispettano le regole di denominazione per gli [identificatori C#](/dotnet/csharp/language-reference/).

Come parte di un `copy` comando AzCopy, è possibile specificare un valore facoltativo per il `s2s-handle-invalid-metadata` flag che specifica come si desidera gestire i file in cui i metadati del file contengono nomi di chiave incompatibili. Nella tabella seguente viene descritto ogni valore di flag.

| Valore del flag | Descrizione  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opzione predefinita) I metadati non sono inclusi nell'oggetto trasferito. AzCopy registra un avviso. |
| **FailIfInvalid** | Gli oggetti non vengono copiati. AzCopy registra un errore e include tale errore nel conteggio non riuscito visualizzato nel riepilogo del trasferimento.  |
| **RenameIfInvalid**  | AzCopy risolve la chiave di metadati non valida e copia l'oggetto in Azure usando la coppia chiave-valore dei metadati risolta. Per informazioni sui passaggi eseguiti da AzCopy per rinominare le chiavi degli oggetti, vedere la sezione [come AzCopy](#rename-logic) Rinomina le chiavi degli oggetti di seguito. Se AzCopy non è in grado di rinominare la chiave, l'oggetto non verrà copiato. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>Come AzCopy Rinomina le chiavi dell'oggetto

AzCopy esegue i passaggi seguenti:

1. Sostituisce i caratteri non validi con ' _'.

2. Aggiunge la stringa `rename_` all'inizio di una nuova chiave valida.

   Questa chiave verrà usata per salvare il **valore** dei metadati originali.

3. Aggiunge la stringa `rename_key_` all'inizio di una nuova chiave valida.
   Questa chiave verrà usata per salvare la **chiave** originale dei metadati non validi.
   È possibile usare questa chiave per provare a ripristinare i metadati nel lato Azure poiché la chiave dei metadati viene mantenuta come valore nel servizio di archiviazione BLOB.

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Trasferire i dati](storage-use-azcopy-v10.md#transfer-data)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)
