---
title: Usare Log Analytics di Azure per raccogliere e visualizzare le metriche e i log (anteprima)
description: Informazioni su come abilitare il connettore di Azure Log Analytics incorporato di sinapsi per la raccolta e l'invio delle metriche e dei log dell'applicazione Apache Spark all'area di lavoro di Azure Log Analytics.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108551"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Esercitazione: usare Log Analytics di Azure per raccogliere e visualizzare le metriche e i log (anteprima)

In questa esercitazione si apprenderà come abilitare il connettore di Azure Log Analytics incorporato di sinapsi per la raccolta e l'invio delle metriche e dei log dell'applicazione Apache Spark all' [area di lavoro di azure log Analytics](/azure/azure-monitor/logs/quick-create-workspace). È quindi possibile utilizzare una cartella di lavoro di monitoraggio di Azure per visualizzare le metriche e i log.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Configurare le informazioni sull'area di lavoro di Azure Log Analytics in sinapsi Studio

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>Passaggio 1: creare un'area di lavoro di Azure Log Analytics

Per creare un'area di lavoro Log Analytics è possibile seguire i documenti seguenti:
- [Creare un'area di lavoro Log Analytics nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Creare un'area di lavoro Log Analytics con interfaccia della riga di comando](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [Creare e configurare un'area di lavoro Log Analytics in Monitoraggio di Azure usando PowerShell](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>Passaggio 2: preparare un file di configurazione Spark

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>Opzione 1. Configurare con la chiave e l'ID dell'area di lavoro Log Analytics di Azure 

Copiare la configurazione di Spark seguente, salvarla come **"spark_loganalytics_conf.txt"** e compilare i parametri:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: ID dell'area di lavoro Log Analytics di Azure.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Chiave di Log Analytics di Azure: **portale di Azure > azure log Analytics area di lavoro > gli agenti gestione > chiave primaria**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>Opzione 2. Configurare con un Azure Key Vault

> [!NOTE]
>
> È necessario concedere l'autorizzazione di lettura segreta agli utenti che invieranno le applicazioni Spark. Vedere [fornire l'accesso alle chiavi Key Vault, ai certificati e ai segreti con un controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Per configurare un Azure Key Vault per archiviare la chiave dell'area di lavoro, attenersi alla procedura seguente:

1. Creare e passare all'insieme di credenziali delle chiavi nella portale di Azure
2. Nella pagina di impostazioni di Key Vault selezionare **Segreti**.
3. Fare clic su **Genera/Importa**.
4. Nella schermata **Crea un segreto** selezionare i seguenti valori:
   - **Nome**: digitare un nome per il segreto, digitare `"SparkLogAnalyticsSecret"` come predefinito.
   - **Valore**: digitare il **>di LOG_ANALYTICS_WORKSPACE_KEY<** per il segreto.
   - Lasciare invariati gli altri valori predefiniti. Fare clic su **Crea**.
5. Copiare la configurazione di Spark seguente, salvarla come **"spark_loganalytics_conf.txt"** e compilare i parametri:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: ID dell'area di lavoro Log Analytics di Azure.
   - `<AZURE_KEY_VAULT_NAME>`: Nome del Azure Key Vault configurato.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (Facoltativo): nome del segreto nella Azure Key Vault per la chiave dell'area di lavoro, valore predefinito: "SparkLogAnalyticsSecret".

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> È anche possibile archiviare l'ID dell'area di lavoro Log Analytics nell'insieme di credenziali delle chiavi di Azure. Vedere i passaggi precedenti e archiviare l'ID dell'area di lavoro con il nome del segreto `"SparkLogAnalyticsWorkspaceId"` . In alternativa, usare il file config `spark.synapse.logAnalytics.keyVault.key.workspaceId` per specificare il nome del segreto dell'ID dell'area di lavoro in Azure Key Vault.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>Opzione 3. Configurare con un servizio collegato Azure Key Vault

> [!NOTE]
>
> È necessario concedere l'autorizzazione di lettura del segreto all'area di lavoro sinapsi. Vedere [fornire l'accesso alle chiavi Key Vault, ai certificati e ai segreti con un controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Per configurare un servizio collegato Azure Key Vault in sinapsi Studio per archiviare la chiave dell'area di lavoro, seguire questa procedura:

1. Seguire tutti i passaggi nella `Option 2. Configure with an Azure Key Vault` sezione.
2. Creare un servizio collegato di Azure Key Vault in sinapsi Studio:

    a. Passare a **sinapsi Studio > gestire > servizi collegati** e fare clic su **nuovo** .

    b. Cercare **Azure Key Vault** nella casella di ricerca.

    c. Digitare un nome per il servizio collegato.

    d. Scegliere l'insieme di credenziali delle chiavi di Azure. Fare clic su **Crea**.

3. Aggiungere un `spark.synapse.logAnalytics.keyVault.linkedServiceName` elemento alla configurazione di Spark.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Configurazione di Spark disponibile

| Nome della configurazione                                  | Valore predefinito                | Descrizione                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Spark. sinapsi. logAnalytics. Enabled                  | false                        | Per abilitare il sink di Azure Log Analytics per le applicazioni Spark, true. In caso contrario, false.                                                                                                                  |
| Spark. sinapsi. logAnalytics. workspaceId              | -                            | ID dell'area di lavoro di Azure Log Analytics di destinazione                                                                                                                                                          |
| Spark. sinapsi. logAnalytics. Secret                   | -                            | Il segreto dell'area di lavoro di Azure Log Analytics di destinazione.                                                                                                                                                      |
| Spark. sinapsi. logAnalytics. Vault. linkedServiceName   | -                            | Nome del servizio collegato di Azure Key Vault per l'ID e la chiave dell'area di lavoro di Azure Log Analytics                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Nome dell'insieme di credenziali delle chiavi di Azure per l'ID e la chiave di Azure Log Analytics                                                                                                                                                |
| Spark. sinapsi. logAnalytics. Key Vault. Key. workspaceId | SparkLogAnalyticsWorkspaceId | Nome del segreto di Azure Key Vault per l'ID dell'area di lavoro di Azure Log Analytics                                                                                                                                       |
| Spark. sinapsi. logAnalytics. Key Vault. Key. Secret      | SparkLogAnalyticsSecret      | Nome del segreto di Azure Key Vault per la chiave dell'area di lavoro di Azure Log Analytics                                                                                                                                      |
| Spark. sinapsi. logAnalytics. Vault. uriSuffix       | ods.opinsights.azure.com     | [Suffisso URI][uri_suffix]dell'area di lavoro di Azure log Analytics di destinazione. Se l'area di lavoro di Azure Log Analytics non è in Azure globale, è necessario aggiornare il suffisso URI in base al rispettivo cloud. |

> [!NOTE]  
> - Per i cloud di Azure Cina, il parametro "Spark. sinapsi. logAnalytics. uriSuffix" deve essere "ods.opinsights.azure.cn". 
> - Per i cloud di Azure gov, il parametro "Spark. sinapsi. logAnalytics. uriSuffix" deve essere "ods.opinsights.azure.us". 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>Passaggio 3: caricare la configurazione di Spark in un pool Spark
È possibile caricare il file di configurazione nel pool di Spark di sinapsi in sinapsi Studio.

   1. Passare al pool di Apache Spark in Azure sinapsi Studio (Manage-> Apache Spark Pools)
   2. Fai clic sul pulsante **"..."** a destra del pool di Apache Spark
   3. Seleziona configurazione Apache Spark 
   4. Fare clic su **carica** e scegliere il **"spark_loganalytics_conf.txt"** creato.
   5. Fare clic su **carica** e **applica**.

      > [!div class="mx-imgBorder"]
      > ![Configurazione pool Spark](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Tutte le applicazioni Spark inviate al pool Spark precedente useranno l'impostazione di configurazione per eseguire il push delle metriche e dei log dell'applicazione Spark nell'area di lavoro di Azure Log Analytics specificata.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Inviare un'applicazione Spark e visualizzare i log e le metriche in Azure Log Analytics

 1. È possibile inviare un'applicazione Spark al pool Spark configurato nel passaggio precedente, usando uno dei modi seguenti:
    - Eseguire un notebook di sinapsi Studio. 
    - Inviare una sinapsi Apache Spark processo batch con la definizione del processo Spark.
    - Eseguire una pipeline che contiene l'attività Spark.

 2. Passare all'area di lavoro di Azure Log Analytics specificata, quindi visualizzare le metriche e i log dell'applicazione quando l'applicazione Spark inizia a essere eseguita.

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>Usare la cartella di lavoro di Azure Log Analytics di esempio per visualizzare le metriche e i log

1. [Scaricare la cartella di lavoro](https://aka.ms/SynapseSparkLogAnalyticsWorkbook) qui.
2. Aprire e **copiare** il contenuto del file della cartella di lavoro.
3. Passare alla cartella di lavoro di Log Analytics di Azure ([portale di Azure](https://portal.azure.com/) > le cartelle di lavoro log Analytics >
4. Aprire la cartella di lavoro di Azure Log Analytics **"vuota"** , in modalità **"Editor avanzato"** (premere l'icona </>).
5. **Incollare** qualsiasi JSON esistente.
6. Quindi, fare clic su **applica** e quindi su **modifica**.

    > [!div class="mx-imgBorder"]
    > ![nuova cartella di lavoro](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![Importa cartella di lavoro](./media/apache-spark-azure-log-analytics/import-workbook.png)

Inviare quindi l'applicazione Apache Spark al pool Spark configurato. Quando l'applicazione passa allo stato in esecuzione, scegliere l'applicazione in esecuzione nell'elenco a discesa cartella di lavoro.

> [!div class="mx-imgBorder"]
> ![imange cartella di lavoro](./media/apache-spark-azure-log-analytics/workbook.png)

È possibile personalizzare la cartella di lavoro tramite kusto query e configurare gli avvisi.

> [!div class="mx-imgBorder"]
> ![query e avvisi di Kusto](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Query Kusto di esempio

1. Esempio di eventi Spark di query.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Esempio di log del driver dell'applicazione Spark e degli Executor.

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Esempio di metrica per la query Spark.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Creare e gestire avvisi con Azure Log Analytics

Gli avvisi di monitoraggio di Azure consentono agli utenti di usare una query Log Analytics per valutare le metriche e registrare ogni frequenza impostata e generare un avviso in base ai risultati.

Per altre informazioni, vedere [creare, visualizzare e gestire gli avvisi dei log con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="limitation"></a>Limitazione

 - L'area di lavoro di Azure sinapsi Analytics con [rete virtuale gestita](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet) abilitata non è supportata.
 - Le aree seguenti non sono attualmente supportate:
   - Stati Uniti orientali 2
   - Norvegia orientale
   - Emirati Arabi Uniti settentrionali

## <a name="next-steps"></a>Passaggi successivi

 - Informazioni su come [usare il pool di Apache Spark senza server in sinapsi Studio](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio).
 - Informazioni su come [eseguire un'applicazione Spark nel notebook](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks).
 - Informazioni su come [creare Apache Spark definizione del processo in sinapsi Studio](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions).