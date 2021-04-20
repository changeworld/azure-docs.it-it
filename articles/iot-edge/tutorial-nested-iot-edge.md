---
title: Esercitazione - Creare una gerarchia di dispositivi IoT Edge - Azure IoT Edge
description: Questa esercitazione illustra come creare una struttura gerarchica di dispositivi IoT Edge usando i gateway.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 44fe6bb3787e1fe0df7ccf83200497b46c473568
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728501"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Esercitazione: Creare una gerarchia di IoT Edge dispositivi

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Questa esercitazione illustra come distribuire nodi di Azure IoT Edge in reti organizzate in livelli gerarchici. Ogni livello di una gerarchia è un dispositivo gateway che gestisce messaggi e richieste provenienti da dispositivi del livello sottostante.

È possibile strutturare una gerarchia di dispositivi in modo che solo il livello superiore disponga della connettività al cloud e che i livelli inferiori possano comunicare solo con i livelli a nord e a sud adiacenti. Questa suddivisione in livelli della rete è alla base della maggior parte delle reti industriali, che seguono lo [standard ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

L'obiettivo di questa esercitazione è creare una gerarchia di IoT Edge che simula un ambiente di produzione semplificato. Alla fine dell'esercitazione si distribuirà il [modulo Simulated Temperature Sensor](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) in un dispositivo di livello inferiore senza accesso a Internet scaricando le immagini del contenitore attraverso la gerarchia.

Per realizzare questo obiettivo, questa esercitazione illustra nel dettaglio la procedura di creazione di una gerarchia di dispositivi IoT Edge, la distribuzione di contenitori di runtime IoT Edge nei dispositivi e la configurazione locale dei dispositivi. In questa esercitazione si usa uno strumento di configurazione automatizzato per:

> [!div class="checklist"]
>
> * Creare e definire le relazioni in una gerarchia di dispositivi IoT Edge.
> * Configurare il runtime IoT Edge nei dispositivi della gerarchia.
> * Installare certificati coerenti nella gerarchia dei dispositivi.
> * Aggiungere carichi di lavoro ai dispositivi della gerarchia.
> * Usare il [IoT Edge proxy API per](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) instradare in modo sicuro il traffico HTTP su una singola porta dai dispositivi di livello inferiore.

>[!TIP]
>Questa esercitazione include una combinazione di passaggi manuali e automatizzati per offrire una dimostrazione delle funzionalità IoT Edge annidate.
>
>Per un'occhiata completamente automatizzata alla configurazione di una gerarchia di dispositivi IoT Edge, è possibile seguire l'esempio di Azure IoT Edge per [l'IoT industriale.](https://aka.ms/iotedge-nested-sample) Questo scenario con script distribuisce le macchine virtuali di Azure come dispositivi preconfigurati per simulare un ambiente factory.
>
>Per un'analisi approfondita dei passaggi manuali per creare e gestire una gerarchia di dispositivi IoT Edge, vedere la guida alle procedure sulle gerarchie dei gateway IoT Edge [dispositivo.](how-to-connect-downstream-iot-edge-device.md)

In questa esercitazione vengono definiti i livelli di rete seguenti:

* **Livello superiore**: i dispositivi IoT Edge in questo livello possono connettersi direttamente al cloud.

* **Livelli inferiori:** IoT Edge dispositivi a livelli inferiori al livello superiore non possono connettersi direttamente al cloud. Devono passare attraverso uno o più dispositivi IoT Edge intermediari per inviare e ricevere dati.

Questa esercitazione usa una gerarchia di due dispositivi per semplicità, illustrata di seguito. Un dispositivo, il **dispositivo di livello superiore,** rappresenta un dispositivo al livello superiore della gerarchia, che può connettersi direttamente al cloud. Questo dispositivo è detto anche **dispositivo padre**. L'altro dispositivo, il **dispositivo di** livello inferiore, rappresenta un dispositivo al livello inferiore della gerarchia, che non può connettersi direttamente al cloud. È possibile aggiungere più dispositivi di livello inferiore per rappresentare l'ambiente di produzione, in base alle esigenze. I dispositivi a livelli inferiori verranno definiti anche **dispositivi figlio.**

![Struttura della gerarchia dell'esercitazione, contenente due dispositivi: il dispositivo di livello superiore e il dispositivo di livello inferiore](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Prerequisiti

Per creare una gerarchia di dispositivi IoT Edge occorre:

* Un computer (Windows o Linux) con connettività Internet.
* Un account Azure con una sottoscrizione valida. Se non si ha una [sottoscrizione di Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Una shell Bash in Azure Cloud Shell l'interfaccia della riga di comando di Azure v2.3.1 con l'estensione Azure IoT versione 0.10.6 o successiva installata. Questa esercitazione usa [Azure Cloud Shell](../cloud-shell/overview.md). Se non si ha familiarità con Azure Cloud Shell, [consultare una guida di avvio rapido per informazioni dettagliate](./quickstart-linux.md#prerequisites).
  * Per visualizzare le versioni correnti dei moduli e delle estensioni dell'interfaccia della riga di comando di Azure, eseguire [az version](/cli/azure/reference-index?#az_version).
* Un dispositivo Linux da configurare come IoT Edge dispositivo per ogni dispositivo nella gerarchia. Questa esercitazione usa due dispositivi. Se non sono disponibili dispositivi, è possibile creare macchine virtuali di Azure per ogni dispositivo nella gerarchia usando il comando seguente.

   Sostituire il testo segnaposto nel comando seguente ed eseguirlo due volte, una volta per ogni macchina virtuale. Ogni macchina virtuale necessita di un prefisso DNS univoco, che fungerà anche da nome. Il prefisso DNS deve essere conforme all'espressione regolare seguente: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

   ```bash
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   La macchina virtuale usa chiavi SSH per l'autenticazione degli utenti. Se non si ha familiarità con la creazione e l'uso di chiavi SSH, è possibile seguire le istruzioni per le coppie di chiavi [SSH pubblica-privata](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)per le macchine virtuali Linux in Azure.

   IoT Edge versione 1.2 è preinstallata con questo modello arm, senza dover installare manualmente gli asset nelle macchine virtuali. Se si installa IoT Edge nei propri dispositivi, vedere [Installare Azure IoT Edge per Linux (versione 1.2)](how-to-install-iot-edge.md) o Aggiornare IoT Edge alla versione [1.2.](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)

   Una creazione corretta di una macchina virtuale usando questo modello arm restituisce l'handle e il nome di dominio completo della macchina `SSH` virtuale ( `FQDN` ). Nei passaggi successivi si useranno l'handle SSH e l'FQDN o l'indirizzo IP di ogni macchina virtuale per la configurazione, quindi tenere traccia di queste informazioni. Di seguito è illustrato un output di esempio.

   >[!TIP]
   >È anche possibile trovare l'indirizzo IP e il nome di dominio completo nella portale di Azure. Per l'indirizzo IP, passare all'elenco di macchine virtuali e prendere nota del **campo Indirizzo IP pubblico**. Per il nome di dominio completo, passare alla pagina di panoramica di ogni macchina virtuale e cercare il **campo Nome DNS.**

   ![La macchina virtuale restituisce un file JSON al momento della creazione, che contiene il relativo handle SSH](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Assicurarsi che le porte seguenti siano aperte in ingresso per tutti i dispositivi ad eccezione del dispositivo di livello più basso: 8000, 443, 5671, 8883:
  * 8000: usata per eseguire il pull delle immagini di contenitori Docker tramite il proxy API.
  * 443: usata tra gli hub perimetrali padre e figlio per le chiamate API REST.
  * 5671, 8883: usate per AMQP e MQTT.

  Per altre informazioni, vedere [come aprire porte a una macchina virtuale con](../virtual-machines/windows/nsg-quickstart-portal.md)il portale di Azure .

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configurare la gerarchia di dispositivi IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Creare una gerarchia di dispositivi IoT Edge

IoT Edge dispositivi costituiscono i livelli della gerarchia. Questa esercitazione creerà una gerarchia di due  dispositivi IoT Edge: il dispositivo di livello superiore e il relativo dispositivo figlio, il **dispositivo di livello inferiore.** È possibile creare altri dispositivi figlio in base alle esigenze.

Per creare e configurare la gerarchia IoT Edge dispositivi, si userà lo `iotedge-config` strumento . Questo strumento semplifica la configurazione della gerarchia automatizzando e condensando diversi passaggi in due:

1. Configurazione della configurazione cloud e preparazione di ogni configurazione del dispositivo, che include:

   * Creazione di dispositivi nell'hub IoT
   * Impostazione delle relazioni padre-figlio per autorizzare la comunicazione tra dispositivi
   * Generazione di una catena di certificati per ogni dispositivo per stabilire una comunicazione sicura tra di essi
   * Generazione di file di configurazione per ogni dispositivo

1. Installazione di ogni configurazione del dispositivo, che include:

   * Installazione di certificati in ogni dispositivo
   * Applicazione dei file di configurazione per ogni dispositivo

Lo `iotedge-config` strumento effettua anche automaticamente le distribuzioni del modulo IoT Edge dispositivo.

Per usare lo strumento `iotedge-config` per creare e configurare la gerarchia, seguire questa procedura nell'interfaccia della riga di comando di Azure:

1. Nel [Azure Cloud Shell](https://shell.azure.com/)creare una directory per le risorse dell'esercitazione:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Scaricare la versione dello strumento di configurazione e dei modelli di configurazione:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Verrà creata la cartella `iotedge_config_cli_release` nella directory dell'esercitazione.

   Il file modello usato per creare la gerarchia dei dispositivi è il `iotedge_config.yaml` file disponibile in `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . Nella stessa directory è presente un file di distribuzione JSON contenente le istruzioni per i moduli `deploymentLowerLayer.json` da distribuire nel dispositivo di livello **inferiore.** Il file è lo stesso, ma per il dispositivo di livello superiore i moduli distribuiti in ogni `deploymentTopLayer.json` dispositivo non sono uguali.  Il file è un modello per IoT Edge di dispositivi e verrà usato per generare automaticamente i bundle di configurazione per i `device_config.toml` dispositivi nella gerarchia.

   Se si desidera esaminare il codice sorgente e gli script per lo strumento, vedere il repository Azure-Samples `iotedge-config` [su GitHub.](https://github.com/Azure-Samples/iotedge_config_cli)

1. Aprire il modello di configurazione dell'esercitazione e modificarlo con le informazioni seguenti:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   Nella sezione **iothub** popolare i campi `iothub_hostname` e con le `iothub_name` informazioni. Queste informazioni sono disponibili nella pagina di panoramica dell'hub IoT nel portale di Azure.

   Nella sezione certificati **facoltativi** è possibile popolare i campi con i percorsi assoluti del certificato e della chiave. Se si lasciano vuoti questi campi, lo script genererà automaticamente certificati di test autofirmati per l'uso. Se non si ha familiarità con [l'uso](how-to-connect-downstream-iot-edge-device.md#prepare-certificates)dei certificati in uno scenario gateway, vedere la sezione relativa ai certificati della guida.

   Nella sezione **di** configurazione è `template_config_path` il percorso del modello usato per creare le `device_config.toml` configurazioni del dispositivo. Il campo determina quali dispositivi di livello inferiore dell'immagine dell'agente `default_edge_agent` Edge estrarranno e da dove.

   Nella sezione **edgedevices,** per uno scenario di produzione, è possibile modificare l'albero della gerarchia in base alla struttura desiderata. Ai fini di questa esercitazione, accettare l'albero predefinito. Per ogni dispositivo è presente un `device_id` campo in cui è possibile assegnare un nome ai dispositivi. È presente anche il `deployment` campo , che specifica il percorso del file JSON di distribuzione per il dispositivo.

   È anche possibile registrare manualmente IoT Edge dispositivi nell'hub IoT tramite portale di Azure o Azure Cloud Shell. Per informazioni su come, [vedere la guida su come registrare un IoT Edge dispositivo](how-to-register-device.md).

   È possibile definire manualmente anche le relazioni padre-figlio. Per altre [informazioni, vedere](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) la sezione creare una gerarchia di gateway nella guida alle modalità.

   ![La sezione edgedevices del file di configurazione consente di definire la gerarchia](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Salvare e chiudere il file:

   `CTRL + S`, `CTRL + Q`

1. Creare una directory di output per i bundle di configurazione nella directory delle risorse dell'esercitazione:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Passare alla `iotedge_config_cli_release` directory ed eseguire lo strumento per creare la gerarchia di IoT Edge dispositivi:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Con il flag , lo strumento crea i certificati del dispositivo, i bundle di certificati e un `--output` file di log in una directory di propria scelta. Con il flag impostato, lo strumento cerca automaticamente i dispositivi IoT Edge esistenti nell'hub IoT e li rimuove, per evitare errori e mantenere pulito `-f` l'hub.

   Lo strumento di configurazione crea IoT Edge dispositivi e configura le relazioni padre-figlio tra di essi. Facoltativamente, crea i certificati che i dispositivi possono usare. Se vengono specificati percorsi per la distribuzione di JSON, lo strumento creerà automaticamente queste distribuzioni nei dispositivi, ma questa operazione non è necessaria. Infine, lo strumento genererà i bundle di configurazione per i dispositivi e li inserirà nella directory di output. Per informazioni approfondite sulla procedura eseguita dallo strumento di configurazione, vedere il file di log nella directory di output.

   ![Lo script visualizza una topologia della gerarchia al momento dell'esecuzione](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Verificare che l'output della topologia dello script sia corretto. Una volta che la gerarchia è strutturata correttamente, è possibile procedere.

### <a name="configure-the-iot-edge-runtime"></a>Configurare il runtime di IoT Edge

Oltre al provisioning dei dispositivi, i passaggi di configurazione stabiliscono una comunicazione attendibile tra i dispositivi nella gerarchia usando i certificati creati in precedenza. I passaggi iniziano anche a stabilire la struttura di rete della gerarchia. Il dispositivo di livello superiore manterrà la connettività Internet, consentendo di eseguire il pull delle immagini per il runtime dal cloud, mentre i dispositivi di livello inferiore instraderanno attraverso il dispositivo di livello superiore per accedere a queste immagini.

Per configurare il runtime IoT Edge, è necessario applicare i bundle di configurazione creati dallo script di installazione ai dispositivi. Le configurazioni sono  leggermente diverse tra il dispositivo di livello superiore e un dispositivo di livello **inferiore,** quindi tenere presente il file di configurazione del dispositivo da applicare a ogni dispositivo.

1. Ogni dispositivo richiede il bundle di configurazione corrispondente. È possibile usare un'unità USB o [una copia sicura del file](https://www.ssh.com/ssh/scp/) per spostare i bundle di configurazione in ogni dispositivo.

   Assicurarsi di inviare il bundle di configurazione corretto a ogni dispositivo.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Indica `:~` che la cartella di configurazione verrà inserita nella home directory della macchina virtuale.

1. Accedere alla macchina virtuale per applicare il bundle di configurazione al dispositivo:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. In ogni dispositivo decomprimere il bundle di configurazione. È prima necessario installare zip:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. In ogni dispositivo applicare il bundle di configurazione al dispositivo:

   ```bash
   sudo ./install.sh
   ```

   Nel dispositivo **di livello superiore** verrà visualizzato un prompt per immettere il nome host. Nel dispositivo **di livello inferiore** verrà chiesto il nome host e il nome host dell'elemento padre. Specificare l'INDIRIZZO IP o FQDN appropriato per ogni richiesta. È possibile usare entrambi i dispositivi, ma essere coerenti nella scelta tra i dispositivi. L'output dello script di installazione è illustrato di seguito.

   Se si vuole esaminare in dettaglio le modifiche apportate al file di configurazione del dispositivo, vedere la sezione configurare IoT Edge nei dispositivi della [guida.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

  ![L'installazione dei bundle di configurazione aggiornerà i file config.toml nel dispositivo e riavvierà automaticamente tutti IoT Edge servizi](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Se i passaggi precedenti sono stati completati correttamente, è possibile verificare che i dispositivi siano configurati correttamente.

Eseguire i controlli di configurazione e connettività nei dispositivi. Per il **dispositivo di livello superiore:**

   ```bash
   sudo iotedge check
   ```

Per il **dispositivo di livello inferiore,** l'immagine di diagnostica deve essere passata manualmente nel comando :

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

Nel dispositivo **di livello superiore** si prevede di visualizzare un output con diverse valutazioni di passaggio. È possibile che vengano visualizzati alcuni avvisi sui criteri di log e, a seconda della rete, dei criteri DNS.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Quando si è soddisfatti della correttezza delle configurazioni in ogni dispositivo, è possibile procedere.

## <a name="deploy-modules-to-your-devices"></a>Distribuire moduli nei dispositivi

Le distribuzioni del modulo nei dispositivi sono state generate automaticamente al momento della creazione dei dispositivi. Lo `iotedge-config-cli` strumento ha alimentato i JSON di distribuzione per i dispositivi di **livello** superiore e inferiore dopo la creazione. La distribuzione del modulo era in sospeso durante la configurazione del runtime IoT Edge in ogni dispositivo. Dopo aver configurato il runtime, sono iniziate le distribuzioni nel **dispositivo di livello** superiore. Al termine delle distribuzioni, il **dispositivo di** livello inferiore potrebbe usare il modulo **proxy API** IoT Edge per eseguire il pull delle immagini necessarie.

Nella [Azure Cloud Shell](https://shell.azure.com/)è possibile esaminare il codice  JSON di distribuzione del dispositivo di livello superiore per comprendere quali moduli sono stati distribuiti nel dispositivo:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Oltre ai moduli di runtime IoT Edge **Agent** e IoT Edge  **Hub**, il dispositivo di livello superiore riceve il modulo del registro **Docker** e IoT Edge **proxy API.**

Il **modulo del registro Docker** punta a un Registro Azure Container. In questo caso, `REGISTRY_PROXY_REMOTEURL` punta alla Microsoft Container Registry. In `createOptions` è possibile vedere che comunica sulla porta 5000.

Il **IoT Edge proxy API** instrada le richieste HTTP ad altri moduli, consentendo ai dispositivi di livello inferiore di eseguire il pull di immagini del contenitore o di eseguire il push di BLOB nell'archiviazione. In questa esercitazione comunica sulla porta 8000 ed è configurato per inviare la route delle richieste pull dell'immagine del contenitore Docker al modulo del registro **Docker** sulla porta 5000. Inoltre, tutte le richieste di caricamento dell'archiviazione BLOB vengono indirizzate al modulo AzureBlobStorageonIoTEdge sulla porta 11002. Per altre informazioni sul **IoT Edge proxy API** e su come configurarlo, vedere la guida alle modalità del [modulo.](how-to-configure-api-proxy-module.md)

Per informazioni su come creare una distribuzione come questa tramite portale di Azure o Azure Cloud Shell, vedere la sezione sul dispositivo di livello superiore della guida [alle procedura.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices)

Nel [Azure Cloud Shell](https://shell.azure.com/), è possibile esaminare il  codice JSON di distribuzione del dispositivo di livello inferiore per comprendere quali moduli sono stati distribuiti nel dispositivo:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

È possibile vedere in che i moduli di runtime del dispositivo di livello inferiore sono impostati per eseguire il pull da , anziché da , come ha fatto `systemModules` il dispositivo di  `$upstream:8000` `mcr.microsoft.com` **livello** superiore. Il **dispositivo di** livello inferiore invia un'immagine Docker che richiede il modulo proxy API **IoT Edge** sulla porta 8000, perché non può eseguire direttamente il pull delle immagini dal cloud. Anche l'altro modulo distribuito nel **dispositivo di** livello inferiore, il modulo **Simulated Temperature Sensor,** effettua la richiesta di immagine a `$upstream:8000` .

Per un'occhiata a come creare una distribuzione come questa tramite portale di Azure o Azure Cloud Shell, vedere la sezione del dispositivo di livello inferiore della [guida.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices)

È possibile visualizzare lo stato dei moduli usando il comando :

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Questo comando restituisce tutte le proprietà segnalate di edgeAgent. Di seguito sono riportati alcuni utili per monitorare lo stato del dispositivo: stato del *runtime,* ora di inizio del *runtime,* ora dell'ultima uscita del *runtime,* *conteggio riavvii di runtime.*

È anche possibile visualizzare lo stato dei moduli nella portale di Azure [.](https://ms.portal.azure.com/) Passare alla sezione **IoT Edge** dell'hub IoT per visualizzare i dispositivi e i moduli.

Dopo aver soddisfatto le distribuzioni del modulo, è possibile procedere.

## <a name="view-generated-data"></a>Visualizzare i dati generati

Il modulo **Simulated Temperature Sensor** ei chi è stato eseguito il push genera dati dell'ambiente di esempio. Invia messaggi che includono la temperatura e l'umidità dell'ambiente, la temperatura e la pressione dell'apparecchiatura e un timestamp.

È anche possibile visualizzarli tramite [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Eseguire il `iotedge check` comando per verificare la configurazione e risolvere gli errori.

È possibile eseguire `iotedge check` in una gerarchia annidata, anche se i computer figlio non hanno accesso diretto a Internet.

Quando si esegue dal livello inferiore, il programma tenta di eseguire il pull dell'immagine `iotedge check` dall'elemento padre tramite la porta 443.

In questa esercitazione viene utilizzata la porta 8000, quindi è necessario specificarla:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2
```

Il valore `azureiotedge-diagnostics` viene estratto dal registro contenitori collegato al modulo del registro. In questa esercitazione è impostato per impostazione predefinita su https://mcr.microsoft.com:

| Nome | Valore |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Se si usa un registro contenitori privato, assicurarsi che tutte le immagini (IoTEdgeAPIProxy, edgeAgent, edgeHub, Simulated Temperature Sensor e diagnostica) siano presenti nel registro contenitori.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare le configurazioni locali e le risorse di Azure create in questo articolo per evitare addebiti.

Per eliminare le risorse:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Selezionare il nome del gruppo di risorse contenente le risorse di test di IoT Edge. 

3. Esaminare l'elenco delle risorse contenute nel gruppo di risorse. Per eliminarle tutte, è possibile selezionare **Elimina gruppo di risorse**. Se se ne vogliono eliminare solo alcune, è possibile fare clic su ogni risorsa per eliminarle singolarmente. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati configurati due dispositivi IoT Edge come gateway, di cui uno è stato impostato come dispositivo padre dell'altro. È stato quindi illustrato come eseguire il pull di un'immagine del contenitore nel dispositivo figlio tramite un gateway usando il IoT Edge proxy API. Per altre informazioni, vedere la guida [sulle modalità d'uso](how-to-configure-api-proxy-module.md) del modulo proxy.

Per altre informazioni sull'uso dei gateway per creare livelli gerarchici di dispositivi IoT Edge, vedere la guida alle modalità di connessione [downstream IoT Edge dispositivi](how-to-connect-downstream-iot-edge-device.md).

È possibile continuare con le altre esercitazioni per scoprire in che modo Azure IoT Edge può creare altre soluzioni per il business.

> [!div class="nextstepaction"]
> [Distribuire un modello di Azure Machine Learning come modulo](tutorial-deploy-machine-learning.md)
