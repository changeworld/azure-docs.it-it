---
title: Configurare Log Analytics area di lavoro per VM Insights
description: Viene descritto come creare e configurare l'area di lavoro Log Analytics usata da VM Insights.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: dc7e6c42837ccaa56c7a211deb646c934ec137a4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717129"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>Configurare Log Analytics area di lavoro per VM Insights
VM Insights raccoglie i dati da una o più aree di lavoro Log Analytics in monitoraggio di Azure. Prima di caricare gli agenti, è necessario creare e configurare un'area di lavoro. Questo articolo descrive i requisiti dell'area di lavoro e per configurarlo per la VM Insights.

## <a name="overview"></a>Panoramica
Una singola sottoscrizione può usare un numero qualsiasi di aree di lavoro in base alle esigenze. L'unico requisito dell'area di lavoro è che si trova in una posizione supportata e deve essere configurato con la soluzione *VMInsights* .

Una volta configurata l'area di lavoro, è possibile usare una qualsiasi delle opzioni disponibili per installare gli agenti necessari nella macchina virtuale e nel set di scalabilità di macchine virtuali e specificare un'area di lavoro per l'invio dei dati. VM Insights raccoglierà i dati da qualsiasi area di lavoro configurata nella propria sottoscrizione.

> [!NOTE]
> Quando si Abilita VM Insights in una singola macchina virtuale o in un set di scalabilità di macchine virtuali usando il portale di Azure, è possibile selezionare un'area di lavoro esistente o crearne una nuova. La soluzione *VMInsights* verrà installata in questa area di lavoro, se non è già presente. È quindi possibile usare questa area di lavoro per altri agenti.


## <a name="create-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics

>[!NOTE]
>Le informazioni descritte in questa sezione sono applicabili anche alla [soluzione mapping dei servizi](service-map.md). 

Accedere alle aree di lavoro Log Analytics nel portale di Azure dal menu **log Analytics aree di lavoro** .

[![Registra aree di lavoro anlitica](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

È possibile creare una nuova area di lavoro Log Analytics usando uno dei metodi seguenti. Vedere [progettazione della distribuzione dei log di monitoraggio di Azure](../logs/design-logs-deployment.md) per indicazioni su come determinare il numero di aree di lavoro da usare nel proprio ambiente e come progettare la strategia di accesso.


* [Azure portal](../logs/quick-create-workspace.md)
* [Interfaccia della riga di comando di Azure](../logs/quick-create-workspace-cli.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>Aree supportate
VM Insights supporta un'area di lavoro Log Analytics in tutte le [aree supportate da log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) ad eccezione di quanto segue:

- Germania centro-occidentale
- Corea centrale

>[!NOTE]
>È possibile monitorare le macchine virtuali di Azure in qualsiasi area. Le macchine virtuali non sono limitate alle aree supportate dall'area di lavoro Log Analytics.

## <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure
Per abilitare e accedere alle funzionalità in VM Insights, è necessario avere il [ruolo di collaboratore log Analytics](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro. Per visualizzare le prestazioni, l'integrità e la mappa dei dati, è necessario avere il [ruolo di lettore di monitoraggio](../roles-permissions-security.md#built-in-monitoring-roles) per la macchina virtuale di Azure. Per altre informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [Gestire le aree di lavoro](../logs/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Aggiungi soluzione VMInsights all'area di lavoro
Prima di poter usare un'area di lavoro Log Analytics con le informazioni dettagliate di VM, è necessario che sia installata la soluzione *VMInsights* . Le sezioni seguenti illustrano i metodi per la configurazione dell'area di lavoro.

> [!NOTE]
> Quando si aggiunge la soluzione *VMInsights* all'area di lavoro, tutte le macchine virtuali esistenti connesse all'area di lavoro inizieranno a inviare i dati a InsightsMetrics. I dati per gli altri tipi di dati non verranno raccolti fino a quando non si aggiungeranno le Dependency Agent alle macchine virtuali esistenti connesse all'area di lavoro.

### <a name="azure-portal"></a>Portale di Azure
Sono disponibili tre opzioni per la configurazione di un'area di lavoro esistente usando il portale di Azure. Ciascun elemento è descritto di seguito.

Per configurare una singola area di lavoro, scegliere l'opzione **macchine virtuali** dal menu **monitoraggio di Azure** , selezionare le **altre opzioni di onboarding** e quindi **configurare un'area di lavoro**. Selezionare una sottoscrizione e un'area di lavoro, quindi fare clic su **Configura**.

[![Configurare l'area di lavoro](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Per configurare più aree di lavoro, selezionare la scheda **configurazione area di lavoro** nel menu **macchine virtuali** del menu **monitoraggio** della portale di Azure. Impostare i valori del filtro per visualizzare un elenco delle aree di lavoro esistenti. Selezionare la casella accanto a ogni area di lavoro da abilitare, quindi fare clic su **Configura selezionato**.

[![Configurazione dell'area di lavoro](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Quando si Abilita VM Insights in una singola macchina virtuale o in un set di scalabilità di macchine virtuali usando il portale di Azure, è possibile selezionare un'area di lavoro esistente o crearne una nuova. La soluzione *VMInsights* verrà installata in questa area di lavoro, se non è già presente. È quindi possibile usare questa area di lavoro per altri agenti.

[![Abilitare una singola VM nel portale](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Modello di Resource Manager
I modelli di Azure Resource Manager per VM Insights sono disponibili in un file di archivio (con estensione zip) che è possibile [scaricare dal repository GitHub](https://aka.ms/VmInsightsARMTemplates). Questo include un modello denominato **ConfigureWorkspace** che configura un'area di lavoro di log Analytics per VM Insights. Questo modello viene distribuito usando uno dei metodi standard, inclusi i comandi PowerShell e CLI di esempio seguenti: 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Passaggi successivi
- Per connettere gli agenti a VM Insights, vedere caricare gli [agenti in VM Insights](vminsights-enable-overview.md) .
- Vedere [targeting Solutions di monitoraggio in monitoraggio di Azure (anteprima)](../insights/solution-targeting.md) per limitare la quantità di dati inviati da una soluzione all'area di lavoro.
