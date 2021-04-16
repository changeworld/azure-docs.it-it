---
title: Monitorare i moduli gemelli - Azure IoT Edge
description: Come interpretare i dispositivi gemelli e i moduli gemelli per determinare la connettività e l'integrità.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5a31e15c88cef588c93f44c8fe5303d930b5b2c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479373"
---
# <a name="monitor-module-twins"></a>Monitorare i moduli gemelli

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

I moduli gemelli in hub IoT di Azure il monitoraggio della connettività e dell'integrità delle IoT Edge distribuzione. I moduli gemelli archiviano informazioni utili nell'hub IoT sulle prestazioni dei moduli in esecuzione. [L'IoT Edge e](iot-edge-runtime.md#iot-edge-agent) i moduli di runtime [dell'hub](iot-edge-runtime.md#iot-edge-hub) IoT Edge mantengono rispettivamente i moduli gemelli e `$edgeAgent` , `$edgeHub` rispettivamente:

* `$edgeAgent` contiene i dati di integrità e connettività relativi all'agente IoT Edge e IoT Edge di runtime dell'hub e ai moduli personalizzati. L IoT Edge agent è responsabile della distribuzione dei moduli, del monitoraggio e della segnalazione dello stato della connessione all'hub Azure IoT dati.
* `$edgeHub` contiene dati sulle comunicazioni tra l'hub IoT Edge in esecuzione in un dispositivo e l'hub Azure IoT dispositivo. Ciò include l'elaborazione dei messaggi in ingresso dai dispositivi downstream. IoT Edge hub è responsabile dell'elaborazione delle comunicazioni tra hub IoT di Azure e IoT Edge dispositivi e moduli.

I dati sono organizzati in metadati, tag, insieme ai set di proprietà desiderati e segnalati nelle strutture JSON dei moduli gemelli. Le proprietà desiderate specificate nel deployment.jsfile vengono copiate nei moduli gemelli. L IoT Edge e l'hub IoT Edge aggiornare le proprietà segnalate per i moduli.

Analogamente, le proprietà desiderate specificate per i moduli personalizzati nel deployment.jsnel file vengono copiate nel modulo gemello, ma la soluzione è responsabile di fornire i valori delle proprietà segnalate.

Questo articolo descrive come esaminare i moduli gemelli nel portale di Azure, nell'interfaccia della riga di comando di Azure e in Visual Studio Code. Per informazioni sul monitoraggio del modo in cui i dispositivi ricevono le distribuzioni, vedere [Monitorare IoT Edge distribuzioni](how-to-monitor-iot-edge-deployments.md). Per una panoramica sul concetto di moduli gemelli, vedere Comprendere e usare i moduli gemelli [nell'hub IoT.](../iot-hub/iot-hub-devguide-module-twins.md)

> [!TIP]
> Le proprietà segnalate di un modulo di runtime potrebbero non essere più disponibili se IoT Edge dispositivo viene disconnesso dall'hub IoT. È possibile [effettuare il ping](how-to-edgeagent-direct-method.md#ping) del modulo per `$edgeAgent` determinare se la connessione è stata persa.

## <a name="monitor-runtime-module-twins"></a>Monitorare i moduli gemelli di runtime

Per risolvere i problemi di connettività della distribuzione, esaminare l'agente di IoT Edge e i moduli gemelli di runtime dell IoT Edge hub e quindi eseguire il drill-down in altri moduli.

### <a name="monitor-iot-edge-agent-module-twin"></a>Monitorare il IoT Edge modulo gemello dell'agente

Il codice JSON seguente mostra il `$edgeAgent` modulo gemello in Visual Studio Code con la maggior parte delle sezioni JSON compresse.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

Il codice JSON può essere descritto nelle sezioni seguenti, a partire dall'alto:

* Metadati: contiene i dati di connettività. È interessante notare che lo stato di connessione per IoT Edge agente è sempre disconnesso: `"connectionState": "Disconnected"` . Il motivo per cui lo stato della connessione riguarda i messaggi da dispositivo a cloud (D2C) e l'agente IoT Edge non invia messaggi D2C.
* Proprietà: contiene le `desired` `reported` sottosezioni e .
* Properties.desired: (mostrata compressa) Valori di proprietà previsti impostati dall'operatore nella deployment.jsnel file.
* Properties.reported: i valori più recenti delle proprietà segnalati IoT Edge'agente.

