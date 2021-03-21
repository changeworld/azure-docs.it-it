---
title: "Avvio rapido: Distribuire un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure con nodi di confidential computing"
description: In questa Guida introduttiva si apprenderà come creare un cluster AKS con nodi riservati e come distribuire un'app Hello World usando l'interfaccia della riga di comando di Azure.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: amgowda
ms.custom: contentperf-fy21q3
ms.openlocfilehash: a7566cdb22d62bc46df82a3ef0aa78a748769531
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657706"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli"></a>Guida introduttiva: distribuire un cluster Azure Kubernetes Service (AKS) con nodi di calcolo riservati (DCsv2) usando l'interfaccia della riga di comando di Azure

Questa guida di avvio rapido è rivolta agli sviluppatori o agli operatori cluster che vogliono creare rapidamente un cluster del servizio Azure Kubernetes e distribuire un'applicazione per monitorare le applicazioni usando il servizio Kubernetes gestito in Azure. È anche possibile eseguire il provisioning del cluster e aggiungere nodi di elaborazione riservati da portale di Azure.

## <a name="overview"></a>Panoramica

In questa Guida introduttiva si apprenderà come distribuire un cluster Azure Kubernetes Service (AKS) con nodi di elaborazione riservati usando l'interfaccia della riga di comando di Azure ed eseguire una semplice applicazione Hello World in un'enclave. Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. Per altre informazioni, vedere l' [Introduzione a AKS](../aks/intro-kubernetes.md) e la [Panoramica dei nodi riservati AKS](confidential-nodes-aks-overview.md).

> [!NOTE]
> Le macchine virtuali DCsv2 con confidential computing usano hardware specializzato soggetto a prezzi maggiori e alla disponibilità a livello di area. Per altre informazioni sugli SKU disponibili e le aree supportate, vedere [Soluzioni nelle macchine virtuali di Azure](virtual-machine-solutions.md).

### <a name="confidential-computing-node-features-dcsv2"></a>Funzionalità del nodo di computing riservato (DCsv2)

