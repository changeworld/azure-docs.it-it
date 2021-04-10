---
title: 'Esercitazione: monitorare Apache Spark metrica a livello di applicazione con Prometheus e Grafana'
description: "Esercitazione: informazioni su come distribuire la soluzione Apache Spark metrica dell'applicazione in un cluster Azure Kubernetes Service (AKS) e informazioni su come integrare i dashboard di Grafana."
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 6a0b63dc7fda25e3911ae713a0bea7ae7a0969f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602299"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Esercitazione: monitorare Apache Spark metrica a livello di applicazione con Prometheus e Grafana

## <a name="overview"></a>Panoramica

In questa esercitazione si apprenderà come distribuire la soluzione Apache Spark metrica dell'applicazione in un cluster di Azure Kubernetes Service (AKS) e come integrare i dashboard di Grafana.

È possibile usare questa soluzione per raccogliere ed eseguire query sui dati delle metriche Apache Spark quasi in tempo reale. I dashboard Grafana integrati consentono di diagnosticare e monitorare l'applicazione Apache Spark. Il codice sorgente e le configurazioni sono state open source in GitHub.

## <a name="prerequisites"></a>Prerequisiti

1.  [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
2.  [Client Helm](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Servizio Azure Kubernetes](https://azure.microsoft.com/en-us/services/kubernetes-service/)

In alternativa, usare la [Azure cloud Shell](https://shell.azure.com/), che include già l'interfaccia della riga di comando di Azure, il client Helm e kubectl.

## <a name="log-in-to-azure"></a>Accedere ad Azure

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Creare un'istanza del servizio Kubernetes di Azure (AKS)

Usare il comando dell'interfaccia della riga di comando di Azure per creare un cluster Kubernetes nella sottoscrizione.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Nota: questo passaggio può essere ignorato se si dispone già di un cluster AKS.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Creare un'entità servizio e concedere l'autorizzazione per l'area di lavoro sinapsi

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

Annotare appId, password e tenantID.

[![schermata Concedi autorizzazione Srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Accedere all'area di [lavoro di Azure sinapsi Analytics](https://web.azuresynapse.net/) come amministratore della sinapsi

2. Nel riquadro sinistro di sinapsi Studio selezionare **Gestisci controllo di accesso >**

3. Fare clic sul pulsante Aggiungi in alto a sinistra per **aggiungere un'assegnazione di ruolo**

4. Per ambito, scegliere **area di lavoro**

5. Per Role scegliere **operatore di calcolo sinapsi**

6. Per selezionare un utente, immettere il **<service_principal_name>** e fare clic sull'entità servizio

7. Fare clic su **applica** (attendere 3 minuti per rendere effettive le autorizzazioni).

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Installare il connettore, il server Prometeo, il dashboard di Grafana

1. Aggiungere il repository sinapsi-Charts al client Helm.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Installare i componenti tramite il client Helm:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: nome dell'area di lavoro sinapsi.
 - subscription_id: ID sottoscrizione dell'area di lavoro sinapsi.
 - workspace_resource_group_name: nome del gruppo di risorse dell'area di lavoro sinapsi.
 - tenant_id: ID tenant dell'area di lavoro sinapsi.
 - service_principal_app_id: l'entità servizio "appId"
 - service_principal_password: la password dell'entità servizio creata.

## <a name="log-in-to-grafana"></a>Accedere a Grafana

Ottenere la password e l'indirizzo predefiniti di Grafana. È possibile modificare la password nelle impostazioni di Grafana.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Ottenere l'indirizzo IP del servizio, copiare & incollare l'IP esterno nel browser e accedere con il nome utente "admin" e la password.

## <a name="use-grafana-dashboards"></a>Usare i dashboard di Grafana

Trovare il dashboard di sinapsi nell'angolo superiore sinistro della pagina Grafana (Home-> area di lavoro sinapsi/applicazione sinapsi), provare a eseguire un codice di esempio in sinapsi studio e attendere alcuni secondi per il pull delle metriche.

Per ottenere una panoramica dell'area di lavoro e dei pool di Apache Spark, è anche possibile usare i dashboard "area di lavoro sinapsi/area di lavoro" e "area di lavoro sinapsi/pool Spark".

## <a name="uninstall"></a>Disinstallare

Rimuovere i componenti tramite il comando Helm come indicato di seguito.

```bash
helm delete <release_name> -n <namespace>
```

Eliminare il cluster AKS.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Introduzione ai componenti

Azure sinapsi Analytics fornisce un [grafico Helm](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) basato sull'operatore Prometheus e sul connettore sinapsi Prometheus. Il grafico Helm include i Dashboard Server Prometeo, server Grafana e Grafana per le metriche a livello di applicazione Apache Spark. Puoi usare [Prometheus](https://prometheus.io/), un diffuso sistema di monitoraggio Open Source, per raccogliere queste metriche quasi in tempo reale e usare [Grafana](https://github.com/grafana/grafana) per la visualizzazione.

### <a name="synapse-prometheus-connector"></a>Connettore di sinapsi Prometheus

Il connettore di sinapsi Prometheus consente di connettere il pool di Apache Spark di sinapsi di Azure e il server Prometeo. Implementa:

1.  Autenticazione: è l'autenticazione basata su AAD e può aggiornare automaticamente il token AAD dell'entità servizio per l'individuazione delle applicazioni, l'inserimento di metriche e altre funzioni.
2.  Apache Spark Application Discovery: quando si inviano applicazioni nell'area di lavoro di destinazione, il connettore di sinapsi Prometheus può individuare automaticamente tali applicazioni.
3.  Apache Spark i metadati dell'applicazione: raccoglie le informazioni di base sull'applicazione ed Esporta i dati in Prometeo.

Il connettore di sinapsi Prometheus viene rilasciato come immagine Docker ospitata in [Microsoft container Registry](https://github.com/microsoft/containerregistry). È open source ed è disponibile nelle [metriche dell'applicazione Azure sinapsi Spark](https://github.com/microsoft/azure-synapse-spark-metrics).

### <a name="prometheus-server"></a>Server Prometeo

Prometeo è un toolkit open source di monitoraggio e avviso. Prometeo laureato da cloud native Computing Foundation (CNCF) ed è diventato lo standard de facto per il monitoraggio nativo del cloud. Prometeo può aiutarci a raccogliere, eseguire query e archiviare enormi quantità di dati di serie temporali e può essere facilmente integrato con Grafana. In questa soluzione, il componente Prometheus viene distribuito in base al grafico Helm.

### <a name="grafana-and-dashboards"></a>Grafana e dashboard

Grafana è un software open source per la visualizzazione e l'analisi. Consente di eseguire query, visualizzare, avvisare ed esplorare le metriche. Azure sinapsi Analytics fornisce un set di dashboard Grafana predefiniti per visualizzare Apache Spark metriche a livello di applicazione.

Il dashboard "area di lavoro/area di lavoro sinapsi" fornisce una visualizzazione a livello di area di lavoro di tutti i pool di Apache Spark, i conteggi delle applicazioni, i core CPU e così via.

[![area di lavoro dashboard screenshot](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

Il dashboard "area di lavoro sinapsi/pool Spark" contiene le metriche di Apache Spark le applicazioni in esecuzione nel pool di Apache Spark selezionato durante il periodo di tempo.

[![schermata del dashboard sparkpool](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

Il dashboard "area di lavoro sinapsi/applicazione Spark" contiene l'applicazione Apache Spark selezionata.

[![applicazione Dashboard screenshot](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

I modelli di dashboard precedenti sono stati open source nelle [metriche dell'applicazione Azure sinapsi Spark](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).