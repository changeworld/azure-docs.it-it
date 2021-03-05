---
title: Visualizzare i dati in tempo reale con informazioni dettagliate sul contenitore | Microsoft Docs
description: Questo articolo descrive la visualizzazione in tempo reale di log Kubernetes, eventi e metriche Pod senza usare kubectl in informazioni sul contenitore.
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: references_regions
ms.openlocfilehash: 5277f5051e291e9058255d8920ac0be950389704
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203199"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Come visualizzare i log Kubernetes, gli eventi e le metriche pod in tempo reale

Il contenitore Insights include la funzionalità dati in tempo reale, ovvero una funzionalità di diagnostica avanzata che consente di accedere direttamente ai log del contenitore del servizio Kubernetes (AKS) di Azure (stdout/stderrr), agli eventi e alle metriche pod. Espone l'accesso diretto a `kubectl logs -c` , `kubectl get` gli eventi e `kubectl top pods` . Un riquadro della console mostra i log, gli eventi e le metriche generati dal motore di contenitori per facilitare ulteriormente la risoluzione dei problemi in tempo reale.

Questo articolo fornisce una panoramica dettagliata e consente di comprendere come usare questa funzionalità.

Per informazioni sulla configurazione o la risoluzione dei problemi della funzionalità dati in tempo reale, vedere la [Guida all'installazione](container-insights-livedata-setup.md). Questa funzionalità consente di accedere direttamente all'API Kubernetes e altre informazioni sul modello di autenticazione sono disponibili [qui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

## <a name="view-aks-resource-live-logs"></a>Visualizzare i log live delle risorse AKS
Usare la procedura seguente per visualizzare i log in tempo reale per i pod, le distribuzioni e i set di repliche con o senza informazioni dettagliate sul contenitore dalla visualizzazione risorse di AKS.

1. Nella portale di Azure passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS.

2. Selezionare **carichi di lavoro** nella sezione **risorse Kubernetes** del menu.

3. Selezionare un pod, una distribuzione e un set di repliche dalla rispettiva scheda.

4. Selezionare **registri Live** dal menu della risorsa.

5. Selezionare un pod per avviare la raccolta dei dati attivi.

    [![Log dinamici della distribuzione](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>Visualizzare i log

È possibile visualizzare i dati di log in tempo reale generati dal motore di contenitori dalla visualizzazione **nodi**, **controller** e **contenitori** . Per visualizzare i dati di log, seguire questa procedura.

1. Nella portale di Azure passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **monitoraggio** sul lato sinistro, scegliere **Insights**.

3. Selezionare la scheda **nodi**, **controller** o **contenitori** .

4. Selezionare un oggetto dalla griglia prestazioni, quindi nel riquadro proprietà disponibile sul lato destro selezionare Visualizza opzioni dati in **tempo reale** . Se il cluster AKS è configurato con Single Sign-On tramite Azure AD, verrà richiesto di eseguire l'autenticazione al primo utilizzo durante la sessione del browser. Selezionare l'account e completare l'autenticazione con Azure.

    >[!NOTE]
    >Quando si visualizzano i dati dall'area di lavoro Log Analytics selezionando l'opzione **Visualizza in Analytics** dal riquadro proprietà, i risultati della ricerca nei log visualizzeranno potenzialmente i **nodi**, i **set di daemon**, i **set di repliche**, i **processi**, i **processi cron**, i **Pod** e i **contenitori** che potrebbero non esistere più. Il tentativo di eseguire la ricerca dei log per un contenitore che non è disponibile in `kubectl` avrà esito negativo anche qui. Esaminare la funzionalità [Visualizza in Analytics](container-insights-log-search.md#search-logs-to-analyze-data) per altre informazioni sulla visualizzazione di log cronologici, eventi e metriche.

Dopo l'autenticazione, il riquadro della console dati attivi verrà visualizzato sotto la griglia dei dati sulle prestazioni in cui è possibile visualizzare i dati di log in un flusso continuo. Se l'indicatore di stato di recupero Mostra un segno di spunta verde, che si trova all'estrema destra del riquadro, significa che i dati possono essere recuperati e inizia lo streaming alla console.

![Opzione dati visualizzazione riquadro Proprietà nodo](./media/container-insights-livedata-overview/node-properties-pane.png)

Il titolo del riquadro Mostra il nome del Pod con il quale viene raggruppato il contenitore.

## <a name="view-events"></a>Visualizzare eventi

È possibile visualizzare i dati degli eventi in tempo reale generati dal motore di contenitori dalla visualizzazione **nodi**, **controller**, **contenitori** e **distribuzioni** quando si seleziona un contenitore, un pod, un nodo, un REPLICASET, un DaemonSet, un processo, un cronjob o una distribuzione. Per visualizzare gli eventi, seguire questa procedura.

1. Nella portale di Azure passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **monitoraggio** sul lato sinistro, scegliere **Insights**.

3. Selezionare la scheda **nodi**, **controller**, **contenitori** o **distribuzioni** .

4. Selezionare un oggetto dalla griglia prestazioni, quindi nel riquadro proprietà disponibile sul lato destro selezionare Visualizza opzioni dati in **tempo reale** . Se il cluster AKS è configurato con Single Sign-On tramite Azure AD, verrà richiesto di eseguire l'autenticazione al primo utilizzo durante la sessione del browser. Selezionare l'account e completare l'autenticazione con Azure.

    >[!NOTE]
    >Quando si visualizzano i dati dall'area di lavoro Log Analytics selezionando l'opzione **Visualizza in Analytics** dal riquadro proprietà, i risultati della ricerca nei log visualizzeranno potenzialmente i **nodi**, i **set di daemon**, i **set di repliche**, i **processi**, i **processi cron**, i **Pod** e i **contenitori** che potrebbero non esistere più. Il tentativo di eseguire la ricerca dei log per un contenitore che non è disponibile in `kubectl` avrà esito negativo anche qui. Esaminare la funzionalità [Visualizza in Analytics](container-insights-log-search.md#search-logs-to-analyze-data) per altre informazioni sulla visualizzazione di log cronologici, eventi e metriche.

Dopo l'autenticazione, il riquadro della console dati attivi verrà visualizzato sotto la griglia dei dati sulle prestazioni. Se l'indicatore di stato di recupero Mostra un segno di spunta verde, che si trova all'estrema destra del riquadro, significa che i dati possono essere recuperati e inizia lo streaming alla console.

Se l'oggetto selezionato era un contenitore, selezionare l'opzione **eventi** nel riquadro. Se è stato selezionato un nodo, un pod o un controller, la visualizzazione degli eventi viene selezionata automaticamente.

![Eventi visualizzazione riquadro Proprietà controller](./media/container-insights-livedata-overview/controller-properties-live-event.png)

Il titolo del riquadro Mostra il nome del Pod con il quale viene raggruppato il contenitore.

### <a name="filter-events"></a>Filtrare gli eventi

Quando si visualizzano gli eventi, è anche possibile limitare i risultati usando la pillola di **filtro** trovata a destra della barra di ricerca. A seconda della risorsa selezionata, la pillola elenca un pod, uno spazio dei nomi o un cluster da cui scegliere.

## <a name="view-metrics"></a>Visualizzare le metriche

È possibile visualizzare i dati delle metriche in tempo reale generati dal motore di contenitori dalla visualizzazione **nodi** o **controller** solo quando è selezionato un **Pod** . Per visualizzare le metriche, seguire questa procedura.

1. Nella portale di Azure passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **monitoraggio** sul lato sinistro, scegliere **Insights**.

3. Selezionare la scheda **nodi** o **controller** .

4. Selezionare un oggetto **Pod** dalla griglia prestazioni, quindi nel riquadro proprietà disponibile sul lato destro selezionare **View Live Data** Option. Se il cluster AKS è configurato con Single Sign-On tramite Azure AD, verrà richiesto di eseguire l'autenticazione al primo utilizzo durante la sessione del browser. Selezionare l'account e completare l'autenticazione con Azure.

    >[!NOTE]
    >Quando si visualizzano i dati dall'area di lavoro Log Analytics selezionando l'opzione **Visualizza in Analytics** dal riquadro proprietà, i risultati della ricerca nei log visualizzeranno potenzialmente i **nodi**, i **set di daemon**, i **set di repliche**, i **processi**, i **processi cron**, i **Pod** e i **contenitori** che potrebbero non esistere più. Il tentativo di eseguire la ricerca dei log per un contenitore che non è disponibile in `kubectl` avrà esito negativo anche qui. Esaminare la funzionalità [Visualizza in Analytics](container-insights-log-search.md#search-logs-to-analyze-data) per altre informazioni sulla visualizzazione di log cronologici, eventi e metriche.

Dopo l'autenticazione, il riquadro della console dati attivi verrà visualizzato sotto la griglia dei dati sulle prestazioni. I dati delle metriche vengono recuperati e avviano la trasmissione alla console per la presentazione nei due grafici. Il titolo del riquadro Mostra il nome del Pod con il quale viene raggruppato il contenitore.

![Esempio di visualizzazione delle metriche Pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>Uso delle visualizzazioni dati attive
Le sezioni seguenti descrivono le funzionalità che è possibile usare nelle diverse visualizzazioni dati in tempo reale.

### <a name="search"></a>Ricerca
La funzionalità dati dinamici include funzionalità di ricerca. Nel campo di **ricerca** è possibile filtrare i risultati digitando una parola chiave o un termine e i risultati corrispondenti vengono evidenziati per consentire la revisione rapida. Quando si visualizzano gli eventi, è anche possibile limitare i risultati usando la pillola di **filtro** trovata a destra della barra di ricerca. A seconda della risorsa selezionata, la pillola elenca un pod, uno spazio dei nomi o un cluster da cui scegliere.

![Esempio di filtro del riquadro della console dati in tempo reale](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![Esempio di filtro del riquadro della console dati in tempo reale per la distribuzione](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>BLOC SCORR e pause

Per sospendere lo scorrimento automatico e controllare il comportamento del riquadro, consentendo di scorrere manualmente i nuovi dati letti, è possibile usare l'opzione di **scorrimento** . Per riabilitare lo scorrimento automatico, è sufficiente selezionare di nuovo l'opzione **Scroll** . È anche possibile sospendere il recupero dei dati di log o degli eventi selezionando l'opzione **Sospendi** e, quando si è pronti per riprendere, è sufficiente selezionare **Riproduci**.

![Sospensione della visualizzazione in tempo reale del riquadro della console dati attiva](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![Riquadro della console dati attivati sospendere la visualizzazione in tempo reale per la distribuzione](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>È consigliabile sospendere o sospendere lo scorrimento automatico solo per un breve periodo di tempo durante la risoluzione di un problema. Queste richieste possono influisca sulla disponibilità e sulla limitazione dell'API Kubernetes nel cluster.

>[!IMPORTANT]
>Nessun dato viene archiviato in modo permanente durante il funzionamento di questa funzionalità. Tutte le informazioni acquisite durante la sessione vengono eliminate quando si chiude il browser o si esce dall'esplorazione. I dati rimangono presenti solo per la visualizzazione all'interno della finestra di cinque minuti della funzionalità metrica. verranno eliminate anche le metriche precedenti a cinque minuti. Le query del buffer dei dati in tempo reale entro limiti di utilizzo ragionevoli della memoria.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare monitoraggio di Azure e monitorare altri aspetti del cluster AKS, vedere [visualizzare l'integrità del servizio Azure Kubernetes](container-insights-analyze.md).

- Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti per creare avvisi, visualizzazioni o eseguire ulteriori analisi dei cluster.
