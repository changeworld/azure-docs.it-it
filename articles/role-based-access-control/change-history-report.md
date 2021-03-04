---
title: Visualizzare i log attività per le modifiche RBAC di Azure
description: Visualizzare i log attività per le modifiche del controllo degli accessi in base al ruolo di Azure (RBAC di Azure) negli ultimi 90 giorni.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042130"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Visualizzare i log attività per le modifiche RBAC di Azure

A volte sono necessarie informazioni sulle modifiche del controllo degli accessi in base al ruolo di Azure (RBAC di Azure), ad esempio per il controllo o la risoluzione dei problemi. Ogni volta che un utente apporta modifiche alle assegnazioni di ruolo o alle definizioni dei ruoli all'interno delle sottoscrizioni, le modifiche vengono registrate nel [log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md). È possibile visualizzare i log attività per visualizzare tutte le modifiche apportate a RBAC di Azure negli ultimi 90 giorni.

## <a name="operations-that-are-logged"></a>Operazioni registrate

Di seguito sono riportate le operazioni correlate a RBAC di Azure registrate nel log attività:

- Crea assegnazione ruolo
- Elimina assegnazione ruolo
- Crea o aggiorna la definizione del ruolo personalizzata
- Elimina la definizione del ruolo personalizzata

## <a name="azure-portal"></a>Portale di Azure

Il modo più semplice per iniziare è visualizzare i log attività con il portale di Azure. Lo screenshot seguente mostra un esempio di operazioni di assegnazione di ruolo nel log attività. Include anche un'opzione per scaricare i log come file CSV.

![Log attività tramite il portale - screenshot](./media/change-history-report/activity-log-portal.png)

Per ottenere ulteriori informazioni, fare clic su una voce per aprire il riquadro Riepilogo. Fare clic sulla scheda **JSON** per ottenere un log dettagliato.

![Log attività tramite il portale con riquadro di riepilogo aperto-screenshot](./media/change-history-report/activity-log-summary-portal.png)

Il log attività nel portale include diversi filtri. Ecco i filtri correlati a RBAC di Azure:

| Filtra | Valore |
| --------- | --------- |
| Categoria evento | <ul><li>Amministrativo</li></ul> |
| Operazione | <ul><li>Crea assegnazione ruolo</li><li>Elimina assegnazione ruolo</li><li>Crea o aggiorna la definizione del ruolo personalizzata</li><li>Elimina la definizione del ruolo personalizzata</li></ul> |

Per altre informazioni sui log attività, vedere [visualizzare i log attività per monitorare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).


## <a name="interpret-a-log-entry"></a>Interpretare una voce di log

L'output del log della scheda JSON, Azure PowerShell o l'interfaccia della riga di comando di Azure può includere una grande quantità di informazioni. Di seguito sono riportate alcune delle proprietà chiave da cercare quando si tenta di interpretare una voce di log. Per i modi per filtrare l'output del log usando Azure PowerShell o l'interfaccia della riga di comando di Azure, vedere le sezioni seguenti.

> [!div class="mx-tableFixed"]
> | Proprietà | Valori di esempio | Descrizione |
> | --- | --- | --- |
> | autorizzazione: azione | Microsoft.Authorization/roleAssignments/write | Crea assegnazione ruolo |
> |  | Microsoft.Authorization/roleAssignments/delete | Elimina assegnazione ruolo |
> |  | Microsoft.Authorization/roleDefinitions/write | Crea o aggiorna la definizione del ruolo |
> |  | Microsoft.Authorization/roleDefinitions/delete | Elimina definizione di ruolo |
> | autorizzazione: ambito | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | Ambito per l'azione |
> | caller | admin@example.com<br/>ObjectId | Chi ha avviato l'azione |
> | eventTimestamp | 2021-03-01T22:07:41.126243 Z | Ora in cui si è verificata l'azione |
> | stato: valore | Avviato<br/>Completato<br/>Non riuscito | Stato dell'azione |

## <a name="azure-powershell"></a>Azure PowerShell

Per visualizzare i log attività con Azure PowerShell, usare il comando [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog).

Questo comando elenca tutte le modifiche relative alle assegnazioni di ruolo in una sottoscrizione per gli ultimi 7 giorni:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Questo comando elenca tutte le modifiche relative alle definizioni di ruolo in un gruppo di risorse per gli ultimi 7 giorni:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Filtrare l'output del log

L'output del log può includere una grande quantità di informazioni. Questo comando elenca tutte le modifiche alle definizioni dei ruoli e delle definizioni dei ruoli in una sottoscrizione per gli ultimi sette giorni e filtra l'output:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

Di seguito viene illustrato un esempio dell'output del log filtrato durante la creazione di un'assegnazione di ruolo:

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Se si utilizza un'entità servizio per creare assegnazioni di ruolo, la proprietà chiamante sarà un ID oggetto entità servizio. È possibile usare [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) per ottenere informazioni sull'entità servizio.

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per visualizzare i log attività usando l'interfaccia della riga di comando di Azure, usare il comando [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list).

Questo comando elenca i log attività in un gruppo di risorse a partire dal 1 ° marzo, cercando in futuro sette giorni:

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

Questo comando elenca i log attività per il provider di risorse di autorizzazione a partire dal 1 ° marzo, cercando in futuro sette giorni:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Filtrare l'output del log

L'output del log può includere una grande quantità di informazioni. Questo comando elenca tutte le modifiche apportate ai ruoli e alle definizioni dei ruoli in una sottoscrizione, esaminando sette giorni e filtra l'output:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

Di seguito viene illustrato un esempio dell'output del log filtrato durante la creazione di un'assegnazione di ruolo:

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

[Log di monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md) è un altro strumento che è possibile usare per raccogliere e analizzare le modifiche RBAC di Azure per tutte le risorse di Azure. I log di monitoraggio di Azure offrono i vantaggi seguenti:

- Possibilità di scrivere query e logica complesse
- Integrazione con avvisi, Power BI e altri strumenti
- Salvataggio dei dati per periodi di conservazione più estesi
- Riferimenti incrociati con gli altri log, ad esempio relativi a sicurezza e macchine virtuali o personalizzati

Ecco i passaggi di base per iniziare:

1. [Creare un'area di lavoro log Analytics](../azure-monitor/logs/quick-create-workspace.md).

1. [Configurare la soluzione Analisi log attività](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) per la propria area di lavoro.

1. [Visualizzare i log attività](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). Un modo rapido per accedere alla pagina Panoramica della soluzione Analisi log attività consiste nel fare clic sull'opzione **logs** .

   ![Opzione log di monitoraggio di Azure nel portale](./media/change-history-report/azure-log-analytics-option.png)

1. Facoltativamente, usare il [log Analytics di monitoraggio di Azure](../azure-monitor/logs/log-analytics-tutorial.md) per eseguire una query e visualizzare i log. Per altre informazioni, vedere [Introduzione alle query di log in monitoraggio di Azure](../azure-monitor/logs/get-started-queries.md).

Ecco una query che restituisce le nuove assegnazioni di ruolo organizzate in base al provider di risorse di destinazione:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Ecco una query che restituisce le modifiche alle assegnazioni di ruolo visualizzate in un grafico:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Log attività tramite il portale Advanced Analytics - screenshot](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare i log attività per monitorare le azioni sulle risorse](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorare l'attività di sottoscrizione con il log attività di Azure](../azure-monitor/essentials/platform-logs-overview.md)