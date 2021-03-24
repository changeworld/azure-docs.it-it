---
title: Risolvere i problemi di Apache Spark in Azure HDInsight
description: Risposte alle domande frequenti sull'uso di Apache Spark e Azure HDInsight.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: b54b9d932505ada890ac21c1b8de3178ad2f0042
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867509"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Risolvere i problemi di Apache Spark tramite Azure HDInsight

Informazioni sui problemi principali e le relative soluzioni quando si lavora con i payload Apache Spark in [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Come si configura un'applicazione Apache Spark tramite Apache Ambari nei cluster?

I valori di configurazione di Spark possono essere ottimizzati per evitare un'eccezione Apache Spark applicazione `OutofMemoryError` . I passaggi seguenti illustrano i valori di configurazione di Spark predefiniti in Azure HDInsight:

1. Accedere a Ambari `https://CLUSTERNAME.azurehdidnsight.net` con le credenziali del cluster. La schermata iniziale Visualizza un dashboard di panoramica. Sono presenti lievi differenze estetiche tra HDInsight 3,6 e 4,0.

1. Passare a **Spark2**  >  **configs**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png" alt-text="Selezionare la scheda Configs (Configurazioni)" border="true":::

1. Nell'elenco delle configurazioni selezionare ed espandere **Custom-spark2-defaults**.

1. Ricercare l'impostazione del valore che si desidera modificare, come **spark.executor.memory**. In questo caso, il valore di **9728m** è troppo elevato.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png" alt-text="Selezionare custom-spark-defaults" border="true":::

1. Configurare il valore sull'impostazione consigliata. Il valore **2048m** è consigliato per questa impostazione.

1. Salvare il valore, quindi salvare la configurazione. Selezionare **Salva**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png" alt-text="Cambiare il valore in 2048m" border="true":::

    Immettere una nota sulle modifiche apportate alla configurazione, quindi selezionare **Save** (Salva).

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png" alt-text="Immettere una nota sulle modifiche apportate" border="true":::

    Si riceve una notifica se una delle configurazioni richiede attenzione. Annotare gli elementi e quindi selezionare **Proceed Anyway** (Continuare comunque).

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png" alt-text="Selezionare Proceed Anyway (Continuare comunque)" border="true":::

1. Ogni volta che viene salvata una configurazione, viene chiesto di riavviare il servizio. Selezionare **Restart** (Riavvia).

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png" alt-text="Seleziona riavvio" border="true":::

    Confermare il riavvio.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png" alt-text="Selezionare la conferma del riavvio completo" border="true":::

    È possibile esaminare i processi in esecuzione.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png" alt-text="Esaminare i processi in esecuzione" border="true":::

1. È possibile aggiungere configurazioni. Nell'elenco delle configurazioni selezionare **Custom-spark2-defaults** e quindi selezionare **Add Property** (Aggiungi proprietà).

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png" alt-text="Selezionare Add property (Aggiungi proprietà)" border="true":::

1. Definire una nuova proprietà. È possibile definire una singola proprietà usando una finestra di dialogo per impostazioni specifiche, ad esempio il tipo di dati. In alternativa, è possibile definire più proprietà usando una definizione per riga.

    In questo esempio la proprietà **spark.driver.memory** è stata definita con un valore di **4g**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png" alt-text="Definire una nuova proprietà" border="true":::

1. Salvare la configurazione e quindi riavviare il servizio come descritto nei passaggi 6 e 7.

Queste modifiche si applicano a tutto il cluster ma è possibile eseguirne l'override quando si invia il processo Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Ricerca per categorie configurare un'applicazione Apache Spark utilizzando un Jupyter Notebook nei cluster?

Nella prima cella del Jupyter Notebook, dopo la direttiva **%% Configure** , specificare le configurazioni di Spark in formato JSON valido. Modificare i valori effettivi in base alla necessità:

:::image type="content" source="./media/apache-troubleshoot-spark/add-configuration-cell.png" alt-text="Aggiungere una configurazione" border="true":::

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Come si configura un'applicazione Apache Spark tramite Apache Livy nei cluster?

Inviare l'applicazione Spark a Livy usando un client REST come cURL. Usare un comando simile al seguente. Modificare i valori effettivi in base alla necessità:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Come si configura un'applicazione Apache Spark tramite lo script spark-submit nei cluster?

Avviare spark-shell usando un comando simile al seguente. Modificare il valore effettivo delle configurazioni in base alla necessità:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Altre letture

[Invio del processo Apache Spark nei cluster HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* [Panoramica sulla gestione della memoria di Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debug di un'applicazione Spark nei cluster HDInsight](/archive/blogs/azuredatalake/spark-debugging-101).

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).