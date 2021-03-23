---
title: Configurare Microsoft OPC Publisher
description: Questa esercitazione illustra come configurare il server di pubblicazione OPC in modalità autonoma.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787807"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Esercitazione: configurare il server di pubblicazione OPC

In questa esercitazione vengono fornite informazioni sulla configurazione del server di pubblicazione OPC. Per la configurazione è possibile usare diverse interfacce.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare il server di pubblicazione OPC tramite il file di configurazione
> * Configurare il server di pubblicazione OPC tramite argomenti della riga di comando
> * Configurare il server di pubblicazione OPC tramite metodi diretti dell'hub Internet
> * Configurare il server di pubblicazione OPC tramite microservizi REST basati sul cloud e complementari

## <a name="configuring-security"></a>Configurazione della protezione

IoT Edge fornisce al server di pubblicazione OPC la configurazione di sicurezza per l'accesso automatico all'hub. Il server di pubblicazione OPC può essere eseguito anche come contenitore Docker autonomo specificando una stringa di connessione del dispositivo per l'accesso all'hub Internet tramite il `dc` parametro della riga di comando. È possibile creare un dispositivo per l'hub Internet e la relativa stringa di connessione recuperata tramite il portale di Azure.

Per accedere agli asset abilitati per OPC UA, i certificati X. 509 e le chiavi private associate vengono usati da OPC UA. Questa operazione è denominata autenticazione dell'applicazione OPC UA e in aggiunta all'autenticazione utente OPC UA. Il server di pubblicazione OPC usa un archivio certificati basato su file system per gestire tutti i certificati dell'applicazione. Durante l'avvio, il server di pubblicazione OPC verifica se è presente un certificato che può essere utilizzato in questo archivio certificati e crea un nuovo certificato autofirmato e una nuova chiave privata associata se non ne è presente alcuno. I certificati autofirmati forniscono un'autenticazione vulnerabile, poiché non sono firmati da un'autorità di certificazione attendibile, ma almeno la comunicazione con l'asset abilitata per OPC UA può essere crittografata in questo modo.

La sicurezza è abilitata nel file di configurazione tramite il `"UseSecurity": true,` flag. L'endpoint più sicuro disponibile nei server OPC UA a cui è prevista la connessione del server di pubblicazione OPC viene selezionato automaticamente.
Per impostazione predefinita, il server di pubblicazione OPC usa l'autenticazione utente anonima (in aggiunta all'autenticazione dell'applicazione descritta in precedenza). Tuttavia, il server di pubblicazione OPC supporta anche l'autenticazione utente mediante nome utente e password. Questo può essere specificato tramite l'interfaccia di configurazione dell'API REST (descritta di seguito) o il file di configurazione come segue:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Inoltre, il server di pubblicazione OPC versione 2,5 e la seguente crittografano il nome utente e la password nel file di configurazione. La versione 2,6 e successive supporta solo il nome utente e la password in testo non crittografato. Questa operazione sarà migliorata nella prossima versione di OPC Publisher.

Per salvare in modo permanente la configurazione di sicurezza del server di pubblicazione OPC tra i riavvii, il certificato e la chiave privata contenuti nella directory dell'archivio certificati devono essere mappati al file System del sistema operativo host IoT Edge. Vedere "specifica delle opzioni di creazione del contenitore nel portale di Azure" sopra.

## <a name="configuration-via-configuration-file"></a>Configurazione tramite il file di configurazione

Il modo più semplice per configurare il server di pubblicazione OPC è tramite un file di configurazione. Un file di configurazione di esempio, nonché la documentazione relativa al formato, viene fornito tramite il file [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) in questo repository.
La sintassi del file di configurazione è cambiata nel tempo e il server di pubblicazione OPC è ancora in grado di leggere i formati precedenti, ma li converte nel formato più recente durante la persistenza della configurazione, eseguita regolarmente in modo automatico.

Un file di configurazione di base ha un aspetto simile al seguente:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

