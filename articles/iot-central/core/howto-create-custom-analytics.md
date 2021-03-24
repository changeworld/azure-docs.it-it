---
title: Estendi IoT Central di Azure con analisi personalizzate | Microsoft Docs
description: Per gli sviluppatori di soluzioni, configurare un'applicazione IoT Central per eseguire analisi e visualizzazioni personalizzate. Questa soluzione USA Azure Databricks.
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0cee343e6769c815ecfb4b9c791783bd246caaac
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953902"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Estendi IoT Central di Azure con analisi personalizzate con Azure Databricks

Questa guida dettagliata illustra come uno sviluppatore di soluzioni, come estendere l'applicazione IoT Central con le analisi e le visualizzazioni personalizzate. Nell'esempio viene usata un'area di lavoro di [Azure Databricks](/azure/azure-databricks/) per analizzare il flusso di dati di telemetria IOT Central e generare visualizzazioni, ad esempio i [tracciati box](https://wikipedia.org/wiki/Box_plot).  

Questa guida dettagliata illustra come estendere IoT Central oltre quello che può già fare con gli [strumenti di analisi incorporati](./howto-create-custom-analytics.md).

In questa guida dettagliata si apprenderà come:

* Trasmettere i dati di telemetria da un'applicazione IoT Central usando l' *esportazione continua dei dati*.
* Creare un ambiente di Azure Databricks per analizzare e tracciare i dati di telemetria del dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="iot-central-application"></a>Applicazione IoT Central

Creare un'applicazione IoT Central nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Piano tariffario | Standard |
| Modello di applicazione | Analisi in-Store-monitoraggio delle condizioni |
| Nome applicazione | Accetta il nome predefinito o scegli il tuo nome |
| URL | Accettare l'impostazione predefinita o scegliere il prefisso URL univoco |
| Directory | Tenant di Azure Active Directory |
| Sottoscrizione di Azure | Sottoscrizione di Azure |
| Region | Area geografica più vicina |

Gli esempi e le schermate in questo articolo usano l'area **Stati Uniti** . Scegliere una località vicina e assicurarsi di creare tutte le risorse nella stessa area.

Questo modello di applicazione include due dispositivi termotermostati simulati che inviano dati di telemetria.

### <a name="resource-group"></a>Resource group

