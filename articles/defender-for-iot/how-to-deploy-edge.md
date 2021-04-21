---
title: Distribuire IoT Edge di sicurezza
description: Informazioni su come distribuire un agente di sicurezza defender per IoT in IoT Edge.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 71efb0bb12d1e20f918481a086fd411d3a237e33
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813597"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Distribuire un modulo di sicurezza nel IoT Edge dispositivo

**Il modulo Defender per IoT** offre una soluzione di sicurezza completa per IoT Edge dispositivi.
Il modulo di sicurezza raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema operativo e dal sistema contenitore in raccomandazioni e avvisi di sicurezza di utilità operativa.
Per altre informazioni, vedere [Modulo di sicurezza per IoT Edge](security-edge-architecture.md).

In questo articolo si apprenderà come distribuire un modulo di sicurezza nel IoT Edge dispositivo.

## <a name="deploy-security-module"></a>Distribuire il modulo di sicurezza

Usare la procedura seguente per distribuire un modulo di sicurezza di Defender per IoT per IoT Edge.

### <a name="prerequisites"></a>Prerequisiti

1. Nell'hub IoT verificare che il dispositivo sia registrato come [IoT Edge dispositivo](../iot-edge/how-to-register-device.md#register-a-new-device).

1. Defender per IoT Edge modulo richiede che [il framework AuditD](https://linux.die.net/man/8/auditd) sia installato nel IoT Edge dispositivo.

    - Installare il framework eseguendo il comando seguente nel IoT Edge dispositivo:

    `sudo apt-get install auditd audispd-plugins`

    - Verificare che AuditD sia attivo eseguendo il comando seguente:

    `sudo systemctl status auditd`<br>
    - La risposta prevista è: `active (running)`

### <a name="deployment-using-azure-portal"></a>Distribuzione tramite portale di Azure

1. Dal portale di Azure aprire **Marketplace.**

1. Selezionare **Internet delle cose**, quindi cercare Centro sicurezza di Azure **IoT** e selezionarlo.

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="Selezionare Defender per IoT":::

1. Selezionare **Crea** per configurare la distribuzione.

1. Scegliere la sottoscrizione **di** Azure dell'hub IoT e quindi selezionare **l'hub IoT.**<br>Selezionare **Deploy to a device to** target a single device (Distribuisci in più dispositivi) o Deploy at Scale to **target** multiple devices (Distribuisci in più dispositivi) e quindi selezionare **Create (Crea).** Per altre informazioni sulla distribuzione su larga scala, vedere [Come distribuire](../iot-edge/how-to-deploy-at-scale.md).

    >[!Note]
    >Se è stata **selezionata l'opzione Distribuisci** su scala , aggiungere il nome e i dettagli del dispositivo prima di continuare con la scheda **Aggiungi** moduli nelle istruzioni seguenti.

Completare ogni passaggio per completare la distribuzione IoT Edge per Defender per IoT.

#### <a name="step-1-modules"></a>Passaggio 1: Moduli

1. Selezionare il **modulo AzureSecurityCenterforIoT.**
1. Nella scheda **Impostazioni modulo** modificare il nome **in** **azureiotsecurity**.
1. Nella scheda **Variabili di** ambiente aggiungere una variabile, se necessario( ad esempio, è possibile aggiungere il livello di *debug* e impostarla su uno dei valori seguenti: "Fatal", "Error", "Warning" o "Information").
1. Nella scheda **Opzioni di creazione contenitore** aggiungere la configurazione seguente:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. Nella scheda **Impostazioni modulo gemello** aggiungere la configurazione seguente:

   Proprietà del modulo gemello:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Contenuto delle proprietà del modulo gemello: 

   ```json
     {

     }
   ```
    
   Per altre informazioni sulla configurazione dell'agente, vedere [Configurare gli agenti di sicurezza.](./how-to-agent-configuration.md)

1. Selezionare **Aggiorna**.

#### <a name="step-2-runtime-settings"></a>Passaggio 2: Impostazioni di runtime

1. Selezionare **Impostazioni di runtime**.
2. In **Hub edge** modificare l'immagine in **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**. 

    >[!Note]
    > Attualmente è supportata la versione 1.0.8.3 o precedente.

3. Verificare **che l'opzione** Crea opzioni sia impostata sulla configurazione seguente:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

4. Selezionare **Salva**.

5. Selezionare **Avanti**.

#### <a name="step-3-specify-routes"></a>Passaggio 3: Specificare le route

1. Nella scheda **Specifica** route assicurarsi di avere una route (esplicita o implicita) che  inoltra i messaggi dal modulo **azureiotsecurity** a $upstream in base agli esempi seguenti. Solo quando la route è disponibile, selezionare **Avanti.**

   Route di esempio:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Selezionare **Avanti**.

#### <a name="step-4-review-deployment"></a>Passaggio 4: Esaminare la distribuzione

- Nella scheda **Rivedi distribuzione** esaminare le informazioni sulla distribuzione e quindi selezionare **Crea** per completare la distribuzione.

## <a name="diagnostic-steps"></a>Passaggi di diagnostica

Se si verifica un problema, i log dei contenitori sono il modo migliore per ottenere informazioni sullo stato di un dispositivo IoT Edge modulo di sicurezza. Usare i comandi e gli strumenti illustrati in questa sezione per raccogliere informazioni.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verificare che i contenitori necessari siano installati e funzionino come previsto

1. Eseguire il comando seguente nel dispositivo IoT Edge:

    `sudo docker ps`

1. Verificare che i contenitori seguenti siano in esecuzione:

   | Nome | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Se non sono presenti i contenitori minimi necessari, verificare se il IoT Edge di distribuzione è allineato alle impostazioni consigliate. Per altre informazioni, vedere [Distribuire IoT Edge modulo](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Esaminare i log del modulo per verificare la presenza di errori

1. Eseguire il comando seguente nel dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`

1. Per log più dettagliati, aggiungere la variabile di ambiente seguente alla distribuzione del modulo **azureiotsecurity:** `logLevel=Debug` .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle opzioni di configurazione, continuare con la guida alle procedura per la configurazione del modulo.
> [!div class="nextstepaction"]
> [Guida alle procedura di configurazione dei moduli](./how-to-agent-configuration.md)