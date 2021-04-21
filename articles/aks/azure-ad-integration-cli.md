---
title: Integrare Azure Active Directory con servizio Azure Kubernetes (legacy)
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare e Azure Active Directory cluster servizio Azure Kubernetes (AKS) (legacy)
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: cb92f84560a88d406f0d519459c27b5d916ec5ad
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769572"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integrare Azure Active Directory con servizio Azure Kubernetes'interfaccia della riga di comando di Azure (legacy)

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione, è possibile accedere a un cluster del servizio Web Diaks usando un token Azure AD di autenticazione. Gli operatori del cluster possono anche configurare il controllo degli accessi in base al ruolo di Kubernetes in base all'identità di un utente o all'appartenenza a gruppi di directory.

Questo articolo illustra come creare i componenti Azure AD necessari, quindi distribuire un cluster abilitato per Azure AD e creare un ruolo Kubernetes di base nel cluster del servizio Kubernetes.

Per lo script di esempio completo usato in questo articolo, vedere Esempi dell'interfaccia della riga di comando di [Azure - Integrazione del servizio][complete-script]Azure Azure AD .

> [!Important]
> Il servizio AKS ha una nuova esperienza di gestione [gestita][managed-aad] dal servizio Azure AD che non richiede la gestione di applicazioni server o client. Se si vuole eseguire la migrazione, seguire le istruzioni [riportate qui.][managed-aad-migrate]

## <a name="the-following-limitations-apply"></a>Si applicano le limitazioni seguenti:

- Azure AD può essere abilitato solo nel cluster abilitato per il controllo degli accessi in base al ruolo di Kubernetes.
- Azure AD'integrazione legacy può essere abilitata solo durante la creazione del cluster.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.61 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

Passare a [https://shell.azure.com](https://shell.azure.com) per aprire Cloud Shell nel browser.

Per coerenza e per facilitare l'esecuzione dei comandi in questo articolo, creare una variabile per il nome del cluster del servizio Web Diaks desiderato. Nell'esempio seguente viene utilizzato *il nome myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD panoramica dell'autenticazione

L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per altre informazioni sui OpenID Connect, vedere la [documentazione di Open ID Connect.][open-id-connect]

Dall'interno del cluster Kubernetes viene usata l'autenticazione del token del webhook per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes. Per altre informazioni sull'autenticazione del token del webhook, consulta [documentazione dell'autenticazione del webhook][kubernetes-webhook].

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione del servizio Azure Kubernetes, vengono configurate due applicazioni di Azure AD. Questa operazione deve essere completata da un amministratore tenant di Azure.

## <a name="create-azure-ad-server-component"></a>Creare Azure AD componente server

Per eseguire l'integrazione con il servizio Diaks, si crea e si usa un'applicazione Azure AD che funge da endpoint per le richieste di identità. La prima applicazione Azure AD necessaria ottiene Azure AD'appartenenza a un gruppo per un utente.

