---
title: Usare il controllo degli accessi in base al ruolo di Azure AD e Kubernetes per i cluster
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare l'appartenenza Azure Active Directory gruppo per limitare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo di Kubernetes in servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 0d5171e9e9a5d7f033ff615a3f1205b8dc93966f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769554"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo kubernetes e Azure Active Directory identità in servizio Azure Kubernetes

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione si accede a un cluster del servizio Web Del servizio Azure AD token di autenticazione. È anche possibile configurare il controllo degli accessi in base al ruolo di Kubernetes per limitare l'accesso alle risorse del cluster in base all'identità o all'appartenenza a gruppi di un utente.

Questo articolo illustra come usare l'appartenenza Azure AD gruppo per controllare l'accesso agli spazi dei nomi e alle risorse del cluster usando il controllo degli accessi in base al ruolo di Kubernetes in un cluster del servizio Kubernetes. I gruppi e gli utenti di esempio vengono creati in Azure AD, quindi vengono creati ruoli e roleBindings nel cluster del servizio Web AKS per concedere le autorizzazioni appropriate per creare e visualizzare le risorse.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia abilitato un cluster del servizio Web del servizio Web di Microsoft Azure AD'integrazione. Se è necessario un cluster del servizio AKS, vedere Integrare Azure Active Directory con il servizio [AKS.][azure-ad-aks-cli]

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.61 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Creare gruppi demo in Azure AD

Questo articolo illustra come creare due ruoli utente che possono essere usati per illustrare il controllo degli accessi in base al ruolo di Kubernetes e Azure AD l'accesso alle risorse del cluster. Vengono usati i due ruoli di esempio seguenti:

* **Sviluppatore di applicazioni**
    * Un utente denominato *aksdev* che fa parte del *gruppo appdev.*
* **Tecnico dell'affidabilità del sito**
    * Un utente denominato *akssre* che fa parte del *gruppo opssre.*

Negli ambienti di produzione è possibile usare utenti e gruppi esistenti all'interno di Azure AD tenant.

Per prima cosa, ottenere l'ID risorsa del cluster del servizio Web Disaks usando [il comando az aks show.][az-aks-show] Assegnare l'ID risorsa a una variabile denominata *AKS_ID* in modo che sia possibile farvi riferimento in comandi aggiuntivi.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Creare il primo gruppo di esempio in Azure AD per gli sviluppatori di applicazioni usando il [comando az ad group create.][az-ad-group-create] L'esempio seguente crea un gruppo denominato *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Creare ora un'assegnazione di ruolo di Azure per *il gruppo appdev* usando il [comando az role assignment create.][az-role-assignment-create] Questa assegnazione consente a qualsiasi membro del gruppo di interagire con un cluster del servizio Servizio Azure Kubernetes AKS concedendo loro il ruolo utente `kubectl` *del cluster*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se viene visualizzato un errore, ad esempio , attendere alcuni secondi che l'ID oggetto gruppo di Azure AD venga propagato nella directory, quindi `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` `az role assignment create` riprovare a eseguire il comando.

Creare un secondo gruppo di esempio, questo per gli SRE denominato *opssre:*

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Anche in questo caso, creare un'assegnazione di ruolo di Azure per concedere ai membri del gruppo *servizio Azure Kubernetes ruolo utente del cluster:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Creare utenti demo in Azure AD

Con due gruppi di esempio creati in Azure AD per gli sviluppatori di applicazioni e gli SRE, è ora possibile creare due utenti di esempio. Per testare l'integrazione del controllo degli accessi in base al ruolo di Kubernetes alla fine dell'articolo, accedere al cluster del servizio Kubernetes con questi account.

Impostare il nome dell'entità utente (UPN) e la password per gli sviluppatori di applicazioni. Il comando seguente richiede l'UPN e lo imposta su *AAD_DEV_UPN* per l'uso in un comando successivo (tenere presente che i comandi in questo articolo vengono immessi in una shell BASH). L'UPN deve includere il nome di dominio verificato del tenant, ad esempio `aksdev@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

Il comando seguente richiede la password e la imposta su AAD_DEV_PW *per* l'uso in un comando successivo.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Creare il primo account utente in Azure AD il [comando az ad user create.][az-ad-user-create]

L'esempio seguente crea un utente con il nome visualizzato *AKS Dev* e l'UPN e la password sicura usando i valori *in* AAD_DEV_UPN e *AAD_DEV_PW*:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Aggiungere ora l'utente al *gruppo appdev* creato nella sezione precedente usando il [comando az ad group member add:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Impostare l'UPN e la password per gli SRE. Il comando seguente richiede l'UPN e lo imposta su *AAD_SRE_UPN* per l'uso in un comando successivo (tenere presente che i comandi in questo articolo vengono immessi in una shell BASH). L'UPN deve includere il nome di dominio verificato del tenant, ad esempio `akssre@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