Entrambe le sezioni e hanno una struttura simile e contengono metadati aggiuntivi per le informazioni su `properties.desired` `properties.reported` schema, versione e runtime. È inclusa anche la sezione per tutti i moduli personalizzati , ad esempio `modules` , e la sezione per i moduli di runtime e `SimulatedTemperatureSensor` `systemModules` `$edgeAgent` `$edgeHub` .

Confrontando i valori delle proprietà segnalate con i valori desiderati, è possibile determinare le discrepanze e identificare le disconnessioni che consentono di risolvere i problemi. In questi confronti, controllare il `$lastUpdated` valore segnalato nella sezione per la proprietà che si sta `metadata` analizzando.

Le proprietà seguenti sono importanti da esaminare per la risoluzione dei problemi:

* **exitcode:** qualsiasi valore diverso da zero indica che il modulo è stato arrestato con un errore. Tuttavia, i codici di errore 137 o 143 vengono usati se un modulo è stato intenzionalmente impostato su uno stato arrestato.

* **lastStartTimeUtc:** mostra il **valore DateTime dell'ultimo** avvio del contenitore. Questo valore è 0001-01-01T00:00:00Z se il contenitore non è stato avviato.

* **lastExitTimeUtc:** mostra il **valore DateTime dell'ultima** volta che il contenitore è stato completato. Questo valore è 0001-01-01T00:00:00Z se il contenitore è in esecuzione e non è mai stato arrestato.

* **runtimeStatus:** può essere uno dei valori seguenti:

    | Valore | Descrizione |
    | --- | --- |
    | unknown | Stato predefinito fino alla creazione della distribuzione. |
    | backoff | Il modulo è pianificato per l'avvio, ma non è attualmente in esecuzione. Questo valore è utile per un modulo sottoposto a modifiche di stato durante il riavvio. Quando un modulo in errore è in attesa del riavvio durante il periodo di raffreddamento, il modulo sarà in uno stato di backoff. |
    | in esecuzione | Indica che il modulo è attualmente in esecuzione. |
    | Malsano | Indica che un controllo del probe di integrità non è riuscito o si è verificata una timeout. |
    | Arrestato | Indica che il modulo è stato chiuso correttamente (con un codice di uscita pari a zero). |
    | non riuscita | Indica che il modulo è stato chiuso con un codice di uscita dell'errore (diverso da zero). Il modulo può tornare al backoff da questo stato a seconda dei criteri di riavvio in vigore. Questo stato può indicare che si è verificato un errore irreversibile nel modulo. L'errore si verifica quando Microsoft Monitoring Agent (MMA) non è più in grado di riuscitare il modulo, richiedendo una nuova distribuzione. |

