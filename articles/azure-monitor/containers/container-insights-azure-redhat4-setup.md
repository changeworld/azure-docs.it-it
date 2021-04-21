---
title: Configurare Azure Red Hat OpenShift v4.x con Container Insights | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio per un cluster Kubernetes con Monitoraggio di Azure ospitato in Azure Red Hat OpenShift versione 4 o successiva.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 11c702d1f46725a12e90a01dc1b38467344a1123
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784642"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Configurare Azure Red Hat OpenShift v4.x con Informazioni dettagliate sui contenitori

Informazioni dettagliate sui contenitori offre un'esperienza di monitoraggio servizio Azure Kubernetes cluster di motore del servizio Servizio Azure Kubernetes e del servizio Web Diaks. Questo articolo descrive come ottenere un'esperienza di monitoraggio simile abilitando il monitoraggio per i cluster Kubernetes ospitati in Azure Red Hat OpenShift [versione](../../openshift/intro-openshift.md) 4.x.

>[!NOTE]
>Il supporto per Azure Red Hat OpenShift è una funzionalità in anteprima pubblica in questo momento.
>

È possibile abilitare Informazioni dettagliate sul contenitore per una o più distribuzioni esistenti di Azure Red Hat OpenShift v4.x usando i metodi supportati descritti in questo articolo.