Usare il [portale di Azure per creare un gruppo di risorse](https://portal.azure.com/#create/Microsoft.ResourceGroup) denominato **IoTCentralAnalysis** per contenere le altre risorse create. Creare le risorse di Azure nello stesso percorso dell'applicazione IoT Central.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare il [portale di Azure per creare uno spazio dei nomi di hub eventi](https://portal.azure.com/#create/Microsoft.EventHub) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Subscription | Sottoscrizione in uso |
| Resource group | IoTCentralAnalysis |
| Località | Stati Uniti orientali |
| Unità elaborate | 1 |

### <a name="azure-databricks-workspace"></a>Area di lavoro Azure Databricks

Usare il [portale di Azure per creare un servizio Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome dell'area di lavoro    | Scegliere il nome dell'area di lavoro |
| Subscription | Sottoscrizione in uso |
| Resource group | IoTCentralAnalysis |
| Località | Stati Uniti orientali |
| Piano tariffario | Standard |

Quando sono state create le risorse necessarie, il gruppo di risorse **IoTCentralAnalysis** è simile allo screenshot seguente:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="immagine di IoT Central gruppo di risorse di analisi.":::

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente i dati di telemetria in un hub eventi. In questa sezione viene creato un hub eventi per ricevere i dati di telemetria dall'applicazione IoT Central. L'hub eventi recapita i dati di telemetria al processo di analisi di flusso per l'elaborazione.

1. Nella portale di Azure passare allo spazio dei nomi di hub eventi e selezionare **+ Hub eventi**.
1. Assegnare un nome all'hub eventi **centralexport**.
1. Nell'elenco degli hub eventi nello spazio dei nomi selezionare **centralexport**. Quindi scegliere **criteri di accesso condiviso**.
1. Selezionare **+ Aggiungi**. Creare un criterio denominato **Listen** con l'attestazione **Listen** .
1. Quando il criterio è pronto, selezionarlo nell'elenco e quindi copiare il valore della **stringa di connessione-chiave primaria** .
1. Prendere nota di questa stringa di connessione, che verrà usata in un secondo momento quando si configura il notebook di databricks per la lettura dall'hub eventi.

Lo spazio dei nomi di hub eventi è simile allo screenshot seguente:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="immagine dello spazio dei nomi di hub eventi.":::

## <a name="configure-export-in-iot-central-and-create-a-new-destination"></a>Configurare l'esportazione in IoT Central e creare una nuova destinazione

Nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) passare all'applicazione IoT Central creata dal modello contoso. In questa sezione l'applicazione viene configurata in modo da trasmettere i dati di telemetria dai dispositivi simulati all'hub eventi. Per configurare l'esportazione:

1. Passare alla pagina **esportazione dati** e selezionare **+ nuova esportazione**.
1. Prima di terminare la prima finestra, selezionare **Crea una destinazione**.

La finestra sarà simile a quella riportata di seguito.  

:::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="immagine della configurazione della destinazione di esportazione dei dati.":::

3. Immettere i valori seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome destinazione | Nome della destinazione |
| Tipo destinazione | Hub eventi di Azure |
| Connection String (Stringa di connessione)| La stringa di connessione dell'hub eventi è stata annotata in precedenza. | 
| Hub eventi| Nome dell'hub eventi|

4. Per terminare, fare clic su **Crea** .

5. Usare le impostazioni seguenti per configurare l'esportazione:

    | Impostazione | Valore |
    | ------- | ----- |
    | Immettere un nome di esportazione | eventhubexport |
    | Abilitato | On |
    | Dati| Seleziona telemetria | 
    | Destinazioni| Creare una destinazione, come illustrato di seguito, per l'esportazione e quindi selezionarla nel menu a discesa destinazione. |

:::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Screenshot della configurazione della destinazione di esportazione dei dati.":::

6. Al termine, selezionare **Salva**.

Prima di continuare, attendere che lo stato di esportazione sia **in esecuzione** .

## <a name="configure-databricks-workspace"></a>Configura area di lavoro databricks

Nella portale di Azure passare al servizio Azure Databricks e selezionare **Avvia area di lavoro**. Viene aperta una nuova scheda nel browser e viene effettuato l'accesso all'area di lavoro.

### <a name="create-a-cluster"></a>Creare un cluster

Nell'elenco delle attività comuni della pagina **Azure Databricks** selezionare **nuovo cluster**.

Usare le informazioni nella tabella seguente per creare il cluster:

| Impostazione | Valore |
| ------- | ----- |
| Cluster Name | centralanalysis |
| Modalità cluster | Standard |
| Versione Databricks Runtime | 5,5 LTS (scala 2,11, Spark 2.4.5) |
| Versione di Python | 3 |
| Enable Autoscaling (Abilita la scalabilità automatica) | No |
| Termina dopo minuti di inattività | 30 |
| Tipo di lavoro | Standard_DS3_v2 |
| Ruoli di lavoro | 1 |
| Tipo di driver | Uguale al ruolo di lavoro |

La creazione di un cluster può richiedere alcuni minuti, attendere il completamento della creazione del cluster prima di continuare.

### <a name="install-libraries"></a>Installare le librerie

Nella pagina **cluster** attendere che lo stato del cluster sia **in esecuzione**.

La procedura seguente illustra come importare la libreria necessaria per l'esempio nel cluster:

1. Nella pagina **cluster** attendere che lo stato del cluster interattivo **Centralanalysis** sia **in esecuzione**.

1. Selezionare il cluster e quindi scegliere la scheda **librerie** .

1. Nella scheda **librerie** scegliere **Installa nuovo**.

1. Nella pagina **Installa libreria** scegliere **Maven** come origine della libreria.

1. Nella casella di testo **Coordinate** immettere il valore seguente: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Scegliere **Installa** per installare la libreria nel cluster.

1. Lo stato della libreria è ora **installato**:

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Screenshot della libreria installata.":::

### <a name="import-a-databricks-notebook"></a>Importare un notebook di databricks

Usare la procedura seguente per importare un notebook di databricks contenente il codice Python per analizzare e visualizzare i dati di telemetria IoT Central:

1. Passare alla pagina dell' **area di lavoro** nell'ambiente databricks. Selezionare l'elenco a discesa accanto al nome dell'account e quindi scegliere **Importa**.

1. Scegliere di importare da un URL e immettere l'indirizzo seguente: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Per importare il notebook, scegliere **Importa**.

1. Selezionare l' **area di lavoro** per visualizzare il notebook importato:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Screenshot del notebook importato.":::

5. Modificare il codice nella prima cella Python per aggiungere la stringa di connessione dell'hub eventi salvata in precedenza:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Esegui analisi

Per eseguire l'analisi, è necessario alleghi il notebook al cluster:

1. Selezionare **scollegato** e quindi selezionare il cluster **centralanalysis** .
1. Se il cluster non è in esecuzione, avviarlo.
1. Per avviare il notebook, selezionare il pulsante Run (Esegui).

Nell'ultima cella potrebbe essere visualizzato un errore. In caso affermativo, controllare che le celle precedenti siano in esecuzione, attendere un minuto per la scrittura dei dati nella risorsa di archiviazione, quindi eseguire di nuovo l'ultima cella.

### <a name="view-smoothed-data"></a>Visualizza dati smussati

Nel notebook scorrere fino alla cella 14 per visualizzare un tracciato dell'umidità media mobile per tipo di dispositivo. Questo tracciato viene aggiornato continuamente durante l'arrivo della telemetria di streaming:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Screenshot del tracciato di telemetria smussato.":::

È possibile ridimensionare il grafico nel notebook.

### <a name="view-box-plots"></a>Visualizza grafici box

Nel notebook scorrere fino alla cella 20 per visualizzare i [tracciati box](https://en.wikipedia.org/wiki/Box_plot). I tracciati box sono basati sui dati statici, pertanto per aggiornarli è necessario eseguire di nuovo la cella:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Screenshot dei tracciati box.":::

È possibile ridimensionare i tracciati nel notebook.

## <a name="tidy-up"></a>Operazioni finali

Per riordinare dopo questa procedura ed evitare i costi non necessari, eliminare il gruppo di risorse **IoTCentralAnalysis** nel portale di Azure.

È possibile eliminare l'applicazione IoT Central dalla pagina di **gestione** all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Trasmettere i dati di telemetria da un'applicazione IoT Central usando l' *esportazione continua dei dati*.
* Creare un ambiente di Azure Databricks per analizzare e tracciare i dati di telemetria.

Ora che si è appreso come creare analisi personalizzate, il passaggio successivo suggerito consiste nell'apprendere come [visualizzare e analizzare i dati di IOT Central di Azure in un dashboard Power bi](howto-connect-powerbi.md).