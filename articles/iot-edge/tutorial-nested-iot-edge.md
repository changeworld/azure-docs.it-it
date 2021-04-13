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
ms.openlocfilehash: bfecc88dc0c504cee615f1a3d35f9208aeb724f8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309192"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Esercitazione: creare una gerarchia di dispositivi IoT Edge

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Questa esercitazione illustra come distribuire nodi di Azure IoT Edge in reti organizzate in livelli gerarchici. Ogni livello di una gerarchia è un dispositivo gateway che gestisce messaggi e richieste provenienti da dispositivi del livello sottostante.

È possibile strutturare una gerarchia di dispositivi in modo che solo il livello superiore disponga della connettività al cloud e che i livelli inferiori possano comunicare solo con i livelli a nord e a sud adiacenti. Questa suddivisione in livelli della rete è alla base della maggior parte delle reti industriali, che seguono lo [standard ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

L'obiettivo di questa esercitazione è creare una gerarchia di dispositivi IoT Edge che simula un ambiente di produzione semplificato. Alla fine dell'esercitazione si distribuirà il [modulo Simulated Temperature Sensor](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) in un dispositivo di livello inferiore senza accesso a Internet scaricando le immagini del contenitore attraverso la gerarchia.

Per realizzare questo obiettivo, questa esercitazione illustra nel dettaglio la procedura di creazione di una gerarchia di dispositivi IoT Edge, la distribuzione di contenitori di runtime IoT Edge nei dispositivi e la configurazione locale dei dispositivi. In questa esercitazione si userà uno strumento di configurazione automatizzato per:

> [!div class="checklist"]
>
> * Creare e definire le relazioni in una gerarchia di dispositivi IoT Edge.
> * Configurare il runtime IoT Edge nei dispositivi della gerarchia.
> * Installare certificati coerenti nella gerarchia dei dispositivi.
> * Aggiungere carichi di lavoro ai dispositivi della gerarchia.
> * Usare il [modulo proxy API IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) per instradare in modo sicuro il traffico HTTP su una singola porta dai dispositivi di livello inferiore.

>[!TIP]
>Questa esercitazione include una combinazione di passaggi manuali e automatizzati per fornire una presentazione delle funzionalità annidate IoT Edge.
>
>Se si desidera un aspetto completamente automatico della configurazione di una gerarchia di IoT Edge dispositivi, è possibile seguire l' [esempio di Azure IOT Edge per l'uso di un sacco industriale](https://aka.ms/iotedge-nested-sample). Questo scenario con script distribuisce le macchine virtuali di Azure come dispositivi preconfigurati per simulare un ambiente Factory.
>
>Per un esame approfondito dei passaggi manuali per creare e gestire una gerarchia di dispositivi IoT Edge, vedere [la Guida alle procedure per IOT Edge gerarchie del gateway del dispositivo](how-to-connect-downstream-iot-edge-device.md).

In questa esercitazione vengono definiti i livelli di rete seguenti:

* **Livello superiore**: i dispositivi IoT Edge in questo livello possono connettersi direttamente al cloud.

* **Livelli inferiori**: i dispositivi IOT Edge ai livelli al di sotto del livello superiore non possono connettersi direttamente al cloud. Devono passare attraverso uno o più dispositivi IoT Edge intermediari per inviare e ricevere dati.

Questa esercitazione usa una gerarchia di due dispositivi per semplicità, illustrata di seguito. Un dispositivo, il **dispositivo di livello superiore**, rappresenta un dispositivo al livello superiore della gerarchia, che può connettersi direttamente al cloud. Questo dispositivo è detto anche **dispositivo padre**. L'altro dispositivo, il **dispositivo di livello inferiore**, rappresenta un dispositivo a livello inferiore della gerarchia, che non è in grado di connettersi direttamente al cloud. È possibile aggiungere più dispositivi di livello inferiore per rappresentare l'ambiente di produzione, in base alle esigenze. I dispositivi a livelli inferiori verranno anche denominati **dispositivi figlio**.

![Struttura della gerarchia dell'esercitazione, contenente due dispositivi: il dispositivo di livello superiore e il dispositivo di livello inferiore](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Prerequisiti

Per creare una gerarchia di dispositivi IoT Edge occorre:

* Un computer (Windows o Linux) con connettività Internet.
* Un account Azure con una sottoscrizione valida. Se non si ha una [sottoscrizione di Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.
* Una shell bash in Azure Cloud Shell usando l'interfaccia della riga di comando di Azure v 2.3.1 con l'estensione Azure 0.10.6 o versione successiva installata. Questa esercitazione usa [Azure Cloud Shell](../cloud-shell/overview.md). Se non si ha familiarità con Azure Cloud Shell, [consultare una guida di avvio rapido per informazioni dettagliate](./quickstart-linux.md#prerequisites).
  * Per visualizzare le versioni correnti dei moduli e delle estensioni dell'interfaccia della riga di comando di Azure, eseguire [AZ Version](/cli/azure/reference-index?#az_version).
* Un dispositivo Linux da configurare come dispositivo IoT Edge per ogni dispositivo nella gerarchia. Questa esercitazione usa due dispositivi. Se non sono disponibili dispositivi, è possibile creare macchine virtuali di Azure per ogni dispositivo nella gerarchia usando il comando seguente.

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

   La macchina virtuale usa le chiavi SSH per l'autenticazione degli utenti. Se non si ha familiarità con la creazione e l'uso di chiavi SSH, è possibile seguire [le istruzioni per le coppie di chiavi SSH pubbliche e private per le macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

   IoT Edge versione 1,2 è preinstallata con questo modello ARM, risparmiando la necessità di installare manualmente gli asset nelle macchine virtuali. Se si sta installando IoT Edge nei propri dispositivi, vedere [installare Azure IOT Edge per Linux (versione 1,2)](how-to-install-iot-edge.md) o [aggiornare IoT Edge alla versione 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   Una corretta creazione di una macchina virtuale con questo modello ARM restituirà l'handle della macchina virtuale `SSH` e il nome di dominio completo ( `FQDN` ). Si utilizzerà l'handle SSH e il nome FQDN o l'indirizzo IP di ogni macchina virtuale per la configurazione nei passaggi successivi, quindi tenere traccia di queste informazioni. Di seguito è illustrato un esempio di output.

   >[!TIP]
   >È anche possibile trovare l'indirizzo IP e il nome di dominio completo nella portale di Azure. Per l'indirizzo IP, passare all'elenco di macchine virtuali e prendere nota del **campo indirizzo IP pubblico**. Per il nome di dominio completo, passare alla pagina Panoramica di ogni macchina virtuale e cercare il campo **nome DNS** .

   ![Alla creazione della macchina virtuale viene restituito un codice JSON, che contiene il relativo handle SSH](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Assicurarsi che le porte seguenti siano aperte in ingresso per tutti i dispositivi, ad eccezione del dispositivo di livello più basso: 8000, 443, 5671, 8883:
  * 8000: usata per eseguire il pull delle immagini di contenitori Docker tramite il proxy API.
  * 443: usata tra gli hub perimetrali padre e figlio per le chiamate API REST.
  * 5671, 8883: usate per AMQP e MQTT.

  Per ulteriori informazioni, vedere [come aprire le porte in una macchina virtuale con il portale di Azure](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configurare la gerarchia di dispositivi IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Creare una gerarchia di dispositivi IoT Edge

I dispositivi IoT Edge costituiscono i livelli della gerarchia. Questa esercitazione creerà una gerarchia di due dispositivi IoT Edge: il **dispositivo di livello superiore** e il relativo figlio, ovvero il **dispositivo di livello inferiore**. È possibile creare altri dispositivi figlio in base alle esigenze.

Per creare e configurare la gerarchia di dispositivi IoT Edge, si userà lo `iotedge-config` strumento. Questo strumento semplifica la configurazione della gerarchia automatizzando e condensando diversi passaggi in due:

1. Impostazione della configurazione cloud e preparazione di ogni configurazione del dispositivo, che include:

   * Creazione di dispositivi nell'hub Internet delle cose
   * Impostazione delle relazioni padre-figlio per autorizzare la comunicazione tra dispositivi
   * Generazione di una catena di certificati per ogni dispositivo per stabilire comunicazioni sicure tra di essi
   * Generazione dei file di configurazione per ogni dispositivo

1. Installazione di ogni configurazione del dispositivo, che include:

   * Installazione dei certificati in ogni dispositivo
   * Applicazione dei file di configurazione per ogni dispositivo

Lo `iotedge-config` strumento renderà automaticamente le distribuzioni dei moduli al dispositivo IOT Edge.

Per usare lo `iotedge-config` strumento per creare e configurare la gerarchia, seguire questa procedura nell'interfaccia della riga di comando di Azure:

1. Nella [Azure cloud Shell](https://shell.azure.com/)creare una directory per le risorse dell'esercitazione:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Scaricare la versione dello strumento di configurazione e dei modelli di configurazione:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Questa operazione creerà la `iotedge_config_cli_release` cartella nella directory dell'esercitazione.

   Il file di modello usato per creare la gerarchia del dispositivo è il `iotedge_config.yaml` file trovato in `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . Nella stessa directory `deploymentLowerLayer.json` è un file di distribuzione JSON che contiene le istruzioni per i moduli da distribuire nel **dispositivo di livello inferiore**. Il `deploymentTopLayer.json` file è lo stesso, ma per il **dispositivo di livello superiore**, perché i moduli distribuiti in ogni dispositivo non sono uguali. Il `device_config.toml` file è un modello per IOT Edge configurazioni del dispositivo e verrà usato per generare automaticamente i bundle di configurazione per i dispositivi nella gerarchia.

   Per esaminare il codice sorgente e gli script per lo `iotedge-config` strumento, consultare [il repository Azure-Samples su GitHub](https://github.com/Azure-Samples/iotedge_config_cli).

1. Aprire il modello di configurazione dell'esercitazione e modificarlo con le informazioni:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   Nella sezione **iothub** compilare i `iothub_hostname` `iothub_name` campi e con le informazioni. Queste informazioni sono disponibili nella pagina Panoramica dell'hub Internet delle cose nel portale di Azure.

   Nella sezione **certificati** facoltativi è possibile popolare i campi con i percorsi assoluti per il certificato e la chiave. Se si lasciano vuoti questi campi, lo script genererà automaticamente i certificati di test autofirmati per l'utilizzo. Se non si ha familiarità con il modo in cui i certificati vengono usati in uno scenario di gateway, vedere [la sezione relativa ai certificati della Guida alle procedure](how-to-connect-downstream-iot-edge-device.md#prepare-certificates).

   Nella sezione **configurazione** `template_config_path` è il percorso del `device_config.toml` modello usato per creare le configurazioni del dispositivo. Il `default_edge_agent` campo determina i dati da cui vengono estratti i dispositivi di livello inferiore dell'immagine dell'agente perimetrale e da dove.

   Nella sezione **edgedevices** , per uno scenario di produzione, è possibile modificare l'albero gerarchico in modo da riflettere la struttura desiderata. Ai fini di questa esercitazione, accettare l'albero predefinito. Per ogni dispositivo è disponibile un `device_id` campo in cui è possibile assegnare un nome ai dispositivi. È anche presente il `deployment` campo che specifica il percorso del file JSON di distribuzione per il dispositivo.

   È anche possibile registrare manualmente i dispositivi IoT Edge nell'hub Internet tramite la portale di Azure o Azure Cloud Shell. Per informazioni, vedere [la guida su come registrare un dispositivo IOT Edge](how-to-register-device.md).

   È anche possibile definire manualmente le relazioni padre-figlio. Per altre informazioni, vedere la sezione [creare una gerarchia di gateway](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) della Guida alle procedure.

   ![La sezione edgedevices del file di configurazione consente di definire la gerarchia](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Salvare e chiudere il file:

   `CTRL + S`, `CTRL + Q`

1. Creare una directory degli output per i bundle di configurazione nella directory delle risorse dell'esercitazione:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Passare alla `iotedge_config_cli_release` Directory ed eseguire lo strumento per creare la gerarchia dei dispositivi IOT Edge:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Con il `--output` flag, lo strumento crea i certificati del dispositivo, i bundle di certificati e un file di log in una directory di propria scelta. Con il `-f` flag impostato, lo strumento cercherà automaticamente i dispositivi IOT Edge esistenti nell'hub Internet e li rimuoverà, per evitare errori e mantenerne puliti l'hub.

   Lo strumento di configurazione crea i dispositivi IoT Edge e configura le relazioni padre-figlio tra di essi. Facoltativamente, consente di creare certificati per i dispositivi da usare. Se vengono forniti i percorsi per i JSON di distribuzione, lo strumento creerà automaticamente le distribuzioni nei dispositivi, ma ciò non è obbligatorio. Infine, lo strumento genererà i bundle di configurazione per i dispositivi e li inserirà nella directory di output. Per un esame approfondito dei passaggi eseguiti dallo strumento di configurazione, vedere il file di log nella directory di output.

   ![Durante l'esecuzione lo script visualizzerà una topologia della gerarchia](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Verificare che l'output della topologia dello script sia corretto. Una volta soddisfatta la struttura della gerarchia, si è pronti per continuare.

### <a name="configure-the-iot-edge-runtime"></a>Configurare il runtime di IoT Edge

Oltre al provisioning dei dispositivi, i passaggi di configurazione stabiliscono una comunicazione attendibile tra i dispositivi nella gerarchia usando i certificati creati in precedenza. I passaggi iniziano anche a stabilire la struttura di rete della gerarchia. Il dispositivo di livello superiore manterrà la connettività Internet, consentendo di eseguire il pull delle immagini per il runtime dal cloud, mentre i dispositivi di livello inferiore eseguiranno il routing attraverso il dispositivo di livello superiore per accedere a tali immagini.

Per configurare il runtime di IoT Edge, è necessario applicare ai dispositivi i bundle di configurazione creati dallo script di installazione. Le configurazioni differiscono leggermente tra il **dispositivo di livello superiore** e un **dispositivo di livello inferiore**, quindi tenere presente il file di configurazione del dispositivo che si sta applicando a ogni dispositivo.

1. Ogni dispositivo necessita del bundle di configurazione corrispondente. È possibile usare un'unità USB o una [copia di file sicura](https://www.ssh.com/ssh/scp/) per spostare i bundle di configurazione in ogni dispositivo.

   Assicurarsi di inviare il bundle di configurazione corretto a ogni dispositivo.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Il `:~` significa che la cartella di configurazione verrà inserita nella home directory della macchina virtuale.

1. Accedere alla macchina virtuale per applicare il bundle di configurazione al dispositivo:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. In ogni dispositivo, decomprimere il bundle di configurazione. È necessario installare prima zip:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. In ogni dispositivo applicare il bundle di configurazione al dispositivo:

   ```bash
   sudo ./install.sh
   ```

   Nel **dispositivo di livello superiore** verrà visualizzata una richiesta di immissione del nome host. Sul **dispositivo di livello inferiore**, verrà richiesto il nome host e il nome host del padre. Specificare l'indirizzo IP o il nome di dominio completo appropriato per ogni richiesta. È possibile usare una delle due opzioni, ma essere coerenti in diversi dispositivi. L'output dello script di installazione è illustrato di seguito.

   Per esaminare più in dettaglio le modifiche apportate al file di configurazione del dispositivo, vedere [la sezione configurare IOT Edge nei dispositivi della Guida alle procedure](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

  ![L'installazione dei bundle di configurazione aggiornerà i file config. toml nel dispositivo e riavvierà automaticamente tutti i servizi IoT Edge](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Se i passaggi precedenti sono stati completati correttamente, è possibile verificare che i dispositivi siano configurati correttamente.

Eseguire i controlli di configurazione e connettività nei dispositivi. Per il **dispositivo di livello superiore**:

   ```bash
   sudo iotedge check
   ```

Per il **dispositivo di livello inferiore**, l'immagine di diagnostica deve essere passata manualmente nel comando:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

Nel **dispositivo di livello superiore** si prevede di visualizzare un output con diverse valutazioni del passaggio. È possibile che vengano visualizzati alcuni avvisi relativi ai criteri dei log e, a seconda della rete, dei criteri DNS.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Una volta soddisfatte le configurazioni corrette per ogni dispositivo, si è pronti per continuare.

## <a name="deploy-modules-to-your-devices"></a>Distribuire moduli nei dispositivi

Le distribuzioni dei moduli nei dispositivi sono state generate automaticamente al momento della creazione dei dispositivi. Lo `iotedge-config-cli` strumento ha fornito i JSON di distribuzione per i **dispositivi di livello superiore e inferiore** dopo la creazione. La distribuzione del modulo è in sospeso durante la configurazione di IoT Edge Runtime in ogni dispositivo. Dopo aver configurato il runtime, sono state avviate le distribuzioni al **dispositivo di livello superiore** . Al termine di queste distribuzioni, il **dispositivo di livello inferiore** potrebbe usare il modulo **proxy API IOT Edge** per eseguire il pull delle immagini necessarie.

Nel [Azure cloud Shell](https://shell.azure.com/)è possibile esaminare il codice JSON di distribuzione del **dispositivo di livello superiore** per comprendere quali moduli sono stati distribuiti nel dispositivo:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Inoltre, i moduli di runtime **IOT Edge Agent** e l' **Hub IOT Edge**, il **dispositivo di livello superiore** riceve il modulo **Docker Registry** e **IOT Edge modulo proxy API** .

Il modulo **del registro di sistema Docker** punta a un container Registry di Azure esistente. In questo caso, `REGISTRY_PROXY_REMOTEURL` punta a Microsoft container Registry. Nella è `createOptions` possibile vedere che comunica sulla porta 5000.

Il modulo **proxy API IOT Edge** instrada le richieste HTTP ad altri moduli, consentendo ai dispositivi di livello inferiore di estrarre le immagini del contenitore o eseguire il push dei BLOB nella risorsa di archiviazione. Questa esercitazione comunica sulla porta 8000 ed è configurata per l'invio di richieste pull di immagini del contenitore Docker indirizzate al modulo **Docker Registry** sulla porta 5000. Inoltre, qualsiasi richiesta di caricamento dell'archiviazione BLOB viene indirizzata al modulo AzureBlobStorageonIoTEdge sulla porta 11002. Per ulteriori informazioni sul modulo **proxy API IOT Edge** e su come configurarlo, vedere la [Guida alle procedure](how-to-configure-api-proxy-module.md)del modulo.

Per informazioni su come creare una distribuzione di questo tipo tramite il portale di Azure o Azure Cloud Shell, vedere [la sezione dispositivo di livello superiore della Guida alle procedure](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices).

Nel [Azure cloud Shell](https://shell.azure.com/)è possibile esaminare il codice JSON di distribuzione del **dispositivo di livello inferiore** per comprendere quali moduli sono stati distribuiti nel dispositivo:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

È possibile osservare `systemModules` che i moduli di runtime del **dispositivo di livello inferiore** sono impostati per eseguire il pull da `$upstream:8000` , anziché `mcr.microsoft.com` come il **dispositivo di livello superiore** . Il **dispositivo di livello inferiore** Invia un'immagine Docker richiede il modulo **proxy API IoT Edge** sulla porta 8000, perché non è in grado di estrarre direttamente le immagini dal cloud. L'altro modulo distribuito nel **dispositivo di livello inferiore**, il modulo di **sensore di temperatura simulato** , esegue anche la richiesta di immagine a `$upstream:8000` .

Per informazioni su come creare una distribuzione di questo tipo tramite il portale di Azure o Azure Cloud Shell, vedere [la sezione relativa ai dispositivi di livello inferiore della Guida alle procedure](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices).

È possibile visualizzare lo stato dei moduli usando il comando:

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Tramite questo comando vengono restituite tutte le proprietà segnalate da edgeAgent. Ecco alcuni utili per il monitoraggio dello stato del dispositivo: *stato Runtime*, ora di *inizio Runtime*, *ora ultima uscita Runtime*, *conteggio riavvio Runtime*.

È anche possibile visualizzare lo stato dei moduli nell' [portale di Azure](https://ms.portal.azure.com/). Passare alla sezione **IOT Edge** dell'hub Internet per visualizzare i dispositivi e i moduli.

Una volta soddisfatte le distribuzioni dei moduli, si è pronti per continuare.

## <a name="view-generated-data"></a>Visualizzare i dati generati

Il modulo **Simulated Temperature Sensor** ei chi è stato eseguito il push genera dati dell'ambiente di esempio. Invia messaggi che includono la temperatura e l'umidità dell'ambiente, la temperatura e la pressione dell'apparecchiatura e un timestamp.

È anche possibile visualizzarli tramite [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Eseguire il `iotedge check` comando per verificare la configurazione e per risolvere gli errori.

È possibile eseguire `iotedge check` in una gerarchia nidificata, anche se i computer figlio non hanno accesso diretto a Internet.

Quando si esegue `iotedge check` dal livello inferiore, il programma tenta di estrarre l'immagine dall'elemento padre tramite la porta 443.

In questa esercitazione viene usata la porta 8000, quindi è necessario specificarla:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2.0-rc4
```

Il valore `azureiotedge-diagnostics` viene estratto dal registro contenitori collegato al modulo del registro. In questa esercitazione è impostato per impostazione predefinita su https://mcr.microsoft.com:

| Nome | Valore |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Se si usa un registro contenitori privato, assicurarsi che tutte le immagini (IoTEdgeAPIProxy, edgeAgent, edgeHub, il sensore di temperatura simulato e la diagnostica) siano presenti nel registro contenitori.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare le configurazioni locali e le risorse di Azure create in questo articolo per evitare addebiti.

Per eliminare le risorse:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Selezionare il nome del gruppo di risorse contenente le risorse di test di IoT Edge. 

3. Esaminare l'elenco delle risorse contenute nel gruppo di risorse. Per eliminarle tutte, è possibile selezionare **Elimina gruppo di risorse**. Se se ne vogliono eliminare solo alcune, è possibile fare clic su ogni risorsa per eliminarle singolarmente. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati configurati due dispositivi IoT Edge come gateway, di cui uno è stato impostato come dispositivo padre dell'altro. Si è quindi dimostrato di eseguire il pull di un'immagine del contenitore sul dispositivo figlio tramite un gateway usando il modulo proxy API IoT Edge. Per altre informazioni, vedere [la Guida alle procedure sull'uso del modulo proxy](how-to-configure-api-proxy-module.md) .

Per altre informazioni sull'uso dei gateway per creare livelli gerarchici di dispositivi IoT Edge, vedere [la Guida alle procedure per la connessione di dispositivi IOT Edge downstream](how-to-connect-downstream-iot-edge-device.md).

È possibile continuare con le altre esercitazioni per scoprire in che modo Azure IoT Edge può creare altre soluzioni per il business.

> [!div class="nextstepaction"]
> [Distribuire un modello di Azure Machine Learning come modulo](tutorial-deploy-machine-learning.md)
