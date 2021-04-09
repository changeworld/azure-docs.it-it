---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 624df1185f86004b48bcca921e76d55bfb14c48d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98900996"
---
Questa sezione descrive i limiti per il servizio di archiviazione di Azure e le convenzioni di denominazione necessarie per File di Azure, BLOB in blocchi di Azure e BLOB di pagine di Azure, come applicabile al servizio Azure Stack Edge/Data Box Gateway. Esaminare attentamente i limiti di archiviazione e seguire tutte le raccomandazioni.

Per informazioni aggiornate su limiti del servizio Archiviazione di Azure e le procedure consigliate per la denominazione di condivisioni, contenitori e file, vedere:

- [Naming and referencing containers](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nomi e riferimenti a contenitori)
- [Naming and referencing shares](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Assegnazione di nomi e riferimenti a condivisioni)
- [Convenzioni di BLOB in blocchi e BLOB di pagine](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se sono presenti file o directory che superano i limiti del servizio Archiviazione di Azure o che non sono conformi alle convenzioni di denominazione di BLOB o File di Azure, tali file o directory non vengono inseriti in Archiviazione di Azure tramite il servizio Azure Stack Edge o Data Box Gateway.