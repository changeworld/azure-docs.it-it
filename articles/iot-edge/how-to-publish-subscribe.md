---
title: Pubblicare e sottoscrivere Azure IoT Edge | Microsoft Docs
description: Usare IoT Edge broker MQTT per pubblicare e sottoscrivere messaggi
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1c4760362e7c2b3965638b3213910b5b8cd6f079
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516179"
---
# <a name="publish-and-subscribe-with-azure-iot-edge-preview"></a>Pubblicare e sottoscrivere Azure IoT Edge (anteprima)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

È possibile usare Azure IoT Edge broker MQTT per pubblicare e sottoscrivere i messaggi. Questo articolo illustra come connettersi a questo broker, pubblicare e sottoscrivere messaggi su argomenti definiti dall'utente e usare le primitive di messaggistica dell'hub IoT. Il IoT Edge MQTT broker è incorporato nell'hub IoT Edge servizio. Per altre informazioni, vedere [le funzionalità di brokering dell'hub IoT Edge .](iot-edge-runtime.md)

> [!NOTE]
> IoT Edge broker MQTT è attualmente in anteprima pubblica.

## <a name="pre-requisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione valida
- [Interfaccia della riga di comando di Azure](/cli/azure/) con l'estensione `azure-iot` dell'interfaccia della riga di comando installata. Per altre informazioni, vedere la procedura di [installazione dell'Azure IoT per](/cli/azure/azure-cli-reference-for-iot)l'interfaccia della riga di comando di Azure.
- Hub **IoT** dello SKU F1, S1, S2 o S3.
- Avere un **IoT Edge con versione 1.2 o successiva.** Poiché IoT Edge broker MQTT è attualmente in anteprima pubblica, impostare le variabili di ambiente seguenti su true nel contenitore edgeHub per abilitare il broker MQTT:

   | Nome | Valore |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Client Mosquitto** installati nel IoT Edge dispositivo. Questo articolo usa i client Mosquitto [più diffusi MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) e [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). È invece possibile usare altri client MQTT. Per installare i client Mosquitto in un dispositivo Ubuntu, eseguire il comando seguente:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Non installare il server Mosquitto perché potrebbe causare il blocco delle porte MQTT (8883 e 1883) e il conflitto con l'hub IoT Edge rete.

## <a name="connect-to-iot-edge-hub"></a>Connettersi all IoT Edge hub

La connessione all'hub IoT Edge segue la stessa procedura descritta nell'articolo Connessione all'hub IoT con un [client MQTT](../iot-hub/iot-hub-mqtt-support.md) generico o nella descrizione concettuale dell'articolo [IoT Edge hub](iot-edge-runtime.md). Questi passaggi sono:

1. Facoltativamente, il client MQTT stabilisce *una* connessione sicura con l'hub IoT Edge usando Transport Layer Security (TLS)
2. Il client MQTT *esegue l'autenticazione* a IoT Edge hub
3. L IoT Edge hub *autorizza il* client MQTT in base ai criteri di autorizzazione

### <a name="secure-connection-tls"></a>Connessione sicura (TLS)

Transport Layer Security (TLS) viene usato per stabilire una comunicazione crittografata tra il client e l IoT Edge hub.

Per disabilitare TLS, usare la porta 1883(MQTT) e associare il contenitore edgeHub alla porta 1883.

Per abilitare TLS, se un client si connette alla porta 8883 (MQTTS) al broker MQTT, verrà avviato un canale TLS. Il broker invia la catena di certificati che il client deve convalidare. Per convalidare la catena di certificati, il certificato radice del broker MQTT deve essere installato come certificato attendibile nel client. Se il certificato radice non è attendibile, la libreria client verrà rifiutata dal broker MQTT con un errore di verifica del certificato. I passaggi da seguire per installare questo certificato radice del broker nel client sono gli stessi del [caso del gateway](how-to-create-transparent-gateway.md) trasparente e sono descritti nella documentazione relativa alla preparazione di un dispositivo [downstream.](how-to-connect-downstream-device.md#prepare-a-downstream-device)

### <a name="authentication"></a>Autenticazione

Per l'autenticazione, un client MQTT deve prima inviare un pacchetto CONNECT al broker MQTT per avviare una connessione nel nome. Questo pacchetto fornisce tre informazioni di autenticazione: `client identifier` , e `username` `password` :

- Il `client identifier` campo è il nome del dispositivo o del modulo nell'hub IoT. Viene usata la sintassi seguente:

  - Per un dispositivo: `<device_name>`

  - Per un modulo: `<device_name>/<module_name>`

   Per connettersi al broker MQTT, è necessario registrare un dispositivo o un modulo nell'hub IoT.

   Il broker non consentirà le connessioni da più client usando le stesse credenziali. Il broker disconnetterà il client già connesso se un secondo client si connette usando le stesse credenziali.

- Il campo deriva dal nome del dispositivo o del modulo e il nome IoTHub a cui appartiene il dispositivo `username` usando la sintassi seguente:

  - Per un dispositivo: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Per un modulo: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- Il `password` campo del pacchetto CONNECT dipende dalla modalità di autenticazione:

  - Quando si usa [l'autenticazione con chiavi simmetriche,](how-to-authenticate-downstream-device.md#symmetric-key-authentication) `password` il campo è un token di firma di accesso condiviso.
  - Quando si [usa l'autenticazione autofirmata X.509,](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) `password` il campo non è presente. In questa modalità di autenticazione è necessario un canale TLS. Il client deve connettersi alla porta 8883 per stabilire una connessione TLS. Durante l'handshake TLS, il broker MQTT richiede un certificato client. Questo certificato viene usato per verificare l'identità del client e quindi il campo non è necessario in un secondo momento `password` quando viene inviato il pacchetto CONNECT. L'invio di un certificato client e del campo della password restituirà un errore e la connessione verrà chiusa. Le librerie MQTT e le librerie client TLS hanno in genere un modo per inviare un certificato client quando si avvia una connessione. È possibile visualizzare un esempio passo per passo nella sezione [Uso del certificato X509 per l'autenticazione client.](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)

I moduli distribuiti da [](how-to-authenticate-downstream-device.md#symmetric-key-authentication) IoT Edge usano l'autenticazione con chiavi simmetriche e possono chiamare l'API del carico di lavoro IoT Edge locale per ottenere un token di firma di accesso condiviso [a](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) livello di codice anche quando è offline.

### <a name="authorization"></a>Autorizzazione

Dopo che un client MQTT è stato autenticato IoT Edge hub, deve essere autorizzato a connettersi. Una volta connesso, deve essere autorizzato a pubblicare o sottoscrivere argomenti specifici. Queste autorizzazioni vengono concesse dall'hub IoT Edge in base ai relativi criteri di autorizzazione. I criteri di autorizzazione sono un set di istruzioni espresse come struttura JSON che viene inviata all'hub IoT Edge tramite il relativo gemello. Modificare un hub IoT Edge gemello per configurarne i criteri di autorizzazione.

> [!NOTE]
> Per l'anteprima pubblica, solo l'interfaccia della riga di comando di Azure supporta le distribuzioni contenenti criteri di autorizzazione broker MQTT. La portale di Azure attualmente non supporta la modifica del dispositivo IoT Edge hub gemello e dei relativi criteri di autorizzazione.

Ogni istruzione dei criteri di autorizzazione è costituita dalla combinazione di `identities` effetti , o , e `allow` `deny` `operations` `resources` :

- `identities` descrivere l'oggetto dei criteri. Deve eseguire il mapping `client identifier` all'oggetto inviato dai client nel pacchetto CONNECT.
- `allow` Gli `deny` effetti o definiscono se consentire o negare le operazioni.
- `operations` definire le azioni da autorizzare. `mqtt:connect`e `mqtt:publish` `mqtt:subscribe` sono le tre azioni attualmente supportate.
- `resources` definire l'oggetto dei criteri. Può trattarsi di un argomento o di un modello di argomento definito con [caratteri jolly MQTT.](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)

Di seguito è riportato un esempio di criteri di autorizzazione che non consentono in modo esplicito la connessione del client "rogue_client", consentono a qualsiasi client Azure IoT di connettersi e consente a "sensor_1" di pubblicare nell'argomento `events/alerts` .

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Quando si scrivono i criteri di autorizzazione, tenere presenti alcuni aspetti:

- Richiede lo `$edgeHub` schema gemello versione 1.2
- Per impostazione predefinita, tutte le operazioni vengono negate.
- Le istruzioni di autorizzazione vengono valutate nell'ordine in cui vengono visualizzate nella definizione JSON. Si inizia esaminando e quindi si selezionano le prime istruzioni allow o `identities` deny che corrispondono alla richiesta. In caso di conflitti tra le istruzioni allow e deny, l'istruzione deny ha la sua prima.
- Nei criteri di autorizzazione è possibile usare diverse variabili, ad esempio sostituzioni:

  - `{{iot:identity}}` rappresenta l'identità del client attualmente connesso. Ad esempio, un'identità del dispositivo come `myDevice` o un'identità del modulo come `myEdgeDevice/SampleModule` .
  - `{{iot:device_id}}` rappresenta l'identità del dispositivo attualmente connesso. Ad esempio, un'identità del dispositivo `myDevice` come o l'identità del dispositivo in cui un modulo è in esecuzione come `myEdgeDevice` .
  - `{{iot:module_id}}` rappresenta l'identità del modulo attualmente connesso. Questa variabile è vuota per i dispositivi connessi o un'identità del modulo come `SampleModule` .
  - `{{iot:this_device_id}}` rappresenta l'identità del dispositivo IoT Edge che esegue i criteri di autorizzazione. Ad esempio: `myIoTEdgeDevice`.

Le autorizzazioni per gli argomenti dell'hub IoT vengono gestite in modo leggermente diverso rispetto agli argomenti definiti dall'utente. Ecco i punti chiave da ricordare:

- Azure IoT dispositivi o moduli necessitano di una regola di autorizzazione esplicita per connettersi IoT Edge broker MQTT dell'hub. Di seguito sono indicati i criteri di autorizzazione di connessione predefiniti.
- Azure IoT dispositivi o moduli possono accedere ai propri argomenti dell'hub IoT per impostazione predefinita senza alcuna regola di autorizzazione esplicita. In questo caso, tuttavia, le autorizzazioni derivano da relazioni padre/figlio e queste relazioni devono essere impostate. IoT Edge i moduli vengono impostati automaticamente come elementi figlio del dispositivo IoT Edge, ma i dispositivi devono essere impostati in modo esplicito come elementi figlio del gateway IoT Edge.

Ecco un criterio di autorizzazione predefinito che può essere usato per consentire a tutti Azure IoT dispositivi o moduli di **connettersi** al broker:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Ora che si è compreso come connettersi al broker MQTT di IoT Edge, è possibile vedere come può essere usato per pubblicare e sottoscrivere i messaggi prima negli argomenti definiti dall'utente, quindi negli argomenti dell'hub IoT e infine in un altro broker MQTT.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Pubblicare e sottoscrivere argomenti definiti dall'utente

In questo articolo si userà un client denominato **sub_client** che sottoscrive un argomento e un altro client denominato **pub_client** pubblica in un argomento. Si userà [](how-to-authenticate-downstream-device.md#symmetric-key-authentication) l'autenticazione con chiave simmetrica, ma lo stesso può essere fatto con l'autenticazione autofirmata [X.509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) o l'autenticazione con firma [CA X.509.](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)

### <a name="create-publisher-and-subscriber-clients"></a>Creare i client del server di pubblicazione e del sottoscrittore

Creare due dispositivi IoT nell'hub IoT e ottenere le relative password. Uso dell'interfaccia della riga di comando di Azure dal terminale per:

1. Creare due dispositivi IoT nell'hub IoT, padre del dispositivo IoT Edge dispositivo:

   ```azurecli-interactive
   az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   ```

2. Ottenere le password generando un token di firma di accesso condiviso:

   - Per un dispositivo:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
     ```

     dove 3600 è la durata del token di firma di accesso condiviso in secondi(ad esempio, 3600 = 1 ora).

   - Per un modulo:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
     ```

     dove 3600 è la durata del token di firma di accesso condiviso in secondi(ad esempio, 3600 = 1 ora).

3. Copiare il token di firma di accesso condiviso, ovvero il valore corrispondente alla chiave "sas" dall'output. Di seguito è riportato un output di esempio del comando dell'interfaccia della riga di comando di Azure precedente:

   ```output
   {
      "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
   }
   ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Autorizzare i client del server di pubblicazione e del sottoscrittore

Per autorizzare il server di pubblicazione e il sottoscrittore, modificare il IoT Edge hub gemello in una distribuzione IoT Edge che include i criteri di autorizzazione seguenti.

>[!NOTE]
>Attualmente, le distribuzioni che contengono le proprietà di autorizzazione MQTT possono essere applicate solo ai dispositivi IoT Edge usando l'interfaccia della riga di comando di Azure.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Autenticazione con chiavi simmetriche senza TLS

#### <a name="subscribe"></a>Sottoscrivi

Connettere il **sub_client** client MQTT al broker MQTT e sottoscrivere eseguendo il comando seguente `test_topic` nel IoT Edge dispositivo:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

dove `<edge_device_address>`  =  `localhost` in questo esempio poiché il client è in esecuzione nello stesso dispositivo IoT Edge.

Si noti che in questo primo esempio viene usata la porta 1883 (MQTT), senza TLS. Un altro esempio con la porta 8883 (MQTTS), con TLS abilitato, è illustrato nella sezione successiva.

Il **sub_client** client MQTT è ora avviato e è in attesa di messaggi in ingresso in `test_topic` .

#### <a name="publish"></a>Pubblica

Connettere  il client MQTT di pub_client al broker MQTT e pubblicare un messaggio nello stesso modo indicato in precedenza eseguendo il comando seguente nel dispositivo IoT Edge da un altro `test_topic` terminale:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

dove `<edge_device_address>`  =  `localhost` in questo esempio poiché il client è in esecuzione nello stesso dispositivo IoT Edge.

Eseguendo il comando, il **sub_client** client MQTT riceve il messaggio "hello".

### <a name="symmetric-keys-authentication-with-tls"></a>Autenticazione con chiavi simmetriche con TLS

Per abilitare TLS, la porta deve essere modificata da 1883 (MQTT) a 8883 (MQTTS) e i client devono avere il certificato radice del broker MQTT per poter convalidare la catena di certificati inviata dal broker MQTT. Questa operazione può essere eseguita seguendo i passaggi descritti nella sezione [Connessione sicura (TLS).](#secure-connection-tls)

Poiché i client sono in esecuzione nello stesso dispositivo del broker MQTT nell'esempio precedente, gli stessi passaggi si applicano per abilitare TLS semplicemente modificando il numero di porta da 1883 (MQTT) a 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Pubblicare e sottoscrivere argomenti sull'hub IoT

Gli [AZURE IOT per](https://github.com/Azure/azure-iot-sdks) dispositivi consentono già ai client di eseguire operazioni dell'hub IoT, ma non consentono la pubblicazione/sottoscrizione di argomenti definiti dall'utente. Le operazioni dell'hub IoT possono essere eseguite usando qualsiasi client MQTT che usa la semantica di pubblicazione e sottoscrizione, purché i protocolli primitivi dell'hub IoT siano rispettati. Verranno ora trattate le specifiche per comprendere il funzionamento di questi protocolli.

### <a name="send-telemetry-data-to-iot-hub"></a>Inviare dati di telemetria all'hub IoT

L'invio di dati di telemetria all'hub IoT è simile alla pubblicazione in un argomento definito dall'utente, ma con un argomento specifico dell'hub IoT:

- Per un dispositivo, i dati di telemetria vengono inviati nell'argomento: `devices/<device_name>/messages/events/`
- Per un modulo, i dati di telemetria vengono inviati nell'argomento: `devices/<device_name>/<module_name>/messages/events/`

Creare anche una route, ad esempio per inviare dati di `FROM /messages/* INTO $upstream` telemetria dal broker MQTT IoT Edge all'hub IoT. Per altre informazioni sul routing, vedere [Dichiarare le route.](module-composition.md#declare-routes)

### <a name="get-twin"></a>Ottenere il dispositivo gemello

Il recupero del dispositivo/modulo gemello non è un modello MQTT tipico. Il client deve mettere una richiesta per il gemello che verrà servito dall'hub IoT.

Per ricevere i dispositivi gemelli, il client deve sottoscrivere un argomento specifico dell'hub `$iothub/twin/res/#` IoT. Il nome di questo argomento viene ereditato dall'hub IoT e tutti i client devono sottoscrivere lo stesso argomento. Ciò non significa che i dispositivi o i moduli ricevano il gemello l'uno dell'altro. L'hub IoT IoT Edge hub sa quale gemello deve essere recapitato dove, anche se tutti i dispositivi sono in ascolto dello stesso nome di argomento.

Dopo aver creato la sottoscrizione, il client deve richiedere il gemello pubblicando un messaggio in un argomento specifico dell'hub IoT, dove è `$iothub/twin/GET/?rid=<request_id>/#`  `<request_id>` un identificatore arbitrario. L'hub IoT invierà quindi la risposta con i dati richiesti sull'argomento `$iothub/twin/res/200/?rid=<request_id>` , sottoscritti dal client. Questo è il modo in cui un client può associare le richieste alle risposte.

### <a name="receive-twin-patches"></a>Ricevere le patch dei dispositivi gemelli

Per ricevere le patch dei dispositivi gemelli, un client deve sottoscrivere uno speciale argomento di `$iothub/twin/PATCH/properties/desired/#` IoTHub. Dopo aver effettuato la sottoscrizione, il client riceve le patch dei dispositivi gemelli inviate dall'hub IoT in questo argomento.

### <a name="receive-direct-methods"></a>Ricevere metodi diretti

La ricezione di un metodo diretto è simile alla ricezione di gemelli completi, con l'aggiunta che il client deve confermare di aver ricevuto la chiamata. Prima di tutto il client sottoscrive l'argomento speciale dell'hub `$iothub/methods/POST/#` IoT. Dopo aver ricevuto un metodo diretto in questo argomento, il client deve estrarre l'identificatore della richiesta dall'argomento secondario in cui viene ricevuto il metodo diretto e pubblicare infine un messaggio di conferma nell'argomento speciale `rid` dell'hub `$iothub/methods/res/200/<request_id>` IoT.

### <a name="send-direct-methods"></a>Inviare metodi diretti

L'invio di un metodo diretto è una chiamata HTTP e pertanto non passa attraverso il broker MQTT. Per inviare un metodo diretto all'hub IoT, vedere [Comprendere e richiamare metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md). Per inviare un metodo diretto in locale a un altro modulo, vedere questo Azure IoT di chiamata diretta al metodo di [C# SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Pubblicare e sottoscrivere tra broker MQTT

Per connettere due broker MQTT, l'hub IoT Edge include un bridge MQTT. Un bridge MQTT viene comunemente usato per connettere un broker MQTT in esecuzione a un altro broker MQTT. Solo un subset del traffico locale viene in genere inserito in un altro broker.

> [!NOTE]
> Il bridge IoT Edge hub può attualmente essere usato solo tra dispositivi IoT Edge annidati. Non può essere usato per inviare dati all'hub IoT perché l'hub IoT non è un broker MQTT completo. Per altre informazioni sul supporto delle funzionalità del broker MQTT dell'hub IoT, vedere Comunicare con [l'hub IoT usando il protocollo MQTT.](../iot-hub/iot-hub-mqtt-support.md) Per altre informazioni sull'annidamento IoT Edge dispositivi, vedere Connettere un dispositivo [downstream IoT Edge a un gateway Azure IoT Edge.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

In una configurazione annidata, il bridge MQTT dell'hub di IoT Edge funge da client del broker MQTT padre, pertanto le regole di autorizzazione devono essere impostate nell'hub Edge padre per consentire all'hub Edge figlio di pubblicare e sottoscrivere argomenti specifici definiti dall'utente per i quali è configurato il bridge.

Il IoT Edge MQTT viene configurato tramite una struttura JSON che viene inviata all'hub IoT Edge tramite il relativo gemello. Modificare un IoT Edge hub gemello per configurare il relativo bridge MQTT.

> [!NOTE]
> Per l'anteprima pubblica, solo l'interfaccia della riga di comando di Azure supporta le distribuzioni contenenti configurazioni bridge MQTT. La portale di Azure attualmente non supporta la modifica del dispositivo IoT Edge hub gemello e della relativa configurazione del bridge MQTT.

Il bridge MQTT può essere configurato per connettere un broker MQTT IoT Edge hub a più broker esterni. Per ogni broker esterno sono necessarie le impostazioni seguenti:

- `endpoint` è l'indirizzo del broker MQTT remoto a cui connettersi. Solo i IoT Edge padre sono attualmente supportati e sono definiti dalla variabile `$upstream` .
- `settings` definisce gli argomenti da creare un bridge per un endpoint. Possono essere presenti più impostazioni per ogni endpoint e per configurarlo vengono usati i valori seguenti:
  - `direction`: `in` per sottoscrivere gli argomenti del broker remoto o per pubblicare negli argomenti `out` del broker remoto
  - `topic`: modello di argomento principale di cui trovare una corrispondenza. Per definire questo modello è possibile usare caratteri jolly [MQTT.](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) È possibile applicare prefissi diversi a questo modello di argomento nel broker locale e nel broker remoto.
  - `outPrefix`: prefisso applicato al `topic` modello nel broker remoto.
  - `inPrefix`: prefisso applicato al modello `topic` nel broker locale.

Di seguito è riportato un esempio di configurazione del bridge MQTT IoT Edge che ripubblica tutti i messaggi ricevuti negli argomenti di un dispositivo IoT Edge padre in un dispositivo IoT Edge figlio negli stessi argomenti e ripubblica tutti i messaggi inviati negli argomenti di un dispositivo IoT Edge figlio a un dispositivo IoT Edge padre negli `alerts/#` `/local/telemetry/#` argomenti `/remote/messages/#` .

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "#",
                    "inPrefix": "/local/telemetry/",
                    "outPrefix": "/remote/messages/"
                }
            ]
        }]
    }
}
```
Altre note sul bridge MQTT IoT Edge hub:
- Il protocollo MQTT verrà usato automaticamente come protocollo upstream quando viene usato il broker MQTT e tale IoT Edge viene usato in una configurazione annidata, ad esempio con un `parent_hostname` oggetto specificato. Per altre informazioni sui protocolli upstream, vedere [Comunicazione cloud.](iot-edge-runtime.md#cloud-communication) Per altre informazioni sulle configurazioni annidate, vedere Connettere un dispositivo [IoT Edge downstream a un gateway Azure IoT Edge.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sull'hub IoT Edge dati](iot-edge-runtime.md#iot-edge-hub)
