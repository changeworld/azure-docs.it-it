---
title: Abilitare Monitoraggio di Azure per una singola macchina virtuale o un set di scalabilità di macchine virtuali nel portale di Azure
description: Informazioni su come abilitare VM Insights in una singola macchina virtuale di Azure o in un set di scalabilità di macchine virtuali usando il portale di Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035586"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Abilitare Monitoraggio di Azure per una singola macchina virtuale o un set di scalabilità di macchine virtuali nel portale di Azure
Questo articolo descrive come abilitare VM Insights per una macchina virtuale o un set di scalabilità di macchine virtuali usando il portale di Azure. Questa procedura può essere utilizzata per gli elementi seguenti:

- Macchina virtuale di Azure
- Set di scalabilità di macchine virtuali di Azure
- Macchina virtuale ibrida connessa ad Azure Arc

## <a name="prerequisites"></a>Prerequisiti

- [Creare e configurare un'area di lavoro log Analytics](./vminsights-configure-workspace.md). In alternativa, è possibile creare una nuova area di lavoro durante questo processo.
- Vedere [sistemi operativi supportati](./vminsights-enable-overview.md#supported-operating-systems) per assicurarsi che il sistema operativo della macchina virtuale o del set di scalabilità di macchine virtuali che si sta abilitando sia supportato. 

## <a name="enable-vm-insights"></a>Abilitare VM Insights

Dal portale di Azure selezionare **macchine virtuali**, set di **scalabilità di macchine virtuali** o **Server-Azure Arc** e selezionare una risorsa nell'elenco. Nella sezione **monitoraggio** del menu selezionare **Insights** e quindi **Enable**. L'esempio seguente mostra una macchina virtuale di Azure, ma il menu è simile per il set di scalabilità di macchine virtuali di Azure o Azure Arc.

![Abilitare VM Insights per una macchina virtuale](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Se la macchina virtuale non è già connessa a un'area di lavoro Log Analytics, verrà chiesto di selezionarne una. Se in precedenza non è stata [creata un'area di lavoro](../logs/quick-create-workspace.md), è possibile selezionare un valore predefinito per il percorso in cui è distribuita la macchina virtuale o il set di scalabilità di macchine virtuali nella sottoscrizione. Questa area di lavoro verrà creata e configurata se non esiste già. Se si seleziona un'area di lavoro esistente, viene configurata per la VM Insights se non è già stata eseguita.

> [!NOTE]
> Se si seleziona un'area di lavoro non configurata in precedenza per VM Insights, l'Management Pack *VMInsights* verrà aggiunta all'area di lavoro. Questa operazione verrà applicata a qualsiasi agente già connesso all'area di lavoro, indipendentemente dal fatto che sia abilitato per la VM Insights. I dati sulle prestazioni verranno raccolti da queste macchine virtuali e archiviati nella tabella *InsightsMetrics* .

![Selezionare l'area di lavoro](media/vminsights-enable-portal/select-workspace.png)

Si riceveranno messaggi di stato durante l'esecuzione della configurazione.

>[!NOTE]
>Se si usa un modello di aggiornamento manuale per il set di scalabilità di macchine virtuali, aggiornare le istanze per completare la configurazione. È possibile avviare gli aggiornamenti dalla pagina **istanze** , nella sezione **Impostazioni** .

![Abilitare l'elaborazione della distribuzione del monitoraggio di VM Insights](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Passaggi successivi

* Vedere [usare il mapping di VM Insights](vminsights-maps.md) per visualizzare le dipendenze dell'applicazione individuate. 
* Vedere [visualizzare le prestazioni delle macchine virtuali di Azure](vminsights-performance.md) per identificare colli di bottiglia, utilizzo complessivo e prestazioni della VM.
