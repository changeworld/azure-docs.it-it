---
title: Configurare Azure Red Hat OpenShift V4. x con container Insights | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio per un cluster Kubernetes con monitoraggio di Azure ospitato in Azure Red Hat OpenShift versione 4 o successiva.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 02cb794463b965ebafef0b6861477dbf69227511
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506413"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Configurare Azure Red Hat OpenShift V4. x con il contenitore Insights

Il contenitore Insights offre un'esperienza di monitoraggio avanzata per i cluster di Azure Kubernetes Service (AKS) e del motore AKS. Questo articolo descrive come ottenere un'esperienza di monitoraggio simile abilitando il monitoraggio per i cluster Kubernetes ospitati in [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versione 4. x.

>[!NOTE]
>Il supporto per Azure Red Hat OpenShift è una funzionalità di anteprima pubblica al momento.
>

È possibile abilitare il contenitore Insights per una o più distribuzioni esistenti di Azure Red Hat OpenShift V4. x usando i metodi supportati descritti in questo articolo.

Per un cluster esistente, eseguire questo [script bash nell'interfaccia della riga di comando di Azure](/cli/azure/openshift#az-openshift-create&preserve-view=true).

## <a name="supported-and-unsupported-features"></a>Funzionalità supportate e non supportate

Il contenitore Insights supporta il monitoraggio di Azure Red Hat OpenShift V4. x, come descritto in [Panoramica di container Insights](container-insights-overview.md), ad eccezione delle funzionalità seguenti:

- Dati in tempo reale (anteprima)
- [Raccolta di metriche](container-insights-update-metrics.md) da nodi e Pod del cluster e archiviazione nel database di metriche di monitoraggio di Azure

## <a name="prerequisites"></a>Prerequisiti

- L'interfaccia della riga di comando di Azure versione 2.0.72 o successiva  

- Strumento dell'interfaccia della riga di [comando di Helm 3](https://helm.sh/docs/intro/install/)

- [Bash versione 4](https://www.gnu.org/software/bash/)

- Strumento da riga di comando [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Un'[area di lavoro Log Analytics](../logs/design-logs-deployment.md).

    Il contenitore Insights supporta un'area di lavoro Log Analytics nelle aree elencate in [prodotti di](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)Azure in base all'area. Per creare un'area di lavoro personalizzata, è possibile crearla tramite [Azure Resource Manager](../logs/resource-manager-workspace.md), tramite [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)o nel [portale di Azure](../logs/quick-create-workspace.md).

- Per abilitare e accedere alle funzionalità in informazioni dettagliate sul contenitore, è necessario avere almeno un ruolo di *collaboratore* di Azure nella sottoscrizione di Azure e un ruolo di [*collaboratore log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro log Analytics, configurato con informazioni dettagliate sul contenitore.

- Per visualizzare i dati di monitoraggio, è necessario avere [*log Analytics ruolo Reader*](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro log Analytics, configurato con informazioni dettagliate sul contenitore.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Abilitare il monitoraggio per un cluster esistente

Per abilitare il monitoraggio per un cluster Azure Red Hat OpenShift versione 4 o successiva distribuito in Azure usando lo script bash specificato, seguire questa procedura:

1. Accedere ad Azure mediante il comando seguente:

    ```azurecli
    az login
    ```

1. Scaricare e salvare in una cartella locale lo script che configura il cluster con il componente aggiuntivo di monitoraggio eseguendo il comando seguente:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Connettersi al cluster ARO V4 usando le istruzioni riportate in [esercitazione: connettersi a un cluster di Azure Red Hat OpenShift 4](../../openshift/tutorial-connect-cluster.md).


### <a name="integrate-with-an-existing-workspace"></a>Eseguire l'integrazione con un'area di lavoro esistente

In questa sezione viene abilitato il monitoraggio del cluster usando lo script bash scaricato in precedenza. Per l'integrazione con un'area di lavoro Log Analytics esistente, iniziare identificando l'ID risorsa completo dell'area di lavoro Log Analytics richiesta per il `logAnalyticsWorkspaceResourceId` parametro, quindi eseguire il comando per abilitare il componente aggiuntivo di monitoraggio per l'area di lavoro specificata.

Se non si dispone di un'area di lavoro da specificare, è possibile passare alla sezione [integrare con l'area di lavoro predefinita](#integrate-with-the-default-workspace) e consentire allo script di creare automaticamente una nuova area di lavoro.

1. Elencare tutte le sottoscrizioni a cui è possibile accedere eseguendo il comando seguente:

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

1. Per visualizzare l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito, eseguire il comando seguente:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Nell'output trovare il nome dell'area di lavoro, quindi copiare l'ID risorsa completo dell'area di lavoro Log Analytics sotto l' **ID** campo.

1. Per abilitare il monitoraggio, eseguire il comando seguente. Sostituire i valori per i `azureAroV4ClusterResourceId` `logAnalyticsWorkspaceResourceId` parametri e.

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Ecco il comando che è necessario eseguire dopo avere popolato le 3 variabili con i comandi di esportazione:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

Dopo aver abilitato il monitoraggio, potrebbero essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="integrate-with-the-default-workspace"></a>Eseguire l'integrazione con l'area di lavoro predefinita

In questa sezione viene abilitato il monitoraggio per il cluster Azure Red Hat OpenShift V4. x usando lo script bash scaricato.

In questo esempio non è necessario creare in anticipo o specificare un'area di lavoro esistente. Questo comando semplifica il processo creando un'area di lavoro predefinita nel gruppo di risorse predefinito della sottoscrizione del cluster, se non ne esiste già una.

L'area di lavoro predefinita creata ha il formato *DefaultWorkspace- \<GUID> - \<Region>*.  

Sostituire il valore per il `azureAroV4ClusterResourceId` parametro.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Ad esempio:

' bash enable-monitoring.sh--Resource-ID $azureAroV 4ClusterResourceId 

Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="enable-monitoring-from-the-azure-portal"></a>Abilitare il monitoraggio dalla portale di Azure

La visualizzazione multicluster in container Insights evidenzia i cluster OpenShift di Azure Red Hat che non dispongono di monitoraggio abilitato nella scheda **cluster non monitorati** . L'opzione **Abilita** accanto al cluster non avvia l'onboarding del monitoraggio dal portale. Si viene reindirizzati a questo articolo per abilitare il monitoraggio manualmente attenendosi alla procedura illustrata in precedenza in questo articolo.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro sinistro o dal home page selezionare **monitoraggio di Azure**.

1. Nella sezione **Insights** selezionare **contenitori**.

1. Nella pagina **monitoraggio-contenitori** selezionare cluster non **monitorati**.

1. Nell'elenco dei cluster non monitorati selezionare il cluster e quindi fare clic su **Abilita**.

    È possibile identificare i risultati nell'elenco cercando il valore **Aro** nella colonna **tipo cluster** . Dopo aver selezionato **Abilita**, si viene reindirizzati a questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- Ora che è stato abilitato il monitoraggio per raccogliere lo stato e l'utilizzo delle risorse del cluster RedHat OpenShift versione 4. x e dei carichi di lavoro in esecuzione su di essi, informazioni su [come usare](container-insights-analyze.md) il contenitore Insights.

- Per impostazione predefinita, l'agente in contenitori raccoglie i log del contenitore *stdout* e *stderr* di tutti i contenitori in esecuzione in tutti gli spazi dei nomi ad eccezione di Kube-System. Per configurare una raccolta di log del contenitore specifica di uno spazio dei nomi o di spazi dei nomi specifico, vedere [configurazione dell'agente di container Insights](container-insights-agent-config.md) per configurare le impostazioni di raccolta dati desiderate per il file di configurazione *ConfigMap* .

- Per eliminare e analizzare le metriche Prometheus dal cluster, vedere Configurare il ritaglio delle [metriche Prometeo](container-insights-prometheus-integration.md).

- Per informazioni su come interrompere il monitoraggio del cluster usando informazioni dettagliate sul contenitore, vedere [come arrestare il monitoraggio del cluster OpenShift di Azure Red Hat](./container-insights-optout-openshift-v3.md).
