---
title: Estendere Azure IoT Central con regole personalizzate e notifiche | Microsoft Docs
description: Gli sviluppatori di soluzioni possono configurare un'applicazione IoT Central per l'invio di notifiche tramite posta elettronica quando un dispositivo smette di inviare dati di telemetria. Questa soluzione usa Analisi di flusso di Azure, Funzioni di Azure e SendGrid.
author: philmea
ms.author: philmea
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: a65d9dbaed4d197c2e0843e73ff3f45b8678017e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864218"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Estendere Azure IoT Central con regole personalizzate usando Analisi di flusso, Funzioni di Azure e SendGrid

Questa guida dettagliata illustra agli sviluppatori di soluzioni come estendere l'applicazione IoT Central con regole e notifiche personalizzate. L'esempio mostra l'invio di una notifica a un operatore quando un dispositivo smette di inviare dati di telemetria. La soluzione usa una query [Analisi di flusso di Azure](../../stream-analytics/index.yml) per rilevare quando un dispositivo ha smesso di inviare dati di telemetria. Il processo di Analisi di flusso usa [Funzioni di Azure](../../azure-functions/index.yml) per inviare messaggi di posta elettronica di notifica [tramite SendGrid.](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)

Questa guida dettagliata illustra come estendere le IoT Central oltre a ciò che può già fare con le regole e le azioni predefinite.

Questa guida dettagliata illustra come:

* Trasmettere dati di telemetria da IoT Central'applicazione usando *l'esportazione continua dei dati.*
* Creare una query di Analisi di flusso che rilevi quando un dispositivo ha smesso di inviare dati.
* Inviare una notifica tramite posta elettronica usando Funzioni di Azure e SendGrid.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="iot-central-application"></a>Applicazione IoT Central

