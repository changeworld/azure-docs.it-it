---
title: Avvio rapido per creare un dispositivo Azure IoT Edge in Windows | Microsoft Docs
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
ms.openlocfilehash: 9f0562d4471ac1129bf9bc7ecfee058cddac7c61
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533137"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Avvio rapido: Distribuire il primo modulo IoT Edge in un dispositivo Windows (anteprima)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Provare a Azure IoT Edge in questa guida introduttiva distribuendo il codice in contenitori in un dispositivo Linux IoT Edge Windows. IoT Edge consente di gestire in remoto il codice nei dispositivi, in modo da poter inviare più carichi di lavoro ai dispositivi perimetrali. Per questa guida introduttiva, è consigliabile usare il proprio dispositivo per vedere quanto sia semplice usare Azure IoT Edge per Linux in Windows.

In questa guida introduttiva si apprende come:

* Creare un hub IoT.
* Registrare un dispositivo IoT Edge nell'hub IoT.
* Installare e avviare il IoT Edge per Linux in Windows Runtime nel dispositivo.
* Distribuire in remoto un modulo in un dispositivo IoT Edge e inviare dati di telemetria.

![Diagramma che illustra l'architettura di questa guida introduttiva per il dispositivo e il cloud.](./media/quickstart/install-edge-full.png)

Questa guida introduttiva illustra come configurare il Azure IoT Edge per Linux in un dispositivo Windows. Si distribuirà quindi un modulo dal portale di Azure al dispositivo. Il modulo che verrà utilizzato è un sensore simulato che genera dati relativi a temperatura, umidità e pressione. Altre Azure IoT Edge esercitazioni si basano sul lavoro che si esegue qui distribuendo moduli che analizzano i dati simulati per informazioni dettagliate aziendali.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

>[!NOTE]
>IoT Edge per Linux in Windows è in [anteprima pubblica.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Prerequisiti

Preparare l'ambiente per l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Creare un gruppo di risorse cloud per gestire tutte le risorse che verranno usate in questa guida introduttiva.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Assicurarsi che il dispositivo IoT Edge soddisfi i requisiti seguenti:

* Edizioni
  * Windows 10 versione 1809 o successiva; build 17763 o successiva
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 build 17763 o versione successiva

* Requisiti hardware
  * Memoria disponibile minima: 1 GB
  * Spazio minimo su disco disponibile: 10 GB

>[!NOTE]
>Questa guida introduttiva usa Windows Admin Center per creare una distribuzione di IoT Edge per Linux in Windows. È anche possibile usare PowerShell. Se si vuole usare PowerShell per creare la distribuzione, seguire la procedura descritta nella guida alle procedure per l'installazione e il [provisioning di Azure IoT Edge per Linux in un dispositivo Windows.](how-to-install-iot-edge-on-windows.md)

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Per iniziare, creare un hub IoT con l'interfaccia della riga di comando di Azure.

![Diagramma che mostra il passaggio per creare un hub di I/O.](./media/quickstart/create-iot-hub.png)

Il livello gratuito di hub IoT di Azure funziona per questa guida introduttiva. Se l'hub IoT è già stato usato in passato ed è disponibile un hub, è possibile usarlo qui.

Il codice seguente crea un hub **F1** gratuito nel gruppo di risorse `IoTEdgeResources`. Sostituire `{hub_name}` con un nome univoco per l'hub IoT. La creazione di un hub IoT potrebbe richiedere alcuni minuti.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Se si verifica un errore perché nella sottoscrizione è già disponibile un hub gratuito, modificare lo SKU in `S1` . Se viene visualizzato un errore che indica che il nome dell'hub IoT non è disponibile, un altro utente ha già un hub con tale nome. Provare con un nuovo nome.

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

Registrare un dispositivo IoT Edge con l'hub IoT appena creato.

![Diagramma che illustra il passaggio per registrare un dispositivo con un'identità dell'hub IoT.](./media/quickstart/register-device.png)

Creare un'identità del dispositivo per il dispositivo simulato in modo che possa comunicare con l'hub IoT. L'identità del dispositivo si trova nel cloud e si usa una stringa di connessione del dispositivo univoca per associare un dispositivo fisico a un'identità del dispositivo.

IoT Edge i dispositivi si comportano e possono essere gestiti in modo diverso rispetto ai dispositivi IoT tipici. Usare il `--edge-enabled` flag per dichiarare che questa identità è per un IoT Edge dispositivo.

1. In Azure Cloud Shell immettere il comando seguente per creare un dispositivo **denominato myEdgeDevice** nell'hub.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Se viene visualizzato un errore sulle chiavi dei criteri, assicurarsi che Cloud Shell sia in esecuzione la versione `iothubowner` più recente dell'estensione Azure IoT.

1. Visualizzare la stringa di connessione per il dispositivo, che collega il dispositivo fisico alla sua identità nell'hub IoT. Contiene il nome dell'hub IoT, il nome del dispositivo e una chiave condivisa che autentica le connessioni tra i due.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Copiare il valore della chiave `connectionString` dall'output JSON e salvarlo. Questo valore corrisponde alla stringa di connessione Verrà utilizzato per configurare il runtime IoT Edge nella sezione successiva.

     ![Screenshot che mostra l'output di connectionString in Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installare e avviare il runtime di IoT Edge

Installare IoT Edge per Linux in Windows nel dispositivo e configurarlo con la stringa di connessione del dispositivo.

![Diagramma che illustra il passaggio per avviare il IoT Edge runtime.](./media/quickstart/start-runtime.png)

1. [Scaricare Windows Admin Center](https://aka.ms/wacdownload).

1. Seguire le istruzioni nell'installazione guidata per configurare Windows Admin Center nel dispositivo.

1. Aprire Windows Admin Center.

1. Selezionare **l'icona a forma di** ingranaggio Impostazioni nell'angolo superiore destro e quindi **selezionare Estensioni**.

1. Nella scheda **Feed selezionare** **Aggiungi**.

1. Immettere `https://aka.ms/wac-insiders-feed` nella casella di testo e quindi selezionare **Aggiungi**.

1. Dopo aver aggiunto il feed, passare alla scheda **Estensioni** disponibili e attendere l'aggiornamento dell'elenco delle estensioni.

1. Nell'elenco Estensioni **disponibili** selezionare **Azure IoT Edge**.

1. Installare l'estensione.

1. Quando l'estensione è installata, **selezionare Windows Admin Center** nell'angolo superiore sinistro per passare alla pagina del dashboard principale.

     La **connessione localhost** rappresenta il PC in cui si esegue Windows Admin Center.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Screenshot della pagina iniziale di Windows Admin.":::

1. Selezionare **Aggiungi**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Screenshot che mostra la selezione del pulsante Aggiungi in Windows Admin Center.":::

1. Nel riquadro Azure IoT Edge selezionare Crea **nuovo** per avviare l'installazione guidata.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Screenshot che mostra la creazione di una nuova distribuzione nel Azure IoT Edge til.":::

1. Continuare con l'installazione guidata per accettare il Condizioni di licenza software Microsoft e quindi selezionare **Avanti.**

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Screenshot che mostra la selezione di Avanti per continuare l'installazione guidata.":::

1. Selezionare **Dati di diagnostica facoltativi** e quindi Selezionare **Avanti: Distribuisci**. Questa selezione fornisce dati di diagnostica estesi che consentono a Microsoft di monitorare e mantenere la qualità del servizio.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Screenshot che mostra le opzioni dei dati di diagnostica.":::

1. Nella schermata **Seleziona dispositivo di destinazione** selezionare il dispositivo di destinazione desiderato per verificare che soddisfi i requisiti minimi. Per questa guida introduttiva, si sta installando IoT Edge nel dispositivo locale, quindi scegliere la **connessione localhost.** Se il dispositivo di destinazione soddisfa i requisiti, selezionare **Avanti** per continuare.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Screenshot che mostra l'elenco dei dispositivi di destinazione.":::

1. Selezionare **Avanti** per accettare le impostazioni predefinite. La schermata di distribuzione mostra il processo di download del pacchetto, l'installazione del pacchetto, la configurazione dell'host e la configurazione finale della macchina virtuale Linux. Una distribuzione riuscita è simile alla seguente:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Screenshot di una distribuzione riuscita.":::

1. Selezionare **Avanti: Connetti** per continuare con il passaggio finale per effettuare il provisioning del dispositivo Azure IoT Edge con il relativo ID dispositivo dall'istanza dell'hub IoT.

1. Incollare la stringa di connessione copiata in precedenza [in questa guida introduttiva](#register-an-iot-edge-device) nel campo Stringa di **connessione** del dispositivo. Selezionare quindi **Provisioning con il metodo selezionato.**

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Screenshot che mostra la stringa di connessione nel campo Stringa di connessione del dispositivo.":::

1. Al termine del provisioning, selezionare **Fine** per completare e tornare alla Windows Admin Center iniziale. Il dispositivo dovrebbe essere elencato come IoT Edge dispositivo.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Screenshot che mostra tutte le connessioni in Windows Admin Center.":::

1. Selezionare il dispositivo Azure IoT Edge per visualizzarne il dashboard. Si dovrebbe vedere che i carichi di lavoro dal dispositivo gemello in hub IoT di Azure sono stati distribuiti. **L IoT Edge di modulo** deve mostrare un modulo che esegue **edgeAgent** **e** lo stato IoT Edge deve essere **attivo (in esecuzione).**

Il dispositivo IoT Edge è ora configurato. È pronto per eseguire i moduli distribuiti nel cloud.

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo Azure IoT Edge dal cloud per distribuire un modulo che invia dati di telemetria all'hub IoT.

![Diagramma che illustra il passaggio per distribuire un modulo.](./media/quickstart/deploy-module.png)

<!--
[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2
-->

Una delle funzionalità principali di Azure IoT Edge è la distribuzione di codice nel IoT Edge dispositivi dal cloud. I *moduli IoT Edge* sono pacchetti eseguibili implementati come contenitori. In questa sezione si distribuirà un modulo predefinito dalla sezione moduli IoT Edge di Azure Marketplace [direttamente](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) da hub IoT di Azure.

Questo modulo simula un sensore e invia i dati generati. Si tratta di un codice utile per iniziare a usare IoT Edge, perché è possibile usare i dati simulati per lo sviluppo e i test. Per informazioni specifiche sul funzionamento di questo modulo, è possibile visualizzare il [codice sorgente del sensore temperatura simulato](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Seguire questa procedura per distribuire il primo modulo da Azure Marketplace.

1. Accedere al portale di Azure [e](https://portal.azure.com) passare all'hub IoT.

1. Nel menu a sinistra, in **Gestione automatica dei dispositivi,** **selezionare IoT Edge**.

1. Selezionare l'ID del dispositivo di destinazione dall'elenco dei dispositivi.

1. Sulla barra superiore selezionare **Imposta moduli**.

   ![Screenshot che mostra la selezione di Imposta moduli.](./media/quickstart/select-set-modules.png)

1. In **IoT Edge Moduli** aprire **il** menu a discesa Aggiungi e quindi selezionare Modulo **Marketplace.**

   ![Screenshot che mostra il menu a discesa Aggiungi.](./media/quickstart/add-marketplace-module.png)

1. In **IoT Edge Marketplace** del modulo cercare e selezionare il `Simulated Temperature Sensor` modulo.

   Il modulo viene aggiunto alla sezione moduli IoT Edge con lo stato di **esecuzione** desiderato.

1. Selezionare **Avanti: Route** per continuare con il passaggio successivo della procedura guidata.

   ![Screenshot che mostra come continuare con il passaggio successivo dopo l'aggiunta del modulo.](./media/quickstart/view-temperature-sensor-next-routes.png)

1. Nella scheda **Route** rimuovere la route predefinita, **instradare** e quindi selezionare **Avanti: Rivedi** e crea per continuare con il passaggio successivo della procedura guidata.

   >[!Note]
   >Le route vengono costruite usando coppie nome/valore. In questa pagina verranno visualizzate due route. La route predefinita, **route**, invia tutti i messaggi all'hub IoT ( denominato `$upstream` ). Una seconda route, **SimulatedTemperatureSensorToIoTHub,** è stata creata automaticamente quando è stato aggiunto il modulo da Azure Marketplace. Questa route invia tutti i messaggi dal modulo della temperatura simulata all'hub IoT. È possibile eliminare la route predefinita perché in questo caso è ridondante.

   ![Screenshot che mostra la rimozione della route predefinita e il passaggio al passaggio successivo.](./media/quickstart/delete-route-next-review-create.png)

1. Esaminare il file JSON e quindi selezionare **Crea.** Il file JSON definisce tutti i moduli distribuiti nel dispositivo IoT Edge dispositivo. Verranno visualizzati il modulo **SimulatedTemperatureSensor** e i due moduli di runtime, **edgeAgent** **ed edgeHub.**

   >[!Note]
   >Quando si invia una nuova distribuzione a un dispositivo IoT Edge, non viene eseguito il push di alcun elemento al dispositivo. Al contrario, il dispositivo richiede periodicamente eventuali nuove istruzioni all'hub IoT. Se il dispositivo trova un manifesto della distribuzione aggiornato, usa le informazioni sulla nuova distribuzione per eseguire il pull delle immagini dei moduli dal cloud, quindi avvia l'esecuzione dei moduli in locale. Questo processo può richiedere alcuni minuti.

1. Dopo aver creato i dettagli della distribuzione dei moduli, la procedura guidata torna nella pagina Dettagli dispositivo. Visualizzare lo stato della distribuzione nella **scheda** Moduli.

   Dovrebbero essere visualizzati tre moduli: **$edgeAgent**, **$edgeHub** e **SimulatedTemperatureSensor**. Se uno o più moduli hanno **YES** in **SPECIFIED IN DEPLOYMENT** ma non in REPORTED BY **DEVICE,** il IoT Edge dispositivo li sta ancora avviando. Attendere alcuni minuti e quindi aggiornare la pagina.

   ![Screenshot che mostra il sensore di temperatura simulato nell'elenco dei moduli distribuiti.](./media/quickstart/view-deployed-modules.png)

## <a name="view-the-generated-data"></a>Visualizzare i dati generati

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per distribuire un modulo IoT Edge da eseguire nel dispositivo senza dover apportare modifiche al dispositivo stesso.

Il modulo di cui è stato fatto il push genera dati dell'ambiente di esempio che è possibile usare per il test in un secondo momento. Il sensore simulato monitora un macchinario e l'ambiente intorno al macchinario. Questo sensore può trovarsi ad esempio in una stanza di server, in un ambiente di produzione o in una turbina eolica. I messaggi inviati includono la temperatura e l'umidità dell'ambiente, la temperatura e la pressione della macchina e un timestamp. IoT Edge esercitazioni usano i dati creati da questo modulo come dati di test per l'analisi.

Dalla shell dei comandi in Windows Admin Center verificare che il modulo distribuito dal cloud sia in esecuzione nel IoT Edge dispositivo.

1. Connettersi al dispositivo IoT Edge appena creato.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Screenshot che mostra la selezione di Connetti in Windows Admin Center.":::

     Nella pagina **Panoramica** verranno visualizzati l'elenco IoT Edge **modulo e** IoT Edge **stato**. È possibile visualizzare i moduli distribuiti e lo stato del dispositivo.  

1. In **Strumenti** selezionare **Shell comandi**. La shell dei comandi è un terminale di PowerShell che usa automaticamente Secure Shell (SSH) per connettersi alla macchina virtuale Linux del dispositivo Azure IoT Edge nel PC Windows.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Screenshot che mostra l'apertura della shell dei comandi.":::

1. Per verificare i tre moduli nel dispositivo, eseguire il comando Bash seguente:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Screenshot che mostra l'output dell'elenco edge I o T della shell dei comandi.":::

1. Visualizzare i messaggi inviati dal modulo di sensore di temperatura al cloud.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge comandi fanno distinzione tra maiuscole e minuscole quando fanno riferimento ai nomi dei moduli.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Screenshot che mostra l'elenco dei messaggi inviati dal modulo al cloud.":::

È anche possibile usare [l'estensione hub IoT di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) per controllare l'arrivo dei messaggi all'hub IoT.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole continuare con le esercitazioni IoT Edge, ignorare questo passaggio. È possibile usare il dispositivo registrato e configurato in questa guida introduttiva. In caso contrario, è possibile eliminare le risorse di Azure create per evitare addebiti.

Se la macchina virtuale e l'hub IoT sono stati creati in un nuovo gruppo di risorse, è possibile eliminare il gruppo e tutte le risorse associate. Se non si vuole eliminare l'intero gruppo, è possibile eliminare le singole risorse.

> [!IMPORTANT]
> Controllare il contenuto del gruppo di risorse per assicurarsi che non ci siano elementi da mantenere. L'eliminazione di un gruppo di risorse è irreversibile.

Usare il comando seguente per rimuovere il **gruppo IoTEdgeResources.** L'eliminazione potrebbe richiedere alcuni minuti.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

È possibile verificare che il gruppo di risorse sia stato rimosso usando questo comando per visualizzare l'elenco dei gruppi di risorse.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Rimuovere Azure IoT Edge per Linux in Windows

Usare l'estensione dashboard in Windows Admin Center per disinstallare Azure IoT Edge per Linux in Windows.

1. Connettersi al dispositivo IoT Edge in Windows Admin Center. Viene caricata l'estensione dello strumento dashboard di Azure.

1. Selezionare **Disinstalla**. Dopo Azure IoT Edge rimozione, Windows Admin Center la voce Azure IoT Edge connessione del dispositivo dalla **pagina** Iniziale.

>[!Note]
>Un altro modo per rimuovere Azure IoT Edge dal sistema Windows è selezionare **Start** Settings Apps (Avvia impostazioni  >    >  **app)** Azure IoT Edge Uninstall (Disinstalla) IoT Edge  >    >   dispositivo. Questo metodo rimuove Azure IoT Edge dal dispositivo IoT Edge, ma lascia la connessione in Windows Admin Center. Per completare la rimozione, Windows Admin Center **dal** menu Impostazioni.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un dispositivo IoT Edge ed è stata usata l'interfaccia cloud di Azure IoT Edge per distribuire il codice nel dispositivo. È ora disponibile un dispositivo di test che genera dati non elaborati sul relativo ambiente.

Configurare quindi l'ambiente di sviluppo locale in modo da iniziare a creare IoT Edge moduli che eseguono la logica di business.

> [!div class="nextstepaction"]
> [Iniziare a sviluppare IoT Edge moduli](tutorial-develop-for-linux.md)
