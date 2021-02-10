---
title: Distribuire un'applicazione Java con Open Liberty o WebSphere Liberty in un cluster Azure Kubernetes Service (AKS)
description: Distribuire un'applicazione Java con Open Liberty o WebSphere Liberty in un cluster Azure Kubernetes Service (AKS).
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: Java, jakartaee, JavaEE, microprofile, Open Liberty, WebSphere-Liberty, AKS, kubernetes
ms.openlocfilehash: d0e6f2fea6894378da736ba83a90ee28402ec7f9
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007134"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Distribuire un'applicazione Java con Open Liberty o WebSphere Liberty in un cluster Azure Kubernetes Service (AKS)

Questo articolo illustra come:  
* Eseguire l'applicazione Java, Java EE, Jakarta EE o microprofile in Open Liberty o WebSphere Liberty Runtime.
* Compilare l'immagine Docker dell'applicazione usando le immagini del contenitore open Liberty.
* Distribuire l'applicazione in contenitori in un cluster AKS usando l'operatore Open Liberty.   

L'operatore Open Liberty semplifica la distribuzione e la gestione delle applicazioni in esecuzione nei cluster Kubernetes. Con l'operatore Open Liberty è inoltre possibile eseguire operazioni più avanzate, ad esempio la raccolta di tracce e dump. 

