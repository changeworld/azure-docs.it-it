---
title: "Guida introduttiva: distribuire un cluster AKS con nodi di elaborazione riservati usando l'interfaccia della riga di comando di Azure"
description: Informazioni su come creare un cluster Azure Kubernetes Service (AKS) con nodi riservati e distribuire un'app Hello World usando l'interfaccia della riga di comando di Azure.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: b012a8a5856b344b366f1ddd89fc5059a6f3c8ae
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283525"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>Guida introduttiva: distribuire un cluster AKS con nodi di elaborazione riservati usando l'interfaccia della riga di comando di Azure

In questa Guida introduttiva si userà l'interfaccia della riga di comando di Azure per distribuire un cluster Azure Kubernetes Service (AKS) con nodi di calcolo riservati (DCsv2). Si eseguirà quindi una semplice applicazione Hello World in un'enclave. È anche possibile eseguire il provisioning di un cluster e aggiungere nodi di elaborazione riservati dalla portale di Azure, ma questa Guida introduttiva è incentrata sull'interfaccia della riga di comando

AKS è un servizio Kubernetes gestito che consente agli sviluppatori o agli operatori di cluster di distribuire e gestire rapidamente i cluster. Per altre informazioni, vedere l' [Introduzione a AKS](../aks/intro-kubernetes.md) e la [Panoramica dei nodi riservati di AKS](confidential-nodes-aks-overview.md).

Le funzionalità dei nodi di elaborazione riservati includono:

- Nodi di lavoro Linux che supportano i contenitori Linux.
- Macchina virtuale di seconda generazione (VM) con nodi VM Ubuntu 18,04.
- CPU Intel SGX in grado di semplificare l'esecuzione dei contenitori nell'enclave protetta per la riservatezza, sfruttando la memoria cache della pagina (EPC) crittografata. Per altre informazioni, vedere [domande frequenti su Azure Confidential computing](./faq.md).
- Driver Intel SGX DCAP preinstallato nei nodi di elaborazione riservati. Per altre informazioni, vedere [domande frequenti su Azure Confidential computing](./faq.md).

> [!NOTE]
> Le macchine virtuali DCsv2 utilizzano hardware specializzato, soggetto a prezzi e disponibilità delle aree più elevati. Per altre informazioni, vedere gli [SKU disponibili e le aree supportate](virtual-machine-solutions.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

- Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- INTERFACCIA della riga di comando di Azure versione 2.0.64 o successiva installata e configurata nel computer di distribuzione. 

  Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md).
- Almeno sei core DCsv2 disponibili nella sottoscrizione. 

  Per impostazione predefinita, la quota per l'elaborazione riservata per ogni sottoscrizione di Azure è di otto core VM. Se si prevede di eseguire il provisioning di un cluster che richiede più di otto core, seguire [queste istruzioni](../azure-portal/supportability/per-vm-quota-requests.md) per generare un ticket di aumento della quota.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Creare un cluster AKS con nodi di calcolo riservati e componenti aggiuntivi

Usare le istruzioni seguenti per creare un cluster AKS con il componente aggiuntivo Confidential computing abilitato, aggiungere un pool di nodi al cluster e verificare cosa è stato creato.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Creare un cluster AKS con un pool di nodi di sistema

> [!NOTE]
> Se si dispone già di un cluster AKS che soddisfa i criteri prerequisiti elencati in precedenza, [passare alla sezione successiva](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) per aggiungere un pool di nodi di calcolo riservato.

Per prima cosa, creare un gruppo di risorse per il cluster usando il comando [AZ Group create][az-group-create] . L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *westus2* :

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

