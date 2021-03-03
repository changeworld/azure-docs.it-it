---
title: Domande frequenti - Azure Synapse Analytics
description: Domande frequenti su Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9a0fb8ed8ac54fa866b6db7d8f808c011c0c6758
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695822"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Domande frequenti su Azure Synapse Analytics

Questa guida include le domande frequenti su Azure Synapse Analytics.

## <a name="general"></a>Generale

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>D: Come si usano i ruoli Controllo degli accessi in base al ruolo per proteggere le aree di lavoro?

A: In Azure Synapse sono stati introdotti numerosi ruoli e diversi ambiti a cui assegnarli per semplificare la protezione dell'area di lavoro.

Ruoli Controllo degli accessi in base al ruolo di Synapse:
* Synapse Administrator
* Synapse SQL Administrator
* Synapse Spark Administrator
* Synapse Contributor (anteprima)
* Synapse Artifact Publisher (anteprima)
* Synapse Artifact User (anteprima)
* Synapse Compute Operator (anteprima)
* Synapse Credential User (anteprima)

Per proteggere l'area di lavoro Synapse, assegnare i ruoli Controllo degli accessi in base al ruolo a questi ambiti del controllo degli accessi in base al ruolo:
* Aree di lavoro
* Pool di Spark
* Runtime di integrazione
* Servizi collegati
* Credenziali

Inoltre, con i pool SQL dedicati si ottengono le stesse funzionalità di sicurezza già note e apprezzate.

### <a name="q-how-do-i-control-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>D: Ricerca per categorie controllare pool SQL dedicati, pool SQL senza server e pool Spark senza server?

A: Come punto di partenza, Azure Synapse è compatibile con le funzionalità predefinite di analisi e avvisi per i costi, disponibili a livello di sottoscrizione di Azure.

- Pool SQL dedicati: si ottiene visibilità diretta e controllo sui costi, perché si creano e si specificano le dimensioni dei pool SQL dedicati. È possibile controllare ulteriormente quali utenti possono creare o dimensionare i pool SQL dedicati con i ruoli Controllo degli accessi in base al ruolo di Azure.

- Pool SQL serverless: sono disponibili controlli per il monitoraggio e la gestione dei costi che consentono di limitare la spesa a livello giornaliero, settimanale e mensile. Per altre informazioni, vedere [Gestione dei costi per i pool SQL serverless](./sql/data-processed.md). 

- Pool di Spark serverless: è possibile limitare chi può creare pool di Spark con i ruoli Controllo degli accessi in base al ruolo di Synapse.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>D: L'area di lavoro di Synapse supporterà l'organizzazione di oggetti in cartelle e la granularità in fase di disponibilità generale?

A: Le aree di lavoro di Synapse supportano le cartelle definite dall'utente.

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>D: È possibile collegare più aree di lavoro di Power BI a una singola area di lavoro di Azure Synapse?
    
A: Attualmente, è possibile collegare solo una singola area di lavoro di Power BI a un'area di lavoro di Azure Synapse. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>D: Collegamento a Synapse in Cosmos DB è disponibile a livello generale?

A: Collegamento a Synapse per Apache Spark è disponibile a livello generale. Collegamento a Synapse per il pool SQL serverless è in fase di anteprima pubblica.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>D: L'area di lavoro di Azure Synapse supporta CI/CD? 

A: Sì. Tutti gli artefatti delle pipeline, i notebook, gli script SQL e le definizioni di processi Spark si troveranno in Git. Tutte le definizioni di pool verranno archiviate in Git come modelli di Azure Resource Manager. Gli oggetti dei pool SQL dedicati (schemi, tabelle, viste e così via) verranno gestiti con progetti di database con il supporto per CI/CD.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>D: Come riconoscere quali credenziali vengono usate per eseguire una pipeline? 

A: Ogni attività di una pipeline di Synapse viene eseguita usando le credenziali specificate all'interno del servizio collegato.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>D: Le istanze di SSIS IR sono supportate in Synapse?

 R: attualmente non è possibile. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>D: Come si esegue la migrazione delle pipeline esistenti da Azure Data Factory a un'area di lavoro di Azure Synapse?

A: Attualmente è necessario ricreare manualmente le pipeline di Azure Data Factory e gli artefatti correlati esportando il codice JSON dalla pipeline originale e importandolo nell'area di lavoro Synapse.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>D: Qual è la differenza tra Apache Spark per Synapse e Apache Spark?