Per altri dettagli su Open Liberty, vedere [la pagina Open Liberty Project](https://openliberty.io/). Per altri dettagli su IBM WebSphere Liberty, vedere [la pagina del prodotto WebSphere Liberty](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Questo articolo richiede l'ultima versione dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.
* Se si eseguono i comandi in questa guida localmente (anziché Azure Cloud Shell):
  * Preparare un computer locale con sistema operativo simile a UNIX installato (ad esempio, Ubuntu, macOS, sottosistema Windows per Linux).
  * Installare un'implementazione di Java SE (ad esempio, [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Installare [Maven](https://maven.apache.org/download.cgi) 3.5.0 o versione successiva.
  * Installare [Docker](https://docs.docker.com/get-docker/) per il sistema operativo.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite.  

Creare un gruppo di risorse denominato *Java-Liberty-Project* usando il comando [AZ Group create](/cli/azure/group#az_group_create) nella località *eastus* . Questo gruppo di risorse verrà usato in un secondo momento per la creazione dell'istanza di Azure Container Registry (ACR) e del cluster AKS. 

```azurecli-interactive
RESOURCE_GROUP_NAME=java-liberty-project
az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-an-acr-instance"></a>Creare un'istanza di ACR

Usare il comando [AZ ACR create](/cli/azure/acr#az_acr_create) per creare l'istanza di ACR. Nell'esempio seguente viene creata un'istanza di ACR denominata *youruniqueacrname*. Assicurarsi che *youruniqueacrname* sia univoco all'interno di Azure.

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
az acr create --resource-group $RESOURCE_GROUP_NAME --name $REGISTRY_NAME --sku Basic --admin-enabled
```

Dopo un breve periodo di tempo, verrà visualizzato un output JSON che contiene:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Connettersi all'istanza di ACR

Per poter eseguire il push di un'immagine, è necessario accedere all'istanza di ACR. Eseguire i comandi seguenti per verificare la connessione:

```azurecli-interactive
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

`Login Succeeded`Alla fine dell'output del comando dovrebbe essere visualizzato se è stato effettuato l'accesso all'istanza di ACR correttamente.

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

Usare il comando [az aks create](/cli/azure/aks#az_aks_create) per creare un cluster del servizio Azure Kubernetes. L'esempio seguente crea un cluster denominato *myAKSCluster* con un nodo. Questa operazione richiede alcuni minuti.

```azurecli-interactive
CLUSTER_NAME=myAKSCluster
az aks create --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Dopo alcuni minuti, il comando viene completato e restituisce le informazioni in formato JSON sul cluster, incluse le seguenti:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Connettersi al cluster AKS

Per gestire un cluster Kubernetes, usare [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli](/cli/azure/aks#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials](/cli/azure/aks#az_aks_get_credentials). Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME --overwrite-existing
```

> [!NOTE]
> Il comando riportato sopra usa il percorso predefinito del [file di configurazione di Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), ossia `~/.kube/config`. È possibile specificare un percorso diverso per il file di configurazione di Kubernetes usando *--file*.

Per verificare la connessione al cluster, usare il comando [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) per restituire un elenco di nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'esempio di output seguente mostra il nodo singolo creato nei passaggi precedenti. Assicurarsi che lo stato del nodo sia impostato su *Pronto*:

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Installare Open Liberty operator

Dopo la creazione e la connessione al cluster, installare l' [operatore Open Liberty](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) eseguendo i comandi seguenti.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Crea immagine applicazione

Per distribuire ed eseguire l'applicazione Liberty nel cluster AKS, distribuire l'applicazione come immagine Docker usando le [Immagini del contenitore open Liberty](https://github.com/OpenLiberty/ci.docker) o [WebSphere Liberty container](https://github.com/WASdev/ci.docker)images.

1. Clonare il codice di esempio per questa guida. L'esempio è su [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Passare alla directory `javaee-app-simple-cluster` del clone locale.
1. Eseguire `mvn clean package` per creare il pacchetto dell'applicazione.
1. Eseguire `mvn liberty:dev` per testare l'applicazione. `The defaultServer server is ready to run a smarter planet.`Se l'operazione ha esito positivo, verrà visualizzato nell'output del comando. Usare `CTRL-C` per arrestare l'applicazione.
1. Eseguire uno dei comandi seguenti per compilare l'immagine dell'applicazione ed eseguirne il push nell'istanza di ACR.
   * Crea con un'immagine di base Open Liberty se preferisci usare Open Liberty come un runtime Java open source leggero™:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Compila con l'immagine di base di WebSphere Liberty se preferisci usare una versione commerciale di Open Liberty:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Distribuire un'applicazione nel cluster AKS

Attenersi alla procedura seguente per distribuire l'applicazione Liberty nel cluster AKS.

1. Creare un segreto di pull in modo che il cluster AKS venga autenticato per eseguire il pull dell'immagine dall'istanza di ACR.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Verificare che la directory di lavoro corrente sia `javaee-app-simple-cluster` del clone locale.
1. Eseguire i comandi seguenti per distribuire l'applicazione Liberty con 3 repliche nel cluster AKS. L'output del comando viene inoltre visualizzato inline.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Attendere fino a visualizzare `3/3` sotto la `READY` colonna e `3` sotto la `AVAILABLE` colonna, usare `CTRL-C` per arrestare il `kubectl` processo di controllo.

### <a name="test-the-application"></a>Testare l'applicazione

Quando l'applicazione viene eseguita, un servizio di bilanciamento del carico Kubernetes espone il front-end dell'applicazione a Internet. Il completamento di questo processo può richiedere del tempo.

Per monitorare lo stato, usare il comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) con l'argomento `--watch`.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   80:31732/TCP     68s
```

Quando l'indirizzo *IP esterno* passa da *in sospeso* a un indirizzo IP pubblico effettivo, usare `CTRL-C` per arrestare il processo di `kubectl` controllo.

Aprire un Web browser all'indirizzo IP esterno del servizio ( `52.152.189.57` per l'esempio precedente) per visualizzare l'applicazione Home page. Nella parte superiore sinistra della pagina verrà visualizzato il nome del Pod delle repliche dell'applicazione. Attendere alcuni minuti e aggiornare la pagina per visualizzare un altro nome di Pod visualizzato a causa del bilanciamento del carico fornito dal cluster AKS.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="L'applicazione Java Liberty è stata distribuita in AKS":::

>[!NOTE]
> - Attualmente l'applicazione non usa HTTPS. È consigliabile [abilitare TLS con i propri certificati](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Pulire le risorse

Per evitare addebiti per Azure, è necessario eliminare le risorse non necessarie.  Quando il cluster non è più necessario, usare il comando [AZ Group Delete](/cli/azure/group#az_group_delete) per rimuovere il gruppo di risorse, il servizio contenitore, il registro contenitori e tutte le risorse correlate.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere i riferimenti utilizzati in questa guida:

* [Servizio Azure Kubernetes](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Apri libertà](https://openliberty.io/)
* [Apri operatore Liberty](https://github.com/OpenLiberty/open-liberty-operator)
* [Apri configurazione di Liberty server](https://openliberty.io/docs/ref/config/)
* [Plug-in Liberty Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Apri le immagini del contenitore Liberty](https://github.com/OpenLiberty/ci.docker)
* [Immagini del contenitore di WebSphere Liberty](https://github.com/WASdev/ci.docker)
