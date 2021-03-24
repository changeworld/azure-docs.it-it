---
title: Gestione automatica dei dispositivi con il servizio Device provisioning
titleSuffix: Azure Digital Twins
description: Vedere come configurare un processo automatizzato per eseguire il provisioning e il ritiro dei dispositivi Internet in Azure Digital gemelli usando il servizio Device provisioning (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a571d92dd9663c7d2d0a576b59e5cd2b3352cb76
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951015"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Gestire automaticamente i dispositivi nei dispositivi gemelli digitali di Azure usando il servizio Device provisioning (DPS)

Questo articolo illustra come integrare i dispositivi gemelli digitali di Azure con il [servizio Device provisioning (DPS)](../iot-dps/about-iot-dps.md).

La soluzione descritta in questo articolo consente di automatizzare il processo di **_provisioning_** e **_ritiro_** dei dispositivi dell'hub Internet in Azure Digital Twins, usando il servizio Device provisioning. 

Per altre informazioni sulle fasi di _provisioning_ e _ritiro_ e per comprendere meglio il set di fasi di gestione dei dispositivi generali comuni a tutti i progetti dell'intero aziendale, vedere la [ *sezione ciclo*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) di vita dei dispositivi della documentazione di gestione dei dispositivi dell'hub.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter configurare il provisioning, è necessario configurare quanto segue:
* un' **istanza di dispositivi gemelli digitali di Azure**. Seguire le istruzioni in [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md) per creare un'istanza di dispositivi gemelli digitali di Azure. Raccogliere il **_nome host_** dell'istanza nel portale di Azure ([istruzioni](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* un **Hub** Internet. Per istruzioni, vedere la sezione *creare un hub* per gli Internet in questa [Guida introduttiva sull'hub](../iot-hub/quickstart-send-telemetry-cli.md).
* [**funzione di Azure**](../azure-functions/functions-overview.md) che aggiorna le informazioni sui dispositivi gemelli digitali in base ai dati dell'hub Internet. Per creare questa funzione di Azure, seguire le istruzioni in [*procedura: inserire i dati dell'hub*](how-to-ingest-iot-hub-data.md) Internet. Raccogliere il **_nome_** della funzione da usare in questo articolo.

