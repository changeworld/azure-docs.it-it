---
title: Configurare il monitoraggio e le metriche con monitoraggio di Azure
titleSuffix: Azure Bastion
description: Informazioni sul monitoraggio di Azure Bastion e sulle metriche con monitoraggio di Azure, la soluzione per metriche, avvisi e log di diagnostica in Azure.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: 3a5beba3938b5a845a378ede155f2f64e6baac7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417944"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Come configurare il monitoraggio e le metriche per Azure Bastion con monitoraggio di Azure

Questo articolo illustra come usare il monitoraggio e le metriche per Azure Bastion con monitoraggio di Azure.

>[!NOTE]
>L'uso delle **metriche classiche** non è consigliato.
>

## <a name="about-metrics"></a>Informazioni sulle metriche

Azure Bastion include diverse metriche disponibili. Nella tabella seguente vengono illustrate la categoria e le dimensioni per ogni metrica disponibile.

|**Metrica**|**Categoria**|**Dimensione/i**|
| --- | --- | --- |
|Stato di comunicazione Bastion * *|[Disponibilità](#availability)|N/D|
|Memoria totale|[Disponibilità](#availability)|Istanza|
|CPU utilizzata|[Traffico](#traffic)|Istanza
|Memoria utilizzata|[Traffico](#traffic)|Istanza
|Conteggio sessioni|[Prestazioni](#performance)|Istanza|

* * Lo stato di comunicazione Bastion è applicabile solo agli host Bastion distribuiti dopo il 2020 novembre.

### <a name="availability-metrics"></a><a name="availability"></a>Metriche di disponibilità

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Stato di comunicazione Bastion

È possibile visualizzare lo stato di comunicazione di Azure Bastion, aggregato in tutte le istanze che comprendono l'host Bastion.

* Il valore **1** indica che il Bastion è disponibile.
* Il valore **0** indica che il servizio Bastion non è disponibile.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="Screenshot che mostra lo stato della comunicazione.":::

#### <a name="total-memory"></a><a name="total-memory"></a>Memoria totale

È possibile visualizzare la memoria totale di Azure Bastion, suddivisa in ogni istanza Bastion.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="Screenshot che mostra la memoria totale.":::

### <a name="traffic-metrics"></a><a name="traffic"></a>Metriche del traffico

#### <a name="used-cpu"></a><a name="used-cpu"></a>CPU utilizzata

È possibile visualizzare l'utilizzo della CPU di Azure Bastion, suddiviso in ogni istanza Bastion. Il monitoraggio di questa metrica consente di misurare la disponibilità e la capacità delle istanze che includono Azure Bastion.

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="Screenshot che mostra la CPU utilizzata.":::

#### <a name="used-memory"></a><a name="used-memory"></a>Memoria utilizzata

È possibile visualizzare l'utilizzo della memoria in ogni istanza Bastion, suddivisa in ogni istanza Bastion. Il monitoraggio di questa metrica consente di misurare la disponibilità e la capacità delle istanze che includono Azure Bastion.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="Screenshot che mostra la memoria usata.":::

### <a name="performance-metrics"></a><a name="performance"></a>Metriche delle prestazioni

#### <a name="session-count"></a>Conteggio sessioni

È possibile visualizzare il numero di sessioni attive per ogni istanza Bastion, aggregate in ogni tipo di sessione (RDP e SSH). Ogni Bastion di Azure può supportare una gamma di sessioni RDP e SSH attive. Il monitoraggio di questa metrica aiuta a comprendere se è necessario modificare il numero di istanze che eseguono il servizio Bastion. Per altre informazioni sul numero di sessioni che Azure Bastion può supportare, vedere le [domande frequenti su Azure Bastion](bastion-faq.md).

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="Screenshot che mostra il numero di sessioni.":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>Come visualizzare le metriche

1. Per visualizzare le metriche, passare all'host Bastion.
1. Dall'elenco **monitoraggio** selezionare **metriche**.
1. Selezionare i parametri. Se non è impostata alcuna metrica, fare clic su **Aggiungi metrica**, quindi selezionare i parametri.

   * **Ambito:** Per impostazione predefinita, l'ambito è impostato sull'host Bastion.
   * **Spazio dei nomi metrica:** Metriche standard.
   * **Metrica:** Selezionare la metrica che si desidera visualizzare.

1. Una volta selezionata la metrica, verrà applicata l'aggregazione predefinita. Facoltativamente, è possibile applicare la suddivisione, che mostrerà la metrica con dimensioni diverse.

## <a name="next-steps"></a>Passaggi successivi

Leggere le [domande frequenti su Bastion](bastion-faq.md).
  
