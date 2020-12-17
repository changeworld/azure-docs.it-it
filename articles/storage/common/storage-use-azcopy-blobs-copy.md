---
title: Copiare i BLOB tra account di archiviazione di Azure con AzCopy V10 | Microsoft Docs
description: Questo articolo contiene una raccolta di comandi di esempio AzCopy che consentono di copiare i BLOB tra gli account di archiviazione.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 524e3f70f9588cfae2c739722fc1a44e683f9a7f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617290"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>Copiare i BLOB tra gli account di archiviazione di Azure usando AzCopy V10

È possibile copiare i BLOB, le directory e i contenitori tra gli account di archiviazione usando l'utilità da riga di comando AzCopy V10. 

Per visualizzare esempi per altri tipi di attività, ad esempio il caricamento di file, il download di BLOB e la sincronizzazione con l'archiviazione BLOB, vedere i collegamenti presentati nella sezione [passaggi successivi](#next-steps) di questo articolo.

AzCopy usa le [API](/rest/api/storageservices/put-page-from-url) [da server a server](/rest/api/storageservices/put-block-from-url) , quindi i dati vengono copiati direttamente tra i server di archiviazione. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer.

Per scaricare AzCopy e ottenere informazioni sulle modalità in cui è possibile fornire le credenziali di autorizzazione al servizio di archiviazione, vedere [Introduzione a AzCopy](storage-use-azcopy-v10.md). 

## <a name="guidelines"></a>Linee guida

Applicare le linee guida seguenti ai comandi di AzCopy. 

- Il client deve avere accesso alla rete sia per gli account di archiviazione di origine che per quelli di destinazione. Per informazioni su come configurare le impostazioni di rete per ogni account di archiviazione, vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](storage-network-security.md?toc=/azure/storage/blobs/toc.json).

- Aggiungere un token di firma di accesso condiviso a ogni URL di origine. 

  Se si forniscono le credenziali di autorizzazione usando Azure Active Directory (Azure AD), è possibile omettere il token SAS solo dall'URL di destinazione. Assicurarsi di aver configurato i ruoli appropriati nell'account di destinazione. Vedere l' [opzione 1: usare Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  Gli esempi in questo articolo presuppongono che l'identità sia stata autenticata usando Azure AD in modo che gli esempi omettono i token di firma di accesso condiviso dall'URL di destinazione.

-  Se si esegue la copia in un account di archiviazione BLOB in blocchi Premium, omettere il livello di accesso di un BLOB dall'operazione di copia impostando `s2s-preserve-access-tier` su `false` (ad esempio: `--s2s-preserve-access-tier=false` ). Gli account di archiviazione BLOB in blocchi Premium non supportano i livelli di accesso. 

- Se si esegue la copia in o da un account che dispone di uno spazio dei nomi gerarchico, usare `blob.core.windows.net` anziché `dfs.core.windows.net` nella sintassi dell'URL. L'accesso a più [protocolli su data Lake storage](../blobs/data-lake-storage-multi-protocol-access.md) consente di usare `blob.core.windows.net` ed è l'unica sintassi supportata per gli scenari di copia dell'account. 

- È possibile aumentare la velocità effettiva delle operazioni di copia impostando il valore della `AZCOPY_CONCURRENCY_VALUE` variabile di ambiente. Per altre informazioni, vedere [ottimizzare la velocità effettiva](storage-use-azcopy-configure.md#optimize-throughput). 

- Se i BLOB di origine contengono tag di indice e si desidera conservare tali tag, sarà necessario riapplicarli ai BLOB di destinazione. Per informazioni su come impostare i tag di indice, vedere la sezione [copiare i BLOB in un altro account di archiviazione con tag di indice](#copy-between-accounts-and-add-index-tags) di questo articolo.

## <a name="copy-a-blob"></a>Copiare un BLOB

Copiare un BLOB in un altro account di archiviazione usando il comando [azcopy Copy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati. 

## <a name="copy-a-directory"></a>Copiare una directory

Copiare una directory in un altro account di archiviazione usando il comando [azcopy Copy](storage-ref-azcopy-copy.md) . 

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

## <a name="copy-a-container"></a>Copiare un contenitore

Copiare un contenitore in un altro account di archiviazione usando il comando [azcopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

## <a name="copy-containers-directories-and-blobs"></a>Copiare contenitori, directory e BLOB

Copiare tutti i contenitori, le directory e i BLOB in un altro account di archiviazione usando il comando [azcopy Copy](storage-ref-azcopy-copy.md) .

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Esempio** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Copiare i BLOB e aggiungere tag di indice

Copiare i BLOB in un altro account di archiviazione e aggiungere [tag di indice BLOB (anteprima)](../blobs/storage-manage-find-blobs.md) al BLOB di destinazione.

Se si usa Azure AD autorizzazione, all'entità di sicurezza deve essere assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) oppure è necessario concedere l'autorizzazione per l' `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operazione del provider di risorse di Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) tramite un ruolo di Azure personalizzato. Se si usa un token di firma di accesso condiviso (SAS), il token deve fornire l'accesso ai tag del BLOB tramite l' `t` autorizzazione SAS.

Per aggiungere tag, usare l' `--blob-tags` opzione insieme a una coppia chiave-valore codificata in URL. 

Ad esempio, per aggiungere la chiave `my tag` e un valore `my tag value` , è necessario aggiungere `--blob-tags='my%20tag=my%20tag%20value'` al parametro Destination. 

Separare più tag di indice usando una e commerciale ( `&` ).  Se ad esempio si desidera aggiungere una chiave `my second tag` e un valore `my second tag value` , la stringa di opzione completa sarà `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` .

Negli esempi seguenti viene illustrato come utilizzare l' `--blob-tags` opzione.

> [!TIP]
> Questi esempi racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Directory** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Contenitore** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Account** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

L'operazione di copia è sincrona, quindi se il comando restituisce il risultato ciò indica che tutti i file sono stati copiati.

> [!NOTE]
> Se si specifica una directory, un contenitore o un account per l'origine, tutti i BLOB copiati nella destinazione avranno gli stessi tag specificati nel comando.

## <a name="copy-with-optional-flags"></a>Copia con flag facoltativi

È possibile modificare l'operazione di copia usando i flag facoltativi. Ecco alcuni esempi.

|Scenario|Contrassegno|
|---|---|
|Copiare i BLOB come blocchi, pagine o BLOB di Accodamento.|**--BLOB-type** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
|Copiare in un livello di accesso specifico, ad esempio il livello archivio.|**--Block-BLOB-Tier** = \[ Nessun \| \| Archivio Hot Cool \|\]|
|Decomprime automaticamente i file.|**--Decomprimi** = \[ \|deflate gzip\]|

Per un elenco completo, vedere [Opzioni](storage-ref-azcopy-copy.md#options). 

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi, vedere questi articoli:

- [Esempi: Caricamento](storage-use-azcopy-blobs-upload.md)
- [Esempi: Download](storage-use-azcopy-blobs-download.md)
- [Esempi: Sincronizza](storage-use-azcopy-blobs-synchronize.md)
- [Esempi: Bucket di Amazon S3](storage-use-azcopy-s3.md)
- [Esempi: File di Azure](storage-use-azcopy-files.md)
- [Eseguire la migrazione di dati locali in una risorsa di archiviazione cloud tramite AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)