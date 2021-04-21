---
title: "Guida introduttiva: Distribuire un cluster del servizio AzureKs con nodi di confidential computing usando l'interfaccia della riga di comando di Azure"
description: Informazioni su come creare un cluster servizio Azure Kubernetes (AKS) con nodi riservati e distribuire un'app Hello World usando l'interfaccia della riga di comando di Azure.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: b1ef397c1a3f6770d197ca8fd0faa83c9e8360f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816459"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Guida introduttiva: Distribuire un cluster del servizio AzureKs con nodi di confidential computing usando l'interfaccia della riga di comando di Azure

In questa guida introduttiva si userà l'interfaccia della riga di comando di Azure per distribuire un cluster servizio Azure Kubernetes (AKS) con nodi confidential computing (DCsv2). Si eseguirà quindi una semplice applicazione Hello World in un enclave. È anche possibile effettuare il provisioning di un cluster e aggiungere nodi di confidential computing dal portale di Azure, ma questa guida introduttiva è incentrata sull'interfaccia della riga di comando di Azure.

Il servizio AKS è un servizio Kubernetes gestito che consente agli sviluppatori o agli operatori del cluster di distribuire e gestire rapidamente i cluster. Per altre informazioni, vedere l'introduzione al [server del](../aks/intro-kubernetes.md) server del cluster di Servizi di controllo di accesso e la panoramica dei nodi riservati del server del server del cluster di Servizi [di rete.](confidential-nodes-aks-overview.md)

Le funzionalità dei nodi di confidential computing includono:

- Nodi di lavoro Linux che supportano contenitori Linux.
- Macchina virtuale (VM) di seconda generazione con nodi di macchine virtuali Ubuntu 18.04.
- CPU che supporta Intel SGX per consentire l'esecuzione dei contenitori nell'enclave protetto da riservatezza sfruttando la memoria EPC (Encrypted Page Cache Memory). Per altre informazioni, vedere [Domande frequenti sul confidential computing di Azure.](./faq.md)
- Driver Intel SGX DCAP preinstallato nei nodi di confidential computing. Per altre informazioni, vedere [Domande frequenti sul confidential computing di Azure.](./faq.md)

> [!NOTE]
> Le macchine virtuali DCsv2 usano hardware specializzato soggetto a prezzi più elevati e alla disponibilità dell'area. Per altre informazioni, vedere gli [SKU disponibili e le aree supportate.](virtual-machine-solutions.md)

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

- Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Interfaccia della riga di comando di Azure versione 2.0.64 o successiva installata e configurata nel computer di distribuzione. 

  Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md).
- Almeno sei core DCsv2 disponibili nella sottoscrizione. 

  Per impostazione predefinita, la quota per il confidential computing per ogni sottoscrizione di Azure è di otto core vm. Se si prevede di effettuare il provisioning di un cluster che richiede più di otto core, seguire queste istruzioni [per](../azure-portal/supportability/per-vm-quota-requests.md) generare un ticket di aumento della quota.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Creare un cluster del servizio Web Diaks con nodi di confidential computing e componente aggiuntivo

Usare le istruzioni seguenti per creare un cluster del servizio Web Disassoce con il componente aggiuntivo di confidential computing abilitato, aggiungere un pool di nodi al cluster e verificare ciò che è stato creato.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Creare un cluster del servizio Web Disassoce con un pool di nodi di sistema

> [!NOTE]
> Se si dispone già di un cluster del servizio Web Diaks che soddisfa i criteri dei prerequisiti elencati in precedenza, passare alla [sezione](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) successiva per aggiungere un pool di nodi di confidential computing.

Creare prima di tutto un gruppo di risorse per il cluster usando il [comando az group create.][az-group-create] L'esempio seguente crea un gruppo di risorse *denominato myResourceGroup* nell'area *westus2:*

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Creare ora un cluster del servizio Web Diaz, con il componente aggiuntivo di confidential computing abilitato, usando il [comando az servizio WebKs create:][az-aks-create]

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Aggiungere un pool di nodi utente con funzionalità di confidential computing al cluster del servizio Web AKS 

Eseguire il comando seguente per aggiungere un pool di nodi utente di dimensioni `Standard_DC2s_v2` con tre nodi al cluster del servizio Web Diaks. È possibile scegliere un altro SKU dall'elenco di SKU e aree [DCsv2 supportati.](../virtual-machines/dcv2-series.md)

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Dopo aver eseguito il comando, un nuovo pool di nodi con DCsv2 dovrebbe essere visibile con i DaemonSet dei componenti aggiuntivi di confidential computing (plug-in del dispositivo[SGX).](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)

### <a name="verify-the-node-pool-and-add-on"></a>Verificare il pool di nodi e il componente aggiuntivo

Ottenere le credenziali per il cluster del servizio Diaks usando il [comando az servizio WebKs get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Usare il comando per verificare che i nodi siano stati creati correttamente e che i DaemonSet correlati a SGX siano in esecuzione nei pool `kubectl get pods` di nodi DCsv2:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se l'output corrisponde al codice precedente, il cluster del servizio Web Diaks è ora pronto per l'esecuzione di applicazioni riservate.

È possibile passare alla sezione Distribuire Hello World da un'applicazione [enclave](#hello-world) isolata in questa guida introduttiva per testare un'app in un'enclave. In altro modo, usare le istruzioni seguenti per aggiungere altri pool di nodi nel cluster del server del cluster di Controllo di accesso. Il servizio Web Diaks supporta la combinazione di pool di nodi SGX e pool di nodi non SGX.

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Aggiungere un pool di nodi di confidential computing a un cluster del servizio Web Disatteso esistente<a id="existing-cluster"></a>

In questa sezione si presuppone che sia già in esecuzione un cluster del servizio Gestione servizio Web di Microsoft Exchange che soddisfi i criteri dei prerequisiti elencati in precedenza in questa guida introduttiva.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Abilitare il componente aggiuntivo confidential computing AKS nel cluster esistente

Eseguire il comando seguente per abilitare il componente aggiuntivo confidential computing:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Aggiungere un pool di nodi utente DCsv2 al cluster

> [!NOTE]
> Per usare la funzionalità di confidential computing, è necessario che il cluster del servizio Web del servizio Web esistente abbia almeno un pool di nodi basato su uno SKU di VM DCsv2. Per altre informazioni sugli SKU delle macchine virtuali DCs-v2 per il confidential computing, vedere gli SKU disponibili [e le aree supportate.](virtual-machine-solutions.md)

Eseguire il comando seguente per creare un pool di nodi:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Verificare che sia stato creato il nuovo pool di nodi con il nome *confcompool1:*

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Verificare che i DaemonSet siano in esecuzione in pool di nodi riservati

Accedere al cluster del servizio AKS esistente per eseguire la verifica seguente:

```console
kubectl get nodes
```

L'output dovrebbe mostrare il pool *confcompool1* appena aggiunto nel cluster del servizio Web Diaks. Potrebbero essere visualizzati anche altri DaemonSet.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se l'output corrisponde al codice precedente, il cluster del servizio Web Disatteso è ora pronto per eseguire applicazioni riservate. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Distribuire Hello World da un'applicazione enclave isolata <a id="hello-world"></a>
A questo punto è possibile distribuire un'applicazione di test. 

Creare un file *denominato hello-world-enclave.yaml* e incollarlo nel manifesto YAML seguente. È possibile trovare questo codice dell'applicazione di esempio nel [progetto Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Questa distribuzione presuppone che sia stato distribuito il componente aggiuntivo *confcom.*

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

Usare ora il comando per creare un processo di esempio che verrà aperto in un `kubectl apply` enclave sicuro, come illustrato nell'output di esempio seguente:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

È possibile verificare che il carico di lavoro sia stato creato correttamente un ambiente di esecuzione attendibile (enclave) eseguendo i comandi seguenti:

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il pool di nodi di confidential computing creato in questa guida introduttiva, usare il comando seguente: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Per eliminare il cluster del servizio Web Diaks, usare il comando seguente: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --cluster-name myAKSCluster
```

## <a name="next-steps"></a>Passaggi successivi

* Eseguire Python, Node o altre applicazioni tramite contenitori riservati usando gli esempi [di contenitori riservati in GitHub.](https://github.com/Azure-Samples/confidential-container-samples)

* Eseguire applicazioni che supportano l'enclave usando gli esempi di contenitori di Azure in grado di riconoscere [l'enclave in GitHub.](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
