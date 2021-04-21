---
title: Configurare Azure Red Hat OpenShift v3.x con Container Insights | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio di un cluster Kubernetes con Monitoraggio di Azure ospitati Azure Red Hat OpenShift versione 3 e successive.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a2910655601548f39983547e12460d949901954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784678"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>Configurare Azure Red Hat OpenShift v3 con Informazioni dettagliate sul contenitore

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11 verrà ritirato a giugno 2022.
>
> A partire da ottobre 2020 non sarà più possibile creare nuovi cluster 3.11.
> I cluster 3.11 esistenti continueranno a funzionare fino a giugno 2022, ma non saranno più supportati dopo tale data.
>
> Seguire questa guida per [creare un cluster Azure Red Hat OpenShift 4](../../openshift/tutorial-create-cluster.md).
> In caso di domande specifiche, [contattare Microsoft.](mailto:aro-feedback@microsoft.com)

Le informazioni dettagliate sui contenitori forniscono un'esperienza di monitoraggio servizio Azure Kubernetes i cluster del motore del servizio Servizio Azure Kubernetes e del motore del servizio Container. Questo articolo descrive come abilitare il monitoraggio dei cluster Kubernetes ospitati [in Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versione 3 e nell'ultima versione supportata della versione 3, per ottenere un'esperienza di monitoraggio simile.

>[!NOTE]
>Il supporto Azure Red Hat OpenShift è una funzionalità in anteprima pubblica al momento.
>

Le informazioni dettagliate sui contenitori possono essere abilitate per distribuzioni nuove o esistenti di Azure Red Hat OpenShift usando i metodi supportati seguenti:

- Per un cluster esistente dal portale di Azure o usando Azure Resource Manager modello.
- Per un nuovo cluster usando un Azure Resource Manager o durante la creazione di un nuovo cluster tramite l'interfaccia della riga [di comando di Azure.](/cli/azure/openshift#az_openshift_create)

## <a name="supported-and-unsupported-features"></a>Funzionalità supportate e non supportate

Container Insights supporta il monitoraggio Azure Red Hat OpenShift come descritto nell'articolo [Panoramica,](container-insights-overview.md) ad eccezione delle funzionalità seguenti:

- Dati live (anteprima)
- [Raccogliere le metriche](container-insights-update-metrics.md) dai nodi e dai pod del cluster e archiviarle nel database Monitoraggio di Azure metriche

## <a name="prerequisites"></a>Prerequisiti

- Un'[area di lavoro Log Analytics](../logs/design-logs-deployment.md).

    Informazioni dettagliate sui contenitori supporta un'area di lavoro Log Analytics nelle aree elencate in Prodotti Azure [per area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Per creare la propria area di lavoro, è possibile crearla [Azure Resource Manager](../logs/resource-manager-workspace.md), tramite [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)o nella portale di Azure [.](../logs/quick-create-workspace.md)

- Per abilitare e accedere alle funzionalità in Informazioni dettagliate sui contenitori, è necessario almeno essere un membro del ruolo Collaboratore di *Azure* nella sottoscrizione di Azure e un membro del ruolo [*Collaboratore Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) dell'area di lavoro Log Analytics configurata con Informazioni dettagliate sul contenitore.

- Per visualizzare i dati di monitoraggio, si è membri dell'autorizzazione ruolo lettore [*di Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) con l'area di lavoro Log Analytics configurata con Informazioni dettagliate sui contenitori.

## <a name="identify-your-log-analytics-workspace-id"></a>Identificare l'ID dell'area di lavoro Log Analytics

 Per eseguire l'integrazione con un'area di lavoro Log Analytics esistente, iniziare identificando l'ID risorsa completo dell'area di lavoro Log Analytics. L'ID risorsa dell'area di lavoro è obbligatorio per il parametro quando si abilita il `workspaceResourceId` monitoraggio usando il Azure Resource Manager del modello.

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

1. Copiare il valore di **SubscriptionId**.

1. Passare alla sottoscrizione che ospita l'area di lavoro Log Analytics eseguendo il comando seguente:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Visualizzare l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito eseguendo il comando seguente:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Nell'output trovare il nome dell'area di lavoro e quindi copiare l'ID risorsa completo dell'area di lavoro Log Analytics nel **campo ID**.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Abilitare per un nuovo cluster usando un modello Azure Resource Manager cluster