A: Apache Spark per Synapse è Apache Spark con l'aggiunta del supporto per l'integrazione con altri servizi (AAD, AzureML e così via), di altre librerie (mssparktuils, Hummingbird) e di configurazioni con prestazioni pre-ottimizzate.

Il carico di lavoro attualmente in esecuzione in Apache Spark verrà eseguito in Apache Spark per Azure Synapse senza modifiche. 

### <a name="q-what-versions-of-spark-are-available"></a>D: Quali versioni di Spark sono disponibili?

A: Apache Spark per Azure Synapse supporta completamente Spark 2.4. Per un elenco completo dei componenti di base e delle versioni attualmente supportate, vedere [Versioni di Apache Spark supportate](./spark/apache-spark-version-support.md).

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>D: Esiste un equivalente di DButils in Azure Synapse Spark?

A: Sì, Apache Spark per Azure Synapse include la libreria **mssparkutils**. Per la documentazione completa dell'utilità, vedere [Introduzione alle utilità di Microsoft Spark](./spark/microsoft-spark-utilities.md).

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>D: Come si impostano i parametri di sessione in Apache Spark?

A: Per impostare i parametri di sessione, usare il comando magic %%configure disponibile. Per rendere effettivo il parametro, è necessario riavviare la sessione. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>D: Come si impostano i parametri a livello di cluster in un pool di Spark serverless?

A: Per impostare i parametri a livello di cluster, è possibile fornire un file spark.conf per il pool di Spark. Questo pool rispetterà quindi i parametri del file di configurazione. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>D: È possibile eseguire un cluster Spark multiutente in Azure Synapse Analytics?
 
A: Azure Synapse prevede motori appositamente progettati per specifici casi d'uso. Apache Spark per Synapse è progettato come servizio di processi e non come modello di cluster. Esistono due scenari in cui è richiesto un modello di cluster multiutente.

**Scenario 1: Molti utenti che accedono a un cluster per inviare dati a scopi di business intelligence.**

Il modo più semplice per realizzare questa attività consiste nell'elaborare i dati con Spark e quindi sfruttare le funzionalità server di Synapse SQL in modo da connettere Power BI a tali set di dati.

**Scenario 2: Più sviluppatori inclusi in un singolo cluster per risparmiare sui costi.**
 
Per soddisfare questo scenario, è consigliabile assegnare a ogni sviluppatore un pool di Spark serverless impostato per l'uso di un numero ridotto di risorse Spark. Poiché i pool di Spark serverless non comportano costi, finché vengono usati attivamente consentono di ridurre i costi quando ci sono più sviluppatori. I pool condividono i metadati (tabelle Spark), semplificando la collaborazione tra sviluppatori.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>D: Come è possibile includere, gestire e installare le librerie?

A:  È possibile installare pacchetti esterni tramite un file requirements.txt durante la creazione del pool di Spark nell'area di lavoro di Synapse o nel portale di Azure. Vedere [Gestire le librerie per Apache Spark in Azure Synapse Analytics](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Pool SQL dedicati

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>D: Quali sono le differenze funzionali tra i pool SQL dedicati e i pool serverless?

A: Per l'elenco completo di differenze, vedere [Differenze delle funzionalità T-SQL in Synapse SQL](./sql/overview-features.md).

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>D: Ora che Azure Synapse è disponibile a livello generale, come è possibile spostare in Azure Synapse i pool SQL dedicati che in precedenza erano autonomi? 

A: Non è disponibile una procedura di spostamento o migrazione. È possibile scegliere di abilitare le nuove funzionalità dell'area di lavoro nei pool esistenti. In tal caso, non sono previste modifiche di rilievo, ma sarà possibile usare le nuove funzionalità come Synapse Studio, Spark e i pool SQL serverless.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>D: Qual è la distribuzione predefinita dei pool SQL dedicati? 

A: Per impostazione predefinita, tutti i nuovi pool SQL dedicati verranno distribuiti in un'area di lavoro. Tuttavia, se necessario, è comunque possibile creare un pool SQL dedicato (in precedenza SQL Data Warehouse) in un fattore di forma autonomo. 

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](get-started.md)
* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)
