---
title: "Avvio rapido: Distribuire un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure con nodi di confidential computing"
description: Informazioni su come creare un cluster del servizio Azure Kubernetes con nodi di confidential computing e distribuire un'app Hello World tramite l'interfaccia della riga di comando di Azure.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: amgowda
ms.openlocfilehash: 92b4cd58b496602b479a24bab81a1d9322e732b0
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760640"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli-preview"></a>Avvio rapido: Distribuire un cluster del servizio Azure Kubernetes con nodi di confidential computing (DCsv2) tramite l'interfaccia della riga di comando di Azure (anteprima)

Questa guida di avvio rapido è rivolta agli sviluppatori o agli operatori cluster che vogliono creare rapidamente un cluster del servizio Azure Kubernetes e distribuire un'applicazione per monitorare le applicazioni usando il servizio Kubernetes gestito in Azure.

## <a name="overview"></a>Panoramica

Questa guida di avvio rapido illustra come distribuire un cluster del servizio Azure Kubernetes con nodi di confidential computing usando l'interfaccia della riga di comando di Azure ed eseguire un'applicazione Hello World in un'enclave. Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. Per altre informazioni sul servizio Azure Kubernetes, leggere [qui](../aks/intro-kubernetes.md).

> [!NOTE]
> Le macchine virtuali DCsv2 con confidential computing usano hardware specializzato soggetto a prezzi maggiori e alla disponibilità a livello di area. Per altre informazioni sugli SKU disponibili e le aree supportate, vedere [Soluzioni nelle macchine virtuali di Azure](virtual-machine-solutions.md).

> DCsv2 sfrutta le macchine virtuali di seconda generazione in Azure, che sono una funzionalità di anteprima nel servizio Azure Kubernetes. 

### <a name="deployment-pre-requisites"></a>Prerequisiti di distribuzione
Le istruzioni per la distribuzione presuppongono di:

1. Avere una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare
1. Avere l'interfaccia della riga di comando di Azure versione 2.0.64 o successiva installata e configurata nel computer di distribuzione (eseguire `az --version` per trovare la versione). Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](../container-registry/container-registry-get-started-azure-cli.md).
1. [Estensione aks-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) versione 0.4.62 o successiva 
1. Disponibilità della quota di core delle VM. Avere almeno sei core **DC<x>s-v2** disponibili per l'uso nella sottoscrizione. Per impostazione predefinita, la quota di core delle VM per il confidential computing per ogni sottoscrizione di Azure è 8 core. Se si prevede di effettuare il provisioning di un cluster che richiede più di 8 core, seguire [queste istruzioni](../azure-portal/supportability/per-vm-quota-requests.md) per generare un ticket di aumento della quota

### <a name="confidential-computing-node-features-dcxs-v2"></a>Funzionalità dei nodi di confidential computing (DC<x>s-v2)

1. Nodi di lavoro Linux che supportano solo contenitori Linux
1. VM di seconda generazione con nodi di macchine virtuali Ubuntu 18.04
1. CPU basata su Intel SGX con memoria EPC (Encrypted Page Cache). Per altre informazioni, leggere [qui](./faq.md).
1. Supporto di Kubernetes versione 1.16 e successiva
1. Driver DCAP di Intel SGX preinstallato nei nodi del servizio Azure Kubernetes. Per altre informazioni, leggere [qui](./faq.md).
1. Supporto dell'interfaccia della riga di comando distribuita durante l'anteprima con il provisioning basato sul portale dopo la disponibilità generale.


## <a name="installing-the-cli-pre-requisites"></a>Installazione dei prerequisiti dell'interfaccia della riga di comando

Per installare l'estensione aks-preview 0.4.62 o versione successiva, usare i comandi seguenti dell'interfaccia della riga di comando di Azure:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Per aggiornare l'estensione aks-preview dell'interfaccia della riga di comando, usare i comandi seguenti dell'interfaccia della riga di comando di Azure:

