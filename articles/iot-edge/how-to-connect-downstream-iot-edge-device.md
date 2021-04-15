---
title: Connettere i dispositivi IoT Edge downstream - Azure IoT Edge | Microsoft Docs
description: Come configurare un dispositivo IoT Edge per connettersi ai dispositivi Azure IoT Edge gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 500833d1bb4fc492942c08239bd488c2d2c16d30
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484320"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway"></a>Connettere un dispositivo IoT Edge downstream a un gateway Azure IoT Edge remoto

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Questo articolo fornisce istruzioni per stabilire una connessione attendibile tra un gateway IoT Edge e un dispositivo IoT Edge downstream.

In uno scenario gateway, un IoT Edge può essere sia un gateway che un dispositivo downstream. Più IoT Edge gateway possono essere a livelli per creare una gerarchia di dispositivi. I dispositivi downstream (o figlio) possono autenticare e inviare o ricevere messaggi tramite il dispositivo gateway (o padre).

Esistono due diverse configurazioni per i dispositivi IoT Edge in una gerarchia di gateway e questo articolo tratta entrambe le soluzioni. Il primo è il **livello superiore IoT Edge** dispositivo. Quando più IoT Edge si connettono tra loro, qualsiasi dispositivo che non dispone di un dispositivo padre ma si connette direttamente all'hub IoT viene considerato nel livello superiore. Questo dispositivo è responsabile della gestione delle richieste da tutti i dispositivi sottostanti. L'altra configurazione si applica IoT Edge dispositivo in un **livello inferiore** della gerarchia. Questi dispositivi possono essere un gateway per altri dispositivi IoT e IoT Edge downstream, ma devono anche instradare le comunicazioni tramite i propri dispositivi padre.

Alcune architetture di rete richiedono che solo il primo IoT Edge in una gerarchia possa connettersi al cloud. In questa configurazione, tutti IoT Edge nei livelli inferiori di una gerarchia possono comunicare solo con il dispositivo gateway (o padre) e con qualsiasi dispositivo downstream (o figlio).

Tutti i passaggi di questo articolo si basano su quelli descritti in Configurare un dispositivo [IoT Edge per](how-to-create-transparent-gateway.md)fungere da gateway trasparente, che configura un dispositivo IoT Edge come gateway per i dispositivi IoT downstream. Gli stessi passaggi di base si applicano a tutti gli scenari del gateway:

* **Autenticazione:** creare identità dell'hub IoT per tutti i dispositivi nella gerarchia del gateway.
* **Autorizzazione:** configurare la relazione padre/figlio nell'hub IoT per autorizzare i dispositivi figlio a connettersi al dispositivo padre in modo che si connettono all'hub IoT.
* **Individuazione gateway:** assicurarsi che il dispositivo figlio possa trovare il dispositivo padre nella rete locale.
* **Connessione sicura:** stabilire una connessione sicura con certificati attendibili che fanno parte della stessa catena.

## <a name="prerequisites"></a>Prerequisiti

* Hub IoT gratuito o standard.
* Almeno due dispositivi **IoT Edge**, uno per essere il dispositivo di livello superiore e uno o più dispositivi di livello inferiore. Se non sono disponibili dispositivi IoT Edge, è possibile eseguire Azure IoT Edge in macchine virtuali [Ubuntu.](how-to-install-iot-edge-ubuntuvm.md)
* Se si usa l'interfaccia della riga di comando di Azure per creare e gestire i dispositivi, installare l'interfaccia della riga di comando di Azure v2.3.1 con l'estensione Azure IoT v0.10.6 o versione successiva.

Questo articolo illustra i passaggi e le opzioni dettagliati che consentono di creare la gerarchia di gateway più semplice per lo scenario. Per un'esercitazione guidata, vedere [Creare una gerarchia](tutorial-nested-iot-edge.md)di IoT Edge dispositivi con gateway .

## <a name="create-a-gateway-hierarchy"></a>Creare una gerarchia di gateway

Per creare una gerarchia IoT Edge gateway, definire relazioni padre/figlio per IoT Edge dispositivi nello scenario. È possibile impostare un dispositivo padre quando si crea una nuova identità del dispositivo oppure è possibile gestire l'identità padre e figlio di un'identità del dispositivo esistente.

