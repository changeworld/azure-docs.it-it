---
title: Ridimensionare i cluster - Azure HDInsight
description: Ridimensionare un cluster Apache Hadoop in modo elastico in modo che corrisponda al carico di lavoro in Azure HDInsight
ms.author: ashish
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 1c388cb070c66fc3a2322c358bc4113ed2106c77
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761850"
---
# <a name="scale-azure-hdinsight-clusters"></a>Ridimensionare Azure HDInsight cluster

HDInsight offre elasticità con opzioni per aumentare e ridimensionare il numero di nodi di lavoro nei cluster. Questa elasticità consente di compattare un cluster dopo ore o fine settimana. Ed espanderlo durante i picchi di richieste aziendali.

Aumentare le prestazioni del cluster prima dell'elaborazione batch periodica in modo che il cluster abbia risorse adeguate.  Dopo aver completato l'elaborazione e ridotto l'utilizzo, ridimensionare il cluster HDInsight a un numero inferiore di nodi di lavoro.

È possibile ridimensionare un cluster manualmente usando uno dei metodi descritti di seguito. È anche possibile usare le [opzioni di scalabilità](hdinsight-autoscale-clusters.md) automatica per aumentare o ridimensionare automaticamente in risposta a determinate metriche.

> [!NOTE]  
> Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà.

## <a name="utilities-to-scale-clusters"></a>Utilità per ridimensionare i cluster

Microsoft offre le utilità seguenti per ridimensionare i cluster:

