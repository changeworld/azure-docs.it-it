---
title: Visualizzare i log del controller del servizio Azure Kubernetes
description: Informazioni su come abilitare e visualizzare i log per il piano di controllo Kubernetes in servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 395422ca97b43488a7d7ad1c7434b20ccc59f2b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767292"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Abilitare ed esaminare i log del piano di controllo Kubernetes in servizio Azure Kubernetes (AKS)

Con servizio Azure Kubernetes(AKS), i componenti del piano di controllo, ad esempio *kube-apiserver* e *kube-controller-manager,* vengono forniti come servizio gestito. Si creano e si gestiscono i nodi che eseguono *kubelet* e il runtime del contenitore e si distribuiscono le applicazioni attraverso il server API Kubernetes gestito. Per risolvere i problemi relativi all'applicazione e ai servizi, potrebbe essere necessario visualizzare i log generati da questi componenti del piano di controllo. Questo articolo illustra come usare i log Monitoraggio di Azure per abilitare ed eseguire query nei log dai componenti del piano di controllo Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede un cluster del servizio Azure Kubernetes esistente in esecuzione nel proprio account di Azure. Se non si dispone ancora di un cluster del servizio Azure Kubernetes, crearne uno usando l'[interfaccia della riga di comando di Azure][cli-quickstart] oppure il [portale di Azure][portal-quickstart]. Monitoraggio di Azure i log funzionano con il controllo degli accessi in base al ruolo di Kubernetes, il controllo degli accessi in base al ruolo di Azure e i cluster del servizio Azure Kubernetes abilitati per il controllo degli accessi in base al ruolo.

## <a name="enable-resource-logs"></a>Abilitare i log risorse

Per raccogliere e rivedere i dati da più origini, i log di Monitoraggio di Azure forniscono un linguaggio di query e un motore di analisi che offrono informazioni dettagliate per l'ambiente in uso. Viene usata un'area di lavoro per collazionare e analizzare i dati che possa integrarsi con altri servizi di Azure, ad esempio Application Insights e Centro sicurezza. Per usare una piattaforma diversa per analizzare i log, è invece possibile scegliere di inviare i log delle risorse a un account di archiviazione di Azure o a un hub eventi. Per altre informazioni, vedere [Analizzare i dati di log in Monitoraggio di Azure][log-analytics-overview].

Monitoraggio di Azure i log vengono abilitati e gestiti nel portale di Azure. Per abilitare la raccolta di log per i componenti del piano di controllo Kubernetes nel cluster del servizio Kubernetes, aprire il portale di Azure in un Web browser e completare la procedura seguente:

1. Selezionare il gruppo di risorse per il cluster servizio Azure Kubernetes, ad esempio *myResourceGroup*. Non selezionare il gruppo di risorse che contiene le singole risorse del cluster servizio Azure Kubernetes, ad esempio *MC_myResourceGroup_myservizio Azure KubernetesCluster_eastus*.

2. Sul lato sinistro, scegliere **Impostazioni di diagnostica**.

3. Selezionare il cluster del servizio AKS, ad esempio *myAKSCluster,* quindi scegliere **Aggiungi impostazione di diagnostica.**
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="Screenshot della portale di Azure in una finestra del browser che mostra le impostazioni di diagnostica, che indicano che è necessario selezionare 'Aggiungi impostazione di diagnostica'":::

4. Immettere un nome, ad esempio *myAKSClusterLogs*, quindi selezionare l'opzione **Send to Log Analytics workspace** (Invia ad area di lavoro Log Analytics).

5. Selezionare un'area di lavoro esistente o crearne una nuova. Se si crea un'area di lavoro, specificare un nome dell'area di lavoro, un gruppo di risorse e una posizione.

6. Nell'elenco dei log disponibili selezionare i log che si desidera abilitare. Per questo esempio, abilitare i *log kube-audit* e *kube-audit-admin.* I log comuni includono *kube-apiserver*, *kube-controller-manager* e *kube-scheduler*. È possibile restituire e modificare i log raccolti dopo l'abilitazione delle aree di lavoro di Log Analytics.

7. Quando si è pronti, selezionare **Salva** per abilitare la raccolta dei log selezionati.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Screenshot della schermata portale di Azure 'Aggiungi impostazione di diagnostica' di portale di Azure. È selezionata la destinazione &quot;Invia all'area di lavoro Log Analytics&quot; e i log &quot;kube-audit&quot; e &quot;kube-audit-admin&quot; sono selezionati":::

