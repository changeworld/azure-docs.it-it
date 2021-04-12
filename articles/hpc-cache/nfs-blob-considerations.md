---
title: Usare l'archiviazione BLOB NFS con la cache HPC di Azure
description: Descrive le procedure e le limitazioni quando si usa l'archiviazione BLOB ADLS-NFS con la cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d861a41d8cdeac548729ff341b3ffe27c0aa8152
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259980"
---
# <a name="use-nfs-mounted-blob-storage-preview-with-azure-hpc-cache"></a>Usare l'archiviazione BLOB montata da NFS (anteprima) con la cache HPC di Azure

A partire da marzo 2021 è possibile usare i contenitori BLOB montati tramite NFS con la cache HPC di Azure. Per altre informazioni, vedere il [supporto del protocollo NFS 3,0 nell'archivio BLOB di Azure](../storage/blobs/network-file-system-protocol-support.md) nel sito della documentazione di archiviazione BLOB.

> [!NOTE]
> Il supporto del protocollo NFS 3,0 nell'archivio BLOB di Azure è in anteprima e non deve essere usato in ambienti di produzione. Verificare la disponibilità di aggiornamenti e altri dettagli nella documentazione del supporto per il [protocollo NFS](../storage/blobs/network-file-system-protocol-support.md).

Cache HPC di Azure usa l'archiviazione BLOB abilitata per NFS nel tipo di destinazione di archiviazione ADLS-NFS. Queste destinazioni di archiviazione sono simili alle normali destinazioni di archiviazione NFS, ma presentano anche alcune sovrapposizioni con le normali destinazioni BLOB di Azure.

Questo articolo illustra le strategie e le limitazioni da comprendere quando si usano le destinazioni di archiviazione ADLS-NFS.

È inoltre consigliabile leggere la documentazione relativa ai BLOB NFS, in particolare le sezioni che descrivono scenari compatibili e non compatibili:

* [Panoramica delle funzionalità](../storage/blobs/network-file-system-protocol-support.md#applications-and-workloads-suited-for-this-feature)
* [Funzionalità non ancora supportate](../storage/blobs/network-file-system-protocol-support.md#azure-storage-features-not-yet-supported)
* [Considerazioni sulle prestazioni](../storage/blobs/network-file-system-protocol-support-performance.md)

## <a name="understand-consistency-requirements"></a>Informazioni sui requisiti di coerenza

La cache HPC richiede una coerenza assoluta per le destinazioni di archiviazione ADLS-NFS. Per impostazione predefinita, l'archiviazione BLOB abilitata per NFS non aggiorna rigorosamente i metadati del file, evitando in questo modo di confrontare accuratamente le versioni di file.

Per ovviare a questa differenza, cache HPC di Azure Disabilita automaticamente la memorizzazione nella cache degli attributi NFS in qualsiasi contenitore BLOB abilitato per NFS usato come destinazione di archiviazione.

Questa impostazione viene mantenute per la durata del contenitore, anche se viene rimossa dalla cache.

## <a name="preload-data-with-nfs-protocol"></a>Precaricamento dei dati con il protocollo NFS

In un contenitore BLOB abilitato per NFS, *un file può essere modificato solo dallo stesso protocollo usato al momento della creazione*. Ovvero, se si usa l'API REST di Azure per popolare un contenitore, non è possibile usare NFS per aggiornare tali file. Poiché la cache HPC di Azure usa solo NFS, non è possibile modificare i file creati con l'API REST di Azure.

Non si tratta di un problema per la cache se il contenitore è vuoto o se i file sono stati creati usando NFS.

Se i file nel contenitore sono stati creati con l'API REST di Azure BLOB anziché NFS, la cache HPC di Azure è limitata a queste azioni sui file originali:

* Elencare il file in una directory.
* Leggere il file e tenerlo nella cache per le letture successive.
* Eliminare il file.
* Svuotare il file (troncarlo a 0).
* Salvare una copia del file. La copia è contrassegnata come file creato da NFS e può essere modificata tramite NFS.

Cache HPC di Azure non è in **grado** di modificare il contenuto di un file creato con REST. Ciò significa che non è possibile salvare un file modificato da un client nella destinazione di archiviazione.

È importante comprendere questa limitazione, perché può causare problemi di integrità dei dati se si usano i modelli di utilizzo della memorizzazione nella cache in lettura/scrittura su file che non sono stati creati con NFS.

> [!TIP]
> Altre informazioni sulla memorizzazione nella cache di lettura e scrittura in informazioni sui [modelli di utilizzo della cache](cache-usage-models.md).

### <a name="write-caching-scenarios"></a>Scenari di scrittura nella cache

Questi modelli di utilizzo della cache includono la memorizzazione nella cache in scrittura:

* **Scritture superiori al 15%**
* **Più di 15% Scritture, verificando le modifiche al server di backup ogni 30 secondi**
* **Più di 15% Scritture, verificando le modifiche al server di backup ogni 60 secondi**
* **Scritture maggiori del 15%, writeback sul server ogni 30 secondi**

Usare questi modelli di utilizzo solo per modificare i file creati con NFS.

Se si tenta di usare la memorizzazione nella cache in scrittura per i file creati da REST, le modifiche al file potrebbero andare perse. Il motivo è che la cache non tenta immediatamente di salvare le modifiche ai file nel contenitore di archiviazione.

Di seguito viene illustrato come il tentativo di memorizzare nella cache le scritture nei file creati con REST inserisce i dati a rischio:

1. La cache accetta le modifiche dai client e restituisce un messaggio di operazione completata a ogni modifica.
1. La cache mantiene il file modificato nella relativa archiviazione e attende ulteriori modifiche.
1. Dopo che è trascorso del tempo, la cache tenta di salvare il file modificato nel contenitore back-end. A questo punto, riceverà un messaggio di errore perché sta provando a scrivere in un file creato in modo REST con NFS.

   È troppo tardi per indicare al computer client che le modifiche non sono state accettate e che la cache non ha alcun modo per aggiornare il file originale. Quindi, le modifiche dai client andranno perse.

### <a name="read-caching-scenarios"></a>Scenari di lettura della cache

Gli scenari di lettura della cache sono appropriati per i file creati con NFS o con l'API REST di BLOB di Azure.

Questi modelli di utilizzo usano solo la memorizzazione nella cache di lettura:

* **Lettura di scritture complesse e non frequenti**
* **I client scrivono nella destinazione NFS, ignorando la cache**
* **Lettura intensiva, controllo del server di backup ogni 3 ore**

È possibile usare questi modelli di utilizzo con i file creati dall'API REST o da NFS. Tutte le Scritture NFS inviate da un client al contenitore back-end avranno comunque esito negativo, ma avranno esito negativo immediatamente e restituiranno un messaggio di errore al client.

Un flusso di lavoro di lettura della cache può comunque coinvolgere le modifiche ai file, purché non vengano memorizzate nella cache. Ad esempio, i client potrebbero accedere ai file dal contenitore ma riscriverne le modifiche come nuovo file oppure salvare i file modificati in un percorso diverso.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Riconosci limitazioni di gestione blocchi di rete (NLM)

I contenitori BLOB abilitati per NFS non supportano Network Lock Manager (NLM), un protocollo NFS comunemente usato per proteggere i file dai conflitti.

Se il flusso di lavoro NFS è stato originariamente scritto per i sistemi di archiviazione hardware, le applicazioni client potrebbero includere le richieste di NLM. Per ovviare a questa limitazione quando si sposta il processo nell'archiviazione BLOB abilitata per NFS, assicurarsi che i client disabilitano NLM quando montano la cache.

Per disabilitare NLM, usare l'opzione ``-o nolock`` nel comando dei client ``mount`` . Questa opzione impedisce ai client di richiedere blocchi NLM e di ricevere errori in risposta.

In alcune situazioni, la cache HPC di Azure riconosce le richieste di NLM dal client. Il modello di utilizzo della cache denominato **Read Heavy, Scritture non frequenti** riconosce le richieste di NLM per conto dell'archiviazione back-end. Questo sistema impedisce errori sul client, ma non crea effettivamente un blocco sul contenitore ADLS-NFS.

Se si passa da un modello di utilizzo della destinazione di archiviazione ADLS-NFS a o da **Scritture pesanti e leggere raramente**, è necessario rimontare tutti i client usando l' ``nolock`` opzione.

Altre informazioni sui modelli NLM, cache HPC e utilizzo sono incluse in [comprendere i modelli di utilizzo della cache](cache-usage-models.md#know-when-to-remount-clients-for-nlm).

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Semplificare le scritture nei contenitori abilitati per NFS con la cache HPC

La cache HPC di Azure può contribuire a migliorare le prestazioni in un carico di lavoro che include la scrittura di modifiche in una destinazione di archiviazione ADLS-NFS.

> [!NOTE]
> È necessario usare NFS per popolare il contenitore di archiviazione ADLS-NFS se si vuole modificare i file tramite la cache HPC di Azure.

Una delle limitazioni descritte nell' [articolo Considerazioni sulle prestazioni](../storage/blobs/network-file-system-protocol-support-performance.md) BLOB abilitate per NFS è che l'archiviazione ADLS-NFS non è molto efficiente nella sovrascrittura dei file esistenti. Se si usa la cache HPC di Azure con l'archiviazione BLOB montata da NFS, la cache gestisce riscritture intermittenti mentre i client modificano un file attivo. La latenza di scrittura di un file nel contenitore back-end è nascosta ai client.

Tenere presente le limitazioni descritte in precedenza in [preload data con il protocollo NFS](#preload-data-with-nfs-protocol).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [prerequisiti di destinazione di archiviazione ADLS-NFS](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview)
* Creare un [account di archiviazione BLOB abilitato per NFS](../storage/blobs/network-file-system-protocol-support-how-to.md)