Il comando seguente richiede la password e la imposta su AAD_SRE_PW *per* l'uso in un comando successivo.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Creare un secondo account utente. L'esempio seguente crea un utente con il nome visualizzato *AKS SRE* e l'UPN e la password sicura usando i valori *in* AAD_SRE_UPN e *AAD_SRE_PW*:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name $AAD_SRE_UPN \
  --password $AAD_SRE_PW \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Creare le risorse del cluster del servizio AppKs per gli sviluppatori di app

I Azure AD e gli utenti vengono ora creati. Sono state create assegnazioni di ruolo di Azure per la connessione dei membri del gruppo a un cluster del servizio AzureKs come utente normale. A questo punto, si configurerà il cluster del servizio AKS per consentire a questi gruppi diversi di accedere a risorse specifiche.

Per prima cosa, ottenere le credenziali di amministratore del cluster usando [il comando az aks get-credentials.][az-aks-get-credentials] In una delle sezioni seguenti si ottengono le normali credenziali del *cluster* utente per visualizzare il flusso di autenticazione Azure AD in azione.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Creare uno spazio dei nomi nel cluster del servizio Ku-Kubak usando il [comando kubectl create namespace.][kubectl-create] L'esempio seguente crea un nome di spazio dei nomi *dev*:

```console
kubectl create namespace dev
```

