---
title: Usare la connessione cluster per connettersi ai cluster Kubernetes abilitati per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Usare la connessione cluster per connettersi in modo sicuro ai cluster Kubernetes abilitati per Azure Arc
ms.openlocfilehash: c6b6555c7d18c0aa0d2e7c94ad2c32353da19502
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451132"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Usare la connessione cluster per connettersi ai cluster Kubernetes abilitati per Azure Arc

Con la connessione cluster è possibile connettersi in modo sicuro ai cluster Kubernetes abilitati per Azure Arc senza richiedere l'abilitazione di alcuna porta in ingresso nel firewall. L'accesso a `apiserver` del cluster Kubernetes abilitato per l'arco consente gli scenari seguenti:
* Abilitare il debug interattivo e la risoluzione dei problemi.
* Fornire l'accesso al cluster ai servizi di Azure per [percorsi personalizzati](custom-locations.md) e altre risorse create sopra di esso.

Una panoramica concettuale di questa funzionalità è disponibile in [cluster Connect-Azure Arc Enabled Kubernetes](conceptual-cluster-connect.md) articolo.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prerequisiti   

- [Installare o aggiornare l'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli) della riga di comando di Azure alla versione >= 2.16.0

- Installare l' `connectedk8s` estensione dell'interfaccia della riga di comando di Azure della versione >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Se l'estensione è già stata installata `connectedk8s` , aggiornare l'estensione alla versione più recente:
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Un cluster con connessione Kubernetes abilitato per Azure ARC esistente.
    - Se non è ancora stato connesso un cluster, usare la [Guida introduttiva](quickstart-connect-cluster.md).
    - [Aggiornare gli agenti](agent-upgrade.md#manually-upgrade-agents) alla versione >= 1.1.0.

- Abilitare la connessione del cluster in qualsiasi cluster Kubernetes abilitato per Azure Arc eseguendo il comando seguente in un computer in cui il `kubeconfig` file fa riferimento al cluster di interesse:

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- Abilitare gli endpoint seguenti per l'accesso in uscita, oltre a quelli indicati in [connessione di un cluster Kubernetes ad Azure Arc](quickstart-connect-cluster.md#meet-network-requirements):

    | Endpoint | Porta |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Utilizzo

Con la funzionalità di connessione del cluster sono supportate due opzioni di autenticazione: 
* Azure Active Directory (Azure AD) 
* Token dell'account del servizio

### <a name="option-1-azure-active-directory"></a>Opzione 1: Azure Active Directory

1. Con il `kubeconfig` file che punta al `apiserver` del cluster Kubernetes, creare un ClusterRoleBinding o un elemento Role per l'entità Azure ad (utente o entità servizio) che richiede l'accesso:

    **Per utente:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Per Azure AD applicazione:**

    1. Ottenere l'oggetto `objectId` associato all'applicazione Azure ad:

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Creare un ClusterRoleBinding o un elemento Roleing per l'entità Azure AD (utente o entità servizio) che deve accedere al cluster:
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. Dopo aver eseguito l'accesso all'interfaccia della riga di comando di Azure usando il Azure AD entità di interesse, ottenere la connessione del cluster `kubeconfig` necessaria per comunicare con il cluster da qualsiasi luogo (anche all'esterno del firewall che circonda il cluster):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. Usare `kubectl` per inviare richieste al cluster:

    ```console
    kubectl get pods
    ```
    
    A questo punto dovrebbe essere visualizzata una risposta dal cluster contenente l'elenco di tutti i pod nello `default` spazio dei nomi.

### <a name="option-2-service-account-bearer-token"></a>Opzione 2: token di porta dell'account di servizio

1. Con il `kubeconfig` file che punta al `apiserver` del cluster Kubernetes, creare un account di servizio in qualsiasi spazio dei nomi (il comando seguente lo crea nello spazio dei nomi predefinito):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Creare ClusterRoleBinding o Rolein per concedere a questo [account del servizio le autorizzazioni appropriate per il cluster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding):

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Ottenere il token dell'account di servizio usando i comandi seguenti

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Ottenere la connessione del cluster `kubeconfig` necessaria per comunicare con il cluster da qualsiasi luogo (anche all'esterno del firewall che circonda il cluster):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. Usare `kubectl` per inviare richieste al cluster:

    ```console
    kubectl get pods
    ```

    A questo punto dovrebbe essere visualizzata una risposta dal cluster contenente l'elenco di tutti i pod nello `default` spazio dei nomi.

## <a name="known-limitations"></a>Limitazioni note

Quando si effettuano richieste al cluster Kubernetes, se l'entità Azure AD utilizzata è parte di più di 200 gruppi, viene rilevato l'errore seguente, poiché si tratta di un limite noto:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

Per superare questo errore:
1. Creare un' [entità servizio](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli), che è meno probabile che sia un membro di più di 200 gruppi.
1. [Accedere](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) all'interfaccia della riga di comando di Azure con l'entità servizio prima di eseguire il `az connectedk8s proxy` comando.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> Configurare [Azure ad RBAC](azure-rbac.md) nei cluster