---
title: Proteggi le distribuzioni Kubernetes ibride e cloud con Azure Defender per Kubernetes
description: Usare Azure Defender per Kubernetes con i cluster Kubernetes locali e multicloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 940cae8829a99ee7ffacdb41844237acc85b7761
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029200"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Difendi i cluster Kubernetes abilitati per Azure Arc in esecuzione in ambienti locali e cloud

L' **estensione Azure Defender per i cluster Kubernetes** è in grado di difendere i cluster locali con le stesse funzionalità di rilevamento delle minacce offerte per i cluster del servizio Kubernetes di Azure. Abilitare [Azure Arc Enabled Kubernetes](../azure-arc/kubernetes/overview.md) nei cluster e distribuire l'estensione come descritto in questa pagina. 

L'estensione può anche proteggere i cluster Kubernetes su altri provider di servizi cloud, anche se non nei servizi Kubernetes gestiti.

> [!TIP]
> Sono stati inseriti alcuni file di esempio per semplificare il processo di installazione in [esempi di installazione in GitHub](https://aka.ms/kubernetes-extension-installation-examples).

## <a name="availability"></a>Disponibilità

| Aspetto | Dettagli |
|--------|---------|
| Stato versione | **Anteprima**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Ruoli e autorizzazioni necessari | L' [amministratore della sicurezza](../role-based-access-control/built-in-roles.md#security-admin) può ignorare gli avvisi<br>Il [lettore di sicurezza](../role-based-access-control/built-in-roles.md#security-reader) può visualizzare i risultati |
| Prezzi | Richiede [Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md) |
| Distribuzioni di Kubernetes supportate | [Servizio Azure Kubernetes in Azure Stack HCI](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [Motore del servizio Azure Kubernetes](https://github.com/Azure/aks-engine)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (versione 4,6 o successive) |
| Limitazioni | Azure Arc Enabled Kubernetes e l'estensione di Azure Defender **non supportano** le offerte Kubernetes gestite come il motore di Google Kubernetes e il servizio di Kubernetes elastico. [Azure Defender è disponibile in modo nativo per il servizio Azure Kubernetes (AKS)](defender-for-kubernetes-introduction.md) e non richiede la connessione del cluster ad Azure Arc. |
| Ambienti e aree | La disponibilità di questa estensione è identica a quella di [Azure Arc abilitata per Kubernetes](../azure-arc/kubernetes/overview.md)|

## <a name="architecture-overview"></a>Panoramica dell'architettura

Per tutti i cluster Kubernetes diversi da AKS, è necessario connettere il cluster ad Azure Arc. Una volta stabilita la connessione, Azure Defender per Kubernetes può essere distribuito in Azure Arc con le risorse [Kubernetes abilitate](../azure-arc/kubernetes/overview.md) come [estensione del cluster](../azure-arc/kubernetes/extensions.md).

I componenti di estensione raccolgono i dati dei log di controllo di Kubernetes da tutti i nodi del piano di controllo nel cluster e li inviano ad Azure Defender per il back-end Kubernetes nel cloud per un'ulteriore analisi. L'estensione viene registrata con un'area di lavoro Log Analytics utilizzata come pipeline di dati, ma i dati del registro di controllo non vengono archiviati nell'area di lavoro Log Analytics.

Questo diagramma mostra l'interazione tra Azure Defender per Kubernetes e il cluster Kubernetes abilitato per Azure Arc:

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Un diagramma dell'architettura di alto livello che illustra l'interazione tra Azure Defender per Kubernetes e un cluster Kubernetes abilitato per Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Prerequisiti

- Azure Defender per Kubernetes è [abilitato nella sottoscrizione](enable-azure-defender.md)
- Il cluster Kubernetes è [connesso ad Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- Sono stati soddisfatti i prerequisiti elencati nella [documentazione relativa alle estensioni cluster generiche](../azure-arc/kubernetes/extensions.md#prerequisites).

## <a name="deploy-the-azure-defender-extension"></a>Distribuire l'estensione di Azure Defender

È possibile distribuire l'estensione di Azure Defender usando un intervallo di metodi. Per la procedura dettagliata, selezionare la scheda pertinente.

### <a name="azure-portal"></a>[**Portale di Azure**](#tab/k8s-deploy-asc)

### <a name="use-the-quick-fix-option-from-the-security-center-recommendation"></a>Usare l'opzione "correzione rapida" dalla raccomandazione del Centro sicurezza

Una raccomandazione dedicata nel centro sicurezza di Azure offre:

- **Visibilità** dei cluster con l'estensione Defender per Kubernetes distribuita
- **Un'opzione di correzione rapida** per distribuirla in questi cluster senza estensione

1. Dalla pagina raccomandazioni del Centro sicurezza di Azure aprire il controllo di sicurezza **Abilita Azure Defender** .

1. Usare il filtro per trovare la raccomandazione denominata **Azure Arc Enabled cluster Kubernetes in cui è installata l'estensione di Azure Defender**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Raccomandazione del Centro sicurezza di Azure per la distribuzione dell'estensione Azure Defender per i cluster Kubernetes abilitati per Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Si noti l'icona correzione rapida nella colonna azioni

1. Selezionare l'estensione per visualizzare i dettagli delle risorse integre e non integre, ovvero i cluster con e senza l'estensione.

1. Dall'elenco delle risorse non integre selezionare un cluster e selezionare Correggi **per aprire** il riquadro con le opzioni di correzione.

1. Selezionare l'area di lavoro Log Analytics pertinente e selezionare **monitora e aggiorna la risorsa x**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Distribuire l'estensione di Azure Defender per Azure Arc con l'opzione di correzione rapida del Centro sicurezza.":::


### <a name="azure-cli"></a>[**Interfaccia della riga di comando di Azure**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Usare l'interfaccia della riga di comando di Azure per distribuire l'estensione di Azure Defender

1. Accedere ad Azure:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Assicurarsi di usare lo stesso ID sottoscrizione per ``<your-subscription-id>`` come quello usato durante la connessione del cluster ad Azure Arc.

1. Eseguire il comando seguente per distribuire l'estensione nella parte superiore del cluster Kubernetes abilitato per Azure Arc:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Di seguito è riportata una descrizione di tutte le impostazioni di configurazione supportate nel tipo di estensione Azure Defender:

    | Proprietà | Descrizione |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Facoltativo**. ID risorsa completo dell'area di lavoro Log Analytics.<br>Quando non viene specificato, verrà utilizzata l'area di lavoro predefinita dell'area.<br><br>Per ottenere l'ID di risorsa completo, eseguire il comando seguente per visualizzare l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito:<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>L'ID della risorsa dell'area di lavoro Log Analytics presenta la sintassi seguente:<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>Per altre informazioni, vedere [aree di lavoro log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces) |
    | auditLogPath |**Facoltativo**. Percorso completo dei file di log di controllo.<br>Quando non viene specificato, verrà usato il percorso predefinito ``/var/log/kube-apiserver/audit.log`` .<br>Per il motore AKS, il percorso standard è ``/var/log/kubeaudit/audit.log`` |

    Il comando seguente mostra un esempio di utilizzo di tutti i campi facoltativi:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Gestione risorse**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Usare Azure Resource Manager per distribuire l'estensione di Azure Defender

Per usare Azure Resource Manager per distribuire l'estensione di Azure Defender, è necessaria un'area di lavoro Log Analytics nella sottoscrizione. Per altre informazioni, vedere [aree di lavoro log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

È possibile usare l' **azure-defender-extension-arm-template.jssul** modello Gestione risorse dagli [esempi di installazione](https://aka.ms/kubernetes-extension-installation-examples)del Centro sicurezza.

> [!TIP]
> Se non si ha familiarità con Gestione risorse modelli, iniziare da qui: [cosa sono i modelli Azure Resource Manager?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**API REST**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>Usare l'API REST per distribuire l'estensione di Azure Defender 

Per usare l'API REST per distribuire l'estensione di Azure Defender, è necessaria un'area di lavoro Log Analytics nella sottoscrizione. Per altre informazioni, vedere [aree di lavoro log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

> [!TIP]
> Il modo più semplice per usare l'API per distribuire l'estensione di Azure Defender è con l'esempio **JSON di raccolta dei posting** fornito dagli [esempi di installazione](https://aka.ms/kubernetes-extension-installation-examples)del Centro sicurezza.
- Per modificare il JSON della raccolta dei post o per distribuire manualmente l'estensione con l'API REST, eseguire il comando PUT seguente:

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Dove:

    | Nome            | In   | Obbligatoria | Tipo   | Descrizione                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | ID sottoscrizione | path | True     | string | ID sottoscrizione della risorsa Kubernetes abilitato per Azure Arc |
    | Gruppo di risorse  | path | True     | string | Nome del gruppo di risorse che contiene la risorsa Kubernetes abilitata per Azure Arc |
    | Cluster Name    | path | True     | string | Nome della risorsa Kubernetes abilitata per Azure Arc  |


    Per **l'autenticazione**, l'intestazione deve avere un token di porta, come per le altre API di Azure. Per ottenere una bearer token, eseguire il comando seguente:

    ```az account get-access-token --subscription <your-subscription-id>``` Usare la struttura seguente per il corpo del messaggio:
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    La descrizione delle proprietà è riportata di seguito:

    | Proprietà | Descrizione | 
    | -------- | ----------- |
    | logAnalytics. workspaceId | ID area di lavoro della risorsa Log Analytics |
    | logAnalytics. Key         | Chiave della risorsa Log Analytics | 
    | auditLogPath             | **Facoltativo**. Percorso completo dei file di log di controllo. Il valore predefinito è ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>Verificare la distribuzione

Per verificare che nel cluster sia installata l'estensione Azure Defender, attenersi alla procedura descritta in una delle schede seguenti:

### <a name="azure-portal---security-center"></a>[**Portale di Azure-Centro sicurezza**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Usare la raccomandazione del Centro sicurezza per verificare lo stato dell'estensione

1. Dalla pagina raccomandazioni del Centro sicurezza di Azure aprire il controllo di sicurezza  **Abilita Azure Defender** .

1. Selezionare la raccomandazione denominata **Azure Arc abilitata per i cluster Kubernetes in cui è installata l'estensione di Azure Defender**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Raccomandazione del Centro sicurezza di Azure per la distribuzione dell'estensione Azure Defender per i cluster Kubernetes abilitati per Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Verificare che il cluster in cui è stata distribuita l'estensione sia elencato come **integro**.


### <a name="azure-portal---azure-arc"></a>[**Portale di Azure-Azure Arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Usare le pagine di Azure Arc per verificare lo stato dell'estensione

1. Dal portale di Azure aprire **Azure Arc**.
1. Dall'elenco infrastruttura selezionare **cluster Kubernetes** e quindi selezionare il cluster specifico.
1. Aprire la pagina estensioni. Le estensioni del cluster sono elencate. Controllare la colonna **stato installazione** per verificare che l'estensione Azure Defender sia stata installata correttamente.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Pagina di Azure Arc per verificare lo stato di tutte le estensioni installate in un cluster Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Per ulteriori informazioni, selezionare l'estensione.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Dettagli completi di un'estensione di Azure Arc in un cluster Kubernetes.":::


### <a name="azure-cli"></a>[**Interfaccia della riga di comando di Azure**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Usare l'interfaccia della riga di comando di Azure per verificare che l'estensione sia distribuita

1. Eseguire il comando seguente nell'interfaccia della riga di comando di Azure:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. Nella risposta cercare "extensionType": "Microsoft. azuredefender. kubernetes" e "installState": "installato".

    > [!NOTE]
    > Potrebbe essere visualizzato "installState": "in sospeso" per i primi minuti.
    
1. Se lo stato indica **installato**, eseguire il comando seguente nel computer con il `kubeconfig` file a cui si fa riferimento nel cluster per verificare che un pod denominato "azuredefender-xxxxx" si trovi nello stato "Running":
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**API REST**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>Usare l'API REST per verificare che l'estensione sia distribuita

Per confermare la corretta distribuzione o per convalidare lo stato dell'estensione in qualsiasi momento:

1. Eseguire il comando GET seguente:

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. Nella risposta, cercare "extensionType": "Microsoft. azuredefender. kubernetes" per "installState": "installato".

    > [!TIP]
    > Potrebbe essere visualizzato "installState": "in sospeso" per i primi minuti.
    
1. Se lo stato indica **installato**, eseguire il comando seguente nel computer con il `kubeconfig` file a cui si fa riferimento nel cluster per verificare che un pod denominato "azuredefender-xxxxx" si trovi nello stato "Running":
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Simulare gli avvisi di sicurezza da Azure Defender per Kubernetes

Un elenco completo degli avvisi supportati è disponibile nella [tabella di riferimento di tutti gli avvisi di sicurezza nel centro sicurezza di Azure](alerts-reference.md#alerts-akscluster).

1. Per simulare un avviso di Azure Defender, eseguire il comando seguente:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    La risposta prevista è "non è stata trovata alcuna risorsa".

    Entro 30 minuti, Azure Defender rileverà questa attività e attiverà un avviso di sicurezza.

1. Nella portale di Azure aprire la pagina degli avvisi di sicurezza del Centro sicurezza di Azure e cercare l'avviso sulla risorsa pertinente:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Avviso di esempio di Azure Defender per Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Rimozione dell'estensione di Azure Defender

È possibile rimuovere l'estensione usando portale di Azure, l'interfaccia della riga di comando di Azure o l'API REST, come illustrato nelle schede seguenti.

### <a name="azure-portal---arc"></a>[**Portale di Azure-Arc**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Usare portale di Azure per rimuovere l'estensione

1. Dal portale di Azure aprire Azure Arc.
1. Dall'elenco infrastruttura selezionare **cluster Kubernetes** e quindi selezionare il cluster specifico.
1. Aprire la pagina estensioni. Le estensioni del cluster sono elencate.
1. Selezionare il cluster e scegliere **Disinstalla**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Rimozione di un'estensione dal cluster Kubernetes abilitato per l'arco." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Interfaccia della riga di comando di Azure**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Usare l'interfaccia della riga di comando di Azure per rimuovere l'estensione di Azure Defender

1. Rimuovere l'estensione Azure Defender per Kubernetes Arc con i comandi seguenti:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    La rimozione dell'estensione potrebbe richiedere alcuni minuti. Si consiglia di attendere prima di provare a verificare l'esito positivo.

1. Per verificare che l'estensione sia stata rimossa correttamente, eseguire i comandi seguenti:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    Non è necessario che la risorsa di estensione venga eliminata dal Azure Resource Manager. Successivamente, verificare che non siano presenti Pod denominati "azuredefender-XXXXX" nel cluster eseguendo il comando seguente con il `kubeconfig` file a cui si fa riferimento nel cluster: 

    ```console
    kubectl get pods -n azuredefender
    ```

    Potrebbero essere necessari alcuni minuti prima che i pod vengano eliminati.

### <a name="rest-api"></a>[**API REST**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>Usare l'API REST per rimuovere l'estensione di Azure Defender 

Per rimuovere l'estensione usando l'API REST, eseguire il comando DELETE seguente:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Nome            | In   | Obbligatoria | Tipo   | Descrizione                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| ID sottoscrizione | path | True     | string | ID sottoscrizione del cluster Kubernetes abilitato per Arc |
| Gruppo di risorse  | path | True     | string | Il gruppo di risorse del cluster Kubernetes abilitato per Arc  |
| Cluster Name    | path | True     | string | Nome del cluster Kubernetes abilitato per l'arco            |

Per **l'autenticazione**, l'intestazione deve avere un token di porta, come per le altre API di Azure. Per ottenere una bearer token, eseguire il comando seguente:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

Il completamento della richiesta può richiedere alcuni minuti.

---

## <a name="next-steps"></a>Passaggi successivi

Questa pagina ha illustrato come distribuire l'estensione Azure Defender per i cluster Kubernetes abilitati per Azure Arc. Altre informazioni sulle funzionalità di sicurezza del contenitore di Azure Defender e del Centro sicurezza di Azure sono disponibili nelle pagine seguenti:

- [Sicurezza dei contenitori nel Centro sicurezza](container-security.md)
- [Introduzione ad Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)
- [Proteggere i carichi di lavoro Kubernetes](kubernetes-workload-protections.md)
