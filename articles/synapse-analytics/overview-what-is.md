---
title: Che cos'è Azure Synapse Analytics?
description: Panoramica di Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 652f98659f96b36e3185432e50d9d36dc569bd43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537954"
---
# <a name="what-is-azure-synapse-analytics"></a>Che cos'è Azure Synapse Analytics?

**Azure Synapse** è un servizio di analisi aziendale che accelera il time to insight nei data warehouse e nei sistemi Big Data. In pratica raggruppa il meglio delle tecnologie **SQL** usate nel data warehousing aziendale, le tecnologie **Spark** usate per Big Data e le **pipeline** per l'integrazione dei dati e le operazioni ETL/ELT, oltre a essere pienamente integrato con altri servizi di Azure, come **Power BI**, **CosmosDB** e **AzureML**.

![Diagramma dell'Azure Synapse Analytics architettura.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>SQL leader di settore

**Synapse SQL** è un sistema di query distribuite per T-SQL che consente scenari di data warehousing e virtualizzazione dei dati ed estende T-SQL per affrontare scenari di streaming e machine learning.

* Synapse SQL offre modelli **di risorse sia serverless** che **dedicati.** Per prestazioni e costi prevedibili, creare pool SQL dedicati per riservare la potenza di elaborazione per i dati archiviati in tabelle SQL. Per i carichi di lavoro non pianificati o con picchi, usare l'endpoint SQL serverless sempre disponibile.
* Usare le funzionalità predefinite di **streaming** per trasferire dati da origini cloud in tabelle SQL
* Integrare l'intelligenza artificiale con SQL usando modelli di **Machine Learning** per assegnare punteggi ai dati tramite la [funzione T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="industry-standard-apache-spark"></a>Apache Spark standard di settore

**Apache Spark per Azure Synapse** si integra pienamente e facilmente con Apache Spark, il motore di Big Data open source più diffuso usato per la preparazione dei dati, l'ingegneria dei dati, i processi ETL e Machine Learning.

* Modelli ML con algoritmi SparkML e integrazione di AzureML per Apache Spark 2.4 con supporto predefinito per Linux Foundation Delta Lake.
* Modello di risorse semplificato che evita di preoccuparsi della gestione dei cluster.
* Avvio rapido di Spark e completa scalabilità automatica.
* Supporto predefinito per .NET per Spark, che consente di riutilizzare le competenze in C# e l'attuale codice .NET all'interno di un'applicazione Spark.

## <a name="working-with-your-data-lake"></a>Uso di Data Lake

Azure Synapse rimuove le tradizionali barriere tecnologiche che ostacolano l'uso simultaneo di SQL e Spark. È possibile combinare facilmente le piattaforme in base alle esigenze e alle competenze.

* Le tabelle definite nei file data lake vengono facilmente utilizzate da Spark o Hive.
* SQL e Spark possono esplorare e analizzare direttamente i file Parquet, CSV, TSV e JSON archiviati nel data lake.
* Caricamento rapido e scalabile dei dati tra database SQL e Spark

## <a name="built-in-data-integration"></a>Integrazione dei dati incorporata

Azure Synapse contiene lo stesso motore di integrazione dati e le stesse esperienze Azure Data Factory, consentendo di creare pipeline ETL su larga scala senza lasciare Azure Synapse Analytics.

* Inserimento di dati da più di 90 origini dati
* Processi ETL senza codice con attività del flusso di dati
* Orchestrare notebook, processi Spark, stored procedure, script SQL e altro ancora

## <a name="unified-experience"></a>Esperienza unificata 

**Synapse Studio** un unico modo per le aziende di creare soluzioni, mantenere e proteggere tutto in un'esperienza utente singola

* Eseguire attività chiave: inserimento, esplorazione, preparazione, orchestrazione, visualizzazione
* Monitorare risorse, utilizzo e utenti in SQL e Spark
* Usare il controllo degli accessi in base al ruolo per semplificare l'accesso alle risorse di analisi
* Scrivere codice SQL o Spark e integrarlo con processi CI/CD aziendali

## <a name="engage-with-the-synapse-community"></a>Interagire con la community di Synapse

- [Microsoft Q&A:](/answers/topics/azure-synapse-analytics.html)porre domande tecniche.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): per domande relative allo sviluppo.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](get-started.md)
* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)
