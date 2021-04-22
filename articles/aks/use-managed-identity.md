---
title: Usare le identità gestite in servizio Azure Kubernetes
description: Informazioni su come usare le identità gestite in servizio Azure Kubernetes (servizio AzureKs)
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 7eb0ab6247e8afc27f938b8b4a25d1fb1ee723f4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876995"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Usare le identità gestite in servizio Azure Kubernetes

Attualmente, un cluster servizio Azure Kubernetes (servizio Azure Kubernetes) richiede un'identità per creare risorse aggiuntive come i servizi di bilanciamento del carico e i dischi gestiti in Azure. Questa identità può essere *un'identità gestita* o *un'entità servizio.* Se si usa [un'entità servizio,](kubernetes-service-principal.md)è necessario specificarne una oppure il servizio Web Del servizio Web crearne una per conto dell'utente. Se si usa l'identità gestita, l'identità verrà creata automaticamente dal servizio Web del servizio Disatteso. I cluster che usano entità servizio raggiungono infine uno stato in cui l'entità servizio deve essere rinnovata per mantenere il cluster funzionante. La gestione delle entità servizio aggiunge complessità, motivo per cui è più semplice usare le identità gestite. Gli stessi requisiti di autorizzazione si applicano sia per le entità servizio che per le identità gestite.

*Le identità gestite sono* essenzialmente un wrapper per le entità servizio e semplificano la gestione. La rotazione delle credenziali per MI viene eseguita automaticamente ogni 46 giorni in base Azure Active Directory predefinita. Il servizio AKS usa sia i tipi di identità gestita assegnati dal sistema che i tipi di identità gestiti assegnati dall'utente. Queste identità non sono attualmente modificabili. Per altre informazioni, vedere Identità [gestite per le risorse di Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="before-you-begin"></a>Prima di iniziare

È necessario avere installato la risorsa seguente:

- Interfaccia della riga di comando di Azure, versione 2.15.1 o successiva

## <a name="limitations"></a>Limitazioni

