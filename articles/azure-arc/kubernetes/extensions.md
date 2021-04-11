---
title: Estensioni del cluster Kubernetes abilitate per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Distribuire e gestire il ciclo di vita delle estensioni in Azure Arc abilitato Kubernetes
ms.openlocfilehash: 63fb14818d148dcc579300fdb4c89d636b47fd05
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451155"
---
# <a name="kubernetes-cluster-extensions"></a>Estensioni del cluster Kubernetes

La funzionalità Kubernetes Extensions Abilita le operazioni seguenti nei cluster Kubernetes abilitati per Azure Arc:

* Distribuzione basata su Azure Resource Manager dell'estensione del cluster.
* Gestione del ciclo di vita dei grafici Helm di estensione.

Una panoramica concettuale di questa funzionalità è disponibile nell'articolo [estensioni cluster-Azure Arc Enabled Kubernetes](conceptual-extensions.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Installare o aggiornare l'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli) della riga di comando di Azure alla versione >= 2.16.0.
- `connectedk8s` (Version >= 1.1.0) e `k8s-extension` (version >= 0.2.0) estensioni dell'interfaccia della riga di comando di Azure. Installare le estensioni dell'interfaccia della riga di comando di Azure eseguendo i comandi seguenti:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    Se l' `connectedk8s` `k8s-extension` estensione e è già installata, è possibile aggiornarla alla versione più recente usando il comando seguente:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Un cluster con connessione Kubernetes abilitato per Azure ARC esistente.
    - Se non è ancora stato connesso un cluster, usare la [Guida introduttiva](quickstart-connect-cluster.md).
    - [Aggiornare gli agenti](agent-upgrade.md#manually-upgrade-agents) alla versione >= 1.1.0.

## <a name="currently-available-extensions"></a>Estensioni attualmente disponibili

| Estensione | Descrizione |
| --------- | ----------- |
| [Monitoraggio di Azure](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Offre visibilità sulle prestazioni dei carichi di lavoro distribuiti nel cluster Kubernetes. Raccoglie le metriche di utilizzo della memoria e della CPU da controller, nodi e contenitori. |
| [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Raccoglie i dati dei log di controllo dai nodi del piano di controllo del cluster Kubernetes. Fornisce indicazioni e avvisi di minaccia basati sui dati raccolti. |

## <a name="usage-of-cluster-extensions"></a>Utilizzo delle estensioni del cluster

### <a name="create-extensions-instance"></a>Crea istanza estensioni

Creare una nuova istanza di estensione con `k8s-extension create` , passando i valori per i parametri obbligatori. Il comando seguente crea un'istanza di estensione di monitoraggio di Azure per contenitori nel cluster Kubernetes abilitato per Azure Arc:

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Output:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * Il servizio non è in grado di mantenere le informazioni riservate per più di 48 ore. Se gli agenti Kubernetes abilitati per Azure Arc non hanno connettività di rete per più di 48 ore e non possono determinare se creare un'estensione nel cluster, l'estensione passa allo `Failed` stato. Una volta in `Failed` stato, sarà necessario eseguire `k8s-extension create` di nuovo per creare una nuova risorsa di Azure di estensione.
> * * Il monitoraggio di Azure per i contenitori è un'estensione Singleton (solo un requisito per ogni cluster). È necessario eseguire la pulizia di tutte le installazioni precedenti di monitoraggio di Azure per i contenitori (senza estensioni) prima di installare le stesse estensioni via. Seguire le istruzioni per [eliminare il grafico Helm prima di `az k8s-extension create` eseguire ](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid).

**Parametri obbligatori**

| Nome parametro | Descrizione |
|----------------|------------|
| `--name` | Nome dell'istanza di estensione |
| `--extension-type` | Il tipo di estensione che si vuole installare nel cluster. Ad esempio: Microsoft. AzureMonitor. Containers, Microsoft. azuredefender. kubernetes | 
| `--scope` | Ambito di installazione per l'estensione `cluster` o `namespace` |
| `--cluster-name` | Nome della risorsa Kubernetes abilitata per Azure Arc in cui deve essere creata l'istanza di estensione |
| `--resource-group` | Il gruppo di risorse che contiene la risorsa Kubernetes abilitata per Azure Arc |
| `--cluster-type` | Tipo di cluster in cui deve essere creata l'istanza di estensione. Solo corrente `connectedClusters` , che corrisponde ad Azure Arc Enabled Kubernetes, è un valore accettato |

**Parametri facoltativi**

| Nome parametro | Descrizione |
|--------------|------------|
| `--auto-upgrade-minor-version` | Proprietà booleana che specifica se la versione secondaria dell'estensione verrà aggiornata automaticamente o meno. Impostazione predefinita: `true`.  Se questo parametro è impostato su true, non è possibile impostare `version` il parametro, perché la versione verrà aggiornata dinamicamente. Se impostato su `false` , l'estensione non verrà aggiornata automaticamente anche per le versioni delle patch. |
| `--version` | Versione dell'estensione da installare (versione specifica a cui aggiungere l'istanza di estensione). Non deve essere specificato se aggiornamento automatico-versione secondaria è impostato su `true` . |
| `--configuration-settings` | Impostazioni che possono essere passate nell'estensione per controllarne la funzionalità. Devono essere passati come coppie separate da spazi `key=value` dopo il nome del parametro. Se questo parametro viene usato nel comando, `--configuration-settings-file` non può essere usato nello stesso comando. |
| `--configuration-settings-file` | Percorso del file JSON con coppie chiave-valore da usare per passare le impostazioni di configurazione all'estensione. Se questo parametro viene usato nel comando, `--configuration-settings` non può essere usato nello stesso comando. |
| `--configuration-protected-settings` | Queste impostazioni non possono essere recuperate mediante `GET` chiamate API o `az k8s-extension show` comandi e vengono quindi utilizzate per passare le impostazioni riservate. Devono essere passati come coppie separate da spazi `key=value` dopo il nome del parametro. Se questo parametro viene usato nel comando, `--configuration-protected-settings-file` non può essere usato nello stesso comando. |
| `--configuration-protected-settings-file` | Percorso del file JSON con coppie chiave-valore da usare per passare le impostazioni riservate all'estensione. Se questo parametro viene usato nel comando, `--configuration-protected-settings` non può essere usato nello stesso comando. |
| `--release-namespace` | Questo parametro indica lo spazio dei nomi in cui deve essere creata la versione. Questo parametro è pertinente solo se il `scope` parametro è impostato su `cluster` . |
| `--release-train` |  Gli autori delle estensioni possono pubblicare versioni in diversi treni di rilascio, ad esempio `Stable` , `Preview` e così via. Se questo parametro non viene impostato in modo esplicito, `Stable` viene utilizzato come valore predefinito. Questo parametro non può essere utilizzato quando il `autoUpgradeMinorVersion` parametro è impostato su `false` . |
| `--target-namespace` | Questo parametro indica lo spazio dei nomi in cui verrà creata la versione. L'autorizzazione dell'account di sistema creato per questa istanza di estensione sarà limitata a questo spazio dei nomi. Questo parametro è pertinente solo se il `scope` parametro è impostato su `namespace` . |

### <a name="show-details-of-an-extension-instance"></a>Mostra i dettagli di un'istanza di estensione

Visualizzare i dettagli di un'istanza di estensione attualmente installata con `k8s-extension show` , passando i valori per i parametri obbligatori:

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Output:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Elencare tutte le estensioni installate nel cluster

Elencare tutte le estensioni installate in un cluster con `k8s-extension list` , passando i valori per i parametri obbligatori.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Output:**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="update-an-existing-extension-instance"></a>Aggiornare un'istanza di estensione esistente

Aggiornare un'istanza di estensione in un cluster con `k8s-extension update` , passando i valori da aggiornare.  Questo comando Aggiorna solo le `auto-upgrade-minor-version` `release-train` proprietà, e `version` . Ad esempio:

- **Aggiorna il training della versione:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --release-train Preview
    ```

- **Disabilitare l'aggiornamento automatico e aggiungere un'istanza di estensione a una versione specifica:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version false --version 2.2.2
    ```

- **Attivare l'aggiornamento automatico per l'istanza di estensione:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version true
    ```

> [!NOTE]
> Il `version` parametro può essere impostato solo quando `--auto-upgrade-minor-version` è impostato su `false` .

### <a name="delete-extension-instance"></a>Elimina istanza di estensione

Eliminare un'istanza di estensione in un cluster con `k8s-extension delete` , passando i valori per i parametri obbligatori.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> La risorsa di Azure che rappresenta questa estensione viene eliminata immediatamente. La versione Helm nel cluster associato a questa estensione viene eliminata solo quando gli agenti in esecuzione nel cluster Kubernetes hanno la connettività di rete e possono raggiungere nuovamente i servizi di Azure per recuperare lo stato desiderato.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle estensioni cluster attualmente disponibili per Azure Arc Enabled Kubernetes:
> [!div class="nextstepaction"]
> [Monitoraggio](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  di Azure [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)