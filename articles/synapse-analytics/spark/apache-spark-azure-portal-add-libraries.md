---
title: Gestione dei pacchetti
description: Informazioni su come aggiungere e gestire le librerie usate da Apache Spark in Azure sinapsi Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 76d3bdc8a6eb133daa2d241bdf4c3b73271f0173
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100203"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gestire le librerie per Apache Spark in Azure sinapsi Analytics
Le librerie forniscono codice riutilizzabile che può essere utile includere nei programmi o nei progetti. 

Potrebbe essere necessario aggiornare l'ambiente del pool di Apache Spark senza server per vari motivi. Ad esempio, è possibile trovare quanto segue:
- una delle dipendenze principali ha rilasciato una nuova versione.
- è necessario un pacchetto aggiuntivo per il training del modello di Machine Learning o la preparazione dei dati.
- è stato trovato un pacchetto migliore e non è più necessario il pacchetto meno recente.
- il team ha creato un pacchetto personalizzato che è necessario disponibile nel pool di Apache Spark.

Per rendere disponibile per le applicazioni di terze parti o codice compilato localmente, è possibile installare una libreria in uno dei pool di Apache Spark senza server o nella sessione del notebook.
  
## <a name="default-installation"></a>Installazione predefinita
Apache Spark in Azure sinapsi Analytics ha un'installazione completa di Anaconda e librerie aggiuntive. L'elenco completo delle librerie è disponibile nel [Apache Spark supporto della versione](apache-spark-version-support.md). 

Quando viene avviata un'istanza di Spark, queste librerie verranno incluse automaticamente. È possibile aggiungere pacchetti aggiuntivi a livello di pool o di sessione di Spark.

## <a name="workspace-packages"></a>Pacchetti dell'area di lavoro
Quando si sviluppano modelli o applicazioni personalizzate, il team può sviluppare diversi artefatti di codice, ad esempio file della rotellina o jar, per comprimere il codice. 

In sinapsi i pacchetti dell'area di lavoro possono essere file di rotellina o file jar personalizzati o privati. È possibile caricare i pacchetti nell'area di lavoro e successivamente assegnarli a un pool Spark specifico. Una volta assegnati, i pacchetti dell'area di lavoro vengono installati automaticamente in tutte le sessioni del pool Spark.

Per altre informazioni su come gestire le librerie dell'area di lavoro, vedere le guide alle procedure seguenti:
- [Pacchetti dell'area di lavoro Python (anteprima): ](./apache-spark-manage-python-packages.md#install-wheel-files) Caricare i file della rotellina Python come pacchetto dell'area di lavoro e successivamente aggiungerli a specifici pool di Apache Spark senza server.
- [Pacchetti dell'area di lavoro scala/Java (anteprima): ](./apache-spark-manage-scala-packages.md#workspace-packages) Caricare i file jar scala e Java come pacchetto dell'area di lavoro e aggiungerli in seguito a pool di Apache Spark senza server specifici.

## <a name="pool-packages"></a>Pacchetti del pool
In alcuni casi, è possibile standardizzare il set di pacchetti usati in un pool di Apache Spark specificato. Questa standardizzazione può essere utile se gli stessi pacchetti sono comunemente installati da più utenti del team. 

Usando le funzionalità di gestione del pool di analisi delle sinapsi di Azure, è possibile configurare il set predefinito di librerie che si vuole installare in un determinato pool di Apache Spark senza server. Queste librerie vengono installate sul [runtime di base](./apache-spark-version-support.md). 

Attualmente la gestione del pool è supportata solo per Python. Per Python, i pool Spark di sinapsi usano conda per installare e gestire le dipendenze dei pacchetti Python. Quando si specificano le librerie a livello di pool, è ora possibile specificare un requirements.txt o un ambiente. yml. Questo file di configurazione dell'ambiente viene usato ogni volta che viene creata un'istanza Spark dal pool Spark. 

Per ulteriori informazioni su queste funzionalità, vedere la documentazione relativa alla [gestione del pool di Python](./apache-spark-manage-python-packages.md#pool-libraries).

> [!IMPORTANT]
> - Se il pacchetto che si sta installando è di grandi dimensioni o richiede molto tempo per l'installazione, questo influirà sul tempo di avvio dell'istanza di Spark.
> - La modifica della versione di PySpark, Python, scala/Java, .NET o Spark non è supportata.
> - L'installazione di pacchetti da PyPI non è supportata nelle aree di lavoro abilitate per DEP.

## <a name="session-scoped-packages"></a>Pacchetti con ambito sessione
Spesso, quando si esegue l'analisi interattiva dei dati o l'apprendimento automatico, è possibile che si desideri provare i pacchetti più recenti o che siano necessari pacchetti non ancora disponibili nel pool di Apache Spark. Anziché aggiornare la configurazione del pool, gli utenti possono ora usare pacchetti con ambito sessione per aggiungere, gestire e aggiornare le dipendenze della sessione.

I pacchetti con ambito sessione consentono agli utenti di definire le dipendenze dei pacchetti all'inizio della sessione. Quando si installa un pacchetto con ambito sessione, solo la sessione corrente può accedere ai pacchetti specificati. Di conseguenza, questi pacchetti con ambito sessione non influiscano su altre sessioni o processi che usano lo stesso pool di Apache Spark. Inoltre, queste librerie vengono installate sopra i pacchetti di runtime di base e a livello di pool. 

Per altre informazioni su come gestire i pacchetti con ambito sessione, vedere le guide alle procedure seguenti:
- [Pacchetti della sessione Python (anteprima):](./apache-spark-manage-python-packages.md) All'inizio di una sessione, fornire un oggetto conda *Environment. yml* per installare pacchetti Python aggiuntivi da repository comuni. 
- [Pacchetti di sessione scala/Java: ](./apache-spark-manage-scala-packages.md) All'inizio della sessione, fornire un elenco di file jar da installare usando ```%%configure``` .

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le librerie predefinite: [supporto della versione Apache Spark](apache-spark-version-support.md)
