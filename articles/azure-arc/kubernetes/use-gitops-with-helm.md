---
title: Distribuire i grafici Helm con GitOps nel cluster Kubernetes abilitato per Arc (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usare GitOps con Helm per la configurazione di un cluster abilitato per Azure Arc (anteprima)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, servizio Azure Container, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 883eb9c152bdc8a7c0e60e999cf9decf47fb80ec
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377925"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Distribuire i grafici Helm con GitOps nel cluster Kubernetes abilitato per Arc (anteprima)

Helm è uno strumento open source per la creazione di pacchetti che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes. Analogamente ai gestori di pacchetti Linux, ad esempio APT e yum, Helm viene usato per gestire i grafici Kubernetes, che sono pacchetti di risorse Kubernetes preconfigurate.

Questo articolo illustra come configurare e usare Helm con Kubernetes con abilitazione di Azure Arc.

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di disporre di un cluster con connessione Kubernetes abilitato per Azure ARC esistente. Se è necessario un cluster connesso, vedere la [Guida introduttiva alla connessione di un cluster Kubernetes abilitato per Azure Arc](./connect-cluster.md).

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Panoramica dell'uso di GitOps e Helm con Azure Arc Enabled Kubernetes

 L'operatore Helm fornisce un'estensione a Flux che consente di automatizzare le versioni dei grafici Helm. Una versione del grafico Helm è descritta tramite una risorsa personalizzata di Kubernetes denominata HelmRelease. Flux sincronizza queste risorse da git al cluster, mentre l'operatore Helm garantisce che i grafici Helm vengano rilasciati come specificato nelle risorse.

 Il [repository di esempio](https://github.com/Azure/arc-helm-demo) usato in questo articolo è strutturato nel modo seguente:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

Nel repository git sono presenti due directory: una contenente un grafico Helm e una che contiene la configurazione releases. Nella `releases` directory `app.yaml` contiene la configurazione HelmRelease, mostrata di seguito:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

La configurazione delle versioni Helm contiene i campi seguenti:

| Campo | Descrizione |
| ------------- | ------------- | 
| `metadata.name` | Campo obbligatorio. Deve seguire le convenzioni di denominazione Kubernetes. |
| `metadata.namespace` | Campo facoltativo. Determina la posizione in cui viene creata la versione. |
| `spec.releaseName` | Campo facoltativo. Se non viene specificato, il nome della versione sarà `$namespace-$name` . |
| `spec.chart.path` | La directory che contiene il grafico, data relativa alla radice del repository. |
| `spec.values` | Personalizzazioni utente dei valori di parametro predefiniti dal grafico stesso. |

Le opzioni specificate in HelmRelease `spec.values` sostituiranno le opzioni specificate in `values.yaml` dall'origine del grafico.

Per altre informazioni su HelmRelease, vedere la [documentazione relativa all'operatore Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/)ufficiale.

## <a name="create-a-configuration"></a>Creare una configurazione

Usando l'estensione dell'interfaccia della riga di comando di Azure per `k8sconfiguration` , collegare il cluster connesso al repository git di esempio. Assegnare a questa configurazione il nome `azure-arc-sample` e distribuire l'operatore Flux nello `arc-k8s-demo` spazio dei nomi.

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parametri di configurazione

Per personalizzare la creazione della configurazione, vedere informazioni [sui parametri aggiuntivi che è possibile usare](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Convalidare la configurazione

Usando l'interfaccia della riga di comando di Azure, verificare che `sourceControlConfiguration` sia stato creato correttamente.

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

La `sourceControlConfiguration` risorsa viene aggiornata con lo stato di conformità, i messaggi e le informazioni di debug.

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Convalidare l'applicazione

Eseguire il comando seguente e passare a `localhost:8080` nel browser per verificare che l'applicazione sia in esecuzione.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)
