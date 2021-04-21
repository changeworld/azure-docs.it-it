---
title: Configurare cluster Kubernetes ibridi con informazioni dettagliate sui contenitori | Microsoft Docs
description: Questo articolo descrive come configurare Informazioni dettagliate sui contenitori per monitorare i cluster Kubernetes ospitati in Azure Stack o in un altro ambiente.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 90a4c14397df8e70fc8f3d88bc339f826bb1ccc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767024"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Configurare cluster Kubernetes ibridi con Informazioni dettagliate sui contenitori

Informazioni dettagliate sui contenitori offre un'esperienza di monitoraggio avanzato per il servizio Azure Kubernetes e il motore del servizio Azure Kubernetes [in Azure,](https://github.com/Azure/aks-engine)un cluster Kubernetes auto-gestito ospitato in Azure. Questo articolo descrive come abilitare il monitoraggio dei cluster Kubernetes ospitati all'esterno di Azure e ottenere un'esperienza di monitoraggio simile.

## <a name="supported-configurations"></a>Configurazioni supportate

Le configurazioni seguenti sono ufficialmente supportate con Informazioni dettagliate sui contenitori. Se si dispone di una versione diversa di Kubernetes e delle versioni del sistema operativo, inviare un messaggio di posta elettronica a askcoin@microsoft.com .

- Ambienti:

    - Kubernetes locale
    - Motore servizio AzureKs in Azure e Azure Stack. Per altre informazioni, vedere [AKS Engine on Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versione 4 e successive, in locale o in altri ambienti cloud.

- Le versioni di Kubernetes e i criteri di supporto sono gli stessi delle versioni del servizio Kubernetes [supportate.](../../aks/supported-kubernetes-versions.md)

- Sono supportati i runtime del contenitore seguenti: Docker, Moby e CRI, ad esempio CRI-O e ContainerD.

- La versione del sistema operativo Linux per i nodi master e di lavoro supportati sono: Ubuntu (18.04 LTS e 16.04 LTS) e Red Hat Enterprise Linux CoreOS 43.81.

- Controllo degli accessi supportato: controllo degli accessi in base al ruolo e non controllo degli accessi in base al ruolo di Kubernetes

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

- Un'[area di lavoro Log Analytics](../logs/design-logs-deployment.md).

    Informazioni dettagliate sui contenitori supporta un'area di lavoro Log Analytics nelle aree elencate in Prodotti di Azure [per area](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Per creare una propria area di lavoro, è possibile crearla tramite [Azure Resource Manager](../logs/resource-manager-workspace.md), [tramite PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)o nella portale di Azure [.](../logs/quick-create-workspace.md)

    >[!NOTE]
    >Non è supportato abilitare il monitoraggio di più cluster con lo stesso nome di cluster nella stessa area di lavoro Log Analytics. I nomi dei cluster devono essere univoci.
    >

- Si è membri del ruolo collaboratore **di Log Analytics per** abilitare il monitoraggio dei contenitori. Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire l'accesso all'area di lavoro e ai dati di log.](../logs/manage-access.md)

