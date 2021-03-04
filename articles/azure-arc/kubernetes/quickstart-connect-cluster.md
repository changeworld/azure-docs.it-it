---
title: 'Guida introduttiva: connettere un cluster Kubernetes esistente ad Azure Arc'
description: In questa Guida introduttiva si apprenderà come connettere un cluster Kubernetes abilitato per Azure Arc.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart
keywords: Kubernetes, Arc, Azure, cluster
ms.openlocfilehash: 3fc522c4bdda9eb1047d5258bcc431d0268990b9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121644"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Guida introduttiva: connettere un cluster Kubernetes esistente ad Azure Arc 

Questa Guida introduttiva raccoglie i vantaggi di Azure Arc Enabled Kubernetes e connette un cluster Kubernetes esistente ad Azure Arc. Per un'esecuzione concettuale sulla connessione dei cluster ad Azure Arc, vedere l' [articolo sull'architettura dell'agente Kubernetes abilitato per Azure Arc](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Verificare di avere:
    * Un cluster Kubernetes in esecuzione.
    * Un `kubeconfig` file che punta al cluster che si vuole connettere ad Azure Arc.
    * Autorizzazioni ' Read ' è Write ' per l'utente o l'entità servizio che si connette creando il tipo di risorsa Kubernetes abilitato per Azure Arc ( `Microsoft.Kubernetes/connectedClusters` ).
* Installare la [versione più recente di Helm 3](https://helm.sh/docs/intro/install).
* Installare le seguenti estensioni dell'interfaccia della riga di comando di Azure Arc abilitate per Kubernetes di versioni >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Per aggiornare queste estensioni alla versione più recente, eseguire i comandi seguenti:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8s-configuration
  ```

>[!NOTE]
>**Aree supportate:**
>* Stati Uniti orientali
>* Europa occidentale
>* Stati Uniti centro-occidentali
>* Stati Uniti centro-meridionali
>* Asia sud-orientale
>* Regno Unito meridionale
>* Stati Uniti occidentali 2
>* Australia orientale
>* Stati Uniti orientali 2
>* Europa settentrionale

## <a name="meet-network-requirements"></a>Soddisfare i requisiti di rete

>[!IMPORTANT]
>Per il funzionamento degli agenti di Azure Arc sono necessari i protocolli/porte/URL in uscita seguenti:
>* TCP sulla porta 443: `https://:443`
>* TCP sulla porta 9418: `git://:9418`
  
| Endpoint (DNS) | Descrizione |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Necessaria per la connessione dell'agente ad Azure e la registrazione del cluster.                                                        |  
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com`, `https://westcentralus.dp.kubernetesconfiguration.azure.com`, `https://southcentralus.dp.kubernetesconfiguration.azure.com`, `https://southeastasia.dp.kubernetesconfiguration.azure.com`, `https://uksouth.dp.kubernetesconfiguration.azure.com`, `https://westus2.dp.kubernetesconfiguration.azure.com`, `https://australiaeast.dp.kubernetesconfiguration.azure.com`, `https://eastus2.dp.kubernetesconfiguration.azure.com`, `https://northeurope.dp.kubernetesconfiguration.azure.com` | Endpoint del piano dati per l'agente per il push dello stato e il recupero delle informazioni di configurazione.                                      |  
| `https://login.microsoftonline.com`                                                                            | Obbligatorio per recuperare e aggiornare i token di Azure Resource Manager.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Obbligatorio per eseguire il pull delle immagini del contenitore per gli agenti di Azure Arc.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Obbligatorio per eseguire il pull di certificati identità del servizio gestita (MSI) assegnati dal sistema.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrare i due provider per Azure Arc Enabled Kubernetes

1. Immettere i comandi seguenti:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    ```
2. Monitorare il processo di registrazione. La registrazione può richiedere fino a 10 minuti.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table    
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

1. Connettere il cluster Kubernetes ad Azure ARC usando il comando seguente:
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
> Il comando precedente senza il parametro location specificato Crea la risorsa Kubernetes abilitata per Azure Arc nella stessa posizione del gruppo di risorse. Per creare la risorsa Kubernetes abilitata per Azure Arc in un percorso diverso, specificare `--location <region>` o `-l <region>` quando si esegue il `az connectedk8s connect` comando.

## <a name="verify-cluster-connection"></a>Verifica connessione cluster

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
> Dopo l'onboarding del cluster, sono necessari circa 5-10 minuti per la visualizzazione dei metadati del cluster (versione del cluster, versione dell'agente, numero di nodi e così via) nella pagina Panoramica della risorsa Kubernetes abilitata per Azure Arc in portale di Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Connettersi tramite un server proxy in uscita

Se il cluster si trova dietro un server proxy in uscita, l'interfaccia della riga di comando di Azure e gli agenti Kubernetes abilitati per Azure Arc devono instradare le richieste tramite il server proxy in uscita. 


1. Impostare le variabili di ambiente necessarie per l'interfaccia della riga di comando di Azure per l'uso del server proxy in uscita:

    * Se si usa bash, eseguire il comando seguente con i valori appropriati:

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

2. Eseguire il comando Connect con i parametri proxy specificati:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Specificare `excludedCIDR` in `--proxy-skip-range` per assicurarsi che la comunicazione nel cluster non sia interruppe per gli agenti.
> * `--proxy-http`, `--proxy-https` e `--proxy-skip-range` sono previsti per la maggior parte degli ambienti proxy in uscita. `--proxy-cert` è necessario *solo* se è necessario inserire certificati attendibili previsti dal proxy nell'archivio certificati attendibili dei Pod dell'agente.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Visualizzare gli agenti di Azure Arc per Kubernetes

Kubernetes abilitato per Azure Arc distribuisce alcuni operatori nello spazio dei nomi `azure-arc`. 

1. Visualizzare le distribuzioni e i pod usando:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Verificare che tutti i Pod si trovino in uno `Running` stato.

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

È possibile eliminare la risorsa Kubernetes abilitata per Azure Arc, le risorse di configurazione associate *e* tutti gli agenti in esecuzione nel cluster usando l'interfaccia della riga di comando di Azure usando il comando seguente:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Eliminando la risorsa Kubernetes abilitata per Azure ARC usando portale di Azure vengono rimosse tutte le risorse di configurazione associate, ma *non* vengono rimossi gli agenti in esecuzione nel cluster. La procedura consigliata consiste nell'eliminare la risorsa Kubernetes abilitata per Azure ARC usando `az connectedk8s delete` invece di portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come distribuire le configurazioni nel cluster Kubernetes connesso usando GitOps.
> [!div class="nextstepaction"]
> [Distribuire le configurazioni con Gitops](tutorial-use-gitops-connected-cluster.md)
