---
title: 'Avvio rapido: Connettere un cluster Kubernetes esistente a Azure Arc'
description: Questa guida introduttiva illustra come connettere un cluster Kubernetes Azure Arc abilitato.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, cluster
ms.openlocfilehash: 21ec5000ed7ef9df1805fa6ec43e20efc0f82182
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481243"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Avvio rapido: Connettere un cluster Kubernetes esistente a Azure Arc 

In questa guida introduttiva si apprendono i vantaggi di Kubernetes Azure Arc abilitato e si connetterà un cluster Kubernetes esistente a Azure Arc. Per un'analisi concettuale della connessione dei cluster Azure Arc, vedere l'articolo [Azure Arc'architettura dell'agente Kubernetes abilitata.](./conceptual-agent-architecture.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Un cluster Kubernetes in esecuzione. Se non se ne ha uno, è possibile creare un cluster usando una di queste opzioni:
    * [Kubernetes in Docker (KIND)](https://kind.sigs.k8s.io/)
    * Creare un cluster Kubernetes usando Docker per [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) o [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Cluster Kubernetes auto-gestito con [l'API Cluster](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > Il cluster deve avere almeno un nodo del sistema operativo e del tipo di architettura `linux/amd64` . I cluster con solo `linux/arm64` nodi non sono ancora supportati.
    
* Un `kubeconfig` file e un contesto che puntano al cluster.
* Autorizzazioni 'Read' e 'Write' per il tipo Azure Arc risorsa Kubernetes abilitato ( `Microsoft.Kubernetes/connectedClusters` ).

* Installare la [versione più recente di Helm 3.](https://helm.sh/docs/intro/install)

- [Installare o aggiornare l'interfaccia della riga](https://docs.microsoft.com/cli/azure/install-azure-cli) di comando di Azure alla versione >= 2.16.0
* Installare l'estensione dell'interfaccia della riga di comando `connectedk8s` di Azure >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Se `connectedk8s` l'estensione è già installata, aggiornarla alla versione più recente usando il comando seguente: `az extension update --name connectedk8s`


>[!NOTE]
>L'elenco delle aree supportate Azure Arc Kubernetes abilitato è disponibile [qui.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)

>[!NOTE]
> Se si vogliono usare località personalizzate nel cluster, usare le aree Stati Uniti orientali o Europa occidentale per connettere il cluster perché le località personalizzate sono attualmente disponibili solo in queste aree. Tutte le altre Azure Arc kubernetes abilitate sono disponibili in tutte le aree elencate in precedenza.

## <a name="meet-network-requirements"></a>Soddisfare i requisiti di rete

>[!IMPORTANT]
>Azure Arc agenti richiedono i protocolli/porte/URL in uscita seguenti per il funzionamento:
>* TCP sulla porta 443: `https://:443`
>* TCP sulla porta 9418: `git://:9418`
  
| Endpoint (DNS) | Descrizione |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Obbligatorio per la connessione dell'agente ad Azure e la registrazione del cluster.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Endpoint del piano dati per l'agente per eseguire il push dello stato e recuperare le informazioni di configurazione.                                      |  
| `https://login.microsoftonline.com`                                                                            | Obbligatorio per recuperare e aggiornare Azure Resource Manager token.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Obbligatorio per eseguire il pull delle immagini del contenitore Azure Arc agenti.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Obbligatorio per eseguire il pull dei certificati di identità del servizio gestito assegnati dal sistema.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrare i due provider per Azure Arc Kubernetes abilitato

1. Immettere i comandi seguenti:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Monitorare il processo di registrazione. La registrazione può richiedere fino a 10 minuti.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse:  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>Connettere un cluster Kubernetes esistente

1. Connettere il cluster Kubernetes a Azure Arc usando il comando seguente:
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
    Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
    ```

> [!TIP]
> Il comando precedente senza il parametro location specificato crea la Azure Arc kubernetes abilitata nella stessa posizione del gruppo di risorse. Per creare la Azure Arc kubernetes abilitata in un percorso diverso, specificare `--location <region>` o `-l <region>` durante l'esecuzione del `az connectedk8s connect` comando.

## <a name="verify-cluster-connection"></a>Verificare la connessione del cluster

Visualizzare un elenco dei cluster connessi con il comando seguente:  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> Dopo l'onboarding del cluster, sono necessari circa 5-10 minuti per visualizzare i metadati del cluster (versione del cluster, versione dell'agente, numero di nodi e così via) nella pagina di panoramica della risorsa Kubernetes abilitata per Azure Arc in portale di Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Connettersi usando un server proxy in uscita

Se il cluster si trova dietro un server proxy in uscita, l'interfaccia della riga di comando di Azure e gli agenti Kubernetes abilitati per Azure Arc devono instradare le richieste tramite il server proxy in uscita. 


1. Impostare le variabili di ambiente necessarie per l'uso del server proxy in uscita da parte dell'interfaccia della riga di comando di Azure:

    * Se si usa Bash, eseguire il comando seguente con i valori appropriati:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Se si usa PowerShell, eseguire il comando seguente con i valori appropriati:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. Eseguire il comando connect con i parametri proxy specificati:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Specificare `excludedCIDR` in per assicurarsi che la comunicazione nel cluster non sia `--proxy-skip-range` interrotta per gli agenti.
> * `--proxy-http`, `--proxy-https` e sono previsti per la maggior parte degli ambienti proxy in `--proxy-skip-range` uscita. `--proxy-cert` è *necessario* solo se è necessario inserire i certificati attendibili previsti dal proxy nell'archivio certificati attendibili dei pod dell'agente.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Visualizzare Azure Arc agenti per Kubernetes

Kubernetes abilitato per Azure Arc distribuisce alcuni operatori nello spazio dei nomi `azure-arc`. 

1. Visualizzare queste distribuzioni e pod usando:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Verificare che tutti i pod siano in `Running` uno stato.

    ```output
    NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
    deployment.apps/cluster-metadata-operator     1/1             1        1      16h
    deployment.apps/clusteridentityoperator       1/1             1        1      16h
    deployment.apps/config-agent                  1/1             1        1      16h
    deployment.apps/controller-manager            1/1             1        1      16h
    deployment.apps/flux-logs-agent               1/1             1        1      16h
    deployment.apps/metrics-agent                 1/1             1        1      16h
    deployment.apps/resource-sync-agent           1/1             1        1      16h

    NAME                                           READY    STATUS   RESTART AGE
    pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
    pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
    pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
    pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
    pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
    pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
    pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare la risorsa Kubernetes Azure Arc abilitata,  tutte le risorse di configurazione associate e tutti gli agenti in esecuzione nel cluster usando l'interfaccia della riga di comando di Azure usando il comando seguente:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>L'eliminazione Azure Arc risorsa Kubernetes abilitata usando portale di Azure rimuove tutte  le risorse di configurazione associate, ma non rimuove gli agenti in esecuzione nel cluster. La procedura consigliata consiste nell'eliminare Azure Arc risorsa Kubernetes abilitata usando `az connectedk8s delete` invece di portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come distribuire le configurazioni nel cluster Kubernetes connesso usando GitOps.
> [!div class="nextstepaction"]
> [Distribuire configurazioni con Gitop](tutorial-use-gitops-connected-cluster.md)
