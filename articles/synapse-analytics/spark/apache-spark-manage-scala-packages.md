---
title: Gestire le librerie Java scala & per Apache Spark
description: Informazioni su come aggiungere e gestire le librerie scala e Java in Azure sinapsi Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098707"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Gestire i pacchetti scala e Java per Apache Spark in Azure sinapsi Analytics

Le librerie forniscono codice riutilizzabile che può essere utile includere nei programmi o nei progetti. 

Per diversi motivi, potrebbe essere necessario aggiornare l'ambiente del pool di Apache Spark senza server. Ad esempio, è possibile trovare quanto segue:
- una delle dipendenze principali ha rilasciato una nuova versione.
- è necessario un pacchetto aggiuntivo per il training del modello di Machine Learning o la preparazione dei dati.
- è stato trovato un pacchetto migliore e non è più necessario il pacchetto meno recente.

Per rendere disponibile per le applicazioni di terze parti o codice compilato localmente, è possibile installare una libreria in uno dei pool di Apache Spark senza server o nella sessione del notebook. In questo articolo verrà illustrato come gestire i pacchetti scala e Java.

## <a name="default-installation"></a>Installazione predefinita
Apache Spark in Azure sinapsi Analytics include un set completo di librerie per le attività comuni di progettazione dei dati, preparazione dei dati, apprendimento automatico e visualizzazione dei dati. L'elenco completo delle librerie è disponibile nel [Apache Spark supporto della versione](apache-spark-version-support.md). 

Quando viene avviata un'istanza di Spark, queste librerie verranno incluse automaticamente. È possibile aggiungere pacchetti scala/Java aggiuntivi a livello di sessione e pool di Spark.

## <a name="workspace-packages"></a>Pacchetti dell'area di lavoro
I pacchetti dell'area di lavoro possono essere file con estensione jar personalizzati o privati. È possibile caricare i pacchetti nell'area di lavoro e successivamente assegnarli a un pool Spark specifico.

Per aggiungere i pacchetti dell'area di lavoro:
1. Passare alla scheda **Gestisci**  >  **pacchetti dell'area di lavoro** .
2. Caricare i file jar usando il selettore file.
3. Una volta caricati i file nell'area di lavoro di sinapsi di Azure, è possibile aggiungere questi file jar a un pool di Apache Spark specificato.

