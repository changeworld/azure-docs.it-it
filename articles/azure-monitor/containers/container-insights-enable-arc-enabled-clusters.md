---
title: Monitorare i cluster Kubernetes abilitati per Azure Arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Raccogliere le metriche e i log dei cluster Kubernetes abilitati per Azure Arc con monitoraggio di Azure
ms.openlocfilehash: 0a983f6d7032310d02d35e713482de942bfbfd70
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443851"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure monitor container Insights per i cluster Kubernetes abilitati per Azure Arc

[Azure monitor container Insights](container-insights-overview.md) offre un'esperienza di monitoraggio avanzata per i cluster Kubernetes abilitati per Azure Arc.

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>Configurazioni supportate

- Azure monitor container Insights supporta il monitoraggio di Azure Arc abilitato Kubernetes (anteprima), come descritto nell'articolo [introduttivo](container-insights-overview.md) , ad eccezione della funzionalità dati in tempo reale (anteprima). Inoltre, non è necessario che gli utenti dispongano delle autorizzazioni di [proprietario](../../role-based-access-control/built-in-roles.md#owner) per [abilitare le metriche](container-insights-update-metrics.md)
- `Docker`, `Moby` e runtime di contenitori compatibili con CRI, ad esempio `CRI-O` e `containerd` .
- Il proxy in uscita senza autenticazione e proxy in uscita con l'autenticazione di base è supportato. Il proxy in uscita che prevede certificati attendibili non è al momento supportato.

## <a name="prerequisites"></a>Prerequisiti

