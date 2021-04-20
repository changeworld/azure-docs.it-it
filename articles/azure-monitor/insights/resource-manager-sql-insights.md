---
title: Resource Manager esempi di modelli per informazioni dettagliate su SQL
description: Esempi Azure Resource Manager modelli per distribuire e configurare informazioni dettagliate su SQL.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730531"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>Resource Manager esempi di modelli per informazioni dettagliate su SQL
Questo articolo include modelli Azure Resource Manager [di esempio](../../azure-resource-manager/templates/template-syntax.md) per abilitare informazioni dettagliate su SQL per il monitoraggio di SQL in esecuzione in Azure.  Per informazioni [dettagliate sull'offerta](sql-insights-overview.md) e sulle versioni di SQL supportate, vedere la documentazione di SQL Insights. Ogni esempio include un file modello e un file di parametri con valori di esempio da fornire al modello.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>Creare un profilo di monitoraggio di SQL Insights
L'esempio seguente crea un profilo di monitoraggio di SQL Insights, che include i dati di monitoraggio SQL da raccogliere, la frequenza di raccolta dei dati e specifica l'area di lavoro a cui verranno inviati i dati.


### <a name="template-file"></a>File modello

Visualizzare il [file modello nell'hub Git.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate)

### <a name="parameter-file"></a>File di parametri

Visualizzare il [file dei parametri nell'hub Git.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json)


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>Aggiungere una macchina virtuale di monitoraggio a un profilo di monitoraggio di SQL Insights
Dopo aver creato un profilo di monitoraggio, è necessario allocare le macchine virtuali di Azure che verranno configurate per raccogliere in remoto i dati dalle risorse SQL specificate nella configurazione per tale macchina virtuale.  Per altri dettagli, vedere la documentazione relativa all'abilitazione di SQL Insights.

L'esempio seguente configura una macchina virtuale di monitoraggio per raccogliere i dati dalle risorse SQL specificate.


### <a name="template-file"></a>File modello

Visualizzare il [file modello nell'hub Git.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate)

### <a name="parameter-file"></a>File di parametri

Visualizzare il [file dei parametri nell'hub Git.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json)


## <a name="create-an-alert-rule-for-sql-insights"></a>Creare una regola di avviso per informazioni dettagliate su SQL
L'esempio seguente crea una regola di avviso che copre le risorse SQL all'interno dell'ambito del profilo di monitoraggio specificato.  Questa regola di avviso verrà visualizzata nell'interfaccia utente di SQL Insights nel pannello del contesto dell'interfaccia utente degli avvisi.  

Il file dei parametri contiene valori di uno dei modelli di avviso forniti in Informazioni dettagliate su SQL. È possibile modificarlo per avvisare altri dati raccolti per SQL.  Il modello non specifica un gruppo di azioni per la regola di avviso.


#### <a name="template-file"></a>File modello

Visualizzare il [file modello nell'hub Git.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate)

### <a name="parameter-file"></a>File di parametri

Visualizzare il [file dei parametri nell'hub Git.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json)





## <a name="next-steps"></a>Passaggi successivi

* [Passare ad altri esempi per Monitoraggio di Azure](../resource-manager-samples.md).
* [Altre informazioni sulle informazioni dettagliate di SQL.](sql-insights-overview.md)