![Screenshot che evidenzia i pacchetti dell'area di lavoro.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Visualizzare i pacchetti dell'area di lavoro")

## <a name="pool-libraries"></a>Librerie di pool
Dopo aver identificato i pacchetti scala e Java che si vuole usare per l'applicazione Spark, è possibile installarli in un pool Spark. Le librerie a livello di pool sono disponibili per tutti i blocchi appunti e i processi in esecuzione nel pool.

È possibile aggiornare le librerie del pool di Spark passando ad Azure sinapsi Studio o portale di Azure. Qui è possibile selezionare le librerie dell'area di lavoro da installare. 

Una volta salvate le modifiche, un processo Spark eseguirà l'installazione e memorizza nella cache l'ambiente risultante per un successivo riutilizzo. Al termine del processo, i nuovi processi Spark o le sessioni notebook utilizzeranno le librerie di pool aggiornate. 

> [!IMPORTANT]
> - Se il pacchetto che si sta installando è di grandi dimensioni o richiede molto tempo per l'installazione, questo influirà sul tempo di avvio dell'istanza di Spark.
> - La modifica della versione di PySpark, Python, scala/Java, .NET o Spark non è supportata.

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Gestire i pacchetti da Azure sinapsi Studio o portale di Azure
Le librerie di pool Spark possono essere gestite da Azure sinapsi Studio o portale di Azure. 

Per aggiornare o aggiungere librerie a un pool Spark:
1. Passare all'area di lavoro di Azure sinapsi Analytics dal portale di Azure.

    Se si esegue l'aggiornamento dal **portale di Azure**:

    - Nella sezione **risorse sinapsi** selezionare la scheda **pool di Apache Spark** e selezionare un pool Spark nell'elenco.
     
    - Selezionare i **pacchetti** dalla sezione **Impostazioni** del pool Spark.
  
    ![Schermata che evidenzia il pulsante Carica file di configurazione dell'ambiente.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Aggiungere librerie Python")
   
    Se si esegue l'aggiornamento da **sinapsi Studio**:
    - Selezionare **Gestisci** dal pannello di navigazione principale, quindi selezionare **Apache Spark pool**.

    - Selezionare la sezione **pacchetti** per un pool Spark specifico.
    ![Screenshot che evidenzia l'opzione Carica configurazione ambiente da studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Aggiungere librerie Python da studio")
   
2. Per aggiungere file jar, passare alla sezione **pacchetti dell'area di lavoro** da aggiungere al pool. 
3. Una volta salvate le modifiche, verrà attivato un processo di sistema per installare e memorizzare nella cache le librerie specificate. Questo processo consente di ridurre i tempi di avvio della sessione complessivi. 
4. Una volta completato il processo, tutte le nuove sessioni preleveranno le librerie di pool aggiornate.

> [!IMPORTANT]
> Selezionando l'opzione per **forzare nuove impostazioni**, si terminano tutte le sessioni correnti per il pool Spark selezionato. Una volta terminate le sessioni, sarà necessario attendere il riavvio del pool. 
>
> Se questa impostazione è deselezionata, sarà necessario attendere la fine della sessione Spark corrente o arrestarla manualmente. Al termine della sessione, sarà necessario consentire il riavvio del pool.

#### <a name="track-installation-progress-preview"></a>Tenere traccia dello stato dell'installazione (anteprima)
Un processo Spark riservato del sistema viene avviato ogni volta che un pool viene aggiornato con un nuovo set di librerie. Questo processo Spark consente di monitorare lo stato dell'installazione della libreria. Se l'installazione non riesce a causa di conflitti di libreria o di altri problemi, il pool Spark verrà ripristinato sullo stato precedente o predefinito. 

Inoltre, gli utenti possono esaminare i log di installazione per identificare i conflitti di dipendenza o vedere quali librerie sono state installate durante l'aggiornamento del pool.

Per visualizzare i log seguenti:
1. Passare all'elenco di applicazioni Spark nella scheda **monitoraggio** . 
2. Selezionare il processo dell'applicazione Spark di sistema che corrisponde all'aggiornamento del pool. Questi processi di sistema vengono eseguiti con il titolo *SystemReservedJob-LibraryManagement* .
   ![Schermata che evidenzia il processo della libreria riservata di sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visualizza processo libreria di sistema")
3. Passare alla visualizzazione del **driver** e dei log **stdout** . 
4. Nei risultati vengono visualizzati i log correlati all'installazione dei pacchetti.
    ![Screenshot che evidenzia i risultati dei processi della libreria riservata di sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visualizza stato processo libreria di sistema")

## <a name="session-scoped-libraries"></a>Librerie con ambito sessione 
Oltre alle librerie a livello di pool, è anche possibile specificare librerie con ambito sessione all'inizio di una sessione del notebook.  Le librerie con ambito sessione consentono di specificare e usare i pacchetti jar esclusivamente all'interno di una sessione del notebook. 

Quando si usano le librerie con ambito sessione, è importante tenere presente quanto segue:
   - Quando si installano le librerie con ambito sessione, solo il notebook corrente può accedere alle librerie specificate. 
   - Queste librerie non influiscano su altre sessioni o processi che usano lo stesso pool Spark. 
   - Queste librerie vengono installate nelle librerie di runtime di base e a livello di pool. 
   - Le librerie di notebook avranno la precedenza più alta.

Per specificare pacchetti Java o scala con ambito sessione, è possibile usare l' ```%%configure``` opzione:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Si consiglia di eseguire%% configure all'inizio del notebook. È possibile fare riferimento a questo [documento](https://github.com/cloudera/livy#request-body) per l'elenco completo dei parametri validi.

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le librerie predefinite: [supporto della versione Apache Spark](apache-spark-version-support.md)
- Risolvere gli errori di installazione della libreria: [risoluzione degli errori di libreria](apache-spark-troubleshoot-library-errors.md)