```azurecli-interactive
az extension update --name aks-preview
```
### <a name="generation-2-vms-feature-registration-on-azure"></a>Registrazione della funzionalità delle VM di seconda generazione in Azure
Registrazione di Gen2VMPreview nella sottoscrizione di Azure. Questa funzionalità consente di effettuare il provisioning di macchine virtuali di seconda generazione come pool di nodi del servizio Azure Kubernetes:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come Registrato. È possibile controllare lo stato della registrazione con il comando "az feature list". La registrazione di questa funzionalità viene eseguita una sola volta per ogni sottoscrizione. Se la funzionalità è già stata registrata, è possibile ignorare il passaggio riportato sopra:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse Microsoft.ContainerService usando il comando "az provider register":

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="azure-confidential-computing-feature-registration-on-azure-optional-but-recommended"></a>Registrazione della funzionalità di confidential computing in Azure (passaggio facoltativo ma consigliato)
Registrazione di AKS-ConfidentialComputinAddon nella sottoscrizione di Azure. Questa funzionalità aggiungerà due DaemonSet come illustrato in dettaglio [qui](./confidential-nodes-aks-overview.md#aks-provided-daemon-sets-addon):
1. Plug-in del driver di dispositivo SGX
2. Helper di citazioni di attestazioni SGX

```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come Registrato. È possibile controllare lo stato della registrazione con il comando "az feature list". La registrazione di questa funzionalità viene eseguita una sola volta per ogni sottoscrizione. Se la funzionalità è già stata registrata, è possibile ignorare il passaggio riportato sopra:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputinAddon')].{Name:name,State:properties.state}"
```
Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse Microsoft.ContainerService usando il comando "az provider register":

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>Creazione di un cluster del servizio Azure Kubernetes

Se si ha già un cluster del servizio Azure Kubernetes che soddisfa i requisiti elencati sopra, [passare alla sezione relativa al cluster esistente](#existing-cluster) per aggiungere un nuovo pool di nodi di confidential computing.

Creare prima di tutto un gruppo di risorse per il cluster usando il comando az group create. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *westus2*:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Creare ora un cluster servizio Azure Kubernetes usando il comando az servizio Azure Kubernetes create.

```azurecli-interactive
# Create a new AKS cluster with  system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
Il codice riportato sopra crea un nuovo cluster del servizio Azure Kubernetes con il pool di nodi di sistema. Aggiungere ora un nodo utente di tipo pool di nodi di confidential computing nel servizio Azure Kubernetes (DCsv2)

L'esempio seguente aggiunge un pool di nodi utente con 3 nodi di dimensioni `Standard_DC2s_v2`. È possibile scegliere un altro elenco supportato di SKU DCsv2 e aree [qui](../virtual-machines/dcv2-series.md):

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2 --aks-custom-headers usegen2vm=true
```
Il comando riportato sopra dovrebbe aggiungere un nuovo pool di nodi con **DC<x>s-v2** ed eseguire automaticamente due DaemonSet in questo pool di nodi - ([Plug-in del dispositivo SGX](confidential-nodes-aks-overview.md#sgx-plugin) & [Helper di citazioni SGX](confidential-nodes-aks-overview.md#sgx-quote))

Ottenere le credenziali per il cluster servizio Azure Kubernetes usando il comando az servizio Azure Kubernetes get-credentials:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Verificare che i nodi siano stati creati correttamente e che i DaemonSet correlati a SGX siano in esecuzione nei pool di nodi **DC<x>s-v2** usando il comando kubectl get pods & nodes, come illustrato di seguito:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Se l'output corrisponde al codice riportato sopra, il cluster del servizio Azure Kubernetes è ora pronto per l'esecuzione di applicazioni di confidential computing.

Passare alla sezione [Hello World da un'applicazione enclave isolata](#hello-world) per testare un'app in un'enclave. Oppure seguire le istruzioni riportate di seguito per aggiungere altri pool di nodi nel cluster del servizio Azure Kubernetes (il servizio supporta una combinazione di pool di nodi SGX e non SGX).

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Aggiunta di pool di nodi di confidential computing al cluster del servizio Azure Kubernetes esistente<a id="existing-cluster"></a>

Questa sezione presuppone che sia già in esecuzione un cluster del servizio Azure Kubernetes che soddisfa i criteri elencati nella sezione dei prerequisiti.

Aggiungere prima di tutto la funzionalità alla sottoscrizione di Azure

```azurecli-interactive
az feature register --name AKS-ConfidentialComputinAddon --namespace Microsoft.ContainerService
```
Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come Registrato. È possibile controllare lo stato della registrazione con il comando "az feature list". La registrazione di questa funzionalità viene eseguita una sola volta per ogni sottoscrizione. Se la funzionalità è già stata registrata, è possibile ignorare il passaggio riportato sopra:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputinAddon')].{Name:name,State:properties.state}"
```
Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse Microsoft.ContainerService usando il comando "az provider register":

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Quindi, abilitare i componenti aggiuntivi del servizio Azure Kubernetes correlati al confidential computing nel cluster esistente:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Aggiungere ora un pool di nodi utente **DC<x>s-v2** al cluster
    
> [!NOTE]
> Per usare la funzionalità di confidential computing, il cluster del servizio Azure Kubernetes esistente deve avere almeno un pool di nodi basato su SKU di VM **DC<x>s-v2**. Per altre informazioni sugli SKU di VM DCsv2 con confidential computing, vedere [SKU disponibili e aree supportate](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
L'output dovrebbe mostrare il pool di nodi confcompool1 appena aggiunto nel cluster del servizio Azure Kubernetes.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Se l'output corrisponde al codice riportato sopra, il cluster del servizio Azure Kubernetes è ora pronto per l'esecuzione di applicazioni di confidential computing.

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
