---
title: Raccogliere & analizzare i log delle risorse
description: Informazioni su come inviare i log delle risorse e i dati degli eventi dai gruppi di contenitori Istanze di Azure Container a Monitoraggio di Azure log
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: e46a1df65a4cfe5d10a58704aff485aa2834b55f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763920"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Registrazione di gruppi di contenitori e istanze con Monitoraggio di Azure log

Le aree di lavoro Log Analytics offrono una posizione centralizzata per l'archiviazione e l'esecuzione di query sui dati di log non solo dalle risorse di Azure, ma anche da risorse e risorse locali in altri cloud. Il servizio Istanze di Azure Container supporta per impostazione predefinita l'invio di log e dati sugli eventi ai log di Monitoraggio di Azure.

Per inviare i dati del log e degli eventi del gruppo di contenitori Monitoraggio di Azure log, specificare un ID area di lavoro Log Analytics esistente e una chiave dell'area di lavoro durante la configurazione di un gruppo di contenitori. 

Le sezioni seguenti descrivono come creare un gruppo di contenitori abilitato per la registrazione e come eseguire query nei log. È anche possibile aggiornare un [gruppo di contenitori con un](container-instances-update.md) ID dell'area di lavoro e una chiave dell'area di lavoro per abilitare la registrazione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Attualmente, è possibile inviare i dati degli eventi solo da istanze di contenitori Linux a Log Analytics.

## <a name="prerequisites"></a>Prerequisiti

Per abilitare la registrazione nelle istanze di contenitore, è necessario quanto segue:

