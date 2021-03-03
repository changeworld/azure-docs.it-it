---
title: "Avvio rapido: Distribuire un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure con nodi di confidential computing"
description: Informazioni su come creare un cluster del servizio Azure Kubernetes con nodi di confidential computing e distribuire un'app Hello World tramite l'interfaccia della riga di comando di Azure.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/25/2020
ms.author: amgowda
ms.openlocfilehash: 51b0813849236d9335d1482019f740fc8b23749f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703287"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Guida introduttiva: distribuire un cluster Azure Kubernetes Service (AKS) con nodi di calcolo riservati (DCsv2) usando l'interfaccia della riga di comando di Azure

Questa guida di avvio rapido è rivolta agli sviluppatori o agli operatori cluster che vogliono creare rapidamente un cluster del servizio Azure Kubernetes e distribuire un'applicazione per monitorare le applicazioni usando il servizio Kubernetes gestito in Azure. È anche possibile eseguire il provisioning del cluster e aggiungere nodi di elaborazione riservati da portale di Azure.

## <a name="overview"></a>Panoramica

In questa Guida introduttiva si apprenderà come distribuire un cluster Azure Kubernetes Service (AKS) con nodi di elaborazione riservati usando l'interfaccia della riga di comando di Azure ed eseguire una semplice applicazione Hello World in un'enclave. Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. Per altre informazioni sul servizio Azure Kubernetes, leggere [qui](../aks/intro-kubernetes.md).

