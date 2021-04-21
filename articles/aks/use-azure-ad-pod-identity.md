---
title: Usare Azure Active Directory identità gestite da pod in servizio Azure Kubernetes (anteprima)
description: Informazioni su come usare le identità gestite gestite da pod di AAD in servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: f090f5e11688f35ce090bb07ec0d23530bf9d90e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777852"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Usare Azure Active Directory identità gestite da pod in servizio Azure Kubernetes (anteprima)

Azure Active Directory identità gestite da pod usa primitive Kubernetes per associare le identità gestite per le risorse e le identità di [Azure][az-managed-identities] in Azure Active Directory (AAD) ai pod. Gli amministratori creano identità e associazioni come primitive Kubernetes che consentono ai pod di accedere alle risorse di Azure che si basano su AAD come provider di identità.

> [!NOTE]
> Se si dispone di un'installazione esistente di AADPODIDENTITY, è necessario rimuovere l'installazione esistente. L'abilitazione di questa funzionalità significa che il componente MIC non è necessario.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata la risorsa seguente:

* Interfaccia della riga di comando di Azure, versione 2.20.0 o successiva
* Versione `azure-preview` dell'estensione 0.5.5 o successiva

### <a name="limitations"></a>Limitazioni

* Per un cluster sono consentite al massimo 200 identità di pod.
* Per un cluster sono consentite al massimo 200 eccezioni di identità di pod.
* Le identità gestite dal pod sono disponibili solo nei pool di nodi Linux.

### <a name="register-the-enablepodidentitypreview"></a>Registrare il `EnablePodIdentityPreview`

Registrare la funzionalità `EnablePodIdentityPreview`:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Installare l'interfaccia della riga `aks-preview` di comando di Azure

È necessaria anche *l'estensione aks-preview dell'interfaccia* della riga di comando di Azure versione 0.4.64 o successiva. Installare *l'estensione dell'interfaccia della riga* di comando di Azure aks-preview usando [il comando az extension add.][az-extension-add] Oppure installare eventuali aggiornamenti disponibili usando il [comando az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-cni"></a>Creare un cluster del servizio Web Disassoce con Azure CNI

> [!NOTE]
> Questa è la configurazione consigliata predefinita

Creare un cluster servizio AKS con Azure CNI e l'identità gestita dal pod abilitata. I comandi seguenti usano [az group create][az-group-create] per creare un gruppo di risorse denominato *myResourceGroup* e il [comando az aks create][az-aks-create] per creare un cluster del servizio AKS denominato *myAKSCluster* nel gruppo di risorse *myResourceGroup.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

Usare [az aks get-credentials][az-aks-get-credentials] per accedere al cluster del servizio AKS. Questo comando scarica e configura anche il certificato `kubectl` client nel computer di sviluppo.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Aggiornare un cluster del servizio Web del servizio Web esistente con Azure CNI

Aggiornare un cluster del servizio AKS esistente con Azure CNI per includere l'identità gestita dal pod.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --network-plugin azure
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Uso del plug-in di rete Kubenet Azure Active Directory identità gestite da pod 

> [!IMPORTANT]
> L'esecuzione di aad-pod-identity in un cluster con Kubenet non è una configurazione consigliata a causa dell'implicazione della sicurezza. Seguire i passaggi di mitigazione e configurare i criteri prima di abilitare aad-pod-identity in un cluster con Kubenet.

## <a name="mitigation"></a>Mitigazione

Per attenuare la vulnerabilità a livello di cluster, è possibile usare il controller di ammissione OpenPolicyAgent insieme a Gatekeeper che convalida il webhook. Se Gatekeeper è già installato nel cluster, aggiungere ConstraintTemplate di tipo K8sPSPCapabilities:

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Aggiungere un modello per limitare la generazione di pod con la NET_RAW funzionalità:

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>Creare un cluster del servizio AKS con il plug-in di rete Kubenet

Creare un cluster del servizio AKS con il plug-in di rete Kubenet e l'identità gestita dal pod abilitata.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Aggiornare un cluster del servizio AKS esistente con il plug-in di rete Kubenet

Aggiornare un cluster del servizio AKS esistente con il plug-in di rete Kubnet per includere l'identità gestita dal pod.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Creare un'identità

Creare un'identità usando [az identity create e][az-identity-create] impostare le IDENTITY_CLIENT_ID *e* *IDENTITY_RESOURCE_ID* variabili.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Assegnare le autorizzazioni per l'identità gestita

*L IDENTITY_CLIENT_ID'identità* gestita deve avere le autorizzazioni di lettore nel gruppo di risorse che contiene il set di scalabilità di macchine virtuali del cluster del servizio AppKs.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Creare un'identità del pod

Creare un'identità del pod per il cluster usando `az aks pod-identity add` .

> [!IMPORTANT]
> Per creare l'identità e l'associazione del ruolo, è necessario disporre delle autorizzazioni appropriate, ad esempio `Owner` , nella sottoscrizione.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Quando si abilita l'identità gestita da pod nel cluster del servizio Azure Kube, un'eccezione AzurePodIdentityException denominata *aks-addon-exception* viene aggiunta allo spazio dei nomi *kube-system.* Un'eccezione AzurePodIdentityException consente ai pod con determinate etichette di accedere all'endpoint del servizio metadati dell'istanza di Azure senza essere intercettati dal server NMI (Node-Managed Identity). *Aks-addon-exception* consente ai componenti aggiuntivi del servizio AzureKs first party, ad esempio l'identità gestita dal pod AAD, di funzionare senza dover configurare manualmente un'eccezione AzurePodIdentityException. Facoltativamente, è possibile aggiungere, rimuovere e aggiornare un'eccezione AzurePodIdentityException usando `az aks pod-identity exception add` `az aks pod-identity exception delete` , , o `az aks pod-identity exception update` `kubectl` .

## <a name="run-a-sample-application"></a>Eseguire un'applicazione di esempio

Per usare l'identità gestita dal pod AAD, un pod deve avere un'etichetta *aadpodidbinding* con un valore corrispondente a un selettore di *AzureIdentityBinding.* Per eseguire un'applicazione di esempio usando l'identità gestita da pod di AAD, creare un `demo.yaml` file con il contenuto seguente. Sostituire *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_GROUP* con i valori dei passaggi precedenti. Sostituire *SUBSCRIPTION_ID* con l'ID sottoscrizione.

> [!NOTE]
> Nei passaggi precedenti sono stati creati i *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* e *IDENTITY_RESOURCE_GROUP* variabili. È possibile usare un comando come `echo` per visualizzare il valore impostato per le variabili, ad esempio `echo $IDENTITY_NAME` .

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Si noti che la definizione del pod ha *un'etichetta aadpodidbinding* con un valore che corrisponde al nome dell'identità del pod `az aks pod-identity add` eseguita nel passaggio precedente.

Eseguire `demo.yaml` la distribuzione nello stesso spazio dei nomi dell'identità del pod usando `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Verificare che l'applicazione di esempio venga eseguita correttamente usando `kubectl logs` .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Verificare che i log mostrino che un token è stato acquisito correttamente e che *l'operazione GET* sia riuscita.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Eseguire la pulizia

Per rimuovere l'identità gestita dal pod AAD dal cluster, rimuovere l'applicazione di esempio e l'identità del pod dal cluster. Rimuovere quindi l'identità.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle identità gestite, vedere [Identità gestite per le risorse di Azure][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