Gli asset OPC UA ottimizzano la larghezza di banda di rete inviando solo le modifiche ai dati al server di pubblicazione OPC quando i dati vengono modificati. Se è necessario pubblicare le modifiche dei dati più spesso o a intervalli regolari, OPC Publisher supporta un "heartbeat" per ogni elemento di dati configurato che può essere abilitato specificando la chiave HeartbeatInterval nella configurazione dell'elemento dati. L'intervallo è specificato in secondi:
```
 "HeartbeatInterval": 3600,
```

Un asset OPC UA Invia sempre il valore corrente di un elemento di dati quando il server di pubblicazione OPC si connette per la prima volta. Per evitare la pubblicazione di questi dati nell'hub Internet, la chiave SkipFirst può essere specificata anche nella configurazione dell'elemento dati:
```
 "SkipFirst": true,
```

Entrambe le impostazioni possono essere abilitate a livello globale tramite le opzioni della riga di comando.

## <a name="configuration-via-command-line-arguments"></a>Configurazione tramite argomenti della riga di comando

Sono disponibili diversi argomenti della riga di comando che è possibile usare per impostare le impostazioni globali per il server di pubblicazione OPC. Sono descritte [qui](reference-command-line-arguments.md).


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Configurazione tramite l'interfaccia server OPC UA incorporata

>[!NOTE] 
> Questa funzionalità è disponibile solo nella versione 2,5 e nelle versioni precedenti del server di pubblicazione OPC. * *

Il server di pubblicazione OPC dispone di un server OPC UA incorporato, in esecuzione sulla porta 62222. Implementa tre metodi OPC UA:

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

È possibile accedere a questa interfaccia usando un'applicazione client OPC UA, ad esempio [Expert UA](https://www.unified-automation.com/products/development-tools/uaexpert.html).

## <a name="configuration-via-iot-hub-direct-methods"></a>Configurazione tramite metodi diretti dell'hub Internet

>[!NOTE] 
> Questa funzionalità è disponibile solo nella versione 2,5 e nelle versioni precedenti del server di pubblicazione OPC. * *

Il server di pubblicazione OPC implementa i [metodi diretti dell'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods)Internet seguenti, che possono essere chiamati da un'applicazione (da qualsiasi parte del mondo) sfruttando l' [SDK per dispositivi dell'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)Internet:

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

È disponibile un' [applicazione di configurazione di esempio](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) , oltre a un' [applicazione per la lettura di informazioni di diagnostica](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) da server di pubblicazione OPC open source, sfruttando questa interfaccia.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Configurazione tramite microservizi REST basati sul cloud e complementari

>[!NOTE] 
> Questa funzionalità è disponibile solo nella versione 2,6 e successive del server di pubblicazione OPC.

Un microservizio complementare basato sul cloud con un'interfaccia REST è descritto e disponibile [qui](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md). Può essere usato per configurare il server di pubblicazione OPC tramite un'interfaccia compatibile con OpenAPI, ad esempio tramite spavalderia.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Configurazione del formato di telemetria JSON semplice tramite un file di configurazione separato

>[!NOTE] 
> Questa funzionalità è disponibile solo nella versione 2,5 e nelle versioni precedenti del server di pubblicazione OPC.

Il server di pubblicazione OPC consente di filtrare le parti del formato di telemetria semplice e non standardizzato tramite un file di configurazione separato, che può essere specificato tramite l'opzione della riga di comando TC. Se non viene specificato alcun file di configurazione, il formato di telemetria JSON completo viene inviato all'hub. Il formato del file di configurazione di telemetria separato è descritto [qui](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format).

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato configurato il server di pubblicazione OPC, il passaggio successivo consiste nell'apprendere come ottimizzare le prestazioni e la memoria del modulo perimetrale:

> [!div class="nextstepaction"]
> [Ottimizzazione delle prestazioni e della memoria](tutorial-publisher-performance-memory-tuning-opc-publisher.md)