1. Nodi di lavoro Linux che supportano i contenitori Linux.
1. VM di generazione 2 con i nodi delle macchine virtuali Ubuntu 18,04.
1. CPU basata su Intel SGX con memoria EPC (Encrypted Page Cache). Altre informazioni sono disponibili [qui](./faq.md).
1. Supporto per Kubernetes versione 1.16 +.
1. Driver Intel SGX DCAP pre-installato nei nodi AKS. Altre informazioni sono disponibili [qui](./faq.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

1. Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
1. INTERFACCIA della riga di comando di Azure versione 2.0.64 o successiva installata e configurata nel computer di distribuzione (eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md).
1. Almeno sei core **DCsv2** disponibili nella sottoscrizione per l'uso. Per impostazione predefinita, la quota di core VM per la sottoscrizione di Azure computing per Azure è di otto core. Se si prevede di effettuare il provisioning di un cluster che richiede più di otto core, seguire [queste](../azure-portal/supportability/per-vm-quota-requests.md) istruzioni per generare un ticket di aumento della quota.

## <a name="create-a-new-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>Creare un nuovo cluster AKS con nodi di elaborazione riservati e componenti aggiuntivi

Seguire le istruzioni seguenti per aggiungere nodi in grado di supportare l'elaborazione riservata con il componente aggiuntivo.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>Creare un cluster AKS con un pool di nodi di sistema

Se si ha già un cluster del servizio Azure Kubernetes che soddisfa i requisiti elencati sopra, [passare alla sezione relativa al cluster esistente](#existing-cluster) per aggiungere un nuovo pool di nodi di confidential computing.

Per prima cosa, creare un gruppo di risorse per il cluster usando il comando [AZ Group create][az-group-create] . L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *westus2*:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Creare ora un cluster AKS usando il comando [AZ AKS create][az-aks-create] :

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```

Il precedente consente di creare un nuovo cluster AKS con un pool di nodi di sistema con il componente aggiuntivo abilitato. Aggiungere quindi un pool di nodi utente con funzionalità di elaborazione riservate al cluster AKS.

### <a name="add-a-confidential-computing-node-pool-to-the-aks-cluster"></a>Aggiungere un pool di nodi di calcolo confidenziale al cluster AKS 

Eseguire il comando seguente per aggiungere un pool di nodi utente di `Standard_DC2s_v2` dimensioni con tre nodi. È possibile scegliere un altro SKU dall'elenco supportato di [SKU e aree DCsv2](../virtual-machines/dcv2-series.md).

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

Dopo l'esecuzione, un nuovo pool di nodi con **DCsv2** deve essere visibile con il componente aggiuntivo per l'elaborazione riservata gli elementi daemonset (plug-in del [dispositivo SGX](confidential-nodes-aks-overview.md#sgx-plugin)).

### <a name="verify-the-node-pool-and-add-on"></a>Verificare il pool di nodi e il componente aggiuntivo

Ottenere le credenziali per il cluster AKS usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Verificare che i nodi siano creati correttamente e che i gli elementi daemonset correlati a SGX siano in esecuzione nei pool di nodi **DCsv2** usando il comando kubectl get Pod & nodes come illustrato di seguito:

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se l'output corrisponde a quello precedente, il cluster AKS è ora pronto per l'esecuzione di applicazioni riservate.

Passare alla sezione [Hello World da enclave](#hello-world) Deployment per testare un'app in un'enclave. In alternativa, seguire le istruzioni seguenti per aggiungere altri pool di nodi in AKS (AKS supporta la combinazione di pool di nodi SGX e pool di nodi non SGX).

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>Aggiungere un pool di nodi di calcolo riservati a un cluster AKS esistente<a id="existing-cluster"></a>

Questa sezione presuppone che sia già in esecuzione un cluster AKS che soddisfi i criteri elencati nella sezione Prerequisiti (si applica al componente aggiuntivo).

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>Abilitare il componente aggiuntivo Confidential computing AKS sul cluster esistente

Eseguire il comando seguente per abilitare il componente aggiuntivo Confidential Computing:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>Aggiungere un pool di nodi utente **DCsv2** al cluster

> [!NOTE]
> Per usare la funzionalità di elaborazione riservata, il cluster AKS esistente deve avere almeno un pool di nodi basato su SKU della macchina virtuale **DCsv2** . Per altre informazioni sui controller di dominio di calcolo riservati-V2 VM SKU, vedere [SKU disponibili e aree supportate](virtual-machine-solutions.md).

Eseguire il comando seguente per creare un nuovo pool di nodi:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

Verificare che sia stato creato il nuovo pool di nodi con il nome confcompool1:

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>Verificare che gli elementi daemonset siano in esecuzione nei pool di nodi riservati

Accedere al cluster AKS esistente per eseguire la verifica seguente.

```console
kubectl get nodes
```

L'output dovrebbe mostrare il pool di nodi confcompool1 appena aggiunto nel cluster del servizio Azure Kubernetes. È anche possibile visualizzare altri gli elementi daemonset.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

Se l'output corrisponde a quello precedente, il cluster AKS è ora pronto per l'esecuzione di applicazioni riservate. Per distribuire un'applicazione di test, seguire le istruzioni riportate di seguito.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World da un'applicazione enclave isolata <a id="hello-world"></a>
Creare un file denominato *hello-world-enclave.yaml* e incollare il manifesto YAML seguente. Questo codice applicativo di esempio basato su Open Enclave è disponibile nel [progetto Open Enclave](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Per la distribuzione seguente si presuppone che sia stato distribuito l'addon "confcom".

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

Per rimuovere i pool di nodi associati o eliminare il cluster AKS, usare i comandi seguenti:

### <a name="remove-the-confidential-computing-node-pool"></a>Rimuovere il pool di nodi di calcolo riservati

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
```

### <a name="delete-the-aks-cluster"></a>Eliminare il cluster AKS

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

## <a name="next-steps"></a>Passaggi successivi

* Eseguire le applicazioni Python, node e così via in modo riservato tramite contenitori riservati visitando [esempi di contenitori riservati](https://github.com/Azure-Samples/confidential-container-samples).

* Eseguire applicazioni con riconoscimento dell'enclave visitando [Esempi di contenitori con riconoscimento dell'enclave](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/).

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials