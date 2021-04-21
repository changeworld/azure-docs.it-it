---
title: Trasformare i dati per Azure IoT Central | Microsoft Docs
description: I dispositivi IoT inviano dati in vari formati che potrebbe essere necessario trasformare. Questo articolo descrive come trasformare i dati sia in IoT Central che in uscita. Gli scenari descritti usano IoT Edge e Funzioni di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6032300bd203db78e8cd147cf79300d6dcd9b1dc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751689"
---
# <a name="transform-data-for-iot-central"></a>Trasformare i dati per IoT Central

*Questo argomento si applica ai generatori di soluzioni.*

I dispositivi IoT inviano dati in vari formati. Per usare i dati del dispositivo con l'IoT Central, potrebbe essere necessario usare una trasformazione per:

- Rendere il formato dei dati compatibile con l'applicazione IoT Central dati.
- Convertire le unità.
- Calcolare le nuove metriche.
- Arricchire i dati da altre origini.

Questo articolo illustra come trasformare i dati del dispositivo al di fuori IoT Central in ingresso o in uscita.

Il diagramma seguente illustra tre route per i dati che includono trasformazioni:

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Riepilogo delle route di trasformazione dei dati sia in ingresso che in uscita" border="false":::

La tabella seguente illustra tre tipi di trasformazione di esempio:

