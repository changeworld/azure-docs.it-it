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
ms.openlocfilehash: a7dcd630b0d5987c796ebb5a02657af9130221e6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045621"
---
# <a name="what-is-azure-synapse-analytics"></a>Che cos'è Azure Synapse Analytics?

**Azure sinapsi** è un servizio di analisi aziendale che accelera il time-to-Insight nei data warehouse e nei sistemi Big Data. In pratica raggruppa il meglio delle tecnologie **SQL** usate nel data warehousing aziendale, le tecnologie **Spark** usate per Big Data e le **pipeline** per l'integrazione dei dati e le operazioni ETL/ELT, oltre a essere pienamente integrato con altri servizi di Azure, come **Power BI**, **CosmosDB** e **AzureML**.


## <a name="industry-leading-sql"></a>SQL leader di settore

**Sinapsi SQL** è un sistema di query distribuite per t-SQL che consente scenari di data warehousing e virtualizzazione dei dati ed estende t-SQL per risolvere scenari di streaming e machine learning.

* Sinapsi SQL offre modelli di risorse sia senza **Server** che **dedicati** . Per prestazioni e costi prevedibili, creare pool SQL dedicati per riservare la potenza di elaborazione per i dati archiviati in tabelle SQL. Per i carichi di lavoro non pianificati o con picchi, usare l'endpoint SQL serverless sempre disponibile.
* Usare le funzionalità predefinite di **streaming** per trasferire dati da origini cloud in tabelle SQL
* Integrare l'intelligenza artificiale con SQL usando modelli di **Machine Learning** per assegnare punteggi ai dati tramite la [funzione T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="industry-standard-apache-spark"></a>Apache Spark standard di settore

**Apache Spark per Azure Synapse** si integra pienamente e facilmente con Apache Spark, il motore di Big Data open source più diffuso usato per la preparazione dei dati, l'ingegneria dei dati, i processi ETL e Machine Learning.

* Modelli ML con algoritmi SparkML e integrazione di AzureML per Apache Spark 2.4 con supporto predefinito per Linux Foundation Delta Lake.
* Modello di risorse semplificato che evita di preoccuparsi della gestione dei cluster.
* Avvio rapido di Spark e completa scalabilità automatica.
* Supporto predefinito per .NET per Spark, che consente di riutilizzare le competenze in C# e l'attuale codice .NET all'interno di un'applicazione Spark.

## <a name="working-with-your-data-lake"></a>Uso della Data Lake

Azure Synapse rimuove le tradizionali barriere tecnologiche che ostacolano l'uso simultaneo di SQL e Spark. È possibile combinare facilmente le piattaforme in base alle esigenze e alle competenze.

* Un sistema di metadati condiviso compatibile con Hive consente di utilizzare in Spark o Hive le tabelle definite nei file del data lake.
* SQL e Spark possono esplorare e analizzare direttamente i file Parquet, CSV, TSV e JSON archiviati nel data lake.
* Procedure veloci e scalabili di caricamento e scaricamento dei dati trasferiti tra database SQL e Spark

## <a name="built-in-data-integration"></a>Integrazione dei dati incorporata

La sinapsi di Azure contiene lo stesso motore di integrazione dei dati e le stesse esperienze di Azure Data Factory, consentendo di creare pipeline ETL su larga scala senza uscire dall'analisi delle sinapsi di Azure.

* Inserimento di dati da più di 90 origini dati
* Processi ETL senza codice con attività del flusso di dati
* Orchestrazione di notebook, processi Spark, stored procedure, script SQL e altro ancora

## <a name="unified-management-monitoring-and-security"></a>Soluzione unificata di gestione, monitoraggio e sicurezza

Azure Synapse offre alle aziende una singola soluzione per gestire le risorse di analisi, monitorare l'utilizzo e l'attività e applicare la sicurezza.

* Assegnazione agli utenti di un ruolo per semplificare l'accesso alle risorse di analisi
* Controllo degli accessi con granularità fine per dati e codice
* Un singolo dashboard per monitorare risorse, utilizzo e utenti tra SQL e Spark

## <a name="unified-experience"></a>Esperienza unificata

**Sinapsi Studio** è l'esperienza utente che unisce tutti gli elementi per gli ingegneri dei dati. Consente di eseguire tutte le attività necessarie per creare una soluzione analitica completa.

* Attività tecnico dati principali in un'unica posizione: inserimento, esplorazione, preparazione, orchestrazione, visualizzazione
* Produttività leader nel settore per la scrittura di codice SQL o Spark: creazione, debug e ottimizzazione delle prestazioni
* Integrazione con il processo Enterprise CI/CD

## <a name="engage-with-the-synapse-engineering-team"></a>Collaborare con il team di progettazione di Synapse

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): per domande relative allo sviluppo.
- [Pagina di domande e risposte Microsoft](/answers/topics/azure-synapse-analytics.html): per domande di carattere tecnico.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](get-started.md)
* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)
