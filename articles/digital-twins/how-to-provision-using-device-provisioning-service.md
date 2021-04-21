---
title: Gestire automaticamente i dispositivi con il servizio Device Provisioning
titleSuffix: Azure Digital Twins
description: Informazioni su come configurare un processo automatizzato per effettuare il provisioning e ritirare i dispositivi IoT in Gemelli digitali di Azure tramite il servizio Device Provisioning.
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 51b5714f9009cbe48aa49c6a04a1434cec12396e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790690"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Gestire automaticamente i dispositivi in Gemelli digitali di Azure tramite il servizio Device Provisioning

In questo articolo si apprenderà come integrare Gemelli digitali di Azure con il servizio [Device Provisioning](../iot-dps/about-iot-dps.md).

La soluzione descritta in questo articolo consentirà di **** automatizzare il processo di provisioning e ritiro dei dispositivi dell'hub **_IoT_** in Gemelli digitali di Azure, usando il servizio Device Provisioning. 

Per altre informazioni sulle  fasi di provisioning e ritiro e per comprendere meglio il set di fasi generali [  ](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) di gestione dei dispositivi comuni a tutti i progetti IoT aziendali, vedere la sezione Ciclo di vita dei dispositivi della documentazione sulla gestione dei dispositivi dell'hub IoT. 

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il provisioning, è necessario configurare quanto segue:
* Istanza **Gemelli digitali di Azure.** Seguire le istruzioni in [*Procedura: Configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md) per creare un'istanza di Gemelli digitali di Azure. Raccogliere il nome **_host dell'istanza_** nel portale di Azure ([istruzioni](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* un **hub IoT**. Per istruzioni, vedere la sezione *Creare un hub IoT* di questa guida [introduttiva all'hub IoT.](../iot-hub/quickstart-send-telemetry-cli.md)
* una [**funzione di Azure**](../azure-functions/functions-overview.md) che aggiorna le informazioni sui gemelli digitali in base ai dati dell'hub IoT. Seguire le istruzioni in [*Procedura: Inserire i dati dell'hub IoT*](how-to-ingest-iot-hub-data.md) per creare questa funzione di Azure. Raccogliere il nome **_della funzione_** per usarlo in questo articolo.

Questo esempio usa anche un **simulatore di dispositivi che** include il provisioning tramite il servizio Device Provisioning. Il simulatore di dispositivo si trova qui: [Gemelli digitali di Azure esempio di integrazione dell'hub IoT.](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) Ottenere il progetto di esempio nel computer passando al collegamento di esempio e selezionando il **pulsante Esplora** codice sotto il titolo. Verrà visualizzato il repository GitHub per l'esempio, che è possibile scaricare come *. Zip* selezionando il **pulsante Codice** e Scarica **ZIP.** 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="Screenshot del repository digital-twins-iothub-integration in GitHub. Il pulsante Codice è selezionato, producendo una piccola finestra di dialogo in cui è evidenziato il pulsante Scarica ZIP." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

Decomprimere la cartella scaricata.

Sarà necessario installare [**Node.js**](https://nodejs.org/download) nel computer. Il simulatore di dispositivo è **basatoNode.js**, versione 10.0.x o successiva.

## <a name="solution-architecture"></a>Architettura della soluzione

L'immagine seguente illustra l'architettura di questa soluzione usando Gemelli digitali di Azure con il servizio Device Provisioning. Mostra sia il flusso di provisioning del dispositivo che il flusso di ritiro.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="Diagramma del dispositivo e di diversi servizi di Azure in uno scenario end-to-end. I dati passano avanti e indietro tra un dispositivo termostato e DPS. I dati passano anche da DPS all'hub IoT e Gemelli digitali di Azure tramite una funzione di Azure etichettata &quot;Allocation&quot;. I dati di un'azione manuale &quot;Elimina dispositivo&quot; passano attraverso l'hub IoT > hub eventi > Funzioni di Azure > Gemelli digitali di Azure." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

Questo articolo è diviso in due sezioni:
* [*Effettuare il provisioning automatico del dispositivo usando il servizio Device Provisioning*](#auto-provision-device-using-device-provisioning-service)
* [*Ritirare automaticamente il dispositivo usando gli eventi del ciclo di vita dell'hub IoT*](#auto-retire-device-using-iot-hub-lifecycle-events)

Per una spiegazione più approfondita di ogni passaggio dell'architettura, vedere le singole sezioni più avanti nell'articolo.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Effettuare il provisioning automatico del dispositivo usando il servizio Device Provisioning

In questa sezione verrà collegato il servizio Device Provisioning per Gemelli digitali di Azure il provisioning automatico dei dispositivi tramite il percorso seguente. Questo è un estratto dell'architettura completa illustrata in [precedenza.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="Diagramma del flusso di provisioning: estratto del diagramma dell'architettura della soluzione, con numeri che etichettano le sezioni del flusso. I dati passano tra un dispositivo termostato e DPS (1 per il dispositivo > DPS e 5 per il dispositivo > DPS). I dati vengono inoltre provenienti da DPS nell'hub IoT (4) e Gemelli digitali di Azure (3) tramite una funzione di Azure etichettata come &quot;Allocation&quot; (2)." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

Ecco una descrizione del flusso di processo:
1. Il dispositivo contatta l'endpoint DPS, passando le informazioni di identificazione per dimostrare la propria identità.
2. DPS convalida l'identità del dispositivo convalidando l'ID registrazione e la chiave rispetto all'elenco di registrazione e chiama una funzione [di Azure](../azure-functions/functions-overview.md) per eseguire l'allocazione.
3. La funzione di Azure crea un nuovo [dispositivo](concepts-twins-graph.md) gemello Gemelli digitali di Azure per il dispositivo. Il dispositivo gemello avrà lo stesso nome dell'ID di **registrazione del dispositivo.**
4. DPS registra il dispositivo con un hub IoT e popola lo stato del dispositivo gemello desiderato.
5. L'hub IoT restituisce le informazioni sull'ID dispositivo e le informazioni di connessione dell'hub IoT al dispositivo. Il dispositivo può ora connettersi all'hub IoT.

Le sezioni seguenti illustrano i passaggi per configurare questo flusso del dispositivo di provisioning automatico.

### <a name="create-a-device-provisioning-service"></a>Creare un servizio Device Provisioning

Quando viene effettuato il provisioning di un nuovo dispositivo usando il servizio Device Provisioning, è possibile creare un nuovo dispositivo gemello per tale dispositivo Gemelli digitali di Azure con lo stesso nome dell'ID registrazione.

Creare un'istanza del servizio Device Provisioning che verrà usata per effettuare il provisioning dei dispositivi IoT. È possibile usare le istruzioni dell'interfaccia della riga di comando di Azure riportate di seguito oppure la guida introduttiva portale di Azure: Configurare il servizio Device Provisioning in [*hub IoT*](../iot-dps/quick-setup-auto-provision.md)con il portale di Azure .

Il comando seguente dell'interfaccia della riga di comando di Azure creerà un servizio Device Provisioning. È necessario specificare un nome del servizio Device Provisioning, un gruppo di risorse e un'area. Per informazioni su quali aree supportano il servizio Device Provisioning, vedere [*Prodotti di Azure disponibili in base all'area*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
Il comando può essere eseguito [in](https://shell.azure.com)Cloud Shell o in locale se nel computer è installata l'interfaccia della riga di [comando di](/cli/azure/install-azure-cli)Azure.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Aggiungere una funzione da usare con il servizio Device Provisioning

All'interno del progetto di app per le funzioni creato nella sezione [dei](#prerequisites) prerequisiti si creerà una nuova funzione da usare con il servizio Device Provisioning. Questa funzione verrà usata dal servizio Device Provisioning in un criterio di allocazione [personalizzato](../iot-dps/how-to-use-custom-allocation-policies.md) per effettuare il provisioning di un nuovo dispositivo.

Per iniziare, aprire il progetto dell'app per le Visual Studio nel computer e seguire questa procedura.

#### <a name="step-1-add-a-new-function"></a>Passaggio 1: Aggiungere una nuova funzione 

Aggiungere una nuova funzione di tipo *HTTP-trigger* al progetto di app per le funzioni in Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png" alt-text="Screenshot della visualizzazione Visual Studio per aggiungere la funzione di Azure di tipo Http Trigger nel progetto dell'app per le funzioni." lightbox="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Passaggio 2: Compilare il codice della funzione

Aggiungere un nuovo pacchetto NuGet al [progetto: Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Potrebbe essere necessario aggiungere altri pacchetti anche al progetto, se i pacchetti usati nel codice non fanno già parte del progetto.

Nel file di codice della funzione appena creato incollare il codice seguente, rinominare la funzione in *DpsAdtAllocationFunc.cs* e salvare il file.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Passaggio 3: Pubblicare l'app per le funzioni in Azure

Pubblicare il progetto *con la funzione DpsAdtAllocationFunc.cs* nell'app per le funzioni in Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Creare la registrazione di Device Provisioning

Sarà quindi necessario creare una registrazione nel servizio Device Provisioning usando una **funzione di allocazione personalizzata.** Seguire le istruzioni per eseguire questa operazione nella sezione [*Creare la*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) registrazione dell'articolo criteri di allocazione personalizzati nella documentazione del servizio Device Provisioning.

Durante il flusso, assicurarsi di selezionare le opzioni seguenti per collegare la registrazione alla funzione appena creata.

* **Selezionare la modalità di assegnazione dei dispositivi agli hub**: Personalizzata (Usare la funzione di Azure).
* **Selezionare gli hub IoT a cui è possibile assegnare questo gruppo:** Scegliere il nome dell'hub IoT o selezionare il pulsante Collega un nuovo *hub IoT* e scegliere l'hub IoT nell'elenco a discesa.

Scegliere quindi il pulsante *Seleziona una nuova funzione per* collegare l'app per le funzioni al gruppo di registrazione. Immettere quindi i valori seguenti:

* **Sottoscrizione:** la sottoscrizione di Azure viene popolata automaticamente. Assicurarsi che sia la sottoscrizione giusta.
* **App per le** funzioni: scegliere il nome dell'app per le funzioni.
* **Funzione:** scegliere DpsAdtAllocationFunc.

Salvare i dettagli.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Screenshot della finestra dei dettagli del gruppo di registrazioni Disartie in Seleziona personalizzato (Usa funzione di Azure) e del nome dell'hub IoT nelle sezioni Selezionare la modalità di assegnazione dei dispositivi agli hub e Selezionare gli hub IoT a cui è possibile assegnare questo gruppo. Selezionare anche la sottoscrizione e l'app per le funzioni nell'elenco a discesa e assicurarsi di selezionare DpsAdtAllocationFunc." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

Dopo aver creato la registrazione, la **chiave primaria per** la registrazione verrà usata in un secondo momento per configurare il simulatore di dispositivi per questo articolo.

### <a name="set-up-the-device-simulator"></a>Configurare il simulatore di dispositivi

Questo esempio usa un simulatore di dispositivo che include il provisioning tramite il servizio Device Provisioning. Il simulatore di dispositivo si trova nell Gemelli digitali di Azure di integrazione dell'hub [IoT](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) scaricato nella [sezione](#prerequisites) Prerequisiti.

#### <a name="upload-the-model"></a>Caricare il modello

Il simulatore di dispositivo è un dispositivo di tipo termostato che usa il modello con questo ID: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Prima di creare un gemello di questo tipo per il Gemelli digitali di Azure, è necessario caricare questo modello nel dispositivo.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Per altre informazioni sui modelli, vedere [*Procedura: Gestire i modelli.*](how-to-manage-model.md#upload-models)

#### <a name="configure-and-run-the-simulator"></a>Configurare ed eseguire il simulatore

Nella finestra di comando passare all'Gemelli digitali di Azure di esempio scaricato e all'integrazione *dell'hub IoT* decompressi in precedenza e quindi nella directory *device-simulator.* Installare quindi le dipendenze per il progetto usando il comando seguente:

```cmd
npm install
```

Nella directory del simulatore di dispositivo copiare quindi il file con estensione env.template in un nuovo file denominato .env e raccogliere i valori seguenti per compilare le impostazioni:

* PROVISIONING_IDSCOPE: per ottenere questo valore, passare al servizio Device Provisioning nel [portale di Azure](https://portal.azure.com/), quindi selezionare *Panoramica* nelle opzioni di menu e cercare il campo *ID Ambito*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="Screenshot della visualizzazione portale di Azure della pagina di panoramica del provisioning dei dispositivi per copiare il valore ambito ID." lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: è possibile scegliere un ID registrazione per il dispositivo.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: si tratta della chiave primaria per la registrazione impostata in precedenza. Per ottenere nuovamente questo valore, passare al servizio device provisioning nel portale di Azure, selezionare Gestisci registrazioni, quindi selezionare il gruppo di registrazione creato in precedenza e copiare *la chiave primaria*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="Screenshot della visualizzazione portale di Azure della pagina di gestione delle registrazioni del servizio device provisioning per copiare il valore della chiave primaria di firma di accesso condiviso." lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

Usare ora i valori precedenti per aggiornare le impostazioni del file con estensione env.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Salvare e chiudere il file.

### <a name="start-running-the-device-simulator"></a>Avviare l'esecuzione del simulatore di dispositivi

Sempre nella directory *device-simulator* nella finestra di comando, avviare il simulatore di dispositivo usando il comando seguente:

```cmd
node .\adt_custom_register.js
```

Il dispositivo verrà registrato e connesso all'hub IoT e quindi inizierà a inviare messaggi.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="Screenshot del finestra di comando che mostra la registrazione del dispositivo e l'invio di messaggi" lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>Convalida

In seguito al flusso configurato in questo articolo, il dispositivo verrà registrato automaticamente in Gemelli digitali di Azure. Usare il comando [Gemelli digitali di Azure comando dell'interfaccia](how-to-use-cli.md) della riga di comando seguente per trovare il dispositivo gemello nell'Gemelli digitali di Azure creata.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Verrà visualizzato il gemello del dispositivo trovato nell'Gemelli digitali di Azure locale.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="Screenshot del finestra di comando che mostra il gemello appena creato." lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Ritirare automaticamente il dispositivo usando gli eventi del ciclo di vita dell'hub IoT

In questa sezione verranno collegati eventi del ciclo di vita dell'hub IoT Gemelli digitali di Azure ritiro automatico dei dispositivi tramite il percorso seguente. Si tratta di un estratto dell'architettura completa illustrata in [precedenza.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="Diagramma del flusso del dispositivo ritiro: estratto del diagramma dell'architettura della soluzione, con numeri che etichettano le sezioni del flusso. Il dispositivo termostato viene visualizzato senza connessioni ai servizi di Azure nel diagramma. I dati di un'azione manuale &quot;Elimina dispositivo&quot; passano attraverso l'hub IoT (1) > Hub eventi (2) > Funzioni di Azure > Gemelli digitali di Azure (3)." lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

Ecco una descrizione del flusso del processo:
1. Un processo esterno o manuale attiva l'eliminazione di un dispositivo nell'hub IoT.
2. L'hub IoT elimina il dispositivo e genera un evento [del](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) ciclo di vita del dispositivo che verrà indirizzato a un [hub eventi.](../event-hubs/event-hubs-about.md)
3. Una funzione di Azure elimina il gemello del dispositivo in Gemelli digitali di Azure.

Le sezioni seguenti illustrano i passaggi per configurare questo flusso del dispositivo di ritiro automatico.

### <a name="create-an-event-hub"></a>Creare un hub eventi

Si creerà quindi un hub eventi di Azure [per](../event-hubs/event-hubs-about.md) ricevere gli eventi del ciclo di vita dell'hub IoT. 

Seguire la procedura descritta nell'avvio [*rapido Creare un hub*](../event-hubs/event-hubs-create.md) eventi. Assegnare al ciclo di vita *dell'hub eventi il nomeevents.* Questo nome dell'hub eventi verrà utilizzato quando si configura la route dell'hub IoT e una funzione di Azure nelle sezioni successive.

Lo screenshot seguente illustra la creazione dell'hub eventi.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Screenshot della finestra portale di Azure eventi per creare un hub eventi con il nome lifecycleevents." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Creare criteri di firma di accesso condiviso per l'hub eventi

Sarà quindi necessario creare un criterio di firma di accesso condiviso [per](../event-hubs/authorize-access-shared-access-signature.md) configurare l'hub eventi con l'app per le funzioni.
A tale scopo, seguire questa procedura:
1. Passare all'hub eventi appena creato nel portale di Azure **e** selezionare Criteri di accesso condiviso nelle opzioni di menu a sinistra.
2. Selezionare **Aggiungi**. Nella finestra *Aggiungi criteri di firma di* accesso condiviso visualizzata immettere un nome di criterio a scelta e selezionare la casella di controllo *Ascolto.*
3. Selezionare **Crea**.
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Screenshot dell'portale di Azure per aggiungere un criterio di firma di accesso condiviso dell'hub eventi." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Configurare l'hub eventi con l'app per le funzioni

Configurare quindi l'app per le funzioni di Azure configurata nella [sezione dei](#prerequisites) prerequisiti per l'utilizzo con il nuovo hub eventi. A tale scopo, impostare una variabile di ambiente all'interno dell'app per le funzioni con la stringa di connessione dell'hub eventi.

1. Aprire il criterio appena creato e copiare il **valore stringa di connessione-chiave primaria.**

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Screenshot del portale di Azure copiare la stringa di connessione-chiave primaria." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. Aggiungere la stringa di connessione come variabile nelle impostazioni dell'app per le funzioni con il comando seguente dell'interfaccia della riga di comando di Azure. Il comando può essere eseguito [in](https://shell.azure.com)Cloud Shell o in locale se nel computer è installata l'interfaccia della riga di [comando di](/cli/azure/install-azure-cli)Azure.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Aggiungere una funzione per ritirare gli eventi del ciclo di vita dell'hub IoT

All'interno del progetto di app per le funzioni creato nella sezione [dei](#prerequisites) prerequisiti, si creerà una nuova funzione per ritirare un dispositivo esistente usando gli eventi del ciclo di vita dell'hub IoT.

Per altre informazioni sugli eventi del ciclo di vita, vedere [*Eventi non di telemetria dell'hub IoT.*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) Per altre informazioni sull'uso di Hub eventi con le funzioni di Azure, vedere Hub eventi di Azure [*trigger per Funzioni di Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Per iniziare, aprire il progetto dell'app per le Visual Studio nel computer e seguire questa procedura.

#### <a name="step-1-add-a-new-function"></a>Passaggio 1: Aggiungere una nuova funzione
     
Aggiungere una nuova funzione di tipo *Trigger hub* eventi al progetto di app per le funzioni in Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png" alt-text="Screenshot della finestra Visual Studio per aggiungere una funzione di Azure di tipo Trigger dell'hub eventi nel progetto dell'app per le funzioni." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Passaggio 2: Compilare il codice della funzione

Nel file di codice della funzione appena creato incollare il codice seguente, rinominare la funzione in `DeleteDeviceInTwinFunc.cs` e salvare il file.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Passaggio 3: Pubblicare l'app per le funzioni in Azure

Pubblicare il progetto *con la funzione DeleteDeviceInTwinFunc.cs* nell'app per le funzioni in Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Creare una route dell'hub IoT per gli eventi del ciclo di vita

A questo punto si configura una route dell'hub IoT per instradare gli eventi del ciclo di vita del dispositivo. In questo caso, si ascolteranno in modo specifico gli eventi di eliminazione del dispositivo, identificati da `if (opType == "deleteDeviceIdentity")` . In questo modo verrà attivata l'eliminazione dell'elemento gemello digitale, finalizzando il ritiro di un dispositivo e del relativo dispositivo gemello digitale.

In primo luogo, è necessario creare un endpoint dell'hub eventi nell'hub IoT. Si aggiungerà quindi una route nell'hub IoT per inviare gli eventi del ciclo di vita a questo endpoint dell'hub eventi.
Seguire questa procedura per creare un endpoint dell'hub eventi:

1. Nel [portale di Azure](https://portal.azure.com/)passare all'hub IoT creato nella [](#prerequisites) sezione dei prerequisiti e selezionare **Routing messaggi** nelle opzioni di menu a sinistra.
2. Selezionare la scheda **Endpoint personalizzati**.
3. Selezionare **+ Aggiungi e** scegliere Hub eventi per **aggiungere** un endpoint di tipo hub eventi.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Screenshot della finestra Visual Studio per aggiungere un endpoint personalizzato dell'hub eventi." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. Nella finestra *Aggiungi un endpoint dell'hub* eventi visualizzata scegliere i valori seguenti:
    * **Nome endpoint:** scegliere un nome di endpoint.
    * **Spazio dei nomi dell'hub** eventi: selezionare lo spazio dei nomi dell'hub eventi dall'elenco a discesa.
    * **Istanza dell'hub** eventi: scegliere il nome dell'hub eventi creato nel passaggio precedente.
5. Selezionare **Crea**. Mantenere aperta questa finestra per aggiungere una route nel passaggio successivo.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Screenshot della finestra Visual Studio per aggiungere un endpoint dell'hub eventi." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

Successivamente, si aggiungerà una route che si connette all'endpoint creato nel passaggio precedente, con una query di routing che invia gli eventi di eliminazione. Per creare una route, seguire questa procedura:

1. Passare alla scheda *Route* e selezionare **Aggiungi per** aggiungere una route.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Screenshot della finestra Visual Studio per aggiungere una route per l'invio di eventi." lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. Nella pagina *Aggiungi una route* visualizzata scegliere i valori seguenti:

   * **Nome:** scegliere un nome per la route. 
   * **Endpoint:** scegliere l'endpoint di Hub eventi creato in precedenza dall'elenco a discesa.
   * **Origine dati:** scegliere Eventi *del ciclo di vita del dispositivo.*
   * **Query di routing:** immettere `opType='deleteDeviceIdentity'` . Questa query limita gli eventi del ciclo di vita del dispositivo per inviare solo gli eventi di eliminazione.

3. Selezionare **Salva**.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Screenshot della finestra portale di Azure per aggiungere una route per inviare eventi del ciclo di vita." lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

Dopo aver passato questo flusso, tutto è impostato per ritirare i dispositivi end-to-end.

### <a name="validate"></a>Convalida

Per attivare il processo di ritiro, è necessario eliminare manualmente il dispositivo dall'hub IoT.

È possibile eseguire questa operazione con un comando dell'interfaccia della riga di comando di [Azure](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) o nel portale di Azure. Seguire questa procedura per eliminare il dispositivo nell'portale di Azure:

1. Passare all'hub IoT e scegliere **Dispositivi IoT** nelle opzioni di menu a sinistra. 
2. Verrà visualizzato un dispositivo con l'ID di registrazione del dispositivo scelto nella [prima metà di questo articolo.](#auto-provision-device-using-device-provisioning-service) In alternativa, è possibile scegliere qualsiasi altro dispositivo da eliminare, purché abbia un dispositivo gemello in Gemelli digitali di Azure in modo da verificare che il dispositivo gemello sia eliminato automaticamente dopo l'eliminazione del dispositivo.
3. Selezionare il dispositivo e scegliere **Elimina**.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="Screenshot dell'portale di Azure per eliminare il dispositivo gemello dai dispositivi IoT." lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Potrebbero essere necessarie alcuni minuti per visualizzare le modifiche riflesse in Gemelli digitali di Azure.

Usare il comando [Gemelli digitali di Azure comando dell'interfaccia](how-to-use-cli.md) della riga di comando seguente per verificare che il dispositivo gemello nell'Gemelli digitali di Azure sia stato eliminato.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Si dovrebbe vedere che il dispositivo gemello non è più presente nell'Gemelli digitali di Azure istanza.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="Screenshot del finestra di comando che mostra il gemello non trovato." lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, seguire questa procedura per eliminarle.

Usando l'Azure Cloud Shell o l'interfaccia della riga di comando di Azure locale, è possibile eliminare tutte le risorse di Azure in un gruppo di risorse con [il comando az group delete.](/cli/azure/group#az_group_delete) In questo modo il gruppo di risorse viene rimosso. istanza Gemelli digitali di Azure; l'hub IoT e la registrazione del dispositivo hub; l'argomento griglia di eventi e le sottoscrizioni associate; lo spazio dei nomi dell'hub eventi e Funzioni di Azure app, incluse le risorse associate, ad esempio l'archiviazione.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Eliminare quindi la cartella di esempio del progetto scaricata dal computer locale.

## <a name="next-steps"></a>Passaggi successivi

I gemelli digitali creati per i dispositivi vengono archiviati come gerarchia semplice in Gemelli digitali di Azure, ma possono essere arricchiti con informazioni sul modello e una gerarchia multi-livello per l'organizzazione. Per altre informazioni su questo concetto, leggere:

* [*Concetti: Gemelli digitali e grafo dei gemelli*](concepts-twins-graph.md)

Per altre informazioni sull'uso delle richieste HTTP con Funzioni di Azure, vedere:

* [*Trigger di richiesta HTTP di Azure per Funzioni di Azure*](../azure-functions/functions-bindings-http-webhook-trigger.md)

È possibile scrivere logica personalizzata per fornire automaticamente queste informazioni usando i dati del modello e del grafo già archiviati in Gemelli digitali di Azure. Per altre informazioni sulla gestione, l'aggiornamento e il recupero di informazioni dal grafo dei gemelli, vedere gli argomenti seguenti:

* [*Procedura: Gestire un gemello digitale*](how-to-manage-twin.md)
* [*Procedura: Eseguire una query sul grafo gemello*](how-to-query-graph.md)