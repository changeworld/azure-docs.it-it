---
title: Risolvere i problemi comuni di Kubernetes di Azure Arc abilitati
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Risoluzione dei problemi comuni relativi ai cluster Kubernetes con abilitazione di Arc.
keywords: Kubernetes, Arc, Azure, contenitori
ms.openlocfilehash: 992ea75c48b2630032e1314610986fbc610eec7b
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025782"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Risoluzione dei problemi di Kubernetes abilitato per Azure Arc

Questo documento fornisce guide per la risoluzione dei problemi relativi a connettività, autorizzazioni e agenti.

## <a name="general-troubleshooting"></a>Risoluzione dei problemi generali

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Prima `az connectedk8s` di usare `az k8s-configuration` i comandi dell'interfaccia della riga di comando di o, controllare che l'interfaccia della riga di comando di Azure sia impostata in modo da funzionare

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Agenti di Azure Arc

Tutti gli agenti per Kubernetes con abilitazione di Azure Arc vengono distribuiti come pod nello spazio dei nomi `azure-arc`. Tutti i pod devono essere in esecuzione e passare i controlli di integrità.

Prima di tutto, verificare la versione di Helm di Azure Arc:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Se la versione Helm non è stata trovata o è mancante, provare [a connettere di nuovo il cluster ad Azure Arc](./quickstart-connect-cluster.md) .

Se la versione Helm è presente con `STATUS: deployed` , verificare lo stato degli agenti utilizzando `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Tutti i pod dovrebbero essere visualizzati `STATUS` come `Running` con `3/3` o `2/2` nella `READY` colonna. Recuperare i log e descrivere i pod che restituiscono un oggetto `Error` o `CrashLoopBackOff` . Se i pod sono bloccati nello `Pending` stato, potrebbero essere presenti risorse insufficienti nei nodi del cluster. [La scalabilità verticale del cluster](https://kubernetes.io/docs/tasks/administer-cluster/) consente di ottenere questi pod per la transizione allo `Running` stato.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Connessione di cluster Kubernetes ad Azure Arc

Per la connessione di cluster ad Azure è necessario l'accesso a una sottoscrizione di Azure e `cluster-admin` l'accesso a un cluster di destinazione. Se non è possibile raggiungere il cluster o non si dispone di autorizzazioni sufficienti, la connessione del cluster ad Azure Arc avrà esito negativo.

### <a name="insufficient-cluster-permissions"></a>Autorizzazioni cluster insufficienti

Se il file kubeconfig specificato non dispone di autorizzazioni sufficienti per installare gli agenti di Azure Arc, il comando dell'interfaccia della riga di comando di Azure restituirà un errore.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Per l'utente che connette il cluster ad Azure Arc dovrebbe essere `cluster-admin` assegnato un ruolo nel cluster.

### <a name="installation-timeouts"></a>Timeout di installazione

Per la connessione di un cluster Kubernetes ad Azure Arc abilitato Kubernetes è necessaria l'installazione degli agenti di Azure Arc sul cluster. Se il cluster è in esecuzione su una connessione Internet lenta, il pull dell'immagine del contenitore per gli agenti potrebbe richiedere più tempo dei timeout dell'interfaccia della riga di comando di Azure.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Problema Helm

`v3.3.0-rc.1`La versione Helm presenta un [problema](https://github.com/helm/helm/pull/8527) a causa del quale l'installazione o l'aggiornamento di Helm (usato dall' `connectedk8s` estensione CLI) comporta l'esecuzione di tutti i hook che comportano l'errore seguente:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Per risolvere questo problema, attenersi alla seguente procedura:

1. Eliminare la risorsa Kubernetes abilitata per Azure Arc nell'portale di Azure.
2. Eseguire i comandi seguenti nel computer:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Installare una versione stabile](https://helm.sh/docs/intro/install/) di Helm 3 nel computer invece della versione finale candidata.
4. Eseguire il `az connectedk8s connect` comando con i valori appropriati per connettere il cluster ad Azure Arc.

## <a name="configuration-management"></a>Gestione della configurazione

### <a name="general"></a>Generale
Per semplificare la risoluzione dei problemi relativi alla risorsa di configurazione, eseguire il comando AZ Commands with `--debug` Parameter specificato.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Crea configurazioni

Le autorizzazioni di scrittura per la risorsa Kubernetes abilitata per Azure Arc ( `Microsoft.Kubernetes/connectedClusters/Write` ) sono necessarie e sufficienti per la creazione di configurazioni in tale cluster.

### <a name="configuration-remains-pending"></a>La configurazione rimane `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Monitoraggio

Il monitoraggio di Azure per i contenitori richiede l'esecuzione di DaemonSet in modalità privilegiata. Per configurare correttamente un cluster Kubernetes con Charmes canonico per il monitoraggio, eseguire il comando seguente:

```console
juju config kubernetes-worker allow-privileged=true
```