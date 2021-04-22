---
title: Estendere Azure IoT Central con analisi personalizzata | Microsoft Docs
description: Gli sviluppatori di soluzioni possono configurare un'IoT Central per eseguire analisi e visualizzazioni personalizzate. Questa soluzione usa Azure Databricks.
author: philmea
ms.author: philmea
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9ada9947b217944d9aec9f785f4716bfe43315b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872768"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Estendere Azure IoT Central con analisi personalizzate mediante Azure Databricks

Questa guida dettagliata illustra agli sviluppatori di soluzioni come estendere l'applicazione IoT Central con visualizzazioni e analisi personalizzate. L'esempio usa [un'Azure Databricks](/azure/azure-databricks/) di lavoro per analizzare IoT Central flusso di telemetria e per generare visualizzazioni, ad esempio [box plot.](https://wikipedia.org/wiki/Box_plot)  

Questa guida dettagliata illustra come estendere le IoT Central oltre a ciò che può già fare con gli [strumenti di analisi predefiniti.](./howto-create-custom-analytics.md)

In questa guida dettagliata si apprenderà come:

* Trasmettere dati di telemetria da IoT Central'applicazione usando *l'esportazione continua dei dati*.
* Creare un ambiente Azure Databricks per analizzare e tracciare i dati di telemetria dei dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="iot-central-application"></a>Applicazione IoT Central

Creare un'IoT Central nel sito [Web Azure IoT Central application manager](https://aka.ms/iotcentral) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Piano tariffario | Standard |
| Modello di applicazione | Analisi in-store : monitoraggio delle condizioni |
| Nome applicazione | Accettare l'impostazione predefinita o scegliere il proprio nome |
| URL | Accettare l'impostazione predefinita o scegliere il prefisso URL univoco |
| Directory | Tenant Azure Active Directory |
| Sottoscrizione di Azure | Sottoscrizione di Azure |
| Region | L'area più vicina |

Gli esempi e gli screenshot in questo articolo usano l'area Stati Uniti **di** lavoro. Scegliere una località vicina e assicurarsi di creare tutte le risorse nella stessa area.

Questo modello di applicazione include due dispositivi termostato simulati che inviano dati di telemetria.

### <a name="resource-group"></a>Resource group

Usare il [portale di Azure per creare un gruppo](https://portal.azure.com/#create/Microsoft.ResourceGroup) di risorse denominato **IoTCentralAnalysis** che contenga le altre risorse create. Creare le risorse di Azure nello stesso percorso dell'applicazione IoT Central locale.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare il [portale di Azure per creare uno spazio dei nomi di Hub eventi](https://portal.azure.com/#create/Microsoft.EventHub) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Subscription | Sottoscrizione in uso |
| Resource group | IoTCentralAnalysis |
| Località | Stati Uniti orientali |
| Unità elaborate | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks lavoro

Usare il [portale di Azure per creare un servizio Azure Databricks con](https://portal.azure.com/#create/Microsoft.Databricks) le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome dell'area di lavoro    | Scegliere il nome dell'area di lavoro |
| Subscription | Sottoscrizione in uso |
| Resource group | IoTCentralAnalysis |
| Località | Stati Uniti orientali |
| Piano tariffario | Standard |

Dopo aver creato le risorse necessarie, il gruppo di risorse **IoTCentralAnalysis** è simile allo screenshot seguente:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="immagine del gruppo IoT Central di risorse di analisi.":::

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente i dati di telemetria in un hub eventi. In questa sezione viene creato un hub eventi per ricevere i dati di telemetria dall'IoT Central applicazione. L'hub eventi recapita i dati di telemetria al processo di Analisi di flusso per l'elaborazione.

1. Nell'portale di Azure passare allo spazio dei nomi di Hub eventi e selezionare **+ Hub eventi.**
1. Assegnare all'hub eventi **il nome centralexport**.
1. Nell'elenco di hub eventi nello spazio dei nomi selezionare **centralexport**. Scegliere quindi **Criteri di accesso condiviso.**
1. Selezionare **+ Aggiungi**. Creare un criterio denominato **SendListen con** le attestazioni **Send** **e Listen.**
1. Quando il criterio è pronto, selezionarlo nell'elenco e quindi copiare il **valore stringa di connessione-chiave primaria.**
1. Prendere nota di questa stringa di connessione e usarla in un secondo momento quando si configura il notebook di Databricks per la lettura dall'hub eventi.

Lo spazio dei nomi di Hub eventi è simile allo screenshot seguente:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="immagine dello spazio dei nomi di Hub eventi.":::

## <a name="configure-export-in-iot-central"></a>Configurare l'esportazione in IoT Central

In questa sezione viene configurata l'applicazione per trasmettere i dati di telemetria dai dispositivi simulati all'hub eventi.

Nel sito [Web Azure IoT Central application manager](https://aka.ms/iotcentral) passare all'applicazione IoT Central creata in precedenza. Per configurare l'esportazione, creare prima una destinazione:

1. Passare alla pagina **Esportazione dati** e quindi selezionare **Destinazioni**.
1. Selezionare **+ Nuova destinazione.**
1. Usare i valori nella tabella seguente per creare una destinazione:

    | Impostazione | Valore |
    | ----- | ----- |
    | Nome destinazione | Hub eventi di telemetria |
    | Tipo destinazione | Hub eventi di Azure |
    | Stringa di connessione | Stringa di connessione dell'hub eventi di cui si è preso nota in precedenza |

    **L'hub eventi** viene visualizzato come **centralexport**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Screenshot che mostra la destinazione di esportazione dati.":::

1. Selezionare **Salva**.

Per creare la definizione di esportazione:

1. Passare alla pagina **Esportazione dati** e selezionare + **Nuova esportazione**.

1. Usare i valori nella tabella seguente per configurare l'esportazione:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome esportazione | Esportazione dell'hub eventi |
    | Abilitato | On |
    | Tipo di dati da esportare | Telemetria |
    | Destinazioni | Selezionare **+ Destinazione,** quindi selezionare Hub **eventi di telemetria** |

1. Selezionare **Salva**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Screenshot che mostra la definizione di esportazione dei dati.":::

Attendere che lo stato dell'esportazione **sia Integro** nella **pagina Esportazione** dati prima di continuare.

## <a name="configure-databricks-workspace"></a>Configurare l'area di lavoro di Databricks

Nel portale di Azure passare al servizio di Azure Databricks e selezionare **Launch Workspace (Avvia area di lavoro).** Nel browser verrà aperta una nuova scheda che consente di accedere all'area di lavoro.

### <a name="create-a-cluster"></a>Creare un cluster

Nella pagina **Azure Databricks,** sotto l'elenco delle attività comuni, selezionare **Nuovo cluster.**

Usare le informazioni nella tabella seguente per creare il cluster:

| Impostazione | Valore |
| ------- | ----- |
| Cluster Name | centralanalysis |
| Modalità cluster | Standard |
| Databricks Runtime versione | 5.5 LTS (Scala 2.11, Spark 2.4.5) |
| Versione di Python | 3 |
| Enable Autoscaling (Abilita la scalabilità automatica) | No |
| Termina dopo minuti di inattività | 30 |
| Tipo di ruolo di lavoro | Standard_DS3_v2 |
| Ruoli di lavoro | 1 |
| Tipo di driver | Uguale al ruolo di lavoro |

La creazione di un cluster può richiedere alcuni minuti. Attendere il completamento della creazione del cluster prima di continuare.

### <a name="install-libraries"></a>Installare le librerie

Nella pagina **Cluster attendere** che lo stato del cluster sia **In esecuzione.**

La procedura seguente illustra come importare nel cluster la libreria di cui l'esempio ha bisogno:

1. Nella pagina **Cluster attendere** che lo stato del cluster **centralanalysis** interactive sia **In esecuzione**.

1. Selezionare il cluster e quindi scegliere la **scheda Librerie.**

1. Nella scheda **Librerie** scegliere **Installa nuovo**.

1. Nella pagina **Installa libreria** scegliere **Maven** come origine della libreria.

1. Nella casella **di testo** Coordinate immettere il valore seguente: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Scegliere **Installa** per installare la libreria nel cluster.

1. Lo stato della libreria è ora **Installato**:

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Screenshot della libreria installata.":::

### <a name="import-a-databricks-notebook"></a>Importare un notebook di Databricks

Usare la procedura seguente per importare un notebook di Databricks che contiene il codice Python per analizzare e visualizzare i dati IoT Central telemetria:

1. Passare alla pagina **Area di** lavoro nell'ambiente Databricks. Selezionare l'elenco a discesa accanto al nome dell'account e quindi scegliere **Importa**.

1. Scegliere di importare da un URL e immettere l'indirizzo seguente: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Per importare il notebook, scegliere **Importa**.

1. Selezionare **l'area di** lavoro per visualizzare il notebook importato:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Screenshot del notebook importato.":::

5. Modificare il codice nella prima cella Python per aggiungere la stringa di connessione di Hub eventi salvata in precedenza:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Eseguire l'analisi

Per eseguire l'analisi, è necessario collegare il notebook al cluster:

1. Selezionare **Scollegato** e quindi selezionare il cluster **centralanalysis.**
1. Se il cluster non è in esecuzione, avviarlo.
1. Per avviare il notebook, selezionare il pulsante Esegui.

È possibile che venga visualizzato un errore nell'ultima cella. In questo caso, controllare che le celle precedenti siano in esecuzione, attendere un minuto che alcuni dati siano scritti nella memoria e quindi eseguire di nuovo l'ultima cella.

### <a name="view-smoothed-data"></a>Visualizzare i dati smussati

Nel notebook scorrere verso il basso fino alla cella 14 per visualizzare un tracciato dell'umidità media mobile in base al tipo di dispositivo. Questo tracciato si aggiorna continuamente all'arrivo dei dati di telemetria di streaming:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Screenshot del tracciato di telemetria smussato.":::

È possibile ridimensionare il grafico nel notebook.

### <a name="view-box-plots"></a>Visualizzare i box plot

Nel notebook scorrere verso il basso fino alla cella 20 per visualizzare i [tracciati del riquadro](https://en.wikipedia.org/wiki/Box_plot). I box plot sono basati su dati statici, quindi per aggiornarli è necessario eseguire di nuovo la cella:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Screenshot dei box plot.":::

È possibile ridimensionare i tracciati nel notebook.

## <a name="tidy-up"></a>Operazioni finali

Per riordinare dopo questa procedura ed evitare costi non necessari, eliminare il gruppo di risorse **IoTCentralAnalysis** nel portale di Azure.

È possibile eliminare l'IoT Central'applicazione dalla **pagina Gestione** all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Trasmettere dati di telemetria da IoT Central'applicazione usando *l'esportazione continua dei dati.*
* Creare un ambiente Azure Databricks per analizzare e tracciare i dati di telemetria.

Ora che si è appreso come creare analisi personalizzate, il passaggio successivo consigliato consiste nell'apprendere come visualizzare e analizzare i dati Azure IoT Central in un [dashboard Power BI .](howto-connect-powerbi.md)