| Trasformazione | Descrizione | Esempio  | Note |
|------------------------|-------------|----------|-------|
| Formato del messaggio         | Convertire o modificare i messaggi JSON. | Da CSV a JSON  | In ingresso. IoT Central accetta solo messaggi JSON di valore. Per altre informazioni, vedere [Telemetria, payload di proprietà e comandi.](concepts-telemetry-properties-commands.md) |
| Calcoli           | Funzioni matematiche [che Funzioni di Azure](../../azure-functions/index.yml) possibile eseguire. | Conversione unità da Fahrenheit a Celsius.  | Eseguire la trasformazione usando il modello di uscita per sfruttare l'ingresso del dispositivo scalabile tramite la connessione diretta IoT Central. La trasformazione dei dati consente di usare IoT Central, ad esempio visualizzazioni e processi. |
| Arricchimento dei messaggi     | Arricchimenti da origini dati esterne non trovati nelle proprietà del dispositivo o nei dati di telemetria. Per altre informazioni sugli arricchimenti interni, vedere [Esportare i dati IoT in destinazioni cloud usando l'esportazione dei dati](howto-export-data.md) | Aggiungere informazioni meteo ai messaggi usando i dati sulla posizione dei dispositivi. | Eseguire la trasformazione usando il modello di uscita per sfruttare l'ingresso del dispositivo scalabile tramite la connessione diretta IoT Central. |

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, è necessaria una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per configurare la soluzione, è necessaria un'IoT Central predefinita. Per informazioni su come creare un'IoT Central, vedere [Creare un'Azure IoT Central applicazione](quick-deploy-iot-central.md).

## <a name="data-transformation-at-ingress"></a>Trasformazione dei dati in ingresso

Per trasformare i dati del dispositivo in ingresso, sono disponibili due opzioni:

- **IoT Edge:** usare un modulo IoT Edge per trasformare i dati dai dispositivi downstream prima di inviarli all'applicazione IoT Central dati.

- **IoT Central** bridge di dispositivi: il bridge di dispositivi [IoT Central](howto-build-iotc-device-bridge.md) connette altri cloud di dispositivi IoT, ad esempio Sigfox, Particle e The Things Network, a IoT Central. Il bridge di dispositivi usa una funzione di Azure per inoltrare i dati ed è possibile personalizzare la funzione per trasformare i dati del dispositivo.

### <a name="use-iot-edge-to-transform-device-data"></a>Usare IoT Edge per trasformare i dati del dispositivo

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="Trasformazione dei dati in ingresso tramite IoT Edge" border="false":::

In questo scenario un modulo IoT Edge trasforma i dati dai dispositivi downstream prima di inoltrarli all IoT Central appliazione. A livello di alto livello, i passaggi per configurare questo scenario sono:

1. **Configurare un dispositivo IoT Edge:** installare ed effettuare il provisioning di un dispositivo IoT Edge come gateway e connettere il gateway all'IoT Central applicazione.

1. **Connettere il dispositivo downstream al IoT Edge dispositivo:** Connettere i dispositivi downstream al IoT Edge dispositivo ed eseguirvi il provisioning all'IoT Central applicazione.

1. **Trasformare i dati del dispositivo in IoT Edge:** Creare un IoT Edge modulo per trasformare i dati. Distribuire il modulo nel dispositivo IoT Edge gateway che inoltra i dati del dispositivo trasformato all'applicazione IoT Central dati.

1. **Verifica:** inviare dati da un dispositivo downstream al gateway e verificare che i dati del dispositivo trasformati raggiungano l'IoT Central applicazione.

Nell'esempio descritto nelle sezioni seguenti, il dispositivo downstream invia i dati CSV nel formato seguente al IoT Edge gateway:

```csv
"<temperature >, <pressure>, <humidity>"
```

Si vuole usare un modulo IoT Edge per trasformare i dati nel formato JSON seguente prima di essere inviati a IoT Central:

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

La procedura seguente illustra come configurare questo scenario:

### <a name="build-the-custom-module"></a>Compilare il modulo personalizzato

In questo scenario, il IoT Edge esegue un modulo personalizzato che trasforma i dati dal dispositivo downstream. Prima di distribuire e configurare il IoT Edge dispositivo, è necessario:

- Compilare il modulo personalizzato.
- Aggiungere il modulo personalizzato a un registro contenitori.

Il IoT Edge runtime scarica moduli personalizzati da un registro contenitori, ad esempio un registro azure container o Docker Hub. Il [Azure Cloud Shell](../../cloud-shell/overview.md) dispone di tutti gli strumenti necessari per creare un registro contenitori, compilare il modulo e caricare il modulo nel Registro di sistema:

Per creare un registro contenitori:

1. Aprire il [Azure Cloud Shell](https://shell.azure.com/) e accedere alla sottoscrizione di Azure.

1. Eseguire i comandi seguenti per creare un Registro Azure Container:

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Prendere nota dei valori `username` e `password` e usarli in un secondo momento.

Per compilare il modulo personalizzato nel [Azure Cloud Shell](https://shell.azure.com/):

1. Nel [Azure Cloud Shell](https://shell.azure.com/)passare a una cartella appropriata.
1. Per clonare il repository GitHub che contiene il codice sorgente del modulo, eseguire il comando seguente:

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Per compilare il modulo personalizzato, eseguire i comandi seguenti nel Azure Cloud Shell:

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    L'esecuzione dei comandi precedenti può richiedere alcuni minuti.

### <a name="set-up-an-iot-edge-device"></a>Configurare un dispositivo IoT Edge

Questo scenario usa un IoT Edge gateway per trasformare i dati da qualsiasi dispositivo downstream. Questa sezione descrive come creare modelli di IoT Central per il gateway e i dispositivi downstream nell'applicazione IoT Central dati. IoT Edge i dispositivi usano un manifesto di distribuzione per configurare i moduli.

Per creare un modello di dispositivo per il dispositivo downstream, questo scenario usa un modello di dispositivo termostato semplice:

1. Scaricare il [modello di dispositivo per il dispositivo termostato](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) nel computer locale.

1. Accedere all'applicazione IoT Central e passare alla **pagina Modelli di** dispositivo.

1. Selezionare **+ Nuovo,** selezionare **Dispositivo IoT** e selezionare **Avanti: Personalizza**.

1. Immettere *Thermostat* come nome del modello e selezionare **Avanti: Rivedi**. Quindi selezionare **Crea**

1. Selezionare **Importa un modello e** importare ilthermostat-2.js *nel* file scaricato in precedenza.

1. Selezionare **Pubblica** per pubblicare il nuovo modello di dispositivo.

Per creare un modello di dispositivo per il IoT Edge gateway:

1. Salvare una copia del manifesto della distribuzione nel computer di sviluppo locale: [moduledeployment.jsin](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json).

1. Aprire la copia locale delmoduledeployment.js *nel* file manifesto in un editor di testo.

1. Trovare la sezione e sostituire i segnaposto con i valori di cui si è preso nota al `registryCredentials` momento della creazione del Registro Azure Container. Il `address` valore è simile a `<username>.azurecr.io` .

1. Trovare la `settings` sezione per `transformmodule` . Sostituire `<acr or docker repo>` con lo stesso valore usato nel passaggio `address` precedente. Salvare le modifiche.

1. Nell'applicazione IoT Central passare alla pagina **Modelli di** dispositivo.

1. Selezionare **+ Nuovo,** selezionare **Azure IoT Edge** e quindi selezionare **Avanti: Personalizza.**

1. Immettere *IoT Edge dispositivo gateway come* nome del modello di dispositivo. Selezionare **Questo è un dispositivo gateway.** Selezionare **Sfoglia** per caricare il *moduledeployment.jsnel* file manifesto della distribuzione modificato in precedenza.

1. Quando il manifesto della distribuzione viene convalidato, selezionare **Avanti: Rivedi** e **quindi** crea.

1. In **Modello** selezionare **Relazioni.** Selezionare **+ Aggiungi relazione**. Immettere *Dispositivo downstream* come nome visualizzato e selezionare **Thermostat** come destinazione. Selezionare **Salva**.

1. Selezionare **Pubblica** per pubblicare il modello di dispositivo.

Nell'applicazione sono ora disponibili due modelli IoT Central dispositivo. Il **IoT Edge dispositivo gateway** e il modello **Thermostat** come dispositivo downstream.

Per registrare un dispositivo gateway in IoT Central:

1. Nell'applicazione IoT Central passare alla **pagina** Dispositivi.

1. Selezionare **IoT Edge dispositivo gateway** e selezionare Crea un **dispositivo.** Immettere *IoT Edge dispositivo gateway* come nome del dispositivo, immettere *gateway-01* come ID dispositivo e assicurarsi che IoT Edge **dispositivo gateway** sia selezionato come modello di dispositivo. Selezionare **Crea**.

1. Nell'elenco dei dispositivi fare clic sul **IoT Edge gateway** e quindi selezionare **Connetti.**

1. Prendere nota dei valori **di ambito ID,** **ID** dispositivo e chiave **primaria** per il **IoT Edge gateway.** Sarà possibile usarli in un secondo momento.

Per registrare un dispositivo downstream in IoT Central:

1. Nell'applicazione IoT Central passare alla **pagina** Dispositivi.

1. Selezionare **Termostato** e **selezionare Crea un dispositivo.** Immettere *Thermostat* come nome del dispositivo, immettere *downstream-01* come ID dispositivo, assicurarsi che **Thermostat** sia selezionato come modello di dispositivo. Selezionare **Crea**.

1. Nell'elenco dei dispositivi selezionare **termostato** e quindi **selezionare Collega al gateway**. Selezionare il modello **IoT Edge del dispositivo gateway** e **l'IoT Edge del dispositivo gateway.** Selezionare **Allega**.

1. Nell'elenco dei dispositivi fare clic su **Thermostat** e quindi selezionare **Connetti**.

1. Prendere nota dei valori **ambito ID**, **ID** dispositivo e **chiave** primaria per il dispositivo **Thermostat.** Vengono usate in un secondo momento.

### <a name="deploy-the-gateway-and-downstream-devices"></a>Distribuire il gateway e i dispositivi downstream

Per praticità, questo articolo usa macchine virtuali di Azure per eseguire il gateway e i dispositivi downstream. Per creare le due macchine virtuali di Azure, selezionare il pulsante Distribuisci in **Azure** seguente e usare le informazioni nella tabella seguente per completare il **modulo Distribuzione** personalizzata:

| Campo | Valore |
| ----- | ----- |
| Resource group | `ingress-scenario` |
| Gateway prefisso etichetta DNS | Nome DNS univoco per il computer, ad esempio `<your name>edgegateway` |
| Prefisso etichetta DNS downstream | Nome DNS univoco per il computer, ad esempio `<your name>downstream` |
| ID ambito | Ambito ID di cui si è preso nota in precedenza |
| ID dispositivo IoT Edge gateway | `gateway-01` |
| Gateway IoT Edge dispositivo | Il valore della chiave primaria di cui si è preso nota in precedenza |
| Tipo di autenticazione | Password |
| Password o chiave amministratore | La password scelta per l'account **AzureUser** in entrambe le macchine virtuali. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Selezionare **Rivedi e crea** e quindi **Crea**. La creazione delle macchine virtuali nel gruppo di risorse **in ingresso-scenario** richiede alcuni minuti.

Per verificare che il IoT Edge sia in esecuzione correttamente:

1. Aprire l'IoT Central applicazione. Passare quindi al **dispositivo IoT Edge Gateway** nell'elenco dei dispositivi nella **pagina** Dispositivi.

1. Selezionare la **scheda** Moduli e controllare lo stato dei tre moduli. L'avvio del runtime IoT Edge nella macchina virtuale richiede alcuni minuti. Quando viene avviato, lo stato dei tre moduli è **In esecuzione**. Se il runtime IoT Edge non viene avviato, vedere Risolvere i [problemi relativi IoT Edge dispositivo](../../iot-edge/troubleshoot.md).

Perché il IoT Edge funzioni come gateway, sono necessari alcuni certificati per dimostrare la propria identità a qualsiasi dispositivo downstream. Questo articolo usa certificati demo. In un ambiente di produzione usare i certificati dell'autorità di certificazione.

Per generare i certificati demo e installarli nel dispositivo gateway:

1. Usare SSH per connettersi e accedere alla macchina virtuale del dispositivo gateway. È possibile trovare il nome DNS per questa macchina virtuale nella portale di Azure. Passare alla **macchina virtuale edgegateway** nel gruppo **di risorse ingress-scenario.**

    > [!TIP]
    > Potrebbe essere necessario aprire la porta 22 per l'accesso SSH in entrambe le macchine virtuali prima di poter usare SSH per connettersi dal computer locale o dal Azure Cloud Shell.

1. Eseguire i comandi seguenti per clonare il repository IoT Edge e generare i certificati demo:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Dopo aver eseguito i comandi precedenti, i file seguenti sono pronti per l'uso nei passaggi successivi:

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem:* certificato CA radice usato per creare tutti gli altri certificati demo per testare uno scenario IoT Edge.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem:* certificato della CA del dispositivo a cui viene fatto riferimento dal file *config.yaml.* In uno scenario di gateway, questo certificato della CA è il modo in cui il IoT Edge verifica la propria identità nei dispositivi downstream.
    - *~/certs/private/iot-edge-device-mycacert.key.pem:* chiave privata associata al certificato CA del dispositivo.

    Per altre informazioni su questi certificati demo, vedere [Creare certificati demo per testare](../../iot-edge/how-to-create-test-certificates.md)le IoT Edge dei dispositivi.

1. Aprire il file *config.yaml* in un editor di testo. Ad esempio:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Individuare le `Certificate settings` impostazioni. Rimuovere il commento e modificare le impostazioni del certificato come indicato di seguito:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    L'esempio precedente presuppone che l'accesso sia stato eseguito come **AzureUser** e che sia stata creata una CA del dispositivo denominata "mycacert".

1. Salvare le modifiche e riavviare il runtime IoT Edge seguente:

    ```bash
    sudo systemctl restart iotedge
    ```

Se il runtime IoT Edge viene avviato correttamente dopo le  modifiche, lo stato dei moduli $edgeAgent e **$edgeHub** cambia in **Esecuzione di**. È possibile visualizzare questi valori di stato **nella pagina Moduli** per il dispositivo gateway in IoT Central.

Se il runtime non viene avviato, controllare le modifiche apportate in *config.yaml* e vedere Risolvere i problemi [del IoT Edge dispositivo](../../iot-edge/troubleshoot.md).

### <a name="connect-downstream-device-to-iot-edge-device"></a>Connettere un dispositivo downstream IoT Edge dispositivo

Per connettere un dispositivo downstream al IoT Edge gateway:

1. Usare SSH per connettersi e accedere alla macchina virtuale del dispositivo downstream. È possibile trovare il nome DNS per questa macchina virtuale nella portale di Azure. Passare alla macchina **virtuale leafdevice** nel gruppo **di risorse ingress-scenario.**

    > [!TIP]
    > Potrebbe essere necessario aprire la porta 22 per l'accesso SSH in entrambe le macchine virtuali prima di poter usare SSH per connettersi dal computer locale o dal Azure Cloud Shell.

1. Per clonare il repository GitHub con il codice sorgente per il dispositivo downstream di esempio, eseguire il comando seguente:

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Per copiare il certificato richiesto dal dispositivo gateway, eseguire i comandi `scp` seguenti. Questo `scp` comando usa il nome host per identificare la macchina virtuale del `edgegateway` gateway. Verrà richiesto di immettere la password:

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. Passare alla cartella *leafdevice* e installare i pacchetti necessari. Eseguire quindi gli `build` script e per effettuare il provisioning e connettere il dispositivo al `start` gateway:

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Immettere l'ID dispositivo, l'ID ambito e la chiave di firma di accesso condiviso per il dispositivo downstream creato in precedenza. Per il nome host immettere `edgegateway` . L'output del comando è simile al seguente:

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Verifica

Per verificare che lo scenario sia in esecuzione, passare **al dispositivo gateway IoT Edge in** IoT Central:

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Screenshot che mostra i dati trasformati nella pagina dei dispositivi.":::

- Selezionare **Moduli.** Verificare che i tre IoT Edge seguenti **$edgeAgent**, **$edgeHub** **e transformmodule** siano in esecuzione.
- Selezionare Dispositivi **downstream e** verificare che sia stato effettuato il provisioning del dispositivo downstream.
- Selezionare **Dati non elaborati**. I dati di telemetria **nella colonna di dati unmodeled** sono simili ai seguenti:

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

Poiché il IoT Edge sta trasformando i dati dal dispositivo downstream, i dati di telemetria vengono associati al dispositivo gateway in IoT Central. Per visualizzare i dati di telemetria, creare una nuova versione del modello di **dispositivo gateway IoT Edge con** definizioni per i tipi di telemetria.

## <a name="data-transformation-at-egress"></a>Trasformazione dei dati in uscita

È possibile connettere i dispositivi a IoT Central, esportare i dati del dispositivo in un motore di calcolo per trasformarlo e quindi inviare i dati trasformati a IoT Central per la gestione e l'analisi dei dispositivi. Ad esempio:

- I dispositivi inviano i dati sulla posizione IoT Central.
- IoT Central esporta i dati in un motore di calcolo che migliora i dati sulla posizione con le informazioni meteo.
- Il motore di calcolo invia i dati migliorati a IoT Central.

È possibile usare il bridge [IoT Central dispositivo](https://github.com/Azure/iotc-device-bridge) come motore di calcolo per trasformare i dati esportati da IoT Central.

Un vantaggio della trasformazione dei dati in uscita è che i dispositivi si connettono direttamente IoT Central, semplificando l'invio di comandi ai dispositivi o l'aggiornamento delle proprietà dei dispositivi. Con questo metodo, tuttavia, è possibile usare più messaggi rispetto all'assegnazione mensile e aumentare il costo dell'Azure IoT Central.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>Usare il bridge IoT Central dispositivo per trasformare i dati del dispositivo

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="Trasformazione dei dati in uscita tramite IoT Edge" border="false":::

In questo scenario, un motore di calcolo trasforma i dati del dispositivo esportati da IoT Central prima di inviarli all'applicazione IoT Central dati. A livello di alto livello, i passaggi per configurare questo scenario sono:

1. **Configurare il motore di calcolo:** Creare un bridge IoT Central dispositivo per fungere da motore di calcolo per la trasformazione dei dati.

1. **Trasformare i dati del dispositivo nel bridge del dispositivo:** Trasformare i dati nel bridge di dispositivi modificando il codice della funzione del bridge di dispositivo per il caso d'uso della trasformazione dei dati.

1. **Abilitare il flusso di dati IoT Central al bridge del dispositivo:** Esportare i dati da IoT Central al bridge di dispositivo per la trasformazione. Quindi, inoltrare di nuovo i dati trasformati IoT Central. Quando si crea l'esportazione dei dati, usare i filtri delle proprietà dei messaggi per esportare solo i dati non trasformati.

1. **Verifica:** connettere il dispositivo all'app IoT Central e verificare la presenza di dati non elaborati del dispositivo e dati trasformati in IoT Central.

<!-- To Do - doesn't the device send JSON data? -->
Nell'esempio descritto nelle sezioni seguenti, il dispositivo invia i dati CSV nel formato seguente al IoT Edge gateway:

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

Usare il bridge di dispositivo per trasformare i dati del dispositivo tramite:

- Modifica dell'unità di temperatura da centigrade a fahrenheit.
- Arricchimento dei dati del dispositivo con dati meteo estratti dal [servizio Open Weather](https://openweathermap.org/) per i valori di latitudine e longitudine.

Il bridge di dispositivi invia quindi i dati trasformati IoT Central nel formato seguente:

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

La procedura seguente illustra come configurare questo scenario:

### <a name="retrieve-your-iot-central-connection-settings"></a>Recuperare le impostazioni IoT Central connessione

Prima di configurare questo scenario, è necessario ottenere alcune impostazioni di connessione dall'IoT Central applicazione:

1. Accedere all'applicazione IoT Central.

1. Passare ad **Amministrazione > connessione del dispositivo.**

1. Prendere nota **dell'ambito ID**. Questo valore verrà utilizzato in un secondo momento.

1. Selezionare il **gruppo di registrazione SaS-IoT-Devices.** Prendere nota della chiave primaria della firma di accesso condiviso. Questo valore verrà utilizzato in un secondo momento.

### <a name="set-up-a-compute-engine"></a>Configurare un motore di calcolo

Questo scenario usa lo stesso Funzioni di Azure distribuzione del bridge IoT Central dispositivo. Per distribuire il bridge di dispositivi, selezionare il pulsante **Distribuisci** in Azure seguente e usare le informazioni nella tabella seguente per completare il **modulo Distribuzione** personalizzata:

| Campo | Valore |
| ----- | ----- |
| Resource group | Creare un nuovo gruppo di risorse denominato `egress-scenario` |
| Region | Selezionare l'area più vicina. |
| ID ambito | Usare **l'ambito ID** di cui si è preso nota in precedenza. |
| IoT Central di firma di accesso condiviso | Usare la chiave primaria della firma di accesso condiviso per il **gruppo di registrazione SaS-IoT-Devices.** Questo valore è stato preso nota in precedenza. |

[ ![ Eseguire la distribuzione in Azure.](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Selezionare **Rivedi e crea** e quindi **Crea**. La creazione della funzione di Azure e delle risorse correlate nel gruppo di risorse **egress-scenario** richiede alcuni minuti.

### <a name="transform-device-data-in-the-device-bridge"></a>Trasformare i dati del dispositivo nel bridge del dispositivo

Per configurare il bridge di dispositivo per trasformare i dati del dispositivo esportati:

1. Ottenere una chiave API dell'applicazione dal servizio meteo. Un account è gratuito con utilizzo limitato del servizio. Per creare una chiave API dell'applicazione, creare un account nel portale [open servizio meteo](https://openweathermap.org/) e seguire le istruzioni. Usare la chiave API Open Weather in un secondo momento.

1. Nel portale di Azure passare ad App per le funzioni nel gruppo **di risorse egress-scenario.**

1. Nel riquadro di spostamento a sinistra in **Strumenti di sviluppo** selezionare editor del servizio app **(anteprima).**

1. Selezionare **&rarr; Vai** per aprire la **editor del servizio app** pagina. Apportare le modifiche seguenti:

    1. Aprire il file *wwwroot/IoTCIntegration/index.js.* Sostituire tutto il codice in questo file con il codice in [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js).

    1. Nel nuovo *index.js* aggiornare il `openWeatherAppId` file delle variabili con la chiave DELL'API Open Weather ottenuta in precedenza.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. Aggiungere una proprietà del messaggio ai dati inviati dalla funzione per IoT Central. IoT Central usa questa proprietà per impedire l'esportazione dei dati trasformati. Per apportare questa modifica, aprire il file *wwwroot/IoTCIntegration/lib/engine.js.* Individuare il codice seguente:

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Aggiungere il codice seguente subito dopo il codice nel frammento di codice precedente:

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Per riferimento, è possibile visualizzare un esempio completo del file [engine.js.](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js)

1. Nel riquadro **editor del servizio app** selezionare **Console** nel riquadro di spostamento a sinistra. Eseguire i comandi seguenti per installare i pacchetti necessari:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    L'esecuzione del comando può richiedere alcuni minuti.

1. Tornare alla pagina **Panoramica delle funzioni di Azure** e riavviare la funzione:

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="Riavviare la funzione":::

1. Selezionare **Funzioni** nel riquadro di spostamento a sinistra. Selezionare quindi **IoTCIntegration.** Selezionare **Codice e test.**

1. Prendere nota dell'URL della funzione. Questo valore sarà necessario in un secondo momento:

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="Ottenere l'URL della funzione":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>Abilitare il flusso di dati IoT Central al bridge di dispositivi

In questa sezione viene descritto come configurare l'Azure IoT Central applicazione.

Salvare prima di tutto il file [del modello](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) di dispositivo nel computer locale.

Per aggiungere un modello di dispositivo all'applicazione IoT Central, passare all'applicazione IoT Central dispositivo e quindi:

1. Accedere all'applicazione IoT Central e passare alla pagina **Modelli di** dispositivo.

1. Selezionare **+ Nuovo,** selezionare **Dispositivo IoT,** selezionare **Avanti: Personalizza** e immettere Modello di *calcolo* come nome del modello. Selezionare **Avanti: Review** (Avanti: Rivedi). Quindi selezionare **Crea**

1. Selezionare **Importa un modello e** passare allamodel.js *nel* file scaricato in precedenza.

1. Dopo aver importato il modello, selezionare Pubblica **per pubblicare** il modello **di dispositivo Modello** di calcolo.

Per configurare l'esportazione dei dati per l'invio di dati al bridge di dispositivi:

1. Nell'IoT Central dati selezionare **Esportazione dati.**

1. Selezionare **+ Nuova destinazione** per creare una destinazione da usare con il bridge di dispositivi. Chiamare la funzione *compute di destinazione*, per Tipo di **destinazione** selezionare **Webhook.** Per URL callback selezionare Incolla nell'URL della funzione di cui si è preso nota in precedenza. Lasciare **l'autorizzazione** **su No Auth**( Nessuna autenticazione).

1. Salvare le modifiche.

1. Selezionare + **Nuova esportazione e** creare un'esportazione dati denominata Esportazione di *calcolo*.

1. Aggiungere un filtro per esportare solo i dati del dispositivo per il modello di dispositivo in uso. Selezionare **+ Filtro,** selezionare l'elemento **Modello** di dispositivo, selezionare l'operatore **Uguale a** e selezionare il modello di dispositivo **Modello** di calcolo appena creato.

1. Aggiungere un filtro messaggi per distinguere tra dati trasformati e non trasformati. Questo filtro impedisce l'invio di valori trasformati al bridge del dispositivo. Selezionare **+ Filtro proprietà messaggio** e immettere il valore del nome calcolato, quindi selezionare l'operatore Non **esiste**.  La stringa `computed` viene usata come parola chiave nel codice di esempio del bridge di dispositivo.

1. Per la destinazione, selezionare la **destinazione della funzione di** calcolo creata in precedenza.

1. Salvare le modifiche. Dopo circa un minuto, lo **stato di esportazione** viene visualizzato come **Integro.**

### <a name="verify"></a>Verifica

Il dispositivo di esempio utilizzato per testare lo scenario è scritto in Node.js. Assicurarsi di avere installato Node.js E NPM nel computer locale. Se non si vogliono installare questi prerequisiti, usare il Azure Cloud Shell[in](https://shell.azure.com/) cui sono preinstallati.

Per eseguire un dispositivo di esempio che testa lo scenario:

1. Clonare il repository GitHub che contiene il codice di esempio, eseguire il comando seguente:

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Per connettere il dispositivo di esempio all'applicazione IoT Central, modificare le impostazioni di connessione nel file *iot-central-compute/device/device.js.* Sostituire l'ID ambito e la chiave di firma di accesso condiviso del gruppo con i valori di cui si è preso nota in precedenza:

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Salvare le modifiche.

1. Usare i comandi seguenti per installare i pacchetti necessari ed eseguire il dispositivo:

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. Il risultato di questo comando è simile all'output seguente:

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. Nell'applicazione IoT Central passare al dispositivo denominato **computeDevice.** Nella visualizzazione **Dati non** elaborati sono presenti due flussi di telemetria diversi che vengono visualizzati circa ogni cinque secondi. Il flusso con dati non modellati è la telemetria originale, il flusso con i dati modellati sono i dati trasformati dalla funzione:

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Screenshot che mostra i dati non elaborati originali e trasformati.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse di Azure create durante la procedura descritta in questa guida non sono più necessarie, eliminare i gruppi di risorse [nel portale di Azure](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups).

I due gruppi di risorse usati in questa guida sono **ingress-scenario** **e egress-scenario.**

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati apprese le diverse opzioni per trasformare i dati dei dispositivi per IoT Central, sia in ingresso che in uscita. L'articolo includeva procedure dettagliate per due scenari specifici:

- Usare un IoT Edge per trasformare i dati dai dispositivi downstream prima che i dati vengono inviati all IoT Central app IoT Central dati.
- Usare Funzioni di Azure per trasformare i dati all'esterno IoT Central. In questo scenario, IoT Central'esportazione dei dati per inviare i dati in ingresso a una funzione di Azure da trasformare. La funzione invia i dati trasformati all'applicazione IoT Central dati.

Ora che si è appreso come trasformare i dati dei dispositivi all'esterno dell'applicazione Azure IoT Central, è possibile apprendere come usare l'analisi per analizzare i dati dei [dispositivi IoT Central](howto-create-analytics.md).