Per un cluster esistente, eseguire questo [script Bash nell'interfaccia della riga di comando di Azure.](/cli/azure/openshift#az_openshift_create&preserve-view=true)

## <a name="supported-and-unsupported-features"></a>Funzionalità supportate e non supportate

Informazioni dettagliate sui contenitori supporta Azure Red Hat OpenShift v4.x, come descritto in [Panoramica di Informazioni dettagliate](container-insights-overview.md)sui contenitori , ad eccezione delle funzionalità seguenti:

- Live Data (anteprima)
- [Raccolta di metriche](container-insights-update-metrics.md) da nodi e pod del cluster e archiviazione nel database Monitoraggio di Azure metriche

## <a name="prerequisites"></a>Prerequisiti

- Interfaccia della riga di comando di Azure versione 2.0.72 o successiva  

- Strumento [dell'interfaccia della riga di](https://helm.sh/docs/intro/install/) comando helm 3

- Versione più recente [dell'interfaccia della riga di comando di OpenShift](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)

- [Bash versione 4](https://www.gnu.org/software/bash/)

- Strumento da riga di comando [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Un'[area di lavoro Log Analytics](../logs/design-logs-deployment.md).

    Informazioni dettagliate sui contenitori supporta un'area di lavoro Log Analytics nelle aree elencate in Prodotti di Azure [per area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Per creare una propria area di lavoro, è possibile crearla tramite [Azure Resource Manager](../logs/resource-manager-workspace.md), [tramite PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)o nella portale di Azure [.](../logs/quick-create-workspace.md)

- Per abilitare e accedere alle funzionalità in Informazioni dettagliate sui contenitori, è necessario avere almeno un ruolo Collaboratore di *Azure* nella sottoscrizione di Azure e un ruolo Collaboratore [*Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro Log Analytics, configurato con Informazioni dettagliate sul contenitore.

- Per visualizzare i dati di monitoraggio, è necessario avere il ruolo di lettore [*di Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro Log Analytics, configurato con Informazioni dettagliate sul contenitore.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Abilitare il monitoraggio per un cluster esistente

Per abilitare il monitoraggio per un cluster Azure Red Hat OpenShift versione 4 o successiva distribuito in Azure usando lo script Bash fornito, eseguire le operazioni seguenti:

1. Accedere ad Azure mediante il comando seguente:

    ```azurecli
    az login
    ```

1. Scaricare e salvare in una cartella locale lo script che configura il cluster con il componente aggiuntivo di monitoraggio eseguendo il comando seguente:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Connettersi al cluster ARO v4 usando le istruzioni in [Esercitazione: Connettersi a un cluster Azure Red Hat OpenShift 4](../../openshift/tutorial-connect-cluster.md).


### <a name="integrate-with-an-existing-workspace"></a>Eseguire l'integrazione con un'area di lavoro esistente

In questa sezione viene abilitato il monitoraggio del cluster usando lo script Bash scaricato in precedenza. Per eseguire l'integrazione con un'area di lavoro Log Analytics esistente, iniziare identificando l'ID risorsa completo dell'area di lavoro Log Analytics necessario per il parametro e quindi eseguire il comando per abilitare il componente aggiuntivo di monitoraggio nell'area di lavoro `logAnalyticsWorkspaceResourceId` specificata.

Se non si ha un'area di lavoro [](#integrate-with-the-default-workspace) da specificare, è possibile passare direttamente alla sezione Integrazione con l'area di lavoro predefinita e consentire allo script di creare automaticamente una nuova area di lavoro.

1. Elencare tutte le sottoscrizioni a cui si ha accesso eseguendo il comando seguente:

    ```azurecli
    az account list --all -o table
    ```

    L'output sarà simile al seguente:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Copiare il valore per **SubscriptionId**.

1. Passare alla sottoscrizione che ospita l'area di lavoro Log Analytics eseguendo il comando seguente:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Visualizzare l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito eseguendo il comando seguente:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Nell'output trovare il nome dell'area di lavoro e quindi copiare l'ID risorsa completo dell'area di lavoro Log Analytics nel **campo ID**.

1. Per abilitare il monitoraggio, eseguire il comando seguente. Sostituire i valori per i `azureAroV4ClusterResourceId` parametri `logAnalyticsWorkspaceResourceId` e .

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Ecco il comando che è necessario eseguire dopo aver popolato le 3 variabili con i comandi export:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

Dopo aver abilitato il monitoraggio, potrebbero essere circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="integrate-with-the-default-workspace"></a>Eseguire l'integrazione con l'area di lavoro predefinita

In questa sezione viene abilitato il monitoraggio per il cluster Azure Red Hat OpenShift v4.x usando lo script Bash scaricato.

In questo esempio non è necessario creare o specificare un'area di lavoro esistente. Questo comando semplifica il processo creando un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster, se non ne esiste già una nell'area.

L'area di lavoro predefinita creata è nel formato *DefaultWorkspace- \<GUID> - \<Region>*.  

Sostituire il valore per il `azureAroV4ClusterResourceId` parametro .

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Ad esempio:

'bash enable-monitoring.sh --resource-id $azureAroV 4ClusterResourceId 

Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="enable-monitoring-from-the-azure-portal"></a>Abilitare il monitoraggio dal portale di Azure

La visualizzazione multi-cluster in Informazioni dettagliate sui contenitori evidenzia Azure Red Hat OpenShift cluster che non hanno il monitoraggio abilitato nella **scheda Cluster non monitorati.** **L'opzione** Abilita accanto al cluster non avvia l'onboarding del monitoraggio dal portale. Si verrà reindirizzati a questo articolo per abilitare manualmente il monitoraggio seguendo la procedura descritta in precedenza in questo articolo.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro sinistro o nell'elenco home page selezionare **Monitoraggio di Azure**.

1. Nella sezione **Informazioni** dettagliate selezionare **Contenitori**.

1. Nella pagina **Monitoraggio - contenitori** selezionare **Cluster non monitorati**.

1. Nell'elenco dei cluster non monitorati selezionare il cluster e quindi **selezionare Abilita**.

    È possibile identificare i risultati nell'elenco cercando il **valore ARO** nella **colonna Tipo di** cluster. Dopo aver selezionato **Abilita**, si viene reindirizzati a questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- Dopo aver abilitato il monitoraggio per raccogliere l'integrità e l'utilizzo delle risorse del cluster RedHat OpenShift [](container-insights-analyze.md) versione 4.x e dei carichi di lavoro in esecuzione su di essi, informazioni su come usare Le informazioni dettagliate sui contenitori.

- Per impostazione predefinita, l'agente in contenitori raccoglie i log dei contenitori *stdout* e *stderr* di tutti i contenitori in esecuzione in tutti gli spazi dei nomi, ad eccezione di kube-system. Per configurare una raccolta di log del contenitore specifica per uno o più spazi dei nomi specifici, vedere Configurazione dell'agente di [Container Insights](container-insights-agent-config.md) per configurare le impostazioni di raccolta dati desiderate per il file di configurazione *ConfigMap.*

- Per esaminare e analizzare le metriche di Prometheus dal cluster, vedere Configurare lo scraping delle metriche di [Prometheus.](container-insights-prometheus-integration.md)

- Per informazioni su come arrestare il monitoraggio del cluster usando Informazioni dettagliate sul contenitore, vedere Come arrestare il [monitoraggio del cluster Azure Red Hat OpenShift contenitore.](./container-insights-optout-openshift-v3.md)