- Sono stati soddisfatti i prerequisiti elencati nella [documentazione relativa alle estensioni cluster generiche](../../azure-arc/kubernetes/extensions.md#prerequisites).
- Un'area di lavoro Log Analytics: Azure monitor container Insights supporta un'area di lavoro Log Analytics nelle aree elencate nella [pagina prodotti di](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)Azure in base all'area. È possibile creare un'area di lavoro personalizzata tramite [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md)o [portale di Azure](../logs/quick-create-workspace.md).
- È necessario avere l'assegnazione di ruolo [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) nella sottoscrizione di Azure contenente la risorsa Kubernetes abilitata per Azure Arc. Se l'area di lavoro Log Analytics si trova in una sottoscrizione diversa, è necessario Log Analytics assegnazione di ruolo [collaboratore](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro log Analytics.
- Per visualizzare i dati di monitoraggio, è necessario disporre di Log Analytics assegnazione di ruolo [Reader](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro log Analytics.
- Gli endpoint seguenti devono essere abilitati per l'accesso in uscita, oltre a quelli indicati in [connessione di un cluster Kubernetes ad Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements).

    | Endpoint | Porta |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Se la risorsa Kubernetes abilitata per l'Arc è nell'ambiente Azure per enti pubblici, è necessario abilitare gli endpoint seguenti per l'accesso in uscita:

    | Endpoint | Porta |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Se in precedenza si è distribuito Azure monitor container Insights in questo cluster usando uno script senza estensioni cluster, seguire le istruzioni riportate [qui](container-insights-optout-hybrid.md) per eliminare questo grafico Helm. È quindi possibile continuare a creare un'istanza di estensione del cluster per Azure monitor container Insights.

    >[!NOTE]
    > La versione basata su script della distribuzione di Azure monitor container Insights (anteprima) viene sostituita dalla forma di distribuzione dell' [estensione del cluster](../../azure-arc/kubernetes/extensions.md) . Il monitoraggio di Azure distribuito in precedenza tramite script è supportato solo fino a giugno 2021 ed è quindi consigliabile eseguire la migrazione al modulo di distribuzione dell'estensione del cluster al più presto.

### <a name="identify-workspace-resource-id"></a>Identificare l'ID risorsa dell'area di lavoro

Eseguire i comandi seguenti per individuare l'identificatore Azure Resource Manager completo dell'area di lavoro Log Analytics. 

1. Elencare tutte le sottoscrizioni a cui si ha accesso usando il comando seguente:

    ```azurecli
    az account list --all -o table
    ```

2. Passare alla sottoscrizione che ospita l'area di lavoro Log Analytics usando il comando seguente:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Nell'esempio seguente viene visualizzato l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Nell'output trovare il nome dell'area di lavoro di interesse. Il `id` campo di che rappresenta l'identificatore Azure Resource Manager dell'area di lavoro log Analytics.

    >[!TIP]
    > Questa operazione `id` è disponibile anche nel pannello *Panoramica* dell'area di lavoro Log Analytics tramite l'portale di Azure.

## <a name="create-extension-instance-using-azure-cli"></a>Creare un'istanza di estensione usando l'interfaccia della riga di comando

### <a name="option-1---with-default-values"></a>Opzione 1-con i valori predefiniti

Questa opzione Usa i valori predefiniti seguenti:

- Crea o usa l'area di lavoro di log Analytics predefinita esistente corrispondente all'area del cluster
- L'aggiornamento automatico è abilitato per l'estensione del cluster di monitoraggio di Azure

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>Opzione 2-con l'area di lavoro di Azure Log Analytics esistente

È possibile usare un'area di lavoro di Azure Log Analytics esistente in qualsiasi sottoscrizione per cui si dispone di un *collaboratore* o un'assegnazione di ruolo più permissiva.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>Opzione 3-con configurazione avanzata

Per modificare le richieste e i limiti predefiniti delle risorse, è possibile usare le impostazioni di configurazione avanzate:

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

Per le impostazioni di configurazione disponibili, vedere la [sezione relativa alle richieste e ai limiti delle risorse del grafico Helm](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml) .

### <a name="option-4---on-azure-stack-edge"></a>Opzione 4-on Azure Stack Edge

Se il cluster Kubernetes abilitato per Azure Arc si trova su Azure Stack Edge, è necessario usare un percorso di montaggio personalizzato `/home/data/docker` .

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Se si specifica in modo esplicito la versione dell'estensione da installare nel comando crea, assicurarsi che la versione specificata sia >= 2.8.2.

## <a name="create-extension-instance-using-azure-portal"></a>Creare un'istanza di estensione usando portale di Azure

>[!IMPORTANT]
>  Se si distribuisce monitoraggio di Azure in un cluster Kubernetes in esecuzione in Azure Stack Edge, è necessario seguire l'opzione dell'interfaccia della riga di comando di Azure anziché l'opzione portale di Azure perché è necessario impostare un percorso di montaggio personalizzato per questi cluster.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Caricamento dal pannello delle risorse Kubernetes abilitato per Azure Arc

1. Nella portale di Azure selezionare il cluster Kubernetes con Arc abilitato che si desidera monitorare.

2. Selezionare l'elemento "Insights (anteprima)" nella sezione "monitoraggio" del pannello della risorsa.

3. Nella pagina onboarding selezionare il pulsante "Configura monitoraggio di Azure"

4. È ora possibile scegliere l' [area di lavoro log Analytics](../logs/quick-create-workspace.md) per inviare le metriche e i dati di log a.

5. Selezionare il pulsante "Configura" per distribuire l'estensione del cluster Azure monitor container Insights.

### <a name="onboarding-from-azure-monitor-blade"></a>Caricamento dal pannello monitoraggio di Azure

1. Nel portale di Azure passare al pannello ' Monitor ' e selezionare l'opzione ' containers ' nel menu ' Insights '.

2. Selezionare la scheda "cluster non monitorati" per visualizzare i cluster Kubernetes abilitati per Azure Arc per i quali è possibile abilitare il monitoraggio.

3. Fare clic sul collegamento ' Abilità accanto al cluster per il quale si desidera abilitare il monitoraggio.

4. Scegliere l'area di lavoro Log Analytics e selezionare il pulsante "Configura" per continuare.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Creare un'istanza di estensione usando Azure Resource Manager

1. Scaricare Azure Resource Manager modello e il parametro:

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. Aggiornare i valori dei parametri in arc-k8s-azmon-extension-arm-template-params.jssu file. Per il cloud pubblico di Azure, `opinsights.azure.com` deve essere usato come valore di workspaceDomain.

3. Distribuire il modello per creare l'estensione Azure monitor container Insights 

    ```console
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>Elimina istanza di estensione

Il comando che segue elimina solo l'istanza dell'estensione, ma non elimina l'area di lavoro Log Analytics. I dati all'interno della risorsa Log Analytics rimangono intatti.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Cluster disconnesso
Se il cluster è disconnesso da Azure per > 48 ore, Azure Resource Graph non avrà informazioni sul cluster. Di conseguenza, il pannello Insights può visualizzare informazioni non corrette sullo stato del cluster.

## <a name="next-steps"></a>Passaggi successivi

- Con il monitoraggio abilitato per raccogliere lo stato e l'utilizzo delle risorse del cluster Kubernetes abilitato per Arc e dei carichi di lavoro in esecuzione su di essi, Scopri [come usare](container-insights-analyze.md) il contenitore Insights.

- Per impostazione predefinita, l'agente in contenitori raccoglie i log del contenitore stdout/stderr di tutti i contenitori in esecuzione in tutti gli spazi dei nomi ad eccezione di Kube-System. Per configurare la raccolta di log del contenitore specifica per determinati spazi dei nomi o spazi dei nomi, esaminare la [configurazione dell'agente di container Insights](container-insights-agent-config.md) per configurare le impostazioni di raccolta dati desiderate nel file di configurazione di ConfigMap.

- Per rimuovere e analizzare le metriche di Prometeo dal cluster, vedere [configurare la metrica di Prometeo](container-insights-prometheus-integration.md)