Per informazioni [dettagliate, vedere Proprietà segnalate di EdgeAgent.](module-edgeagent-edgehub.md#edgeagent-reported-properties)

### <a name="monitor-iot-edge-hub-module-twin"></a>Monitorare IoT Edge modulo gemello dell'hub

Il codice JSON seguente mostra il `$edgeHub` modulo gemello in Visual Studio Code con la maggior parte delle sezioni JSON compresse.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

Il codice JSON può essere descritto nelle sezioni seguenti, a partire dall'alto:

* Metadati: contiene i dati di connettività.

* Proprietà: contiene le `desired` `reported` sottosezioni e .
* Properties.desired: (mostrati compressi) Valori di proprietà previsti impostati dall'operatore nella deployment.jsnel file.
* Properties.reported: i valori più recenti delle proprietà segnalati IoT Edge hub.

Se si verificano problemi con i dispositivi downstream, esaminare questi dati è un buon punto di partenza.

## <a name="monitor-custom-module-twins"></a>Monitorare i moduli gemelli personalizzati

Le informazioni sulla connettività dei moduli personalizzati vengono mantenute nel modulo gemello IoT Edge agent. Il modulo gemello per il modulo personalizzato viene usato principalmente per la gestione dei dati per la soluzione. Le proprietà desiderate definite nel deployment.jsnel file vengono riflesse nel modulo gemello e il modulo può aggiornare i valori delle proprietà segnalate in base alle esigenze.

È possibile usare il linguaggio di programmazione preferito con gli SDK per dispositivi [hub IoT di Azure](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) per aggiornare i valori delle proprietà segnalate nel modulo gemello, in base al codice dell'applicazione del modulo. La procedura seguente usa Azure SDK per .NET a tale scopo, usando il codice del [modulo SimulatedTemperatureSensor:](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs)

1. Creare un'istanza di [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) con [il metodo CreateFromEnvironmentAysnc.](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync)

1. Ottenere una raccolta delle proprietà del modulo gemello con [il metodo GetTwinAsync.](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync)

1. Creare un listener (passando un callback) per rilevare le modifiche alle proprietà desiderate con il [metodo SetDesiredPropertyUpdateCallbackAsync.](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync)

1. Nel metodo di callback aggiornare le proprietà segnalate nel modulo gemello con il metodo [UpdateReportedPropertiesAsync,](/dotnet/api/microsoft.azure.devices.client.moduleclient) passando un [oggetto TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) dei valori delle proprietà da impostare.

## <a name="access-the-module-twins"></a>Accedere ai moduli gemelli

È possibile esaminare il codice JSON per i moduli gemelli nella hub IoT di Azure, nella Visual Studio Code e con l'interfaccia della riga di comando di Azure.

### <a name="monitor-in-azure-iot-hub"></a>Monitorare in hub IoT di Azure

Per visualizzare il codice JSON per il modulo gemello:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge** nel riquadro a sinistra.
1. Nella scheda **IoT Edge dispositivi** selezionare **l'ID** dispositivo del dispositivo con i moduli da monitorare.
1. Selezionare il nome del modulo nella **scheda Moduli** e quindi selezionare Module **Identity Twin** dalla barra dei menu superiore.

  ![Selezionare un modulo gemello da visualizzare nel portale di Azure](./media/how-to-monitor-module-twins/select-module-twin.png)

Se viene visualizzato il messaggio "Un'identità del modulo non esiste per questo modulo", questo errore indica che la soluzione back-end non è più disponibile che ha originariamente creato l'identità.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Monitorare i moduli gemelli in Visual Studio Code

Per rivedere e modificare un modulo gemello:

1. Se non è già installato, installare [l'estensione Azure IoT Tools per](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Visual Studio Code.
1. In **Esplora risorse** espandere il **hub IoT di Azure** e quindi espandere il dispositivo con il modulo da monitorare.
1. Fare clic con il pulsante destro del mouse sul modulo e **scegliere Modifica modulo gemello.** Un file temporaneo del modulo gemello viene scaricato nel computer e visualizzato in Visual Studio Code.

  ![Ottenere un modulo gemello da modificare in Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Se si apportano modifiche, selezionare **Aggiorna modulo gemello** sopra il codice nell'editor per salvare le modifiche nell'hub IoT.

  ![Aggiornare un modulo gemello in Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Monitorare i moduli gemelli nell'interfaccia della riga di comando di Azure

Per verificare se IoT Edge è in esecuzione, usare [il metodo az iot hub invoke-module-method](how-to-edgeagent-direct-method.md#ping) per eseguire il ping dell IoT Edge agent.

La [struttura az iot hub module-twin](/cli/azure/iot/hub/module-twin) fornisce questi comandi:

* **az iot hub module-twin show** : mostra una definizione del modulo gemello.
* **az iot hub module-twin update** - Aggiornare una definizione del modulo gemello.
* **az iot hub module-twin replace:** sostituire una definizione del modulo gemello con un file JSON di destinazione.

## <a name="next-steps"></a>Passaggi successivi

Leggere le informazioni su come [comunicare con EdgeAgent usando i metodi diretti predefiniti](how-to-edgeagent-direct-method.md).
