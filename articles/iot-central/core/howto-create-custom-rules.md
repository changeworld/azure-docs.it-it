---
title: Estendere IoT Central di Azure con regole e notifiche personalizzate | Microsoft Docs
description: Per gli sviluppatori di soluzioni, configurare un'applicazione IoT Central per inviare notifiche tramite posta elettronica quando un dispositivo smette di inviare dati di telemetria. Questa soluzione USA analisi di flusso di Azure, funzioni di Azure e SendGrid.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 6146676121bac0089d5f520d60a97d74567a32bc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179341"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Estendere Azure IoT Central con regole personalizzate usando Analisi di flusso, Funzioni di Azure e SendGrid

Questa guida illustra come uno sviluppatore di soluzioni, come estendere l'applicazione IoT Central con regole e notifiche personalizzate. Nell'esempio viene illustrato l'invio di una notifica a un operatore quando un dispositivo interrompe l'invio di dati di telemetria. La soluzione USA una query di [analisi di flusso di Azure](../../stream-analytics/index.yml) per rilevare quando un dispositivo ha interrotto l'invio della telemetria. Il processo di analisi di flusso usa [funzioni di Azure](../../azure-functions/index.yml) per inviare messaggi di posta elettronica di notifica usando [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Questa guida dettagliata illustra come estendere IoT Central oltre quello che può già fare con le regole e le azioni predefinite.

In questa guida dettagliata si apprenderà come:

* Trasmettere i dati di telemetria da un'applicazione IoT Central usando l' *esportazione continua dei dati*.
* Creare una query di analisi di flusso che rilevi quando un dispositivo ha interrotto l'invio dei dati.
* Inviare una notifica tramite posta elettronica usando funzioni di Azure e i servizi SendGrid.

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

