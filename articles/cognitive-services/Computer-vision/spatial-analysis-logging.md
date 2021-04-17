---
title: Telemetria e registrazione per i contenitori di analisi spaziale
titleSuffix: Azure Cognitive Services
description: Analisi spaziale fornisce a ogni contenitore informazioni dettagliate, registrazione e impostazioni di sicurezza comuni del framework di configurazione.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 901e857a346b0955726c5755e23595efefbc2ca1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589500"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetria e risoluzione dei problemi

L'analisi spaziale include un set di funzionalità per monitorare l'integrità del sistema e facilitare la diagnosi dei problemi.

## <a name="enable-visualizations"></a>Abilitare le visualizzazioni

Per abilitare una visualizzazione di Informazioni dettagliate sull'intelligenza artificiale eventi in un fotogramma video, è necessario usare la versione di un'operazione di analisi `.debug` [spaziale](spatial-analysis-operations.md) in un computer desktop. La visualizzazione non è possibile nei Azure Stack Edge mobili. Sono disponibili quattro operazioni di debug.

Se il dispositivo non è un Azure Stack Edge, modificare il file manifesto della distribuzione per i computer [desktop in](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) modo che usi il valore corretto per la variabile di `DISPLAY` ambiente . Deve corrispondere alla `$DISPLAY` variabile nel computer host. Dopo aver aggiornato il manifesto della distribuzione, ridistribuire il contenitore.

Al termine della distribuzione, potrebbe essere necessario copiare il file dal computer host al contenitore `.Xauthority` e riavviarlo. Nell'esempio seguente `peopleanalytics` è il nome del contenitore nel computer host.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Raccogliere dati di telemetria sull'integrità del sistema

Telegraf è un'open source che funziona con l'analisi spaziale ed è disponibile nel Microsoft Container Registry. Accetta gli input seguenti e li invia a Monitoraggio di Azure. Il modulo telegraf può essere compilato con input e output personalizzati desiderati. La configurazione del modulo telegraf nell'analisi spaziale fa parte del manifesto della distribuzione (collegato in precedenza). Questo modulo è facoltativo e può essere rimosso dal manifesto se non è necessario. 

Input: 
1. Metriche di analisi spaziale
2. Metriche del disco
3. Metriche CPU
4. Metriche di Docker
5. Metriche GPU

Output:
1. Monitoraggio di Azure

Il modulo telegraf di analisi spaziale fornito pubblicherà tutti i dati di telemetria generati dal contenitore Analisi spaziale per Monitoraggio di Azure. Vedere il [Monitoraggio di Azure](../../azure-monitor/overview.md) per informazioni sull'aggiunta di Monitoraggio di Azure alla sottoscrizione.

Dopo aver impostato Monitoraggio di Azure, è necessario creare credenziali che consentono al modulo di inviare dati di telemetria. È possibile usare il portale di Azure per creare una nuova entità servizio o usare il comando dell'interfaccia della riga di comando di Azure seguente per crearne una.

> [!NOTE] 
> Per questo comando è necessario disporre dei privilegi di proprietario per la sottoscrizione. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

