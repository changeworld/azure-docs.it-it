---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 112c30fdd242c20f11c43f42ba54e3717e074bbb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706037"
---
Di seguito è riportato un elenco degli account di archiviazione e dei tipi di archiviazione supportati per un dispositivo Data Box. Per un elenco completo di tutte le funzionalità per tutti i tipi di account di archiviazione, vedere [tipi di account di archiviazione](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Per gli ordini di importazione, la tabella seguente Mostra gli account di archiviazione supportati.

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine** _ |_ *File di Azure** |**Note**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | S | S | S |
| Utilizzo generico v1 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v1 Premium  |  | S| | |
| Utilizzo generico v2 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione filepremium di Azure |  |  | S |  |  
| Archiviazione BLOB Standard |S | | |Sono supportati BLOB sia ad accesso frequente che sporadico. |

\* *- I dati caricati nei BLOB di pagine devono essere pari a 512 byte allineati, ad esempio VHD.*

Per gli ordini di esportazione, la tabella seguente Mostra gli account di archiviazione supportati.

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine** _ |_ *File di Azure** |**Livelli di accesso supportati**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | S | S | S | |
| Utilizzo generico v1 Standard  | S | S | S | Frequente, ad accesso sporadico|
| Utilizzo generico v1 Premium  |  | S| | |
| Utilizzo generico v2 Standard  | S | S | S | Frequente, ad accesso sporadico|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione filepremium di Azure |  |  | S |  |
| Archiviazione BLOB Standard |S | | |Frequente, ad accesso sporadico |
| Archiviazione BLOB in blocchi Premium |S | | |Frequente, ad accesso sporadico |
| Archiviazione BLOB di pagine Premium | |S | | |

> [!IMPORTANT]
> - Per gli account per utilizzo generico, Data Box non supporta i tipi di archiviazione di Accodamento, tabelle e dischi per gli ordini di importazione. Per gli ordini di esportazione, Data Box non supporta i tipi di archiviazione Queue, Table, disk e Azure Data Lake gen 2 per gli account per utilizzo generico.
> - Data Box non supporta i BLOB di Accodamento per l'archiviazione BLOB e gli account di archiviazione BLOB in blocchi.
> - Il supporto del protocollo NFS (Network File System) 3,0 nell'archivio BLOB di Azure non è supportato con Data Box.
> - I dati caricati nei BLOB di pagine devono essere allineati a 512 byte, ad esempio VHD.
> - È possibile esportare un massimo di 80 TB.
> - La cronologia file e gli snapshot BLOB non vengono esportati.