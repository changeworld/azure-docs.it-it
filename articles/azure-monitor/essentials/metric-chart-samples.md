---
title: Esempio di grafico delle metriche di Monitoraggio di Azure
description: Informazioni sulla visualizzazione dei dati di Monitoraggio di Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 0430f8efedbcefba551ce3ee81d0db8a654f5b13
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617127"
---
# <a name="metric-chart-examples"></a>Esempi di grafici delle metriche 

La piattaforma di Azure offre [più di mille metriche](../platform/metrics-supported.md), molte delle quali con dimensioni. Usando i [filtri di dimensione](./metrics-charts.md), applicando [suddivisioni](./metrics-charts.md), controllando il tipo di grafico e modificando le impostazioni del grafico, è possibile creare visualizzazioni diagnostiche avanzate e dashboard che forniscono informazioni dettagliate sull'integrità dell'infrastruttura e delle applicazioni. Questo articolo mostra alcuni esempi dei grafici che è possibile creare usando [Esplora metriche](./metrics-charts.md) e illustra i passaggi necessari per configurare ciascuno di essi.

Come condividere ottimi esempi di grafici? Contribuendo a questa pagina su GitHub, condividendo qui i propri esempi di grafico.

## <a name="website-cpu-utilization-by-server-instances"></a>Uso della CPU su sito Web da istanze del server

Questo grafico mostra se l'uso della CPU per un servizio app rientra in un intervallo accettabile e lo suddivide per istanza per determinare se il carico è stato distribuito correttamente. Il grafico mostra che l'app era in esecuzione in una singola istanza del server prima delle ore 06:00 e che in seguito è stata aumentata mediante l'aggiunta di un'altra istanza.

![Grafico a linee della percentuale CPU media per istanza del server](./media/metrics-charts/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Come configurare questo grafico?

Selezionare la risorsa del Servizio app di Azure e cercare la metrica **Percentuale CPU**. Quindi fare clic su **Applica suddivisione** e selezionare la dimensione **Instance** (Istanza).

## <a name="application-availability-by-region"></a>Disponibilità dell'applicazione per area

Consente di visualizzare la disponibilità dell'applicazione per area, per identificare quali aree geografiche stanno riscontrando problemi. Questo grafico mostra la metrica di disponibilità di Application Insights. È possibile notare che l'applicazione monitorata non presenta alcun problema di disponibilità dal data center degli Stati Uniti orientali, ma sussiste un problema di disponibilità parziale dai data center degli Stati Uniti occidentali e dell'Asia orientale.

![Grafico della disponibilità media in base ai percorsi](./media/metrics-charts/availability-by-location.png)

### <a name="how-to-configure-this-chart"></a>Come configurare questo grafico?

È innanzitutto necessario attivare il monitoraggio della [disponibilità di Application Insights](../app/monitor-web-app-availability.md) dal sito Web. In seguito, selezionare la risorsa di Application Insights e selezionare la metrica di disponibilità. Applicare la suddivisione nella dimensione **Percorso esecuzione**.

## <a name="volume-of-failed-storage-account-transactions-by-api-name"></a>Volume delle transazioni dell'account di archiviazione non riuscite per nome API

La risorsa dell'account di archiviazione sta riscontrando un volume eccedente di transazioni non riuscite. È possibile usare la metrica transazioni per identificare quale API è responsabile dell'errore in eccesso. Si noti che il grafico seguente viene configurato con la stessa dimensione (nome API) in divisione e filtrato in base al tipo di risposta non riuscita:

![Grafico a barre delle transazioni API](./media/metrics-charts/split-and-filter-example.png)

### <a name="how-to-configure-this-chart"></a>Come configurare questo grafico?

Nel selettore metrica, selezionare l'account di archiviazione e la metrica **Transactions** (Transazioni). Passare al tipo di grafico **Grafico a barre**. Fare clic su **Applica suddivisione** e selezionare il **nome API** della dimensione. Quindi fare clic su **Aggiungi filtro** e selezionare nuovamente il **nome API** della dimensione. Nella finestra di dialogo dei filtri, selezionare le API da tracciare sul grafico.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [Cartelle di lavoro](../visualize/workbooks-overview.md) di Monitoraggio di Azure
* Informazioni su [Esplora metriche](metrics-charts.md)