## <a name="log-categories"></a>Categorie di log

Oltre alle voci scritte da Kubernetes, i log di controllo del progetto hanno anche voci dal servizio Web di servizio Web.

I log di controllo vengono registrati in tre categorie: *kube-audit*, *kube-audit-admin* e *guard*.

- La *categoria kube-audit* contiene tutti i dati del log di controllo per ogni evento di controllo, inclusi *get*, *list*, *create,* *update,* *delete,* *patch* e *post*.
- La *categoria kube-audit-admin* è un subset della categoria di log *kube-audit.* *kube-audit-admin* riduce significativamente il numero di log escludendo gli eventi di controllo *get* ed *list* dal log.
- La *categoria guard* è gestita per i Azure AD controllo degli accessi in base al ruolo di Azure. Per i Azure AD gestiti: token in, informazioni utente in uscita. Per il controllo degli accessi in base al ruolo di Azure: verifiche di accesso in e in uscita.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Pianificare un pod di test nel cluster servizio Azure Kubernetes

Per generare alcuni log, creare un nuovo pod nel cluster servizio Azure Kubernetes. Il seguente esempio di manifesto YAML può essere usato per creare un'istanza NGINX di base. Creare un file denominato `nginx.yaml` in un editor a scelta e incollare il contenuto seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Creare il pod con il comando [kubectl create][kubectl-create] e specificare il file YAML, come illustrato nell'esempio seguente:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Visualizzare i log raccolti

Potrebbero essere disponibili fino a 10 minuti prima che i log di diagnostica vengano abilitati e visualizzati.

> [!NOTE]
> Se sono necessari tutti i dati del log di controllo per motivi di conformità o per altri scopi, raccoglierlo e archiviarlo in un archivio economico, ad esempio l'archiviazione BLOB. Usare la categoria di log *kube-audit-admin* per raccogliere e salvare un set significativo di dati del log di controllo a scopo di monitoraggio e avviso.

Nell'portale di Azure passare al cluster del servizio Web Disassoce e selezionare **Log** sul lato sinistro. Chiudere la *finestra Query di* esempio, se visualizzata.

Sul lato sinistro scegliere **Log**. Per visualizzare i *log kube-audit,* immettere la query seguente nella casella di testo:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

È probabile che siano restituiti molti log. Per impostare l'ambito della query per visualizzare i log relativi al pod NGINX creato nel passaggio precedente, aggiungere un'ulteriore istruzione *where* per cercare *nginx,* come illustrato nella query di esempio seguente:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Per visualizzare i *log di kube-audit-admin,* immettere la query seguente nella casella di testo:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

In questo esempio la query mostra tutti i processi di creazione in *kube-audit-admin.* È probabile che siano stati restituiti molti risultati. Per impostare l'ambito della query per visualizzare i log sul pod NGINX creato nel passaggio precedente, aggiungere un'ulteriore istruzione *where* per cercare *nginx,* come illustrato nella query di esempio seguente.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Per altre informazioni su come eseguire query e filtrare i dati di log, vedere Visualizzare o analizzare i dati raccolti con la ricerca [log di Log Analytics.][analyze-log-analytics]

## <a name="log-event-schema"></a>Schema di eventi del log

Il programma AKS registra gli eventi seguenti:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Heartbeat][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Perf][log-schema-perf]

## <a name="log-roles"></a>Ruoli del log

| Ruolo                     | Descrizione |
|--------------------------|-------------|
| *aksService*             | Nome visualizzato nel log di controllo per l'operazione del piano di controllo (da hcpService) |
| *masterclient*           | Il nome visualizzato nel log di controllo per MasterClientCertificate, il certificato che si ottiene da az aks get-credentials |
| *nodeclient*             | Nome visualizzato per ClientCertificate, usato dai nodi dell'agente |

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come abilitare ed esaminare i log per i componenti del piano di controllo Kubernetes nel cluster del servizio Kubernetes. Per monitorare ulteriormente e risolvere eventuali problemi, è anche possibile [visualizzare i log di Kubelet][kubelet-logs] e [abilitare l'accesso ai nodi SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/logs/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/logs/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf