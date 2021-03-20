---
title: Terminologia di Azure Synapse Analytics
description: Guida di riferimento per l'uso di Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 828f37030ae567cacbaad25849b7ba24c561c20c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98132767"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologia di Azure Synapse Analytics

Questo documento illustra i concetti di base di Azure Synapse Analytics.

## <a name="basics"></a>Operazioni di base

Un'**area di lavoro di Synapse** è un limite di collaborazione a protezione diretta per l'analisi aziendale basata sul cloud in Azure. Un'area di lavoro viene distribuita in un'area specifica e con un account e un file system di ADLS Gen2 associati (per l'archiviazione di dati temporanei). Un'area di lavoro in un gruppo di risorse.

Un'area di lavoro consente di eseguire analisi con SQL e Apache Spark. Le risorse disponibili per SQL e Spark Analytics sono organizzate in **pool** SQL e Spark. 

## <a name="linked-services"></a>Servizi collegati

Un'area di lavoro può contenere un numero indefinito di **servizi collegati**, essenzialmente stringhe di connessione che definiscono le informazioni necessarie per la connessione dell'area di lavoro a risorse esterne.

## <a name="synapse-sql"></a>SQL Synapse

**Synapse SQL** offre la possibilità di eseguire analisi basate su T-SQL nell'area di lavoro di Synapse. Synapse SQL ha due modelli di utilizzo: dedicato e serverless.  Per il modello dedicato, usare **pool SQL dedicati**. Un'area di lavoro può contenere un numero qualsiasi di questi pool. Per il modello serverless, usare i **pool SQL serverless**. Ogni area di lavoro dispone di uno di questi pool.

All'interno di Synapse Studio è possibile usare i pool SQL creando ed eseguendo **script SQL**.

## <a name="apache-spark-for-synapse"></a>Apache Spark per Synapse

Per usare Spark Analytics, creare e usare **pool di Apache Spark serverless** nell'area di lavoro di Synapse. Quando si inizia a usare un pool di Spark, l'area di lavoro crea una **sessione Spark** per gestire le risorse associate. 

Per usare Spark all'interno di Synapse, sono disponibili due modi:
* **Notebook Spark** per eseguire attività di data science e progettazione con Scala, PySpark, C# e SparkSQL
* **Definizioni di processi Spark** per eseguire processi Spark tramite file JAR.

## <a name="pipelines"></a>Pipelines

Le pipeline rappresentano il metodo con cui Azure Synapse fornisce integrazione dei dati, consentendo di spostare i dati tra servizi e di orchestrare le attività.

* Le **pipeline** sono un raggruppamento logico di attività che insieme svolgono una funzione.
* Le **attività** definiscono le azioni all'interno di una pipeline da eseguire sui dati, ad esempio la copia, l'esecuzione di un notebook o uno script SQL.
* I **flusso di dati** sono un tipo specifico di attività che offrono un'esperienza senza codice per eseguire trasformazioni sui dati dietro le quinte con Synapse Spark.
* **Trigger**: esegue una pipeline. Può essere eseguito manualmente o automaticamente (pianificazione, finestra a cascata o basata su eventi).
* **Set di dati di integrazione**: vista denominata di dati che semplicemente punta o fa riferimento ai dati da usare come input e output di un'attività. Appartiene a un servizio collegato.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](get-started.md)
* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)

