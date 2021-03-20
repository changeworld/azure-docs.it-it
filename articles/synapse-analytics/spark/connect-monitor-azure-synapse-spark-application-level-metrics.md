---
title: 'Esercitazione: connettere e monitorare le metriche a livello di applicazione di Azure sinapsi Spark'
description: "Esercitazione: informazioni su come integrare il server Prometeo locale esistente con l'area di lavoro di Azure sinapsi per le metriche dell'applicazione Azure Spark quasi in tempo reale usando il connettore sinapsi Prometheus."
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: cb27401b2925c800ebde3b554c076f0d8cb747ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593017"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>Esercitazione: connettere e monitorare le metriche a livello di applicazione di Azure sinapsi Spark

## <a name="overview"></a>Panoramica

In questa esercitazione si apprenderà come integrare il server Prometeo locale esistente con l'area di lavoro di Azure sinapsi per le metriche dell'applicazione Azure Spark quasi in tempo reale usando il connettore sinapsi Prometheus. 

Questa esercitazione introduce anche le API delle metriche REST di Azure sinapsi. È possibile recuperare i dati della metrica dell'applicazione Spark tramite le API REST per creare un toolkit di monitoraggio e diagnosi o integrarsi con i sistemi di monitoraggio.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Usare il connettore di Azure sinapsi Prometheus per i server Prometeo locali

Il [connettore Azure sinapsi Prometheus](https://github.com/microsoft/azure-synapse-spark-metrics) è un progetto open source. Il connettore sinapsi Prometheus usa un metodo di individuazione del servizio basato su file che consente di:
 - Eseguire l'autenticazione nell'area di lavoro sinapsi tramite un'entità servizio AAD.
 - Recuperare l'area di lavoro Apache Spark elenco di applicazioni. 
 - Estrarre le metriche dell'applicazione Spark tramite la configurazione basata su file Prometheus. 

### <a name="1-prerequisite"></a>1. prerequisito

È necessario avere un server Prometeo distribuito in una macchina virtuale Linux.

### <a name="2-create-a-service-principal"></a>2. creare un'entità servizio

Per usare il connettore Azure sinapsi Prometheus nel server Prometeo locale, attenersi alla procedura seguente per creare un'entità servizio.

#### <a name="21-create-a-service-principal"></a>2,1 creare un'entità servizio:

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Il risultato dovrebbe essere simile al seguente:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Annotare appId, password e ID tenant.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2,2 aggiungere le autorizzazioni corrispondenti all'entità servizio creata nel passaggio precedente.

![schermata Concedi autorizzazione Srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Accedere all'area di [lavoro di Azure sinapsi Analytics](https://web.azuresynapse.net/) come amministratore della sinapsi

2. Nel riquadro sinistro di sinapsi Studio selezionare **Gestisci controllo di accesso >**

3. Fare clic sul pulsante Aggiungi in alto a sinistra per **aggiungere un'assegnazione di ruolo**

4. Per ambito, scegliere **area di lavoro**

5. Per Role scegliere **operatore di calcolo sinapsi**

6. Per selezionare un utente, immettere il **<service_principal_name>** e fare clic sull'entità servizio

7. Fare clic su **applica** (attendere 3 minuti per rendere effettive le autorizzazioni).


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. scaricare il connettore di Azure sinapsi Prometheus

Usare i comandi per installare il connettore di Azure sinapsi Prometheus.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. creare un file di configurazione per le aree di lavoro di sinapsi di Azure

Creare un file config. YAML nella cartella config e compilare i campi seguenti: workspace_name, tenant_id, service_principal_name e service_principal_password.
È possibile aggiungere più aree di lavoro nella configurazione YAML.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. aggiornare la configurazione di Prometeo

Aggiungere la sezione di configurazione seguente nell'scrape_config Prometheus e sostituire il <your_workspace_name> al nome dell'area di lavoro e il <path_to_synapse_connector> alla cartella sinapsi-Prometheus-Connector clonata

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. avviare il connettore nella macchina virtuale del server Prometeo

Avviare un server del connettore nella macchina virtuale del server Prometeo come indicato di seguito.

```
python main.py
```

Attendere alcuni secondi e il connettore dovrebbe iniziare a funzionare. È possibile visualizzare "sinapsi-Prometeo-Connector" nella pagina di individuazione del servizio Prometheus.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Usare le API di Azure sinapsi Prometheus o delle metriche REST per raccogliere dati di metrica

### <a name="1-authentication"></a>1. autenticazione
Per ottenere un token di accesso, è possibile usare il flusso di credenziali client. Per accedere all'API metrica, è necessario ottenere un token di accesso Azure AD per l'entità servizio, che dispone delle autorizzazioni appropriate per accedere alle API.

| Parametro     | Obbligatoria | Descrizione                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | Vero     | ID tenant dell'entità servizio (applicazione) di Azure                                                          |
| grant_type    | Vero     | Specifica il tipo di concessione richiesto. In un flusso di concessione delle credenziali client il valore deve essere client_credentials. |
| client_id     | Vero     | ID dell'applicazione (entità servizio) dell'applicazione registrata in portale di Azure o nell'interfaccia della riga di comando di Azure.        |
| client_secret | Vero     | Il segreto generato per l'applicazione (entità servizio)                                                  |
| risorse      | Vero     | L'URI della risorsa sinapsi dovrebbe essere https://dev.azuresynapse.net                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

La risposta ha un aspetto simile al seguente:

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. elencare le applicazioni in esecuzione nell'area di lavoro sinapsi di Azure

Per ottenere l'elenco delle applicazioni Spark per un'area di lavoro sinapsi, è possibile seguire questo monitoraggio del documento [: ottenere l'elenco dei processi Spark](/rest/api/synapse/data-plane/monitoring/getsparkjoblist).


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. raccogliere le metriche dell'applicazione Spark con le API REST o Prometheus


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Raccogliere le metriche dell'applicazione Spark con l'API Prometheus

Ottenere le metriche più recenti dell'applicazione Spark specificata dall'API Prometheus

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Parametro          | Obbligatoria | Descrizione                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Vero     | L'endpoint di sviluppo dell'area di lavoro, ad esempio https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Vero     | Versione API valida per la richiesta. Attualmente è 2019-11-01-Preview                    |
| sparkPoolName      | Vero     | Nome del pool Spark.                                                                   |
| sessionID          | Vero     | Identificatore per la sessione.                                                               |
| sparkApplicationId | Vero     | ID applicazione Spark                                                                      |

Richiesta di esempio: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Risposta di esempio:

Codice di stato: 200 risposta simile alla seguente:

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>Raccogliere le metriche dell'applicazione Spark con l'API REST

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Parametro          | Obbligatoria | Descrizione                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Vero     | L'endpoint di sviluppo dell'area di lavoro, ad esempio https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Vero     | Versione API valida per la richiesta. Attualmente è 2019-11-01-Preview                    |
| sparkPoolName      | Vero     | Nome del pool Spark.                                                                   |
| sessionID          | Vero     | Identificatore per la sessione.                                                               |
| sparkApplicationId | Vero     | ID applicazione Spark                                                                      |

Richiesta di esempio

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Codice di stato della risposta di esempio: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. creare strumenti di diagnostica e monitoraggio personalizzati

L'API Prometeo e le API REST forniscono dati di metrica avanzati sull'applicazione Spark che esegue le informazioni. È possibile raccogliere i dati di metrica correlati all'applicazione tramite l'API Prometeo e le API REST. E crea strumenti di diagnostica e monitoraggio personalizzati più adatti alle tue esigenze.