Creare un IoT Central app applicazione nel sito [Web Azure IoT Central application manager](https://aka.ms/iotcentral) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Piano tariffario | Standard |
| Modello di applicazione | Analisi dei negozi: monitoraggio delle condizioni |
| Nome applicazione | Accettare l'impostazione predefinita o scegliere un nome personalizzato |
| URL | Accettare il valore predefinito o scegliere un prefisso URL univoco personalizzato |
| Directory | Tenant Azure Active Directory |
| Sottoscrizione di Azure | Sottoscrizione di Azure |
| Region | Area più vicina |

Gli esempi e gli screenshot in questo articolo usano **l'Stati Uniti** geografica. Scegliere una località vicina e assicurarsi di creare tutte le risorse nella stessa area.

Questo modello di applicazione include due dispositivi termostati simulati che inviano dati di telemetria.

### <a name="resource-group"></a>Resource group

Usare il [portale di Azure per creare un gruppo](https://portal.azure.com/#create/Microsoft.ResourceGroup) di risorse denominato **DetectStoppedDevices** che contenga le altre risorse create. Creare le risorse di Azure nella stessa posizione dell'applicazione IoT Central servizio.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare il [portale di Azure per creare uno spazio dei nomi di Hub eventi](https://portal.azure.com/#create/Microsoft.EventHub) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Subscription | Sottoscrizione in uso |
| Resource group | DetectStoppedDevices |
| Località | Stati Uniti orientali |
| Unità elaborate | 1 |

### <a name="stream-analytics-job"></a>Processo di Analisi di flusso

Usare il [portale di Azure per creare un processo di Analisi di flusso](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  con le impostazioni seguenti:

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

### <a name="sendgrid-account-and-api-keys"></a>Account SendGrid e chiavi API

Se non si ha un account Sendgrid, creare un [account gratuito](https://app.sendgrid.com/) prima di iniziare.

1. In Impostazioni dashboard Sendgrid nel menu a sinistra selezionare **Chiavi API.**
1. Fare clic **su Crea chiave API.**
1. Assegnare alla nuova chiave API il **nome AzureFunctionAccess.**
1. Fare **clic su Crea & visualizzazione**.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Screenshot della chiave API Create SendGrid.":::

Successivamente, verrà data una chiave API. Salvare questa stringa per usarla in un secondo momento.

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente i dati di telemetria in un hub eventi. In questa sezione viene creato un hub eventi per ricevere i dati di telemetria dall IoT Central app. L'hub eventi recapita i dati di telemetria al processo di Analisi di flusso per l'elaborazione.

1. Nell'portale di Azure passare allo spazio dei nomi di Hub eventi e selezionare **+ Hub eventi.**
1. Assegnare all'hub eventi il nome **centralexport** e selezionare **Crea**.

Lo spazio dei nomi di Hub eventi è simile allo screenshot seguente: 

```:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## Define the function

This solution uses an Azure Functions app to send an email notification when the Stream Analytics job detects a stopped device. To create your function app:

1. In the Azure portal, navigate to the **App Service** instance in the **DetectStoppedDevices** resource group.
1. Select **+** to create a new function.
1. Select **HTTP Trigger**.
1. Select **Add**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Image of the Default HTTP trigger function"::: 

## Edit code for HTTP Trigger

The portal creates a default function called **HttpTrigger1**:

```:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Replace the C# code with the following code:

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

    You may see an error message until you save the new code.
1. Select **Save** to save the function.

## Add SendGrid Key

To add your SendGrid API Key, you need to add it to your **Function Keys** as follows:

1. Select **Function Keys**.
1. Choose **+ New Function Key**.
1. Enter the *Name* and *Value* of the API Key you created before.
1. Click **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot of Add Sangrid Key.":::


## Configure HttpTrigger function to use SendGrid

To send emails with SendGrid, you need to configure the bindings for your function as follows:

1. Select **Integrate**.
1. Choose **Add Output** under **HTTP ($return)**.
1. Select **Delete.**
1. Choose **+ New Output**.
1. For Binding Type, then choose **SendGrid**.
1. For SendGrid API Key Setting Type, click New.
1. Enter the *Name* and *Value* of your SendGrid API key.
1. Add the following information:

| Setting | Value |
| ------- | ----- |
| Message parameter name | Choose your name |
| To address | Choose the name of your To Address |
| From address | Choose the name of your From Address |
| Message subject | Enter your subject header |
| Message text | Enter the message from your integration |

1. Select **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot of Add SandGrid Output.":::


### Test the function works

To test the function in the portal, first choose **Logs** at the bottom of the code editor. Then choose **Test** to the right of the code editor. Use the following JSON as the **Request body**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

I messaggi del log delle funzioni vengono visualizzati **nel pannello** Log:

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Aggiungere una query di Analisi di flusso

Questa soluzione usa una query di Analisi di flusso per rilevare quando un dispositivo smette di inviare dati di telemetria per più di 120 secondi. La query usa i dati di telemetria dell'hub eventi come input. Il processo invia i risultati della query all'app per le funzioni. In questa sezione viene configurato il processo di Analisi di flusso:

1. Nel portale di Azure passare al processo di Analisi  di flusso, in Topologia processi selezionare **Input,** **scegliere + Aggiungi input** flusso e quindi Hub **eventi.**
1. Usare le informazioni nella tabella seguente per configurare l'input usando l'hub eventi creato in precedenza, quindi scegliere **Salva**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Alias di input | centraltelemetry |
    | Subscription | Sottoscrizione in uso |
    | Spazio dei nomi dell'hub eventi | Spazio dei nomi dell'hub eventi |
    | Nome dell'hub eventi | Usa esistente - **centralexport** |

1. In **Topologia processi** selezionare **Output,** scegliere **+ Aggiungi** e quindi Funzione **di Azure.**
1. Usare le informazioni nella tabella seguente per configurare l'output, quindi scegliere **Salva**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Alias di output | emailnotification |
    | Subscription | Sottoscrizione in uso |
    | App per le funzioni | App per le funzioni |
    | Funzione  | HttpTrigger1 |

1. In **Topologia processi** selezionare **Query** e sostituire la query esistente con il codice SQL seguente:

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
1. Per avviare il processo di Analisi di flusso, scegliere **Panoramica,** **quindi Avvia**, **ora** e infine **Avvia**:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Screenshot di Analisi di flusso.":::

## <a name="configure-export-in-iot-central"></a>Configurare l'esportazione in IoT Central 

Nel sito [Web Azure IoT Central application manager](https://aka.ms/iotcentral) passare alla IoT Central'applicazione creata.

In questa sezione viene configurata l'applicazione per trasmettere i dati di telemetria dai dispositivi simulati all'hub eventi. Per configurare l'esportazione:

1. Passare alla pagina **Esportazione dati,** selezionare **+ Nuovo** e quindi **Hub eventi di Azure**.
1. Usare le impostazioni seguenti per configurare l'esportazione, quindi selezionare **Salva**: 

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome visualizzato | Esportare in Hub eventi |
    | Abilitato | On |
    | Tipo di dati da esportare | Telemetria |
    | Arricchimenti | Immettere la chiave o il valore desiderato per l'organizzazione dei dati esportati | 
    | Destination | Creare nuovo e immettere le informazioni per la posizione in cui verranno esportati i dati |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Screenshot dell'esportazione dati.":::

Attendere che lo stato dell'esportazione **sia In esecuzione** prima di continuare.

## <a name="test"></a>Test

Per testare la soluzione, è possibile disabilitare l'esportazione continua dei dati IoT Central ai dispositivi arrestati simulati:

1. Nell'applicazione IoT Central passare alla pagina **Esportazione** dati e selezionare la configurazione Esporta in Hub eventi per **l'esportazione.**
1. Impostare **Abilitato** su **Disattivato e** scegliere **Salva.**
1. Dopo almeno due minuti, **l'indirizzo di** posta elettronica A riceve uno o più messaggi di posta elettronica simili al contenuto di esempio seguente:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Operazioni finali

Per riordinare dopo questa procedura ed evitare costi non necessari, eliminare il gruppo di risorse **DetectStoppedDevices** nel portale di Azure.

È possibile eliminare l'IoT Central'applicazione dalla **pagina Gestione** all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Trasmettere dati di telemetria da IoT Central'applicazione usando *l'esportazione continua dei dati.*
* Creare una query di Analisi di flusso che rilevi quando un dispositivo ha smesso di inviare dati.
* Inviare una notifica tramite posta elettronica usando Funzioni di Azure e SendGrid.

Ora che si è appreso come creare regole e notifiche personalizzate, il passaggio successivo consigliato consiste nell'apprendere come estendere le Azure IoT Central [con l'analisi personalizzata.](howto-create-custom-analytics.md)