|Utilità | Descrizione|
|---|---|
|[PowerShell Az](/powershell/azure)|[`Set-AzHDInsightClusterSize`](/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Interfaccia della riga di comando di Azure](/cli/azure/) | [`az hdinsight resize`](/cli/azure/hdinsight#az_hdinsight_resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Interfaccia della riga di comando classica di Azure](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Portale di Azure](https://portal.azure.com)|Aprire il riquadro del cluster HDInsight, selezionare **Dimensioni** cluster nel menu a sinistra, quindi nel riquadro Dimensioni cluster digitare il numero di nodi di lavoro e selezionare Salva.|  

:::image type="content" source="./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png" alt-text="portale di Azure cluster di scalabilità orizzontale":::

Usando uno di questi metodi, è possibile aumentare o ridurre il cluster HDInsight in pochi minuti.

> [!IMPORTANT]  
> * L'interfaccia della riga di comando classica di Azure è deprecata e deve essere usata solo con il modello di distribuzione classica. Per tutte le altre distribuzioni, usare l'interfaccia della riga [di comando di Azure.](/cli/azure/)
> * Il modulo AzureRM di PowerShell è deprecato.  Usare il modulo [Az quando](/powershell/azure/new-azureps-module-az) possibile.

## <a name="impact-of-scaling-operations"></a>Impatto delle operazioni di ridimensionamento

Quando si **aggiungono** nodi al cluster HDInsight in esecuzione (scalabilità verticale), i processi non saranno interessati. È possibile inviare nuovi processi in tutta sicurezza durante l'esecuzione del processo di ridimensionamento. Se l'operazione di ridimensionamento non riesce, l'errore lascia il cluster in uno stato funzionale.

Se si **rimuovono** nodi (riduzione), i processi in sospeso o in esecuzione avranno esito negativo al termine dell'operazione di ridimensionamento. Questo errore è dovuto al riavvio di alcuni servizi durante il processo di ridimensionamento. Il cluster potrebbe rimanere bloccato in modalità sicura durante un'operazione di ridimensionamento manuale.

Impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

* Apache Hadoop

    È possibile aumentare facilmente il numero di nodi di lavoro in un cluster Hadoop in esecuzione senza influire sui processi. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di ridimensionamento vengono gestiti correttamente. Il cluster viene sempre lasciato in uno stato funzionale.

    Quando un cluster Hadoop viene ridotto con un numero inferiore di nodi dati, alcuni servizi vengono riavviati. A causa di questo comportamento, tutti i processi in esecuzione e in sospeso avranno esito negativo al completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.

* Apache HBase

    È possibile aggiungere o rimuovere facilmente nodi al cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. Tuttavia, è possibile bilanciare manualmente i server a livello di regione. Accedere al nodo head del cluster ed eseguire i comandi seguenti:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Per altre informazioni sull'uso della shell HBase, vedere [Iniziare a usare un esempio di Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    È possibile aggiungere o rimuovere facilmente nodi dati mentre Storm è in esecuzione. Tuttavia, dopo aver completato correttamente l'operazione di ridimensionamento, sarà necessario ribilanciare la topologia. Il ribilanciamento consente alla topologia di modificare le impostazioni [di parallelismo](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) in base al nuovo numero di nodi nel cluster. Per ribilanciare le topologie in esecuzione, usare una delle opzioni seguenti:

  * Interfaccia utente Web di Storm

    usare la procedura seguente per ribilanciare una topologia usando l'interfaccia utente Storm.

    1. Aprire `https://CLUSTERNAME.azurehdinsight.net/stormui` nel Web browser, dove è il nome `CLUSTERNAME` del cluster Storm. Se richiesto, immettere il nome amministratore (admin) del cluster HDInsight e la password specificata durante la creazione del cluster.

    1. Selezionare la topologia da ribilanciare e quindi fare clic sul pulsante **Rebalance** (Ribilancia). Immettere il ritardo prima che venga eseguita l'operazione di ribilanciamento.

        :::image type="content" source="./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="Ribilanciamento della scalabilità di HDInsight Storm":::

  * Interfaccia della riga di comando (CLI)

    connettersi al server e usare il comando seguente per ribilanciare una topologia:

    ```bash
     storm rebalance TOPOLOGYNAME
    ```

    È anche possibile specificare parametri per eseguire l'override degli hint di parallelismo forniti in origine dalla topologia. Ad esempio, il codice seguente riconfigura la topologia in 5 processi di lavoro, 3 executor per il componente blue-spout e 10 executor per il componente `mytopology` yellow-bolt.

    ```bash
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

* Kafka

    è consigliabile ribilanciare le repliche di una partizione dopo le operazioni di ridimensionamento. Per altre informazioni, vedere il documento [Disponibilità elevata dei dati con Apache Kafka in HDInsight](./kafka/apache-kafka-high-availability.md).

* Apache Hive LLAP

    Dopo il `N` ridimensionamento in nodi di lavoro, HDInsight imposta automaticamente le configurazioni seguenti e riavvia Hive.

  * Numero massimo di query simultanee: `hive.server2.tez.sessions.per.default.queue = min(N, 32)`
  * Numero di nodi usati da LLAP di Hive: `num_llap_nodes  = N`
  * Numero di nodi per l'esecuzione del daemon Hive LLAP: `num_llap_nodes_for_llap_daemons = N`

## <a name="how-to-safely-scale-down-a-cluster"></a>Come ridimensionare in modo sicuro un cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Ridimensionare un cluster con processi in esecuzione

Per evitare che i processi in esecuzione non riescano durante un'operazione di ridimensionamento, è possibile provare a eseguire tre operazioni:

1. Attendere il completamento dei processi prima di ridurre il cluster.
1. Terminare manualmente i processi.
1. Inviare di nuovo i processi al termine dell'operazione di ridimensionamento.

Per visualizzare un elenco dei processi in sospeso e in esecuzione, è possibile usare l'interfaccia utente di YARN **Resource Manager,** seguendo questa procedura:

1. Selezionare il proprio cluster nel [portale di Azure](https://portal.azure.com/).  Il cluster viene aperto in una nuova pagina del portale.
2. Dalla visualizzazione principale passare a **Dashboard cluster**  >  **Ambari home**. Immettere le credenziali del cluster.
3. Nell'interfaccia utente di Ambari selezionare **YARN** nell'elenco dei servizi nel menu a sinistra.  
4. Nella pagina YARN  selezionare Collegamenti rapidi e passare il puntatore del mouse sul nodo head attivo, quindi selezionare Resource Manager **interfaccia utente.**

    :::image type="content" source="./media/hdinsight-scaling-best-practices/resource-manager-ui1.png" alt-text="Collegamenti rapidi di Apache Ambari Resource Manager interfaccia utente":::

È possibile accedere direttamente all'interfaccia Resource Manager con `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` .

Verrà visualizzato un elenco dei processi, insieme allo stato corrente di ognuno. Nello screenshot è presente un processo attualmente in esecuzione:

:::image type="content" source="./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png" alt-text="Resource Manager applicazioni dell'interfaccia utente":::

Per terminare manualmente l'applicazione in esecuzione, eseguire il comando seguente dalla shell SSH:

```bash
yarn application -kill <application_id>
```

Ad esempio:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Blocco in modalità provvisoria

Quando si ridimensiona un cluster, HDInsight usa le interfacce di gestione di Apache Ambari per rimuovere prima le autorizzazioni dei nodi di lavoro aggiuntivi. I nodi replicano i blocchi HDFS in altri nodi di lavoro online. Successivamente, HDInsight ridimensiona in modo sicuro il cluster. HDFS passa in modalità sicura durante l'operazione di ridimensionamento. HdFS dovrebbe uscire al termine del ridimensionamento. In alcuni casi, tuttavia, HDFS rimane bloccato in modalità sicura durante un'operazione di ridimensionamento a causa della sotto-replica del blocco di file.

Per impostazione predefinita, HDFS è configurato con l'impostazione 1, che controlla il numero di copie di `dfs.replication` ogni blocco di file disponibili. Ogni copia di un blocco di file viene archiviata in un nodo diverso del cluster.

Quando il numero previsto di copie in blocchi non è disponibile, HDFS entra in modalità provvisoria e Ambari genera avvisi. HDFS può entrare in modalità sicura per un'operazione di ridimensionamento. Il cluster potrebbe rimanere bloccato in modalità sicura se il numero necessario di nodi non viene rilevato per la replica.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Errori di esempio quando la modalità sicura è attiva

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

È possibile esaminare i log dei nodi dei nomi nella cartella `/var/log/hadoop/hdfs/`, in prossimità del momento in cui il cluster è stato ridimensionato, per visualizzare quando è stata attivata la modalità sicura. I file di log sono denominati `Hadoop-hdfs-namenode-<active-headnode-name>.*`.

La causa principale è che Hive dipende dai file temporanei in HDFS durante l'esecuzione di query. Quando HDFS entra in modalità provvisoria, Hive non può eseguire query perché non può scrivere in HDFS. I file temporanei in HDFS si trovano nell'unità locale montata nelle singole macchine virtuali del nodo di lavoro. I file vengono replicati tra gli altri nodi di lavoro in tre repliche, almeno.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Come evitare che HDInsight si blocca in modalità provvisoria

Esistono diversi modi per impedire che HDInsight venga lasciato in modalità sicura:

* Interrompere tutti i processi Hive prima della riduzione di HDInsight. In alternativa, pianificare il processo di riduzione per evitare conflitti con i processi Hive in esecuzione.
* Pulire manualmente i file temporanei della directory `tmp` di Hive in Hadoop Distributed File System prima della riduzione.
* Ridurre HDInsight solo a tre nodi di lavoro come minimo. Evitare di arrivare a un solo nodo di lavoro.
* Eseguire il comando per disattivare la modalità sicura, se necessario.

Le sezioni seguenti descrivono queste opzioni.

#### <a name="stop-all-hive-jobs"></a>Arrestare tutti i processi Hive

Interrompere tutti i processi Hive prima di eseguire la riduzione a un solo nodo di lavoro. Se il carico di lavoro è pianificato, eseguire la riduzione al termine delle operazioni di Hive.

L'arresto dei processi Hive prima del ridimensionamento consente di ridurre al minimo il numero di file temporanei nella cartella tmp (se presente).

#### <a name="manually-clean-up-hives-scratch-files"></a>Pulire manualmente i file temporanei di Hive

Se sono rimasti file temporanei di Hive, è possibile pulire questi file manualmente prima della riduzione per evitare la modalità sicura.

1. Controllare il percorso usato per i file temporanei Hive esaminando la `hive.exec.scratchdir` proprietà di configurazione. Questo parametro è impostato all'interno di `/etc/hive/conf/hive-site.xml` :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Arrestare i servizi Hive e assicurarsi che tutte le query e i processi siano stati completati.

1. Elencare il contenuto della directory scratch trovata in precedenza per `hdfs://mycluster/tmp/hive/` verificare se contiene file:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Ecco un esempio di output quando sono presenti file:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

1. Se si è certi che questi file non sono più necessari ad Hive, è possibile rimuoverli. Assicurarsi che Hive non abbia query in esecuzione cercando nella pagina yarn Resource Manager'interfaccia utente.

    Riga di comando di esempio per rimuovere file da Hadoop Distributed File System:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Ridimensionare HDInsight in tre o più nodi di lavoro

Se i cluster si bloccano spesso in modalità sicura quando si esegue il ridimensionamento a meno di tre nodi di lavoro, mantenere almeno tre nodi di lavoro.

La presenza di tre nodi di lavoro è più disattesa rispetto alla riduzione a un solo nodo di lavoro. Tuttavia, questa azione impedirà al cluster di rimanere bloccato in modalità sicura.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Ridimensionare HDInsight a un nodo di lavoro

Anche quando il cluster viene ridimensionato a un nodo, il nodo di lavoro 0 continuerà a non essere più disponibile. Il nodo di lavoro 0 non può mai essere ritirato.

#### <a name="run-the-command-to-leave-safe-mode"></a>Eseguire il comando per disattivare la modalità sicura

L'opzione finale è l'esecuzione del comando leave safe mode. Se HDFS è in modalità sicura a causa della replica non riuscita del file Hive, eseguire il comando seguente per uscire dalla modalità sicura:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Ridimensionare un cluster Apache HBase

I server di area vengono bilanciati automaticamente entro pochi minuti dal completamento di un'operazione di ridimensionamento. Per bilanciare manualmente i server di area, seguire questa procedura:

1. Connettersi al cluster HDInsight tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Avviare la shell HBase:

    ```bash
    hbase shell
    ```

3. Usare il comando seguente per bilanciare manualmente i server a livello di area:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Dimensionare automaticamente i cluster Azure HDInsight](hdinsight-autoscale-clusters.md)

Per informazioni specifiche sul ridimensionamento del cluster HDInsight, vedere:

* [Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gestire i Apache Hadoop cluster in HDInsight usando l'interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md#scale-clusters)