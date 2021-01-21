---
title: 'Guida introduttiva: creare un dispositivo Azure IoT Edge su Windows | Microsoft Docs'
description: Questa guida introduttiva descrive come creare un dispositivo IoT Edge e distribuire codice predefinito in modalità remota dal portale di Azure.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: f3af2b7839465f886d1edba01eb9988419761dac
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630980"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Guida introduttiva: distribuire il primo modulo di IoT Edge in un dispositivo Windows (anteprima)

Provare Azure IoT Edge in questa Guida introduttiva distribuendo codice in contenitori in un dispositivo Linux IoT Edge Windows. IoT Edge consente di gestire in remoto il codice nei dispositivi, in modo da poter inviare più carichi di lavoro ai dispositivi perimetrali. Per questa Guida introduttiva, si consiglia di usare il proprio dispositivo per vedere quanto è facile usare Azure IoT Edge per Linux in Windows.

In questa guida introduttiva si apprende come:

* Creare un hub IoT.
* Registrare un dispositivo IoT Edge nell'hub IoT.
* Installare e avviare la IoT Edge per Linux in Windows Runtime nel dispositivo.
* Distribuire in modalità remota un modulo a un dispositivo IoT Edge e inviare dati di telemetria.

![Diagramma - Guida introduttiva sull'architettura per dispositivo e cloud](./media/quickstart/install-edge-full.png)

Questa Guida introduttiva illustra come configurare la Azure IoT Edge per Linux nel dispositivo Windows. Verrà quindi distribuito un modulo dal portale di Azure nel dispositivo. Il modulo distribuito in questa guida di avvio rapido è un sensore simulato che genera dati di temperatura, umidità e pressione. Le altre esercitazioni su Azure IoT Edge si basano sulle procedure eseguite qui tramite la distribuzione di moduli aggiuntivi che analizzano dati simulati per ottenere informazioni aziendali dettagliate.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

>[!NOTE]
>IoT Edge per Linux in Windows è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Preparare l'ambiente per l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Risorse cloud:

* Un gruppo di risorse per la gestione di tutte le risorse usate in questa guida introduttiva.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Dispositivo IoT Edge:

* Il dispositivo deve essere un PC o un server Windows, versione 1809 o successiva
* Almeno 4 GB di memoria, consigliati 8 GB di memoria
* 10 GB di spazio libero su disco

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Per iniziare, creare un hub IoT con l'interfaccia della riga di comando di Azure.

![Diagramma - Creare un hub IoT nel cloud](./media/quickstart/create-iot-hub.png)

Per questa guida introduttiva è possibile usare il livello gratuito dell'hub IoT. Se l'hub IoT è già stato usato in passato ed è disponibile un hub, è possibile usarlo qui.

Il codice seguente crea un hub **F1** gratuito nel gruppo di risorse `IoTEdgeResources`. Sostituire `{hub_name}` con un nome univoco per l'hub IoT. La creazione di un hub IoT potrebbe richiedere alcuni minuti.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Se si verifica un errore perché è già presente un hub gratuito nella sottoscrizione, modificare lo SKU in **S1**. Se si verifica un errore che indica che il nome dell'hub IoT non è disponibile, significa che qualcuno ha già un hub con lo stesso nome. Provare con un nuovo nome.

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

Registrare un dispositivo IoT Edge con l'hub IoT appena creato.

![Diagramma - Registrare un dispositivo con un'identità dell'hub IoT](./media/quickstart/register-device.png)

Creare un'identità del dispositivo per il dispositivo simulato in modo che possa comunicare con l'hub IoT. L'identità del dispositivo si trova nel cloud e si usa una stringa di connessione del dispositivo univoca per associare un dispositivo fisico a un'identità del dispositivo.

Poiché i dispositivi IoT Edge si comportano e possono essere gestiti in modo diverso rispetto ai dispositivi IoT tipici, indicare con un flag `--edge-enabled` che l'identità si riferisce a un dispositivo IoT Edge.

1. In Azure Cloud Shell immettere il comando seguente per creare un dispositivo denominato **myEdgeDevice** nell'hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Se viene visualizzato un errore relativo alle chiavi dei criteri iothubowner, assicurarsi che Cloud Shell esegua la versione più recente dell'estensione azure-iot.

2. Visualizzare la stringa di connessione per il dispositivo, che collega il dispositivo fisico alla sua identità nell'hub IoT. Contiene il nome dell'hub IoT, il nome del dispositivo e quindi una chiave condivisa che autentica le connessioni tra i due.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copiare il valore della chiave `connectionString` dall'output JSON e salvarlo. Questo valore corrisponde alla stringa di connessione che verrà usata per configurare il runtime IoT Edge nella sezione successiva.

   ![Recuperare la stringa di connessione dall'output dell'interfaccia della riga di comando](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installare e avviare il runtime di IoT Edge

Installare IoT Edge per Linux in Windows sul dispositivo e configurarlo con una stringa di connessione del dispositivo.

![Diagramma-avviare il runtime di IoT Edge nel dispositivo](./media/quickstart/start-runtime.png)

1. [Scaricare](https://aka.ms/WACDownloadEFLOW)l'interfaccia di amministrazione di Windows.
2. Seguire l'installazione guidata per configurare l'interfaccia di amministrazione di Windows sul dispositivo.
3. Quando ci si trova nell'interfaccia di amministrazione di Windows, nella parte superiore destra della schermata selezionare l'icona a forma di **ingranaggio impostazioni** .  
4. Dal menu impostazioni, in Gateway, selezionare **estensioni** .
5. Nell'elenco delle **estensioni disponibili** selezionare **Azure IOT Edge**
6. **Installare** l'estensione

7. Una volta installata l'estensione, passare alla pagina principale del dashboard selezionando l'interfaccia di **amministrazione di Windows** nell'angolo superiore sinistro della schermata.

8. Verrà visualizzata la connessione host locale che rappresenta il PC in cui è in esecuzione l'interfaccia di amministrazione di Windows.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Schermata-pagina iniziale dell'amministratore di Windows":::

9. Selezionare **Aggiungi**.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Screenshot-pulsante Aggiungi pagina iniziale amministratore di Windows":::

10. Individuare il riquadro Azure IoT Edge e selezionare **Crea nuovo**. Verrà avviata l'installazione guidata di.

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Schermata-riquadro Azure IoT Edge per Linux in Windows":::

11. Per accettare il contratto di licenza e scegliere **Avanti** , procedere con l'installazione guidata

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Schermata-pagina iniziale della procedura guidata":::

12. Scegliere i **dati di diagnostica facoltativi** per fornire dati diagnostici estesi che consentono a Microsoft di monitorare e gestire la qualità del servizio e fare clic su **Avanti: Distribuisci.**

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Screenshot-dati di diagnostica":::

13. Nella schermata **selezionare il dispositivo di destinazione** selezionare il dispositivo di destinazione desiderato per verificare che soddisfi i requisiti minimi. Per questa Guida introduttiva si sta installando IoT Edge sul dispositivo locale, quindi scegliere la connessione localhost. Una volta confermata, scegliere **Avanti** per continuare.

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Screenshot-selezionare il dispositivo di destinazione":::

14. Accettare le impostazioni predefinite scegliendo **Avanti**.

15. La schermata di distribuzione Mostra il processo di download del pacchetto, installazione del pacchetto, configurazione dell'host e configurazione finale della VM Linux.  Una distribuzione corretta avrà un aspetto simile al seguente:

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Screenshot: distribuzione guidata riuscita":::

16. Fare clic su **Avanti: Connetti** per continuare con il passaggio finale per eseguire il provisioning del dispositivo Azure IOT Edge con l'ID del dispositivo dall'istanza dell'hub Internet.

17. Copiare la stringa di connessione dal dispositivo nell'hub Azure e incollarla nel campo stringa di connessione del dispositivo. Scegliere quindi **provisioning con il metodo selezionato**.

    > [!NOTE]
    > Per recuperare la stringa di connessione, vedere il passaggio 3 della sezione precedente, [registrare un dispositivo IOT Edge](#register-an-iot-edge-device).

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Screenshot-Provisioning guidato":::

18. Al termine del provisioning, selezionare **fine** per completare e tornare alla schermata Start dell'interfaccia di amministrazione di Windows. A questo punto dovrebbe essere possibile visualizzare il dispositivo elencato come dispositivo IoT Edge.

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Schermata-interfaccia di amministrazione di Windows Azure IoT Edge dispositivo":::

19. Selezionare il dispositivo Azure IoT Edge per visualizzare il dashboard. Si noterà che i carichi di lavoro del dispositivo gemello nell'hub Azure è stato distribuito. Nell' **elenco IOT Edge modulo** dovrebbe essere visualizzato un modulo che esegue, **edgeAgent**, e lo **stato del IOT Edge** dovrebbe mostrare **attivo (in esecuzione)**.

Il dispositivo IoT Edge è ora configurato. È pronto per eseguire i moduli distribuiti nel cloud.

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo Azure IoT Edge dal cloud per distribuire un modulo che invia dati di telemetria all'hub IoT.

![Diagramma - Distribuire un modulo dal cloud al dispositivo](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualizzare i dati generati

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per distribuire un modulo di IoT Edge da eseguire nel dispositivo senza dovere apportare modifiche al dispositivo stesso.

In questo caso il modulo di cui è stato eseguito il push crea i dati dell'ambiente di esempio che potranno essere usati successivamente per il test. Il sensore simulato monitora un macchinario e l'ambiente intorno al macchinario. Questo sensore può trovarsi ad esempio in una stanza di server, in un ambiente di produzione o in una turbina eolica. Il messaggio include la temperatura e l'umidità dell'ambiente, la temperatura e la pressione dell'apparecchiatura e un timestamp. Le esercitazioni di IoT Edge usano i dati creati da questo modulo come dati di test per le analisi.

Verificare che il modulo distribuito dal cloud sia in esecuzione nel dispositivo IoT Edge passando alla shell dei comandi nell'interfaccia di amministrazione di Windows.

1. Connettersi al dispositivo IoT Edge appena creato

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Screenshot-connettere il dispositivo":::

2. Nella pagina **Overview (panoramica** ) viene visualizzato l' **elenco del modulo IOT Edge** e **IOT Edge stato** in cui è possibile visualizzare i vari moduli distribuiti, nonché lo stato del dispositivo.  

3. In **strumenti** selezionare **Shell comandi**. La shell dei comandi è un terminale di PowerShell che usa automaticamente SSH (Secure Shell) per connettersi alla VM Linux del dispositivo Azure IoT Edge nel PC Windows.

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Screenshot-shell comandi":::

4. Per verificare i tre moduli nel dispositivo, eseguire il **comando bash** seguente:

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Screenshot-elenco della shell dei comandi":::

5. Visualizzare i messaggi inviati dal modulo di sensore di temperatura al cloud.

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Quando viene fatto riferimento a nomi di moduli, i comandi di IoT Edge fanno distinzione tra maiuscole e minuscole.

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Schermata-sensore di temperatura":::

È anche possibile visualizzare i messaggi ricevuti dall'hub IoT usando l'[estensione Azure IoT Hub per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole continuare con le esercitazioni su IoT Edge, è possibile usare il dispositivo registrato e configurato in questa guida introduttiva. In caso contrario, è possibile eliminare le risorse di Azure create per evitare addebiti.

Se la macchina virtuale e l'hub IoT sono stati creati in un nuovo gruppo di risorse, è possibile eliminare il gruppo e tutte le risorse associate. Verificare il contenuto del gruppo di risorse per assicurarsi che non vi siano dati da conservare. Se non si vuole eliminare l'intero gruppo, è possibile eliminare le singole risorse.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile.

Rimuovere il gruppo **IoTEdgeResources**. L'eliminazione di un gruppo di risorse potrebbe richiedere alcuni minuti.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Per confermare la rimozione del gruppo di risorse, visualizzare l'elenco dei gruppi di risorse.

```azurecli-interactive
az group list
```

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>Rimozione pulita di Azure IoT Edge per Linux in Windows

È possibile disinstallare Azure IoT Edge per Linux in Windows dal dispositivo IoT Edge tramite l'estensione del dashboard nell'interfaccia di amministrazione di Windows.

1. Connettersi al Azure IoT Edge per Linux in connessione dispositivo Windows nell'interfaccia di amministrazione di Windows. Verrà caricata l'estensione dello strumento dashboard di Azure.
2. Selezionare **Disinstalla**. Una volta rimossa Azure IoT Edge per Linux in Windows, l'interfaccia di amministrazione di Windows passa alla pagina iniziale e rimuove la voce di connessione del dispositivo Azure IoT Edge dall'elenco.

Un altro modo per rimuovere Azure IOT Edge dal sistema Windows consiste nel passare alle impostazioni di **avvio**  >  **delle**  >  **app**  >  **Azure IOT Edge**  >  **disinstallare** sul dispositivo IOT Edge. Questa operazione rimuoverà Azure IoT Edge dal dispositivo IoT Edge, ma rimarrà invariata nell'interfaccia di amministrazione di Windows. L'interfaccia di amministrazione di Windows può essere disinstallata anche dal menu impostazioni.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un dispositivo IoT Edge ed è stata usata l'interfaccia cloud di Azure IoT Edge per distribuire il codice nel dispositivo. A questo punto, è disponibile un dispositivo di test che genera dati non elaborati relativi al proprio ambiente.

Il passaggio successivo consiste nell'impostare l'ambiente di sviluppo locale in modo che sia possibile avviare la creazione di moduli IoT Edge che eseguano la propria logica di business.

> [!div class="nextstepaction"]
> [Inizia a sviluppare moduli IoT Edge](tutorial-develop-for-linux.md)
