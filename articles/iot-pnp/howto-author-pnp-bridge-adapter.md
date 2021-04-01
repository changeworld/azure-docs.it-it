---
title: Come creare un adapter per il Bridge Plug and Play Microsoft Docs
description: Identificare i componenti dell'adapter Plug and Play Bridge. Informazioni su come estendere il Bridge scrivendo un adapter personalizzato.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746825"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>Estendi il Bridge Plug and Play
Il [Bridge](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) Internet delle cose plug and Play consente di connettere i dispositivi esistenti collegati a un gateway all'hub Internet. Si usa il Bridge per eseguire il mapping delle interfacce Plug and Play ai dispositivi collegati. Un'interfaccia Plug and Play di Internet delle cose definisce i dati di telemetria inviati da un dispositivo, le proprietà sincronizzate tra il dispositivo e il cloud e i comandi a cui risponde il dispositivo. È possibile installare e configurare l'applicazione Bridge Open Source nei gateway Windows o Linux. Inoltre, il Bridge può essere eseguito come modulo di runtime Azure IoT Edge.

Questo articolo illustra in dettaglio come:

- Estendere il Plug and Play Bridge con una scheda.
- Implementare callback comuni per un adattatore Bridge.

Per un semplice esempio in cui viene illustrato come usare il Bridge, vedere [come connettere l'esempio plug and Play Bridge eseguito in Linux o Windows all'hub](howto-use-iot-pnp-bridge.md)Internet.

Le linee guida e gli esempi in questo articolo presuppongono una certa familiarità con i dispositivi [gemelli digitali](../digital-twins/overview.md) e [plug and Play](overview-iot-plug-and-play.md)di Azure. Inoltre, in questo articolo si presuppone una certa familiarità con [la compilazione e la distribuzione di plug and Play Bridge](howto-build-deploy-extend-pnp-bridge.md).

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>Guida alla progettazione per estendere il Plug and Play Bridge con un adapter

Per estendere le funzionalità del Bridge, è possibile creare schede Bridge personalizzate.

Il Bridge usa gli adapter per:

- Stabilire una connessione tra un dispositivo e il cloud.
- Abilitare il flusso di dati tra un dispositivo e il cloud.
- Abilitare la gestione dei dispositivi dal cloud.

Ogni adattatore del Bridge deve:

- Creare un'interfaccia di dispositivi gemelli digitali.
- Usare l'interfaccia per associare funzionalità lato dispositivo a funzionalità basate su cloud come la telemetria, le proprietà e i comandi.
- Stabilire la comunicazione tra il controllo e i dati con l'hardware o il firmware del dispositivo.

Ogni adattatore Bridge interagisce con un tipo specifico di dispositivo in base al modo in cui l'adapter si connette e interagisce con il dispositivo. Anche se per la comunicazione con un dispositivo viene usato un protocollo di handshake, un adattatore Bridge può avere diversi modi per interpretare i dati dal dispositivo. In questo scenario, l'adattatore Bridge usa le informazioni per l'adapter nel file di configurazione per determinare la *configurazione dell'interfaccia* che l'adapter deve usare per analizzare i dati.

Per interagire con il dispositivo, un adattatore Bridge usa un protocollo di comunicazione supportato dal dispositivo e dalle API fornite dal sistema operativo sottostante o dal fornitore del dispositivo.

Per interagire con il cloud, un adattatore Bridge usa le API fornite da Azure Internet per dispositivi C SDK per inviare dati di telemetria, creare interfacce digitali gemelle, inviare aggiornamenti delle proprietà e creare funzioni di callback per gli aggiornamenti e i comandi delle proprietà.

### <a name="create-a-bridge-adapter"></a>Creare un adattatore Bridge

Il Bridge prevede che un adattatore del Bridge implementi le API definite nell'interfaccia [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) :

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

In questa interfaccia:

- `PNPBRIDGE_ADAPTER_CREATE` Crea l'adapter e configura le risorse di gestione dell'interfaccia. Un adapter può inoltre basarsi sui parametri dell'adapter globale per la creazione dell'adapter. Questa funzione viene chiamata una volta per un singolo adapter.
- `PNPBRIDGE_COMPONENT_CREATE` Crea le interfacce client dei dispositivi gemelli digitali e associa le funzioni di callback. L'adapter avvia il canale di comunicazione al dispositivo. L'adapter può configurare le risorse per abilitare il flusso di dati di telemetria, ma non avvia la creazione di report di telemetria finché non `PNPBRIDGE_COMPONENT_START` viene chiamato. Questa funzione viene chiamata una volta per ogni componente dell'interfaccia nel file di configurazione.
- `PNPBRIDGE_COMPONENT_START` viene chiamato per consentire all'adapter Bridge di avviare l'invio della telemetria dal dispositivo al client gemello digitale. Questa funzione viene chiamata una volta per ogni componente dell'interfaccia nel file di configurazione.
- `PNPBRIDGE_COMPONENT_STOP` arresta il flusso di telemetria.
- `PNPBRIDGE_COMPONENT_DESTROY` Elimina il client gemello digitale e le risorse di interfaccia associate. Questa funzione viene chiamata una volta per ogni componente dell'interfaccia nel file di configurazione quando il Bridge viene eliminato o quando si verifica un errore irreversibile.
- `PNPBRIDGE_ADAPTER_DESTROY` pulisce le risorse dell'adattatore del Bridge.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Interazione dei componenti di base di Bridge con adattatori Bridge

