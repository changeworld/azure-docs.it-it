---
title: Distribuire il bridge Azure IoT Central dispositivo | Microsoft Docs
description: Distribuire il IoT Central del dispositivo per connettere altri cloud IoT all'app IoT Central app. Altri cloud IoT includono Sigfox, Particle Device Cloud e The Things Network.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 6b535ecb80fae9f55eb6ab11751c26e0c6d0e9e5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713733"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Usare il bridge IoT Central dispositivo per connettere altri cloud IoT IoT Central

*Questo articolo si applica agli amministratori.*

## <a name="azure-iot-central-device-bridge"></a>Azure IoT Central del dispositivo

Il IoT Central del dispositivo è una soluzione open source che connette altri cloud IoT all'applicazione IoT Central dati. Esempi di altri cloud IoT includono [Sigfox,](https://www.sigfox.com/) [Particle Device Cloud](https://www.particle.io/)e The Things [Network.](https://www.thethingsnetwork.org/) Il bridge di dispositivi funziona inoltrando i dati dai dispositivi connessi ad altri cloud IoT all'applicazione IoT Central dati. Il bridge del dispositivo inoltra solo i dati IoT Central, non invia comandi o aggiornamenti delle proprietà IoT Central ai dispositivi.

Il bridge di dispositivi consente di combinare la potenza di IoT Central con dispositivi come i dispositivi di rilevamento degli asset connessi alla rete ad area larga a basso consumo di Sigfox, i dispositivi di monitoraggio della qualità dell'aria nel cloud dei dispositivi di particelle o i dispositivi di monitoraggio dell'umidità del suolo nella rete Things. È possibile usare IoT Central delle applicazioni, ad esempio regole e analisi sui dati, creare flussi di lavoro in app Power Automate e app per la logica di Azure o esportare i dati.

La soluzione bridge di dispositivi effettua il provisioning di diverse risorse di Azure nella sottoscrizione di Azure che funzionano insieme per trasformare e inoltrare i messaggi del dispositivo IoT Central.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Completare la guida di avvio rapido [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) per creare un'applicazione IoT Central usando il modello **App personalizzata > Applicazione personalizzata**.

## <a name="overview"></a>Panoramica

Il bridge di dispositivi IoT Central è una soluzione open source in GitHub. Usa un modello di Azure Resource Manager personalizzato per distribuire diverse risorse nella sottoscrizione di Azure, inclusa un'app per le funzioni di Azure.

L'app per le funzioni è il componente principale del bridge del dispositivo. Riceve richieste HTTP POST da altre piattaforme IoT tramite un semplice webhook. Il [repository Azure IoT Central Device Bridge](https://github.com/Azure/iotc-device-bridge) include esempi che illustrano come connettere i cloud Sigfox, Particle e The Things Network. È possibile estendere questa soluzione per connettersi al cloud IoT personalizzato se la piattaforma può inviare richieste HTTP POST all'app per le funzioni.

L'app per le funzioni trasforma i dati in un formato accettato IoT Central e inoltra i dati usando il servizio Device Provisioning e le API client del dispositivo:

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Screenshot di Funzioni di Azure.":::

Se l IoT Central appliata riconosce l'ID dispositivo nel messaggio inoltrato, i dati di telemetria del dispositivo vengono visualizzati IoT Central. Se l'ID dispositivo non viene riconosciuto dall'applicazione IoT Central, l'app per le funzioni tenta di registrare un nuovo dispositivo con l'ID dispositivo. Il nuovo dispositivo viene visualizzato come **dispositivo** non ad associazione nella **pagina** Dispositivi dell'IoT Central app IoT Central dispositivo. Dalla pagina **Dispositivi** è possibile associare il nuovo dispositivo a un modello di dispositivo e quindi visualizzare i dati di telemetria.

## <a name="deploy-the-device-bridge"></a>Distribuire il bridge di dispositivi

Per distribuire il bridge di dispositivi nella sottoscrizione:

1. Nell'applicazione IoT Central passare alla pagina **Amministrazione > connessione dispositivo.**

    1. Prendere nota dell'**Ambito ID**. Questo valore viene utilizzato quando si distribuisce il bridge di dispositivi.

    1. Nella stessa pagina aprire il gruppo **di registrazione SAS-IoT-Devices.** Nella pagina **del gruppo SAS-IoT-Devices** copiare la **chiave primaria**. Questo valore viene utilizzato quando si distribuisce il bridge di dispositivi.

