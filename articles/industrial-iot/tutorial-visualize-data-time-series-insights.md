---
title: Visualizza i dati di OPC UA in Azure Time Series Insights
description: In questa esercitazione si apprenderà come visualizzare i dati con Time Series Insights.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787815"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Esercitazione: visualizzare i dati con Time Series Insights (TSI)

Il modulo di pubblicazione OPC si connette ai server OPC UA e pubblica i dati da questi server nell'hub Internet. Il processore di telemetria nella piattaforma dell'it industriale elabora questi eventi e li trasmette a STI e ad altri utenti.  

Questa guida illustra come visualizzare e analizzare i dati di telemetria OPC UA usando questo ambiente Time Series Insights.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Tutte le esercitazioni includono un elenco che riepiloga i passaggi da completare
> * Ognuno di questi punti elenco è allineato a una chiave H2
> * Usare queste caselle di controllo verdi in un'esercitazione

## <a name="prerequisite"></a>Prerequisito

* Distribuire la piattaforma IIoT per creare automaticamente un ambiente Time Series Insights
* I dati vengono pubblicati nell'hub Internet

## <a name="time-series-insights-explorer"></a>Strumento di esplorazione di Time Series Insights

Lo strumento di esplorazione di Azure Time Series Insights è un'app Web che si può usare per visualizzare i dati di telemetria. Per recuperare l'URL dell'applicazione, aprire il `.env` file salvato come risultato della distribuzione.  Aprire un browser per l'URL nella `PCS_TSI_URL` variabile.  

Prima di utilizzare Esplora Time Series Insights, è necessario concedere l'accesso ai dati di STI agli utenti autorizzati a visualizzare i dati. Si noti che in una nuova distribuzione non vengono impostati criteri di accesso ai dati per impostazione predefinita, pertanto nessuno può visualizzare i dati. I criteri di accesso ai dati devono essere impostati nell'portale di Azure, nell'ambiente Time Series Insights distribuito nel gruppo di risorse distribuito della piattaforma IIoT, come indicato di seguito:

   ![Esplora Time Series Insights 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Selezionare i criteri di accesso ai dati:

   ![Esplora Time Series Insights 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

Assegnare gli utenti necessari:

   ![Esplora Time Series Insights 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


In TSI Explorer, prendere nota delle istanze della serie temporale non assegnate. Un'istanza di TSI corrisponde alla serie time/value per uno specifico punto dati originato da un nodo pubblicato in un server OPC. L'istanza di TSI, rispettivamente il punto dati OPC UA, viene identificata in modo univoco da EndpointId all', SubscriptionId e NodeId. I modelli di istanze di TSI vengono automaticamente rilevati e visualizzati nella finestra di esplorazione in base ai dati di telemetria inseriti dall'hub eventi del processore di telemetria della piattaforma IIoT.

   ![Time Series Insights Explorer 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

È possibile visualizzare i dati di telemetria nel grafico facendo clic con il pulsante destro del mouse sull'istanza di TSI e selezionando il valore. L'intervallo di tempo da utilizzare nel grafico può essere regolato dall'angolo superiore destro. Il valore di più istanze può essere visualizzato con la stessa selezione basata sul tempo.

Per altre informazioni, vedere [Guida introduttiva: esplorare l'anteprima Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

## <a name="define-and-apply-a-new-model"></a>Definire e applicare un nuovo modello

Poiché le istanze di telemetria sono ora in formato non elaborato, devono essere configurate con il 

Per informazioni dettagliate sui modelli di STI, vedere [modello Time Series in Azure Time Series Insights Preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)

1. Passaggio 1: nella scheda modello della finestra di esplorazione definire una nuova gerarchia per i dati di telemetria inseriti. Una gerarchia è la struttura ad albero logica progettata per consentire all'utente di inserire le metainformazioni necessarie per una navigazione più intuitiva nelle istanze di TSI. un utente può creare, eliminare o modificare i modelli di gerarchia che possono essere creati in un secondo momento per le varie istanze di TSI.

   ![Passaggio 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. Passaggio 2: definire un nuovo tipo per i valori. In questo esempio vengono gestiti solo i tipi di dati numerici

   ![Passaggio 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. Passaggio 3: selezionare la nuova istanza di TSI che richiede la categorizzazione nella gerarchia definita in precedenza

   ![Passaggio 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. Passaggio 4: compilare le proprietà delle istanze, ovvero nome, descrizione, valore dei dati e campi della gerarchia in modo che corrispondano alla struttura logica 

   ![Passaggio 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. Passaggio 5: ripetere il passaggio 5 per tutte le istanze di TSI senza categoria

   ![Passaggio 5](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. Passaggio 6: tornare alla pagina principale di STI Explorer, esaminare la gerarchia di istanze categorizzate e selezionare i valori per i punti dati da analizzare

   ![Passaggio 6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Connetti Time Series Insights a Power BI

È anche possibile connettere l'ambiente Time Series Insights per Power BI.  Per ulteriori informazioni, vedere la pagina relativa alla [modalità di connessione di TSI a Power bi](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) e [visualizzazione dei dati da TSI in Power bi](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi).


## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come visualizzare i dati in TSI, è possibile consultare il repository GitHub Industrial Internet:

> [!div class="nextstepaction"]
> [Repository GitHub della piattaforma IIoT](https://github.com/Azure/iot-edge-opc-publisher)