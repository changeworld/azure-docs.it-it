---
title: "Esercitazione: Introduzione ad Azure Synapse Analytics - Monitorare l'area di lavoro Synapse"
description: In questa esercitazione viene illustrato come monitorare le attività nell'area di lavoro Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 7e8525dbebb42e1f387ee8f0c192efd5e64c9453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426041"
---
# <a name="monitor-your-synapse-workspace"></a>Monitorare l'area di lavoro Synapse

In questa esercitazione viene illustrato come monitorare le attività nell'area di lavoro Synapse. È possibile monitorare le attività correnti e cronologiche per SQL, Apache Spark e pipeline. 

## <a name="introduction-to-the-monitor-hub"></a>Introduzione all'hub Monitoraggio

Aprire Synapse Studio e passare all'hub **Monitoraggio**. Qui è possibile visualizzare una cronologia di tutte le attività che si verificano nell'area di lavoro e quelle attualmente attive. 

* In **integrazione** è possibile monitorare pipeline, trigger e runtime di integrazione.
* In **attività** è possibile monitorare le attività di Spark e SQL. 

## <a name="integration"></a>Integrazione

1. Passare a **Integration > esecuzione della pipeline**. Questa vista mostra quante volte una pipeline è stata eseguita nell'area di lavoro. 
1. Trovare la pipeline eseguita nel passaggio precedente e fare clic su **Nome della pipeline** per visualizzare i dettagli.
1. Fare clic su **Barra di navigazione** nella parte superiore di Synapse Studio e quindi fare clic su **Tutte le esecuzioni della pipeline** per tornare alla vista precedente.

## <a name="apache-spark-activities"></a>Attività di Apache Spark

1. Passare a **attività > Apache Spark applicazioni**. È ora possibile vedere tutte le applicazioni Spark che sono in esecuzione o sono state eseguite nell'area di lavoro.
1. Trovare un'applicazione che non è più in esecuzione e fare clic sul relativo **Nome applicazione**. Verranno visualizzati i dettagli dell'applicazione Spark.
1. Se si ha familiarità con Apache Spark, è possibile accedere all'interfaccia utente standard del server cronologia Spark facendo clic su **Server cronologia Spark**.

## <a name="sql-activities"></a>Attività di SQL

1. Passare a **attività > richieste SQL**.
1. Verranno visualizzate le richieste SQL.
1. Selezionare un **pool** da monitorare dal filtro del **pool** . È ora possibile vedere tutte le richieste SQL che sono in esecuzione o sono state eseguite nell'area di lavoro in tale pool.
1. Trovare una richiesta SQL specifica e fare clic sul collegamento **altro** per visualizzare il testo completo della richiesta SQL.

    > [!NOTE] 
    > Le richieste SQL inviate tramite Synapse Studio in un pool SQL dedicato (in precedenza SQL Data Warehouse) abilitato per l'area di lavoro possono essere visualizzate nell'hub Monitoraggio. Per tutte le altre attività di monitoraggio, è possibile accedere alle funzionalità del pool SQL dedicato (in precedenza SQL Data Warehouse) nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare il Knowledge Center](get-started-knowledge-center.md)
