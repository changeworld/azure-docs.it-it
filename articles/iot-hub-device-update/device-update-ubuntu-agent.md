---
title: Esercitazione sull'aggiornamento 18,04 del dispositivo per l'hub di Azure Microsoft Docs
description: Introduzione all'aggiornamento del dispositivo per l'hub di Azure Internet con l'agente di pacchetti di Ubuntu server 18,04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664896"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Esercitazione sull'aggiornamento del dispositivo per 18,04 l'hub di Azure

L'aggiornamento del dispositivo per l'hub Internet supporta due forme di aggiornamenti, basati su immagini e basati su pacchetti.

Gli aggiornamenti basati su pacchetti sono aggiornamenti mirati che modificano solo un componente o un'applicazione specifica nel dispositivo. In questo modo si riduce il consumo di larghezza di banda e si riduce il tempo necessario per scaricare e installare l'aggiornamento. Gli aggiornamenti dei pacchetti consentono in genere un minor tempo di inattività per i dispositivi quando si applica un aggiornamento ed evitano il sovraccarico della creazione di immagini.

Questa esercitazione illustra i passaggi per completare un aggiornamento basato su pacchetti end-to-end tramite l'aggiornamento del dispositivo per l'hub Internet. Per questa esercitazione verrà usato un agente pacchetto di esempio per Ubuntu server 18,04 x64. Anche se si prevede di usare una configurazione della piattaforma del sistema operativo diversa, questa esercitazione è ancora utile per conoscere gli strumenti e i concetti relativi all'aggiornamento dei dispositivi per l'hub Internet. Completare questa introduzione a un processo di aggiornamento end-to-end, quindi scegliere la forma preferita di aggiornamento e piattaforma del sistema operativo per approfondire i dettagli. È possibile usare l'aggiornamento del dispositivo per l'hub di tutto per aggiornare un dispositivo Azure o Azure IoT Edge usando questa esercitazione. 

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Configurare il repository del pacchetto di aggiornamento del dispositivo
> * Scaricare e installare l'agente di aggiornamento del dispositivo e le relative dipendenze
> * Aggiungere un tag al dispositivo Internet delle cose
> * Importazione di un aggiornamento
> * Creare un gruppo di dispositivi
> * Distribuire un aggiornamento del pacchetto
> * Monitorare la distribuzione degli aggiornamenti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
* Accesso a un hub. Si consiglia di usare un livello S1 (standard) o superiore.
* Un dispositivo Azure o Azure IoT Edge su cui è in esecuzione Ubuntu server 18,04 x64, connesso all'hub Internet.
   * Se si usa un dispositivo di Azure IoT Edge, assicurarsi che sia in v 1.2.0 del runtime di Edge o versione successiva 