Il passaggio di configurazione delle relazioni padre/figlio autorizza i dispositivi figlio a connettersi al dispositivo padre in modo che si connettono all'hub IoT.

Solo IoT Edge dispositivi possono essere dispositivi padre, ma entrambi IoT Edge dispositivi IoT possono essere figlio. Un elemento padre può avere molti elementi figlio, ma un elemento figlio può avere un solo elemento padre. Una gerarchia di gateway viene creata concatenando i set padre/figlio in modo che l'elemento figlio di un dispositivo sia il padre di un altro.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nel portale di Azure, è possibile gestire la relazione padre/figlio quando si creano nuove identità del dispositivo o modificando i dispositivi esistenti.

Quando si crea un nuovo dispositivo IoT Edge, è possibile scegliere i dispositivi padre e figlio dall'elenco dei dispositivi IoT Edge esistenti nell'hub.

1. Nel [portale di Azure](https://portal.azure.com)passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu di spostamento.
1. Selezionare **Aggiungi un dispositivo IoT Edge**.
1. Oltre a impostare l'ID dispositivo e le impostazioni di autenticazione, è possibile impostare un **dispositivo padre** o Scegliere i **dispositivi figlio.**
1. Scegliere il dispositivo o i dispositivi da usare come padre o figlio.

È anche possibile creare o gestire relazioni padre/figlio per i dispositivi esistenti.

1. Nel [portale di Azure](https://portal.azure.com)passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu di spostamento.
1. Selezionare il dispositivo che si vuole gestire dall'elenco di IoT Edge **dispositivi**.
1. Selezionare **Imposta un dispositivo padre o** Gestisci dispositivi **figlio**.
1. Aggiungere o rimuovere i dispositivi padre o figlio.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[L'estensione azure-iot](/cli/azure/iot) per l'interfaccia della riga di comando di Azure fornisce comandi per gestire le risorse IoT. È possibile gestire la relazione padre/figlio dei dispositivi IoT e IoT Edge quando si creano nuove identità del dispositivo o modificando i dispositivi esistenti.

Il [set di comandi az iot hub device-identity](/cli/azure/iot/hub/device-identity) consente di gestire le relazioni padre/figlio per un determinato dispositivo.

Il `create` comando include parametri per l'aggiunta di dispositivi figlio e l'impostazione di un dispositivo padre al momento della creazione del dispositivo.

Altri comandi di identità del dispositivo, tra cui , e o e , consentono di gestire `add-children` `list-children` le relazioni `remove-children` `get-parent` `set-parent` padre/figlio per i dispositivi esistenti.

---

## <a name="prepare-certificates"></a>Preparare i certificati

Per stabilire una comunicazione sicura tra loro, è necessario installare una catena coerente di certificati tra dispositivi nella stessa gerarchia di gateway. Ogni dispositivo nella gerarchia, che si IoT Edge dispositivo foglia o ioT, necessita di una copia dello stesso certificato CA radice. Ogni IoT Edge nella gerarchia usa quindi il certificato della CA radice come radice per il certificato della CA del dispositivo.

Con questa configurazione, ogni dispositivo IoT Edge downstream o dispositivo foglia IoT può verificare l'identità del padre verificando che edgeHub a cui si connettono abbia un certificato server firmato dal certificato della CA radice condivisa.

<!-- TODO: certificate graphic -->

Creare i certificati seguenti:

* Un **certificato CA radice,** ovvero il certificato condiviso più in alto per tutti i dispositivi in una gerarchia di gateway specificata. Questo certificato viene installato in tutti i dispositivi.
* Eventuali **certificati intermedi** da includere nella catena di certificati radice.
* Un **certificato CA del dispositivo** e la relativa chiave **privata,** generati dai certificati radice e intermedi. È necessario un certificato CA univoco per ogni dispositivo IoT Edge nella gerarchia del gateway.

È possibile usare un'autorità di certificazione autofirmata o acquistarne una da un'autorità di certificazione commerciale attendibile, ad esempio Baltimore, Verisign, Digicert o GlobalSign.

Se non si hanno certificati personalizzati da usare, è possibile creare certificati demo per testare le funzionalità [IoT Edge dispositivo.](how-to-create-test-certificates.md) Seguire i passaggi descritti in questo articolo per creare un set di certificati radice e intermedi, quindi per creare IoT Edge certificati CA del dispositivo per ognuno dei dispositivi.

## <a name="configure-iot-edge-on-devices"></a>Configurare IoT Edge nei dispositivi

I passaggi per la configurazione IoT Edge come gateway sono molto simili ai passaggi per la configurazione IoT Edge come dispositivo downstream.

Per abilitare l'individuazione del gateway, ogni dispositivo gateway IoT Edge deve essere configurato con un **nome host** che verrà utilizzato dai dispositivi figlio per trovarlo nella rete locale. Ogni dispositivo IoT Edge deve essere configurato con un parent_hostname **a** cui connettersi. Se un singolo IoT Edge è sia un dispositivo padre che un dispositivo figlio, sono necessari entrambi i parametri.

Per abilitare connessioni sicure, ogni dispositivo IoT Edge in uno scenario gateway deve essere configurato con un certificato CA univoco del dispositivo e una copia del certificato CA radice condiviso da tutti i dispositivi nella gerarchia del gateway.

È necessario avere già IoT Edge installato nel dispositivo. In caso contrario, seguire la procedura descritta in Registrare [un dispositivo IoT Edge nell'hub IoT](how-to-register-device.md) e quindi [installare il runtime Azure IoT Edge.](how-to-install-iot-edge.md)

I passaggi descritti in questa sezione fanno riferimento al **certificato CA radice** e al certificato ca del dispositivo e alla chiave **privata** descritti in precedenza in questo articolo. Se i certificati sono stati creati in un dispositivo diverso, è necessario che sia disponibile in questo dispositivo. È possibile trasferire i file fisicamente, ad esempio con un'unità USB, con un servizio come [Azure Key Vault](../key-vault/general/overview.md)o con una funzione come [Secure file copy](https://www.ssh.com/ssh/scp/).

Usare la procedura seguente per configurare IoT Edge nel dispositivo.

Assicurarsi che l'utente **iotedge abbia** le autorizzazioni di lettura per la directory che contiene i certificati e le chiavi.

1. Installare il **certificato CA radice** in questo IoT Edge dispositivo.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. Aggiornare l'archivio certificati.

   ```bash
   sudo update-ca-certificates
   ```

   Questo comando restituisce che è stato aggiunto un certificato a /etc/ssl/certs.

1. Aprire il file di configurazione di IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >Se il file di configurazione non esiste ancora nel dispositivo, usare `/etc/aziot/config.toml.edge.template` come modello per crearne uno.

1. Trovare la **sezione Hostname** nel file di configurazione. Rimuovere il commento dalla riga che contiene il parametro e aggiornare il valore in modo che sia il nome di dominio completo `hostname` (FQDN) o l'indirizzo IP del IoT Edge dispositivo.

   Il valore di questo parametro è quello che i dispositivi downstream useranno per connettersi a questo gateway. Il nome host accetta il nome del computer per impostazione predefinita, ma l'FQDN o l'indirizzo IP è necessario per connettere i dispositivi downstream.

   Usare un nome host più breve di 64 caratteri, che rappresenta il limite di caratteri per un nome comune del certificato del server.

   Coerenza con il modello di nome host in una gerarchia di gateway. Usare nomi fqdn o indirizzi IP, ma non entrambi.

1. *Se si tratta di un dispositivo figlio,* trovare la **sezione Nome host** padre. Rimuovere il commento e aggiornare il parametro in modo che sia l'FQDN o l'indirizzo IP del dispositivo padre, corrispondente a quello specificato come nome host nel file di configurazione `parent_hostname` del dispositivo padre.

1. Trovare la **sezione Trust bundle cert (Certificato bundle** di attendibilità). Rimuovere il commento e aggiornare il `trust_bundle_cert` parametro con l'URI del file nel certificato CA radice nel dispositivo.

1. Verificare che IoT Edge dispositivo usi la versione corretta dell'agente IoT Edge all'avvio.

   Trovare la **sezione Default Edge Agent** (Agente edge predefinito) e verificare che il valore dell'immagine IoT Edge versione 1.2. In caso contrario, aggiornarlo:

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2"
   ```

1. Trovare la **sezione Del certificato della CA** edge nel file di configurazione. Rimuovere il commento delle righe in questa sezione e specificare i percorsi URI del file per il certificato e i file di chiave nel IoT Edge dispositivo.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Salvare ( `Ctrl+O` ) e chiudere ( ) il file di `Ctrl+X` configurazione.

1. Se in precedenza sono stati usati altri certificati IoT Edge, eliminare i file nelle due directory seguenti per assicurarsi che i nuovi certificati siano applicati:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. Scegliere Applica per applicare le modifiche.

   ```bash
   sudo iotedge config apply
   ```

1. Verificare la presenza di eventuali errori nella configurazione.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >Lo IoT Edge di controllo usa un contenitore per eseguire parte del controllo di diagnostica. Se si vuole usare questo strumento nei dispositivi IoT Edge downstream, assicurarsi che possano accedere a o avere l'immagine del contenitore nel registro `mcr.microsoft.com/azureiotedge-diagnostics:latest` contenitori privato.

## <a name="network-isolate-downstream-devices"></a>Isolamento della rete dei dispositivi downstream

I passaggi descritti in questo articolo configurano IoT Edge dispositivi come gateway o dispositivo downstream e creano una connessione attendibile tra di essi. Il dispositivo gateway gestisce le interazioni tra il dispositivo downstream e l'hub IoT, tra cui l'autenticazione e il routing dei messaggi. I moduli distribuiti in dispositivi downstream IoT Edge possono comunque creare le proprie connessioni ai servizi cloud.

Alcune architetture di rete, come quelle che seguono lo standard ISA-95, cercano di ridurre al minimo il numero di connessioni Internet. In questi scenari è possibile configurare i dispositivi downstream IoT Edge senza connettività Internet diretta. Oltre a instradare le comunicazioni dell'hub IoT tramite il dispositivo gateway, i dispositivi IoT Edge downstream possono basarsi sul dispositivo gateway per tutte le connessioni cloud.

Questa configurazione di rete richiede che solo il IoT Edge nel livello superiore di una gerarchia di gateway abbia connessioni dirette al cloud. IoT Edge i dispositivi nei livelli inferiori possono comunicare solo con il dispositivo padre o con qualsiasi dispositivo figlio. I moduli speciali nei dispositivi gateway abilitano questo scenario, tra cui:

* Il **modulo proxy API** è obbligatorio in qualsiasi gateway IoT Edge che dispone di un altro IoT Edge dispositivo sottostante. Ciò significa che deve essere su *ogni livello di* una gerarchia del gateway, ad eccezione del livello inferiore. Questo modulo usa un proxy [inverso nginx](https://nginx.org) per instradare i dati HTTP attraverso i livelli di rete su una singola porta. È altamente configurabile tramite il modulo gemello e le variabili di ambiente, quindi può essere modificato in base ai requisiti dello scenario del gateway.

* Il **modulo registro Docker** può essere distribuito nel gateway IoT Edge livello *superiore* di una gerarchia di gateway. Questo modulo è responsabile del recupero e della memorizzazione nella cache delle immagini dei contenitori per conto di tutti IoT Edge dispositivi nei livelli inferiori. L'alternativa alla distribuzione di questo modulo al livello superiore consiste nell'usare un registro locale o nel caricare manualmente le immagini dei contenitori nei dispositivi e impostare i criteri di pull del modulo su **mai**.

* Il **Archiviazione BLOB di Azure nel IoT Edge** può essere distribuito nel gateway IoT Edge livello *superiore* di una gerarchia di gateway. Questo modulo è responsabile del caricamento dei BLOB per conto di tutti IoT Edge dispositivi nei livelli inferiori. La possibilità di caricare i BLOB consente anche funzionalità utili per la risoluzione dei IoT Edge nei livelli inferiori, ad esempio il caricamento del log dei moduli e il caricamento di bundle di supporto.

### <a name="network-configuration"></a>Configurazione di rete

Per ogni dispositivo gateway nel livello superiore, gli operatori di rete devono:

* Specificare un indirizzo IP statico o un nome di dominio completo (FQDN).
* Autorizzare le comunicazioni in uscita da questo indirizzo IP al nome host hub IoT di Azure tramite le porte 443 (HTTPS) e 5671 (AMQP).
* Autorizzare le comunicazioni in uscita da questo indirizzo IP al nome host Registro Azure Container sulla porta 443 (HTTPS).

  Il modulo proxy API può gestire solo le connessioni a un registro contenitori alla volta. È consigliabile avere tutte le immagini del contenitore, incluse le immagini pubbliche fornite da Microsoft Container Registry (mcr.microsoft.com), archiviate nel registro contenitori privato.

Per ogni dispositivo gateway in un livello inferiore, gli operatori di rete devono:

* Specificare un indirizzo IP statico.
* Autorizzare le comunicazioni in uscita da questo indirizzo IP all'indirizzo IP del gateway padre tramite le porte 443 (HTTPS) e 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Distribuire moduli nei dispositivi di livello superiore

Il IoT Edge al livello superiore di una gerarchia di gateway include un set di moduli obbligatori che devono essere distribuiti in esso, oltre a tutti i moduli del carico di lavoro che è possibile eseguire nel dispositivo.

Il modulo proxy API è stato progettato per essere personalizzato per gestire gli scenari di gateway più comuni. Questo articolo fornisce ed esempio per configurare i moduli in una configurazione di base. Per informazioni [ed esempi più dettagliati,](how-to-configure-api-proxy-module.md) vedere Configurare il modulo proxy API per lo scenario della gerarchia del gateway.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com)passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu di spostamento.
1. Selezionare il dispositivo di livello superiore che si sta configurando dall'elenco di IoT Edge **dispositivi**.
1. Selezionare **Imposta moduli**.
1. Nella sezione **IoT Edge moduli** selezionare **Aggiungi** e quindi scegliere **Modulo Marketplace.**
1. Cercare e selezionare il modulo **proxy IoT Edge API.**
1. Selezionare il nome del modulo proxy API dall'elenco dei moduli distribuiti e aggiornare le impostazioni del modulo seguenti:
   1. Nella scheda **Variabili di ambiente** aggiornare il valore di NGINX_DEFAULT_PORT a  `443` .
   1. Nella scheda **Opzioni di creazione contenitore** aggiornare le associazioni di porta in modo che fanno riferimento alla porta 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Queste modifiche configurano il modulo proxy API per l'ascolto sulla porta 443. Per evitare conflitti di associazione di porte, è necessario configurare il modulo edgeHub in modo che non sia in ascolto sulla porta 443. Il modulo proxy API instraderà invece qualsiasi traffico edgeHub sulla porta 443.

1. Selezionare **Impostazioni di runtime** e trovare le opzioni di creazione del modulo edgeHub. Eliminare il binding di porta per la porta 443, lasciando le associazioni per le porte 5671 e 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Selezionare **Salva** per salvare le modifiche apportate alle impostazioni di runtime.
1. Selezionare **di** nuovo Aggiungi, quindi scegliere **IoT Edge modulo**.
1. Specificare i valori seguenti per aggiungere il modulo del registro Docker alla distribuzione:
   1. **IoT Edge modulo:**`registry`
   1. Nella scheda **Impostazioni modulo** URI **immagine**: `registry:latest`
   1. Nella scheda **Variabili di** ambiente aggiungere le variabili di ambiente seguenti:

      * **Nome:** `REGISTRY_PROXY_REMOTEURL` **Valore:** l'URL del registro contenitori a cui si vuole eseguire il mapping del modulo del registro. Ad esempio: `https://myregistry.azurecr`.

        Il modulo registry può eseguire il mapping solo a un registro contenitori, quindi è consigliabile avere tutte le immagini del contenitore in un unico registro contenitori privato.

      * **Nome:** `REGISTRY_PROXY_USERNAME` **Valore:** nome utente per l'autenticazione nel registro contenitori.

      * **Nome:** `REGISTRY_PROXY_PASSWORD` **Valore:** password per l'autenticazione nel registro contenitori.

   1. Nella scheda **Opzioni di creazione contenitore** incollare:

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. Selezionare **Aggiungi** per aggiungere il modulo alla distribuzione.
1. Selezionare **Avanti: Route** per andare al passaggio successivo.
1. Per consentire ai messaggi da dispositivo a cloud provenienti da dispositivi downstream di raggiungere l'hub IoT, includere una route che passi tutti i messaggi all'hub IoT. Ad esempio:
    1. **Nome**: `Route`
    1. **Valore**: `FROM /messages/* INTO $upstream`
1. Selezionare **Rivedi e** crea per andare al passaggio finale.
1. Selezionare **Crea** per eseguire la distribuzione nel dispositivo.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Nella finestra [Azure Cloud Shell](https://shell.azure.com/)creare un file JSON di distribuzione. Ad esempio:

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "The URL for the container registry you want this registry module to map to. For example, https://myregistry.azurecr"
                               },
                               "REGISTRY_PROXY_USERNAME": {
                                   "value": "Username to authenticate to the container registry."
                               },
                               "REGISTRY_PROXY_PASSWORD": {
                                   "value": "Password to authenticate to the container registry."
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy:1.0",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"443/tcp\": [{\"HostPort\":\"443\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "443"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {},
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

   Questo file di distribuzione configura il modulo proxy API per l'ascolto sulla porta 443. Per evitare conflitti di binding delle porte, il file configura il modulo edgeHub in modo che non sia in ascolto sulla porta 443. Il modulo proxy API instraderà invece qualsiasi traffico edgeHub sulla porta 443.

1. Immettere il comando seguente per creare una distribuzione in un IoT Edge dispositivo:

   ```bash
   az iot edge set-modules --device-id <device_id> --hub-name <iot_hub_name> --content ./<deployment_file_name>.json
   ```

---

### <a name="deploy-modules-to-lower-layer-devices"></a>Distribuire moduli nei dispositivi di livello inferiore

IoT Edge i dispositivi nei livelli inferiori di una gerarchia di gateway hanno un modulo obbligatorio che deve essere distribuito in essi, oltre a tutti i moduli di carico di lavoro che è possibile eseguire nel dispositivo.

#### <a name="route-container-image-pulls"></a>Pull dell'immagine del contenitore di route

Prima di illustrare il modulo proxy necessario per i dispositivi IoT Edge nelle gerarchie del gateway, è importante comprendere come i IoT Edge nei livelli inferiori ottengono le immagini del modulo.

Se i dispositivi di livello inferiore non possono connettersi al cloud, ma si vuole eseguire il pull delle immagini del modulo come di consueto, è necessario configurare il dispositivo di livello superiore della gerarchia del gateway per gestire queste richieste. Il dispositivo di livello superiore deve eseguire un modulo del registro **Docker** mappato al registro contenitori. Configurare quindi il modulo proxy API per instradare le richieste del contenitore. Questi dettagli sono illustrati nelle sezioni precedenti di questo articolo. In questa configurazione, i dispositivi di livello inferiore non devono puntare ai registri contenitori cloud, ma al registro in esecuzione nel livello superiore.

Ad esempio, invece di chiamare `mcr.microsoft.com/azureiotedge-api-proxy:1.0` , i dispositivi di livello inferiore devono chiamare `$upstream:443/azureiotedge-api-proxy:1.0` .

Il **parametro $upstream** punta all'elemento padre di un dispositivo di livello inferiore, quindi la richiesta instraderà attraverso tutti i livelli fino a raggiungere il livello superiore con un ambiente proxy che instraderà le richieste del contenitore al modulo del Registro di sistema. La porta in questo esempio deve essere sostituita con la porta su cui è in ascolto il modulo `:443` proxy API nel dispositivo padre.

Il modulo proxy API può essere indirizzato solo a un modulo del Registro di sistema e ogni modulo del Registro di sistema può eseguire il mapping solo a un registro contenitori. Pertanto, tutte le immagini che i dispositivi di livello inferiore devono eseguire il pull devono essere archiviate in un singolo registro contenitori.

Se non si vogliono dispositivi di livello inferiore che effettuano richieste pull del modulo tramite una gerarchia di gateway, un'altra opzione consiste nel gestire una soluzione del Registro di sistema locale. In caso contrario, eseguire il push delle immagini del modulo nei dispositivi prima di creare le distribuzioni e quindi impostare **imagePullPolicy** su **mai**.

#### <a name="bootstrap-the-iot-edge-agent"></a>Avviare l'agente IoT Edge

L IoT Edge agente è il primo componente di runtime da avviare in qualsiasi IoT Edge dispositivo. È necessario assicurarsi che tutti i dispositivi IoT Edge downstream possano accedere all'immagine del modulo edgeAgent all'avvio e quindi possano accedere alle distribuzioni e avviare il resto delle immagini del modulo.

Quando si passa al file di configurazione in un dispositivo IoT Edge per fornire le informazioni di autenticazione, i certificati e il nome host padre, aggiornare anche l'immagine del contenitore edgeAgent.

Se il dispositivo gateway di primo livello è configurato per gestire le richieste di immagini del contenitore, sostituire con il nome host padre e la porta di ascolto `mcr.microsoft.com` del proxy API. Nel manifesto della distribuzione è possibile usare come collegamento, ma ciò richiede che il modulo edgeHub gestirà il routing e tale modulo non è `$upstream` stato avviato a questo punto. Ad esempio:

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2"
```

Se si usa un registro contenitori locale o si forniscono manualmente le immagini del contenitore nel dispositivo, aggiornare il file di configurazione di conseguenza.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Configurare il runtime e distribuire il modulo proxy

Il **modulo proxy API è** necessario per il routing di tutte le comunicazioni tra il cloud e qualsiasi dispositivo IoT Edge downstream. Un IoT Edge nel livello inferiore della gerarchia, senza dispositivi IoT Edge downstream, non richiede questo modulo.

Il modulo proxy API è stato progettato per essere personalizzato per gestire gli scenari di gateway più comuni. Questo articolo illustra brevemente i passaggi per configurare i moduli in una configurazione di base. Per informazioni [dettagliate ed esempi,](how-to-configure-api-proxy-module.md) vedere Configurare il modulo proxy API per lo scenario della gerarchia di gateway.

1. Nel [portale di Azure](https://portal.azure.com)passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu di spostamento.
1. Selezionare il dispositivo di livello inferiore che si sta configurando dall'elenco di IoT Edge **dispositivi**.
1. Selezionare **Imposta moduli**.
1. Nella sezione **IoT Edge moduli** selezionare **Aggiungi e** quindi scegliere Modulo **Marketplace.**
1. Cercare e selezionare il modulo **proxy IoT Edge API.**
1. Selezionare il nome del modulo proxy API dall'elenco dei moduli distribuiti e aggiornare le impostazioni del modulo seguenti:
   1. Nella scheda **Impostazioni modulo** aggiornare il valore di **URI immagine**. Sostituire `mcr.microsoft.com` con `$upstream:443`.
   1. Nella scheda **Variabili di ambiente** aggiornare il valore di NGINX_DEFAULT_PORT a  `443` .
   1. Nella scheda **Opzioni di creazione contenitore** aggiornare le associazioni di porta in modo che fanno riferimento alla porta 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Queste modifiche configurano il modulo proxy API per l'ascolto sulla porta 443. Per evitare conflitti di associazione di porte, è necessario configurare il modulo edgeHub in modo che non sia in ascolto sulla porta 443. Il modulo proxy API instraderà invece qualsiasi traffico edgeHub sulla porta 443.

1. Selezionare **Impostazioni di runtime**.
1. Aggiornare le impostazioni del modulo edgeHub:

   1. Nel campo **Immagine** sostituire `mcr.microsoft.com` con `$upstream:443` .
   1. Nel campo **Crea opzioni** eliminare il binding di porta per la porta 443, lasciando le associazioni per le porte 5671 e 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Aggiornare le impostazioni del modulo edgeAgent:
   1. Nel campo **Immagine** sostituire `mcr.microsoft.com` con `$upstream:443` .

1. Selezionare **Salva** per salvare le modifiche apportate alle impostazioni di runtime.
1. Selezionare **Avanti: Route** per andare al passaggio successivo.
1. Per abilitare i messaggi da dispositivo a cloud dai dispositivi downstream per raggiungere l'hub IoT, includere una route che passi tutti i messaggi a `$upstream` . Il parametro upstream punta al dispositivo padre nel caso di dispositivi di livello inferiore. Ad esempio:
    1. **Nome**: `Route`
    1. **Valore**: `FROM /messages/* INTO $upstream`
1. Selezionare **Rivedi e crea** per andare al passaggio finale.
1. Selezionare **Crea** per eseguire la distribuzione nel dispositivo.

## <a name="next-steps"></a>Passaggi successivi

[Come usare un dispositivo Azure IoT Edge come gateway](iot-edge-as-gateway.md)

[Configurare il modulo proxy API per lo scenario della gerarchia del gateway](how-to-configure-api-proxy-module.md)