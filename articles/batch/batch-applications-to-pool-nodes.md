---
title: Copiare applicazioni e dati nei nodi del pool
description: Informazioni su come copiare applicazioni e dati nei nodi del pool.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 0109171fd78dc11058daa30bf4604bebc1eeb857
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703648"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Copiare applicazioni e dati nei nodi del pool

Azure Batch supporta diversi modi per ottenere i dati e le applicazioni nei nodi di calcolo in modo che siano disponibili per l'utilizzo da attività.

Il metodo scelto può dipendere dall'ambito del file o dell'applicazione. È possibile che i dati e le applicazioni siano necessari per eseguire l'intero processo ed è quindi necessario installarli in ogni nodo. Alcuni file o applicazioni possono essere necessari solo per un'attività specifica. È possibile che altri utenti debbano essere installati per il processo, ma non devono trovarsi in ogni nodo. Batch offre strumenti per ognuno di questi scenari.

## <a name="determine-the-scope-required-of-a-file"></a>Determinare l'ambito richiesto di un file

È necessario determinare l'ambito di un file, ovvero il file necessario per un pool, un processo o un'attività. I file che hanno come ambito il pool devono usare i pacchetti dell'applicazione del pool o un'attività iniziale. I file che hanno come ambito il processo devono usare un'attività di preparazione del processo. Un valido esempio di file con ambito a livello di pool o di processo sono le applicazioni. I file che hanno come ambito l'attività devono usare i file di risorse dell'attività.

## <a name="pool-start-task-resource-files"></a>File di risorse dell'attività di avvio del pool

Per le applicazioni o i dati che devono essere installati in ogni nodo del pool, usare i file di risorse dell'attività di avvio del pool. Utilizzare questo metodo insieme a un [pacchetto dell'applicazione](batch-application-packages.md) o alla raccolta di file di risorse dell'attività di avvio per eseguire un comando di installazione.  

Ad esempio, è possibile usare la riga di comando avvia attività per spostare o installare le applicazioni. È anche possibile specificare un elenco di file o contenitori in un account di archiviazione di Azure. Per altre informazioni, vedere [aggiungere # resourceFile nella documentazione Rest](/rest/api/batchservice/pool/add#resourcefile).

Se ogni processo in esecuzione nel pool esegue un'applicazione (con estensione exe) che deve prima essere installata con un file con estensione msi, è necessario impostare la proprietà **Attendi** operazione di avvio dell'attività di avvio su **true**. Per altre informazioni, vedere [aggiungere # StartTask nella documentazione Rest](/rest/api/batchservice/pool/add#starttask).

## <a name="application-package-references"></a>Riferimenti ai pacchetti dell'applicazione

Per le applicazioni o i dati che devono essere installati in ogni nodo del pool, provare a usare i [pacchetti dell'applicazione](batch-application-packages.md). Sebbene non sia presente alcun comando di installazione associato a un pacchetto dell'applicazione, è possibile usare un'attività iniziale per eseguire qualsiasi comando di installazione. Se l'applicazione non richiede l'installazione o è costituita da un numero elevato di file, è possibile usare questo metodo.

I pacchetti dell'applicazione sono utili quando si dispone di un numero elevato di file, perché possono combinare molti riferimenti a file in un payload ridotto. Se si tenta di includere più di 100 file di risorse separati in un'unica attività, il servizio Batch potrebbe riscontrare limitazioni di sistema interne per una singola attività. I pacchetti dell'applicazione sono utili anche quando si dispone di molte versioni diverse della stessa applicazione ed è necessario sceglierle tra loro.

## <a name="job-preparation-task-resource-files"></a>File di risorse dell'attività di preparazione dei processi

Per le applicazioni o i dati che devono essere installati per l'esecuzione del processo, ma non devono essere installati nell'intero pool, provare a usare [i file di risorse dell'attività di preparazione dei processi](./batch-job-prep-release.md).

Se, ad esempio, il pool presenta molti tipi diversi di processi e un solo tipo di processo richiede un file con estensione msi per poter essere eseguito, è opportuno inserire il passaggio di installazione in un'attività di preparazione del processo.

## <a name="task-resource-files"></a>File di risorse dell'attività

I file di risorse dell'attività sono appropriati quando l'applicazione o i dati sono rilevanti solo per una singola attività.

Ad esempio, si potrebbero avere cinque attività, ognuna delle quali elabora un file diverso e quindi scrivere l'output nell'archiviazione BLOB in questo caso, il file di input deve essere specificato nella raccolta di file di risorse dell'attività, perché ogni attività dispone di un proprio file di input.

## <a name="additional-ways-to-get-data-onto-nodes"></a>Altri modi per ottenere i dati nei nodi

Poiché si ha il controllo sui nodi Azure Batch e si possono eseguire file eseguibili personalizzati, è possibile eseguire il pull dei dati da un numero qualsiasi di origini personalizzate. Verificare che il nodo batch disponga della connettività alla destinazione e di disporre delle credenziali per tale origine nel nodo.

Di seguito sono riportati alcuni esempi di modalità di trasferimento dei dati nei nodi batch:

- Download dei dati da SQL
- Download dei dati da altri servizi Web/percorsi personalizzati
- Mapping di una condivisione di rete

## <a name="azure-storage"></a>Archiviazione di Azure

Tenere presente che l'archiviazione BLOB ha obiettivi di scalabilità per il download. Le soglie di scalabilità della condivisione file dell'archiviazione di Azure sono uguali a quelli di un singolo BLOB. La dimensione influirà sul numero di nodi e pool necessari.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare i [pacchetti dell'applicazione con batch](batch-application-packages.md).
- Altre informazioni sull' [uso di nodi e pool](nodes-and-pools.md).
