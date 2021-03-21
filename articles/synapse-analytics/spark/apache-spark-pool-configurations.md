---
title: Concetti relativi al pool di Apache Spark
description: Introduzione alle dimensioni e alle configurazioni del pool di Apache Spark in Azure sinapsi Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 6422c33f17879aa8ec4844cc6de63411528a388b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606158"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Configurazioni del pool di Apache Spark in Azure sinapsi Analytics

Un pool Spark è un set di metadati che definisce i requisiti delle risorse di calcolo e le caratteristiche del comportamento associate quando viene creata un'istanza di Spark. Queste caratteristiche includono, a titolo esemplificativo, il nome, il numero di nodi, le dimensioni del nodo, il comportamento di ridimensionamento e la durata (TTL). Un pool Spark non utilizza risorse. Non sono previsti costi per la creazione di pool Spark. Gli addebiti vengono addebitati solo dopo l'esecuzione di un processo Spark nel pool Spark di destinazione e l'istanza di Spark su richiesta.

Per informazioni su come creare un pool di Spark e visualizzarne tutte le proprietà, vedere [Creare un pool di Apache Spark (anteprima) in Synapse Analytics usando gli strumenti Web](../quickstart-create-apache-spark-pool-portal.md).

## <a name="nodes"></a>Nodi

Apache Spark istanza del pool è costituita da un nodo Head e da due o più nodi del ruolo di lavoro con un minimo di tre nodi in un'istanza di Spark.  Il nodo head esegue servizi di gestione aggiuntivi, ad esempio Livio, Yarn Gestione risorse, Zookeeper e il driver Spark.  Tutti i nodi eseguono servizi quali node Agent e yarn node Manager. Tutti i nodi del ruolo di lavoro eseguono il servizio di Spark Executor.

## <a name="node-sizes"></a>Dimensioni dei nodi

Un pool Spark può essere definito con dimensioni dei nodi che variano da un piccolo nodo di calcolo con 8 vCore e 64 GB di memoria fino a un nodo di calcolo XXLarge con 64 vCore e 432 GB di memoria per nodo. Le dimensioni dei nodi possono essere modificate dopo la creazione del pool, anche se potrebbe essere necessario riavviare l'istanza.

|Dimensione | vCore | Memoria|
|-----|------|-------|
|Piccola|4|32 GB|
|Medio|8|64 GB|
|Grande|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>Autoscale

I pool di Apache Spark offrono la possibilità di scalare automaticamente le risorse di calcolo in base alla quantità di attività.  Quando la funzionalità di scalabilità automatica è abilitata, è possibile impostare il numero minimo e massimo di nodi per la scalabilità.
Quando la funzionalità di scalabilità automatica è disabilitata, il numero di nodi impostati rimarrà fisso.  Questa impostazione può essere modificata dopo la creazione del pool, anche se potrebbe essere necessario riavviare l'istanza.

## <a name="automatic-pause"></a>Sospensione automatica

La funzionalità di sospensione automatica rilascia risorse dopo un periodo di inattività impostato riducendo il costo complessivo di un pool di Apache Spark.  Il numero di minuti di tempo di inattività può essere impostato quando questa funzionalità è abilitata.  La funzionalità di sospensione automatica è indipendente dalla funzionalità di scalabilità automatica. È possibile sospendere le risorse se la scalabilità automatica è abilitata o disabilitata.  Questa impostazione può essere modificata dopo la creazione del pool, anche se potrebbe essere necessario riavviare l'istanza.

## <a name="next-steps"></a>Passaggi successivi

* [Azure Synapse Analytics](../index.yml)
* [Documentazione di Apache Spark](https://spark.apache.org/docs/2.4.5/)