Usare il [portale di Azure per creare un gruppo di risorse](https://portal.azure.com/#create/Microsoft.ResourceGroup) denominato **DetectStoppedDevices** per contenere le altre risorse create. Creare le risorse di Azure nello stesso percorso dell'applicazione IoT Central.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare il [portale di Azure per creare uno spazio dei nomi di hub eventi](https://portal.azure.com/#create/Microsoft.EventHub) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Subscription | Sottoscrizione in uso |
| Resource group | DetectStoppedDevices |
| Località | Stati Uniti orientali |
| Unità elaborate | 1 |

### <a name="stream-analytics-job"></a>Processo di Analisi di flusso

Usare il [portale di Azure per creare un processo di analisi di flusso](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome    | Scegliere il nome del processo |
| Subscription | Sottoscrizione in uso |
| Resource group | DetectStoppedDevices |
| Località | Stati Uniti orientali |
| Ambiente di hosting | Cloud |
| Unità di streaming | 3 |

### <a name="function-app"></a>App per le funzioni

Usare il [portale di Azure per creare un'app per le funzioni](https://portal.azure.com/#create/Microsoft.FunctionApp) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome app    | Scegliere il nome dell'app per le funzioni |
| Subscription | Sottoscrizione in uso |
| Resource group | DetectStoppedDevices |
| Sistema operativo | Windows |
| Piano di hosting | Piano a consumo |
| Località | Stati Uniti orientali |
| Stack di runtime | .NET |
| Archiviazione | Creare un nuovo gruppo di risorse |

### <a name="sendgrid-account-and-api-keys"></a>Chiavi API e account SendGrid

Se non si ha un account SendGrid, creare un [account gratuito](https://app.sendgrid.com/) prima di iniziare.

1. Dalle impostazioni del dashboard di SendGrid nel menu a sinistra selezionare **chiavi API**.
1. Fare clic su **Crea chiave API.**
1. Denominare la nuova chiave API **AzureFunctionAccess.**
1. Fare clic su **crea & visualizzazione**.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Screenshot della chiave API Create SendGrid.":::

Successivamente, verrà fornita una chiave API. Salvare questa stringa per un uso successivo.

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente i dati di telemetria in un hub eventi. In questa sezione viene creato un hub eventi per ricevere i dati di telemetria dall'applicazione IoT Central. L'hub eventi recapita i dati di telemetria al processo di analisi di flusso per l'elaborazione.

1. Nella portale di Azure passare allo spazio dei nomi di hub eventi e selezionare **+ Hub eventi**.
1. Denominare il **centralexport** dell'hub eventi e selezionare **Crea**.

Lo spazio dei nomi di hub eventi è simile allo screenshot seguente: 

:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot dello spazio dei nomi di hub eventi." border="false":::


## <a name="define-the-function"></a>Definire la funzione

Questa soluzione usa un'app funzioni di Azure per inviare una notifica di posta elettronica quando il processo di analisi di flusso rileva un dispositivo interrotto. Per creare l'app per le funzioni:

1. Nella portale di Azure passare all'istanza del **servizio app** nel gruppo di risorse **DetectStoppedDevices** .
1. Selezionare **+** per creare una nuova funzione.
1. Selezionare **trigger http**.
1. Selezionare **Aggiungi**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Immagine della funzione trigger HTTP predefinita"::: 

## <a name="edit-code-for-http-trigger"></a>Modificare il codice per il trigger HTTP

Il portale crea una funzione predefinita denominata **HttpTrigger1**:

:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot della funzione di modifica trigger HTTP.":::


1. Sostituire il codice C# con il codice seguente:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);  

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    È possibile che venga visualizzato un messaggio di errore fino a quando non si salva il nuovo codice.
1. Selezionare **Save (Salva** ) per salvare la funzione.

## <a name="add-sendgrid-key"></a>Aggiungi chiave di SendGrid

Per aggiungere la chiave API SendGrid, è necessario aggiungerla ai **tasti funzione** come indicato di seguito:

1. Selezionare i **tasti funzione**.
1. Scegliere **+ nuovo tasto funzione**.
1. Immettere il *nome* e il *valore* della chiave API creata in precedenza.
1. Scegliere **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot della chiave add Sangrid.":::


## <a name="configure-httptrigger-function-to-use-sendgrid"></a>Configurare la funzione HttpTrigger per l'uso di SendGrid

Per inviare messaggi di posta elettronica con SendGrid, è necessario configurare i binding per la funzione nel modo seguente:

1. Selezionare **Integrazione**.
1. Scegliere **Aggiungi output** in **http ($Return)**.
1. Selezionare **Elimina.**
1. Scegliere **+ nuovo output**.
1. Per tipo di binding, scegliere **SendGrid**.
1. Per tipo di impostazione della chiave API SendGrid, fare clic su nuovo.
1. Immettere il *nome* e il *valore* della chiave API SendGrid.
1. Aggiungere le informazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome del parametro del messaggio | Scegliere il nome |
| Indirizzo | Scegliere il nome dell'indirizzo |
| Indirizzo del mittente. | Scegliere il nome dell'indirizzo da |
| Oggetto del messaggio | Immettere l'intestazione dell'oggetto |
| Testo del messaggio | Immettere il messaggio dall'integrazione |

1. Selezionare **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot dell'aggiunta dell'output SandGrid.":::


### <a name="test-the-function-works"></a>Testare la funzione funziona

Per testare la funzione nel portale, scegliere innanzitutto **log** nella parte inferiore dell'editor di codice. Quindi scegliere **test** a destra dell'editor di codice. Usare il codice JSON seguente come **corpo della richiesta**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

I messaggi del log delle funzioni vengono visualizzati nel pannello **logs** :

:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Output del log delle funzioni":::

Dopo alcuni minuti, l'indirizzo **di** posta elettronica a riceve un messaggio di posta elettronica con il contenuto seguente:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Aggiungere una query di analisi di flusso

Questa soluzione USA una query di analisi di flusso per rilevare quando un dispositivo smette di inviare dati di telemetria per più di 120 secondi. La query usa i dati di telemetria dell'hub eventi come input. Il processo invia i risultati della query all'app per le funzioni. In questa sezione si configura il processo di analisi di flusso:

1. Nel portale di Azure passare al processo di analisi di flusso, in **topologia processi** selezionare **input**, scegliere **+ Aggiungi input flusso**, quindi scegliere **Hub eventi**.
1. Usare le informazioni nella tabella seguente per configurare l'input usando l'hub eventi creato in precedenza, quindi scegliere **Salva**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Alias di input | centraltelemetry |
    | Subscription | Sottoscrizione in uso |
    | Spazio dei nomi dell'hub eventi | Spazio dei nomi dell'hub eventi |
    | Nome dell'hub eventi | USA **centralexport** esistente |

1. In **topologia processi** selezionare **output**, fare clic su **+ Aggiungi**, quindi scegliere **funzione di Azure**.
1. Usare le informazioni nella tabella seguente per configurare l'output, quindi scegliere **Salva**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Alias di output | emailnotification |
    | Subscription | Sottoscrizione in uso |
    | App per le funzioni | App per le funzioni |
    | Funzione  | HttpTrigger1 |

1. In **topologia processi** selezionare **query** e sostituire la query esistente con la seguente SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Selezionare **Salva**.
1. Per avviare il processo di analisi di flusso, scegliere **Panoramica**, **Avvia**, quindi **ora**, quindi **Avvia**:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Screenshot di analisi di flusso.":::

## <a name="configure-export-in-iot-central"></a>Configurare l'esportazione in IoT Central 

Nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) passare all'applicazione IoT Central creata.

In questa sezione l'applicazione viene configurata in modo da trasmettere i dati di telemetria dai dispositivi simulati all'hub eventi. Per configurare l'esportazione:

1. Passare alla pagina **esportazione dati** , selezionare **+ nuovo**, quindi **Hub eventi di Azure**.
1. Usare le impostazioni seguenti per configurare l'esportazione, quindi selezionare **Salva**: 

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome visualizzato | Esporta in hub eventi |
    | Abilitato | On |
    | Tipo di dati da esportare | Telemetria |
    | Arricchimenti | Immettere la chiave e il valore desiderati per organizzare i dati esportati | 
    | Destination | Crea nuovo e immettere le informazioni per la posizione in cui verranno esportati i dati |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Screenshot dell'esportazione dei dati.":::

Prima di continuare, attendere che lo stato di esportazione sia **in esecuzione** .

## <a name="test"></a>Test

Per testare la soluzione, è possibile disabilitare l'esportazione dei dati continui da IoT Central a dispositivi arrestati simulati:

1. Nell'applicazione IoT Central passare alla pagina **esportazione dati** e selezionare la configurazione Esporta **in hub eventi** .
1. Impostare **abilitato** su **disattivato** e scegliere **Salva**.
1. Dopo almeno due minuti, l'indirizzo **di** posta elettronica a riceve uno o più messaggi di posta elettronica simili al contenuto di esempio seguente:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Operazioni finali

Per riordinare dopo questa procedura ed evitare i costi non necessari, eliminare il gruppo di risorse **DetectStoppedDevices** nel portale di Azure.

È possibile eliminare l'applicazione IoT Central dalla pagina di **gestione** all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Trasmettere i dati di telemetria da un'applicazione IoT Central usando l' *esportazione continua dei dati*.
* Creare una query di analisi di flusso che rilevi quando un dispositivo ha interrotto l'invio dei dati.
* Inviare una notifica tramite posta elettronica usando funzioni di Azure e i servizi SendGrid.

Ora che si è appreso come creare regole e notifiche personalizzate, il passaggio successivo suggerito consiste nell'apprendere come [estendere IOT Central di Azure con l'analisi personalizzata](howto-create-custom-analytics.md).