* Lo spostamento/migrazione di tenant di cluster abilitati per l'identità gestita non è supportato.
* Se il cluster è abilitato, i pod Node-Managed Identity (NMI) modificano le tabelle IP dei nodi per intercettare le chiamate all'endpoint dei metadati dell'istanza `aad-pod-identity` di Azure. Questa configurazione indica che qualsiasi richiesta effettuata all'endpoint dei metadati viene intercettata da NMI anche se il pod non usa `aad-pod-identity` . AzurePodIdentityException CRD può essere configurato per informare che tutte le richieste all'endpoint dei metadati provenienti da un pod che corrisponde alle etichette definite in CRD devono essere proxy senza alcuna elaborazione `aad-pod-identity` in NMI. I pod di sistema con etichetta nello spazio dei nomi kube-system devono essere esclusi in configurando il `kubernetes.azure.com/managedby: aks`  `aad-pod-identity` CRD AzurePodIdentityException. Per altre informazioni, vedere [Disabilitare aad-pod-identity per un pod o un'applicazione specifica.](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Per configurare un'eccezione, installare il file [YAML mic-exception.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="summary-of-managed-identities"></a>Riepilogo delle identità gestite

Il servizio AzureKs usa diverse identità gestite per i servizi e i componenti aggiuntivi predefiniti.

| Identità                       | Nome    | Caso d'uso | Autorizzazioni predefinite | Bring Your Own Identity
|----------------------------|-----------|----------|
| Piano di controllo | non visibile | Usato dai componenti del piano di controllo del servizio AzureKs per gestire le risorse del cluster, inclusi i servizi di bilanciamento del carico in ingresso e gli indirizzi IP pubblici gestiti dal servizio Azure Online, e le operazioni di scalabilità automatica del cluster | Ruolo Collaboratore per il gruppo di risorse Node | supportato
| Kubelet | Nome del cluster del servizio Web Diaks-agentpool | Autenticazione con Registro Azure Container (ACR) | NA (per Kubernetes v1.15+) | Attualmente non supportato
| Componente aggiuntivo | AzureNPM | Non è necessaria alcuna identità | N/D | No
| Componente aggiuntivo | Monitoraggio della rete AzureCNI | Non è necessaria alcuna identità | N/D | No
| Componente aggiuntivo | azure-policy (gatekeeper) | Non è necessaria alcuna identità | N/D | No
| Componente aggiuntivo | azure-policy | Non è necessaria alcuna identità | N/D | No
| Componente aggiuntivo | Calico | Non è necessaria alcuna identità | N/D | No
| Componente aggiuntivo | Dashboard | Non è richiesta alcuna identità | N/D | No
| Componente aggiuntivo | HTTPApplicationRouting | Gestisce le risorse di rete necessarie | Ruolo lettore per il gruppo di risorse del nodo, ruolo collaboratore per la zona DNS | No
| Componente aggiuntivo | Gateway applicazione in ingresso | Gestisce le risorse di rete necessarie| Ruolo collaboratore per il gruppo di risorse del nodo | No
| Componente aggiuntivo | omsagent | Usato per inviare le metriche del servizio Web del servizio Monitoraggio di Azure | Ruolo server di pubblicazione delle metriche di monitoraggio | No
| Componente aggiuntivo | Virtual-Node (ACIConnector) | Gestisce le risorse di rete necessarie per Istanze di Azure Container (ACI) | Ruolo collaboratore per il gruppo di risorse del nodo | No
| Progetto OSS | aad-pod-identity | Consente alle applicazioni di accedere alle risorse cloud in modo sicuro con Azure Active Directory (AAD) | N/D | Passaggi per concedere l'autorizzazione in https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Creare un cluster del servizio AzureKs con identità gestite

È ora possibile creare un cluster del servizio AzureKs con identità gestite usando i comandi dell'interfaccia della riga di comando seguenti.

Creare prima di tutto un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Creare quindi un cluster del servizio Servizio Web Di seguito:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Dopo aver creato il cluster, è possibile distribuire i carichi di lavoro dell'applicazione nel nuovo cluster e interagire con esso esattamente come si è fatto con i cluster del servizio Diaks basati su entità servizio.

Ottenere infine le credenziali per accedere al cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>Aggiornare un cluster del servizio AzureKs alle identità gestite (anteprima)

È ora possibile aggiornare un cluster del servizio AzureKs attualmente in uso con le entità servizio in modo che funzioni con le identità gestite usando i comandi dell'interfaccia della riga di comando seguenti.

Per prima cosa, registrare il flag di funzionalità per l'identità assegnata dal sistema:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Aggiornare l'identità assegnata dal sistema:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Registrare il flag di funzionalità per l'identità assegnata dall'utente:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Aggiornare l'identità assegnata dall'utente:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Dopo che le identità assegnate dal sistema o dall'utente sono state aggiornate all'identità gestita, eseguire un nei nodi per completare l'aggiornamento `az aks nodepool upgrade --node-image-only` all'identità gestita.

## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>Ottenere e usare l'identità gestita assegnata dal sistema per il cluster del servizio Web Diaks

Verificare che il cluster del servizio Servizio Web Di seguito sia in uso l'identità gestita con il comando dell'interfaccia della riga di comando seguente:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

Se il cluster usa identità gestite, verrà visualizzato il `clientId` valore "msi". Un cluster che usa invece un'entità servizio mostrerà invece l'ID oggetto. Ad esempio: 

```output
{
  "clientId": "msi"
}
```

Dopo aver verificato che il cluster usa identità gestite, è possibile trovare l'ID oggetto dell'identità assegnata dal sistema del piano di controllo con il comando seguente:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> Per creare e usare la propria rete virtuale, l'indirizzo IP statico o il disco di Azure collegato in cui le risorse si trova all'esterno del gruppo di risorse del nodo di lavoro, usare il Valore PrincipalID dell'identità gestita assegnata dal sistema del cluster per eseguire un'assegnazione di ruolo. Per altre informazioni sull'assegnazione di ruolo, vedere [Delegare l'accesso ad altre risorse di Azure.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> Il popolamento delle autorizzazioni all'identità gestita del cluster usato dal provider di servizi cloud di Azure può richiedere fino a 60 minuti.


## <a name="bring-your-own-control-plane-mi"></a>Bring your own control plane MI
Un'identità del piano di controllo personalizzata consente di concedere l'accesso all'identità esistente prima della creazione del cluster. Questa funzionalità consente scenari come l'uso di una rete virtuale personalizzata o outboundType di UDR con un'identità gestita creata in precedenza.

È necessario avere installato l'interfaccia della riga di comando di Azure, versione 2.15.1 o successiva.

### <a name="limitations"></a>Limitazioni
* Azure per enti pubblici non è attualmente supportato.
* Azure China (21Vianet) non è attualmente supportato.

Se non si ha ancora un'identità gestita, è consigliabile crearne una, ad esempio usando [az identity CLI.][az-identity-create]

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Il risultato dovrebbe essere simile al seguente:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Se l'identità gestita fa parte della sottoscrizione, è possibile usare il [comando az identity dell'interfaccia][az-identity-list] della riga di comando per eseguire query.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

È ora possibile usare il comando seguente per creare il cluster con l'identità esistente:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

Una creazione corretta del cluster usando le proprie identità gestite contiene le informazioni sul profilo userAssignedIdentities seguenti:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="bring-your-own-kubelet-mi-preview"></a>Bring Your Own Kubelet MI (Anteprima)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Un'identità Kubelet consente di concedere l'accesso all'identità esistente prima della creazione del cluster. Questa funzionalità consente scenari come la connessione al controllo di accesso con un'identità gestita creata in precedenza.

### <a name="prerequisites"></a>Prerequisiti

- È necessario avere installato l'interfaccia della riga di comando di Azure, versione 2.21.1 o successiva.
- È necessario avere installato aks-preview, versione 0.5.10 o successiva.

### <a name="limitations"></a>Limitazioni

- Funziona solo con un cluster User-Assigned gestito.
- Azure per enti pubblici non è attualmente supportato.
- Azure China (21Vianet) non è attualmente supportato.

Per prima cosa, registrare il flag di funzionalità per l'identità kubelet:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n CustomKubeletIdentityPreview
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomKubeletIdentityPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="create-or-obtain-managed-identities"></a>Creare o ottenere identità gestite

Se non si ha ancora un'identità gestita del piano di controllo, è consigliabile crearne una. L'esempio seguente usa [il comando az identity create:][az-identity-create]

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```

Il risultato dovrebbe essere simile al seguente:

```output
{                                  
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Se non si ha ancora un'identità gestita kubelet, è consigliabile crearne una. L'esempio seguente usa [il comando az identity create:][az-identity-create]

```azurecli-interactive
az identity create --name myKubeletIdentity --resource-group myResourceGroup
```

Il risultato dovrebbe essere simile al seguente:

```output
{
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity", 
  "location": "westus2",
  "name": "myKubeletIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Se l'identità gestita esistente fa parte della sottoscrizione, è possibile usare il [comando az identity list][az-identity-list] per eseguire una query:

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

### <a name="create-a-cluster-using-kubelet-identity"></a>Creare un cluster usando l'identità kubelet

A questo punto è possibile usare il comando seguente per creare il cluster con le identità esistenti. Specificare l'ID identità del piano di controllo `assign-identity` tramite e l'identità gestita di kubelet tramite `assign-kublet-identity` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
    --assign-kubelet-identity <kubelet-identity-id> \
```

La creazione di un cluster con la propria identità gestita kubelet contiene l'output seguente:

```output
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
        "clientId": "<client-id>",
        "principalId": "<principal-id>"
      }
    }
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "<client-id>",
      "objectId": "<object-id>",
      "resourceId": "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity"
    }
  },
```

## <a name="next-steps"></a>Passaggi successivi
* Usare [Azure Resource Manager per creare ][aks-arm-template] cluster abilitati per l'identità gestita.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-list]: /cli/azure/identity#az_identity_list
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
