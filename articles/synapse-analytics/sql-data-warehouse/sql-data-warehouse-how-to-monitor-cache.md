---
title: Ottimizzare la cache Gen2
description: Altre informazioni sul monitoraggio della cache Gen2 usando il portale di Azure.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9de795c54f55295fa69ed7fcb5dd894e2963385b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566631"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Come monitorare la cache adattiva

Questo articolo descrive come monitorare e risolvere i problemi di prestazioni delle query lente determinando se il carico di lavoro sta sfruttando in modo ottimale la cache adattiva per i pool SQL dedicati.

L'architettura di archiviazione del pool SQL dedicata crea automaticamente livelli per i segmenti columnstore sottoposti a query più di frequente in una cache che risiede in unità SSD basate su NVMe. Si avranno prestazioni migliori quando le query recuperano i segmenti che risiedono nella cache.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Risolvere i problemi usando il portale di Azure

È possibile usare le Monitoraggio di Azure per visualizzare le metriche della cache per risolvere i problemi relativi alle prestazioni delle query. Passare prima di tutto al portale di Azure e fare clic su **Monitoraggio**, **Metriche** **e + Seleziona un ambito:**

![Screenshot che mostra l'opzione Selezionare un ambito selezionato in Metriche nella portale di Azure.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Usare le barre di ricerca e a discesa per individuare il pool SQL dedicato. Selezionare quindi Applica.

![Screenshot che mostra il riquadro Selezionare un ambito in cui è possibile selezionare il data warehouse.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Le metriche chiave per la risoluzione dei problemi della cache sono **Percentuale riscontri cache** e **Percentuale cache usata**. Selezionare **Percentuale riscontri** cache e quindi usare **il pulsante Aggiungi metrica** per aggiungere **la percentuale usata dalla cache.** 

![Metrica della cache](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Riscontro nella cache e percentuale usata

La matrice seguente descrive scenari in base ai valori della metrica della cache:

|                                | **Elevata percentuale di riscontro nella cache** | **Bassa percentuale di riscontro nella cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Elevata percentuale usata della cache** |          Scenario 1           |          Scenario 2          |
| **Bassa percentuale usata della cache**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:**. Uso ottimale della cache. [Risolvere](sql-data-warehouse-manage-monitor.md) i problemi relativi ad altre aree che potrebbero rallentare le query.

**Scenario 2:**. Il set di dati di lavoro corrente non può entrare nella cache, ciò comporta una bassa percentuale di riscontro nella cache dovuta a letture fisiche. Si consiglia di aumentare il livello della prestazione ed eseguire nuovamente il carico di lavoro per popolare la cache.

**Scenario 3:** È probabile che la query venga eseguita lentamente per motivi non relativi alla cache. [Risolvere](sql-data-warehouse-manage-monitor.md) i problemi relativi ad altre aree che potrebbero rallentare le query. È inoltre possibile considerare di [ridurre l'istanza](sql-data-warehouse-manage-monitor.md) per ridurre le dimensioni della cache e i costi. 

**Scenario 4:** Si è verificato un cold cache che potrebbe essere il motivo della lentezza della query. Si consiglia di eseguire nuovamente la query poiché il set di dati di lavoro dovrebbe ora essere memorizzato nella cache. 

> [!IMPORTANT]
> Se la percentuale di riscontri nella cache o la percentuale usata dalla cache non viene aggiornata dopo la rieseguizione del carico di lavoro, il working set può già risiedere in memoria. Solo le tabelle columnstore cluster vengono memorizzate nella cache.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'ottimizzazione delle prestazioni delle query generali, vedere [Monitoraggio dell'esecuzione delle query](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
