---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98225264"
---
Di seguito sono riportati i limiti relativi alle dimensioni dei dati copiati in un account di archiviazione. Assicurarsi che i dati caricati siano conformi a questi limiti. Per informazioni aggiornate su questi limiti, vedere [obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](../articles/storage/blobs/scalability-targets.md) e [obiettivi di scalabilità e prestazioni file di Azure](../articles/storage/files/storage-files-scale-targets.md).

| Dimensioni dei dati copiati nell'account di archiviazione di Azure                      | Limite predefinito          |
|---------------------------------------------------------------------|------------------------|
| BLOB in blocchi e BLOB di pagine                                            | Il limite massimo corrisponde al limite di [archiviazione definito per la sottoscrizione di Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) e include i dati provenienti da tutte le origini, tra cui data box.   |
| File di Azure                                                          | Data Box supporta le condivisioni file di grandi dimensioni (100 TiB) se abilitate prima della creazione dell'ordine di Data Box. <br> Se non è abilitato prima della creazione dell'ordine, la dimensione massima della condivisione file supportata è 5 TiB. <br> Data Box supporta le condivisioni file Premium di Azure che consentono un totale di 100 TiB per tutte le condivisioni nell'account di archiviazione. <br> La capacità massima utilizzabile è leggermente inferiore a causa dello spazio utilizzato per la copia dei log e dei log di controllo. Almeno 100 GiB è riservato per il log di copia e il registro di controllo. Per ulteriori informazioni, vedere [log di controllo per Azure Data Box, Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md). <br> Tutte le cartelle sotto *StorageAccount_AzureFiles* devono rispettare questo limite. <br> Per altre informazioni, vedere [abilitare e creare condivisioni file di grandi dimensioni](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
