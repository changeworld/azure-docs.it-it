---
title: Scenari di cache HPC di Azure
description: Viene descritto come sapere se il processo di calcolo funziona correttamente con la cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259974"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>Il tuo lavoro è adatto alla cache HPC di Azure?

La cache HPC di Azure può velocizzare l'accesso ai dati per i processi di calcolo ad alte prestazioni in un'ampia gamma di discipline. Ma non è perfetto per tutti i tipi di flussi di lavoro. Questo articolo fornisce linee guida su come decidere se la cache HPC è una scelta ideale per le proprie esigenze.

L'articolo [introduttivo](hpc-cache-overview.md) fornisce anche una breve descrizione del momento in cui usare la cache HPC di Azure e alcuni esempi di casi d'uso.

Leggere anche [questo articolo](nfs-blob-considerations.md) su come usare in modo efficace l' [archiviazione BLOB montata da NFS](../storage/blobs/network-file-system-protocol-support.md), disponibile in anteprima.

## <a name="nfs-version-30-applications"></a>Applicazioni NFS versione 3,0

La cache HPC di Azure supporta solo client NFS 3,0.

## <a name="high-read-to-write-ratio"></a>Rapporto di lettura/scrittura elevato

I carichi di lavoro in cui i client di calcolo eseguono una maggiore lettura rispetto alla scrittura sono in genere candidati ottimali per una cache. Se, ad esempio, il rapporto lettura/scrittura è 80/20 o 70/30, la cache HPC di Azure può aiutare a usare i file richiesti di frequente dalla cache anziché recuperarli dalla risorsa di archiviazione remota.

Il recupero di un file e l'archiviazione nella cache per la prima volta presenta una piccola latenza aggiuntiva rispetto a una normale richiesta client direttamente alla risorsa di archiviazione, pertanto l'incremento dell'efficienza viene raggiunto la volta successiva in cui un client richiede lo stesso file. Questo vale soprattutto per i file di grandi dimensioni. Se ogni richiesta client è univoca, l'effetto della cache HPC è limitato. Ma maggiore è il file, migliore sarà il tempo necessario per le prestazioni nel tempo dopo il primo accesso.

## <a name="file-based-analytic-workload"></a>Carico di lavoro analitico basato su file

La cache HPC di Azure è ideale per una pipeline che usa dati basati su file e viene eseguita su un numero elevato di client di calcolo, soprattutto se i client di calcolo sono macchine virtuali di Azure. Può aiutare a correggere le prestazioni lente o incoerenti causate da tempi di accesso ai file lunghi.

## <a name="remote-data-access"></a>Accesso ai dati remoti

La cache HPC di Azure consente di ridurre la latenza se il carico di lavoro deve accedere a dati remoti che non possono essere spostati più vicino alle risorse di calcolo. Ad esempio, i record potrebbero essere all'estremità di un ambiente WAN, in un'area di Azure diversa o in un data center del cliente. Questa operazione è talvolta denominata "espansione di file".

## <a name="heavy-request-load"></a>Carico richieste pesanti

Se un numero elevato di client richiede dati dall'origine allo stesso tempo, la cache HPC di Azure può velocizzare l'accesso ai file. Ad esempio, se usato con un cluster di elaborazione ad alte prestazioni, la cache HPC di Azure offre scalabilità per un numero elevato di richieste simultanee attraverso la cache.

## <a name="compute-resources-are-located-in-azure"></a>Le risorse di calcolo si trovano in Azure

Le macchine virtuali di Azure rappresentano una soluzione scalabile e conveniente per carichi di lavoro di calcolo ad alte prestazioni. La cache HPC di Azure può aiutare a inserire le informazioni necessarie più vicino, soprattutto se i dati originali vengono archiviati in un sistema remoto.

Se un cliente vuole eseguire la pipeline corrente "così com'è" in macchine virtuali di Azure, la cache HPC di Azure può offrire una soluzione di archiviazione condivisa (o Caching) basata su POSIX per la scalabilità.

Con la cache HPC di Azure non è necessario riprogettare la pipeline di lavoro per eseguire chiamate native all'archivio BLOB di Azure. È possibile accedere ai dati nel sistema originale oppure usare la cache HPC per spostarli in un nuovo contenitore BLOB.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come pianificare e configurare una cache, vedere gli articoli [Panoramica](hpc-cache-overview.md) e [prerequisiti](hpc-cache-prerequisites.md)
* Leggere considerazioni sull'uso dell' [archiviazione BLOB abilitata per NFS](nfs-blob-considerations.md) (anteprima) con la cache HPC di Azure
