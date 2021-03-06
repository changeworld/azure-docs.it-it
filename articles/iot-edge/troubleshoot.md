---
title: Risoluzione dei problemi - Azure IoT Edge | Microsoft Docs
description: Usare questo articolo per apprendere le competenze diagnostiche standard per Azure IoT Edge, ad esempio il recupero dello stato dei componenti e dei log
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fa49af946a1e5fc631eeb1ee9b9c7c99d3adff8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308269"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Risolvere i problemi del dispositivo IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Se si verificano problemi durante l'esecuzione di Azure IoT Edge nell'ambiente in uso, usare questo articolo come guida per la risoluzione dei problemi e la diagnostica.

## <a name="run-the-check-command"></a>Eseguire il comando ' check '

Il primo passaggio per la risoluzione dei problemi di IoT Edge deve essere quello di usare il `check` comando, che esegue una raccolta di test di configurazione e connettività per i problemi comuni. Il `check` comando è disponibile in [Release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e versioni successive.

>[!NOTE]
>Lo strumento per la risoluzione dei problemi non può eseguire controlli di connettività se il dispositivo IoT Edge è dietro a un server proxy.

È possibile eseguire il `check` comando come indicato di seguito o includere il `--help` flag per visualizzare un elenco completo delle opzioni:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
In Linux:

```bash
sudo iotedge check
```

In Windows:

```powershell
iotedge check
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.1 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge check
```

:::moniker-end
<!-- end 1.2 -->

Lo strumento di risoluzione dei problemi esegue molti controlli ordinati nelle tre categorie seguenti:

* I *controlli di configurazione* esaminano i dettagli che potrebbero impedire la connessione dei dispositivi IOT Edge al cloud, inclusi i problemi con il file di configurazione e il motore di gestione dei contenitori.
* I *controlli di connessione* verificano che il runtime di IOT Edge possa accedere alle porte sul dispositivo host e che tutti i componenti IOT Edge possano connettersi all'hub Internet. Questo set di controlli restituisce errori se il dispositivo IoT Edge si trova dietro un proxy.
* I *controlli di conformità* per la produzione cercano le procedure consigliate per la produzione, ad esempio lo stato dei certificati dell'autorità di certificazione (CA) del dispositivo e la configurazione del file di registro del modulo.

Lo strumento di controllo IoT Edge utilizza un contenitore per eseguire la diagnostica. L'immagine del contenitore, `mcr.microsoft.com/azureiotedge-diagnostics:latest` , è disponibile tramite [Microsoft container Registry](https://github.com/microsoft/containerregistry). Se è necessario eseguire un controllo in un dispositivo senza accesso diretto a Internet, i dispositivi dovranno accedere all'immagine del contenitore.

<!-- <1.2> -->
:::moniker range=">=iotedge-2020-11"

In uno scenario che usa dispositivi IoT Edge annidati, è possibile ottenere l'accesso all'immagine di diagnostica nei dispositivi figlio instradando il pull dell'immagine attraverso i dispositivi padre.

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:<port_for_api_proxy_module>/azureiotedge-diagnostics:1.2
```

<!-- </1.2> -->
:::moniker-end

Per informazioni su ognuno dei controlli diagnostici eseguiti da questo strumento, incluse le operazioni da eseguire se viene visualizzato un errore o un avviso, vedere [IOT Edge risolvere i problemi relativi ai controlli](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Raccogliere le informazioni di debug con il comando ' support-bundle '

Quando è necessario raccogliere i log da un dispositivo IoT Edge, il modo più pratico consiste nell'usare il `support-bundle` comando. Per impostazione predefinita, questo comando raccoglie il modulo IoT Edge Security Manager e i log del motore del contenitore, l' `iotedge check` output JSON e altre informazioni di debug utili. Li comprime in un singolo file per semplificare la condivisione. Il `support-bundle` comando è disponibile in [Release 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) e versioni successive.

Eseguire il `support-bundle` comando con il `--since` flag per specificare per quanto tempo dal passato si desidera ottenere i log. Ad esempio `6h` , otterrà i log dalle ultime sei ore, `6d` dagli ultimi sei giorni, `6m` dagli ultimi sei minuti e così via. Includere il `--help` flag per visualizzare un elenco completo delle opzioni.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

In Linux:

```bash
sudo iotedge support-bundle --since 6h
```

In Windows:

```powershell
iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.2 -->

È anche possibile usare una chiamata al [metodo diretto](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) al dispositivo per caricare l'output del comando support-bundle nell'archivio BLOB di Azure.

> [!WARNING]
> L'output del `support-bundle` comando può contenere nomi host, dispositivi e moduli, informazioni registrate dai moduli e così via. Tenere presente questo problema se si condivide l'output in un forum pubblico.

## <a name="check-your-iot-edge-version"></a>Controllare la versione di IoT Edge

Se si sta eseguendo una versione precedente di IoT Edge, l'aggiornamento può risolvere il problema. Lo `iotedge check` strumento verifica che il daemon di sicurezza IoT Edge sia la versione più recente, ma non controlla le versioni dei moduli dell'hub IOT Edge e dell'agente. Per verificare la versione dei moduli di runtime nel dispositivo, usare i comandi `iotedge logs edgeAgent` e `iotedge logs edgeHub` . Il numero di versione è dichiarato nei log all'avvio del modulo.

Per istruzioni su come aggiornare il dispositivo, vedere [aggiornare il daemon di sicurezza di IOT Edge e il runtime](how-to-update-iot-edge.md).

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>Verificare l'installazione di IoT Edge nei dispositivi

È possibile verificare l'installazione di IoT Edge nei dispositivi [monitorando il modulo gemello edgeAgent](./how-to-monitor-module-twins.md).

Per ottenere la versione più recente del modulo edgeAgent, eseguire il comando seguente da [Azure cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

Tramite questo comando vengono restituite tutte le [proprietà segnalate](./module-edgeagent-edgehub.md)da edgeAgent. Ecco alcune informazioni utili per monitorare lo stato del dispositivo:

* stato Runtime
* ora di inizio Runtime
* ora dell'ultima uscita Runtime
* conteggio riavvio Runtime

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verificare lo stato di IoT Edge Security Manager e dei relativi log

Il [IOT Edge Security Manager](iot-edge-security-manager.md) è responsabile di operazioni quali l'inizializzazione del sistema IOT Edge all'avvio e il provisioning dei dispositivi. Se IoT Edge non viene avviato, è possibile che i log del gestore sicurezza forniscano informazioni utili.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
In Linux:

* Visualizzare lo stato di IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Visualizzare i log di IoT Edge Security Manager:

   ```bash
   sudo journalctl -u iotedge -f
   ```

* Visualizzare i log più dettagliati di IoT Edge Security Manager:

  1. Modificare le impostazioni del daemon di IoT Edge:

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. Aggiornare le righe seguenti:

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=debug
     ```

  3. Riavviare il daemon di sicurezza IoT Edge:

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```

In Windows:

* Visualizzare lo stato di IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

* Visualizzare i log di IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Visualizzare solo gli ultimi 5 minuti dei log di IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Visualizzare i log più dettagliati di IoT Edge Security Manager:

  1. Aggiungere una variabile di ambiente a livello di sistema:

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. Riavviare il daemon di sicurezza di IoT Edge:

     ```powershell
     Restart-Service iotedge
     ```

:::moniker-end
<!--end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* Visualizzare lo stato dei servizi di sistema IoT Edge:

   ```bash
   sudo iotedge system status
   ```

* Visualizzare i log dei servizi di sistema IoT Edge:

   ```bash
   sudo iotedge system logs -- -f
   ```

* Abilitare i log a livello di debug per visualizzare log più dettagliati dei servizi di sistema IoT Edge:

  1. Abilitare i log a livello di debug.

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. Tornare ai registri predefiniti a livello di informazioni dopo il debug.

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

:::moniker-end
<!-- end 1.2 -->

## <a name="check-container-logs-for-issues"></a>Controllare i log dei contenitori per eventuali problemi

Una volta che il daemon di sicurezza di IoT Edge è in esecuzione, esaminare i log dei contenitori per rilevare i problemi. Iniziare con i contenitori distribuiti, quindi esaminare i contenitori che costituiscono il runtime di IoT Edge: edgeAgent e edgeHub. I log dell'agente di IoT Edge in genere forniscono informazioni sul ciclo di vita di ogni contenitore. I log dell'hub IoT Edge forniscono informazioni sulla messaggistica e sul routing.

```cmd
iotedge logs <container name>
```

È anche possibile usare una chiamata a un [metodo diretto](how-to-retrieve-iot-edge-logs.md#upload-module-logs) a un modulo sul dispositivo per caricare i log del modulo nell'archivio BLOB di Azure.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Visualizzare i messaggi che passano attraverso l'hub IoT Edge

<!--1.1 -->
:::moniker range="iotedge-2018-06"

È possibile visualizzare i messaggi che passano attraverso l'hub IoT Edge e raccogliere informazioni dettagliate dai log dettagliati dai contenitori di Runtime. Per attivare i log dettagliati su questi contenitori, impostare `RuntimeLogLevel` nel file di configurazione yaml. Per aprire il file:

In Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Per impostazione predefinita, l'elemento `agent` avrà un aspetto simile all'esempio seguente:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

Sostituire `env: {}` con:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > I file YAML non possono contenere tabulazioni come rientri. In alternativa usare due spazi. Gli elementi di primo livello non possono contenere spazi vuoti iniziali.

Salvare il file e riavviare il gestore sicurezza IoT Edge.

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

È possibile visualizzare i messaggi che passano attraverso l'hub IoT Edge e raccogliere informazioni dettagliate dai log dettagliati dai contenitori di Runtime. Per attivare i log dettagliati in questi contenitori, impostare la `RuntimeLogLevel` variabile di ambiente nel manifesto della distribuzione.

Per visualizzare i messaggi che passano attraverso l'hub IoT Edge, impostare la `RuntimeLogLevel` variabile di ambiente su `debug` per il modulo edgeHub.

Entrambi i moduli edgeHub e edgeAgent dispongono di questa variabile di ambiente del log di runtime, con il valore predefinito impostato su `info` . Questa variabile di ambiente può assumere i valori seguenti:

* irreversibile
* error
* warning
* info
* debug
* verbose

<!-- end 1.2 -->
:::moniker-end

È anche possibile controllare i messaggi inviati tra l'hub e i dispositivi Internet. Per visualizzare questi messaggi, è necessario usare l' [estensione Hub Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Per altre informazioni, vedere [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Strumento utile quando si sviluppa con Azure IoT).

## <a name="restart-containers"></a>Riavviare i contenitori

Dopo avere esaminato i log e i messaggi per raccogliere informazioni, è possibile provare a riavviare i contenitori:

```cmd
iotedge restart <container name>
```

Riavviare i contenitori di runtime di IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Controllare le regole di configurazione del firewall e della porta

Azure IoT Edge consente la comunicazione da un server locale al cloud di Azure usando i protocolli dell'hub informazioni supportati, vedere [scelta di un protocollo di comunicazione](../iot-hub/iot-hub-devguide-protocols.md). Per una maggiore sicurezza, i canali di comunicazione tra Azure IoT Edge e hub IoT di Azure sono sempre configurati per essere in uscita. Questa configurazione si basa sul [modello di comunicazione assistita dei servizi](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices), che consente di ridurre la superficie di attacco esplorabile da un'entità dannosa. Le comunicazioni in ingresso sono necessarie solo per scenari specifici in cui l'hub IoT di Azure deve eseguire il push dei messaggi al dispositivo Azure IoT Edge. I messaggi da cloud a dispositivo sono protetti tramite canali sicuri TLS e possono essere protetti ulteriormente tramite certificati X.509 e moduli di dispositivo TPM. Il gestore sicurezza di Azure IoT Edge stabilisce come attivare la comunicazione, vedere [Gestore sicurezza di Azure IoT Edge](../iot-edge/iot-edge-security-manager.md).

Sebbene IoT Edge offra una configurazione avanzata per la protezione del runtime e dei moduli distribuiti di Azure IoT Edge, dipende comunque dal computer e dalla configurazione di rete sottostanti. Di conseguenza, è fondamentale assicurarsi che le regole di rete e del firewall appropriate siano configurate per la comunicazione tra Edge e cloud. La tabella seguente può essere usata come linea guida per le regole del firewall di configurazione per i server sottostanti in cui è ospitato Azure IoT Edge Runtime:

|Protocollo|Porta|In arrivo|In uscita|Indicazioni|
|--|--|--|--|--|
|MQTT|8883|BLOCCATO (impostazione predefinita)|BLOCCATO (impostazione predefinita)|<ul> <li>Configurare i dati in uscita in modo che siano Aperti quando si usa MQTT come protocollo di comunicazione.<li>1883 per MQTT non è supportato da IoT Edge. <li>Le connessioni in ingresso devono essere bloccate.</ul>|
|AMQP|5671|BLOCCATO (impostazione predefinita)|APERTO (impostazione predefinita)|<ul> <li>Protocollo di comunicazione predefinito per IoT Edge. <li> Deve essere configurato per essere Aperto se Azure IoT Edge non è configurato per altri protocolli supportati o AMQP è il protocollo di comunicazione desiderato.<li>5672 per AMQP non è supportato da IoT Edge.<li>Bloccare questa porta quando Azure IoT Edge usa un protocollo supportato dall'hub IoT diverso.<li>Le connessioni in ingresso devono essere bloccate.</ul></ul>|
|HTTPS|443|BLOCCATO (impostazione predefinita)|APERTO (impostazione predefinita)|<ul> <li>Configurare le connessioni in uscita come Aperto sulla porta 443 per il provisioning di IoT Edge. Questa configurazione è necessaria quando si usano script manuali o il servizio Device Provisioning di Azure IoT. <li>La connessione In ingresso deve essere Aperta solo per scenari specifici: <ul> <li>  Se si dispone di un gateway trasparente con dispositivi foglia che possono inviare richieste di metodo. In questo caso, non occorre che la porta 443 sia aperta a reti esterne per connettersi a IotHub o fornire servizi IoTHub tramite Azure IoT Edge. Pertanto, la regola in ingresso potrebbe essere limitata all'apertura solo in ingresso dalla rete interna. <li> Per scenari da client a dispositivo (C2D).</ul><li>80 per HTTP non è supportato da IoT Edge.<li>Se i protocolli non HTTP (ad esempio, AMQP o MQTT) non possono essere configurati nell'azienda. I messaggi possono essere inviati tramite WebSocket. In questo caso, la porta 443 verrà usata per la comunicazione WebSocket.</ul>|

## <a name="next-steps"></a>Passaggi successivi

Se si ritiene di aver rilevato un bug nella piattaforma di IoT Edge, [Inviare un problema](https://github.com/Azure/iotedge/issues) in modo da poter migliorare l'esperienza.

In caso di altre domande, creare una [Richiesta di supporto](https://portal.azure.com/#create/Microsoft.Support) per assistenza.