Questo esempio usa anche un **simulatore di dispositivi** che include il provisioning usando il servizio Device provisioning. Il simulatore di dispositivi è disponibile qui: [esempio di integrazione di Azure Digital gemells e dell'hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)Internet. Ottenere il progetto di esempio nel computer passando al collegamento di esempio e selezionando il pulsante *Scarica zip* sotto il titolo. Decomprimere la cartella scaricata.

È necessario [**Node.js**](https://nodejs.org/download) installato nel computer. Il simulatore del dispositivo si basa su **Node.js**, versione 10.0. x o successiva.

## <a name="solution-architecture"></a>Architettura della soluzione

L'immagine seguente illustra l'architettura di questa soluzione usando i dispositivi gemelli digitali di Azure con il servizio Device provisioning. Mostra sia il provisioning del dispositivo che il flusso di ritiro.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Diagramma del dispositivo e di diversi servizi di Azure in uno scenario end-to-end. I dati passano da un dispositivo termostato a un DP e viceversa. I dati passano anche da DPS nell'hub Internet e ai gemelli digitali di Azure tramite una funzione di Azure con etichetta &quot;Allocation&quot;. I dati provenienti da un'azione ' Elimina dispositivo ' manuale passano attraverso l'hub degli eventi > Hub eventi > funzioni di Azure > i dispositivi gemelli digitali di Azure." lightbox="media/how-to-provision-using-dps/flows.png":::

Questo articolo è diviso in due sezioni:
* [*Eseguire il provisioning automatico del dispositivo con il servizio Device provisioning*](#auto-provision-device-using-device-provisioning-service)
* [*Ritirare automaticamente il dispositivo usando gli eventi del ciclo di vita dell'hub*](#auto-retire-device-using-iot-hub-lifecycle-events)

Per una spiegazione più approfondita di ogni passaggio dell'architettura, vedere le singole sezioni più avanti in questo articolo.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Eseguire il provisioning automatico del dispositivo con il servizio Device provisioning

In questa sezione si collegherà il servizio Device provisioning a dispositivi digitali gemelli di Azure per eseguire il provisioning automatico dei dispositivi tramite il percorso riportato di seguito. Si tratta di un estratto dall'architettura completa illustrata in [precedenza](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Diagramma del flusso di provisioning: un estratto del diagramma dell'architettura della soluzione, con i numeri che identificano le sezioni del flusso. I dati passano tra un dispositivo termostato e un DPS (1 per il dispositivo > DPS e 5 per il dispositivo DPS >). I dati passano anche da DPS nell'hub Internet (4) e nei dispositivi gemelli digitali di Azure (3) tramite una funzione di Azure con etichetta &quot;Allocation&quot; (2)." lightbox="media/how-to-provision-using-dps/provision.png":::

Ecco una descrizione del flusso del processo:
1. Il dispositivo Contatta l'endpoint DPS, passando le informazioni di identificazione per dimostrare la propria identità.
2. DPS convalida l'identità del dispositivo convalidando l'ID e la chiave di registrazione nell'elenco di registrazione e chiama una [funzione di Azure](../azure-functions/functions-overview.md) per eseguire l'allocazione.
3. La funzione di Azure crea un nuovo [gemello](concepts-twins-graph.md) nei dispositivi gemelli digitali di Azure per il dispositivo. Il gemello avrà lo stesso nome dell' **ID di registrazione** del dispositivo.
4. DPS registra il dispositivo con un hub Internet e popola lo stato del dispositivo gemello desiderato.
5. L'hub Internet delle cose restituisce al dispositivo le informazioni relative all'ID del dispositivo e alle informazioni di connessione dell'hub. Il dispositivo può ora connettersi all'hub Internet delle cose.

Le sezioni seguenti illustrano i passaggi per configurare il flusso del dispositivo con provisioning automatico.

### <a name="create-a-device-provisioning-service"></a>Creare un servizio Device Provisioning

Quando viene effettuato il provisioning di un nuovo dispositivo usando il servizio Device provisioning, è possibile creare un nuovo dispositivo gemello in Azure Digital gemelli con lo stesso nome dell'ID di registrazione.

Creare un'istanza del servizio Device provisioning, che verrà usata per eseguire il provisioning dei dispositivi. È possibile usare le istruzioni dell'interfaccia della riga di comando di Azure riportate di seguito oppure usare la portale di Azure: [*Guida introduttiva: configurare il servizio Device provisioning in hub Internet con l'portale di Azure*](../iot-dps/quick-setup-auto-provision.md).

Il comando dell'interfaccia della riga di comando di Azure seguente creerà un servizio Device provisioning. È necessario specificare un nome del servizio Device provisioning, un gruppo di risorse e un'area. Per visualizzare le aree che supportano il servizio Device provisioning, visitare i [*prodotti Azure disponibili in base all'area*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
Il comando può essere eseguito in [cloud Shell](https://shell.azure.com)o localmente se nel [computer è installata](/cli/azure/install-azure-cli)l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Aggiungere una funzione da usare con il servizio Device provisioning

All'interno del progetto di app per le funzioni creato nella sezione [prerequisiti](#prerequisites) verrà creata una nuova funzione da usare con il servizio Device provisioning. Questa funzione verrà usata dal servizio Device provisioning in un criterio di [allocazione personalizzato](../iot-dps/how-to-use-custom-allocation-policies.md) per eseguire il provisioning di un nuovo dispositivo.

Per iniziare, aprire il progetto di app per le funzioni in Visual Studio nel computer e seguire questa procedura.

#### <a name="step-1-add-a-new-function"></a>Passaggio 1: aggiungere una nuova funzione 

Aggiungere una nuova funzione di tipo *http-trigger* al progetto di app per le funzioni in Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="Screenshot della visualizzazione di Visual Studio per aggiungere la funzione di Azure di tipo trigger http nel progetto di app per le funzioni." lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Passaggio 2: compilare il codice della funzione

Aggiungere un nuovo pacchetto NuGet al progetto: [Microsoft. Azure. Devices. provisioning. Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Potrebbe essere necessario aggiungere altri pacchetti anche al progetto, se i pacchetti usati nel codice non fanno già parte del progetto.

Nel file di codice della funzione appena creato incollare il codice seguente, rinominare la funzione in *DpsAdtAllocationFunc. cs* e salvare il file.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Passaggio 3: pubblicare l'app per le funzioni in Azure

Pubblicare il progetto con la funzione *DpsAdtAllocationFunc. cs* nell'app per le funzioni in Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Creare la registrazione del provisioning dei dispositivi

Successivamente, sarà necessario creare una registrazione nel servizio Device provisioning usando una **funzione di allocazione personalizzata**. Seguire le istruzioni per eseguire questa operazione nella sezione [*creare la registrazione*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) dell'articolo criteri di allocazione personalizzati nella documentazione del servizio Device provisioning.

Durante l'attraversamento di tale flusso, assicurarsi di selezionare le opzioni seguenti per collegare la registrazione alla funzione appena creata.

* **Selezionare la modalità di assegnazione dei dispositivi a hub**: personalizzata (usare la funzione di Azure).
* **Selezionare gli hub Internet che possono essere assegnati a questo gruppo:** Scegliere il nome dell'hub Internet delle cose oppure selezionare il pulsante *collega un nuovo hub* Internet e scegliere l'hub delle cose dall'elenco a discesa.

Scegliere quindi il pulsante *Seleziona una nuova funzione* per collegare l'app per le funzioni al gruppo di registrazione. Quindi, immettere i valori seguenti:

* **Sottoscrizione**: la sottoscrizione di Azure viene popolata automaticamente. Assicurarsi che sia la sottoscrizione corretta.
* **App per le funzioni**: scegliere il nome dell'app per le funzioni.
* **Funzione**: scegliere DpsAdtAllocationFunc.

Salvare i dettagli.                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Screenshot della finestra dei dettagli del gruppo di registrazione doganale per selezionare personalizzato (usare la funzione di Azure) e il nome dell'hub Internet nelle sezioni selezionare come assegnare i dispositivi agli hub e selezionare gli hub Internet che possono essere assegnati a questo gruppo. Selezionare anche la sottoscrizione, l'app per le funzioni nell'elenco a discesa e assicurarsi di selezionare DpsAdtAllocationFunc." lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

Dopo aver creato la registrazione, la **chiave primaria** per la registrazione verrà usata in un secondo momento per configurare il simulatore di dispositivi per questo articolo.

### <a name="set-up-the-device-simulator"></a>Configurare il simulatore di dispositivi

Questo esempio usa un simulatore di dispositivi che include il provisioning tramite il servizio Device provisioning. Il simulatore di dispositivi è disponibile qui: [esempio di integrazione di Azure Digital gemells e dell'hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)Internet. Se l'esempio non è ancora stato scaricato, scaricarlo ora passando al collegamento di esempio e selezionando il pulsante *Scarica zip* sotto il titolo. Decomprimere la cartella scaricata.

#### <a name="upload-the-model"></a>Caricare il modello

Il simulatore del dispositivo è un dispositivo di tipo termostato che usa il modello con questo ID: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Per poter creare un dispositivo gemello di questo tipo per il dispositivo, è necessario caricare il modello in Azure Digital gemelli.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Per ulteriori informazioni sui modelli, vedere [*procedura: gestire i modelli*](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Configurare ed eseguire il simulatore

Nella finestra di comando passare all'esempio scaricato di *Azure Digital gemelli e all'integrazione dell'hub* Internet che è stato decompresso in precedenza e quindi nella directory *Device-Simulator* . Installare quindi le dipendenze per il progetto usando il comando seguente:

```cmd
npm install
```

Successivamente, nella directory del simulatore del dispositivo copiare il file. env. template in un nuovo file denominato. env e raccogliere i valori seguenti per inserire le impostazioni:

* PROVISIONING_IDSCOPE: per ottenere questo valore, passare al servizio Device provisioning nel [portale di Azure](https://portal.azure.com/), quindi selezionare *Panoramica* nelle opzioni di menu e cercare l' *ambito ID* campo.

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="Screenshot della visualizzazione portale di Azure della pagina Panoramica del provisioning dei dispositivi per copiare il valore di ambito ID." lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: è possibile scegliere un ID di registrazione per il dispositivo.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: questa è la chiave primaria per la registrazione configurata in precedenza. Per ottenere nuovamente questo valore, passare al servizio Device provisioning nel portale di Azure, selezionare *Gestisci registrazioni*, quindi selezionare il gruppo di registrazioni creato in precedenza e copiare la *chiave primaria*.

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="Screenshot della visualizzazione portale di Azure della pagina Gestisci registrazioni del servizio Device provisioning per copiare il valore della chiave primaria SAS." lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

A questo punto, usare i valori precedenti per aggiornare le impostazioni del file. env.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Salvare e chiudere il file.

### <a name="start-running-the-device-simulator"></a>Avviare l'esecuzione del simulatore del dispositivo

Sempre nella directory *Device-Simulator* nella finestra di comando, avviare il simulatore di dispositivi usando il comando seguente:

```cmd
node .\adt_custom_register.js
```

Si noterà che il dispositivo è registrato e connesso all'hub Internet e quindi inizia a inviare messaggi.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Screenshot della finestra di comando che mostra la registrazione del dispositivo e l'invio di messaggi" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>Convalida

In seguito al flusso configurato in questo articolo, il dispositivo verrà registrato automaticamente nei dispositivi gemelli digitali di Azure. Usare il comando dell'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md) seguente per trovare il dispositivo gemello nell'istanza di Azure Digital Twins creata.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Si noterà che il dispositivo gemello si trova nell'istanza di Azure Digital gemelli.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Screenshot del finestra di comando che mostra i dispositivi gemelli appena creati." lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Ritirare automaticamente il dispositivo usando gli eventi del ciclo di vita dell'hub

In questa sezione verranno collegati gli eventi del ciclo di vita dell'hub per i dispositivi gemelli di Azure per disattivare automaticamente i dispositivi tramite il percorso riportato di seguito. Si tratta di un estratto dall'architettura completa illustrata in [precedenza](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Diagramma del flusso del dispositivo di ritiro: un estratto del diagramma dell'architettura della soluzione, con i numeri che descrivono le sezioni del flusso. Il dispositivo termostato viene visualizzato senza connessioni ai servizi di Azure nel diagramma. I dati provenienti da un'azione ' Elimina dispositivo ' manuale passano attraverso l'hub Internet (1) > Hub eventi (2) > funzioni di Azure > i dispositivi gemelli digitali di Azure (3)." lightbox="media/how-to-provision-using-dps/retire.png":::

Ecco una descrizione del flusso del processo:
1. Un processo esterno o manuale attiva l'eliminazione di un dispositivo nell'hub.
2. L'hub Internet delle cose Elimina il dispositivo e genera un evento del ciclo di vita del [dispositivo](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) che verrà indirizzato a un [Hub eventi](../event-hubs/event-hubs-about.md).
3. Una funzione di Azure Elimina il gemello del dispositivo nei dispositivi gemelli digitali di Azure.

Le sezioni seguenti illustrano i passaggi per configurare questo flusso di dispositivo di ritiro automatico.

### <a name="create-an-event-hub"></a>Creare un hub eventi

Successivamente, si creerà un [Hub eventi](../event-hubs/event-hubs-about.md) di Azure per ricevere gli eventi del ciclo di vita dell'hub. 

Seguire i passaggi descritti nella Guida introduttiva per [*creare un hub eventi*](../event-hubs/event-hubs-create.md) . Assegnare un nome all'hub eventi *lifecycleevents*. Questo nome dell'hub eventi verrà usato quando si configurano le route dell'hub Internet e una funzione di Azure nelle sezioni successive.

La schermata seguente illustra la creazione dell'hub eventi.
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="Screenshot della finestra di portale di Azure per creare un hub eventi con il nome lifecycleevents." lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Creare criteri SAS per l'hub eventi

Successivamente, è necessario creare un criterio di [firma di accesso condiviso (SAS)](../event-hubs/authorize-access-shared-access-signature.md) per configurare l'hub eventi con l'app per le funzioni.
A tale scopo, seguire questa procedura:
1. Passare all'hub eventi appena creato nella portale di Azure e selezionare **criteri di accesso condiviso** nelle opzioni di menu a sinistra.
2. Selezionare **Aggiungi**. Nella finestra *Aggiungi criteri* di firma di accesso condiviso visualizzata immettere il nome di un criterio desiderato e selezionare la casella di controllo *Ascolta* .
3. Selezionare **Crea**.
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="Screenshot del portale di Azure per aggiungere un criterio di firma di accesso condiviso dell'hub eventi." lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Configurare l'hub eventi con l'app per le funzioni

Configurare quindi l'app per le funzioni di Azure configurata nella sezione [prerequisiti](#prerequisites) per usare il nuovo hub eventi. Questa operazione viene eseguita impostando una variabile di ambiente all'interno dell'app per le funzioni con la stringa di connessione dell'hub eventi.

1. Aprire il criterio appena creato e copiare il valore **stringa di connessione-chiave primaria** .

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="Screenshot del portale di Azure di copiare la stringa di connessione-chiave primaria." lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. Aggiungere la stringa di connessione come variabile nelle impostazioni dell'app per le funzioni con il comando seguente dell'interfaccia della riga di comando di Azure. Il comando può essere eseguito in [cloud Shell](https://shell.azure.com)o localmente se nel [computer è installata](/cli/azure/install-azure-cli)l'interfaccia della riga di comando di Azure.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Aggiungere una funzione da ritirare con gli eventi del ciclo di vita dell'hub

All'interno del progetto di app per le funzioni creato nella sezione [prerequisiti](#prerequisites) , si creerà una nuova funzione per ritirare un dispositivo esistente usando gli eventi del ciclo di vita dell'hub Internet.

Per altre informazioni sugli eventi del ciclo di vita, vedere [*eventi di non telemetria dell'hub*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)Internet. Per altre informazioni sull'uso di hub eventi con funzioni di Azure, vedere [*trigger di hub eventi di Azure per funzioni di Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Per iniziare, aprire il progetto di app per le funzioni in Visual Studio nel computer e seguire questa procedura.

#### <a name="step-1-add-a-new-function"></a>Passaggio 1: aggiungere una nuova funzione
     
Aggiungere una nuova funzione di tipo *trigger dell'hub eventi* al progetto di app per le funzioni in Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="Screenshot della finestra di Visual Studio per aggiungere una funzione di Azure di tipo trigger dell'hub eventi nel progetto di app per le funzioni." lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Passaggio 2: compilare il codice della funzione

Nel file di codice della funzione appena creato incollare il codice seguente, rinominare la funzione in `DeleteDeviceInTwinFunc.cs` e salvare il file.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Passaggio 3: pubblicare l'app per le funzioni in Azure

Pubblicare il progetto con la funzione *DeleteDeviceInTwinFunc. cs* nell'app per le funzioni in Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Creare una route dell'hub Internet per gli eventi del ciclo di vita

A questo punto si configurerà una route dell'hub Internet per indirizzare gli eventi del ciclo di vita del dispositivo. In questo caso, si ascolteranno in modo specifico gli eventi Delete del dispositivo, identificati da `if (opType == "deleteDeviceIdentity")` . Questa operazione attiverà l'eliminazione dell'elemento del dispositivo gemello digitale, finalizzando il ritiro di un dispositivo e il suo gemello digitale.

Prima di tutto, è necessario creare un endpoint dell'hub eventi nell'hub Internet. Si aggiungerà quindi una route nell'hub Internet per inviare gli eventi del ciclo di vita a questo endpoint dell'hub eventi.
Per creare un endpoint di hub eventi, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com/)passare all'hub di Internet delle cose creato nella sezione [prerequisiti](#prerequisites) e selezionare **routing messaggi** nelle opzioni di menu a sinistra.
2. Selezionare la scheda **Endpoint personalizzati**.
3. Selezionare **+ Aggiungi** e scegliere **Hub eventi** per aggiungere un endpoint del tipo di hub eventi.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="Screenshot della finestra di Visual Studio per aggiungere un endpoint personalizzato di hub eventi." lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. Nella finestra *aggiungere un endpoint dell'hub eventi* che si apre, scegliere i valori seguenti:
    * **Nome endpoint**: scegliere un nome di endpoint.
    * **Spazio dei nomi dell'hub eventi**: selezionare lo spazio dei nomi dell'hub eventi dall'elenco a discesa.
    * **Istanza di hub eventi**: scegliere il nome dell'hub eventi creato nel passaggio precedente.
5. Selezionare **Crea**. Lasciare aperta questa finestra per aggiungere una route nel passaggio successivo.

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="Screenshot della finestra di Visual Studio per aggiungere un endpoint di hub eventi." lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

Si aggiungerà quindi una route che si connette all'endpoint creato nel passaggio precedente, con una query di routing che invia gli eventi Delete. Per creare una route, attenersi alla procedura seguente:

1. Passare alla scheda *Route* e selezionare **Aggiungi** per aggiungere una route.

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="Screenshot della finestra di Visual Studio per aggiungere una route per l'invio di eventi." lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. Nella pagina *Aggiungi una route* visualizzata scegliere i valori seguenti:

   * **Nome**: scegliere un nome per la route. 
   * **Endpoint**: selezionare l'endpoint di hub eventi creato in precedenza dall'elenco a discesa.
   * **Origine dati**: scegliere *gli eventi del ciclo* di vita del dispositivo.
   * **Query di routing**: immettere `opType='deleteDeviceIdentity'` . Questa query limita gli eventi del ciclo di vita del dispositivo per inviare solo gli eventi Delete.

3. Selezionare **Salva**.

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Screenshot della finestra di portale di Azure per aggiungere una route per inviare eventi del ciclo di vita." lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

Una volta attraversato questo flusso, tutti gli elementi vengono impostati per ritirare i dispositivi end-to-end.

### <a name="validate"></a>Convalida

Per attivare il processo di ritiro, è necessario eliminare manualmente il dispositivo dall'hub.

È possibile eseguire questa operazione con un [comando dell'interfaccia della riga di comando di Azure](/cli/azure/ext/azure-iot/iot/hub/module-identity#ext_azure_iot_az_iot_hub_module_identity_delete) o nel portale di Azure. Attenersi alla procedura seguente per eliminare il dispositivo nel portale di Azure:

1. Passare all'hub Internet e scegliere **dispositivi** Internet nelle opzioni di menu a sinistra. 
2. Nella [prima metà di questo articolo](#auto-provision-device-using-device-provisioning-service)verrà visualizzato un dispositivo con l'ID di registrazione del dispositivo scelto. In alternativa, è possibile scegliere qualsiasi altro dispositivo da eliminare, purché abbia un gemello in Azure Digital gemelli, in modo da poter verificare che il dispositivo gemello venga eliminato automaticamente dopo l'eliminazione del dispositivo.
3. Selezionare il dispositivo e scegliere **Elimina**.

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="Screenshot del portale di Azure eliminare i dispositivi gemelli dai dispositivi Internet delle cose." lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Potrebbero essere necessari alcuni minuti per visualizzare le modifiche riflesse nei dispositivi gemelli digitali di Azure.

Usare il comando dell'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md) seguente per verificare che il dispositivo gemello nell'istanza di Azure Digital Twins sia stato eliminato.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Si noterà che il gemello del dispositivo non è più disponibile nell'istanza di Azure Digital Twins.

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Screenshot del finestra di comando che mostra il gemello non trovato." lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, attenersi alla procedura seguente per eliminarle.

Usando l'interfaccia della riga di comando di Azure Azure Cloud Shell o locale è possibile eliminare tutte le risorse di Azure in un gruppo di risorse con il comando [AZ Group Delete](/cli/azure/group#az-group-delete) . Il gruppo di risorse verrà rimosso. istanza di Azure Digital Twins; l'hub Internet delle cose e la registrazione del dispositivo hub; argomento di griglia di eventi e sottoscrizioni associate; lo spazio dei nomi di hub eventi e entrambe le app di funzioni di Azure, incluse le risorse associate come l'archiviazione.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Eliminare quindi la cartella di esempio del progetto scaricata dal computer locale.

## <a name="next-steps"></a>Passaggi successivi

I dispositivi gemelli digitali creati per i dispositivi vengono archiviati come una gerarchia semplice nei dispositivi gemelli digitali di Azure, ma possono essere arricchiti con le informazioni sul modello e una gerarchia a più livelli per l'organizzazione. Per ulteriori informazioni su questo concetto, vedere:

* [*Concetti: i dispositivi gemelli digitali e i grafici gemelli*](concepts-twins-graph.md)

Per ulteriori informazioni sull'utilizzo di richieste HTTP con funzioni di Azure, vedere:

* [*Trigger di richiesta HTTP di Azure per funzioni di Azure*](../azure-functions/functions-bindings-http-webhook-trigger.md)

È possibile scrivere logica personalizzata per fornire automaticamente queste informazioni usando i dati del modello e del grafo già archiviati nei dispositivi gemelli digitali di Azure. Per altre informazioni sulla gestione, l'aggiornamento e il recupero delle informazioni dal grafo gemello, vedere gli argomenti seguenti:

* [*Procedura: gestire un dispositivo gemello digitale*](how-to-manage-twin.md)
* [*Procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md)