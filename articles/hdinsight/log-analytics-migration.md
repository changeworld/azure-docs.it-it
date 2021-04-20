---
title: Eseguire la migrazione dei dati di Log Analytics per Azure HDInsight
description: Informazioni sulle modifiche apportate all'integrazione Monitoraggio di Azure e sulle procedure consigliate per l'uso delle nuove tabelle.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 6659b515ee2d25a4b9136ccfac4cc3444e491438
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741669"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Guida alla migrazione di Log Analytics per Azure HDInsight cluster

 Azure HDInsight è un servizio cluster gestito e pronto per l'azienda. Questo servizio esegue framework di analisi open source come Apache Spark, Hadoop, HBase e Kafka in Azure. Azure HDInsight si è integrato con altri servizi di Azure per consentire ai clienti di gestire meglio le applicazioni di analisi dei Big Data.

Log Analytics offre uno strumento nel portale di Azure per modificare ed eseguire query di log. Le query provengono dai dati raccolti dai log Monitoraggio di Azure e analizzano in modo interattivo i risultati. I clienti possono usare le query di Log Analytics per recuperare i record che soddisfano criteri specifici. Possono anche usare le query per identificare le tendenze, analizzare i modelli e fornire informazioni dettagliate sui dati.

Azure HDInsight'integrazione abilitata con Log Analytics nel 2017. I clienti di HDInsight hanno adottato rapidamente questa funzionalità per monitorare i cluster HDInsight ed eseguire query sui log nei cluster. Anche se l'adozione di questa funzionalità è aumentata, i clienti hanno fornito commenti e suggerimenti sull'integrazione:

- I clienti non possono decidere quali log archiviare e l'archiviazione di tutti i log può diventare costosa.
- I log degli schemi HDInsight correnti non stanno seguendo convenzioni di denominazione coerenti e alcune tabelle sono ripetitive.
- I clienti vogliono un dashboard predefinito per monitorare facilmente l'indicatore KPI dei cluster HDInsight.
- I clienti devono passare a Log Analytics per eseguire query semplici.

## <a name="solution-overview"></a>Panoramica della soluzione

Considerando il feedback dei clienti, il team Azure HDInsight ha investito nell'integrazione con Monitoraggio di Azure. Questa integrazione consente di:

- Nuovo set di tabelle nell'area di lavoro Log Analytics dei clienti. Le nuove tabelle vengono recapitate tramite una nuova pipeline di Log Analytics.
- Maggiore affidabilità
- Recapito dei log più veloce
- Raggruppamento di tabelle basato su risorse e query predefinite

## <a name="benefits-of-the-new-azure-monitor-integration"></a>Vantaggi della nuova integrazione Monitoraggio di Azure dati

Questo documento illustra le modifiche apportate all'integrazione Monitoraggio di Azure e fornisce le procedure consigliate per l'uso delle nuove tabelle.

**Schemi riprogettati:** la formattazione dello schema per la nuova Monitoraggio di Azure integrazione è più organizzata e facile da comprendere. Esistono due terzi di schemi in meno per rimuovere la maggior ambiguità possibile negli schemi legacy.

**Registrazione selettiva (presto disponibile):** sono disponibili log e metriche tramite Log Analytics. Per risparmiare sui costi di monitoraggio, verrà rilasciata una nuova funzionalità di registrazione selettiva. Usare questa funzionalità per attivare e disattivare log e origini metriche diverse. Con questa funzionalità, sarà necessario pagare solo per ciò che si usa.

**Integrazione del portale del cluster di** log: il riquadro Log è una novità del portale del cluster HDInsight.  Chiunque abbia accesso al cluster può passare a questo riquadro per eseguire query su qualsiasi tabella a cui la risorsa cluster invia i record. Gli utenti non devono più accedere all'area di lavoro Log Analytics per visualizzare i record per una risorsa cluster specifica.