A questo punto, creare un cluster AKS con il componente aggiuntivo di elaborazione riservato abilitato, usando il comando [AZ AKS create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>Aggiungere un pool di nodi utente con funzionalità di elaborazione riservate al cluster AKS 

Eseguire il comando seguente per aggiungere un pool di nodi utente di `Standard_DC2s_v2` dimensioni con tre nodi al cluster AKS. È possibile scegliere un altro SKU dall' [elenco delle aree e degli SKU DCsv2 supportati](../virtual-machines/dcv2-series.md).

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Dopo aver eseguito il comando, un nuovo pool di nodi con DCsv2 deve essere visibile con il componente aggiuntivo per l'elaborazione riservata gli elementi daemonset ([plug-in del dispositivo SGX](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks)).

### <a name="verify-the-node-pool-and-add-on"></a>Verificare il pool di nodi e il componente aggiuntivo

Ottenere le credenziali per il cluster AKS usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Usare il `kubectl get pods` comando per verificare che i nodi siano creati correttamente e che i gli elementi daemonset correlati a SGX siano in esecuzione nei pool di nodi DCsv2:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se l'output corrisponde al codice precedente, il cluster AKS è ora pronto per l'esecuzione di applicazioni riservate.

È possibile passare alla sezione [Deploy Hello World from an isolated enclave](#hello-world) in questa Guida introduttiva per testare un'app in una enclave. In alternativa, usare le istruzioni seguenti per aggiungere altri pool di nodi in AKS. (AKS supporta la combinazione di pool di nodi SGX e pool di nodi non SGX).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Aggiungere un pool di nodi di calcolo riservati a un cluster AKS esistente<a id="existing-cluster"></a>

Questa sezione presuppone che sia già in esecuzione un cluster AKS che soddisfi i criteri prerequisiti elencati in precedenza in questa Guida introduttiva.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Abilitare il componente aggiuntivo Confidential computing AKS sul cluster esistente

Eseguire il comando seguente per abilitare il componente aggiuntivo Confidential Computing:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Aggiungere un pool di nodi utente DCsv2 al cluster

> [!NOTE]
> Per usare la funzionalità di elaborazione riservata, il cluster AKS esistente deve avere almeno un pool di nodi basato su uno SKU di VM DCsv2. Per altre informazioni sugli SKU di VM DCs-V2 per l'elaborazione riservata, vedere gli [SKU disponibili e le aree supportate](virtual-machine-solutions.md).

Eseguire il comando seguente per creare un pool di nodi:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Verificare che sia stato creato il nuovo pool di nodi con il nome *confcompool1* :

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Verificare che gli elementi daemonset siano in esecuzione nei pool di nodi riservati

Accedere al cluster AKS esistente per eseguire la verifica seguente:

```console
kubectl get nodes
```

L'output dovrebbe mostrare il pool *confcompool1* appena aggiunto nel cluster AKS. È anche possibile visualizzare altri gli elementi daemonset.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se l'output corrisponde al codice precedente, il cluster AKS è ora pronto per l'esecuzione di applicazioni riservate. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>Distribuire Hello World da un'applicazione enclave isolata <a id="hello-world"></a>
A questo punto si è pronti per distribuire un'applicazione di test. 

Creare un file denominato *Hello-World-enclave. YAML* e incollare il manifesto YAML seguente. È possibile trovare questo codice dell'applicazione di esempio nel [progetto Open enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Questa distribuzione presuppone che sia stato distribuito il componente aggiuntivo *confcom* .

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

Usare ora il `kubectl apply` comando per creare un processo di esempio che verrà aperto in un'enclave protetta, come illustrato nell'output di esempio seguente:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

È possibile verificare che il carico di lavoro abbia creato correttamente un ambiente di esecuzione attendibile eseguendo i comandi seguenti:

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

Per rimuovere il pool di nodi di calcolo riservato creato in questa Guida introduttiva, usare il comando seguente: 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

Per eliminare il cluster AKS, usare il comando seguente: 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Passaggi successivi

* Eseguire Python, node o altre applicazioni tramite contenitori riservati usando gli esempi di contenitori [riservati in GitHub](https://github.com/Azure-Samples/confidential-container-samples).

* Eseguire applicazioni compatibili con l'enclave usando gli [esempi di contenitori di Azure compatibili con l'enclave in GitHub](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
