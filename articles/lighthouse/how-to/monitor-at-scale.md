---
title: Monitorare le risorse delegate su larga scala
description: Informazioni su come usare efficacemente i log di monitoraggio di Azure in modo scalabile nei tenant dei clienti gestiti.
ms.date: 01/07/2021
ms.topic: how-to
ms.openlocfilehash: 797a6159d310f85c35c7eb550f05ff152526b3e8
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131152"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorare le risorse delegate su larga scala

Come provider di servizi, è possibile che siano stati caricati più tenant del cliente nel [Faro di Azure](../overview.md). Azure Lighthouse consente ai provider di servizi di eseguire operazioni su larga scala tra più tenant contemporaneamente, rendendo più efficienti le attività di gestione.

Questo argomento illustra come usare i [log di monitoraggio di Azure](../../azure-monitor/platform/data-platform-logs.md) in modo scalabile tra i tenant dei clienti gestiti. Per quanto riguarda i provider di servizi e i clienti in questo argomento, queste indicazioni si applicano anche alle [aziende che usano Azure Lighthouse per gestire più tenant](../concepts/enterprise.md).

> [!NOTE]
> Assicurarsi che agli utenti nei tenant di gestione siano stati concessi i [ruoli necessari per la gestione delle aree di lavoro log Analytics](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) nelle sottoscrizioni dei clienti Delegate.

## <a name="create-log-analytics-workspaces"></a>Creare aree di lavoro Log Analytics

Per raccogliere i dati, è necessario creare Log Analytics aree di lavoro. Queste aree di lavoro Log Analytics sono ambienti univoci per i dati raccolti da monitoraggio di Azure. Ogni area di lavoro ha un proprio repository di dati e una propria configurazione, mentre le origini dati e le soluzioni sono configurate per l'archiviazione dei dati in un'area di lavoro specifica.

Si consiglia di creare queste aree di lavoro direttamente nei tenant del cliente. In questo modo i dati rimangono nei tenant anziché essere esportati nel proprio. Questo consente anche il monitoraggio centralizzato di risorse o servizi supportati da Log Analytics, garantendo una maggiore flessibilità sui tipi di dati monitorati.

> [!TIP]
> Qualsiasi account di automazione usato per accedere ai dati da un'area di lavoro di Log Analytics deve essere creato nello stesso tenant dell'area di lavoro.

È possibile creare un'area di lavoro Log Analytics usando il [portale di Azure](../../azure-monitor/learn/quick-create-workspace.md), usando l'interfaccia della riga di comando di [Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)oppure [Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md).

> [!IMPORTANT]
> Anche se tutte le aree di lavoro vengono create nel tenant del cliente, il provider di risorse Microsoft. Insights deve essere registrato anche in una sottoscrizione nel tenant di gestione.

## <a name="deploy-policies-that-log-data"></a>Distribuire i criteri che registrano i dati

Dopo aver creato le aree di lavoro di Log Analytics, è possibile distribuire i [criteri di Azure](../../governance/policy/index.yml) tra le gerarchie dei clienti in modo che i dati di diagnostica vengano inviati all'area di lavoro appropriata in ogni tenant. I criteri esatti distribuiti possono variare a seconda dei tipi di risorse che si desidera monitorare.

Per altre informazioni sulla creazione di criteri, vedere [esercitazione: creare e gestire criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md). Questo [strumento della community](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) fornisce uno script che consente di creare criteri per monitorare i tipi di risorse specifici scelti.

Una volta determinati i criteri da distribuire, è possibile [distribuirli nelle sottoscrizioni delegate su larga scala](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analizzare i dati raccolti

Dopo aver distribuito i criteri, i dati verranno registrati nelle aree di lavoro Log Analytics create in ogni tenant del cliente. Per ottenere informazioni approfondite su tutti i clienti gestiti, è possibile usare strumenti come le [cartelle di lavoro di monitoraggio di Azure](../../azure-monitor/platform/workbooks-overview.md) per raccogliere e analizzare le informazioni provenienti da più origini dati.

## <a name="view-alerts-across-customers"></a>Visualizza avvisi tra clienti

È possibile visualizzare gli [avvisi](../../azure-monitor/platform/alerts-overview.md) per le sottoscrizioni delegate nei tenant dei clienti gestiti dall'utente.

PER aggiornare automaticamente gli avvisi tra più clienti, usare una query di [Azure Resource Graph](../../governance/resource-graph/overview.md) per filtrare gli avvisi. È possibile aggiungere la query al dashboard e selezionare tutti i clienti e le sottoscrizioni appropriati.

Nella query di esempio seguente vengono visualizzati gli avvisi di gravità 0 e 1, con aggiornamento ogni 60 minuti.

```kusto
alertsmanagementresources
| where type == "microsoft.alertsmanagement/alerts"
| where properties.essentials.severity =~ "Sev0" or properties.essentials.severity =~ "Sev1"
| where properties.essentials.monitorCondition == "Fired"
| where properties.essentials.startDateTime > ago(60m)
| project StartTime=properties.essentials.startDateTime,name,Description=properties.essentials.description, Severity=properties.essentials.severity, subscriptionId
| sort by tostring(StartTime)
```

## <a name="next-steps"></a>Passaggi successivi

- Esplorare questa [cartella di lavoro di esempio creata da MVP](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks), che tiene traccia dei report di conformità delle patch eseguendo una [query sui log gestione aggiornamenti](../../automation/update-management/query-logs.md) in più aree di lavoro log Analytics. 
- Informazioni su [Monitoraggio di Azure](../../azure-monitor/index.yml).
- Informazioni sui [log di monitoraggio di Azure](../../azure-monitor/platform/data-platform-logs.md).
- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).