Creare il componente dell'applicazione server usando [il comando az ad app create,][az-ad-app-create] quindi aggiornare le attestazioni di appartenenza al gruppo usando [il comando az ad app update.][az-ad-app-update] L'esempio seguente usa *la variabile aksname* definita nella sezione [Prima](#before-you-begin) di iniziare e crea una variabile

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Creare ora un'entità servizio per l'app server usando [il comando az ad sp create.][az-ad-sp-create] Questa entità servizio viene usata per autenticarsi all'interno della piattaforma Azure. Ottenere quindi il segreto dell'entità servizio usando il [comando az ad sp credential reset][az-ad-sp-credential-reset] e assegnarlo alla variabile denominata *serverApplicationSecret* per l'uso in uno dei passaggi seguenti:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

L Azure AD'entità servizio deve avere le autorizzazioni per eseguire le azioni seguenti:

* Lettura dati directory
* Accesso e lettura del profilo utente

Assegnare queste autorizzazioni usando il [comando az ad app permission add:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Infine, concedere le autorizzazioni assegnate nel passaggio precedente per l'applicazione server usando il [comando az ad app permission grant.][az-ad-app-permission-grant] Questo passaggio ha esito negativo se l'account corrente non è un amministratore tenant. È anche necessario aggiungere le autorizzazioni per Azure AD'applicazione per richiedere informazioni che altrimenti potrebbero richiedere il consenso amministrativo usando [az ad app permission admin-consent][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Creare Azure AD componente client

La seconda applicazione Azure AD usata quando un utente accede al cluster del servizio Kubernetes con l'interfaccia della riga di comando di Kubernetes ( `kubectl` ). Questa applicazione client accetta la richiesta di autenticazione dall'utente e verifica le credenziali e le autorizzazioni. Creare l Azure AD app per il componente client usando il [comando az ad app create:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Creare un'entità servizio per l'applicazione client usando [il comando az ad sp create:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Ottenere l'ID oAuth2 per l'app server per consentire il flusso di autenticazione tra i due componenti dell'app usando [il comando az ad app show.][az-ad-app-show] Questo ID oAuth2 viene usato nel passaggio successivo.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Aggiungere le autorizzazioni per i componenti dell'applicazione client e dell'applicazione server per usare il flusso di comunicazione oAuth2 usando [il comando az ad app permission add.][az-ad-app-permission-add] Concedere quindi le autorizzazioni per consentire all'applicazione client di comunicare con l'applicazione server usando [il comando az ad app permission grant:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Distribuire il cluster

Dopo la creazione Azure AD due applicazioni, creare il cluster del servizio Web Del servizio AKS stesso. Creare prima di tutto un gruppo di risorse usando [il comando az group create.][az-group-create] L'esempio seguente crea il gruppo di risorse *nell'area EastUS:*

Creare un gruppo di risorse per il cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Ottenere l'ID tenant della sottoscrizione di Azure usando il [comando az account show.][az-account-show] Creare quindi il cluster del servizio Servizio Web Diaz usando [il comando az servizio WebKs create.][az-aks-create] Il comando per creare il cluster del servizio Diaks fornisce gli ID dell'applicazione client e del server, il segreto dell'entità servizio dell'applicazione server e l'ID tenant:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Infine, ottenere le credenziali di amministratore del cluster usando il [comando az servizio Web Sdk get-credentials.][az-aks-get-credentials] In uno dei passaggi seguenti si ottengono le *normali* credenziali del cluster utente per vedere il Azure AD di autenticazione in azione.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Creare un'associazione di controllo degli accessi in base al ruolo di Kubernetes

Prima di poter usare un account di Azure Active Directory con il cluster servizio Azure Kubernetes, è necessario creare un'associazione di ruolo o un'associazione di ruolo del cluster. I *ruoli* definiscono le autorizzazioni da concedere e le *associazioni* le applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere Uso dell'autorizzazione del controllo degli accessi in base al ruolo di [Kubernetes.][rbac-authorization]

Ottenere il nome dell'entità utente (UPN) per l'utente attualmente connesso usando il [comando az ad signed-in-user show.][az-ad-signed-in-user-show] Questo account utente è abilitato per Azure AD'integrazione nel passaggio successivo.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se l'utente per cui si concede l'associazione RBAC di Kubernetes si trova nello stesso tenant Azure AD, assegnare le autorizzazioni in base a *userPrincipalName*. Se l'utente si trova in un tenant Azure AD, eseguire una query per e usare la *proprietà objectId.*

Creare un manifesto YAML denominato `basic-azure-ad-binding.yaml` e incollare il contenuto seguente. Nell'ultima riga sostituire *userPrincipalName_or_objectId*  con l'UPN o l'ID oggetto restituito dal comando precedente:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Creare ClusterRoleBinding usando il [comando kubectl apply][kubectl-apply] e specificare il nome file del manifesto YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Accedere al cluster con Azure AD

A questo punto è possibile testare l'integrazione dell'Azure AD per il cluster del servizio Web Diaks. Impostare il `kubectl` contesto di configurazione per l'uso di credenziali utente normali. Questo contesto passa tutte le richieste di autenticazione tramite Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Usare ora il [comando kubectl get pods][kubectl-get] per visualizzare i pod in tutti gli spazi dei nomi:

```console
kubectl get pods --all-namespaces
```

Viene visualizzata una richiesta di accesso per l'autenticazione usando Azure AD credenziali usando un Web browser. Dopo aver eseguito l'autenticazione, il comando visualizza i pod nel cluster del servizio `kubectl` Web Disatteso, come illustrato nell'output di esempio seguente:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Il token di autenticazione ricevuto per `kubectl` viene memorizzato nella cache. Si è riprompati per l'accesso solo quando il token è scaduto o il file di configurazione Kubernetes viene ri-creato.

Se viene visualizzato un messaggio di errore di autorizzazione dopo aver eseguito l'accesso con un Web browser come nell'output di esempio seguente, verificare i possibili problemi seguenti:

```output
error: You must be logged in to the server (Unauthorized)
```

* È stato definito l'ID oggetto o l'UPN appropriato, a seconda che l'account utente si trova nello stesso tenant Azure AD o meno.
* L'utente non deve essere membro di più di 200 gruppi.
* Il segreto definito nella registrazione dell'applicazione per il server corrisponde al valore configurato tramite `--aad-server-app-secret`

## <a name="next-steps"></a>Passaggi successivi

Per lo script completo che contiene i comandi illustrati in questo articolo, vedere lo script di integrazione Azure AD nel repo degli esempi del servizio [Web Disaks.][complete-script]

Per usare Azure AD utenti e gruppi per controllare l'accesso alle risorse del cluster, vedere Controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo kubernetes e le identità Azure AD nel servizio Azure [Kubernetes.][azure-ad-rbac]

Per altre informazioni su come proteggere i cluster Kubernetes, vedere Opzioni di accesso e [identità per il][rbac-authorization]servizio Kubernetes.

Per le procedure consigliate per il controllo delle identità e delle risorse, vedere [Procedure consigliate per l'autenticazione e l'autorizzazione nel servizio Web AKS.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-ad-app-create]: /cli/azure/ad/app#az_ad_app_create
[az-ad-app-update]: /cli/azure/ad/app#az_ad_app_update
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az_ad_app_permission_add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az_ad_app_permission_grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az_ad_app_permission_admin_consent
[az-ad-app-show]: /cli/azure/ad/app#az_ad_app_show
[az-group-create]: /cli/azure/group#az_group_create
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az_ad_signed_in_user_show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
