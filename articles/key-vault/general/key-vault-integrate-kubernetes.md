---
title: Integrare Azure Key Vault con Kubernetes
description: Questa esercitazione illustra come accedere e recuperare segreti da Azure Key Vault usando il driver CSI (Container Storage Interface) dell'archivio segreti per poi montarli nei pod Kubernetes.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: defe546c008f741040c78e639d5bc4b9c6e02fb8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741447"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Esercitazione: Configurare ed eseguire il provider di Azure Key Vault per il driver CSI dell'archivio di segreti in Kubernetes

> [!IMPORTANT]
> Il driver CSI dell'archivio segreti è un progetto open source non supportato dal supporto tecnico di Azure. Segnalare eventuali commenti, suggerimenti e problemi correlati all'integrazione di Key Vault del driver CSI nel collegamento GitHub nella parte inferiore della pagina. Questo strumento è disponibile per consentire agli utenti di eseguire l'installazione autonoma nei cluster e di ottenere commenti e suggerimenti dalla community.

Questa esercitazione illustra come accedere e recuperare segreti da Azure Key Vault usando il driver CSI (Container Storage Interface) dell'archivio segreti per poi montarli nei pod Kubernetes.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Usare identità gestite.
> * Distribuire un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure.
> * Installare Helm, il driver CSI dell'archivio segreti e il provider di Azure Key Vault per il driver CSI.
> * Creare un'istanza di Azure Key Vault e impostare i segreti.
> * Creare un oggetto SecretProviderClass personale.
> * Distribuire il pod con i segreti montati da Key Vault.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Prima di iniziare questa esercitazione, installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

Questa esercitazione presuppone che l'utente esegua il servizio Azure Kubernetes nei nodi Linux.

## <a name="use-managed-identities"></a>Usare identità gestite

Questo diagramma illustra il flusso di integrazione dell'insieme di credenziali delle chiavi del servizio Azure Kubernetes per l'identità gestita:

