---
title: SKU BareMetal per carichi di lavoro Oracle
description: Informazioni sugli SKU per i carichi di lavoro oracle BareMetal Infrastructure.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 2482f8ed682a982ee3c8c4907e21b4e6c6ebbb4f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559092"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>SKU BareMetal per carichi di lavoro Oracle

Questo articolo illustra gli SKU dell'infrastruttura BareMetal specializzati per i carichi di lavoro Oracle.

L'infrastruttura BareMetal per gli SKU Oracle va da due socket fino a quattro socket. È anche possibile scegliere tra diverse dimensioni di memoria e core CPU per soddisfare i requisiti del carico di lavoro. Ecco una tabella che riepiloga le funzionalità degli SKU disponibili.
 
| **Hardware certificato**  **Oracle** | **Modello** | **Memoria totale** | **Archiviazione** | **Disponibilità** |
| --- | --- | --- | --- | --- |
| YES | SAP HANA in Azure S32m- 2 x Intel® Xeon® processori I623416 core CPU e 32 thread CPU | 1,5 TB | --- | Disponibile |
| YES | SAP HANA in Azure S64m- 4 x Intel® Xeon® processori I623432 core CPU e 64 thread CPU | 3 TB | --- | Disponibile |
| YES | SAP HANA in Azure S96- 2 x Intel® Xeon® processori E7-8890 v448 core CPU e 96 thread CPU | 768 GB | 3 TB | Disponibile |
| YES | SAP HANA in Azure S224 - 4 x Intel® Xeon® processore Platinum 8276 112 core CPU e 224 thread CPU | 3 TB | 6,3 TB | Disponibile |
| YES | SAP HANA su Azure S224m– 4 x Intel® Xeon® processore Platinum 8276 112 core CPU e 224 thread CPU | 6 TB | 10,5 TB | Disponibile |

- Core CPU = somma dei core CPU non hyper-threaded (numero totale di processori fisici) dell'unità server. 
- Thread CPU = somma dei thread di calcolo forniti dai core CPU con hyperthread (il numero totale di processori logici) dell'unità server. Per impostazione predefinita, la maggior parte delle unità sono configurate per l'uso di Hyper-Threading Technology.
- I server sono dedicati ai clienti.
- Il cliente ha accesso alla radice (nessun hypervisor).
- I server non sono direttamente nelle VNET di Azure.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'archiviazione offerta dall'infrastruttura BareMetal per Oracle.

> [!div class="nextstepaction"]
> [Archiviazione in BareMetal per carichi di lavoro Oracle](oracle-baremetal-storage.md)
