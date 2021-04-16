---
title: Visualizzazione dei dati in tempo reale da hub IoT di Azure - Power BI
description: Usare Power BI per visualizzare i dati di temperatura e umidità raccolti dal sensore e inviati all'hub IoT di Azure.
author: robinsh
keywords: visualizzazione dei dati in tempo reale, visualizzazione dei dati dal vivo, visualizzazione dei dati del sensore
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 0b099f4ce91fd24e8d7baec054bcfc5a6cf0b032
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567112"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualizzare i dati del sensore in tempo reale da IoT Hub di Azure tramite Power BI

![Diagramma end-to-end](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Questo articolo illustra come visualizzare i dati dei sensori in tempo reale che l'hub Azure IoT riceve tramite Power BI. Se si vuole provare a visualizzare i dati nell'hub IoT con un'app Web, vedere Usare un'app Web per visualizzare i dati dei sensori in tempo [reale da hub IoT di Azure](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="prerequisites"></a>Prerequisiti

* Completare [l'esercitazione sul simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o una delle esercitazioni sul dispositivo. Ad esempio, è possibile passare a [Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) con node.jso a una delle [guide](quickstart-send-telemetry-dotnet.md) introduttive Inviare dati di telemetria. Questi articoli riguardano i requisiti seguenti:
  
  * Una sottoscrizione di Azure attiva.
  * Un Azure IoT hub nella sottoscrizione.
  * Un'applicazione client che invia messaggi ad Azure IoT hub.

* Un account di Power BI. ([Provare Power BI gratuitamente](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Configurare, configurare ed eseguire un processo di analisi di flusso

Iniziare creando un processo di Analisi di flusso. Dopo aver creato il processo, definire gli input, gli output e la query usata per recuperare i dati.

### <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.

2. Immettere le seguenti informazioni per il processo.

   **Nome processo**: Nome del processo. Il nome deve essere univoco a livello globale.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Località**: usare lo stesso percorso del gruppo di risorse.

   ![Creare un processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Selezionare **Crea**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Aggiungere un input al processo di Analisi di flusso

1. Aprire il processo di analisi di flusso.

2. In **Topologia processo** selezionare **Input**.

3. Nel riquadro **Input selezionare** Aggiungi input **del flusso** e quindi selezionare **Hub IoT** dall'elenco a discesa. Nel nuovo riquadro di input immettere le informazioni seguenti:

   **Alias di input:** immettere un alias univoco per l'input.

   **Selezionare Hub IoT dalla sottoscrizione:** selezionare questo pulsante di opzione.

   **Sottoscrizione** selezionare la sottoscrizione usata per questa esercitazione.

   **Hub IoT:** selezionare l'hub IoT in uso per questa esercitazione.

   **Endpoint**: selezionare **Messaggistica**.

   **Nome criteri di accesso condiviso:** selezionare il nome dei criteri di accesso condiviso da usare per il processo di Analisi di flusso per l'hub IoT. Per questa esercitazione, è possibile selezionare *il servizio*. I *criteri del* servizio vengono creati per impostazione predefinita nei nuovi hub IoT e concedono l'autorizzazione per l'invio e la ricezione su endpoint sul lato cloud esposti dall'hub IoT. Per altre informazioni, vedere Controllo [di accesso e autorizzazioni.](iot-hub-devguide-security.md#access-control-and-permissions)

   **Chiave dei criteri di accesso condiviso:** questo campo viene compilato automaticamente in base alla selezione del nome dei criteri di accesso condiviso.

   **Gruppo di consumer:** selezionare il gruppo di consumer creato in precedenza.

   Lasciare i valori predefiniti per tutti gli altri campi.

   ![Aggiungere un input al processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Selezionare **Salva**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Aggiungere un output al processo di Analisi di flusso

1. In **Topologia processo** selezionare **Output**.

2. Nel riquadro **Output** selezionare **Aggiungi** e quindi **Power BI**.

3. Nel riquadro **Power BI - Nuovo output** selezionare **Autorizza** e seguire le istruzioni per accedere all'account Power BI locale.

4. Dopo aver eseguito l'accesso a Power BI, immettere le informazioni seguenti:

   **Alias di output:** alias univoco per l'output.

   **Area di lavoro del gruppo:** selezionare l'area di lavoro del gruppo di destinazione.

   **Nome set di** dati: immettere un nome di set di dati.

   **Nome tabella:** immettere un nome di tabella.

   **Modalità di autenticazione:** lasciare il valore predefinito.

   ![Aggiungere un output al processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Selezionare **Salva**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurare la query del processo di Analisi di flusso

1. In **Topologia processo** selezionare **Query**.

2. Sostituire `[YourInputAlias]` con l'alias di input del processo.

3. Sostituire `[YourOutputAlias]` con l'alias di output del processo.

   ![Aggiungere una query al processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Selezionare **Salva query**.

### <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

Nel processo di Analisi di flusso selezionare **Panoramica,** quindi **selezionare**  >  **Avvia adesso**  >  **Avvia**. Dopo aver avviato correttamente il processo, lo stato del processo passa da **Interrotto** a **In esecuzione**.

![Eseguire un processo di analisi di flusso in Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Creare e pubblicare un report di Power BI per visualizzare i dati

La procedura seguente illustra come creare e pubblicare un report usando il servizio Power BI. È possibile seguire questi passaggi, con alcune modifiche, se si vuole usare il "nuovo aspetto" in Power BI. Per comprendere le differenze e come spostarsi nel "nuovo aspetto", vedere Il nuovo aspetto [dell'servizio Power BI](/power-bi/consumer/service-new-look).

1. Verificare che l'applicazione di esempio sia in esecuzione nel dispositivo. Se non lo fosse, è possibile fare riferimento alle esercitazioni descritte in [Configurare il dispositivo](./iot-hub-raspberry-pi-kit-node-get-started.md).

2. Accedere all'account [Power BI](https://powerbi.microsoft.com/en-us/).

3. Selezionare l'area di lavoro usata, **Area di lavoro personale**.

4. Selezionare **Set di dati**.

   Dovrebbero essere visualizzati i set di dati specificati quando è stato creato l'output per il processo di Analisi di flusso.

5. Per il set di dati creato, selezionare **Aggiungi report** (la prima icona a destra del nome del set di dati).

   ![Creare un report di Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Creare un grafico a linee per visualizzare la temperatura in tempo reale nel tempo.

   1. Nel riquadro **Visualizzazioni della** pagina di creazione del report selezionare l'icona del grafico a linee per aggiungere un grafico a linee.

   2. Nel riquadro **Campi** espandere la tabella specificata durante la creazione di output per il processo di analisi di flusso.

   3. Trascinare **EventEnqueuedUtcTime** in **Asse** sul riquadro **Visualizzazioni**.

   4. Trascinare la **temperatura** in **Valori**.

      Viene creato un grafico a linee. L'asse x mostra data e ora per il fuso orario UTC. L'asse y mostra la temperatura dal sensore.

      ![Aggiungere un grafico a linee per la temperatura a un report di Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Creare un altro grafico a linee in modo da visualizzare in tempo reale l'umidità nel tempo. A tale scopo, fare clic su una parte vuota dell'area di disegno e seguire gli  stessi passaggi precedenti per posizionare **EventEnqueuedUtcTime** sull'asse x e umidità sull'asse y.

   ![Aggiungere un grafico a linee per l'umidità a un report di Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selezionare **Salva** per salvare il report.

9. Selezionare **Report** nel riquadro sinistro e quindi selezionare il report appena creato.

10. Selezionare **Pubblica**  >  **file sul Web.**

    ![Selezionare Pubblica sul Web per il report di Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Se si ottiene una notifica per contattare l'amministratore per abilitare la creazione del codice di incorporamento, potrebbe essere necessario contattarli. La creazione del codice di incorporamento deve essere abilitata prima di poter completare questo passaggio.
    >
    > ![Contattare la notifica dell'amministratore](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Selezionare **Crea codice di incorporamento** e quindi Selezionare **Pubblica.**

Viene fornito il collegamento al report che è possibile condividere con chiunque per l'accesso al report e un frammento di codice che è possibile usare per integrare il report nel blog o nel sito Web.

![Pubblicare un report di Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

Microsoft offre anche le [App per dispositivi mobili di Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) per la visualizzazione e l'interazione con i dashboard di Power BI e i report sul tuo dispositivo mobile.

## <a name="next-steps"></a>Passaggi successivi

Si è utilizzato correttamente Power BI per visualizzare i dati del sensore in tempo reale dall'hub IoT di Azure.

Per un altro modo per visualizzare i dati hub IoT di Azure, vedere Usare un'app Web per visualizzare i dati del sensore in tempo [reale da hub IoT di Azure](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