Nell'elenco seguente vengono descritte le operazioni eseguite all'avvio del Bridge:

1. All'avvio del Bridge, Gestione adapter Bridge esamina ogni componente dell'interfaccia definito nel file di configurazione e chiama `PNPBRIDGE_ADAPTER_CREATE` sull'adapter appropriato. L'adapter può usare i parametri di configurazione dell'adapter globale per configurare le risorse per supportare le varie *configurazioni di interfaccia*.
1. Per ogni dispositivo nel file di configurazione, il gestore del Bridge avvia la creazione dell'interfaccia chiamando `PNPBRIDGE_COMPONENT_CREATE` nell'adattatore Bridge appropriato.
1. L'adapter riceve tutte le impostazioni di configurazione facoltative dell'adapter per il componente dell'interfaccia e usa queste informazioni per configurare le connessioni al dispositivo.
1. L'adapter crea le interfacce client dei dispositivi gemelli digitali e associa le funzioni di callback per i comandi e gli aggiornamenti della proprietà. La definizione delle connessioni del dispositivo non deve bloccare il ritorno dei callback dopo che la creazione dell'interfaccia del dispositivo gemello digitale è riuscita. La connessione al dispositivo attivo è indipendente dal client di interfaccia attivo creato dal Bridge. Se una connessione ha esito negativo, l'adapter presuppone che il dispositivo non sia attivo. L'adattatore Bridge può scegliere di riprovare a eseguire la connessione.
1. Dopo la creazione di tutti i componenti di interfaccia specificati nel file di configurazione, il gestore dell'adapter Bridge registra tutte le interfacce con l'hub Azure. La registrazione è una chiamata asincrona bloccante. Al termine della chiamata, viene attivato un callback nell'adattatore Bridge che può quindi iniziare a gestire i callback di proprietà e comandi dal cloud.
1. Bridge Adapter Manager chiama quindi `PNPBRIDGE_INTERFACE_START` su ogni componente e l'adattatore Bridge avvia la segnalazione dei dati di telemetria al client gemello digitale.

### <a name="design-guidelines"></a>Linee guida di progettazione

Quando si sviluppa una nuova scheda Bridge, attenersi alle seguenti linee guida:

- Determinare quali funzionalità del dispositivo sono supportate e l'aspetto della definizione dell'interfaccia dei componenti che usano l'adapter.
- Determinare l'interfaccia e i parametri globali necessari per l'adapter definiti nel file di configurazione.
- Identificare la comunicazione del dispositivo di basso livello necessaria per supportare le proprietà e i comandi dei componenti.
- Determinare il modo in cui l'adapter deve analizzare i dati non elaborati dal dispositivo e convertirli nei tipi di telemetria specificati dalla definizione dell'interfaccia Plug and Play.
- Implementare l'interfaccia dell'adattatore Bridge descritta in precedenza.
- Aggiungere il nuovo adapter al manifesto dell'adapter e compilare il Bridge.

### <a name="enable-a-new-bridge-adapter"></a>Abilita una nuova scheda Bridge

Per abilitare gli adapter nel Bridge, è necessario aggiungere un riferimento in [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> I callback dell'adapter Bridge vengono richiamati in sequenza. Un adapter non deve bloccare un callback perché impedisce lo stato di avanzamento del Bridge core.

### <a name="sample-camera-adapter"></a>Scheda fotocamera di esempio

Il [file Leggimi della fotocamera](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) descrive una scheda di esempio della fotocamera che è possibile abilitare.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Esempi di codice per gli scenari di adapter/callback comuni

Nella sezione seguente vengono fornite informazioni dettagliate sul modo in cui un adattatore per il Bridge implementa i callback per diversi scenari comuni e utilizzi in questa sezione vengono trattati i callback seguenti:
- [Ricezione aggiornamento proprietà (da cloud a dispositivo)](#receive-property-update-cloud-to-device)
- [Segnala un aggiornamento della proprietà (da dispositivo a cloud)](#report-a-property-update-device-to-cloud)
- [Invia dati di telemetria (da dispositivo a cloud)](#send-telemetry-device-to-cloud)
- [Ricevere il callback di aggiornamento del comando dal cloud ed elaborarlo sul lato dispositivo (da cloud a dispositivo)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Rispondere all'aggiornamento del comando sul lato dispositivo (da dispositivo a cloud)](#respond-to-command-update-on-the-device-side-device-to-cloud)

Gli esempi seguenti si basano sull' [adattatore di esempio del sensore ambientale](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor).

### <a name="receive-property-update-cloud-to-device"></a>Ricezione aggiornamento proprietà (da cloud a dispositivo)
Il primo passaggio consiste nel registrare una funzione di callback:

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
Il passaggio successivo consiste nell'implementare la funzione di callback per leggere l'aggiornamento della proprietà nel dispositivo:

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Segnala un aggiornamento della proprietà (da dispositivo a cloud)
In qualsiasi momento dopo la creazione del componente, il dispositivo può segnalare le proprietà al cloud con lo stato: 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Invia dati di telemetria (da dispositivo a cloud)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Ricevere il callback di aggiornamento del comando dal cloud ed elaborarlo sul lato dispositivo (da cloud a dispositivo)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Rispondere all'aggiornamento del comando sul lato dispositivo (da dispositivo a cloud)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul Bridge Plug and Play, visitare il repository GitHub [plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) .
