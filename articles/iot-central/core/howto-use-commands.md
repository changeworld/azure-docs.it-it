---
title: Come usare i comandi del dispositivo in una soluzione IoT Central di Azure
description: Come usare i comandi del dispositivo in Azure IoT Central soluzione. Questa esercitazione illustra come uno sviluppatore di dispositivi deve usare i comandi del dispositivo nell'app client nell'applicazione IoT Central di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: dd05a4880bdf077f63dc58575828e6180e415260
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122356"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Come usare i comandi in una soluzione IoT Central di Azure

Questa guida illustra come gli sviluppatori di dispositivi possono usare i comandi definiti in un modello di dispositivo.

Un operatore può usare l'interfaccia utente di IoT Central per chiamare un comando in un dispositivo. I comandi controllano il comportamento di un dispositivo. Ad esempio, è possibile che un operatore chiami un comando per riavviare un dispositivo o raccogliere i dati di diagnostica.

Un dispositivo può:

* Rispondere immediatamente a un comando.
* Rispondere a IoT Central quando riceve il comando e successivamente invia una notifica IoT Central quando il *comando con esecuzione prolungata* è completo.

Per impostazione predefinita, i comandi prevedono la connessione di un dispositivo e hanno esito negativo se non è possibile raggiungere il dispositivo. Se si seleziona l'opzione **queue if offline** nell'interfaccia utente del modello di dispositivo, è possibile accodare un comando finché un dispositivo non è online. Questi *comandi offline* sono descritti in una sezione separata più avanti in questo articolo.

## <a name="define-your-commands"></a>Definire i comandi

I comandi standard vengono inviati a un dispositivo per indicare al dispositivo di eseguire un'operazione. Un comando può includere parametri con informazioni aggiuntive. Ad esempio, un comando per aprire una valvola in un dispositivo potrebbe avere un parametro che specifica la quantità di apertura della valvola. I comandi possono anche ricevere un valore restituito quando il dispositivo completa il comando. Ad esempio, un comando che chiede a un dispositivo di eseguire alcuni dati di diagnostica potrebbe ricevere un report di diagnostica come valore restituito.

I comandi sono definiti come parte di un modello di dispositivo. Lo screenshot seguente illustra la definizione di comando **Get Max-Min report** nel modello di dispositivo **termostato** . Questo comando ha parametri di richiesta e risposta: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Screenshot che mostra il comando Ottieni max min report nel modello di dispositivo termostato":::

La tabella seguente illustra le impostazioni di configurazione per una funzionalità di comando:

| Campo             |Descrizione|
|-------------------|-----------|
|Nome visualizzato       |Valore del comando usato nei dashboard e nei form.|
| Nome            | Nome del comando. IoT Central genera un valore per questo campo dal nome visualizzato, ma è possibile scegliere un valore personalizzato, se necessario. Questo campo deve essere alfanumerico. Il codice del dispositivo usa questo valore **nome** .|
| Tipo di funzionalità | Comando.|
| Accoda se non in linea | Indica se rendere questo comando un comando *offline* . |
| Descrizione     | Descrizione della funzionalità del comando.|
| Comment     | Eventuali commenti sulla funzionalità del comando.|
| Richiesta     | Payload per il comando del dispositivo.|
| Risposta     | Payload della risposta del comando del dispositivo.|

Il frammento di codice seguente mostra la rappresentazione JSON del comando nel modello di dispositivo. In questo esempio il valore della risposta è un tipo di **oggetto** complesso con più campi:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> È possibile esportare un modello di dispositivo dalla pagina modello di dispositivo.

È possibile correlare questa definizione di comando allo screenshot dell'interfaccia utente usando i campi seguenti:

* `@type` per specificare il tipo di funzionalità: `Command`
* `name` per il valore del comando.

I campi facoltativi, ad esempio nome visualizzato e descrizione, consentono di aggiungere ulteriori dettagli all'interfaccia e alle funzionalità.

## <a name="standard-commands"></a>Comandi standard

Questa sezione illustra il modo in cui un dispositivo invia un valore di risposta non appena riceve il comando.