* Se non si usa un dispositivo di Azure IoT Edge, [installare il pacchetto più recente `aziot-identity-service` (anteprima) nel dispositivo Internet delle](https://github.com/Azure/iot-identity-service/actions/runs/575919358) cose 
* [L'account e l'istanza di aggiornamento del dispositivo sono collegati allo stesso hub degli stessi.](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>Configurare il repository del pacchetto di aggiornamento del dispositivo

1. Installare la configurazione del repository che corrisponde al sistema operativo del dispositivo. Per questa esercitazione, si tratta del server Ubuntu 18,04. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. Copiare l'elenco generato nella directory sources.list.d.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Installare la chiave pubblica GPG Microsoft.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Installare i pacchetti dell'agente Update. deb del dispositivo

1. Aggiornare gli elenchi di pacchetti nel dispositivo

   ```shell
      sudo apt-get update
   ```

2. Installare il pacchetto DeviceUpdate-Agent e le relative dipendenze

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

L'aggiornamento del dispositivo per i pacchetti software dell'hub Azure è soggetto alle condizioni di licenza seguenti:
   * [Aggiornamento del dispositivo per la licenza dell'hub Internet](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licenza client di ottimizzazione recapito](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Leggere le condizioni di licenza prima di utilizzare un pacchetto. L'installazione e l'utilizzo di un pacchetto costituiscono l'accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non utilizzare il pacchetto.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Configurare l'agente di aggiornamento del dispositivo con il servizio di identità Azure Internet (anteprima)

Una volta installati i pacchetti necessari, è necessario eseguire il provisioning del dispositivo con le informazioni di autenticazione e identità cloud.

1. Aprire il file di configurazione

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. Trovare la sezione di configurazione del provisioning del file. Rimuovere il commento dalla sezione "provisioning manuale con stringa di connessione". Aggiornare il valore della connection_string con la stringa di connessione per il dispositivo Internet delle cose (Edge). Verificare che tutte le altre sezioni di provisioning siano impostate come commento.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Salvare e chiudere il file con CTRL + X, Y

4. Applicare la configurazione. 

   Se si utilizza un dispositivo IoT Edge, utilizzare il comando seguente. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   Se si usa un dispositivo Internet delle cose, con il `aziot-identity-service` pacchetto installato, usare il comando seguente. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. Facoltativamente, è possibile verificare che i servizi siano in esecuzione da

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    L'output deve essere letto:

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

## <a name="add-a-tag-to-your-device"></a>Aggiungere un tag al dispositivo

1. Accedere a [portale di Azure](https://portal.azure.com) e passare all'hub Internet delle cose.

2. Da "dispositivi Internet o" IoT Edge "nel riquadro di spostamento a sinistra trova il tuo dispositivo e passa al dispositivo gemello.

3. Nel dispositivo gemello eliminare qualsiasi valore del tag di aggiornamento del dispositivo esistente impostando il valore su null.

4. Aggiungere un nuovo valore del tag di aggiornamento del dispositivo come illustrato di seguito.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importa aggiornamento

1. Scaricare il [file manifesto apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) seguente e [importare il file manifesto](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json). Questo manifesto apt installerà la versione disponibile più recente di nel dispositivo Internet delle cose `libcurl4-doc package` . 

   In alternativa, è possibile scaricare questo [file manifesto apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) e [importare il file manifesto](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). Verrà installata una versione specifica v 7.58.0 del nel `libcurl4-doc package` dispositivo Internet. 

2. In portale di Azure selezionare l'opzione aggiornamenti del dispositivo in gestione automatica dispositivi dalla barra di spostamento a sinistra nell'hub.

3. Selezionare la scheda aggiornamenti.

4. Selezionare "+ Importa nuovo aggiornamento".

5. Selezionare l'icona della cartella o la casella di testo in "selezionare un file manifesto di importazione". Viene visualizzata una finestra di dialogo di selezione file. Selezionare il manifesto di importazione scaricato in precedenza. Selezionare quindi l'icona della cartella o la casella di testo in "selezionare uno o più file di aggiornamento". Viene visualizzata una finestra di dialogo di selezione file. Selezionare il file di aggiornamento del manifesto apt scaricato in precedenza.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Screenshot che mostra la selezione del file di aggiornamento." lightbox="media/import-update/select-update-files.png":::

6. Selezionare l'icona della cartella o la casella di testo in "selezionare un contenitore di archiviazione". Selezionare quindi l'account di archiviazione appropriato.

7. Se è già stato creato un contenitore, è possibile riusarlo. In caso contrario, selezionare "+ contenitore" per creare un nuovo contenitore di archiviazione per gli aggiornamenti.  Selezionare il contenitore che si vuole usare e fare clic su "Seleziona".

   :::image type="content" source="media/import-update/container.png" alt-text="Screenshot che mostra la selezione del contenitore." lightbox="media/import-update/container.png":::

8. Selezionare "Invia" per avviare il processo di importazione.

9. Viene avviato il processo di importazione e la schermata passa alla sezione "cronologia di importazione". Selezionare "Aggiorna" per visualizzare lo stato di avanzamento fino al completamento del processo di importazione. A seconda delle dimensioni dell'aggiornamento, l'operazione può essere completata in pochi minuti, ma potrebbe richiedere più tempo.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Screenshot che mostra la sequenza di importazione degli aggiornamenti." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando la colonna stato indica che l'importazione è riuscita, selezionare l'intestazione "pronto per la distribuzione". Nell'elenco verrà visualizzato l'aggiornamento importato.

[Altre](import-update.md) informazioni sull'importazione di aggiornamenti.

## <a name="create-update-group"></a>Crea gruppo di aggiornamento

1. Passare all'hub Internet delle cose precedentemente connesso all'istanza di aggiornamento del dispositivo.

2. Selezionare l'opzione aggiornamenti del dispositivo in gestione automatica dispositivi dalla barra di spostamento a sinistra.

3. Selezionare la scheda gruppi nella parte superiore della pagina. 

4. Selezionare il pulsante Aggiungi per creare un nuovo gruppo.

5. Selezionare il tag dell'hub Internet delle cose creato nel passaggio precedente dall'elenco. Selezionare Crea gruppo di aggiornamento.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Screenshot che mostra la selezione di tag." lightbox="media/create-update-group/select-tag.PNG":::

[Altre](create-update-group.md) informazioni sull'aggiunta di tag e la creazione di gruppi di aggiornamento


## <a name="deploy-update"></a>Distribuisci aggiornamento

1. Una volta creato il gruppo, verrà visualizzato un nuovo aggiornamento per il gruppo di dispositivi, con un collegamento all'aggiornamento in aggiornamenti in sospeso. Potrebbe essere necessario eseguire l'aggiornamento una sola volta. 

2. Fare clic sull'aggiornamento disponibile.

3. Verificare che sia selezionato il gruppo corretto come gruppo di destinazione. Pianificare la distribuzione, quindi selezionare Distribuisci aggiornamento.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Seleziona aggiornamento" lightbox="media/deploy-update/select-update.png":::

4. Visualizzare il grafico di conformità. Si noterà che l'aggiornamento è in corso. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Aggiornamento in corso" lightbox="media/deploy-update/update-in-progress.png":::

5. Al termine dell'aggiornamento del dispositivo, si noterà che l'aggiornamento del grafico di conformità e dei dettagli della distribuzione è identico. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Aggiornamento completato" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorare una distribuzione degli aggiornamenti

1. Selezionare la scheda distribuzioni nella parte superiore della pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Scheda distribuzioni" lightbox="media/deploy-update/deployments-tab.png":::

2. Selezionare la distribuzione creata per visualizzare i dettagli della distribuzione.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dettagli di distribuzione" lightbox="media/deploy-update/deployment-details.png":::

3. Selezionare Aggiorna per visualizzare i dettagli più recenti sullo stato. Continuare questo processo fino a quando lo stato diventa succeeded.

A questo punto è stato completato un aggiornamento end-to-end corretto con l'aggiornamento del dispositivo per l'hub Internet in un dispositivo Ubuntu server 18,04 x64. 

## <a name="bonus-steps"></a>Passaggi bonus

1. Scaricare il [file manifesto apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) seguente e [importare il file manifesto](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json). Questo manifesto apt rimuoverà il installato `libcurl4-doc package` dal dispositivo Internet delle cose. 

2. Ripetere le sezioni "Importa aggiornamento" e "Distribuisci aggiornamento"

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, pulire l'account di aggiornamento del dispositivo, l'istanza, l'hub e il dispositivo Internet delle cose. A tale scopo, passare a ogni singola risorsa e selezionare "Elimina". Si noti che è necessario pulire un'istanza di aggiornamento del dispositivo prima di pulire l'account di aggiornamento del dispositivo. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiornamento dell'immagine in Raspberry Pi 3 B + esercitazione](device-update-raspberry-pi.md)

