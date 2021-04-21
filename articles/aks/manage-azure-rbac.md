---
title: Gestire il controllo degli accessi in base al ruolo di Azure in Kubernetes da Azure
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes con servizio Azure Kubernetes (servizio Azure Kubernetes).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c708a577a1c2e4bb8f7ddff90f458afd0d9e566f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783000"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione di Kubernetes (anteprima)

Attualmente è già possibile sfruttare [l'autenticazione integrata tra Azure Active Directory (Azure AD) e servizio AKS.](managed-aad.md) Se abilitata, questa integrazione consente ai clienti di usare Azure AD utenti, gruppi o entità servizio come argomenti nel controllo degli accessi in base al ruolo di Kubernetes. Vedere altre informazioni [qui.](azure-ad-rbac.md)
Questa funzionalità consente di gestire separatamente le identità utente e le credenziali per Kubernetes. Tuttavia, è comunque necessario configurare e gestire il controllo degli accessi in base al ruolo di Azure e il controllo degli accessi in base al ruolo di Kubernetes separatamente. Per altri dettagli sull'autenticazione e l'autorizzazione con il controllo degli accessi in base al ruolo nel servizio Web Disatteso, vedere [qui](concepts-identity.md).

Questo documento illustra un nuovo approccio che consente la gestione unificata e il controllo di accesso tra risorse di Azure, servizio Azure Kubernetes e risorse Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

La possibilità di gestire il controllo degli accessi in base al ruolo per le risorse Kubernetes da Azure offre la possibilità di gestire il controllo degli accessi in base al ruolo per le risorse del cluster usando i meccanismi di Azure o Kubernetes nativi. Se abilitata, le entità Azure AD verranno convalidate esclusivamente dal controllo degli accessi in base al ruolo di Azure, mentre gli utenti e gli account del servizio Kubernetes regolari vengono convalidati esclusivamente dal controllo degli accessi in base al ruolo di Kubernetes. Per altri dettagli sull'autenticazione e l'autorizzazione con il controllo degli accessi in base al ruolo nel servizio Web Disatteso, vedere [qui](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Prerequisiti 
- Assicurarsi di avere l'interfaccia della riga di comando di Azure versione 2.9.0 o successiva
- Assicurarsi di avere abilitato `EnableAzureRBACPreview` il flag di funzionalità.
- Assicurarsi di avere installato l'estensione dell'interfaccia della riga di comando `aks-preview` [][az-extension-add] v0.4.55 o versione successiva
- Assicurarsi di aver installato [kubectl v1.18.3+][az-aks-install-cli].

#### <a name="register-enableazurerbacpreview-preview-feature"></a>Registrare `EnableAzureRBACPreview` la funzionalità di anteprima

Per creare un cluster del servizio Azure Kubernetes che usa il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes, è necessario abilitare il `EnableAzureRBACPreview` flag di funzionalità nella sottoscrizione.

Registrare `EnableAzureRBACPreview` il flag di funzionalità usando il comando [az feature register,][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Al termine, aggiornare la registrazione del provider *di risorse Microsoft.ContainerService* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster del servizio AzureKs che usa il controllo degli accessi in base al ruolo di Azure, è necessaria l'estensione dell'interfaccia della riga di comando *aks-preview* versione 0.4.55 o successiva. Installare *l'estensione dell'interfaccia* della riga di comando di Azure aks-preview usando il [comando az extension add][az-extension-add] oppure installare gli aggiornamenti disponibili usando il comando [az extension update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitazioni

- Richiede [l'integrazione Azure AD gestita.](managed-aad.md)
- Non è possibile integrare l'autorizzazione di Controllo degli accessi in base al ruolo di Azure per Kubernetes nei cluster esistenti durante l'anteprima, ma sarà possibile usare la disponibilità generale.
- Usare [kubectl v1.18.3+][az-aks-install-cli].
- Se si dispone di CRD e si stanno effettuando definizioni di ruolo personalizzate, l'unico modo per coprire i CRD attualmente è fornire `Microsoft.ContainerService/managedClusters/*/read` . AKS sta lavorando per fornire autorizzazioni più granulari per i CRD. Per gli oggetti rimanenti è possibile usare gruppi di API specifici, ad esempio: `Microsoft.ContainerService/apps/deployments/read` .
- La propagazione e l'aggiornamento delle nuove assegnazioni di ruolo possono richiedere fino a 5 minuti dal server di autorizzazione.
- Richiede che Azure AD tenant configurato per l'autenticazione sia lo stesso del tenant per la sottoscrizione che contiene il cluster del servizio AzureKs. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Creare un nuovo cluster usando il controllo degli accessi in base al ruolo di Azure e l'Azure AD gestita

Creare un cluster del servizio Web Diaks usando i comandi dell'interfaccia della riga di comando seguenti.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Creare il cluster del servizio Azure Kubernetes con l'integrazione Azure AD e il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Una creazione corretta di un cluster con integrazione Azure AD e controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes include la sezione seguente nel corpo della risposta:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Creare assegnazioni di ruolo per consentire agli utenti di accedere al cluster

Il servizio AKS offre i quattro ruoli predefiniti seguenti:


| Ruolo                                | Descrizione  |
|-------------------------------------|--------------|
| servizio Azure Kubernetes lettore controllo degli accessi in base al ruolo  | Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. Non consente la visualizzazione di ruoli o associazioni di ruolo. Questo ruolo non consente la visualizzazione di , perché la lettura del contenuto dei segreti consente l'accesso alle credenziali ServiceAccount nello spazio dei nomi , che consentirebbe l'accesso all'API come qualsiasi ServiceAccount nello spazio dei nomi (una forma di escalation dei `Secrets` privilegi)  |
| servizio Azure Kubernetes RBAC Writer | Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. Questo ruolo non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. Tuttavia, questo ruolo consente l'accesso e l'esecuzione di pod come qualsiasi ServiceAccount nello spazio dei nomi, quindi può essere usato per ottenere i livelli di accesso API di qualsiasi ServiceAccount nello `Secrets` spazio dei nomi. |
| servizio Azure Kubernetes amministratore del controllo degli accessi in base al ruolo  | Consente l'accesso amministratore, destinato a essere concesso all'interno di uno spazio dei nomi. Consente l'accesso in lettura/scrittura alla maggior parte delle risorse in uno spazio dei nomi (o nell'ambito del cluster), inclusa la possibilità di creare ruoli e associazioni di ruolo all'interno dello spazio dei nomi. Questo ruolo non consente l'accesso in scrittura alla quota di risorse o allo spazio dei nomi stesso. |
| servizio Azure Kubernetes amministratore del cluster controllo degli accessi in base al ruolo  | Consente l'accesso utente con privilegi più utenti per eseguire qualsiasi azione su qualsiasi risorsa. Offre il controllo completo su ogni risorsa nel cluster e in tutti gli spazi dei nomi. |


Le assegnazioni di ruoli con ambito all'intero cluster del servizio **AzureKs** possono essere eseguite nel pannello Controllo di accesso (IAM) della risorsa cluster in portale di Azure o usando i comandi dell'interfaccia della riga di comando di Azure, come illustrato di seguito:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

dove `<AAD-ENTITY-ID>` può essere un nome utente (ad esempio , ) o anche il user@contoso.com ClientID di un'entità servizio.

È anche possibile creare assegnazioni di ruolo con ambito a uno spazio dei **nomi specifico** all'interno del cluster:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Attualmente, le assegnazioni di ruolo con ambito per gli spazi dei nomi devono essere configurate tramite l'interfaccia della riga di comando di Azure.


### <a name="create-custom-roles-definitions"></a>Creare definizioni di ruoli personalizzati

Facoltativamente, è possibile scegliere di creare una definizione di ruolo e quindi assegnarla come indicato in precedenza.

Di seguito è riportato un esempio di definizione del ruolo che consente a un utente di leggere solo le distribuzioni e altro ancora. È possibile controllare l'elenco completo delle azioni [possibili qui.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


Copiare il codice JSON seguente in un file denominato `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

Sostituire `<YOUR SUBSCRIPTION ID>` con l'ID della sottoscrizione, che è possibile ottenere eseguendo:

```azurecli-interactive
az account show --query id -o tsv
```


A questo punto è possibile creare la definizione del ruolo eseguendo il comando seguente dalla cartella in cui è stato salvato `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Dopo aver creato la definizione del ruolo, è possibile assegnarla a un utente o a un'altra identità eseguendo:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes con `kubectl`

> [!NOTE]
> Assicurarsi di avere la versione più recente di kubectl eseguendo il comando seguente:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Potrebbe essere necessario eseguirlo `sudo` con privilegi. 

Ora che sono stati assegnati il ruolo e le autorizzazioni desiderati. È possibile iniziare a chiamare l'API Kubernetes, ad esempio da `kubectl` .

A questo scopo, si otterrà prima di tutto il file kubeconfig del cluster usando il comando seguente:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Per eseguire il passaggio precedente [è servizio Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) ruolo predefinito Utente cluster.

È ora possibile usare kubectl per elencare, ad esempio, i nodi nel cluster. La prima volta che lo si esegue, sarà necessario eseguire l'accesso e i comandi successivi useranno il rispettivo token di accesso.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes con `kubelogin`

Per sbloccare altri scenari, ad esempio gli account di accesso non interattivi, le versioni precedenti o l'uso dell'accesso SSO tra più cluster senza la necessità di accedere a un nuovo cluster, con la concessione che il token sia ancora valido, il servizio AKS ha creato un plug-in `kubectl` exec denominato [`kubelogin`](https://github.com/Azure/kubelogin) .

È possibile usarlo eseguendo:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

La prima volta, sarà necessario accedere in modo interattivo come con il normale kubectl, ma in seguito non sarà più necessario, neanche per i nuovi cluster Azure AD (purché il token sia ancora valido).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Eseguire la pulizia

### <a name="clean-role-assignment"></a>Pulire l'assegnazione di ruolo

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Copiare l'ID o gli ID da tutte le assegnazioni e quindi.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Pulire la definizione del ruolo

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Eliminare il cluster e il gruppo di risorse

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'autenticazione del servizio Azure Kubernetes, l'autorizzazione, il controllo degli accessi in base al ruolo di Kubernetes e il controllo degli accessi in base al ruolo di Azure [sono qui.](concepts-identity.md)
- Per altre informazioni sul controllo degli accessi in base al ruolo [di Azure, vedere qui](../role-based-access-control/overview.md).
- Altre informazioni su tutte le azioni che è possibile usare per definire in modo granulare i ruoli di Azure personalizzati per l'autorizzazione Kubernetes [sono disponibili qui.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
