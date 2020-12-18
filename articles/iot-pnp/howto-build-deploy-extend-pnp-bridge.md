---
title: Come compilare, distribuire ed estendere Plug and Play Bridge | Microsoft Docs
description: Identificare i componenti Plug and Play Bridge. Informazioni su come estendere il Bridge e su come eseguirlo nei dispositivi, nei gateway e in un modulo IoT Edge.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ece9f62e64eb64b1f34af46b42d57ec583f8f214
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97675872"
---
# <a name="build-deploy-and-extend-the-iot-plug-and-play-bridge"></a>Crea, Distribuisci ed Estendi i Plug and Play Bridge

Il Bridge Internet delle cose Plug and Play consente di connettere i dispositivi esistenti collegati a un gateway all'hub Internet. Si usa il Bridge per eseguire il mapping delle interfacce Plug and Play ai dispositivi collegati. Un'interfaccia Plug and Play di Internet delle cose definisce i dati di telemetria inviati da un dispositivo, le proprietà sincronizzate tra il dispositivo e il cloud e i comandi a cui risponde il dispositivo. È possibile installare e configurare l'applicazione Bridge Open Source nei gateway Windows o Linux.

Questo articolo illustra in dettaglio come:

- Configurare un Bridge.
- Estendere un bridge creando nuovi adapter.
- Come compilare ed eseguire il Bridge in diversi ambienti.

Per un semplice esempio in cui viene illustrato come usare il Bridge, vedere [come connettere l'esempio plug and Play Bridge eseguito in Linux o Windows all'hub](howto-use-iot-pnp-bridge.md)Internet.

Le linee guida e gli esempi in questo articolo presuppongono una certa familiarità con i dispositivi [gemelli digitali](../digital-twins/overview.md) e [plug and Play](overview-iot-plug-and-play.md)di Azure.

## <a name="general-prerequisites"></a>Prerequisiti generali

### <a name="supported-os-platforms"></a>Piattaforme del sistema operativo supportate

Sono supportate le piattaforme e le versioni del sistema operativo seguenti:

|Piattaforma  |Versioni supportate  |
|---------|---------|
|Windows 10 |     Sono supportati tutti gli SKU di Windows. Ad esempio: Internet delle cose Enterprise, server, desktop, Internet delle cose. *Per la funzionalità di monitoraggio dell'integrità della fotocamera è consigliabile usare 20H1 o una compilazione successiva. Tutte le altre funzionalità sono disponibili in tutte le build di Windows 10.*  |
|Linux     |Testato e supportato in Ubuntu 18,04, la funzionalità su altre distribuzioni non è stata testata.         |
||

### <a name="hardware"></a>Hardware

- Qualsiasi piattaforma hardware che supporta gli SKU e le versioni precedenti del sistema operativo.
- I sensori e le periferiche Serial, USB, Bluetooth e della fotocamera sono supportati in modalità nativa. Il Bridge Plug and Play può essere esteso per supportare qualsiasi periferica o sensore personalizzato.

### <a name="azure-iot-products-and-tools"></a>Prodotti e strumenti di Azure.

- **Hub** di Azure: per connettere il dispositivo a è necessario un [Hub](../iot-hub/index.yml) di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Se non si dispone di un hub Internet delle cose, [seguire queste istruzioni per crearne uno](../iot-hub/iot-hub-create-using-cli.md). Il supporto di Internet delle cose Plug and Play non è incluso negli hub delle cose di livello Basic.
- **Esplora risorse di Azure** : per interagire con il dispositivo Plug and Play, è possibile usare lo strumento Azure Internet Explorer. [Scaricare e installare la versione di Azure IoT Explorer più recente](./howto-use-iot-explorer.md) per il sistema operativo in uso. Configurare lo strumento Azure Internet Explorer in modo da connettersi all'hub Internet e cercare le definizioni di modello nella cartella *pnpbridge\docs\schemas* del **plug-in-and-Play-Bridge** che è stato clonato.

## <a name="configure-a-bridge"></a>Configurare un Bridge

Esistono due modi per configurare il Bridge:

- Se il Bridge viene eseguito in modalità nativa su un dispositivo o un gateway Internet, usare il file di configurazione. Questo scenario può usare un computer Linux o Windows.
- Se il Bridge viene eseguito come modulo IoT Edge nel runtime IoT Edge, usare la proprietà desiderata del modulo gemello. Questo scenario presuppone che il runtime di IoT Edge sia ospitato in un ambiente Linux.

