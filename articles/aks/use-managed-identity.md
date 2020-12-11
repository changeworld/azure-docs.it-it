---
title: Usare identità gestite in Azure Kubernetes Service
description: Informazioni su come usare le identità gestite in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/06/2020
ms.openlocfilehash: 68d8111da5ec10f23d14b375a18229bca075da84
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97026830"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Usare identità gestite in Azure Kubernetes Service

Attualmente, un cluster Azure Kubernetes Service (AKS) (in particolare, il provider di cloud Kubernetes) richiede un'identità per creare risorse aggiuntive, ad esempio i servizi di bilanciamento del carico e i dischi gestiti in Azure. Questa identità può essere un' *identità gestita* o un' *entità servizio*. Se si usa un' [entità servizio](kubernetes-service-principal.md), è necessario specificare una o AKS per crearne una per conto dell'utente. Se si usa l'identità gestita, questa verrà creata automaticamente da AKS. I cluster che usano entità servizio raggiungono infine uno stato in cui l'entità servizio deve essere rinnovata per tenere il cluster funzionante. La gestione delle entità servizio aggiunge complessità, motivo per cui è più facile usare le identità gestite. Gli stessi requisiti di autorizzazione si applicano sia per le entità servizio sia per le identità gestite.

Le *identità gestite* sono essenzialmente un wrapper per le entità servizio e semplificano la gestione. La rotazione delle credenziali per MI viene eseguita automaticamente ogni 46 giorni in base Azure Active Directory valore predefinito. AKS USA sia i tipi di identità gestiti assegnati dal sistema che quelli assegnati dall'utente. Queste identità non sono attualmente modificabili. Per altre informazioni, vedere informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata la seguente risorsa:

- INTERFACCIA della riga di comando di Azure, versione 2.15.1 o successiva

## <a name="limitations"></a>Limitazioni

