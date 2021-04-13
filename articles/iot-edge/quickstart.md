---
title: Guida introduttiva per la creazione di un dispositivo Azure IoT Edge in Windows | Microsoft Docs
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
ms.openlocfilehash: de24f6c8436b4537519f8cc65931325dd7d5f8d9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313352"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Guida introduttiva: distribuire il primo modulo di IoT Edge in un dispositivo Windows (anteprima)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Provare Azure IoT Edge in questa Guida introduttiva distribuendo codice in contenitori in un dispositivo Linux IoT Edge Windows. IoT Edge consente di gestire in remoto il codice nei dispositivi, in modo da poter inviare più carichi di lavoro ai dispositivi perimetrali. Per questa Guida introduttiva, si consiglia di usare il proprio dispositivo per vedere quanto è facile usare Azure IoT Edge per Linux in Windows.

In questa guida introduttiva si apprende come:

* Creare un hub IoT.
* Registrare un dispositivo IoT Edge nell'hub IoT.
* Installare e avviare la IoT Edge per Linux in Windows Runtime nel dispositivo.
* Distribuire in modalità remota un modulo a un dispositivo IoT Edge e inviare dati di telemetria.

![Diagramma che illustra l'architettura di questa Guida introduttiva per il dispositivo e il cloud.](./media/quickstart/install-edge-full.png)

Questa Guida introduttiva illustra come configurare la Azure IoT Edge per Linux nel dispositivo Windows. Quindi, si distribuirà un modulo dal portale di Azure al dispositivo. Il modulo che verrà usato è un sensore simulato che genera dati di temperatura, umidità e pressione. Altre esercitazioni Azure IoT Edge si basano sul lavoro svolto in questo articolo, distribuendo moduli che analizzano i dati simulati per informazioni aziendali.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

>[!NOTE]
>IoT Edge per Linux in Windows è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Preparare l'ambiente per l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Creare un gruppo di risorse cloud per gestire tutte le risorse che verranno usate in questa Guida introduttiva.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Verificare che il dispositivo IoT Edge soddisfi i requisiti seguenti:

* Edizioni
  * Windows 10 versione 1809 o successiva; Build 17763 o versione successiva
    * Professional, Enterprise, Internet delle cose
  * Windows Server 2019 Build 17763 o versione successiva

* Requisiti hardware
  * Memoria minima disponibile: 2 GB
  * Spazio minimo disponibile su disco: 10 GB

>[!NOTE]
>Questa Guida introduttiva usa l'interfaccia di amministrazione di Windows per creare una distribuzione di IoT Edge per Linux in Windows. È anche possibile usare PowerShell. Se si vuole usare PowerShell per creare la distribuzione, seguire i passaggi descritti nella Guida alle procedure per l' [installazione e il provisioning di Azure IOT Edge per Linux in un dispositivo Windows](how-to-install-iot-edge-on-windows.md).

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Per iniziare, creare un hub Internet con l'interfaccia della riga di comando di Azure.

![Diagramma che illustra il passaggio per creare un hub I o T.](./media/quickstart/create-iot-hub.png)

Per questa Guida introduttiva viene utilizzato il livello gratuito dell'hub Azure. Se l'hub IoT è già stato usato in passato ed è disponibile un hub, è possibile usarlo qui.

Il codice seguente crea un hub **F1** gratuito nel gruppo di risorse `IoTEdgeResources`. Sostituire `{hub_name}` con un nome univoco per l'hub IoT. La creazione di un hub IoT potrebbe richiedere alcuni minuti.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Se si riceve un errore perché è già presente un hub gratuito nella sottoscrizione, impostare lo SKU su `S1` . Se viene ricevuto un messaggio di errore che segnala che il nome dell'hub Internet non è disponibile, qualcun altro dispone già di un hub con tale nome. Provare con un nuovo nome.

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

Registrare un dispositivo IoT Edge con l'hub IoT appena creato.

![Diagramma che illustra il passaggio per registrare un dispositivo con un'identità dell'hub Internet.](./media/quickstart/register-device.png)

Creare un'identità del dispositivo per il dispositivo simulato in modo che possa comunicare con l'hub IoT. L'identità del dispositivo si trova nel cloud e si usa una stringa di connessione del dispositivo univoca per associare un dispositivo fisico a un'identità del dispositivo.

I dispositivi IoT Edge si comportano e possono essere gestiti in modo diverso rispetto ai normali dispositivi Internet. Usare il `--edge-enabled` flag per dichiarare che questa identità è per un dispositivo IOT Edge.

1. In Azure Cloud Shell, immettere il comando seguente per creare un dispositivo denominato **myEdgeDevice** nell'hub.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Se viene ricevuto un errore relativo `iothubowner` alle chiavi dei criteri, assicurarsi che cloud Shell esegua la versione più recente dell'estensione Azure.

1. Visualizzare la stringa di connessione per il dispositivo, che collega il dispositivo fisico alla sua identità nell'hub IoT. Contiene il nome dell'hub Internet delle cose, il nome del dispositivo e una chiave condivisa che autentica le connessioni tra i due.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Copiare il valore della chiave `connectionString` dall'output JSON e salvarlo. Questo valore corrisponde alla stringa di connessione Verrà usato per configurare il runtime di IoT Edge nella sezione successiva.

     ![Screenshot che mostra l'output di connectionString in Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installare e avviare il runtime di IoT Edge

Installare IoT Edge per Linux in Windows nel dispositivo e configurarlo con la stringa di connessione del dispositivo.

![Diagramma che illustra il passaggio per avviare il runtime di IoT Edge.](./media/quickstart/start-runtime.png)

1. [Scaricare](https://aka.ms/wacdownload)l'interfaccia di amministrazione di Windows.

1. Seguire le istruzioni dell'installazione guidata per configurare l'interfaccia di amministrazione di Windows sul dispositivo.

1. Aprire l'interfaccia di amministrazione di Windows.

1. Selezionare l'icona dell' **ingranaggio impostazioni** nell'angolo in alto a destra e quindi selezionare **estensioni**.

1. Nella scheda **feed** selezionare **Aggiungi**.

1. Immettere `https://aka.ms/wac-insiders-feed` nella casella di testo e quindi selezionare **Aggiungi**.

1. Una volta aggiunto il feed, passare alla scheda **estensioni disponibili** e attendere l'aggiornamento dell'elenco estensioni.

1. Nell'elenco delle **estensioni disponibili** selezionare **Azure IOT Edge**.

1. Installare l'estensione.

1. Quando l'estensione è installata, selezionare l'interfaccia di **amministrazione di Windows** nell'angolo superiore sinistro per passare alla pagina principale del dashboard.

     La connessione **localhost** rappresenta il PC in cui si esegue l'interfaccia di amministrazione di Windows.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Screenshot della pagina iniziale dell'amministratore di Windows.":::

1. Selezionare **Aggiungi**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Screenshot che Mostra come selezionare il pulsante Aggiungi nell'interfaccia di amministrazione di Windows.":::

1. Nel riquadro Azure IoT Edge selezionare **Crea nuovo** per avviare l'installazione guidata.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Screenshot che mostra la creazione di una nuova distribuzione nel Azure IoT Edge fino a.":::

1. Continuare con l'installazione guidata di per accettare le condizioni di licenza software Microsoft e quindi selezionare **Avanti**.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Screenshot che mostra la selezione di avanti per continuare con l'installazione guidata.":::

1. Selezionare **dati di diagnostica facoltativi** e quindi fare clic su **Avanti: Distribuisci**. Questa selezione fornisce dati di diagnostica estesi che consentono a Microsoft di monitorare e gestire la qualità del servizio.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Screenshot che mostra le opzioni dei dati di diagnostica.":::

1. Nella schermata **selezionare il dispositivo di destinazione** selezionare il dispositivo di destinazione desiderato per verificare che soddisfi i requisiti minimi. Per questa Guida introduttiva si sta installando IoT Edge sul dispositivo locale, quindi scegliere la connessione **localhost** . Se il dispositivo di destinazione soddisfa i requisiti, fare clic su **Avanti** per continuare.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Screenshot che mostra l'elenco dei dispositivi di destinazione.":::

1. Selezionare **Avanti** per accettare le impostazioni predefinite. La schermata di distribuzione Mostra il processo di download del pacchetto, installazione del pacchetto, configurazione dell'host e configurazione finale della macchina virtuale (VM) Linux. Una distribuzione corretta avrà un aspetto simile al seguente:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Screenshot di una distribuzione riuscita.":::

1. Selezionare **Avanti: Connetti** per continuare con il passaggio finale per eseguire il provisioning del dispositivo Azure IOT Edge con l'ID del dispositivo dall'istanza dell'hub Internet.

1. Incollare la stringa di connessione copiata [in precedenza in questa Guida introduttiva](#register-an-iot-edge-device) nel campo **stringa di connessione del dispositivo** . Selezionare quindi **provisioning con il metodo selezionato**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Screenshot che mostra la stringa di connessione nel campo della stringa di connessione del dispositivo.":::

1. Al termine del provisioning, selezionare **fine** per completare e tornare alla schermata Start dell'interfaccia di amministrazione di Windows. Il dispositivo dovrebbe essere visualizzato come dispositivo IoT Edge.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Screenshot che Mostra tutte le connessioni nell'interfaccia di amministrazione di Windows.":::

1. Selezionare il dispositivo Azure IoT Edge per visualizzare il dashboard. Si noterà che i carichi di lavoro del dispositivo gemello nell'hub Azure è stato distribuito. L' **elenco dei moduli IOT Edge** dovrebbe mostrare un modulo che esegue **EdgeAgent** e lo **stato del IOT Edge** deve essere **attivo (in esecuzione)**.

Il dispositivo IoT Edge è ora configurato. È pronto per eseguire i moduli distribuiti nel cloud.

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo Azure IoT Edge dal cloud per distribuire un modulo che invia dati di telemetria all'hub IoT.

![Diagramma che illustra il passaggio per distribuire un modulo.](./media/quickstart/deploy-module.png)

<!--
[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2
-->

Una delle funzionalità principali di Azure IoT Edge consiste nel distribuire il codice nei dispositivi IoT Edge dal cloud. I *moduli IoT Edge* sono pacchetti eseguibili implementati come contenitori. In questa sezione verrà distribuito un modulo predefinito dalla [sezione moduli IOT Edge di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) direttamente dall'hub Azure.

Questo modulo simula un sensore e invia i dati generati. Si tratta di un codice utile per iniziare a usare IoT Edge, perché è possibile usare i dati simulati per lo sviluppo e i test. Per informazioni specifiche sul funzionamento di questo modulo, è possibile visualizzare il [codice sorgente del sensore temperatura simulato](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Per distribuire il primo modulo da Azure Marketplace, seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub Internet.

1. Dal menu a sinistra, in **gestione automatica dispositivi**, selezionare **IOT Edge**.

1. Selezionare l'ID dispositivo del dispositivo di destinazione dall'elenco di dispositivi.

1. Sulla barra superiore selezionare **Imposta moduli**.

   ![Screenshot che mostra la selezione di set modules.](./media/quickstart/select-set-modules.png)

1. In **moduli IOT Edge** aprire il menu a discesa **Aggiungi** , quindi selezionare **modulo Marketplace**.

   ![Screenshot che mostra il menu a discesa Aggiungi.](./media/quickstart/add-marketplace-module.png)

1. In **IOT Edge Module Marketplace** cercare e selezionare il `Simulated Temperature Sensor` modulo.

   Il modulo viene aggiunto alla sezione moduli IoT Edge con lo stato **in esecuzione** desiderato.

1. Selezionare **Avanti: Route** per continuare con il passaggio successivo della procedura guidata.

   ![Screenshot che Mostra come proseguire con il passaggio successivo dopo l'aggiunta del modulo.](./media/quickstart/view-temperature-sensor-next-routes.png)

1. Nella scheda **Route** rimuovere la route predefinita, **Route**, quindi selezionare **Next: Review + create** per passare al passaggio successivo della procedura guidata.

   >[!Note]
   >Le route vengono costruite mediante coppie di nome e valore. In questa pagina verranno visualizzate due route. La route predefinita, **Route**, invia tutti i messaggi all'hub Internet (denominato `$upstream` ). Una seconda route, **SimulatedTemperatureSensorToIoTHub**, è stata creata automaticamente quando è stato aggiunto il modulo da Azure Marketplace. Questa route invia tutti i messaggi dal modulo temperatura simulata all'hub Internet. È possibile eliminare la route predefinita perché in questo caso è ridondante.

   ![Screenshot che Mostra come rimuovere la route predefinita, quindi passare al passaggio successivo.](./media/quickstart/delete-route-next-review-create.png)

1. Esaminare il file JSON e quindi selezionare **Crea**. Il file JSON definisce tutti i moduli distribuiti nel dispositivo IoT Edge. Verranno visualizzati il modulo **SimulatedTemperatureSensor** e i due moduli di runtime, **edgeAgent** e **edgeHub**.

   >[!Note]
   >Quando si invia una nuova distribuzione a un dispositivo IoT Edge, non viene eseguito il push di alcun elemento al dispositivo. Al contrario, il dispositivo richiede periodicamente eventuali nuove istruzioni all'hub IoT. Se il dispositivo trova un manifesto della distribuzione aggiornato, usa le informazioni sulla nuova distribuzione per eseguire il pull delle immagini dei moduli dal cloud, quindi avvia l'esecuzione dei moduli in locale. Questo processo può richiedere alcuni minuti.

1. Dopo aver creato i dettagli della distribuzione dei moduli, la procedura guidata torna nella pagina Dettagli dispositivo. Visualizzare lo stato della distribuzione nella scheda **moduli** .

   Verranno visualizzati tre moduli: **$edgeAgent**, **$edgeHub** e **SimulatedTemperatureSensor**. Se uno o più moduli hanno **Sì** IN **specificato nella distribuzione** , ma non sono **segnalati dal dispositivo**, il dispositivo IOT Edge li sta ancora avviando. Attendere alcuni minuti e quindi aggiornare la pagina.

   ![Screenshot che mostra il sensore di temperatura simulato nell'elenco dei moduli distribuiti.](./media/quickstart/view-deployed-modules.png)

## <a name="view-the-generated-data"></a>Visualizzare i dati generati

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per distribuire un modulo di IoT Edge da eseguire sul dispositivo senza dover apportare modifiche al dispositivo stesso.

Il modulo sottomesso a push genera dati dell'ambiente di esempio che è possibile usare per il test in un secondo momento. Il sensore simulato monitora un macchinario e l'ambiente intorno al macchinario. Questo sensore può trovarsi ad esempio in una stanza di server, in un ambiente di produzione o in una turbina eolica. I messaggi inviati includono temperatura ambiente e umidità, temperatura e pressione del computer e un timestamp. IoT Edge esercitazioni usano i dati creati da questo modulo come dati di test per l'analisi.

Dalla shell dei comandi nell'interfaccia di amministrazione di Windows verificare che il modulo distribuito dal cloud sia in esecuzione nel dispositivo IoT Edge.

1. Connettersi al dispositivo IoT Edge appena creato.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Screenshot che mostra la selezione di Connetti nell'interfaccia di amministrazione di Windows.":::

     Nella pagina **Overview (panoramica** ) verrà visualizzato l' **elenco IoT Edge Module** e **IOT Edge status**. È possibile visualizzare i moduli distribuiti e lo stato del dispositivo.  

1. In **strumenti** selezionare **Shell comandi**. La shell dei comandi è un terminale di PowerShell che usa automaticamente Secure Shell (SSH) per connettersi alla VM Linux del dispositivo Azure IoT Edge nel PC Windows.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Screenshot che mostra l'apertura della shell dei comandi.":::

1. Per verificare i tre moduli nel dispositivo, eseguire il comando bash seguente:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Screenshot che mostra l'output della shell dei comandi I/o.":::

1. Visualizzare i messaggi inviati dal modulo di sensore di temperatura al cloud.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge comandi fanno distinzione tra maiuscole e minuscole quando fanno riferimento ai nomi dei moduli.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Screenshot che mostra l'elenco dei messaggi inviati dal modulo al cloud.":::

È anche possibile usare l' [estensione hub Internet degli hub Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) per guardare i messaggi che arrivano all'hub Internet.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si desidera continuare con le esercitazioni di IoT Edge, ignorare questo passaggio. È possibile usare il dispositivo registrato e configurato in questa Guida introduttiva. In caso contrario, è possibile eliminare le risorse di Azure create per evitare addebiti.

Se la macchina virtuale e l'hub IoT sono stati creati in un nuovo gruppo di risorse, è possibile eliminare il gruppo e tutte le risorse associate. Se non si vuole eliminare l'intero gruppo, è possibile eliminare le singole risorse.

> [!IMPORTANT]
> Verificare il contenuto del gruppo di risorse per assicurarsi che non ci siano elementi da contenere. L'eliminazione di un gruppo di risorse è irreversibile.

Usare il comando seguente per rimuovere il gruppo **IoTEdgeResources** . L'eliminazione potrebbe richiedere alcuni minuti.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

È possibile verificare che il gruppo di risorse venga rimosso usando questo comando per visualizzare l'elenco dei gruppi di risorse.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Rimuovere Azure IoT Edge per Linux in Windows

Usare l'estensione Dashboard nell'interfaccia di amministrazione di Windows per disinstallare Azure IoT Edge per Linux in Windows.

1. Connettersi al dispositivo IoT Edge nell'interfaccia di amministrazione di Windows. Viene caricata l'estensione dello strumento dashboard di Azure.

1. Selezionare **Disinstalla**. Dopo la rimozione di Azure IoT Edge, il centro di amministrazione di Windows rimuove la voce di connessione del dispositivo Azure IoT Edge dalla pagina **iniziale** .

>[!Note]
>Un altro modo per rimuovere Azure IOT Edge dal sistema Windows consiste nel selezionare **Avvia**  >  **Impostazioni**  >  **app**  >  **Azure IOT Edge**  >  **disinstallare** sul dispositivo IOT Edge. Questo metodo rimuove Azure IoT Edge dal dispositivo IoT Edge, ma lascia la connessione dietro l'interfaccia di amministrazione di Windows. Per completare la rimozione, disinstallare l'interfaccia di amministrazione di Windows anche dal menu **Impostazioni** .

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un dispositivo IoT Edge ed è stata usata l'interfaccia cloud di Azure IoT Edge per distribuire il codice nel dispositivo. A questo punto si dispone di un dispositivo di test che genera dati non elaborati sull'ambiente.

Successivamente, configurare l'ambiente di sviluppo locale in modo da poter iniziare a creare moduli di IoT Edge che eseguono la logica di business.

> [!div class="nextstepaction"]
> [Inizia a sviluppare moduli IoT Edge](tutorial-develop-for-linux.md)