### <a name="configuration-file"></a>File di configurazione

Quando Plug and Play Bridge viene eseguito in modalità nativa su un dispositivo o un gateway Internet, usa un file di configurazione per:

- Ottenere le informazioni di connessione dell'hub IoT Central o Internet.
- Configurare i dispositivi per i quali vengono pubblicate le interfacce Plug and Play.

Usare una delle opzioni seguenti per fornire il file di configurazione al Bridge:

- Passare il percorso del file di configurazione come parametro della riga di comando all'eseguibile del Bridge.
- Usare il *config.js* esistente nel file nella cartella *pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* .

Lo [schema del file di configurazione](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) è disponibile nel repository GitHub.

> [!TIP]
> Se si modifica un file di configurazione del Bridge in VS Code, è possibile utilizzare questo file di schema per convalidare il file.

### <a name="iot-edge-module-configuration"></a>Configurazione del modulo IoT Edge

Quando il Bridge viene eseguito come modulo IoT Edge in un runtime IoT Edge, il file di configurazione viene inviato dal cloud come aggiornamento alla `PnpBridgeConfig` proprietà desiderata. Il Bridge attende che questa proprietà venga aggiornata prima di configurare gli adapter e i componenti.

## <a name="extend-the-bridge"></a>Estendere il Bridge

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

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>Compilare ed eseguire il Bridge su un dispositivo o un gateway Internet

| Piattaforma | Supportato |
| :-----------: | :-----------: |
| Windows |  Sì |
| Linux | Sì |

### <a name="prerequisites"></a>Prerequisiti

Per completare questa sezione, è necessario installare il software seguente nel computer locale:

