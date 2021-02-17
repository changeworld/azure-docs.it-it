---
title: Report in monitoraggio di Azure per i contenitori
description: Vengono descritti i report disponibili per analizzare i dati raccolti da monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 94709cf4a8b579447c11f91f9bc6863ab4b4fe08
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582320"
---
# <a name="reports-in-azure-monitor-for-containers"></a>Report in monitoraggio di Azure per i contenitori
I report in monitoraggio di Azure per i contenitori sono le cartelle di lavoro di [Azure](../visualize/workbooks-overview.md)predefinite. Questo articolo descrive i diversi report disponibili e come accedervi.

## <a name="viewing-reports"></a>Visualizzazione dei report
Dal menu **monitoraggio di Azure** nella portale di Azure selezionare **contenitori**. Selezionare **informazioni dettagliate** nella sezione **monitoraggio** , scegliere un cluster specifico e quindi selezionare la pagina **report (anteprima)** . 

[![Pagina dei report](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Creare una cartella di lavoro personalizzata
Per creare una cartella di lavoro personalizzata in base a una di queste cartelle di lavoro, selezionare l'elenco a discesa **Visualizza cartelle di lavoro** e quindi **passare alla raccolta AKS** nella parte inferiore dell'elenco a discesa. Vedere [cartelle di lavoro di monitoraggio di Azure](../visualize/workbooks-overview.md) per altre informazioni sulle cartelle di lavoro e sull'uso di modelli di cartelle di lavoro

[![Raccolta AKS](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Cartelle di lavoro del nodo

- **Capacità disco**: grafici di utilizzo interattivo del disco per ogni disco presentato al nodo all'interno di un contenitore dalle prospettive seguenti:

    - Percentuale di utilizzo del disco per tutti i dischi.
    - Liberare spazio su disco per tutti i dischi.
    - Griglia che mostra il disco di ogni nodo, la percentuale di spazio utilizzato, la tendenza della percentuale di spazio utilizzato, lo spazio libero su disco (GiB) e la tendenza dello spazio libero su disco (GiB). Quando si seleziona una riga nella tabella, la percentuale di spazio utilizzato e lo spazio libero su disco (GiB) viene visualizzata sotto la riga.

- I/o **su disco**: grafici di utilizzo del disco interattivo per ogni disco presentato al nodo all'interno di un contenitore dalle prospettive seguenti:

    - Operazioni di I/O su disco riepilogate in tutti i dischi per byte letti/sec, scritture byte/sec, tendenze di lettura e scrittura byte/sec.
    - Otto grafici delle prestazioni mostrano indicatori di prestazioni chiave che consentono di misurare e identificare I colli di bottiglia di I/O del disco.

- **GPU**: grafici di utilizzo della GPU interattiva per ogni nodo del cluster Kubernetes compatibile con GPU.

## <a name="resource-monitoring-workbooks"></a>Cartelle di lavoro di monitoraggio delle risorse

- **Distribuzioni**: lo stato delle distribuzioni & il livello di scalabilità automatica pod (hPa), incluso l'hPa personalizzato. 
  
- **Dettagli carico di lavoro**: grafici interattivi che mostrano le statistiche sulle prestazioni dei carichi di lavoro per uno spazio dei nomi Include più schede:

  - Panoramica dell'utilizzo di CPU e memoria da POD.
  - Stato POD/contenitore che mostra la tendenza di riavvio del POD, la tendenza di riavvio del contenitore e lo stato dei contenitori per i pod.
  - Eventi Kubernetes che mostrano il riepilogo degli eventi per il controller.

- **Kubelet**: include due griglie che mostrano le statistiche operative del nodo chiave:

    - Panoramica per griglia del nodo riepiloga le operazioni totali, errori totali e operazioni riuscite per percentuale e tendenza per ogni nodo.
    - Panoramica per tipo di operazione riepiloga per ogni operazione l'operazione totale, gli errori totali e le operazioni riuscite per percentuale e tendenza.
## <a name="billing-workbooks"></a>Cartelle di lavoro di fatturazione

- **Utilizzo dei dati**: consente di visualizzare l'origine dei dati senza dover creare una propria libreria di query da ciò che si condivide nella documentazione. In questa cartella di lavoro sono disponibili grafici con i quali è possibile visualizzare i dati fatturabili da tali prospettive come:

  - Totale dei dati fatturabili inseriti in GB per soluzione
  - Dati fatturabili inseriti da log del contenitore (registri applicazioni)
  - Contenitori fatturabili i dati inseriti in base allo spazio dei nomi Kubernetes
  - Dati dei log del contenitore fatturabili inseriti isolati dal nome del cluster
  - Dati del log del contenitore fatturabili inseriti dalla voce LogSource
  - Dati di diagnostica fatturabili inseriti da log del nodo master di diagnostica

## <a name="networking-workbooks"></a>Cartelle di lavoro di rete

- **Configurazione di NPM**: monitoraggio delle configurazioni di rete configurate tramite Network Policy Manager (NPM).

  - Informazioni di riepilogo sulla complessità complessiva della configurazione.
  - Il conteggio dei criteri, delle regole e dei set nel tempo consente di comprendere la relazione tra i tre e di aggiungere una dimensione temporale al debug di una configurazione.
  - Numero di voci in tutte le IPSets e ogni IPSet.
  - Prestazioni del caso peggiori e medie per nodo per l'aggiunta di componenti alla configurazione di rete.

- **Rete**: grafici interattivi di utilizzo della rete per ogni scheda di rete del nodo e una griglia presenta gli indicatori di prestazioni chiave per misurare le prestazioni delle schede di rete.



## <a name="next-steps"></a>Passaggi successivi

- Vedere [cartelle di lavoro di monitoraggio di Azure](../visualize/workbooks-overview.md) per informazioni dettagliate sulle cartelle di lavoro in monitoraggio di Azure.