> [!NOTE]
> Le macchine virtuali DCsv2 con confidential computing usano hardware specializzato soggetto a prezzi maggiori e alla disponibilità a livello di area. Per altre informazioni sugli SKU disponibili e le aree supportate, vedere [Soluzioni nelle macchine virtuali di Azure](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcxs-v2"></a>Funzionalità dei nodi di confidential computing (DC<x>s-v2)

1. Nodi di lavoro Linux che supportano i contenitori Linux
1. VM di seconda generazione con nodi di macchine virtuali Ubuntu 18.04
1. CPU basata su Intel SGX con memoria EPC (Encrypted Page Cache). Per altre informazioni, leggere [qui](./faq.md).
1. Supporto di Kubernetes versione 1.16 e successiva
1. Driver Intel SGX DCAP pre-installato nei nodi AKS. Per altre informazioni, leggere [qui](./faq.md).

## <a name="deployment-prerequisites"></a>Prerequisiti di distribuzione
Per l'esercitazione sulla distribuzione è necessario quanto segue:

1. Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare
1. INTERFACCIA della riga di comando di Azure versione 2.0.64 o successiva installata e configurata nel computer di distribuzione (eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md).
1. Almeno sei core **DC <x> s-V2** disponibili nella sottoscrizione per l'uso. Per impostazione predefinita, la quota di core delle VM per il confidential computing per ogni sottoscrizione di Azure è 8 core. Se si prevede di effettuare il provisioning di un cluster che richiede più di 8 core, seguire [queste istruzioni](../azure-portal/supportability/per-vm-quota-requests.md) per generare un ticket di aumento della quota

## <a name="creating-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Creazione di un nuovo cluster AKS con nodi di elaborazione riservati e componenti aggiuntivi
Seguire le istruzioni seguenti per aggiungere nodi in grado di supportare l'elaborazione riservata con il componente aggiuntivo.

### <a name="step-1-creating-an-aks-cluster-with-system-node-pool"></a>Passaggio 1: creazione di un cluster AKS con il pool di nodi di sistema

Se si ha già un cluster del servizio Azure Kubernetes che soddisfa i requisiti elencati sopra, [passare alla sezione relativa al cluster esistente](#existing-cluster) per aggiungere un nuovo pool di nodi di confidential computing.

Creare prima di tutto un gruppo di risorse per il cluster usando il comando az group create. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *westus2*:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Creare ora un cluster servizio Azure Kubernetes usando il comando az servizio Azure Kubernetes create.

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
Il precedente crea un nuovo cluster AKS con il pool di nodi di sistema con il componente aggiuntivo abilitato. Aggiungere ora un nodo utente di tipo pool di nodi di confidential computing nel servizio Azure Kubernetes (DCsv2)

### <a name="step-2-adding-confidential-computing-node-pool-to-aks-cluster"></a>Passaggio 2: aggiunta del pool di nodi di calcolo confidenziale al cluster AKS 

Eseguire il comando seguente per un utente nodepool di `Standard_DC2s_v2` dimensioni con 3 nodi. È possibile scegliere un altro elenco supportato di SKU DCsv2 e aree [qui](../virtual-machines/dcv2-series.md):

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```
Il comando precedente è stato completato. il nuovo pool di nodi con **DC <x> s-V2** dovrebbe essere visibile con il componente aggiuntivo per il calcolo riservato gli elementi daemonset (plug-in del [dispositivo SGX](confidential-nodes-aks-overview.md#sgx-plugin)
 
### <a name="step-3-verify-the-node-pool-and-add-on"></a>Passaggio 3: verificare il pool di nodi e il componente aggiuntivo
Ottenere le credenziali per il cluster servizio Azure Kubernetes usando il comando az servizio Azure Kubernetes get-credentials:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Verificare che i nodi siano stati creati correttamente e che i DaemonSet correlati a SGX siano in esecuzione nei pool di nodi **DC<x>s-v2** usando il comando kubectl get pods & nodes, come illustrato di seguito:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Se l'output corrisponde al codice riportato sopra, il cluster del servizio Azure Kubernetes è ora pronto per l'esecuzione di applicazioni di confidential computing.

Passare alla sezione [Hello World da un'applicazione enclave isolata](#hello-world) per testare un'app in un'enclave. In alternativa, seguire le istruzioni seguenti per aggiungere altri pool di nodi in AKS (AKS supporta la combinazione di pool di nodi SGX e pool di nodi non SGX)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Aggiunta di pool di nodi di confidential computing al cluster del servizio Azure Kubernetes esistente<a id="existing-cluster"></a>

Questa sezione presuppone che sia già in esecuzione un cluster AKS che soddisfi i criteri elencati nella sezione Prerequisiti (si applica al componente aggiuntivo).

### <a name="step-1-enabling-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Passaggio 1: abilitare il componente aggiuntivo Confidential computing AKS sul cluster esistente

Eseguire il comando seguente per abilitare il componente aggiuntivo Confidential computing

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
### <a name="step-2-add-dcxs-v2-user-node-pool-to-the-cluster"></a>Passaggio 2: aggiungere il pool di nodi utente **DC <x> s-V2** al cluster
    
> [!NOTE]
> Per usare la funzionalità di confidential computing, il cluster del servizio Azure Kubernetes esistente deve avere almeno un pool di nodi basato su SKU di VM **DC<x>s-v2**. Per altre informazioni sugli SKU di VM DCsv2 con confidential computing, vedere [SKU disponibili e aree supportate](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```
il comando precedente dovrebbe elencare il pool di nodi recente aggiunto con il nome confcompool1.

### <a name="step-3-verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Passaggio 3: verificare che gli elementi daemonset siano in esecuzione nei pool di nodi riservati

Accedere al cluster AKS esistente per eseguire la verifica seguente. 

```console
kubectl get nodes
```
L'output dovrebbe mostrare il pool di nodi confcompool1 appena aggiunto nel cluster del servizio Azure Kubernetes.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Se l'output corrisponde al codice riportato sopra, il cluster del servizio Azure Kubernetes è ora pronto per l'esecuzione di applicazioni di confidential computing. Seguire la distribuzione dell'applicazione di test riportata di seguito.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World da un'applicazione enclave isolata <a id="hello-world"></a>
Creare un file denominato *hello-world-enclave.yaml* e incollare il manifesto YAML seguente. Questo codice applicativo di esempio basato su Open Enclave è disponibile nel [progetto Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). La distribuzione seguente presuppone che sia stato distribuito il componente aggiuntivo "confcom".

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
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Usare ora il comando kubectl apply per creare un processo di esempio che verrà avviato in un'enclave protetta, come illustrato nell'output di esempio seguente:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

È possibile verificare che il carico di lavoro abbia creato correttamente un ambiente TEE (Enclave) eseguendo i comandi seguenti:

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere i pool di nodi associati o eliminare il cluster del servizio Azure Kubernetes, usare i comandi seguenti:

Eliminazione del cluster del servizio Azure Kubernetes
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Passaggi successivi

Eseguire applicazioni Python, Node e altre in modo riservato tramite contenitori riservati visitando gli [esempi di contenitori riservati](https://github.com/Azure-Samples/confidential-container-samples).

Eseguire applicazioni con riconoscimento dell'enclave visitando [Esempi di contenitori con riconoscimento dell'enclave](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).
