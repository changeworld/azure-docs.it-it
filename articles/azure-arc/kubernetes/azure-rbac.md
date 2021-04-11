---
title: Controllo degli accessi in base al ruolo di Azure per Azure Arc abilitato in Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Usare il controllo degli accessi in base al ruolo di Azure per i controlli di autorizzazione in Azure Arc Kubernetes
ms.openlocfilehash: bd8029cb2772a6f6bd9821abe6acf69c9c08599d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451107"
---
# <a name="azure-rbac-for-azure-arc-enabled-kubernetes-clusters"></a>Controllo degli accessi in base al ruolo di Azure per Azure Arc abilitato in Kubernetes

I tipi di oggetto Kubernetes [ClusterRoleBinding e Rolet](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) consentono di definire l'autorizzazione in Kubernetes in modo nativo. Con il controllo degli accessi in base al ruolo di Azure è possibile usare Azure Active Directory e assegnazioni di ruolo in Azure per controllare i controlli di autorizzazione nel cluster. Ciò implica che è ora possibile usare le assegnazioni di ruolo di Azure per controllare in modo granulare Chi può leggere, scrivere ed eliminare gli oggetti Kubernetes, ad esempio distribuzione, Pod e servizio

Una panoramica concettuale di questa funzionalità è disponibile in controllo degli accessi in base al ruolo di Azure [Kubernetes](conceptual-azure-rbac.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Installare o aggiornare l'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli) della riga di comando di Azure alla versione >= 2.16.0

- Installare l' `connectedk8s` estensione dell'interfaccia della riga di comando di Azure della versione >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Se l' `connectedk8s` estensione è già installata, è possibile aggiornarla alla versione più recente usando il comando seguente: 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Un cluster con connessione Kubernetes abilitato per Azure ARC esistente.
    - Se non è ancora stato connesso un cluster, usare la [Guida introduttiva](quickstart-connect-cluster.md).
    - [Aggiornare gli agenti](agent-upgrade.md#manually-upgrade-agents) alla versione >= 1.1.0.

> [!NOTE]
> Questa funzionalità non può essere configurata per le offerte Kubernetes gestite di provider di servizi cloud come il servizio Kubernetes elastico o il motore di Google Kubernetes in cui l'utente non ha accesso al `apiserver` cluster. Per i cluster Azure Kubernetes Service (AKS), questa [funzionalità è disponibile a livello nativo](../../aks/manage-azure-rbac.md) e non richiede la connessione del cluster AKS ad Azure Arc.

## <a name="set-up-azure-ad-applications"></a>Configurare applicazioni Azure AD

### <a name="create-server-application"></a>Creare l'applicazione server

1. Creare una nuova applicazione Azure AD e ottenere il relativo `appId` valore, che verrà usato nei passaggi successivi come `serverApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. Aggiornare le attestazioni di appartenenza al gruppo di applicazioni:

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Creare un'entità servizio e ottenere il relativo `password` valore del campo, che sarà necessario in seguito come `serverApplicationSecret` quando si abilita questa funzionalità nel cluster:

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. Concedere le autorizzazioni per l'API dell'applicazione:

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * Questo passaggio deve essere eseguito da un amministratore tenant di Azure.
    > * Per l'utilizzo di questa funzionalità in produzione, è consigliabile creare un'altra applicazione server per ogni cluster.

### <a name="create-client-application"></a>Creare l'applicazione client

1. Creare una nuova applicazione Azure AD e ottenere il relativo valore ' appId ', che verrà usato nei passaggi successivi come `clientApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Creare un'entità servizio per questa applicazione client:

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. Ottenere `oAuthPermissionId` per l'applicazione server:

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. Concedere le autorizzazioni necessarie per l'applicazione client:

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Creare un'assegnazione di ruolo per l'applicazione server

L'applicazione server deve disporre delle `Microsoft.Authorization/*/read` autorizzazioni per verificare se l'utente che effettua la richiesta è autorizzato negli oggetti Kubernetes che fanno parte della richiesta.

1. Creare un file denominato accessCheck.json con il contenuto seguente:

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Sostituire `<subscription-id>` con l'ID sottoscrizione effettivo.

2. Eseguire il comando seguente per creare il nuovo ruolo personalizzato:

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. Dall'output del comando precedente, archiviare il valore di `id` Field, che verrà usato nei passaggi successivi come `roleId` .

4. Creare un'assegnazione di ruolo nell'applicazione server come assegnatario usando il ruolo creato in precedenza:

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>Abilita RBAC di Azure nel cluster

1. Abilitare il controllo degli accessi in base al ruolo di Azure nel cluster Kubernetes abilitato eseguendo il comando seguente:

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. Prima di eseguire il comando precedente, verificare che il `kubeconfig` file nel computer faccia riferimento al cluster in cui abilitare la funzionalità controllo degli accessi in base al ruolo di Azure.
    > 2. Usare `--skip-azure-rbac-list` con il comando precedente per un elenco delimitato da virgole di nomi utente/messaggi di posta elettronica/OID sottoposto a controlli di autorizzazione con Kubernetes nativi ClusterRoleBinding e oggetti roleing invece di Azure RBAC.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>Per un cluster generico in cui non è presente alcun riconciliatore in esecuzione nella specifica apiserver:

1. Eseguire SSH in ogni nodo master del cluster ed eseguire i passaggi seguenti:

    1. Apri `apiserver` manifesto in modalità di modifica:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Aggiungere la specifica seguente in `volumes` :
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Aggiungere la specifica seguente in `volumeMounts` :

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Aggiungere gli `apiserver` argomenti seguenti:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Se il cluster Kubernetes è di versione >= 1.19.0, è `apiserver argument` necessario impostare anche quanto segue:

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Salvare e chiudere l'editor per aggiornare il `apiserver` pod.


### <a name="for-a-cluster-created-using-cluster-api"></a>Per un cluster creato con l'API cluster

1. Copiare il segreto Guard contenente i file di configurazione del webhook di autenticazione e autorizzazione `from the workload cluster` nel computer:

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Modificare il `namespace` campo del `azure-arc-guard-manifests.yaml` file nello spazio dei nomi all'interno del cluster di gestione in cui si applicano le risorse personalizzate per la creazione di cluster del carico di lavoro.

1. Applica questo manifesto:

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Modificare l' `KubeadmControlPlane` oggetto eseguendo `kubectl edit kcp <clustername>-control-plane` :
    
    1. Aggiungere il frammento di codice seguente in `files:` :
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Aggiungere il frammento di codice seguente in `apiServer:`  ->  `extraVolumes:` :
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Aggiungere il frammento di codice seguente in `apiServer:`  ->  `extraArgs:` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Salvare e chiudere per aggiornare l' `KubeadmControlPlane` oggetto. Attendere che le modifiche vengano realizzate nel cluster del carico di lavoro.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Creare assegnazioni di ruolo per consentire agli utenti di accedere al cluster

I proprietari della risorsa Kubernetes abilitata per Azure Arc possono usare ruoli predefiniti o ruoli personalizzati per concedere ad altri utenti l'accesso al cluster Kubernetes.

### <a name="built-in-roles"></a>Ruoli predefiniti

| Ruolo | Descrizione |
|---|---|
| Visualizzatore Azure Arc Kubernetes | Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. Questo ruolo non consente la visualizzazione dei segreti. Questo è dovuto al fatto `read` che l'autorizzazione per i segreti consente l'accesso alle `ServiceAccount` credenziali nello spazio dei nomi, che a sua volta consente l'accesso all'API usando tale `ServiceAccount` (una forma di escalation dei privilegi). |
| Azure Arc Kubernetes writer | Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. Questo ruolo non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. Tuttavia, questo ruolo consente l'accesso ai segreti e l'esecuzione di pod come qualsiasi `ServiceAccount` nello spazio dei nomi, quindi può essere usato per ottenere i livelli di accesso all'API di qualsiasi `ServiceAccount` nello spazio dei nomi. |
| Amministratore di Azure Arc Kubernetes | Consente l'accesso dell'amministratore. Progettato per essere concesso all'interno di uno spazio dei nomi utilizzando un oggetto Role. Se utilizzato in un oggetto RoleGroup, consente l'accesso in lettura/scrittura alla maggior parte delle risorse in uno spazio dei nomi, inclusa la possibilità di creare ruoli e associazioni di ruolo all'interno dello spazio dei nomi. Questo ruolo non consente l'accesso in scrittura alla quota di risorse o allo spazio dei nomi stesso. |
| Amministrazione cluster Azure Arc Kubernetes | Consente l'accesso con privilegi avanzati per eseguire qualsiasi azione su qualsiasi risorsa. Quando viene usato in un ClusterRoleBinding, fornisce il controllo completo su tutte le risorse nel cluster e in tutti gli spazi dei nomi. Quando viene usato in un oggetto Role, fornisce il controllo completo su ogni risorsa nello spazio dei nomi dell'associazione di ruoli, incluso lo spazio dei nomi stesso.|

È possibile creare assegnazioni di ruolo nell'ambito del cluster Kubernetes abilitato per l'arco nel pannello `Access Control (IAM)` della risorsa cluster in portale di Azure. È anche possibile usare i comandi dell'interfaccia della riga di comando di Azure come illustrato di seguito:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

dove `AZURE-AD-ENTITY-ID` può essere un nome utente (ad esempio, testuser@mytenant.onmicrosoft.com ) o anche il `appId` di un'entità servizio.

Ecco un altro esempio di creazione di un'assegnazione di ruolo con ambito per uno spazio dei nomi specifico all'interno del cluster.

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Mentre le assegnazioni di ruolo con ambito per il cluster possono essere create usando il portale di Azure o l'interfaccia della riga di comando, le assegnazioni di ruolo con ambito di spazi dei nomi possono essere create solo usando l'interfaccia della riga di comando.

### <a name="custom-roles"></a>Ruoli personalizzati

È possibile scegliere di creare una definizione di ruolo personalizzata per l'utilizzo nelle assegnazioni di ruolo.

Esaminare l'esempio seguente di una definizione di ruolo che consente a un utente di leggere solo le distribuzioni. Per ulteriori informazioni, vedere [l'elenco completo delle azioni dati che è possibile utilizzare per creare una definizione di ruolo](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes).

Copiare l'oggetto JSON seguente in un file denominato custom-role.json. Sostituire il `<subscription-id>` segnaposto con l'ID sottoscrizione effettivo. Il ruolo personalizzato seguente utilizza una delle azioni dati e consente di visualizzare tutte le distribuzioni nell'ambito (cluster/spazio dei nomi) in cui viene creata l'assegnazione di ruolo.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Creare la definizione di ruolo eseguendo il comando seguente dalla cartella in cui è stato salvato `custom-role.json` :

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Creare un'assegnazione di ruolo usando questa definizione di ruolo personalizzata:

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Configurare kubectl con le credenziali utente

Esistono due modi per ottenere `kubeconfig` il file necessario per accedere al cluster:
1. Usare la funzionalità di [connessione cluster](cluster-connect.md) ( `az connectedk8s proxy` ) del cluster Kubernetes abilitato per Azure Arc.
1. L'amministrazione del cluster condivide il `kubeconfig` file con tutti gli altri utenti.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>Se si accede a un cluster tramite la funzionalità di connessione cluster

Eseguire il comando seguente per avviare il processo del proxy:

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

Al termine dell'esecuzione del processo del proxy, è possibile aprire un'altra scheda della console per [iniziare a inviare le richieste al cluster](#sending-requests-to-cluster).

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Se l'amministratore del cluster `kubeconfig` ha condiviso il file con l'utente 

1. Eseguire il comando seguente per impostare le credenziali per l'utente:

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Aprire il `kubeconfig` file creato in precedenza. In `contexts` , verificare che il contesto associato ai punti del cluster sia le credenziali utente create nel passaggio precedente.

1. Aggiungere l'impostazione della **modalità di configurazione** in configurazione utente:
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>Invio di richieste al cluster

1. Eseguire qualsiasi `kubectl` comando. Ad esempio:
  * `kubectl get nodes` 
  * `kubectl get pods`

1. Quando viene richiesta l'autenticazione basata su browser, copiare l'URL di accesso del dispositivo `https://microsoft.com/devicelogin` e aprirlo nel Web browser.

1. Immettere il codice stampato nella console, copiare e incollare il codice nel terminale nella richiesta di input per l'autenticazione del dispositivo.

1. Immettere il nome utente ( testuser@mytenant.onmicrosoft.com ) e la password associata.

1. Se viene visualizzato un messaggio di errore simile al seguente, significa che non si è autorizzati ad accedere alla risorsa richiesta:

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    Un amministratore deve creare una nuova assegnazione di ruolo che autorizza l'utente ad accedere alla risorsa.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> Connettersi in modo sicuro al cluster tramite la [connessione cluster](cluster-connect.md)