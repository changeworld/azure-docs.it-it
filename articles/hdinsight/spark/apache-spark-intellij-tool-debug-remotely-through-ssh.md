---
title: 'Azure Toolkit for IntelliJ: eseguire il debug di app Spark con SSH-HDInsight'
description: Istruzioni dettagliate su come usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni in cluster di HDInsight tramite SSH
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: de838e094c8a37d375aa6c7649ee5717705ad33c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866353"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Eseguire il debug di applicazioni Apache Spark in un cluster HDInsight con Azure Toolkit for IntelliJ tramite SSH

Questo articolo fornisce istruzioni dettagliate su come usare gli strumenti HDInsight in [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) per eseguire il debug remoto di applicazioni in un cluster HDInsight. Per eseguire il debug del progetto, è anche possibile guardare il video [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Eseguire il debug delle applicazioni Spark di HDInsight con Azure Toolkit for IntelliJ).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Vedere [Creare un cluster Apache Spark](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Per gli utenti di Windows: mentre si esegue l'applicazione Spark scala locale in un computer Windows, è possibile che venga generata un'eccezione, come spiegato in [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). che si verifica a causa di un file WinUtils.exe mancante in Windows.

    Per correggere l'errore, scaricare [Winutils.exe](https://github.com/steveloughran/winutils) in un percorso, ad esempio **C:\WinUtils\bin**. È quindi necessario aggiungere una variabile di ambiente **HADOOP_HOME** e impostare il valore della variabile su **C:\WinUtils**.

* [INTELLIJ idea](https://www.jetbrains.com/idea/download/#section=windows) (la Community Edition è gratuita).

* [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [Plug-in scala per IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Creare un'applicazione Spark Scala

1. Avviare IntelliJ IDEA e selezionare **Crea nuovo progetto** per aprire la finestra **Nuovo progetto**.

1. Selezionare **Apache Spark/HDInsight** nel riquadro sinistro.

1. Selezionare **progetto Spark con esempi (scala)** dalla finestra principale.

1. Nell'elenco **Strumento di compilazione** selezionare uno degli strumenti seguenti:

    * **Maven**, per ottenere supporto per la creazione guidata di un progetto Scala.
    * **SBT**, per la gestione delle dipendenze e la compilazione per il progetto Scala.

     :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png" alt-text="IntelliJ Crea nuovo progetto Spark" border="true":::

1. Selezionare **Avanti**.

1. Nella finestra **nuovo progetto** successiva specificare le informazioni seguenti:

    |Proprietà |Descrizione |
    |---|---|
    |Project name (Nome progetto)|Immettere un nome. Questa procedura dettagliata USA `myApp` .|
    |Posizione del progetto|Immettere il percorso desiderato in cui salvare il progetto.|
    |Project SDK (SDK progetto)|Se vuoto, selezionare **nuovo** e passare al JDK.|
    |Versione Spark|La creazione guidata integra la versione corretta dell'SDK di Spark e Scala. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2. x.** In questo esempio viene usata la versione **Spark 2.3.0 (Scala 2.11.8)** .|

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png" alt-text="IntelliJ nuovo progetto selezionare versione Spark" border="true":::

1. Selezionare **Fine**. Potrebbero occorrere alcuni minuti prima che il progetto diventi disponibile. Osservare l'angolo inferiore destro per lo stato di avanzamento.

1. Espandere il progetto e passare all'esempio **src**  >  **Main**  >  **scala**  >  . Fare doppio clic su **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Eseguire l'esecuzione in locale

1. Dallo script **SparkCore_WasbIOTest** , fare clic con il pulsante destro del mouse sull'editor di script, quindi selezionare l'opzione **Esegui ' SparkCore_WasbIOTest '** per eseguire l'esecuzione locale.

1. Una volta completata l'esecuzione locale, è possibile visualizzare il file di output Salva nei **dati** correnti di Esplora progetti  >  ****.

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png" alt-text="Risultato dell'esecuzione locale del progetto IntelliJ" border="true":::

1. Per questi strumenti la configurazione dell'esecuzione in locale predefinita viene impostata automaticamente quando l'esecuzione e il debug vengono eseguiti in modalità locale. Aprire la configurazione **[Spark in HDInsight] xxx** nell'angolo superiore destro. è possibile vedere **[Spark in HDInsight] xxx** già creato in **Apache Spark su HDInsight**. Passare alla scheda **Locally Run** (Esecuzione in locale).

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png" alt-text="Esecuzione locale del debug delle configurazioni in IntelliJ" border="true":::

    - [Environment variables](#prerequisites) (Variabili di ambiente): se la variabile di ambiente di sistema **HADOOP_HOME** è già stata impostata su **C:\WinUtils**, viene automaticamente rilevato che l'aggiunta manuale non è necessaria.
    - [WinUtils.exe Location](#prerequisites) (Posizione WinUtils.exe): se la variabile di ambiente di sistema non è stata impostata, per trovare la posizione è sufficiente fare clic sul relativo pulsante.
    - È sufficiente scegliere una delle due opzioni, che non sono necessarie in MacOS e Linux.

1. È anche possibile impostare la configurazione manualmente prima di eseguire l'esecuzione e il debug in modalità locale. Nella schermata precedente selezionare il segno più ( **+** ). Selezionare quindi l'opzione **Apache Spark on HDInsight** . Immettere le informazioni per il **nome** e il **nome della classe principale** da salvare e quindi fare clic sul pulsante Esecuzione in locale.

## <a name="perform-local-debugging"></a>Eseguire il debug in locale

1. Aprire lo script **SparkCore_wasbloTest** e impostare i punti di interruzione.

1. Fare clic con il pulsante destro del mouse sull'editor di script e quindi selezionare l'opzione **debug ' [Spark on HDInsight] xxx '** per eseguire il debug locale.

## <a name="perform-remote-run"></a>Esecuzione remota

1. Passare a **Esegui**  >  **Modifica configurazioni...**. Da questo menu è possibile creare o modificare le configurazioni per il debug remoto.

1. Nella finestra di dialogo **Run/Debug Configurations** (Esegui/Debug delle configurazioni) selezionare il segno più (**+**). Selezionare quindi l'opzione **Apache Spark on HDInsight** .

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png" alt-text="IntelliJ aggiungere una nuova configurazione" border="true":::

1. Passare alla scheda **Esegui in remoto in cluster** . Immettere le informazioni per il **nome**, il **cluster Spark** e il **nome della classe principale**. Fare quindi clic su **configurazione avanzata (debug remoto)**. Questi strumenti supportano il debug con **executor**. Il valore predefinito della chiave **numExectors** è 5. È consigliabile non impostarlo su un valore maggiore di 3.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png" alt-text="IntelliJ eseguire configurazioni di debug" border="true":::

1. Nella parte **configurazione avanzata (debug remoto)** selezionare **Abilita debug remoto Spark**. Immettere il nome utente SSH e inserire una password oppure usare un file di chiave privata. Se si desidera eseguire il debug in modalità remota, è necessario impostare questa opzione. Non è necessario impostarla se si desidera usare l'esecuzione in modalità remota.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png" alt-text="La configurazione avanzata di IntelliJ Abilita il debug remoto Spark" border="true":::

1. A questo punto, la configurazione viene salvata con il nome specificato. Per visualizzare i dettagli della configurazione, selezionare il relativo nome. Per apportare modifiche, selezionare **Modifica configurazioni**.

1. Dopo aver completato le impostazioni di configurazione, è possibile eseguire il progetto con il cluster remoto oppure eseguire il debug remoto.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png" alt-text="Pulsante di esecuzione remota del processo Spark remoto di IntelliJ" border="true":::

1. Fare clic sul pulsante **Disconnetti**. I log di invio non vengono visualizzati nel riquadro sinistro, ma l'esecuzione è ancora in corso nel back-end.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png" alt-text="Risultato dell'esecuzione remota del processo Spark remoto di IntelliJ debug" border="true":::

## <a name="perform-remote-debugging"></a>Eseguire l'esecuzione da remoto

1. Impostare i punti di interruzione e quindi fare clic sull'icona **Remote debug** (Debug remoto). La differenza rispetto all'invio remoto risiede nel fatto che non è necessario configurare il nome utente/password SSH.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png" alt-text="Icona di debug del processo Spark remoto di IntelliJ debug" border="true":::

1. Quando l'esecuzione del programma raggiunge il punto di interruzione, nel riquadro **Debugger** vengono visualizzate la scheda **Diver** e le due schede **Executor**. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare l'esecuzione del codice, che raggiungerà il punto di interruzione successivo. È necessario passare alla scheda **Executor** (Esecutore) corretta per trovare l'esecutore di destinazione da sottoporre a debug. È possibile visualizzare i log di esecuzione nella scheda **Console** corrispondente.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png" alt-text="IntelliJ debug-scheda debug processo Spark remoto" border="true":::

### <a name="perform-remote-debugging-and-bug-fixing"></a>Eseguire il debug e la correzione di bug da remoto

1. Impostare due punti di interruzione e quindi selezionare l'icona **Debug** per avviare il processo di debug remoto.

1. Il codice si interrompe in corrispondenza del primo punto di interruzione e le informazioni di parametro e variabile vengono visualizzate nella finestra **Variables** (Variabili).

1. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare. Il codice si interrompe in corrispondenza del secondo punto. L'eccezione viene rilevata come previsto.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png" alt-text="Errore di generazione del processo Spark remoto di IntelliJ debug" border="true":::

1. Selezionare di nuovo l'icona **Resume Program** (Riprendi programma). La finestra **HDInsight Spark Submission** (Invio Spark in HDInsight) mostra un errore di "esecuzione processo non riuscita".

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png" alt-text="Invio errore processo Spark remoto di IntelliJ debug" border="true":::

1. Per aggiornare dinamicamente il valore della variabile usando la funzionalità di debug di IntelliJ, selezionare nuovamente **Debug**. Viene visualizzato di nuovo il riquadro **Variables** (Variabili).

1. Fare clic con il pulsante destro del mouse sulla destinazione nella scheda **Debug**, quindi selezionare **Imposta valore**. Successivamente inserire un nuovo valore per la variabile. e selezionare **Invia** per salvarlo.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png" alt-text="Valore del set di processi Spark remoto di IntelliJ debug" border="true":::

1. Selezionare l'icona **Resume Program** (Riprendi programma) per continuare a eseguire il programma. Questa volta, non viene rilevata alcuna eccezione. È possibile notare come il progetto venga eseguito correttamente senza eccezioni.

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png" alt-text="Processo Spark remoto di IntelliJ debug senza eccezione" border="true":::

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo

* Creare il progetto Scala (video): [Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Apache Spark in Scala)
* Debug remoto (video): [Use Azure Toolkit for IntelliJ to debug Apache Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark nel cluster HDInsight)

### <a name="scenarios"></a>Scenari

* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di compilazione usando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](./apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](./apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per Jupyter Notebook nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con notebook di Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestione di risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)