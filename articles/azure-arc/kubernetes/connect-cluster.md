---
title: Connettere un cluster Kubernetes abilitato per Azure Arc (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Connettere un cluster Kubernetes abilitato per Azure Arc con Azure Arc
keywords: Kubernetes, Arc, Azure, K8s, contenitori
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 876bc15a3f4db1d12afec37c69656c431e5e6773
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652514"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Connettere un cluster Kubernetes abilitato per Azure Arc (anteprima)

Questo articolo illustra la connessione di un cluster Kubernetes esistente ad Azure Arc. Per un'esecuzione concettuale sulla connessione dei cluster, vedere l' [articolo sull'architettura dell'agente Kubernetes abilitato per Azure Arc](./conceptual-agent-architecture.md).

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di aver preparato i prerequisiti seguenti:

* Un cluster Kubernetes in esecuzione. Se non si dispone di un cluster Kubernetes esistente, è possibile usare una delle guide seguenti per creare un cluster di test:
  * Creare un cluster Kubernetes usando [Kubernetes in Docker (Kind)](https://kind.sigs.k8s.io/).
  * Creare un cluster Kubernetes con Docker per [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) o [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* Un file kubeconfig per accedere al cluster e al ruolo di amministratore del cluster nel cluster per la distribuzione degli agenti Kubernetes abilitati per Arc.
* Autorizzazioni ' Read ' è Write ' per l'utente o l'entità servizio usata `az login` con `az connectedk8s connect` i comandi e nel `Microsoft.Kubernetes/connectedclusters` tipo di risorsa. Il ruolo "cluster Kubernetes-caricamento di Azure Arc" dispone di queste autorizzazioni e può essere usato per assegnazioni di ruolo all'utente o all'entità servizio.
* Helm 3 per l'onboarding del cluster usando un' `connectedk8s` estensione. Per soddisfare questo requisito, [installare la versione più recente di Helm 3](https://helm.sh/docs/intro/install) .
* INTERFACCIA della riga di comando di Azure versione 2.15 + per l'installazione delle estensioni CLI Kubernetes abilitate per Azure Arc. [Installare l'interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) della riga di comando di Azure o aggiornare alla versione più recente.
* Estensioni dell'interfaccia della riga di comando Kubernetes di Azure Arc abilitate:
  
  * Installare l' `connectedk8s` estensione per connettere i cluster Kubernetes ad Azure:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Installare l'estensione `k8sconfiguration`:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Se si desidera aggiornare le estensioni in un secondo momento, eseguire i comandi seguenti:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Aree supportate

* Stati Uniti orientali
* Europa occidentale

## <a name="network-requirements"></a>Requisiti di rete

Per il funzionamento degli agenti di Azure Arc sono necessari i protocolli/porte/URL in uscita seguenti:

* TCP sulla porta 443: `https://:443`
* TCP sulla porta 9418: `git://:9418`

| Endpoint (DNS)                                                                                               | Descrizione                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Necessaria per la connessione dell'agente ad Azure e la registrazione del cluster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Endpoint del piano dati per l'agente per il push dello stato e il recupero delle informazioni di configurazione.                                      |
| `https://login.microsoftonline.com`                                                                            | Obbligatorio per recuperare e aggiornare i token di Azure Resource Manager.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Obbligatorio per eseguire il pull delle immagini del contenitore per gli agenti di Azure Arc.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Obbligatorio per eseguire il pull di certificati identità del servizio gestita (MSI) assegnati dal sistema.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registrare i due provider per Azure Arc Enabled Kubernetes

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

La registrazione è un processo asincrono che può richiedere circa 10 minuti. È possibile monitorare il processo di registrazione con i comandi seguenti:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Usare un gruppo di risorse per archiviare metadati per il cluster.

Prima di tutto creare un gruppo di risorse in cui inserire la risorsa cluster connessa.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Output:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Connettere un cluster

Successivamente, il cluster Kubernetes verrà connesso ad Azure tramite `az connectedk8s connect` :

1. Verificare la connettività al cluster Kubernetes tramite uno dei seguenti elementi:
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
1. Distribuire gli agenti Azure Arc per Kubernetes usando Helm 3 nello `azure-arc` spazio dei nomi:

    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

**Output:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
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

## <a name="verify-connected-cluster"></a>Verificare il cluster connesso

Usare il comando seguente per elencare i cluster connessi:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Output:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

È anche possibile visualizzare questa risorsa nella [portale di Azure](https://portal.azure.com/).
1. Aprire il portale nel browser.
1. Passare al gruppo di risorse e alla risorsa Kubernetes di Azure Arc abilitata, in base al nome della risorsa e agli input del nome del gruppo di risorse usati in precedenza nel `az connectedk8s connect` comando.  
> [!NOTE]
> Dopo l'onboarding del cluster, sono necessari circa 5-10 minuti per la visualizzazione dei metadati del cluster (versione del cluster, versione dell'agente, numero di nodi e così via) nella pagina Panoramica della risorsa Kubernetes abilitata per Azure Arc in portale di Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Connettersi tramite un server proxy in uscita

Se il cluster si trova dietro un server proxy in uscita, l'interfaccia della riga di comando di Azure e gli agenti Kubernetes abilitati per Azure Arc devono instradare le richieste tramite il server proxy in uscita:

1. Controllare la versione dell' `connectedk8s` estensione installata nel computer:

    ```console
    az -v
    ```

    `connectedk8s`Per configurare gli agenti con il proxy in uscita, è necessaria la versione dell'estensione 0.2.5 +. Se nel computer è installata la versione 0.2.3 o precedente, seguire la [procedura di aggiornamento](#before-you-begin) per ottenere la versione più recente dell'estensione nel computer.

2. Impostare le variabili di ambiente necessarie per l'interfaccia della riga di comando di Azure per l'uso del server proxy in uscita:

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

3. Eseguire il comando Connect con i parametri proxy specificati:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * `excludedCIDR`La specifica `--proxy-skip-range` di in è importante per garantire che la comunicazione nel cluster non venga interruppe per gli agenti.
> * Mentre `--proxy-http` , `--proxy-https` e `--proxy-skip-range` sono previsti per la maggior parte degli ambienti proxy in uscita, `--proxy-cert` è necessario solo se i certificati attendibili del proxy devono essere inseriti nell'archivio certificati attendibili dei Pod dell'agente.
> * La specifica del proxy precedente è attualmente applicata solo per gli agenti di Azure Arc e non per i pod Flux usati in `sourceControlConfiguration` . Il team di Kubernetes abilitato per Azure Arc sta lavorando attivamente a questa funzionalità.

## <a name="azure-arc-agents-for-kubernetes"></a>Agenti di Azure Arc per Kubernetes

Kubernetes abilitato per Azure Arc distribuisce alcuni operatori nello spazio dei nomi `azure-arc`. È possibile visualizzare le distribuzioni e i pod usando:

```console
kubectl -n azure-arc get deployments,pods
```

**Output:**

```console
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

Verificare che tutti i Pod si trovino in uno `Running` stato.

## <a name="delete-a-connected-cluster"></a>Eliminare un cluster connesso

È possibile eliminare una risorsa `Microsoft.Kubernetes/connectedcluster` usando l'interfaccia della riga di comando di Azure o il portale di Azure.


* **Eliminazione con l'interfaccia** della riga di comando di Azure: usare il comando dell'interfaccia della riga di comando di Azure seguente per avviare l'eliminazione della risorsa Kubernetes abilitata per
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Questo comando rimuove la `Microsoft.Kubernetes/connectedCluster` risorsa e tutte le `sourcecontrolconfiguration` risorse associate in Azure. L'interfaccia della `helm uninstall` riga di comando di Azure usa per rimuovere anche gli agenti in esecuzione nel cluster.

* **Eliminazione in portale di Azure**: l'eliminazione della risorsa Kubernetes abilitata per Azure Arc in portale di Azure Elimina la `Microsoft.Kubernetes/connectedcluster` risorsa e tutte le `sourcecontrolconfiguration` risorse associate in Azure  , ma non rimuove gli agenti in esecuzione nel cluster. 

  Per rimuovere gli agenti in esecuzione nel cluster, eseguire il comando seguente:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Passaggi successivi

* [Usare GitOps in un cluster connesso](./use-gitops-connected-cluster.md)
* [Usare Criteri di Azure per gestire la configurazione del cluster](./use-azure-policy.md)