- Un ambiente di sviluppo che supporta la compilazione di C++, ad esempio [Visual Studio (community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/) , assicurarsi di includere il componente Gestione pacchetti NuGet e il carico **di lavoro sviluppo di applicazioni desktop con C++** quando si installa Visual Studio.
- [CMake](https://cmake.org/download/) : quando si installa CMake, selezionare l'opzione **Aggiungi CMake al percorso di sistema**.
- Se si esegue la compilazione in Windows, è anche necessario scaricare [windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk).

### <a name="source-code"></a>Codice sorgente

Clonare il repository di [bridge plug and Play](https://github.com/Azure/iot-plug-and-play-bridge) nel computer locale:

```cmd/sh
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Si prevede che l'esecuzione del comando precedente imprenda diversi minuti.

> [!NOTE]
> Se si verificano problemi con l' `git submodule update` errore in Windows, provare a eseguire il comando seguente per risolvere il problema: `git config --system core.longpaths true` .

### <a name="build-the-bridge-on-windows"></a>Compilare il Bridge in Windows

Aprire il **prompt dei comandi per gli sviluppatori per Visual studio 2019** e passare alla cartella che contiene il repository clonato ed eseguire i comandi seguenti:

```cmd
cd pnpbridge\scripts\windows

build.cmd
```

Si prevede che l'esecuzione del comando precedente imprenda diversi minuti.

Facoltativamente, è possibile aprire il file della soluzione *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge. sln* generato in Visual Studio per modificare, ricompilare ed eseguire il debug del codice.

> [!TIP]
> Questo progetto usa CMAKe per generare i file di progetto. Eventuali modifiche apportate al progetto in Visual Studio potrebbero andare perse se i file CMAKe appropriati non vengono aggiornati.

### <a name="build-the-bridge-on-linux"></a>Compilare il Bridge in Linux

Usando la shell bash, passare alla cartella che contiene il repository clonato ed eseguire i comandi seguenti:

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>Configurare i sensori generici

Modificare i seguenti parametri sotto il `pnp_bridge_connection_parameters` nodo nella *config.js* file nella cartella *IOT-plug-and-Play-bridge\pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* in Windows o nella cartella Internet e nella cartella Internet degli elementi, ovvero *plug-and-Play-Bridge/pnpbridge/CMake/pnpbridge_linux \ src/pnpbridge/Samples/console* in Windows:

Se si usa una stringa di connessione per connettersi all'hub Internet delle cose:

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> Il `symmetric_key` valore deve corrispondere alla chiave SAS nella stringa di connessione.

Se si usa DPS per connettersi all'hub Internet delle cose o IoT Central applicazione:

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

Esaminare il resto del file di configurazione per vedere quali componenti dell'interfaccia e i parametri globali sono configurati in questo esempio.

### <a name="start-the-bridge-in-windows"></a>Avviare il Bridge in Windows

Avviare il Bridge eseguendolo al prompt dei comandi:

```cmd
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> Per usare un file di configurazione da un percorso diverso, passare il relativo percorso come parametro della riga di comando quando si esegue il Bridge.
  
> [!TIP]
> Se è presente una fotocamera incorporata o una fotocamera USB connessa al PC, è possibile avviare un'applicazione che usa la fotocamera, ad esempio l'app della **fotocamera** incorporata. Quando l'app della **fotocamera** viene eseguita, l'output della console Bridge Mostra le statistiche di monitoraggio e la frequenza dei fotogrammi di viene segnalata tramite l'interfaccia del dispositivo gemello digitale all'hub Internet.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>Compilare ed eseguire il Bridge come modulo IoT Edge

| Piattaforma | Supportato |
| :-----------: | :-----------: |
| Windows |  No |
| Linux | Sì |

### <a name="prerequisites"></a>Prerequisiti

Per completare questa sezione, è necessario disporre di un hub di Azure. Per informazioni su come creare un hub Internet delle cose, vedere [creare un hub](../iot-hub/iot-hub-create-through-portal.md)Internet.

I passaggi descritti in questa sezione presuppongono che sia presente l'ambiente di sviluppo seguente in un computer Windows 10. Questi strumenti consentono di creare e distribuire un modulo IoT Edge nel dispositivo IoT Edge:

- Sottosistema Windows per Linux (WSL) 2 che esegue Ubuntu 18,04 LTS. Per ulteriori informazioni, vedere la [Guida all'installazione del sottosistema Windows per Linux per Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).
- Docker desktop per Windows configurato per l'uso di WSL 2. Per altre informazioni, vedere [Docker desktop WSL 2 backend](https://docs.docker.com/docker-for-windows/wsl/).
- [Visual Studio Code installato nell'ambiente Windows](https://code.visualstudio.com/docs/setup/windows) con le seguenti tre estensioni installate:

  - [Remote Development Extension Pack](https://aka.ms/vscode-remote/download/extension) : questa estensione consente di compilare ed eseguire codice in WSL 2.
  - [Docker per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) : questa estensione deve essere abilitata nell'ambiente **WSL: Ubuntu-18,04** di vs code.
  - [Strumenti di Azure per la Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) : questa estensione deve essere abilitata nell'ambiente **WSL: Ubuntu-18,04** di vs code.

- Eseguire i comandi seguenti nell'ambiente WSL 2 per installare gli strumenti di compilazione necessari:

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- INTERFACCIA della riga di comando di [Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest&preserve-view=true) installata nell'ambiente WSL 2 per gestire le risorse di Azure.

  > [!TIP]
  > Se si preferisce, è possibile eseguire i `az` comandi nel [Azure cloud Shell](https://shell.azure.com/) in cui è preinstallata l'interfaccia della riga di comando.

### <a name="create-an-iot-edge-device"></a>Creare un dispositivo IoT Edge

I comandi seguenti consentono di creare un dispositivo IoT Edge in esecuzione in una macchina virtuale di Azure. L'esecuzione di un dispositivo IoT Edge in una macchina virtuale è utile per il test della distribuzione se non si ha accesso a un dispositivo reale.

Per creare una registrazione del dispositivo IoT Edge nell'hub Internet, eseguire i comandi seguenti nell'ambiente WSL 2. Usare il `az login` comando per accedere alla sottoscrizione di Azure:

```bash
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

Per creare una macchina virtuale di Azure con il runtime di IoT Edge installato, eseguire i comandi seguenti. Aggiornare i segnaposto con i valori appropriati:

```bash
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

A questo punto il runtime di IoT Edge è in esecuzione in una macchina virtuale. È possibile usare il comando seguente per verificare che il **$edgeAgent** e il **$edgeHub** siano in esecuzione nel dispositivo:

```bash
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> Viene addebitata la fatturazione mentre la macchina virtuale è in esecuzione. Assicurarsi di arrestarlo quando non lo si usa e rimuoverlo al termine dell'operazione.

### <a name="download-the-source-code"></a>Scaricare il codice sorgente

Nei passaggi seguenti si compila l'immagine Docker nell'ambiente WSL 2. Per clonare il repository internet delle cose **-plug-and-Play-Bridge** , eseguire i comandi seguenti in una shell WSL 2 bash in una cartella appropriata:

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

Si prevede che l'esecuzione del comando precedente imprenda diversi minuti.

### <a name="update-the-dockerfile"></a>Aggiornare *Dockerfile*

Avviare VS Code, aprire il riquadro comandi, immettere *Remote WSL: Apri cartella in WSL* e quindi aprire *la cartella Internet degli strumenti* , che contiene il repository clonato.

Aprire il file *pnpbridge\Dockerfile.amd64* . Modificare le definizioni delle variabili di ambiente come segue:

```dockerfile
ENV IOTHUB_DEVICE_CONNECTION_STRING="{Add your device connection string here}"
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> È possibile usare il comando seguente per recuperare la stringa di connessione del dispositivo: `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

Sono disponibili *dockerfile* di esempio per altre architetture.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>Creare l'immagine del modulo del Bridge Plug and Play

In VS Code aprire il riquadro comandi, immettere *Docker registrations: accedere a Docker CLI*, quindi selezionare la sottoscrizione di Azure e il registro contenitori. Questo comando consente a Docker di connettersi al registro contenitori e caricare l'immagine del modulo.

Aprire il *pnpbridge/module.jssu* file. Aggiungere `{your container registry name}.azurecr.io/iotpnpbridge` come archivio di immagini del contenitore e `v1` come versione.

In VS Code, fare clic con il pulsante destro del mouse *su pnpbridge/module.jssu* file nella visualizzazione **Esplora** , selezionare **Compila e push IOT Edge immagine del modulo** e selezionare **amd64** come piattaforma.

In VS Code, nella visualizzazione **Docker** è possibile esplorare il contenuto del registro contenitori che ora include l'immagine del modulo **iotpnpbridge: V1-amd64** .

### <a name="create-a-container-registry"></a>Creare un registro contenitori

Un dispositivo IoT Edge Scarica le immagini del modulo da un registro contenitori. Questo esempio usa un registro contenitori di Azure.

Creare un registro contenitori di Azure nel gruppo di risorse **Bridge-Edge-Resources** . Abilitare quindi l'accesso amministrativo al registro contenitori e ottenere le credenziali necessarie al dispositivo IoT Edge per scaricare le immagini del modulo:

```bash
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>Creare il manifesto di distribuzione

Un manifesto di distribuzione IoT Edge specifica i moduli e i valori di configurazione da distribuire in un dispositivo IoT Edge.

In VS Code aprire il file *pnpbridge/deployment.template.js* :

- Aggiornare `registryCredentials` con i valori del comando precedente. Il `address` valore è simile a `{your container registry name}.azurecr.io` .
- Aggiornare il `image` valore per `ModulePnpBridge` . L'immagine del modulo ha un aspetto simile al seguente: `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` .
- Sostituire `PnPBridgeConfig` con il codice JSON seguente per configurare la scheda di rilevamento CO2:

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  Salvare le modifiche.

In VS Code fare clic con il pulsante destro del mouse sul file *pnpbridge/deployment.template.js* nella visualizzazione **Esplora** , quindi selezionare **genera IOT Edge manifesto di distribuzione**. Questo comando genera *pnpbridge/config/deployment.amd64.jsnel* manifesto della distribuzione.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>Distribuire il Bridge al dispositivo IoT Edge

In VS Code aprire il riquadro comandi, immettere hub tutto di *Azure: selezionare Hub* tutto, quindi selezionare la sottoscrizione e l'hub.

In VS Code fare clic con il pulsante destro del mouse *su pnpbridge/config/deployment.amd64.json* file nella visualizzazione **Explorer** , scegliere **Crea distribuzione per singolo dispositivo** e selezionare **Bridge-Edge-Device**.

Per visualizzare lo stato dei moduli nel dispositivo, eseguire il comando seguente:

```bash
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

L'elenco dei moduli in esecuzione include ora il modulo **ModulePnpBridge** configurato per l'uso della scheda di rilevamento CO2.

### <a name="tidy-up"></a>Operazioni finali

Per rimuovere la macchina virtuale e il registro contenitori dalla sottoscrizione di Azure, eseguire il comando seguente:

```bash
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Struttura di cartelle

*pnpbridge\deps\azure-IOT-SDK-c-PNP*: moduli secondari git che contengono Azure Internet per dispositivi SDK per c SDK.

*pnpbridge\scripts*: script di compilazione.

*pnpbridge\src*: codice sorgente per Internet delle cose plug and Play Bridge core.

*pnpbridge\src\adapters*: codice sorgente per diversi adattatori plug and Play Bridge.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul Bridge Plug and Play, visitare il repository GitHub [plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) .
