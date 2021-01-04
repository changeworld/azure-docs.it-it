---
title: Connettere l'ambiente a Power BI-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come connettere Azure Time Series Insights a Power BI per condividere, visualizzare grafici e visualizzare i dati nell'intera organizzazione.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 07e79dbde142400677901ee02903144f9a42cd6b
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740765"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Visualizzare i dati da Azure Time Series Insights in Power BI

Azure Time Series Insights è una piattaforma per l'archiviazione, la gestione, l'esecuzione di query e la visualizzazione di dati di serie temporali nel cloud. [Power bi](https://powerbi.microsoft.com) è uno strumento di analisi aziendale con funzionalità di visualizzazione avanzate che consente di condividere informazioni dettagliate e risultati nell'intera organizzazione. È ora possibile integrare entrambi i servizi consentendo di potenziare la potente analisi dei Azure Time Series Insights grazie alla visualizzazione dei dati avanzata e alle semplici funzionalità di condivisione di Power BI.

Si apprenderà come:

* Connettersi Azure Time Series Insights a Power BI tramite il connettore nativo Azure Time Series Insights
* Crea oggetti visivi con i dati delle serie temporali in Power BI
* Pubblicare il report per Power BI e condividere con il resto dell'organizzazione


## <a name="prerequisites"></a>Prerequisiti

* Registrarsi per creare una [sottoscrizione di Azure gratuita](https://azure.microsoft.com/free/), se non è già stato fatto.
* Scaricare e installare la versione più recente di [Power bi desktop](https://powerbi.microsoft.com/downloads/)
* Avere o creare un [ambiente Azure Time Series Insights Gen2](./how-to-provision-manage.md)

Esaminare i [criteri di accesso all'ambiente](./concepts-access-policies.md) e assicurarsi di avere accesso diretto o Guest all'ambiente di Azure Time Series Insights Gen2. 

> [!IMPORTANT]
> * Scaricare e installare la versione più recente di [Power bi desktop](https://powerbi.microsoft.com/downloads/). Per seguire la procedura descritta in questo articolo, verificare che sia installata almeno la versione di dicembre 2020 (2.88.321.0) di Power BI Desktop. 

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Connettere i dati da Azure Time Series Insights a Power BI

### <a name="1-export-data-into-power-bi-desktop"></a>1. esportare i dati in Power BI desktop

Attività iniziali

1. Aprire il Azure Time Series Insights Gen2 Explorer e curare i dati. Si tratta dei dati che verranno esportati in Power BI.
1. Dopo aver creato una vista soddisfatta, passare al menu a discesa **altre azioni** e selezionare **Connetti a Power bi**.

    [![Esportazione di Azure Time Series Insights Gen2 Explorer](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Impostare i parametri per l'esportazione:

   * **Formato dati**: scegliere se si desidera esportare **dati aggregati** o **eventi non elaborati** in Power bi. 

       > [!NOTE]
       > * Se si esportano eventi non elaborati, è possibile aggregare tali dati in un secondo momento in Power BI. Tuttavia, se si esportano dati di aggregazione, non è possibile ripristinare i dati non elaborati in Power BI. 
       > * È previsto un limite di numero di eventi 250.000 per i dati non elaborati a livello di evento.

   * **Intervallo di tempo**: scegliere se si desidera visualizzare un intervallo di tempo **fisso** o i dati **più recenti** in Power bi. Se si sceglie l'intervallo di tempo fisso, i dati nell'intervallo di ricerca di cui è stato effettuato il grafico verranno esportati in Power BI. Se si sceglie l'intervallo di tempo più recente, Power BI acquisisce i dati più recenti per l'intervallo di ricerca scelto. ad esempio, se si crea un grafico di un'ora di dati e si sceglie l'impostazione "più recente", Power BI connettore eseguirà sempre query per la versione più recente di 1 ora di dati.
  
   * **Tipo di archivio**: scegliere se si vuole eseguire la query selezionata nell'archivio di accesso **frequente o a** **freddo**. 

    > [!TIP]
    > * Azure Time Series Insights Explorer selezionerà automaticamente i parametri consigliati a seconda dei dati selezionati per l'esportazione. 

1. Dopo aver configurato le impostazioni, selezionare **copia query negli Appunti**.

    [![Modalità di esportazione di Azure Time Series Insights Explorer](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

2. Avviare Power BI Desktop.
   
3. In Power BI Desktop nella scheda **Home** selezionare **recuperare i dati** nell'angolo in alto a sinistra, quindi **altro**.

    [![Recuperare i dati in Power BI](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

4. Cercare **Azure Time Series Insights**, selezionare **Azure Time Series Insights (beta)**, quindi **Connetti**.

    [![Connetti Power BI a Azure Time Series Insights](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    In alternativa, passare alla scheda **Azure** , selezionare **Azure Time Series Insights (beta)**, quindi **Connetti**.

5. Incollare la query copiata da Esplora Azure Time Series Insights nel campo **query personalizzato** , quindi fare clic su **OK**.

    [![Incollare la query personalizzata e selezionare OK.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

6.  La tabella dati verrà ora caricata. Premere **carica** per caricare i Power bi. Per eseguire trasformazioni dei dati, è possibile fare clic su **trasforma dati**. È anche possibile trasformare i dati dopo che sono stati caricati.

    [![Esaminare i dati nella tabella e selezionare carica](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Creare un report con gli oggetti visivi

Ora che i dati sono stati importati in Power BI, è possibile creare un report con oggetti visivi.

1. Sul lato sinistro della finestra verificare di aver selezionato la visualizzazione **report** .

    [![Screenshot che mostra l'icona della visualizzazione report.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. Nella colonna **visualizzazioni** selezionare l'oggetto visivo scelto. Ad esempio, selezionare **grafico a linee**. Verrà aggiunto un grafico a linee vuoto nell'area di disegno.

1.  Nell'elenco **campi** selezionare **_Timestamp** e trascinarlo nel campo **asse** per visualizzare l'ora lungo l'asse X. Assicurarsi di passare a **_Timestamp** a come valore per l' **asse** (l'impostazione predefinita è **gerarchia data**).

    [![Selezionare _Timestamp](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Anche in questo caso, nell'elenco **campi** selezionare la variabile che si desidera tracciare e trascinarla nel campo **valori** per visualizzare i valori lungo l'asse Y. Selezionare il valore dell'ID della serie temporale e trascinarlo nel campo **Legenda** per creare più righe nel grafico, una per ogni ID di serie temporale. Verrà eseguito il rendering di una visualizzazione simile a quella fornita da Esplora Azure Time Series Insights. 

    [![Creare un grafico a linee di base](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Per aggiungere un altro grafico all'area di disegno, selezionare un punto qualsiasi nell'area di disegno all'esterno del grafico a linee e ripetere il processo.

    [![Crea grafici aggiuntivi da condividere](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Una volta creato il report, è possibile pubblicarlo per Power BI Reporting Services e condividerlo con altri utenti dell'organizzazione.

## <a name="advanced-editing"></a>Modifica avanzata
Se è già stato caricato un set di dati in Power BI ma si vuole modificare la query, ad esempio i parametri di data/ora o ID ambiente, è possibile eseguire questa operazione tramite la funzionalità di Editor avanzato di Power BI. Per ulteriori informazioni su come apportare modifiche utilizzando l' **Editor Power query**, vedere la [documentazione di Power bi](https://docs.microsoft.com/power-bi/desktop-query-overview) . 

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più su [Power bi desktop](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Informazioni sull' [esecuzione di query sui dati](concepts-query-overview.md) in Azure Time Series Insights Gen2.