![Diagramma che mostra il flusso di integrazione dell'insieme di credenziali delle chiavi del servizio Azure Kubernetes per l'identità gestita](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Distribuire un cluster del servizio Azure Kubernetes tramite l'interfaccia della riga di comando di Azure

Non è necessario usare Azure Cloud Shell. Sarà sufficiente usare il prompt dei comandi (terminale) con l'interfaccia della riga di comando di Azure installata. 

Completare le sezioni "Creare un gruppo di risorse", "Creare un cluster del servizio Azure Kubernetes" e "Connettersi al cluster" dell'articolo [Distribuire un cluster del servizio Azure Kubernetes con l'interfaccia della riga di comando di Azure](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Se si prevede di usare l'identità Pod, il plug-in di rete consigliato è `azure` . Per ulteriori informazioni, fare riferimento alla [documentazione](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) . Creare il cluster Kubernetes, come illustrato nel comando seguente:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Impostare la variabile di ambiente PATH](https://www.java.com/en/download/help/path.xml) sul file *kubectl.exe* scaricato.
2. Controllare la versione di Kubernetes usando il comando seguente, che restituisce la versione del client e del server. La versione del client è il file *kubectl.exe* installato mentre la versione del server è il servizio Azure Kubernetes in cui è in esecuzione il cluster.
    ```azurecli
    kubectl version
    ```
3. Assicurarsi che la versione di Kubernetes sia 1.16.0 o successiva. Per i cluster Windows assicurarsi che la versione di Kubernetes sia 1.18.0 o successiva. Il comando seguente aggiorna sia il cluster Kubernetes che il pool di nodi. L'esecuzione del comando potrebbe richiedere un paio di minuti. In questo esempio il gruppo di risorse è *contosoResourceGroup* e il cluster Kubernetes è *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. Per visualizzare i metadati del cluster del servizio Azure Kubernetes creato, usare il comando seguente. Copiare i valori di **principalId**, **clientId**, **subscriptionId** e **nodeResourceGroup** per un uso successivo. Se il cluster del servizio Azure Kubernetes non è stato creato con le identità gestite abilitate, **principalId** e **clientId** saranno Null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    L'output mostra entrambi i parametri evidenziati:
    
    ![Screenshot dell'interfaccia della riga di comando di Azure con valori di principalId e clientId evidenziati](../media/kubernetes-key-vault-2.png) ![Screenshot dell'interfaccia della riga di comando di Azure con i valori di subscriptionId e nodeResourceGroup evidenziati](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Installare Helm e il driver CSI dell'archivio segreti
> [!NOTE]
> L'installazione seguente funziona solo su AKS in Linux. Per ulteriori informazioni sull'installazione dei driver CSI nell'archivio dei segreti, vedere [provider di Azure Key Vault per il driver CSI dell'archivio segreti](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/) 

Per installare il driver CSI del negozio Secrets e il provider di Azure Key Vault, è necessario prima installare [Helm](https://helm.sh/docs/intro/install/).

Con l'interfaccia del driver [CSI Store di Secrets](https://azure.github.io/secrets-store-csi-driver-provider-azure/) , è possibile ottenere i segreti archiviati nell'istanza di Azure Key Vault e quindi usare l'interfaccia del driver per montare il contenuto del segreto in pod Kubernetes.

1. Verificare che la versione di Helm sia v3 o successiva:
    ```azurecli
    helm version
    ```
1. Installare il driver CSI dell'archivio di segreti e il provider Azure Key Vault per il driver:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Se si prevede di usare i driver CSI dell'archivio segreti e il provider Azure Key Vault nei nodi di Windows, abilitare il driver e il provider nei nodi Windows usando i [valori di configurazione di Helm](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration)

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Creare un'istanza di Azure Key Vault e impostare i segreti

Per creare un'istanza di Key Vault e impostare i segreti, seguire le istruzioni riportate in [Impostare e recuperare un segreto da Azure Key Vault con l'interfaccia della riga di comando di Azure](../secrets/quick-create-cli.md).

> [!NOTE] 
> Non è necessario usare Azure Cloud Shell o creare un nuovo gruppo di risorse. È possibile usare il gruppo di risorse creato in precedenza per il cluster Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Creare un oggetto SecretProviderClass personale

Per creare un oggetto SecretProviderClass personalizzato con parametri specifici del provider per il driver CSI dell'archivio segreti, [usare questo modello](https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/examples/pod-identity/v1alpha1_secretproviderclass_pod_identity.yaml). Questo oggetto fornirà all'identità l'accesso all'istanza di Key Vault.

Nel file YAML SecretProviderClass di esempio compilare i parametri mancanti. I parametri seguenti sono obbligatori:

* **keyvaultName**: il nome dell'istanza di Key Vault
* **oggetti**: elenco di tutti i contenuti del segreto che si desidera montare
    * **objectName**: il nome del contenuto dei segreti
    * **objectType**: il tipo di oggetto (segreto, chiave, certificato)
* **tenantID**: l'ID tenant, ID directory, dell'istanza di Key Vault

La documentazione di tutti i campi obbligatori e le configurazioni supportate sono disponibili qui: [collegamento](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

Il modello aggiornato è mostrato nel codice seguente. Scaricarlo come file YAML e compilare i campi obbligatori. In questo esempio l'istanza di Key Vault è **contosoKeyVault5**. Contiene due segreti, **secret1** e **secret2**.

> [!NOTE] 
> Se si usano le identità Pod, impostare il valore di **usePodIdentity** su *true* e impostare il valore **userAssignedIdentityID** come coppia di virgolette (**""**). Se si usano identità gestite, impostare il valore di **useVMManagedIdentity** su *true* e impostare il valore **userAssignedIdentityID** come ClientID dell'identità assegnata dall'utente.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
L'immagine seguente mostra l'output della console per **az keyvault show --name contosoKeyVault5** con i metadati pertinenti evidenziati:

![Screenshot che mostra l'output della console per "az keyvault show --name contosoKeyVault5"](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Installa Azure Active Directory (Azure AD) identità Pod

1. Assegnare ruoli specifici al cluster del servizio Azure Kubernetes creato. 

    La documentazione per tutte le assegnazioni di ruolo obbligatorie con Azure Active Directory (Azure AD) identità Pod è disponibile qui: [collegamento](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

2. Installare l'identità di Azure Active Directory (Azure AD) nel servizio Azure Kubernetes.

    > [!NOTE] 
    > Se si usa un cluster AKS con il plug-in di rete kubenet, vedere questo [documento](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) su come distribuire l'identità Pod nel cluster.

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. Creare un'identità gestita User-Assigned. Nell'output copiare i **ClientID** per un uso successivo.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. Concedere le autorizzazioni di identità per ottenere i segreti dall'insieme di credenziali delle chiavi. Usare il **ClientID** dall'identità gestita assegnata dall'utente.
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Distribuire il pod con i segreti montati da Key Vault

Per configurare l'oggetto SecretProviderClass, eseguire il comando seguente:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Usare identità gestite

Se si usano le identità Pod, creare un *AzureIdentity* nel cluster che faccia riferimento all'identità creata in precedenza. Quindi, creare un oggetto *AzureIdentityBinding* che fa riferimento all'oggetto AzureIdentity creato. Compilare i parametri nel modello seguente e salvarlo come *podIdentityAndBinding.yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Eseguire il comando seguente per configurare l'associazione:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Successivamente, distribuire il pod. Il codice seguente corrisponde al file YAML di distribuzione, che usa il binding dell'identità del pod del passaggio precedente. Salvare il file come *podBindingDeployment.yaml*.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
```

Eseguire il comando seguente per distribuire il pod:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>Controllare lo stato del pod e il contenuto dei segreti 

Per visualizzare i pod distribuiti, eseguire il comando seguente:
```azurecli
kubectl get pods
```

Per controllare lo stato del pod, eseguire il comando seguente:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Screenshot dell'output dell'interfaccia della riga di comando di Azure che visualizza lo stato "In esecuzione" del pod e mostra tutti gli eventi come "Normale" ](../media/kubernetes-key-vault-6.png)

Nella finestra di output lo stato del pod distribuito dovrà essere *In esecuzione*. Nella sezione **Eventi** in basso tutti i tipi di evento vengono visualizzati come *Normale*.

Dopo aver verificato che il pod è in esecuzione, è possibile verificare che contenga i segreti dell'istanza di Key Vault.

Per visualizzare tutti i segreti contenuti nel pod, eseguire il comando seguente:
```azurecli
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Per visualizzare il contenuto di un segreto specifico, eseguire il comando seguente:
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Verificare che il contenuto del segreto sia visualizzato.

## <a name="next-steps"></a>Passaggi successivi

Per assicurarsi che l'istanza di Key Vault sia recuperabile, vedere:
> [!div class="nextstepaction"]
> [Abilitare l'eliminazione temporanea](./key-vault-recovery.md)