- Per visualizzare i dati di monitoraggio, è necessario avere il ruolo di lettore [*di Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro Log Analytics, configurato con Informazioni dettagliate sul contenitore.

- [Client HELM per](https://helm.sh/docs/using_helm/) eseguire l'onboarding del grafico informazioni dettagliate sul contenitore per il cluster Kubernetes specificato.

- Le informazioni di configurazione del proxy e del firewall seguenti sono necessarie per la versione in contenitori dell'agente di Log Analytics per Linux per comunicare con Monitoraggio di Azure:

    |Risorsa agente|Porte |
    |------|---------|
    |*.ods.opinsights.azure.com |Porta 443 |
    |*.oms.opinsights.azure.com |Porta 443 |
    |*.dc.services.visualstudio.com |Porta 443 |

- L'agente in contenitori richiede che Kubelet o sia aperto in tutti i nodi del cluster per `cAdvisor secure port: 10250` raccogliere le metriche delle `unsecure port :10255` prestazioni. Se non è già configurato, è consigliabile eseguire la configurazione in `secure port: 10250` cAdvisor di Kubelet.

- L'agente in contenitori richiede che nel contenitore siano specificate le variabili di ambiente seguenti per comunicare con il servizio API Kubernetes all'interno del cluster per raccogliere i dati di inventario `KUBERNETES_SERVICE_HOST` , e `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>La versione minima dell'agente supportata per il monitoraggio dei cluster Kubernetes ibridi è 10182019 o successiva.

## <a name="enable-monitoring"></a>Abilitare il monitoraggio

L'abilitazione di Informazioni dettagliate sul contenitore per il cluster Kubernetes ibrido consiste nell'eseguire i passaggi seguenti nell'ordine indicato.

1. Configurare l'area di lavoro Log Analytics con la soluzione Informazioni dettagliate sul contenitore.   

2. Abilitare il grafico HELM di Informazioni dettagliate sul contenitore con l'area di lavoro Log Analytics.

Per altre informazioni sulle soluzioni di monitoraggio in Monitoraggio di Azure vedere [qui](../../azure-monitor/insights/solutions.md).

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Come aggiungere la soluzione Monitoraggio di Azure contenitori

È possibile distribuire la soluzione con il modello di Azure Resource Manager fornito usando il cmdlet Azure PowerShell o con l'interfaccia della `New-AzResourceGroupDeployment` riga di comando di Azure.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

- **workspaceResourceId: ID** risorsa completo dell'area di lavoro Log Analytics.
- **workspaceRegion:** area in cui viene creata l'area  di lavoro, definita anche Località nelle proprietà dell'area di lavoro quando viene visualizzata dal portale di Azure.

Per identificare innanzitutto l'ID risorsa completo dell'area di lavoro Log Analytics necessaria per il valore del parametro nel filecontainerSolutionParams.js, seguire questa procedura e quindi eseguire il cmdlet di PowerShell o il comando dell'interfaccia della riga di comando di Azure per aggiungere la `workspaceResourceId` soluzione. 

1. Elencare tutte le sottoscrizioni a cui si ha accesso usando il comando seguente:

    ```azurecli
    az account list --all -o table
    ```

    L'output sarà simile al seguente:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Copiare il valore per **SubscriptionId**.

2. Passare alla sottoscrizione che ospita l'area di lavoro Log Analytics usando il comando seguente:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Nell'esempio seguente viene visualizzato l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Nell'output trovare il nome dell'area di lavoro e quindi copiare l'ID risorsa completo dell'area di lavoro Log Analytics nel **campo ID**.

4. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Salvare questo file come containerSolution.jsin una cartella locale.

6. Incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Modificare i valori per **workspaceResourceId** usando il valore copiato nel passaggio 3 e per **workspaceRegion** copiare il valore **Region** dopo aver eseguito il comando dell'interfaccia della riga di comando di Azure [az monitor log-analytics workspace show](/cli/azure/monitor/log-analytics/workspace#az_monitor-log-analytics-workspace-list&preserve-view=true).

8. Salvare il file come containerSolutionParams.jsin una cartella locale.

9. A questo punto è possibile distribuire il modello.

   - Per distribuire con Azure PowerShell, usare i comandi seguenti nella cartella che contiene il modello:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Per eseguire la distribuzione con l'interfaccia della riga di comando di Azure, eseguire i comandi seguenti:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

## <a name="install-the-helm-chart"></a>Installare il grafico HELM

In questa sezione viene installato l'agente in contenitori per Informazioni dettagliate sui contenitori. Prima di procedere, è necessario identificare l'ID dell'area di lavoro necessario per il parametro e `omsagent.secret.wsid` la chiave primaria necessaria per il parametro `omsagent.secret.key` . È possibile identificare queste informazioni eseguendo i passaggi seguenti e quindi eseguire i comandi per installare l'agente usando il grafico HELM.

1. Eseguire il comando seguente per identificare l'ID dell'area di lavoro:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    Nell'output trovare il nome dell'area di lavoro sotto il nome del campo **e** quindi copiare l'ID dell'area di lavoro Log Analytics nel campo **customerID**.

2. Eseguire il comando seguente per identificare la chiave primaria per l'area di lavoro:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Nell'output trovare la chiave primaria nel campo **primarySharedKey** e quindi copiare il valore.

>[!NOTE]
>I comandi seguenti sono applicabili solo per Helm versione 2. L'uso `--name` del parametro non è applicabile con Helm versione 3. 

>[!NOTE]
>Se il cluster Kubernetes comunica tramite un server proxy, configurare il parametro `omsagent.proxy` con l'URL del server proxy. Se il cluster non comunica tramite un server proxy, non è necessario specificare questo parametro. Per altre informazioni, vedere Configurare [l'endpoint proxy](#configure-proxy-endpoint) più avanti in questo articolo.

3. Aggiungere il repository di grafici di Azure all'elenco locale eseguendo il comando seguente:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Installare il grafico eseguendo il comando seguente:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Se l'area di lavoro Log Analytics Azure China (21Vianet), eseguire il comando seguente:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Se l'area di lavoro Log Analytics è in Azure US Gov, eseguire questo comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Abilitare il grafico Helm usando il modello API

È possibile specificare un componente aggiuntivo nel file JSON della specifica del cluster del motore del servizio Web Diaks, noto anche come modello API. In questo componente aggiuntivo specificare la versione con codifica Base64 di e dell'area di lavoro Log Analytics in cui sono archiviati i dati `WorkspaceGUID` `WorkspaceKey` di monitoraggio raccolti. È possibile trovare `WorkspaceGUID` e usando i passaggi `WorkspaceKey` 1 e 2 nella sezione precedente.

Le definizioni API supportate per hub di Azure Stack cluster sono disponibili in questo esempio, [kubernetes-container-monitoring_existing_workspace_id_and_key.jsin](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). In particolare, trovare **la proprietà addons** in **kubernetesConfig:**

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Configurare la raccolta dati dell'agente

A partire dalla versione 1.0.0 del grafico, le impostazioni di raccolta dei dati dell'agente vengono controllate da ConfigMap. Fare riferimento alla documentazione sulle impostazioni di raccolta dati [dell'agente qui](container-insights-agent-config.md).

Dopo aver distribuito correttamente il grafico, è possibile esaminare i dati per il cluster Kubernetes ibrido in Informazioni dettagliate sul contenitore portale di Azure.  

>[!NOTE]
>La latenza di inserimento è di circa 5-10 minuti dall'agente per il commit nell'area di lavoro Azure Log Analytics. Lo stato del cluster mostra il valore Nessun **dato** **o** Sconosciuto fino a quando tutti i dati di monitoraggio necessari non sono disponibili in Monitoraggio di Azure.

## <a name="configure-proxy-endpoint"></a>Configurare l'endpoint proxy

A partire dalla versione 2.7.1 del grafico, chart supporterà la specifica dell'endpoint proxy con il `omsagent.proxy` parametro chart. Ciò consente di comunicare tramite il server proxy. La comunicazione tra l'agente di Informazioni dettagliate contenitore e Monitoraggio di Azure può essere un server proxy HTTP o HTTPS e sono supportate sia l'autenticazione anonima che l'autenticazione di base (nome utente/password).

Il valore di configurazione del proxy ha la sintassi seguente: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Se il server proxy non richiede l'autenticazione, è comunque necessario specificare un nome utente/password psuedo. Può trattarsi di qualsiasi nome utente o password.

|Proprietà| Descrizione |
|--------|-------------|
|Protocollo | http o https |
|utente | Nome utente facoltativo per l'autenticazione proxy |
|password | Password facoltativa per l'autenticazione proxy |
|proxyhost | Indirizzo o FQDN del server proxy |
|port | Numero di porta facoltativo per il server proxy |

ad esempio `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Se si specifica il protocollo **come http**, le richieste HTTP vengono create usando la connessione protetta SSL/TLS. Il server proxy deve supportare i protocolli SSL/TLS.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante il tentativo di abilitare il monitoraggio per il cluster Kubernetes ibrido, copiare lo script di PowerShell [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) e salvarlo in una cartella nel computer. Questo script viene fornito per rilevare e risolvere i problemi riscontrati. I problemi di cui è progettato per rilevare e tentare la correzione sono i seguenti:

- L'area di lavoro Log Analytics specificata è valida
- L'area di lavoro Log Analytics è configurata con la soluzione Informazioni dettagliate sui contenitori. In caso contrario, configurare l'area di lavoro.
- I pod del set di repliche OmsAgent sono in esecuzione
- I pod del daemonset OmsAgent sono in esecuzione
- Il servizio Integrità OmsAgent è in esecuzione
- L'ID e la chiave dell'area di lavoro Log Analytics configurati nell'agente in contenitori corrispondono all'area di lavoro con cui sono configurate le informazioni dettagliate.
- Verificare che tutti i nodi di lavoro Linux abbia `kubernetes.io/role=agent` un'etichetta per pianificare il pod rs. Se non esiste, aggiungerlo.
- Convalidare `cAdvisor secure port:10250` o viene aperto in tutti i nodi del `unsecure port: 10255` cluster.

Per eseguire con Azure PowerShell, usare i comandi seguenti nella cartella che contiene lo script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Passaggi successivi

Con il monitoraggio abilitato per raccogliere l'integrità e l'utilizzo delle risorse del cluster Kubernetes ibrido e dei carichi di lavoro in esecuzione su di essi, vedere come [usare Informazioni](container-insights-analyze.md) dettagliate sui contenitori.