Il frammento di codice seguente mostra come un dispositivo può rispondere a un comando che invia immediatamente un codice di esito positivo:

> [!NOTE]
> Questo articolo usa Node.js per semplicità. Per altri esempi di linguaggio, vedere l'esercitazione [creare e connettere un'applicazione client all'applicazione Azure IOT Central](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

La chiamata a `onDeviceMethod` imposta il `commandHandler` metodo. Questo gestore comando:

1. Verifica il nome del comando.
1. Per il `getMaxMinReport` comando chiama `getMaxMinReportObject` per recuperare i valori da includere nell'oggetto restituito.
1. Chiama `sendCommandResponse` per restituire la risposta a IOT Central. Questa risposta include il `200` codice di risposta per indicare l'esito positivo.

Lo screenshot seguente mostra come viene visualizzata la risposta del comando corretta nell'interfaccia utente di IoT Central:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Screenshot che illustra come visualizzare il payload del comando per un comando standard":::

## <a name="long-running-commands"></a>Comandi con esecuzione prolungata

Questa sezione illustra il modo in cui un dispositivo può ritardare l'invio di una conferma della concorrenza del comando.

Il frammento di codice seguente mostra come un dispositivo può implementare un comando con esecuzione prolungata:

> [!NOTE]
> Questo articolo usa Node.js per semplicità. Per altri esempi di linguaggio, vedere l'esercitazione [creare e connettere un'applicazione client all'applicazione Azure IOT Central](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

La chiamata a `onDeviceMethod` imposta il `commandHandler` metodo. Questo gestore comando:

1. Verifica il nome del comando.
1. Chiama `sendCommandResponse` per restituire la risposta a IOT Central. Questa risposta include il `202` codice di risposta per indicare i risultati in sospeso.
1. Completa l'operazione a esecuzione prolungata.
1. Usa una proprietà segnalata con lo stesso nome del comando per indicare IoT Central il completamento del comando.

Lo screenshot seguente mostra come viene visualizzata la risposta del comando nell'interfaccia utente di IoT Central quando riceve il codice di risposta 202:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Screenshot che mostra la risposta immediata dal dispositivo":::

Lo screenshot seguente mostra l'interfaccia utente di IoT Central quando riceve l'aggiornamento della proprietà che indica che il comando è stato completato:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Screenshot che mostra il comando con esecuzione prolungata completato":::

## <a name="offline-commands"></a>Comandi offline

Questa sezione illustra il modo in cui un dispositivo gestisce un comando offline. Se un dispositivo è online, può gestire il comando offline non appena viene ricevuto. Se un dispositivo è offline, gestisce il comando offline quando si connette successivamente a IoT Central. I dispositivi non possono inviare un valore restituito in risposta a un comando offline.

Il frammento di codice seguente mostra come un dispositivo può implementare un comando offline:

> [!NOTE]
> Questo articolo usa Node.js per semplicità. Per altri esempi di linguaggio, vedere l'esercitazione [creare e connettere un'applicazione client all'applicazione Azure IOT Central](tutorial-connect-device.md) .

La schermata seguente mostra un comando offline chiamato **GenerateDiagnostics**. Il parametro request è un oggetto con una proprietà DateTime denominata **StartTime** e una proprietà di enumerazione Integer denominata **Bank**:

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Screenshot che mostra l'interfaccia utente per un comando offline":::

Il frammento di codice seguente mostra in che modo un client può rimanere in ascolto dei comandi offline e visualizzare il contenuto del messaggio:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

L'output del frammento di codice precedente Mostra il payload con i valori **StartTime** e **Bank** . L'elenco di proprietà include il nome del comando nell'elemento dell'elenco **nome metodo** :

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i comandi nell'applicazione IoT Central di Azure, vedere [payload](concepts-telemetry-properties-commands.md) per ulteriori informazioni sui parametri del comando e [creare e connettere un'applicazione client all'applicazione IoT Central di Azure](tutorial-connect-device.md) per visualizzare esempi di codice completi in linguaggi diversi.
