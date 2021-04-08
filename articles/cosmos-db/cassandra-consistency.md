---
title: Livelli di coerenza di Apache Cassandra e Azure Cosmos DB
description: I livelli di coerenza di Apache Cassandra e Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516826"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Livelli di coerenza di Apache Cassandra e Azure Cosmos DB API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Diversamente da Azure Cosmos DB, Apache Cassandra non fornisce in modo nativo garanzie di coerenza definite in modo nativo. Apache Cassandra fornisce invece un livello di coerenza di scrittura e un livello di coerenza di lettura per consentire i compromessi di disponibilità elevata, coerenza e latenza. Quando si usa API Cassandra di Azure Cosmos DB:

* Il livello di coerenza di scrittura di Apache Cassandra viene mappato al livello di coerenza predefinito configurato nell'account Azure Cosmos. La coerenza per un'operazione di scrittura (CL) non può essere modificata in base alle singole richieste.

* Azure Cosmos DB eseguirà il mapping dinamico del livello di coerenza di lettura specificato dal driver del client Cassandra a uno dei livelli di coerenza Azure Cosmos DB configurati in modo dinamico in una richiesta di lettura.

## <a name="multi-region-writes-vs-single-region-writes"></a>Scritture in più aree e scritture in una singola area

Per impostazione predefinita, il database Apache Cassandra è un sistema multimaster e non fornisce un'opzione predefinita per le Scritture in una singola area con replica in più aree per le letture. Tuttavia, Azure Cosmos DB offre la possibilità di disporre di una sola area o di configurazioni di scrittura in più [aree](how-to-multi-master.md) . Uno dei vantaggi offerti dalla possibilità di scegliere una singola area di scrittura in più aree è l'evitare scenari di conflitto tra aree e l'opzione di mantenere la coerenza assoluta tra più aree. 

Con le Scritture a singola area, è possibile mantenere la coerenza assoluta, mantenendo al tempo stesso un livello di disponibilità elevata tra aree con [failover automatico](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage). In questa configurazione è comunque possibile sfruttare la località dei dati per ridurre la latenza di lettura effettuando il downgrade alla coerenza finale per ogni richiesta. Oltre a queste funzionalità, la piattaforma Azure Cosmos DB offre anche la possibilità di abilitare la [ridondanza della zona](high-availability.md#availability-zone-support) quando si seleziona un'area. Pertanto, a differenza di Apache Cassandra nativo, Azure Cosmos DB consente di spostarsi nello [spettro di compromesso](consistency-levels.md#rto) del teorema del Cap con maggiore granularità.

## <a name="mapping-consistency-levels"></a>Mapping dei livelli di coerenza

La piattaforma Azure Cosmos DB fornisce un set di cinque impostazioni di coerenza orientate ai casi d'uso aziendale ben definite rispetto alla replica e ai compromessi definiti dal [teorema Cap](https://en.wikipedia.org/wiki/CAP_theorem) e dal [teorema PACLC](https://en.wikipedia.org/wiki/PACELC_theorem). Poiché questo approccio differisce significativamente da Apache Cassandra, è consigliabile esaminare e comprendere Azure Cosmos DB impostazioni di coerenza nella [documentazione](consistency-levels.md)oppure guardare questa breve guida [video](https://www.youtube.com/watch?v=t1--kZjrG-o) per comprendere le impostazioni di coerenza nella piattaforma Azure Cosmos DB.

La tabella seguente illustra i possibili mapping tra Apache Cassandra e i livelli di coerenza Azure Cosmos DB quando si usa API Cassandra. Vengono mostrate le configurazioni per una singola area, letture in più aree con scritture a area singola e scritture in più aree.

> [!NOTE]
> Non si tratta di mapping esatti. Abbiamo invece fornito gli analoghi più vicini ad Apache Cassandra e ambiguità le eventuali differenze qualitative nella colonna più a destra. Come indicato in precedenza, è consigliabile esaminare [le impostazioni di coerenza](consistency-levels.md)del Azure Cosmos DB. 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Mapping del livello di account di coerenza Cassandra" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Mapping dinamico della coerenza Cassandra" lightbox="./media/cassandra-consistency/dynamic.png" :::

Se l'account Azure Cosmos è configurato con un livello di coerenza diverso da quello della coerenza assoluta, è possibile determinare la probabilità che i client ottengano letture solide e coerenti per i carichi di lavoro esaminando la metrica di *obsolescenza* (PBS) con decadimento ristretto. Questa metrica viene esposta nel portale di Azure. Per altre informazioni, vedere [Monitorare la metrica del decadimento ristretto probabilistico (Probabilistic Bounded Staleness, PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Il decadimento ristretto probabilistico mostra il livello di finalità della coerenza finale. Questa metrica fornisce informazioni dettagliate sulla frequenza con cui è possibile ottenere una coerenza più avanzata rispetto al livello di coerenza attualmente configurato nell'account Azure Cosmos. In altre parole, è possibile visualizzare la probabilità (misurata in millisecondi) di ottenere letture con coerenza assoluta per una combinazione di aree di scrittura e lettura.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla distribuzione globale e i livelli di coerenza per Azure Cosmos DB:

* [Panoramica della distribuzione globale](distribute-data-globally.md)
* [Panoramica del livello di coerenza](consistency-levels.md)
* [Disponibilità elevata](high-availability.md)