**Integrazione del portale cluster di Insights:** anche il **riquadro Informazioni** dettagliate è una novità del portale del cluster HDInsight. Dopo aver abilitato la nuova integrazione Monitoraggio di Azure,  è possibile selezionare il riquadro Informazioni dettagliate e verrà popolato automaticamente un dashboard predefinito per i log e le metriche specifici del tipo di cluster. Questi dashboard sono stati rinnovati dalle soluzioni di Azure precedenti. Forniscono informazioni approfondite sulle prestazioni e sull'integrità del cluster.

**Informazioni dettagliate su larga** scala: è possibile usare la nuova cartella di lavoro Informazioni dettagliate su larga scala nel portale **di Monitoraggio di Azure** per monitorare l'integrità e le prestazioni dei cluster tra sottoscrizioni diverse. 

## <a name="customer-scenarios"></a>Scenari dei clienti

Le sezioni seguenti descrivono come i clienti possono usare la nuova integrazione Monitoraggio di Azure in scenari diversi. La [sezione Activate a new Monitoraggio di Azure integration (Attivare](#activate-a-new-azure-monitor-integration) una nuova integrazione di Monitoraggio di Azure) illustra come attivare e usare il nuovo Monitoraggio di Azure integrazione. La [sezione Migrazione Monitoraggio di Azure classica](#migrate-to-the-new-azure-monitor-integration) alla nuova integrazione Monitoraggio di Azure include informazioni aggiuntive per gli utenti che dipendono dall'integrazione Monitoraggio di Azure precedente.

> [!NOTE]
> Solo i cluster creati alla fine di settembre 2020 e dopo sono idonei per la nuova integrazione di Monitoraggio di Azure.

## <a name="activate-a-new-azure-monitor-integration"></a>Attivare una nuova integrazione Monitoraggio di Azure servizio 

> [!NOTE]
> È necessario avere un'area di lavoro Log Analytics creata in una sottoscrizione a cui si ha accesso prima di abilitare la nuova integrazione. Per altre informazioni su come creare un'area di lavoro Log Analytics, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../azure-monitor/learn/quick-create-workspace.md).

Attivare la nuova integrazione selezionando la pagina del portale del cluster e scorrendo verso il basso il menu a sinistra fino a raggiungere la **sezione** Monitoraggio. Nella sezione **Monitoraggio** selezionare Monitoraggio **integrazione.** Selezionare quindi Abilita **per** scegliere l'area di lavoro Log Analytics a cui inviare i log. Dopo **aver scelto l'area** di lavoro, selezionare Salva. 

### <a name="access-the-new-tables"></a>Accedere alle nuove tabelle

Esistono due modi per accedere alle nuove tabelle. 

### <a name="approach-1"></a>Approccio 1:
Il primo modo per accedere alle nuove tabelle è tramite l'area di lavoro Log Analytics. 

1. Passare all'area di lavoro Log Analytics selezionata quando è stata abilitata l'integrazione. 
2. Scorrere verso il basso nel menu sul lato sinistro della schermata e selezionare **Log**. Verrà visualizzato un editor di query Log con un elenco di tutte le tabelle nell'area di lavoro. 
3. Se le tabelle sono raggruppate per **soluzione**, le nuove tabelle HDI sono nella **sezione Gestione** log. 
4. Se si raggruppano le tabelle in **base al tipo** di risorsa , le tabelle sono nella sezione Cluster **HDInsight,** come illustrato nell'immagine seguente. 

> [!NOTE]
> Questo processo descrive come è stato eseguito l'accesso ai log nell'integrazione precedente. Ciò richiede che l'utente abbia accesso all'area di lavoro.

### <a name="approach-2"></a>Approccio 2:

Il secondo modo per accedere alle nuove tabelle è tramite l'accesso al portale cluster.
 
1. Passare alla pagina del portale del cluster e scorrere verso il basso il menu a sinistra fino a visualizzare la **sezione** Monitoraggio. In questa sezione verrà visualizzato il **riquadro** Log. 
2. Selezionare **Log e** viene visualizzato un editor di query Log. L'editor contiene tutti i log associati alla risorsa cluster. I log sono stati inviati all'area di lavoro Log Analytics quando è stata abilitata l'integrazione. Questi log forniscono l'accesso basato sulle risorse (RBAC). Con il controllo degli accessi in base al ruolo, gli utenti che hanno accesso al cluster ma non all'area di lavoro possono visualizzare i log associati al cluster.

A confronto, gli screenshot seguenti mostrano la visualizzazione dell'area di lavoro di integrazione legacy e la nuova visualizzazione dell'area di lavoro di integrazione:

**Visualizzazione dell'area di lavoro di integrazione legacy**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="Screenshot che mostra la visualizzazione dell'area di lavoro di integrazione legacy." border="false":::

**Nuova visualizzazione dell'area di lavoro di integrazione**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="Screenshot che mostra la nuova visualizzazione dell'area di lavoro di integrazione." border="false":::

### <a name="use-the-new-tables"></a>Usare le nuove tabelle

Queste integrazioni consentono di usare le nuove tabelle:

#### <a name="default-queries-to-use-with-new-tables"></a>Query predefinite da usare con nuove tabelle

Nell'editor di query Log impostare l'interruttore su **Query** sopra l'elenco tabelle. Assicurarsi di raggruppare le query in **base** al tipo di risorsa e che non sia disponibile alcun set di filtri per un tipo di risorsa diverso da **Cluster HDInsight**. L'immagine seguente mostra l'aspetto dei risultati quando vengono raggruppati per **tipo** di risorsa e filtrati per **cluster HDInsight.** È sufficiente selezionarne uno che verrà visualizzato nell'editor di query Log. Assicurarsi di leggere i commenti inclusi nelle query, perché alcuni richiedono l'immissione di alcune informazioni, ad esempio il nome del cluster, per la corretta esecuzione della query.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="Screenshot che mostra il tipo di risorsa raggruppata predefinito dei risultati della query." border="true":::


#### <a name="create-your-own-queries"></a>Creare query personalizzate

È possibile immettere query personalizzate nell'editor di query Log. Le query usate nelle tabelle meno datate non saranno valide nelle nuove tabelle perché molte delle nuove tabelle hanno schemi nuovi e perfezionati. Le query predefinite sono ottimi riferimenti per il data shaping delle query nelle nuove tabelle.

#### <a name="insights"></a>Informazioni dettagliate

Le informazioni dettagliate sono dashboard di visualizzazione specifici del cluster e vengono effettuate usando [Cartelle di lavoro di Azure.](../azure-monitor/platform/workbooks-overview.md) Questi dashboard offrono grafici dettagliati e visualizzazioni della modalità di esecuzione del cluster. I dashboard includono sezioni per ogni tipo di cluster, YARN, metriche di sistema e log dei componenti. È possibile accedere al dashboard del cluster visitando la pagina del cluster  nel portale, scorrendo verso il basso fino alla sezione Monitoraggio e selezionando il **riquadro Informazioni** dettagliate. Il dashboard viene caricato automaticamente se è stata abilitata la nuova integrazione. Consentire il caricamento dei grafici dopo alcuni secondi durante la query dei log.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="Screenshot che mostra il dashboard di visualizzazione.":::

#### <a name="custom-azure-workbooks"></a>Cartelle di lavoro di Azure personalizzate

È possibile creare cartelle di lavoro di Azure personalizzate con grafici e visualizzazioni personalizzati. Nella pagina del portale del cluster  scorrere verso il  basso fino alla sezione Monitoraggio e selezionare il riquadro Cartelle di lavoro nel menu a sinistra. È possibile iniziare a usare un modello vuoto o usare uno dei modelli nella sezione **Cluster HDInsight.** È presente un modello per ogni tipo di cluster. I modelli sono utili se si vogliono salvare personalizzazioni specifiche non fornite da HDInsight Insights predefinite. È possibile inviare richieste di nuove funzionalità in HDInsight Insights se si è a conoscenza di qualcosa.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>Cartelle di lavoro su larga scala per nuove Monitoraggio di Azure di lavoro

Usare la nuova cartella di lavoro su larga scala per ottenere un'esperienza di monitoraggio multi-cluster per i cluster. La cartella di lavoro su larga scala mostra i cluster in cui è abilitata la pipeline di monitoraggio. La cartella di lavoro offre anche un modo semplice per controllare l'integrità di più cluster contemporaneamente. Per visualizzare questa cartella di lavoro:

1. Passare alla pagina **Monitoraggio di Azure** in dal portale di Azure home page
2. Una volta nella **pagina Monitoraggio di Azure,** selezionare **Hub di Insights** nella **sezione** Informazioni dettagliate.
3. Selezionare **Cluster HDInsight nella** sezione **Analisi.**

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="Screenshot che mostra la cartella di lavoro su larga scala." border="false":::

#### <a name="alerts"></a>Avvisi

È possibile aggiungere avvisi personalizzati ai cluster e alle aree di lavoro nell'editor di query log. Passare all'editor di query Log selezionando **il** riquadro Log nel portale del cluster o dell'area di lavoro. Eseguire una query e quindi selezionare **Nuova regola di avviso,** come illustrato nello screenshot seguente. Per altre informazioni, vedere La [configurazione degli avvisi](../azure-monitor/platform/alerts-log.md).

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="Screenshot che mostra il nuovo avviso della regola." border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>Eseguire la migrazione alla nuova Monitoraggio di Azure integration

Se si usa l'integrazione Monitoraggio di Azure classica, è necessario apportare alcune modifiche ai nuovi formati di tabella dopo il passaggio alla nuova Monitoraggio di Azure integrazione.

Per abilitare la nuova integrazione Monitoraggio di Azure, seguire i passaggi descritti nella sezione Attivare una nuova Monitoraggio di Azure [di integrazione.](#activate-a-new-azure-monitor-integration)

### <a name="run-queries-in-log-analytics"></a>Eseguire query in Log Analytics

Poiché il nuovo formato di tabella è diverso da quello precedente, le query devono essere rielaborate per poter usare le nuove tabelle. Dopo aver abilitato la nuova Monitoraggio di Azure, è possibile esplorare le tabelle e gli schemi per identificare i campi usati nelle query precedente.

Viene specificata una [tabella di mapping](#appendix-table-mapping) tra la tabella precedente e la nuova tabella per trovare rapidamente i nuovi campi da usare per eseguire la migrazione di dashboard e query.

**Query predefinite:** sono state create query predefinite che mostrano come usare le nuove tabelle per situazioni comuni. Le query predefinite mostrano anche le informazioni disponibili in ogni tabella. È possibile accedere alle query predefinite seguendo le istruzioni riportate nella [sezione Query predefinite da usare con le nuove](#default-queries-to-use-with-new-tables) tabelle di questo articolo.

### <a name="update-dashboards-for-hdinsight-clusters"></a>Aggiornare i dashboard per i cluster HDInsight

Se sono stati creati più dashboard per monitorare i cluster HDInsight, è necessario modificare la query alla base della tabella dopo aver abilitato il nuovo Monitoraggio di Azure integrazione. Il nome della tabella o il nome del campo potrebbe cambiare nella nuova integrazione, ma tutte le informazioni presenti nell'integrazione precedente sono incluse.

Fare riferimento alla [tabella di mapping](#appendix-table-mapping) tra la tabella/schema precedente e la nuova tabella/schema per aggiornare la query dietro i dashboard.

#### <a name="out-of-box-dashboards"></a>Dashboard out-of-box 

Sono stati anche migliorati i dashboard out-of-box a livello di cluster. Nella parte superiore destra di ogni grafico è presente un pulsante che consente di visualizzare la query sottostante che produce le informazioni. Il grafico è un ottimo modo per acquisire familiarità con il modo in cui è possibile eseguire query sulle nuove tabelle in modo efficace. È possibile accedere ai dashboard [out-of-box](#at-scale-workbooks-for-new-azure-monitor-integrations) seguendo le istruzioni disponibili nelle sezioni Informazioni [dettagliate](#insights) e Cartelle di lavoro su larga scala per le nuove integrazioni Monitoraggio di Azure dati.

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>Usare un dashboard di monitoraggio su larga scala di HDInsight

Se si usa il dashboard di monitoraggio predefinito per i cluster HDInsight, ad esempio il monitoraggio spark di HDInsight e il monitoraggio interattivo di HDInsight, microsoft sta lavorando per offrire le stesse funzionalità nel portale Monitoraggio di Azure.

Si noti che è disponibile un'opzione cluster HDInsight in Monitoraggio di Azure.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Screenshot che mostra l'opzione HDInsight in Monitoraggio di Azure." border="false":::

L Monitoraggio di Azure hub insights del portale offre la possibilità di monitorare più cluster HDInsight in un'unica posizione. I cluster vengono organizzati in base al tipo di carico di lavoro, in modo da visualizzare tipi come Spark, HBase e Hive. Invece di accedere a più dashboard, è ora possibile monitorare tutti i cluster HDInsight in questa visualizzazione.

> [!NOTE]
> Per altre informazioni, vedere le sezioni [Informazioni dettagliate](#insights) e Su larga scala per le nuove Monitoraggio di Azure [integrazioni](#at-scale-workbooks-for-new-azure-monitor-integrations) in questo articolo.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>Abilitare entrambe le integrazioni per accelerare la migrazione

È possibile attivare contemporaneamente le integrazioni di Monitoraggio di Azure classiche e nuove in un cluster idoneo per entrambe le integrazioni per la migrazione alla nuova Monitoraggio di Azure integrazione. La nuova integrazione è disponibile per tutti i cluster creati dopo metà settembre 2020.

In questo modo, è possibile eseguire facilmente un confronto side-by-side per le query in uso.

### <a name="enabling-the-classic-integration"></a>Abilitazione dell'integrazione classica

Se si usa un cluster creato dopo metà settembre 2020, nel portale del cluster verrà visualizzata la nuova esperienza del portale. Per abilitare la nuova pipeline, è possibile seguire i passaggi descritti nella sezione Attivare un nuovo [Monitoraggio di Azure integrazione.](#activate-a-new-azure-monitor-integration) Per attivare l'integrazione classica in questo cluster, passare alla pagina del portale del cluster. Selezionare il **riquadro Di integrazione** monitoraggio nella sezione **Monitoraggio** del menu sul lato sinistro della pagina del portale del cluster. Selezionare **Configura Monitoraggio di Azure per l'integrazione dei cluster HDInsight (versione classica).** Viene visualizzato un contesto laterale con un interruttore che è possibile usare per abilitare e disabilitare l'integrazione classica di Monitoraggio di Azure. 

> [!NOTE]
> Non verrà visualizzato alcun log o metrica dall'integrazione classica tramite la pagina dei log e delle informazioni dettagliate del portale cluster. In tali posizioni saranno presenti solo i log e le metriche delle nuove integrazioni.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="Screenshot che mostra il collegamento per accedere all'integrazione classica." border="false":::

Dopo il 1° luglio 2021, la creazione di nuovi cluster con integrazione Monitoraggio di Azure classica non è disponibile.

## <a name="release-and-support-timeline"></a>Sequenza temporale di rilascio e supporto

- L'integrazione classica di Monitoraggio di Azure non sarà disponibile dopo il 15 ottobre 2021. Non è possibile abilitare l'integrazione classica di Monitoraggio di Azure dopo tale data.
- Le integrazioni di monitoraggio classico di Azure esistenti continueranno a funzionare. Il supporto per l'integrazione classica di Monitoraggio di Azure sarà limitato. 
  - I problemi verranno esaminati dopo che i clienti hanno inviato il ticket di supporto.
  - Se la soluzione richiede la modifica dell'immagine, i clienti devono passare alla nuova integrazione.
  - Non si applizzerà la patch ai cluster di integrazione di Monitoraggio di Azure classici, ad eccezione dei problemi di sicurezza critici.

## <a name="appendix-table-mapping"></a>Appendice: Mapping delle tabelle

I grafici seguenti illustrano i mapping delle tabelle dall'integrazione classica di Monitoraggio di Azure a quella nuova. La **colonna Carico** di lavoro descrive il carico di lavoro a cui è associata ogni tabella. La **riga Nuova** tabella mostra il nome della nuova tabella. La **riga** Descrizione descrive il tipo di log/metriche che saranno disponibili in questa tabella. La **riga Tabella** precedente è un elenco di tutte le tabelle dell'integrazione Monitoraggio di Azure classica i cui dati saranno ora presenti nella tabella elencata nella riga **Nuova** tabella.

> [!NOTE]
> Alcune tabelle sono nuove e non si basano sulle tabelle meno datate.

## <a name="general-workload-tables"></a>Tabelle generali del carico di lavoro

| Nuova tabella | Dettagli |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**Descrizione:** questa tabella contiene le metriche di sistema raccolte da Ambari. Le metriche provengono ora da ogni nodo del cluster (ad eccezione dei nodi perimetrali) anziché solo dai due nodi head. Ogni metrica è ora una colonna e ogni metrica viene segnalata una volta per ogni record.</li><li>**Tabella precedente:** metrics cpu nice cl, metrics cpu system cl, metrics cpu user cl, metrics memory cache CL, metrics memory swap CL, metrics memory swap CL, metrics \_ \_ memory \_ \_ \_ \_ \_ \_ total \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ CLmetrics \_ memory buffer \_ \_ \_ CL, metrics load \_ 1min \_ CL, metrics load cpu \_ \_ \_ CL, metrics load nodes \_ \_ \_ CL, metrics load \_ procs CL, metrics load \_ procs \_ CL, metrics network in \_ \_ \_ CL, metrics network out \_ \_ \_ CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**Descrizione:** questa tabella contiene gli avvisi del cluster Ambari da ogni nodo del cluster (ad eccezione dei nodi perimetrali). Ogni avviso è un record in questa tabella.</li><li>**Tabella precedente:** avvisi \_ del cluster di metriche \_ \_ CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**Descrizione:** questa tabella contiene i record dei log di controllo e autenticazione di Ambari.</li><li>**Tabella precedente:** log \_ ambari \_ audit \_ CL, log \_ auth \_ CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**Descrizione:** questa tabella contiene tutti i record del log di controllo di Ranger per i cluster ESP.</li><li>**Tabella precedente:** log di \_ controllo ranger \_ \_ CL</li></ul>|
| HDInsightGatewayAuditLogs \_ CL | <ul><li>**Descrizione:** questa tabella contiene le informazioni di controllo dei nodi del gateway. È lo stesso formato della tabella nella colonna Tabelle vecchie. **Si trova ancora nella sezione Log personalizzati.**</li><li>**Tabella precedente:** log \_ gateway Audit \_ \_ CL</li></ul>|

## <a name="spark-workload"></a>Carico di lavoro Spark

> [!NOTE]
> Le tabelle correlate all'applicazione Spark sono state sostituite con 11 nuove tabelle Spark (a partire da HDInsightSpark*) che offrono informazioni più dettagliate sui carichi di lavoro Spark.


| Nuova tabella | Dettagli |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**Descrizione:** questa tabella contiene tutti i log correlati a Spark e al relativo componente: Livy e Jupyter.</li><li>**Tabella precedente:** log \_ livy, \_ CL, log \_ jupyter \_ CL, log \_ spark \_ CL, log \_ sparkappsexecutors \_ CL, log \_ sparkappsdrivers \_ CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sugli eventi per le applicazioni Spark, tra cui l'ora di invio e completamento, l'ID app e il nome dell'app. È utile per tenere traccia dell'avvio e del completamento delle applicazioni. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sull'evento correlate a Gestione blocchi di Spark. Include informazioni come l'utilizzo della memoria dell'executor.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sugli eventi correlati all'ambiente in cui viene eseguita un'applicazione, tra cui modalità di distribuzione Spark, master e informazioni sull'executor.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sugli eventi relativi all'utilizzo dell'executor Spark per da parte di un'applicazione.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sull'evento che non rientrano in altre tabelle spark. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sui processi Spark, inclusi l'ora di inizio e di fine, il risultato e le fasi associate.</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sugli eventi nelle query SPARK SQL, incluse le informazioni sul piano e la descrizione e le ore di inizio e fine.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sugli eventi per le fasi Spark, inclusi gli orari di inizio e di completamento, lo stato dell'errore e informazioni dettagliate sull'esecuzione.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**Descrizione:** questa tabella contiene le metriche delle prestazioni per fasi e attività.</li></ul>|
| HDInsightTaskEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sugli eventi per attività Spark, tra cui ora di inizio e completamento, fasi associate, stato di esecuzione e tipo di attività.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**Descrizione:** questa tabella contiene informazioni sugli eventi per Jupyter Notebooks.</li></ul>|

## <a name="hadoopyarn-workload"></a>Carico di lavoro Hadoop/YARN

| Nuova tabella | Dettagli |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**Descrizione:** questa tabella contiene le metriche JMX dei framework Hadoop e YARN. Contiene tutte le stesse metriche JMX delle tabelle dei log personalizzati meno importanti, oltre a altre metriche considerate importanti. Sono state aggiunte le metriche Server sequenza temporale, Gestione nodi e Server cronologia processi. Contiene una metrica per ogni record.</li><li>**Tabella precedente:** metriche \_ resourcemanager \_ clustermetrics CL, metrics \_ \_ resourcemanager jvm CL, metrics resourcemanager queue root \_ \_ \_ \_ \_ \_ CL, metrics \_ resourcemanager \_ queue root \_ \_ joblauncher \_ CL, metrics \_ resourcemanager queue root default \_ \_ \_ \_ CL, metrics \_ resourcemanager queue root \_ \_ \_ thriftsvr \_ CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**Descrizione:** questa tabella contiene tutti i log generati dai framework Hadoop e YARN.</li><li>**Tabella precedente:** log \_ mrjobsummary \_ CL, log \_ resourcemanager \_ CL, log \_ timelineserver \_ CL, log \_ nodemanager \_ CL</li></ul>|

 
## <a name="hivellap-workload"></a>Carico di lavoro Hive/LLAP 

| Nuova tabella | Dettagli |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**Descrizione:** questa tabella contiene le metriche JMX dei framework Hive e LLAP. Contiene tutte le stesse metriche JMX delle tabelle dei log personalizzati. Contiene una metrica per ogni record.</li><li>**Tabella** precedente: \_ llap metrics \_ hiveserver2 \_ CL, llap \_ metrics \_ hs2 \_ metrics \_ subsystemllap \_ metrics \_ jvm \_ CL, llap \_ metrics \_ llap \_ daemon \_ info \_ CL, llap \_ metrics metrics \_ \_ allocator info \_ \_ CL, llap \_ metrics \_ deamon \_ jvm \_ CL, llap \_ metrics io \_ \_ CL, llap \_ metrics \_ executor metrics \_ \_ CL, llap \_ metrics \_ metricssystem \_ stats \_ CL, llap \_ metrics cache \_ \_ CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**Descrizione:** questa tabella contiene i log generati da Hive, LLAP e i relativi componenti: WebHCat e Zeppelin.</li><li>**Tabella precedente:** log \_ hivemetastore \_ CL log \_ hiveserver2 \_ CL, log \_ hiveserve2interactive \_ CL, log \_ webhcat \_ CL, log \_ \_ zeppelin zeppelin \_ CL</li></ul>|


## <a name="kafka-workload"></a>Carico di lavoro Kafka

| Nuova tabella | Dettagli |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**Descrizione:** questa tabella contiene le metriche JMX di Kafka. Contiene tutte le stesse metriche JMX delle tabelle dei log personalizzati più altre metriche considerate importanti. Contiene una metrica per ogni record.</li><li>**Tabella precedente:** metriche \_ kafka \_ CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**Descrizione:** questa tabella contiene tutti i log generati dai broker Kafka.</li><li>**Tabella precedente:** log \_ kafkaserver \_ CL, log \_ kafkacontroller \_ CL</li></ul>|

## <a name="hbase-workload"></a>Carico di lavoro HBase

| Nuova tabella | Dettagli |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**Descrizione:** questa tabella contiene le metriche JMX di HBase. Contiene tutte le stesse metriche JMX delle tabelle elencate nella colonna Schema precedente. A differenza delle tabelle precedente, ogni riga contiene una metrica.</li><li>Tabella **precedente:** metrics \_ regionserver \_ CL, metrics \_ regionserver wal \_ \_ CL, metrics \_ regionserver \_ ipc \_ CL, metrics \_ regionserver \_ os \_ CL, metrics \_ regionserver replication CL, metrics \_ \_ restserver CL, metrics \_ \_ \_ restserver \_ jvm \_ CL, metrics \_ hmaster \_ assignmentmanager \_ CL, metrics \_ hmaster \_ ipc CL, metrics \_ \_ hmaser \_ os \_ CL, metrics \_ hmaster balancer \_ \_ CL, metrics \_ hmaster \_ jvm \_ CL, metrics \_ hmaster \_ CL, metrics \_ hmaster \_ \_ CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>**Descrizione:** questa tabella contiene i log di HBase e i relativi componenti correlati: Phoenix e HDFS.</li><li>**Tabella precedente:** log \_ regionserver \_ CL, log \_ restserver \_ CL, log \_ phoenixserver \_ CL, log \_ hmaster \_ CL, log \_ hdfsnamenode \_ CL, \_ \_ \_ CL del Garbage Collector di log</li></ul>|

## <a name="storm-workload"></a>Carico di lavoro Storm

| Nuova tabella | Dettagli |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**Descrizione:** questa tabella contiene le stesse metriche JMX delle tabelle nella sezione Tabelle vecchie. Le righe contengono una metrica per ogni record.</li><li>**Tabella precedente:** metriche \_ stormnimbus \_ CL, \_ metriche stormsupervisor \_ CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**Descrizione:** questa tabella contiene le metriche a livello di topologia di Storm. Si tratta della stessa forma della tabella elencata nella sezione Tabelle vecchie.</li><li>**Tabella precedente:** metriche \_ stormrest \_ CL</li></ul>|
| HDInsightStormLogs | <ul><li>**Descrizione:** questa tabella contiene tutti i log generati da Storm.</li><li>**Tabella precedente:** CL \_ del \_ supervisore del log, log \_ nimbus \_ CL</li></ul>|

## <a name="oozie-workload"></a>Carico di lavoro Oozie

| Nuova tabella | Dettagli |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**Descrizione:** questa tabella contiene tutti i log generati dal framework Oozie.</li><li>**Tabella precedente:** Log \_ oozie \_ CL</li></ul>|

## <a name="next-steps"></a>Passaggi successivi
[Eseguire query nei log di Monitoraggio di Azure per monitorare i cluster HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
