---
title: "Azure Red Hat OpenShift che esegue OpenShift 4: configurare l'autenticazione Azure Active Directory tramite la riga di comando"
description: Informazioni su come configurare l'autenticazione Azure Active Directory per un cluster Azure Red Hat OpenShift che esegue OpenShift 4 tramite la riga di comando
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 06f7bfea9a88627733eb9ce9166e05d05790e23a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213063"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Configurare l'autenticazione Azure Active Directory per un cluster Azure Red Hat OpenShift 4 (CLI)

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.6.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Recuperare gli URL specifici del cluster che verranno usati per configurare l'applicazione Azure Active Directory.

Impostare le variabili per il nome del gruppo di risorse e del cluster.

Sostituire **\<resource_group>** con il nome del gruppo di risorse e **\<aro_cluster>** con il nome del cluster.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

Costruire l'URL di callback OAuth del cluster e archiviarlo in una variabile **oauthCallbackURL**. 

> [!NOTE]
> La `AAD` sezione nell'URL di callback OAuth deve corrispondere al nome del provider di identità OAuth che verrà configurato in un secondo momento.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

Il formato di oauthCallbackURL è leggermente diverso con i domini personalizzati:

* Eseguire il comando seguente se si usa un dominio personalizzato, ad esempio `contoso.com` . 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* Se non si usa un dominio personalizzato `$domain` , sarà una stringa alnum a otto caratteri ed è estesa da `$location.aroapp.io` .

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> La `AAD` sezione nell'URL di callback OAuth deve corrispondere al nome del provider di identità OAuth che verrà configurato in un secondo momento.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Creare un'applicazione Azure Active Directory per l'autenticazione

Sostituire **\<client_secret>** con una password sicura per l'applicazione.

```azurecli-interactive
client_secret=<client_secret>
```

Creare un'applicazione Azure Active Directory e recuperare l'identificatore dell'applicazione creato.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

Recuperare l'ID tenant della sottoscrizione a cui appartiene l'applicazione.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Creare un file manifesto per definire le attestazioni facoltative da includere nel token ID

Gli sviluppatori di applicazioni possono utilizzare [attestazioni facoltative](../active-directory/develop/active-directory-optional-claims.md) nelle applicazioni Azure ad per specificare le attestazioni desiderate nei token inviati alla propria applicazione.

Le attestazioni facoltative possono essere usate per:

- Selezionare attestazioni aggiuntive da includere nei token per l'applicazione.
- Modificare il comportamento di determinate attestazioni che Azure AD restituisce nei token.
- Aggiungere e accedere ad attestazioni personalizzate per l'applicazione.

Si configurerà OpenShift per usare l'attestazione e si eseguirà il `email` fallback per `upn` impostare il nome utente preferito aggiungendo il `upn` come parte del token ID restituito da Azure Active Directory.

Creare una **manifest.jsnel** file per configurare l'applicazione Azure Active Directory.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Aggiornare il optionalClaims dell'applicazione Azure Active Directory con un manifesto

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Aggiornare le autorizzazioni dell'ambito dell'applicazione Azure Active Directory

Per poter leggere le informazioni utente da Azure Active Directory, è necessario definire gli ambiti appropriati.

Aggiungere l'autorizzazione per l'ambito **Azure Active Directory Graph. User. Read** per abilitare l'accesso e la lettura del profilo utente.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> È possibile ignorare tranquillamente il messaggio per concedere il consenso, a meno che non si sia autenticato come amministratore globale per questo Azure Active Directory. Agli utenti di dominio standard verrà richiesto di concedere il consenso quando si accede per la prima volta al cluster usando le credenziali di AAD.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Assegnare utenti e gruppi al cluster (facoltativo)

Per impostazione predefinita, le applicazioni registrate in un tenant di Azure Active Directory (Azure AD) sono disponibili per tutti gli utenti del tenant che eseguono correttamente l'autenticazione. Azure AD consente agli amministratori e agli sviluppatori di tenant di limitare un'app a un set specifico di utenti o gruppi di sicurezza all'interno del tenant.

Seguire le istruzioni nella documentazione di Azure Active Directory per [assegnare utenti e gruppi all'app](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Configurare l'autenticazione OpenID OpenShift

Recuperare le `kubeadmin` credenziali. Eseguire il comando seguente per trovare la password per l'utente `kubeadmin`.

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

Accedere al server API del cluster OpenShift usando il comando seguente. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Creare un segreto OpenShift per archiviare il segreto dell'applicazione Azure Active Directory.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Creare un file **oidc. YAML** per configurare l'autenticazione OpenID di OpenShift in Azure Active Directory. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

Applicare la configurazione al cluster.

```azurecli-interactive
oc apply -f oidc.yaml
```

Viene restituita una risposta simile alla seguente.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Verificare l'accesso tramite Azure Active Directory

Se ora si esegue la disconnessione della console Web di OpenShift e si tenta di eseguire nuovamente l'accesso, verrà visualizzata una nuova opzione per accedere con **AAD**. Potrebbe essere necessario attendere alcuni minuti.

![Schermata di accesso con Azure Active Directory opzione](media/aro4-login-2.png)