Nel manifesto di distribuzione per il dispositivo [Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179)il [computer desktop](https://go.microsoft.com/fwlink/?linkid=2152270)o la macchina virtuale di Azure con [GPU](https://go.microsoft.com/fwlink/?linkid=2152189)cercare il modulo *telegraf* e sostituire i valori seguenti con le informazioni sull'entità servizio del passaggio precedente e ridistribuire.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Dopo aver distribuito il modulo telegraf, è possibile accedere alle metriche segnalate  tramite il servizio Monitoraggio di Azure o selezionando Monitoraggio nell'hub IoT nel portale di Azure.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Monitoraggio di Azure di telemetria":::

### <a name="system-health-events"></a>Eventi di integrità del sistema

| Nome evento                  | Descrizione    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | Inviato quando un utente modifica lo stato del modulo Analisi spaziale da *in esecuzione* a *arrestato.*  |
| archon_error                | Inviato quando uno dei processi all'interno del contenitore si arresta in modo anomalo. Si tratta di un errore critico.      |
| InputRate                   | Velocità con cui il grafo elabora l'input video. Segnalato ogni 5 minuti.              |
| OutputRate                  | Frequenza con cui il grafico restituisce informazioni dettagliate sull'intelligenza artificiale. Segnalato ogni 5 minuti.                |
| archon_allGraphsStarted     | Inviato al termine dell'avvio di tutti i grafici.                                           |
| archon_configchange         | Inviato quando viene modificata una configurazione del grafo.                                              |
| archon_graphCreationFailed  | Inviato quando il grafico con il report `graphId` non viene avviato.                           |
| archon_graphCreationSuccess | Inviato quando il grafico con il report `graphId` viene avviato correttamente.                      |
| archon_graphCleanup         | Inviato quando il grafico con il report `graphId` viene pulito e chiuso.                      |
| archon_graphHeartbeat       | Heartbeat inviato ogni minuto per ogni grafo di una competenza.                                   |
| archon_apiKeyAuthFail       | Inviato quando la chiave Visione artificiale non riesce ad autenticare il contenitore per più di 24 ore, a causa dei motivi seguenti: Fuori quota, Non valido, Offline. |
| VideoIngesterSinglebeat      | Inviato ogni ora per indicare che il video viene trasmesso dall'origine video, con il numero di errori in quell'ora. Segnalato per ogni grafico. |
| VideoIngesterState          | Report *arrestati* o *avviati per* lo streaming video. Segnalato per ogni grafo.              |

##  <a name="troubleshooting-an-iot-edge-device"></a>Risoluzione dei problemi di IoT Edge dispositivo

È possibile usare `iotedge` lo strumento da riga di comando per controllare lo stato e i log dei moduli in esecuzione. Ad esempio:
* `iotedge list`: segnala un elenco di moduli in esecuzione. 
  È possibile controllare ulteriormente la presenza di errori con `iotedge logs edgeAgent` . Se `iotedge` si blocca, è possibile provare a riavviarlo con `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` per riavviare un modulo specifico 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Raccogliere file di log con il contenitore di diagnostica

L'analisi spaziale genera log di debug Docker che è possibile usare per diagnosticare i problemi di runtime o includere nei ticket di supporto. Il modulo Diagnostica analisi spaziale è disponibile nella Microsoft Container Registry per il download. Nel file di distribuzione manifesto per il [dispositivo Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179)il [computer desktop](https://go.microsoft.com/fwlink/?linkid=2152270)o la macchina virtuale di Azure con [GPU](https://go.microsoft.com/fwlink/?linkid=2152189) cercare il *modulo di* diagnostica.

Nella sezione "env" aggiungere la configurazione seguente:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Per ottimizzare i log caricati in un endpoint remoto, ad esempio Archiviazione BLOB di Azure, è consigliabile mantenere dimensioni di file ridotte. Vedere l'esempio seguente per la configurazione consigliata dei log Docker.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Configurare il livello di log

La configurazione a livello di log consente di controllare il livello di dettaglio dei log generati. I livelli di log supportati sono: `none` , , , e `verbose` `info` `warning` `error` . Il livello di dettaglio del log predefinito sia per i nodi che per la piattaforma è `info` . 

I livelli di log possono essere modificati a livello globale impostando la `ARCHON_LOG_LEVEL` variabile di ambiente su uno dei valori consentiti.
Può anche essere impostato tramite il documento modulo gemello di IoT Edge a livello globale, per tutte le competenze distribuite o per ogni competenza specifica impostando i valori per e come illustrato di `platformLogLevel` `nodesLogLevel` seguito.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Raccolta di log

> [!NOTE]
> Il modulo non influisce sul contenuto di registrazione, ma consente solo di raccogliere, filtrare e `diagnostics` caricare i log esistenti.
> Per usare questo modulo, è necessario disporre dell'API Docker versione 1.40 o successiva.

Il file manifesto della distribuzione di esempio [per il Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179)il computer [desktop](https://go.microsoft.com/fwlink/?linkid=2152270)o una macchina virtuale di Azure con [GPU](https://go.microsoft.com/fwlink/?linkid=2152189) include un modulo denominato che raccoglie e `diagnostics` carica i log. Questo modulo è disabilitato per impostazione predefinita e deve essere abilitato tramite la IoT Edge del modulo quando è necessario accedere ai log. 

La raccolta è su richiesta e controllata tramite un IoT Edge diretto e può inviare log a un `diagnostics` Archiviazione BLOB di Azure.

### <a name="configure-diagnostics-upload-targets"></a>Configurare le destinazioni di caricamento della diagnostica

Nel portale IoT Edge selezionare il dispositivo e quindi il **modulo di** diagnostica. Nel file manifesto della distribuzione di esempio per il dispositivo [Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179)i  computer [desktop](https://go.microsoft.com/fwlink/?linkid=2152270)o le macchine virtuali di Azure con [GPU](https://go.microsoft.com/fwlink/?linkid=2152189) cercare la sezione Variabili di ambiente per la diagnostica denominata e aggiungere le `env` informazioni seguenti:

**Configurare il caricamento in Archiviazione BLOB di Azure**

1. Creare il proprio account Archiviazione BLOB di Azure, se non è già stato fatto.
2. Ottenere la **stringa di connessione** per l'account di archiviazione dal portale di Azure. Si trova in Chiavi **di accesso.**
3. I log di analisi spaziale verranno caricati automaticamente in un contenitore di archiviazione BLOB denominato *rtcvlogs* con il formato di nome file seguente: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Caricamento dei log di analisi spaziale

I log vengono caricati su richiesta con `getRTCVLogs` IoT Edge metodo , nel modulo `diagnostics` . 


1. Passare alla pagina del portale dell'hub IoT, selezionare **Dispositivi Perimetrali,** quindi selezionare il dispositivo e il modulo di diagnostica. 
2. Passare alla pagina dei dettagli del modulo e fare clic sulla ***scheda metodo*** diretto.
3. Digitare `getRTCVLogs` il nome del metodo e una stringa di formato JSON nel payload. È possibile immettere `{}` , che è un payload vuoto. 
4. Impostare i timeout della connessione e del metodo e fare clic **su Richiama metodo**.
5. Selezionare il contenitore di destinazione e compilare una stringa json del payload usando i parametri descritti nella **sezione Sintassi di** registrazione. Fare **clic su Richiama metodo** per eseguire la richiesta.

>[!NOTE]
> Se si richiama `getRTCVLogs` il metodo con un payload vuoto, verrà restituito un elenco di tutti i contenitori distribuiti nel dispositivo. Per il nome del metodo si applica la distinzione tra maiuscole e minuscole. Verrà visualizzato un errore 501 se viene specificato un nome di metodo non corretto.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Chiamata del metodo getRTCVLogs ":::
![Pagina del metodo getRTCVLogs Direct](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Sintassi di registrazione

Nella tabella seguente sono elencati i parametri che è possibile usare durante l'esecuzione di query sui log.

| Parola chiave | Descrizione | Valore predefinito |
|--|--|--|
| StartTime | Ora di inizio dei log desiderata, in millisecondi UTC. | `-1`, l'inizio del runtime del contenitore. Quando `[-1.-1]` viene usata come intervallo di tempo, l'API restituisce i log dell'ultima ora.|
| EndTime | Ora di fine dei log desiderata, in millisecondi UTC. | `-1`, l'ora corrente. Quando `[-1.-1]` si usa l'intervallo di tempo, l'API restituisce i log dell'ultima ora. |
| ContainerId | Contenitore di destinazione per il recupero dei log.| `null`, quando non è presente alcun ID contenitore. L'API restituisce tutte le informazioni sui contenitori disponibili con ID.|
| DoPost | Eseguire l'operazione di caricamento. Quando è impostato su `false` , esegue l'operazione richiesta e restituisce le dimensioni di caricamento senza eseguire il caricamento. Se impostato su `true` , avvierà il caricamento asincrono dei log selezionati | `false`, non caricare.|
| Limitazione | Indicare il numero di righe di log da caricare per ogni batch | `1000`, usare questo parametro per regolare la velocità del post. |
| Filtri | Filtra i log da caricare | `null`, i filtri possono essere specificati come coppie chiave-valore in base alla struttura dei log di analisi spaziale: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . ad esempio `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

Nella tabella seguente sono elencati gli attributi nella risposta alla query.

| Parola chiave | Descrizione|
|--|--|
|DoPost| True *o* *false.* Indica se i log sono stati caricati o meno. Quando si sceglie di non caricare i log, l'API restituisce informazioni * in **modo sincrono** _. Quando si sceglie di caricare i log, l'API restituisce 200, se la richiesta è valida, e avvia il caricamento dei log __*_ in modo asincrono **.|
|Filtro temporale| Filtro temporale applicato ai log.|
|Filtri di valore| Filtri di parole chiave applicati ai log. |
|TimeStamp| Ora di inizio dell'esecuzione del metodo. |
|ContainerId| ID contenitore di destinazione. |
|FetchCounter| Numero totale di righe di log. |
|FetchSizeInByte| Quantità totale di dati di log in byte. |
|MatchCounter| Numero valido di righe di log. |
|MatchSizeInByte| Quantità valida di dati di log in byte. |
|FilterCount| Numero totale di righe di log dopo l'applicazione del filtro. |
|FilterSizeInByte| Quantità totale di dati di log in byte dopo l'applicazione del filtro. |
|FetchLogsDurationInMiliSec| Durata dell'operazione di recupero. |
|PaseLogsDurationInMiliSec| Durata dell'operazione di filtro. |
|PostLogsDurationInMiliSec| Durata post-operazione. |

#### <a name="example-request"></a>Richiesta di esempio 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Risposta di esempio 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Controllare le righe, gli orari e le dimensioni del log di recupero. Se queste impostazioni hanno un aspetto positivo, sostituire ***DoPost*** in e che esegue il push dei log con gli stessi `true` filtri nelle destinazioni. 

È possibile esportare i log dal Archiviazione BLOB di Azure durante la risoluzione dei problemi. 

## <a name="common-issues"></a>Problemi comuni

Se viene visualizzato il messaggio seguente nei log del modulo, potrebbe significare che la sottoscrizione di Azure deve essere approvata: 

"Il contenitore non è in uno stato valido. Convalida della sottoscrizione non riuscita con stato 'Mismatch'. La chiave API non è destinata al tipo di contenitore specificato."

Per altre informazioni, vedere Richiedere [l'approvazione per eseguire il contenitore.](spatial-analysis-container.md#request-approval-to-run-the-container)

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Risoluzione dei problemi Azure Stack Edge dispositivo

La sezione seguente viene fornita per facilitare il debug e la verifica dello stato del Azure Stack Edge dispositivo.

### <a name="access-the-kubernetes-api-endpoint"></a>Accedere all'endpoint dell'API Kubernetes. 

1. Nell'interfaccia utente locale del dispositivo passare alla **pagina** Dispositivi. 
2. In **Endpoint dispositivo copiare** l'endpoint del servizio API Kubernetes. Questo endpoint è una stringa nel formato seguente: `https://compute..[device-IP-address]`.
3. Salvare la stringa dell'endpoint. Questo verrà utilizzato in un secondo momento durante la configurazione `kubectl` per accedere al cluster Kubernetes.

### <a name="connect-to-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

Connettersi in remoto da un client Windows. Dopo aver creato il cluster Kubernetes, è possibile gestire le applicazioni tramite questo cluster. È necessario connettersi all'interfaccia di PowerShell del dispositivo. A seconda del sistema operativo del client, le procedure per connettersi in remoto al dispositivo possono essere diverse. I passaggi seguenti sono per un client Windows che esegue PowerShell.

> [!TIP]
> * Prima di iniziare, assicurarsi che il client Windows sia in esecuzione Windows PowerShell 5.0 o versione successiva.
> * PowerShell è disponibile [anche in Linux.](/powershell/scripting/install/installing-powershell-core-on-linux)

1. Eseguire una Windows PowerShell come amministratore. 
    1. Assicurarsi che il servizio Gestione remota Windows sia in esecuzione nel client. Al prompt dei comandi digitare `winrm quickconfig`.

2. Assegnare una variabile per l'indirizzo IP del dispositivo. Ad esempio: `$ip = "<device-ip-address>"`.

3. Usare il comando seguente per aggiungere l'indirizzo IP del dispositivo all'elenco degli host attendibili del client. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Avviare una Windows PowerShell sessione nel dispositivo. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Specificare la password quando richiesto. Usare la stessa password usata per accedere all'interfaccia Web locale. La password predefinita dell'interfaccia Web locale è `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Accedere al cluster Kubernetes

Dopo aver creato il cluster Kubernetes, è possibile usare lo strumento da riga `kubectl` di comando per accedere al cluster.

1. Creare un nuovo spazio dei nomi. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Creare un utente e ottenere un file di configurazione. Questo comando restituisce informazioni di configurazione per il cluster Kubernetes. Copiare queste informazioni e salvarle in un file denominato *config*. Non salvare il file come estensione di file.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Aggiungere il file *di* configurazione alla *cartella .kube* nel profilo utente nel computer locale.    

4. Associare lo spazio dei nomi all'utente creato.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Installare `kubectl` nel client Windows usando il comando seguente:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Aggiungere una voce DNS al file hosts nel sistema. 
    1. Eseguire blocco note come amministratore e aprire il file *hosts* disponibile in `C:\windows\system32\drivers\etc\hosts` . 
    2. Creare una voce nel file hosts con l'indirizzo IP del dispositivo e il dominio DNS ottenuto dalla **pagina Dispositivo** nell'interfaccia utente locale. L'endpoint da usare sarà simile al seguente: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Verificare che sia possibile connettersi ai pod Kubernetes.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Per ottenere i log del contenitore, eseguire il comando seguente:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Comandi utili

|Comando  |Descrizione  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Genera un file di configurazione Kubernetes. Quando si usa il comando , copiare le informazioni in un file denominato *config*. Non salvare il file con un'estensione.        |
| `Get-HcsApplianceInfo` | Restituisce informazioni sul dispositivo. |
| `Enable-HcsSupportAccess` | Genera le credenziali di accesso per avviare una sessione di supporto. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>Come creare un ticket di supporto per l'analisi spaziale 

Se è necessario maggiore supporto per trovare una soluzione a un problema che si verifica con il contenitore analisi spaziale, seguire questa procedura per compilare e inviare un ticket di supporto. Il team microsoft riceverà indicazioni aggiuntive. 

### <a name="fill-out-the-basics"></a>Completare le nozioni di base 
Creare un nuovo ticket di supporto nella [pagina Nuova richiesta di](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) supporto. Seguire le istruzioni per specificare i parametri seguenti:

![Nozioni di base sul supporto](./media/support-ticket-page-1-final.png)

1. Impostare **Tipo di problema** su `Technical` .
2. Selezionare la sottoscrizione che si sta utilizzando per distribuire il contenitore analisi spaziale.
3. Selezionare `My services` e selezionare come `Cognitive Services` servizio.
4. Selezionare la risorsa utilizzata per distribuire il contenitore Analisi spaziale.
5. Scrivere una breve descrizione che descrive in dettaglio il problema che si sta verificando. 
6. Selezionare `Spatial Analysis` come tipo di problema.
7. Selezionare il sottotipo appropriato nell'elenco a discesa.
8. Selezionare **Avanti: Soluzioni** per passare alla pagina successiva.

### <a name="recommended-solutions"></a>Soluzioni consigliate
La fase successiva offrirà le soluzioni consigliate per il tipo di problema selezionato. Queste soluzioni risolveranno i problemi più comuni, ma se non sono utili per la soluzione, selezionare **Avanti: Dettagli** per andare al passaggio successivo.

### <a name="details"></a>Dettagli
In questa pagina aggiungere altri dettagli sul problema che si è verificato. Assicurarsi di includere il maggior numero possibile di dettagli, in quanto ciò aiuterà i tecnici a restringere meglio il problema. Includere il metodo di contatto preferito e la gravità del problema in modo da poter contattare l'utente in modo appropriato e selezionare **Avanti: Rivedi e** crea per passare al passaggio successivo. 

### <a name="review-and-create"></a>Rivedi e crea 
Esaminare i dettagli della richiesta di supporto per assicurarsi che tutto sia accurato e rappresenti il problema in modo efficace. Quando si è pronti, selezionare **Crea** per inviare il ticket al team. Una volta ricevuto il ticket, si riceverà una conferma tramite posta elettronica e il team si tornerà al più presto possibile. È possibile visualizzare lo stato del ticket nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'applicazione Web People Counting](spatial-analysis-web-app.md)
* [Configurare le operazioni di analisi spaziale](./spatial-analysis-operations.md)
* [Guida al posizionamento della fotocamera](spatial-analysis-camera-placement.md)
* [Guida al posizionamento di zone e linee](spatial-analysis-zone-line-placement.md)