In Kubernetes i ruoli *definiscono* le autorizzazioni da concedere e *RoleBindings* le applica agli utenti o ai gruppi desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [Uso dell'autorizzazione per il controllo degli accessi in base al ruolo di Kubernetes.][rbac-authorization]

Creare prima di tutto un ruolo per lo spazio *dei nomi dev.* Questo ruolo concede autorizzazioni complete allo spazio dei nomi. Negli ambienti di produzione è possibile specificare autorizzazioni più granulari per utenti o gruppi diversi.

Creare un file denominato `role-dev-namespace.yaml` e incollare il manifesto YAML seguente:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Creare il ruolo usando il [comando kubectl apply][kubectl-apply] e specificare il nome file del manifesto YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Ottenere quindi l'ID risorsa per il *gruppo appdev* usando il [comando az ad group show.][az-ad-group-show] Questo gruppo viene impostato come oggetto di un RoleBinding nel passaggio successivo.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Creare ora un RoleBinding per il gruppo *appdev* per usare il ruolo creato in precedenza per l'accesso allo spazio dei nomi. Creare un file denominato `rolebinding-dev-namespace.yaml` e incollare il manifesto YAML seguente. Nell'ultima riga sostituire *groupObjectId con*  l'ID oggetto gruppo restituito dal comando precedente:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Creare roleBinding usando il [comando kubectl apply][kubectl-apply] e specificare il nome file del manifesto YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Creare le risorse del cluster del servizio Servizio Web di Gestione risorse per gli SRE

Ripetere ora i passaggi precedenti per creare uno spazio dei nomi, Un ruolo e un RoleBinding per gli SRE.

Creare prima di tutto uno spazio dei nomi *per sre* usando il [comando kubectl create namespace:][kubectl-create]

```console
kubectl create namespace sre
```

Creare un file denominato `role-sre-namespace.yaml` e incollare il manifesto YAML seguente:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Creare il ruolo usando il [comando kubectl apply][kubectl-apply] e specificare il nome file del manifesto YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Ottenere l'ID risorsa per *il gruppo opssre* usando il [comando az ad group show:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Creare un RoleBinding per il gruppo *opssre* per usare il ruolo creato in precedenza per l'accesso allo spazio dei nomi. Creare un file denominato `rolebinding-sre-namespace.yaml` e incollare il manifesto YAML seguente. Nell'ultima riga sostituire *groupObjectId con*  l'ID oggetto gruppo restituito dal comando precedente:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Creare roleBinding usando il [comando kubectl apply][kubectl-apply] e specificare il nome file del manifesto YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagire con le risorse del cluster Azure AD identità

A questo punto, è possibile testare il funzionamento delle autorizzazioni previste quando si creano e si gestiscono le risorse in un cluster del servizio Web AKS. In questi esempi si pianificano e si visualizzano i pod nello spazio dei nomi assegnato dell'utente. Si prova quindi a pianificare e visualizzare i pod all'esterno dello spazio dei nomi assegnato.

Per prima cosa, reimpostare *il contesto kubeconfig* usando il [comando az servizio Kuacks get-credentials.][az-aks-get-credentials] In una sezione precedente si imposta il contesto usando le credenziali di amministratore del cluster. L'utente amministratore ignora Azure AD richieste di accesso. Senza il parametro , viene applicato il contesto utente che richiede l'autenticazione di tutte le richieste `--admin` tramite Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Pianificare un pod NGINX di base usando il [comando kubectl run][kubectl-run] nello spazio *dei nomi dev:*

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Come richiesta di accesso, immettere le credenziali per il proprio `appdev@contoso.com` account creato all'inizio dell'articolo. Dopo aver eseguito l'accesso, il token dell'account viene memorizzato nella cache per i comandi `kubectl` futuri. La pianificazione di NGINX viene eseguita correttamente, come illustrato nell'output di esempio seguente:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Usare ora il [comando kubectl get pods][kubectl-get] per visualizzare i pod nello spazio *dei nomi dev.*

```console
kubectl get pods --namespace dev
```

Come illustrato nell'output di esempio seguente, il pod NGINX è in *esecuzione correttamente:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Creare e visualizzare le risorse del cluster all'esterno dello spazio dei nomi assegnato

Provare ora a visualizzare i pod all'esterno dello spazio *dei nomi dev.* Usare di [nuovo il comando kubectl get pods,][kubectl-get] questa volta per vedere `--all-namespaces` come segue:

```console
kubectl get pods --all-namespaces
```

L'appartenenza al gruppo dell'utente non ha un ruolo Kubernetes che consente questa azione, come illustrato nell'output di esempio seguente:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Allo stesso modo, provare a pianificare un pod in uno spazio dei nomi diverso, ad esempio lo *spazio dei nomi sre.* L'appartenenza al gruppo dell'utente non è allineata a un ruolo Kubernetes e a RoleBinding per concedere queste autorizzazioni, come illustrato nell'output di esempio seguente:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testare l'accesso SRE alle risorse del cluster del servizio AKS

Per verificare che l'appartenenza Azure AD gruppo e il controllo degli accessi in base al ruolo di Kubernetes funzionino correttamente tra utenti e gruppi diversi, provare i comandi precedenti quando si è connessi come utente *opssre.*

Reimpostare il *contesto kubeconfig* usando il [comando az aks get-credentials][az-aks-get-credentials] che cancella il token di autenticazione memorizzato in precedenza nella cache per l'utente *aksdev:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Provare a pianificare e visualizzare i pod nello spazio dei nomi *sre* assegnato. Quando richiesto, accedere con le proprie `opssre@contoso.com` credenziali create all'inizio dell'articolo:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Come illustrato nell'output di esempio seguente, è possibile creare e visualizzare correttamente i pod:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

A questo punto, provare a visualizzare o pianificare i pod all'esterno dello spazio dei nomi SRE assegnato:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Questi `kubectl` comandi hanno esito negativo, come illustrato nell'output di esempio seguente. L'appartenenza a gruppi dell'utente e il ruolo Kubernetes e RoleBindings non concedono le autorizzazioni per creare o gestione risorse in altri spazi dei nomi:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo sono state create le risorse nel cluster del servizio Web Diaks e gli utenti e i gruppi in Azure AD. Per pulire tutte queste risorse, eseguire i comandi seguenti:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come proteggere i cluster Kubernetes, vedere Opzioni di accesso e [identità per][rbac-authorization]il servizio Kubernetes.

Per le procedure consigliate sul controllo delle identità e delle risorse, vedere [Procedure consigliate per l'autenticazione e l'autorizzazione nel servizio Web Diaks.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-ad-group-create]: /cli/azure/ad/group#az_ad_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-ad-user-create]: /cli/azure/ad/user#az_ad_user_create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az_ad_group_member_add
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