* [area di lavoro Log Analytics](../azure-monitor/logs/quick-create-workspace.md)
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (o [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Ottenere le credenziali di Log Analytics

Il servizio Istanze di Azure Container deve avere l'autorizzazione per l'invio dei dati all'area di lavoro Log Analytics. Per concedere questa autorizzazione e abilitare la registrazione, è necessario fornire l'ID dell'area di lavoro Log Analytics e una delle relative chiavi (primaria o secondaria) quando si crea il gruppo di contenitori.

Per ottenere l'ID e la chiave primaria dell'area di lavoro Log Analytics:

1. Passare all'area di lavoro Log Analytics nel portale di Azure
1. In **Impostazioni** selezionare **Gestione agenti**
1. Prendere nota di:
   * **ID area di lavoro**
   * **Chiave primaria**

## <a name="create-container-group"></a>Creare un gruppo di contenitori

Dopo aver ottenuto l'ID e la chiave primaria dell'area di lavoro Log Analytics, è possibile procedere con la creazione di un gruppo di contenitori abilitato per la registrazione.

Gli esempi seguenti illustrano due modi per creare un gruppo di contenitori costituito da un singolo contenitore [fluente:][fluentd] l'interfaccia della riga di comando di Azure e l'interfaccia della riga di comando di Azure con un modello YAML. Il contenitore Fluentd produce diverse righe di output nella configurazione predefinita. Dato che questo output viene inviato all'area di lavoro Log Analytics, è adatto per dimostrare le funzionalità di visualizzazione ed esecuzione di query per i log.

### <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per eseguire la distribuzione con l'interfaccia della riga di comando di Azure, specificare i parametri `--log-analytics-workspace` e `--log-analytics-workspace-key` nel comando [az container create][az-container-create]. Sostituire i due valori dell'area di lavoro con i valori ottenuti nel passaggio precedente (e aggiornare il nome del gruppo di risorse) prima di eseguire il comando seguente.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Distribuire con YAML

Usare questo metodo se si preferisce distribuire gruppi di contenitori con YAML. Il codice YAML seguente definisce un gruppo di contenitori con un singolo contenitore. Copiare il codice YAML in un nuovo file e quindi sostituire `LOG_ANALYTICS_WORKSPACE_ID` e `LOG_ANALYTICS_WORKSPACE_KEY` con i valori ottenuti nel passaggio precedente. Salvare il file come **deploy-aci.yaml**.

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Eseguire quindi il comando seguente per distribuire il gruppo di contenitori. Sostituire `myResourceGroup` con un gruppo di risorse nella sottoscrizione oppure creare prima un gruppo di risorse denominato "myResourceGroup":

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Si riceverà una risposta da Azure contenente i dettagli di distribuzione poco dopo aver eseguito il comando.

## <a name="view-logs"></a>Visualizzare i log

Dopo aver distribuito il gruppo di contenitori, possono essere necessari diversi minuti (fino a 10) per la visualizzazione delle prime voci di log nel portale di Azure. 

Per visualizzare i log del gruppo di contenitori nella tabella `ContainerInstanceLog_CL`:

1. Passare all'area di lavoro Log Analytics nel portale di Azure
1. In **Generale** selezionare **Log**  
1. Digitare la query seguente: `ContainerInstanceLog_CL | limit 50`
1. Selezionare **Esegui**

Dovrebbero essere visibili i vari risultati visualizzati dalla query. Se all'inizio non vengono visualizzati risultati, attendere alcuni minuti, quindi selezionare il **pulsante** Esegui per eseguire di nuovo la query. Per impostazione predefinita, le voci del log vengono visualizzate in formato **Tabella**. È quindi possibile espandere una riga per visualizzare il contenuto di una singola voce del log.

![Risultati di Ricerca log nel portale di Azure][log-search-01]

## <a name="view-events"></a>Visualizzare eventi

È anche possibile visualizzare gli eventi per le istanze di contenitori nel portale di Azure. Gli eventi includono la data e l'ora di creazione e di avvio dell'istanza. Per visualizzare i dati degli eventi nella tabella `ContainerEvent_CL`:

1. Passare all'area di lavoro Log Analytics nel portale di Azure
1. In **Generale** selezionare **Log**  
1. Digitare la query seguente: `ContainerEvent_CL | limit 50`
1. Selezionare **Esegui**

Dovrebbero essere visibili i vari risultati visualizzati dalla query. Se all'inizio non vengono visualizzati risultati, attendere alcuni  minuti, quindi selezionare il pulsante Esegui per eseguire di nuovo la query. Per impostazione predefinita, le voci vengono visualizzate in formato **tabella**. È quindi possibile espandere una riga per visualizzare il contenuto di una singola voce.

![Risultati della ricerca di eventi nel portale di Azure][log-search-02]

## <a name="query-container-logs"></a>Eseguire query sui log dei contenitori

I log di Monitoraggio di Azure includono un [linguaggio di query][query_lang] completo per estrarre informazioni potenzialmente da migliaia di righe di output del log.

La struttura di base di una query è costituita da una tabella di origine (in questo articolo `ContainerInstanceLog_CL` o `ContainerEvent_CL`) seguita da una serie di operatori separati dal carattere barra verticale (`|`). È possibile concatenare più operatori per limitare i risultati ed eseguire funzioni avanzate.

Per visualizzare risultati di query di esempio, incollare la query seguente nella casella di testo della query e selezionare il **pulsante** Esegui per eseguire la query. Questa query consente di visualizzare tutte le voci di log il cui campo "Message" contiene la parola "warn":

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Sono supportate anche query più complesse. Ad esempio, questa query visualizza solo le voci di log per il gruppo di contenitori "mycontainergroup001" generate nell'ultima ora:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

Per altre informazioni sull'esecuzione di query sui log e la configurazione di avvisi nei log di Monitoraggio di Azure, vedere:

* [Informazioni sulle ricerche nei log Monitoraggio di Azure log](../azure-monitor/logs/log-query-overview.md)
* [Avvisi unificati in Monitoraggio di Azure](../azure-monitor/alerts/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Monitorare CPU e memoria del contenitore

Per informazioni sul monitoraggio delle risorse di CPU e memoria per l'istanza di contenitore, vedere:

* [Monitorare le risorse dei contenitori in Istanze di Azure Container](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create