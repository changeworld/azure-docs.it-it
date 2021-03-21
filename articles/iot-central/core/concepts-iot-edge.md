---
title: Azure IoT Edge e IoT Central di Azure | Microsoft Docs
description: Informazioni su come usare Azure IoT Edge con un'applicazione IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: e0f3464420c5cb429f780999bf5983b2ab142567
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608632"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Connettere un dispositivo Azure IoT Edge a un'applicazione Azure IoT Central

*Le informazioni di questo articolo sono destinate a generatori di soluzioni e sviluppatori di dispositivi.*

Azure IoT Edge sposta le analisi del cloud e la logica di business personalizzata nei dispositivi, in modo che l'organizzazione possa concentrarsi sulle informazioni aziendali invece che sulla gestione dei dati. Ridimensionare la soluzione Internet delle cose con la creazione di pacchetti di logica di business in contenitori standard, distribuire i contenitori nei dispositivi e monitorarli dal cloud.

L'articolo illustra:

* Il modo in cui i dispositivi IoT Edge si connettono a un'applicazione IoT Central.
* Come usare IoT Central per gestire i dispositivi di IoT Edge.

Per ulteriori informazioni su IoT Edge, vedere [che cos'è Azure IOT Edge?](../../iot-edge/about-iot-edge.md)

## <a name="iot-edge"></a>IoT Edge

IoT Edge è costituito da tra componenti:

* I *moduli di IoT Edge* sono contenitori che eseguono servizi di Azure, servizi partner o il codice dell'utente. Vengono distribuiti nei dispositivi IoT Edge ed eseguiti in locale in tali dispositivi. Per altre informazioni, vedere informazioni sui [moduli Azure IOT Edge](../../iot-edge/iot-edge-modules.md).
* Il *runtime di IOT Edge* viene eseguito in ogni dispositivo IOT Edge e gestisce i moduli distribuiti in ogni dispositivo. Il runtime è costituito da due moduli IoT Edge: *IOT Edge Agent* e *IOT Edge Hub*. Per altre informazioni, vedere [comprendere il runtime di Azure IOT Edge e la relativa architettura](../../iot-edge/iot-edge-runtime.md).
* Un'*interfaccia basata sul cloud* consente di monitorare e gestire in remoto i dispositivi IoT Edge. IoT Central è un esempio di un'interfaccia cloud.

Un dispositivo IoT Edge può essere:

* Un dispositivo autonomo composto da moduli.
* Un *dispositivo gateway*, con i dispositivi downstream connessi.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge come gateway

Un dispositivo IoT Edge può funzionare come gateway che fornisce una connessione tra altri dispositivi downstream sulla rete e l'applicazione IoT Central.

Esistono due modelli di gateway:

* Nel modello di *gateway trasparente* , il modulo dell'hub IOT Edge si comporta come IOT Central e gestisce le connessioni dai dispositivi registrati in IOT Central. I messaggi passano da dispositivi downstream a IoT Central come se non vi fosse alcun gateway tra di essi.

* Nel modello del *gateway di traduzione* , i dispositivi che non possono connettersi a IOT Central autonomamente, si connettono a un modulo di IOT Edge personalizzato. Il modulo nel dispositivo IoT Edge elabora i messaggi di dispositivo downstream in arrivo e li invia al IoT Central.

I modelli del gateway trasparente e di conversione non si escludono a vicenda. Un singolo dispositivo IoT Edge può funzionare sia come gateway trasparente che come gateway di conversione.

Per altre informazioni sui modelli di gateway IoT Edge, vedere [come un dispositivo IOT Edge può essere usato come gateway](../../iot-edge/iot-edge-as-gateway.md).

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relazioni dei dispositivi downstream con il gateway e i moduli

I dispositivi downstream possono connettersi a un dispositivo gateway IoT Edge tramite il modulo *hub IOT Edge*  . In questo scenario, il dispositivo IoT Edge è un gateway trasparente:

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="Diagramma del gateway trasparente" border="false":::

I dispositivi downstream possono anche connettersi al dispositivo gateway IoT Edge tramite un modulo personalizzato. Nello scenario seguente i dispositivi downstream si connettono tramite un modulo *Modbus* personalizzato. In questo scenario, il dispositivo IoT Edge è un gateway di traduzione:

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="Diagramma della connessione del modulo personalizzato" border="false":::

Il diagramma seguente mostra le connessioni a un dispositivo gateway IoT Edge tramite entrambi i tipi di moduli. In questo scenario, il dispositivo IoT Edge è sia un gateway trasparente che un gateway di traduzione:

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="Diagramma della connessione tramite entrambi i moduli di connessione" border="false":::

I dispositivi downstream possono connettersi a un dispositivo gateway IoT Edge tramite più moduli personalizzati. Il diagramma seguente illustra i dispositivi downstream che si connettono tramite un modulo personalizzato Modbus, un modulo personalizzato BLE e il modulo *hub IOT Edge*  :

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="Diagramma della connessione tramite più moduli personalizzati" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>Dispositivi IoT Edge e IoT Central

I dispositivi IoT Edge possono usare token di *firma di accesso condiviso* o certificati X. 509 per l'autenticazione con IOT Central. È possibile registrare manualmente i dispositivi IoT Edge in IoT Central prima che si connettano per la prima volta o usare il servizio Device provisioning per gestire la registrazione. Per altre informazioni, vedere [Connettersi ad Azure IoT Central](concepts-get-connected.md).

IoT Central USA i [modelli di dispositivo](concepts-device-templates.md) per definire il modo in cui IOT Central interagisce con un dispositivo. Ad esempio, un modello di dispositivo specifica:

* Tipi di dati di telemetria e proprietà inviati da un dispositivo in modo che IoT Central possano interpretarli e creare visualizzazioni.
* I comandi a cui un dispositivo risponde, in modo che IoT Central possibile visualizzare un'interfaccia utente per un operatore da usare per chiamare i comandi.

Un dispositivo IoT Edge può inviare dati di telemetria, sincronizzare i valori delle proprietà e rispondere ai comandi in modo analogo a un dispositivo standard. Quindi, un dispositivo IoT Edge necessita di un modello di dispositivo IoT Central.

### <a name="iot-edge-device-templates"></a>Modelli di dispositivo IoT Edge

IoT Central modelli di dispositivo usano i modelli per descrivere le funzionalità dei dispositivi. Il diagramma seguente illustra la struttura del modello per un dispositivo IoT Edge:

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="Struttura del modello per IoT Edge dispositivo connesso a IoT Central" border="false":::

IoT Central modella un dispositivo IoT Edge come segue:

* Ogni modello di dispositivo IoT Edge dispone di un modello di funzionalità.
* Per ogni modulo personalizzato elencato nel manifesto di distribuzione, viene generato un modello di funzionalità del modulo.
* Viene stabilita una relazione tra ogni modello di capacità del modulo e un modello di dispositivo.
* Un modello di funzionalità del modulo implementa una o più interfacce del modulo.
* Ogni interfaccia del modulo contiene i dati di telemetria, le proprietà e i comandi.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>Manifesti della distribuzione IoT Edge e modelli di dispositivo IoT Central

In IoT Edge si distribuisce e si gestisce la logica di business sotto forma di moduli. I moduli IoT Edge sono la più piccola unità di calcolo gestita da IoT Edge e possono contenere servizi di Azure, ad esempio analisi di flusso di Azure, o codice specifico della soluzione.

Un *manifesto di distribuzione* IOT Edge elenca i moduli IOT Edge da distribuire nel dispositivo e come configurarli. Per altre informazioni, vedere [informazioni su come distribuire moduli e definire route in IOT Edge](../../iot-edge/module-composition.md).

In IoT Central di Azure è possibile importare un manifesto di distribuzione per creare un modello di dispositivo per il dispositivo IoT Edge.

Il frammento di codice seguente mostra un esempio IoT Edge manifesto di distribuzione:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

Nel frammento di codice precedente è possibile vedere:

* Sono disponibili tre moduli. I moduli di sistema *IOT Edge Agent* e *IOT Edge Hub* presenti in ogni manifesto della distribuzione. Il modulo **SimulatedTemperatureSensor** personalizzato.
* Le immagini del modulo pubblico vengono recuperate da un repository di Azure Container Registry che non richiede credenziali per la connessione. Per le immagini del modulo privato, impostare le credenziali del registro contenitori da usare nell' `registryCredentials` impostazione del modulo *agente IOT Edge* .
* Il modulo **SimulatedTemperatureSensor** personalizzato ha due proprietà `"SendData": true` e `"SendInterval": 10` .

Quando si importa questo manifesto di distribuzione in un'applicazione IoT Central, viene generato il modello di dispositivo seguente:

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="Screenshot che mostra il modello di dispositivo creato dal manifesto della distribuzione.":::

Nella schermata precedente è possibile vedere:

* Un modulo denominato **SimulatedTemperatureSensor**. I moduli dell' *agente IOT Edge* e del sistema *Hub IOT Edge* non vengono visualizzati nel modello.
* Interfaccia denominata **gestione** che include due proprietà scrivibili denominate **SendData** e **SendInterval**.

Il manifesto di distribuzione non include informazioni sui dati di telemetria inviati dal modulo **SimulatedTemperatureSensor** o sui comandi a cui risponde. Aggiungere queste definizioni al modello di dispositivo manualmente prima di pubblicarlo.

Per altre informazioni, vedere [esercitazione: aggiungere un dispositivo Azure IOT Edge all'applicazione Azure IOT Central](tutorial-add-edge-as-leaf-device.md).

### <a name="update-a-deployment-manifest"></a>Aggiornare un manifesto di distribuzione

Se si crea una nuova [versione](howto-version-device-template.md) del modello di dispositivo, è possibile sostituire il manifesto di distribuzione con una nuova versione:

Quando si sostituisce il manifesto di distribuzione, tutti i dispositivi IoT Edge connessi scaricano il nuovo manifesto e ne aggiornano i moduli. Tuttavia, IoT Central non aggiorna le interfacce nel modello di dispositivo con le modifiche apportate alla configurazione del modulo. Se ad esempio si sostituisce il manifesto mostrato nel frammento di codice precedente con il manifesto seguente, la proprietà **SendUnits** non viene visualizzata automaticamente nell'interfaccia di **gestione** del modello di dispositivo. Aggiungere manualmente la nuova proprietà all'interfaccia di **gestione** per IOT Central per riconoscerla:

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>Distribuire il runtime di IoT Edge

Per informazioni su dove è possibile eseguire il runtime di IoT Edge, vedere [Azure IOT Edge sistemi supportati](../../iot-edge/support.md).

È anche possibile installare il runtime di IoT Edge negli ambienti seguenti:

* [Installare o disinstallare Azure IoT Edge per Linux](../../iot-edge/how-to-install-iot-edge.md)
* [Installare Azure IoT Edge per Linux in un dispositivo Windows ed effettuare il provisioning (anteprima)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Eseguire Azure IoT Edge in macchine virtuali Ubuntu in Azure](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>Dispositivi IoT Edge Gateway

Se è stato selezionato un dispositivo IoT Edge come dispositivo gateway, è possibile aggiungere relazioni downstream ai modelli di dispositivo per i dispositivi che si desidera connettere al dispositivo gateway.

Per altre informazioni, vedere [How to Connect Devices through an IOT Edge Transparent Gateway](how-to-connect-iot-edge-transparent-gateway.md).

## <a name="next-steps"></a>Passaggi successivi

Per gli sviluppatori di dispositivi, un passaggio successivo suggerito consiste nell'apprendere come [sviluppare moduli IOT Edge personalizzati](../../iot-edge/module-development.md).
