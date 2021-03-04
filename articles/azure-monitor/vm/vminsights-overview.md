---
title: Informazioni su VM Insights
description: Panoramica di VM Insights, che monitora l'integrità e le prestazioni delle macchine virtuali di Azure e individua e associa automaticamente i componenti dell'applicazione e le relative dipendenze.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046415"
---
# <a name="overview-of-vm-insights"></a>Panoramica di VM Insights

VM Insights monitora le prestazioni e l'integrità delle macchine virtuali e dei set di scalabilità di macchine virtuali, inclusi i processi in esecuzione e le dipendenze da altre risorse. Consente di offrire prestazioni prevedibili e la disponibilità di applicazioni essenziali, identificando i colli di bottiglia delle prestazioni e i problemi di rete, nonché di comprendere se un problema è correlato ad altre dipendenze.

VM Insights supporta i sistemi operativi Windows e Linux nei computer seguenti:

- Macchine virtuali di Azure
- Set di scalabilità di macchine virtuali di Azure
- Macchine virtuali ibride connesse ad Azure Arc
- Macchine virtuali locali
- Macchine virtuali ospitate in un altro ambiente cloud
  

VM Insights archivia i dati nei log di monitoraggio di Azure, che consentono di distribuire potenti aggregazioni e filtri e analizzare le tendenze dei dati nel tempo. È possibile visualizzare i dati in una singola VM direttamente dalla macchina virtuale oppure è possibile usare monitoraggio di Azure per fornire una visualizzazione aggregata di più macchine virtuali.

![Prospettiva di informazioni dettagliate della macchina virtuale nel portale di Azure](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Prezzi
Non è previsto alcun costo diretto per la VM Insights, ma l'attività viene addebitata nell'area di lavoro Log Analytics. In base ai prezzi pubblicati nella [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/), la fatturazione di VM Insights è la seguente:

- Dati inseriti dagli agenti e archiviati nell'area di lavoro.
- Dati sullo stato di integrità raccolti dall'integrità Guest (anteprima)
- Regole di avviso basate sui dati di log e di integrità.
- Notifiche inviate dalle regole di avviso.

Le dimensioni del log variano in base alle lunghezze di stringa dei contatori delle prestazioni e possono aumentare con il numero di dischi logici e schede di rete allocate alla VM. Se si usa già Mapping dei servizi, l'unica modifica che si vedrà sono i dati aggiuntivi sulle prestazioni inviati al tipo di dati di monitoraggio di Azure `InsightsMetrics` .


## <a name="configuring-vm-insights"></a>Configurazione di VM Insights
I passaggi per la configurazione di VM Insights sono i seguenti. Per istruzioni dettagliate su ogni passaggio, seguire ogni collegamento:

- [Creare Log Analytics area di lavoro.](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Aggiungere la soluzione VMInsights all'area di lavoro.](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Installare gli agenti nella macchina virtuale e nel set di scalabilità di macchine virtuali da monitorare.](./vminsights-enable-overview.md)



## <a name="next-steps"></a>Passaggi successivi

- Vedere [distribuire VM Insights](./vminsights-enable-overview.md) per i requisiti e i metodi che consentono il monitoraggio delle macchine virtuali.