Seguire questa procedura per distribuire un cluster Azure Red Hat OpenShift con il monitoraggio abilitato. Prima di procedere, vedere [l'esercitazione Creare](../../openshift/tutorial-create-cluster.md) un cluster Azure Red Hat OpenShift per comprendere le dipendenze che è necessario configurare in modo che l'ambiente sia configurato correttamente.

Questo metodo include due modelli JSON. Un modello specifica la configurazione per distribuire il cluster con il monitoraggio abilitato e l'altro contiene i valori dei parametri che è possibile configurare per specificare quanto segue:

- ID Azure Red Hat OpenShift risorsa cluster.

- Gruppo di risorse in cui viene distribuito il cluster.

- [Azure Active Directory'ID tenant](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) specificato dopo aver eseguito i passaggi per crearne uno o uno già creato.

- [Azure Active Directory'ID applicazione client](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) specificato dopo aver eseguito i passaggi per crearne uno o uno già creato.

- [Azure Active Directory segreto client](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) specificato dopo aver eseguito i passaggi per crearne uno o uno già creato.

- [Azure AD gruppo di sicurezza](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) specificato dopo aver eseguito la procedura per crearne uno o uno già creato.

- ID risorsa di un'area di lavoro Log Analytics esistente. Per [informazioni su come ottenere queste informazioni,](#identify-your-log-analytics-workspace-id) vedere Identificare l'ID dell'area di lavoro Log Analytics.

- Numero di nodi master da creare nel cluster.

- Numero di nodi di calcolo nel profilo del pool di agenti.

- Numero di nodi dell'infrastruttura nel profilo del pool di agenti.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Scaricare e salvare in una cartella locale, il modello Azure Resource Manager e il file di parametri, per creare un cluster con il componente aggiuntivo di monitoraggio usando i comandi seguenti:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Accedere ad Azure

    ```azurecli
    az login
    ```

    Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

3. Creare un gruppo di risorse per il cluster se non ne è già presente uno. Per un elenco delle aree di Azure che supportaNo OpenShift in Azure, vedere [Aree supportate](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Modificare il file di parametri **JSONnewClusterWithMonitoringParam.jse** aggiornare i valori seguenti:

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. Il passaggio seguente distribuisce il cluster con il monitoraggio abilitato usando l'interfaccia della riga di comando di Azure.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    L'output sarà simile al seguente:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Abilitare per un cluster esistente

Seguire questa procedura per abilitare il monitoraggio di un cluster Azure Red Hat OpenShift distribuito in Azure. È possibile eseguire questa operazione dal portale di Azure o usando i modelli forniti.

### <a name="from-the-azure-portal"></a>Dal portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu portale di Azure o nella home page selezionare **Monitoraggio di Azure**. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**.

3. Nella pagina **Monitoraggio - Contenitori** selezionare **Cluster non monitorati**.

4. Nell'elenco dei cluster non monitorati individuare il cluster nell'elenco e fare clic su **Abilita**. È possibile identificare i risultati nell'elenco cercando il valore **ARO** nella colonna **CLUSTER TYPE**.

5. Nella pagina **Onboarding in Informazioni** dettagliate contenitore selezionare un'area di lavoro Log Analytics esistente nella stessa sottoscrizione del cluster nell'elenco a discesa.  
    L'elenco preseleziona l'area di lavoro e il percorso predefiniti in cui viene distribuito il cluster nella sottoscrizione.

    ![Abilitare il monitoraggio per i cluster non monitorati](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se si vuole creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio dal cluster, seguire le istruzioni in [Creare un'area di lavoro Log Analytics](../logs/quick-create-workspace.md). Assicurarsi di creare l'area di lavoro nella stessa sottoscrizione in cui è distribuito il cluster RedHat OpenShift.

Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

### <a name="enable-using-an-azure-resource-manager-template"></a>Abilitare l'uso di Azure Resource Manager modello

Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

- ID risorsa cluster di Azure RedHat OpenShift.

- Gruppo di risorse in cui viene distribuito il cluster.

- Un'area di lavoro Log Analytics. Per [informazioni su come ottenere queste informazioni,](#identify-your-log-analytics-workspace-id) vedere Identificare l'ID dell'area di lavoro Log Analytics.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Scaricare il modello e il file dei parametri per aggiornare il cluster con il componente aggiuntivo di monitoraggio usando i comandi seguenti:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Accedere ad Azure

    ```azurecli
    az login
    ```

    Se si ha accesso a più sottoscrizioni, eseguire `az account set -s {subscription ID}` sostituendo `{subscription ID}` con la sottoscrizione che si vuole usare.

3. Specificare la sottoscrizione del cluster Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Eseguire il comando seguente per identificare il percorso del cluster e l'ID risorsa:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Modificare il file di parametri JSON **existingClusterParam.js** e aggiornare i valori *aroResourceId* *e aroResourceLocation*. Il valore per **workspaceResourceId** è l'ID risorsa completo dell'area di lavoro Log Analytics, che include il nome dell'area di lavoro.

6. Per eseguire la distribuzione con l'interfaccia della riga di comando di Azure, eseguire i comandi seguenti:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    L'output sarà simile al seguente:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Passaggi successivi

- Con il monitoraggio abilitato per raccogliere l'integrità e l'utilizzo delle risorse del cluster RedHat OpenShift e dei carichi di lavoro in esecuzione su di essi, informazioni su come [usare](container-insights-analyze.md) Le informazioni dettagliate sui contenitori.

- Per impostazione predefinita, l'agente in contenitori raccoglie i log del contenitore stdout/stderr di tutti i contenitori in esecuzione in tutti gli spazi dei nomi, ad eccezione di kube-system. Per configurare la raccolta di log del contenitore specifica per spazi dei nomi o spazi dei nomi specifici, esaminare la configurazione dell'agente di [Container Insights](container-insights-agent-config.md) per configurare le impostazioni di raccolta dati desiderate nel file di configurazioni di ConfigMap.

- Per raschiare e analizzare le metriche di Prometheus dal cluster, vedere Configurare lo scraping delle metriche [di Prometheus](container-insights-prometheus-integration.md)

- Per informazioni su come arrestare il monitoraggio del cluster con Informazioni dettagliate sui contenitori, vedere [Come arrestare](./container-insights-optout-openshift-v3.md)il monitoraggio Azure Red Hat OpenShift cluster .