* Durante le operazioni di **aggiornamento** del cluster, l'identità gestita è temporaneamente non disponibile.
* Lo spostamento/migrazione dei tenant di cluster abilitati per identità gestite non è supportato.
* Se il cluster ha `aad-pod-identity` abilitato, i pod di identità Node-Managed (NMI) modificano i iptables dei nodi per intercettare le chiamate all'endpoint dei metadati dell'istanza di Azure. Questa configurazione significa che tutte le richieste effettuate all'endpoint dei metadati vengono intercettate da NMI anche se il Pod non lo usa `aad-pod-identity` . AzurePodIdentityException CRD può essere configurato per informare `aad-pod-identity` che qualsiasi richiesta all'endpoint di metadati originata da un pod che corrisponde alle etichette definite in CRD deve essere inoltrata senza alcuna elaborazione in NMI. I pod di sistema con `kubernetes.azure.com/managedby: aks` etichetta nello spazio dei nomi _Kube-System_ devono essere esclusi in `aad-pod-identity` tramite la configurazione di AzurePodIdentityException CRD. Per altre informazioni, vedere [disabilitare AAD-Pod-Identity per un pod o un'applicazione specifica](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Per configurare un'eccezione, installare la [YAML di eccezione MIC](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Riepilogo delle identità gestite

AKS usa diverse identità gestite per i servizi e i componenti aggiuntivi predefiniti.

| Identità                       | Name    | Caso d'uso | Autorizzazioni predefinite | Porta la tua identità
|----------------------------|-----------|----------|
| Piano di controllo | non visibile | Usato dai componenti del piano di controllo AKS per gestire le risorse del cluster, inclusi i bilanciamenti del carico in ingresso e gli indirizzi IP pubblici gestiti da AKS e le operazioni di scalabilità automatica del cluster | Ruolo Collaboratore per il gruppo di risorse nodo | Anteprima
| Kubelet | Nome del cluster AKS-agentpool | Autenticazione con Container Registry di Azure (ACR) | NA (per kubernetes v 1.15 +) | Attualmente non supportato
| Componente aggiuntivo | AzureNPM | Non è richiesta alcuna identità | N/D | No
| Componente aggiuntivo | Monitoraggio della rete AzureCNI | Non è richiesta alcuna identità | N/D | No
| Componente aggiuntivo | Azure-criteri (Gatekeeper) | Non è richiesta alcuna identità | N/D | No
| Componente aggiuntivo | criteri di Azure | Non è richiesta alcuna identità | N/D | No
| Componente aggiuntivo | Calico | Non è richiesta alcuna identità | N/D | No
| Componente aggiuntivo | Dashboard | Non è richiesta alcuna identità | N/D | No
| Componente aggiuntivo | HTTPApplicationRouting | Gestisce le risorse di rete necessarie | Ruolo Reader per il gruppo di risorse nodo, ruolo Collaboratore per la zona DNS | No
| Componente aggiuntivo | Gateway applicazione in ingresso | Gestisce le risorse di rete necessarie| Ruolo Collaboratore per il gruppo di risorse nodo | No
| Componente aggiuntivo | omsagent | Usato per inviare metriche AKS a monitoraggio di Azure | Monitoraggio del ruolo server di pubblicazione metrica | No
| Componente aggiuntivo | Virtual-Node (ACIConnector) | Gestisce le risorse di rete necessarie per istanze di contenitore di Azure | Ruolo Collaboratore per il gruppo di risorse nodo | No
| Progetto OSS | AAD-Pod-identità | Consente alle applicazioni di accedere in modo sicuro alle risorse cloud con Azure Active Directory (AAD) | N/D | Passaggi per concedere l'autorizzazione in https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Creare un cluster AKS con identità gestite

È ora possibile creare un cluster AKS con identità gestite usando i comandi dell'interfaccia della riga di comando seguenti.

Creare prima di tutto un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Quindi, creare un cluster AKS:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

La creazione di un cluster con identità gestite contiene le informazioni sul profilo dell'entità servizio seguenti:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Usare il comando seguente per eseguire una query su ObjectID dell'identità gestita del piano di controllo:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Il risultato dovrebbe essere simile al seguente:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Una volta creato il cluster, è possibile distribuire i carichi di lavoro dell'applicazione nel nuovo cluster e interagire con esso come è stato fatto con i cluster AKS basati su entità servizio.

> [!NOTE]
> Per creare e usare il proprio VNet, un indirizzo IP statico o un disco di Azure collegato dove le risorse si trovano all'esterno del gruppo di risorse del nodo di lavoro, usare il PrincipalID dell'identità gestita assegnata dal sistema cluster per eseguire un'assegnazione di ruolo. Per altre informazioni sull'assegnazione di ruolo, vedere [delegare l'accesso ad altre risorse di Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Le concessioni di autorizzazioni per l'identità gestita del cluster usata dal provider di servizi cloud di Azure possono richiedere fino a 60 minuti.

Ottenere infine le credenziali per accedere al cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>Aggiornare un cluster AKS a identità gestite (anteprima)

È ora possibile aggiornare un cluster AKS attualmente in uso con le entità servizio per lavorare con le identità gestite usando i comandi dell'interfaccia della riga di comando seguenti.

Registrare innanzitutto il flag funzionalità per l'identità assegnata dal sistema:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Aggiornare l'identità assegnata dal sistema:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Registrare il flag funzionalità per l'identità assegnata dall'utente:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Aggiornare l'identità assegnata dall'utente:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Quando le identità assegnate dal sistema o assegnate dall'utente sono state aggiornate a identità gestite, eseguire un `az nodepool upgrade --node-image-only` nei nodi per completare l'aggiornamento all'identità gestita.

## <a name="bring-your-own-control-plane-mi"></a>Usa il tuo piano di controllo
Un'identità del piano di controllo personalizzato consente di concedere l'accesso all'identità esistente prima della creazione del cluster. Questa funzionalità consente scenari come l'uso di un VNET personalizzato o outboundType di UDR con un'identità gestita creata in precedenza.

È necessario avere installato l'interfaccia della riga di comando di Azure versione 2.15.1 o successiva.

### <a name="limitations"></a>Limitazioni
* Azure Government attualmente non è supportato.
* Azure Cina 21Vianet attualmente non è supportato.

Se non si ha ancora un'identità gestita, è necessario crearne una, ad esempio usando [AZ Identity CLI][az-identity-create].

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

Se l'identità gestita fa parte della sottoscrizione, è possibile usare il [comando AZ Identity CLI][az-identity-list] per eseguire una query su di essa.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

A questo punto è possibile usare il comando seguente per creare il cluster con l'identità esistente:

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

La creazione di un cluster con le proprie identità gestite contiene le informazioni sul profilo userAssignedIdentities:

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

## <a name="next-steps"></a>Passaggi successivi
* Usare i [modelli di Azure Resource Manager (ARM) ][aks-arm-template] per creare cluster abilitati per l'identità gestita.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create&preserve-view=true
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list&preserve-view=true
