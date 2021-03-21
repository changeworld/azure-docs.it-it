---
title: Matrice di supporto per il backup BLOB di Azure
description: Fornisce un riepilogo delle impostazioni e delle limitazioni di supporto per il backup dei BLOB di Azure (in anteprima)
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: ade43350bbe3fa1bcf58f47e93b948db3a5b21bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745526"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Matrice di supporto per il backup BLOB di Azure (in anteprima)

Questo articolo riepiloga la disponibilità a livello di area, gli scenari supportati e le limitazioni del backup operativo dei BLOB.

## <a name="supported-regions"></a>Aree supportate

Il backup operativo per i BLOB è attualmente disponibile nelle aree seguenti: Australia centrale, Australia orientale, Brasile meridionale, Canada centrale, India centrale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Germania centro-occidentale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Europa settentrionale, Stati Uniti centro-meridionali, Asia orientale sud, Svizzera settentrionale, Emirati Arabi Uniti settentrionali, Regno Unito meridionale, Regno Unito occidentale , Stati Uniti occidentali, Stati Uniti occidentali 2

## <a name="limitations"></a>Limitazioni

Il backup operativo dei BLOB usa il ripristino temporizzato del BLOB, il controllo delle versioni dei BLOB, l'eliminazione temporanea per i BLOB, il feed delle modifiche per i BLOB e il blocco di eliminazione per fornire una soluzione di backup locale. Le limitazioni che si applicano a queste funzionalità si applicano anche al backup operativo.

**Scenari supportati:** Il backup operativo supporta solo i BLOB in blocchi negli account di archiviazione standard per utilizzo generico V2. Quindi gli account ADLS Gen2 non sono supportati. Inoltre, tutti i BLOB di pagine, i BLOB di Accodamento e i BLOB Premium nell'account di archiviazione non verranno ripristinati e verranno ripristinati solo i BLOB in blocchi.

**Altre limitazioni:**

- Se un contenitore è stato eliminato durante il periodo di memorizzazione, il contenitore non verrà ripristinato con l'operazione di ripristino temporizzato. Se si tenta di ripristinare un intervallo di BLOB che include BLOB in un contenitore eliminato, l'operazione di ripristino temporizzato avrà esito negativo. Per ulteriori informazioni sulla protezione dei contenitori dall'eliminazione, vedere [soft delete for Containers (anteprima)](https://docs.microsoft.com/azure/storage/blobs/soft-delete-container-overview).
- Se un BLOB è stato spostato tra i livelli ad accesso frequente e ad accesso sporadico nel periodo compreso tra il momento corrente e il punto di ripristino, il BLOB viene ripristinato al livello precedente. Il ripristino dei BLOB in blocchi nel livello archivio non è supportato. Se, ad esempio, un BLOB nel livello di accesso frequente è stato spostato nel livello di archiviazione due giorni fa e un'operazione di ripristino viene ripristinata a un punto di tre giorni fa, il BLOB non viene ripristinato nel livello critico. Per ripristinare un BLOB archiviato, spostarlo prima dal livello archivio. Per altre informazioni, vedere [reidratare i dati BLOB dal livello archivio](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration).
- Un blocco che è stato caricato tramite [Put Block](https://docs.microsoft.com/rest/api/storageservices/put-block) o [Put Block dall'URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url), ma non eseguito tramite [Put Block List](https://docs.microsoft.com/rest/api/storageservices/put-block-list), non fa parte di un BLOB e pertanto non viene ripristinato come parte di un'operazione di ripristino.
- Non è possibile ripristinare un BLOB con un lease attivo. Se un BLOB con un lease attivo è incluso nell'intervallo di BLOB da ripristinare, l'operazione di ripristino avrà esito negativo automaticamente. Interrompi tutti i lease attivi prima di avviare l'operazione di ripristino.
- Gli snapshot non vengono creati o eliminati come parte di un'operazione di ripristino. Solo il BLOB di base viene ripristinato allo stato precedente.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica del backup operativo per i BLOB di Azure (in anteprima)](blob-backup-overview.md)