1. Usare il **pulsante Distribuisci in Azure** seguente per aprire il modello di Resource Manager personalizzato che distribuisce l'app per le funzioni nella sottoscrizione. Usare **l'ambito ID** **e la chiave primaria** del passaggio precedente:

    [![Distribuisci in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Al termine della distribuzione, è necessario installare i pacchetti NPM necessari per la funzione:

1. Nel portale di Azure aprire l'app per le funzioni distribuita nella sottoscrizione. Passare quindi a **Strumenti di sviluppo > Console**. Nella console eseguire i comandi seguenti per installare i pacchetti:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    L'esecuzione di questi comandi può richiedere alcuni minuti. È possibile ignorare tutti i messaggi di avviso.

1. Al termine dell'installazione del pacchetto, selezionare **Riavvia** nella **pagina Panoramica** dell'app per le funzioni:

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="Screenshot di Riavvia.":::

1. La funzione è ora pronta per l'uso. I sistemi esterni possono usare le richieste HTTP POST per inviare i dati del dispositivo tramite il bridge di dispositivo nell'IoT Central applicazione. Per ottenere l'URL della funzione, passare a **Funzioni > IoTCIntegration > Code + Test > Get function URL**:

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="Screenshot di Get Function URL (Ottieni URL funzione).":::

I corpi dei messaggi inviati al bridge del dispositivo devono avere il formato seguente:

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

Ogni chiave nell'oggetto deve corrispondere al nome di un tipo di telemetria nel modello di `measurements` dispositivo nell IoT Central app. Questa soluzione non supporta la specifica dell'ID interfaccia nel corpo del messaggio. Pertanto, se due interfacce diverse hanno un tipo di telemetria con lo stesso nome, la misura viene visualizzata in entrambi i flussi di telemetria nell'applicazione IoT Central dati.

È possibile includere un `timestamp` campo nel corpo per specificare la data e l'ora UTC del messaggio. Questo campo deve essere in formato ISO 8601. Ad esempio: `2020-06-08T20:16:54.602Z`. Se non si include un timestamp, vengono usate la data e l'ora correnti.

È possibile includere `modelId` un campo nel corpo. Usare questo campo per associare il dispositivo a un modello di dispositivo durante il provisioning. Questa funzionalità è supportata solo dalle [applicazioni V3.](howto-get-app-info.md)

Deve `deviceId` essere alfanumerico, minuscolo e può contenere trattini.

Se non si include il campo o se IoT Central non riconosce l'ID modello, un messaggio con un dispositivo non riconosciuto crea un nuovo dispositivo non associato in `modelId` `deviceId` IoT Central.  Un operatore può eseguire manualmente la migrazione del dispositivo al modello di dispositivo corretto. Per altre informazioni, vedere [Gestire i dispositivi nell'applicazione Azure IoT Central > Migrazione di dispositivi a un modello.](howto-manage-devices.md)

Nelle [applicazioni V2](howto-get-app-info.md)il nuovo dispositivo viene visualizzato nella **Device Explorer > Dispositivi nonssociati.** Selezionare **Associa e** scegliere un modello di dispositivo per iniziare a ricevere i dati di telemetria in ingresso dal dispositivo.

> [!NOTE]
> Finché il dispositivo non è associato a un modello, tutte le chiamate HTTP alla funzione restituiscono uno stato di errore 403.

Per attivare la registrazione per l'app per le funzioni con Application Insights, passare a Monitoraggio > **log** nell'app per le funzioni nel portale di Azure. Selezionare **Attiva Application Insights**.

## <a name="provisioned-resources"></a>Risorse di cui è stato effettuato il provisioning

Il Resource Manager seguente effettua il provisioning delle risorse seguenti nella sottoscrizione di Azure:

* App per le funzioni
* Piano di servizio app
* Account di archiviazione
* Insieme di credenziali delle chiavi

L'insieme di credenziali delle chiavi archivia la chiave del gruppo di firma di accesso condiviso per l IoT Central appliata.

L'app per le funzioni viene eseguita in un [piano a consumo](https://azure.microsoft.com/pricing/details/functions/). Anche se questa opzione non offre risorse di calcolo dedicate, consente al bridge di dispositivi di gestire centinaia di messaggi di dispositivo al minuto, adatti a una flotta più piccola di dispositivi o dispositivi che inviano messaggi con minore frequenza. Se l'applicazione dipende dallo streaming di un numero elevato di messaggi del dispositivo, sostituire il piano a consumo con un piano di [servizio app dedicato.](https://azure.microsoft.com/pricing/details/app-service/windows/) Questo piano offre risorse di calcolo dedicate, che offrono tempi di risposta del server più rapidi. Usando un piano di servizio app standard, le prestazioni massime osservate della funzione di Azure in questo repository sono state di circa 1.500 messaggi dispositivo al minuto. Per altre informazioni, vedere Opzioni [di hosting di Funzioni di Azure.](../../azure-functions/functions-scale.md)

Per usare un piano di servizio app dedicato anziché un piano a consumo, modificare il modello personalizzato prima della distribuzione. Selezionare **Modifica modello.**

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="Screenshot di Modifica modello.":::

Sostituire il segmento seguente:

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

con

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

Modificare quindi il modello da includere nella configurazione per la risorsa nella configurazione alwaysOn per garantire che l'app per le funzioni `"alwaysOn": true` `functionapp` sia sempre in `"properties": {"SiteConfig": {...}}` esecuzione. [](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan)

## <a name="examples"></a>Esempio

Gli esempi seguenti illustrano come configurare il bridge di dispositivi per vari cloud IoT:

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>Esempio 1: Connessione di dispositivi particle tramite il bridge del dispositivo

Per connettere un dispositivo Particle tramite il bridge del dispositivo IoT Central, passare alla console particle e creare una nuova integrazione del webhook. Impostare **Formato richiesta** su **JSON.**  In **Impostazioni avanzate** usare il formato del corpo personalizzato seguente:

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": {{{PARTICLE_EVENT_VALUE}}}
  }
}
```

Incollare **l'URL della** funzione dall'app per le funzioni di Azure e i dispositivi particle vengono visualizzati come dispositivi non IoT Central. Per altre informazioni, vedere il post di blog [Here's how to integrate your Particle-powered projects with Azure IoT Central](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/) (Come integrare i progetti basati su particelle con Azure IoT Central blog).

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>Esempio 2: Connessione di dispositivi Sigfox tramite il bridge di dispositivi

Alcune piattaforme potrebbero non consentire di specificare il formato dei messaggi del dispositivo inviati tramite un webhook. Per questi sistemi, è necessario convertire il payload del messaggio nel formato del corpo previsto prima che venga elaborata dal bridge del dispositivo. È possibile eseguire la conversione nella stessa funzione di Azure che esegue il bridge del dispositivo.

Questa sezione illustra come convertire il payload di un'integrazione del webhook Sigfox nel formato del corpo previsto dal bridge del dispositivo. Il cloud Sigfox trasmette i dati del dispositivo in formato stringa esadecimale. Per praticità, il bridge di dispositivi include una funzione di conversione per questo formato, che accetta un subset dei possibili tipi di campo in un payload del dispositivo Sigfox: e di `int` `uint` 8, 16, 32 o 64 bit; di `float` 32 bit o 64 bit; little-endian e big-endian. Per elaborare i messaggi da un'integrazione del webhook Sigfox, apportare le modifiche seguenti al file _IoTCIntegration/index.js_ nell'app per le funzioni.

Per convertire il payload del messaggio, aggiungere il codice seguente prima della chiamata a alla riga 21, sostituendo con `handleMessage` la definizione del payload `payloadDefinition` Sigfox:

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

I dispositivi Sigfox prevedono un `204` codice di risposta. Aggiungere il codice seguente dopo la chiamata a `handleMessage` nella riga 21:

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>Esempio 3: Connessione di dispositivi dalla rete Things tramite il bridge di dispositivi

Per connettere i dispositivi di rete Things a IoT Central:

* Aggiungere una nuova integrazione HTTP all'applicazione in The Things Network: Application > Integrations > **aggiungere l'integrazione >'integrazione HTTP.**
* Assicurarsi che l'applicazione includa una funzione di decodificatore che converte automaticamente il payload dei messaggi del dispositivo in JSON prima di essere inviata alla funzione di Azure: Funzioni di payload di Application > > **decoder**.

L'esempio seguente illustra una funzione decodificatore JavaScript che è possibile usare per decodificare tipi numerici comuni da dati binari:

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

Dopo aver definito l'integrazione, aggiungere il codice seguente prima della chiamata a nella riga `handleMessage` 21 del file *IoTCIntegration/index.js* dell'app per le funzioni di Azure. Questo codice converte il corpo dell'integrazione HTTP nel formato previsto.

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>Limitazioni

Il bridge di dispositivi inoltra solo i messaggi IoT Central e non invia i messaggi ai dispositivi. Per questo motivo, le proprietà e i comandi non funzionano per i dispositivi che si connettono IoT Central tramite questo bridge di dispositivi. Poiché le operazioni del dispositivo gemello non sono supportate, non è possibile aggiornare le proprietà del dispositivo tramite il bridge di dispositivi. Per usare queste funzionalità, un dispositivo deve connettersi direttamente IoT Central usando uno degli [SDK Azure IoT dispositivo.](../../iot-hub/iot-hub-devguide-sdks.md)

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come distribuire il bridge IoT Central dispositivo, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Gestire i dispositivi](howto-manage-devices.md)
