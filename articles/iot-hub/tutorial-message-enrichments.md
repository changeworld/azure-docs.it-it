---
title: 'Esercitazione: Usare hub IoT di Azure arricchimenti dei messaggi'
description: Esercitazione che illustra come usare gli arricchimenti dei messaggi per hub IoT di Azure messaggi
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 0d6c90120d050b6896161f50332faf447c3ed67b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788854"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Esercitazione: Usare hub IoT di Azure arricchimenti dei messaggi

*L'arricchimento* dei messaggi descrive la  possibilità hub IoT di Azure di timbrare i messaggi con informazioni aggiuntive prima che i messaggi siano inviati all'endpoint designato. Un motivo per usare gli arricchimenti dei messaggi è includere dati che possono essere usati per semplificare l'elaborazione downstream. Ad esempio, l'arricchimento dei messaggi di telemetria del dispositivo con un tag dispositivo gemello può ridurre il carico sui clienti per effettuare chiamate API del dispositivo gemello per queste informazioni. Per altre informazioni, vedere [Panoramica degli arricchimenti dei messaggi.](iot-hub-message-enrichments-overview.md)

In questa esercitazione vengono visualizzati due modi per creare e configurare le risorse necessarie per testare gli arricchimenti dei messaggi per un hub IoT. Le risorse includono un account di archiviazione con due contenitori di archiviazione. Un contenitore contiene i messaggi arricchiti e un altro contenitore contiene i messaggi originali. È incluso anche un hub IoT per ricevere i messaggi e instradare i messaggi al contenitore di archiviazione appropriato a seconda che siano arricchiti o meno.

* Il primo metodo è usare l'interfaccia della riga di comando di Azure per creare le risorse e configurare il routing dei messaggi. Gli arricchimenti vengono quindi definiti manualmente usando l'portale di Azure [.](https://portal.azure.com)

* Il secondo metodo è usare un modello Azure Resource Manager per  creare sia le risorse che le configurazioni per il routing dei messaggi e gli arricchimenti dei messaggi.

Al termine delle configurazioni per il routing dei messaggi e gli arricchimenti dei messaggi, si usa un'applicazione per inviare messaggi all'hub IoT. L'hub li instrada quindi a entrambi i contenitori di archiviazione. Vengono arricchiti solo i messaggi inviati all'endpoint per il **contenitore** di archiviazione arricchito.

Ecco le attività da eseguire per completare questa esercitazione:

**Usare gli arricchimenti dei messaggi dell'hub IoT**
> [!div class="checklist"]
> * Primo metodo: creare risorse e configurare il routing dei messaggi usando l'interfaccia della riga di comando di Azure. Configurare manualmente gli arricchimenti dei messaggi usando il [portale di Azure](https://portal.azure.com).
> * Secondo metodo: creare risorse e configurare il routing dei messaggi e gli arricchimenti dei messaggi usando Resource Manager modello. 
> * Eseguire un'app che simula un dispositivo IoT che invia messaggi all'hub.
> * Visualizzare i risultati e verificare che gli arricchimenti dei messaggi funzionino come previsto.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Installare [Visual Studio](https://www.visualstudio.com/).

- Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questa esercitazione usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Recuperare il repository di esempi C# di IoT

Scaricare gli [esempi C# di IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) da GitHub e decomprimerli. In questo repository sono presenti diverse applicazioni, script e Resource Manager modelli. Quelli da usare per questa esercitazione sono i seguenti:

* Per il metodo manuale, è presente uno script dell'interfaccia della riga di comando usato per creare le risorse. Questo script si trova in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Questo script crea le risorse e configura il routing dei messaggi. Dopo aver eseguito questo script, creare manualmente gli arricchimenti del messaggio usando il [portale di Azure](https://portal.azure.com).
* Per il metodo automatizzato è presente un modello Azure Resource Manager automatico. Il modello si trova in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Questo modello crea le risorse, configura il routing dei messaggi e quindi configura gli arricchimenti dei messaggi.
* La terza applicazione in uso è l'app Simulazione dispositivi, che consente di inviare messaggi all'hub IoT e testare gli arricchimenti dei messaggi.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Configurare manualmente tramite l'interfaccia della riga di comando di Azure

Oltre a creare le risorse necessarie, lo script dell'interfaccia della riga di comando di Azure configura anche le due route agli endpoint che sono contenitori di archiviazione separati. Per altre informazioni su come configurare il routing dei messaggi, vedere [l'esercitazione sul routing](tutorial-routing.md). Dopo aver configurato le risorse, usare il portale di Azure [per](https://portal.azure.com) configurare gli arricchimenti dei messaggi per ogni endpoint. Continuare quindi con il passaggio di test.

> [!NOTE]
> Tutti i messaggi vengono instradati a entrambi gli endpoint, ma solo i messaggi indirizzati all'endpoint con arricchimenti dei messaggi configurati verranno arricchiti.
>

È possibile usare lo script seguente oppure aprire lo script nella cartella /resources del repository scaricato. Lo script esegue le operazioni seguenti:

* Creare un hub IoT.
* Creare un account di archiviazione.
* Creare due contenitori nell'account di archiviazione. Un contenitore è per i messaggi arricchiti e un altro contenitore per i messaggi che non vengono arricchiti.
* Configurare il routing per i due diversi account di archiviazione:
    * Creare un endpoint per ogni contenitore dell'account di archiviazione.
    * Creare una route per ogni endpoint del contenitore dell'account di archiviazione.

Esistono diversi nomi di risorse che devono essere univoci a livello globale, ad esempio il nome dell'hub IoT e il nome dell'account di archiviazione. Per semplificare l'esecuzione dello script, a questi nomi di risorse viene aggiunto un valore alfanumerico casuale denominato *randomValue*. Il valore casuale viene generato una volta all'inizio dello script. Viene aggiunto ai nomi delle risorse in base alle esigenze in tutto lo script. Se non si vuole che il valore sia casuale, è possibile impostarlo su una stringa vuota o su un valore specifico.

Se non è già stato fatto, aprire una finestra Cloud Shell [azure](https://shell.azure.com) e assicurarsi che sia impostata su Bash. Aprire lo script nel repository decompresso, selezionare CTRL+A per selezionarlo e quindi premere CTRL+C per copiarlo. In alternativa, è possibile copiare lo script dell'interfaccia della riga di comando seguente o aprirlo direttamente in Cloud Shell. Incollare lo script nella finestra Cloud Shell facendo clic con il pulsante destro del mouse sulla riga di comando e scegliendo **Incolla**. Lo script esegue un'istruzione alla volta. Dopo l'arresto dell'esecuzione dello script, selezionare **INVIO** per assicurarsi che eserciti l'ultimo comando. Il blocco di codice seguente mostra lo script usato, con commenti che illustrano le operazioni da eseguire.

Ecco le risorse create dallo script. *Enriched significa* che la risorsa è per i messaggi con arricchimenti. *Originale* significa che la risorsa è per i messaggi che non vengono arricchiti.

| Nome | Valore |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nome del contenitore | originale  |
| nome del contenitore | Arricchito  |
| Nome dispositivo IoT | Contoso-Test-Device |
| Nome dell'hub IoT | ContosoTestHubMsgEn |
| storage Account Name | contosostorage |
| nome endpoint 1 | ContosoStorageEndpointOriginal |
| nome endpoint 2 | ContosoStorageEndpointEnriched|
| route Name 1 | ContosoStorageRouteOriginal |
| route Name 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

A questo punto, le risorse sono tutte configurate e il routing dei messaggi è configurato. È possibile visualizzare la configurazione del routing dei messaggi nel portale e configurare gli arricchimenti dei messaggi per i messaggi che vengono inviati al **contenitore di** archiviazione arricchito.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Configurare manualmente gli arricchimenti dei messaggi usando il portale di Azure

1. Passare all'hub IoT selezionando Gruppi **di risorse**. Selezionare quindi il gruppo di risorse configurato per questa esercitazione (**ContosoResourcesMsgEn**). Trovare l'hub IoT nell'elenco e selezionarlo. Selezionare **Routing messaggi** per l'hub IoT.

   ![Selezionare il routing dei messaggi](./media/tutorial-message-enrichments/select-iot-hub.png)

   Il riquadro di routing dei messaggi include tre schede con l'etichetta **Route**, **Endpoint personalizzati** e Arricchi **messaggi**. Esplorare le prime due schede per visualizzare la configurazione impostata dallo script. Usare la terza scheda per aggiungere arricchimenti dei messaggi. Arricchiamo ora i messaggi che vengono inviati all'endpoint per il contenitore di archiviazione denominato **enriched**. Immettere il nome e il valore e quindi selezionare l'endpoint **ContosoStorageEndpointEnriched** nell'elenco a discesa. Ecco un esempio di come configurare un arricchimento che aggiunge il nome dell'hub IoT al messaggio:

   ![Aggiungere il primo arricchimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Aggiungere questi valori all'elenco per l'endpoint ContosoStorageEndpointEnriched.

   | Chiave | Valore | Endpoint (elenco a discesa) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Customerid | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Se il dispositivo non ha un gemello, il valore inserito qui verrà contrassegnato come stringa per il valore negli arricchimenti del messaggio. Per visualizzare le informazioni sul dispositivo gemello, passare all'hub nel portale e selezionare **Dispositivi IoT.** Selezionare il dispositivo e quindi selezionare **Dispositivo gemello** nella parte superiore della pagina.
   >
   > È possibile modificare le informazioni sui dispositivi gemelli per aggiungere tag, ad esempio la posizione, e impostarla su un valore specifico. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](iot-hub-devguide-device-twins.md).

3. Al termine, il riquadro dovrebbe essere simile all'immagine seguente:

   ![Tabella con tutti gli arricchimenti aggiunti](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selezionare **Applica** per salvare le modifiche. Passare alla sezione [Testare gli arricchimenti dei](#test-message-enrichments) messaggi.

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Creare e configurare usando un modello di Resource Manager
È possibile usare un modello Resource Manager per creare e configurare le risorse, il routing dei messaggi e gli arricchimenti dei messaggi.

1. Accedere al portale di Azure. Selezionare **+ Crea una risorsa per** visualizzare una casella di ricerca. Immettere *la distribuzione* del modello e cercarla. Nel riquadro dei risultati selezionare Distribuzione modelli **(distribuire usando un modello personalizzato).**

   ![Distribuzione modelli nella portale di Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selezionare **Crea** nel riquadro **Distribuzione modelli** dati.

1. Nel riquadro **Distribuzione personalizzata** selezionare Crea un modello **personalizzato nell'editor**.

1. Nel riquadro **Modifica modello** selezionare **Carica file**. Esplora risorse visualizzato. Individuare il **template_messageenrichments.js** file nel file del repo decompresso in **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**. 

   ![Selezionare il modello dal computer locale](./media/tutorial-message-enrichments/template-select.png)

1. Selezionare **Apri** per caricare il file modello dal computer locale. Viene caricato e visualizzato nel riquadro di modifica.

   Questo modello è configurato per l'uso di un nome univoco globale dell'hub IoT e del nome dell'account di archiviazione aggiungendo un valore casuale alla fine dei nomi predefiniti, quindi è possibile usare il modello senza apportarvi modifiche.

   Ecco le risorse create caricando il modello. **Arricchito** significa che la risorsa è per i messaggi con arricchimenti. **Original** indica che la risorsa è per i messaggi che non vengono arricchiti. Si tratta degli stessi valori usati nello script dell'interfaccia della riga di comando di Azure.

   | Nome | Valore |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nome del contenitore | originale  |
   | nome del contenitore | Arricchito  |
   | Nome del dispositivo IoT | Contoso-Test-Device |
   | Nome dell'hub IoT | ContosoTestHubMsgEn |
   | storage Account Name | contosostorage |
   | nome endpoint 1 | ContosoStorageEndpointOriginal |
   | nome endpoint 2 | ContosoStorageEndpointEnriched|
   | route Name 1 | ContosoStorageRouteOriginal |
   | route Name 2 | ContosoStorageRouteEnriched |

1. Selezionare **Salva**. Viene **visualizzato il** riquadro Distribuzione personalizzata con tutti i parametri usati dal modello. L'unico campo che è necessario impostare è **Gruppo di risorse.** Crearne uno nuovo o selezionarne uno dall'elenco a discesa.

   Ecco la metà superiore del riquadro **Distribuzione** personalizzata. È possibile visualizzare la posizione in cui si compila il gruppo di risorse.

   ![Metà superiore del riquadro Distribuzione personalizzata](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Ecco la metà inferiore del riquadro **Distribuzione** personalizzata. È possibile visualizzare il resto dei parametri e i termini e le condizioni. 

   ![Metà inferiore del riquadro Distribuzione personalizzata](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Selezionare la casella di controllo per accettare i termini e le condizioni. Selezionare quindi **Acquista** per continuare con la distribuzione del modello.

1. Attendere che il modello sia completamente distribuito. Selezionare l'icona a forma di campana nella parte superiore della schermata per verificare lo stato di avanzamento. Al termine, passare alla sezione [Test message enrichments (Arricchimenti messaggi di](#test-message-enrichments) test).

## <a name="test-message-enrichments"></a>Testare gli arricchimenti dei messaggi

Per visualizzare gli arricchimenti dei messaggi, selezionare **Gruppi di risorse**. Selezionare quindi il gruppo di risorse in uso per questa esercitazione. Selezionare l'hub IoT dall'elenco delle risorse e passare a **Messaggistica**. Vengono visualizzati la configurazione del routing dei messaggi e gli arricchimenti configurati.

Ora che gli arricchimenti dei messaggi sono configurati per l'endpoint, eseguire l'applicazione Dispositivo simulato per inviare messaggi all'hub IoT. L'hub è stato configurato con impostazioni che eseere le attività seguenti:

* I messaggi indirizzati all'endpoint di archiviazione ContosoStorageEndpointOriginal non verranno arricchiti e verranno archiviati nel contenitore di archiviazione `original` .

* I messaggi indirizzati all'endpoint di archiviazione ContosoStorageEndpointEnriched verranno arricchiti e archiviati nel contenitore di archiviazione `enriched` .

L'applicazione Dispositivo simulato è una delle applicazioni nel download decompresso. L'applicazione invia messaggi per ognuno dei diversi metodi di routing dei messaggi nell'esercitazione [sul](tutorial-routing.md)routing, che include Archiviazione di Azure.

Fare doppio clic sul file di **soluzione IoT_SimulatedDevice.sln** per aprire il codice in Visual Studio e quindi **aprire Program.cs**. Sostituire il nome dell'hub IoT con il marcatore `{your hub name}` . Il formato del nome host dell'hub IoT **è {nome hub}.azure-devices.net**. Per questa esercitazione, il nome host dell'hub è ContosoTestHubMsgEn.azure-devices.net. Sostituire quindi la chiave del dispositivo salvata in precedenza quando è stato eseguito lo script per creare le risorse per il marcatore `{your device key}` .

Se non si ha la chiave del dispositivo, è possibile recuperarla dal portale. Dopo l'accesso, passare a **Gruppi di risorse,** selezionare il gruppo di risorse e quindi selezionare l'hub IoT. Cercare il dispositivo di test in Dispositivi **IoT** e selezionare il dispositivo. Selezionare l'icona di copia **accanto a Chiave primaria** per copiarla negli Appunti.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Esecuzione e test

Eseguire l'applicazione console per alcuni minuti. I messaggi inviati vengono visualizzati nella schermata della console dell'applicazione.

L'app invia un nuovo messaggio da dispositivo a cloud all'hub IoT ogni secondo. Il messaggio contiene un oggetto serializzato JSON con l'ID del dispositivo, la temperatura, l'umidità e il livello del messaggio, che per impostazione predefinita è `normal`. Assegna in modo casuale un livello di o , che determina il routing del messaggio all'account di archiviazione `critical` `storage` o all'endpoint predefinito. I messaggi inviati al **contenitore arricchito** nell'account di archiviazione verranno arricchiti.

Dopo l'invio di diversi messaggi di archiviazione, visualizzare i dati.

1. Selezionare **Gruppi di risorse**. Trovare il gruppo di risorse **ContosoResourcesMsgEn** e selezionarlo.

2. Selezionare l'account di archiviazione, **ovvero contosostorage**. Selezionare quindi **Storage Explorer (anteprima)** nel riquadro sinistro.

   ![Selezionare Storage Explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selezionare **CONTENITORI BLOB** per visualizzare i due contenitori che possono essere usati.

   ![Visualizzare i contenitori nell'account di archiviazione](./media/tutorial-message-enrichments/show-blob-containers.png)

I messaggi nel contenitore denominato **enriched** includono gli arricchimenti dei messaggi. I messaggi nel contenitore denominato **originale** hanno i messaggi non elaborati senza arricchimenti. Eseguire il drill-down in uno dei contenitori fino alla fine e aprire il file di messaggio più recente. Eseguire quindi la stessa operazione per l'altro contenitore per verificare che non siano stati aggiunti arricchimenti ai messaggi in tale contenitore.

Quando si osservano i messaggi arricchiti, viene visualizzato "hub IoT personale" con il nome dell'hub e la posizione e l'ID cliente, come nell'esempio seguente:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Ecco un messaggio non dirimente. Si noti che "my IoT Hub", "devicelocation" e "customerID" non vengono visualizzati qui perché questi campi vengono aggiunti dagli arricchimenti. Questo endpoint non ha arricchimenti.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create in questa esercitazione, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso, verranno rimossi l'hub IoT, l'account di archiviazione e il gruppo di risorse stesso.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usare l'interfaccia della riga di comando di Azure per pulire le risorse

Per rimuovere il gruppo di risorse, usare il comando [eliminazione del gruppo az](/cli/azure/group#az_group_delete). Tenere presente `$resourceGroup` che è stato impostato su **ContosoResourcesMsgEn** all'inizio di questa esercitazione.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata configurata e testata l'aggiunta di arricchimenti dei messaggi ai messaggi dell'hub IoT seguendo questa procedura:

**Usare gli arricchimenti dei messaggi dell'hub IoT**

> [!div class="checklist"]
> * Primo metodo: creare risorse e configurare il routing dei messaggi usando l'interfaccia della riga di comando di Azure. Configurare manualmente gli arricchimenti dei messaggi usando il portale di Azure [.](https://portal.azure.com)
> * Secondo metodo: creare risorse e configurare il routing dei messaggi e gli arricchimenti dei messaggi usando un Azure Resource Manager modello.
> * Eseguire un'app che simula un dispositivo IoT che invia messaggi all'hub.
> * Visualizzare i risultati e verificare che gli arricchimenti dei messaggi funzionino come previsto.

Per altre informazioni sugli arricchimenti dei messaggi, vedere [Panoramica degli arricchimenti dei messaggi.](iot-hub-message-enrichments-overview.md)

Per altre informazioni sul routing dei messaggi, vedere questi articoli:

> [!div class="nextstepaction"]
> [Usare il routing dei messaggi dell'hub IoT per inviare messaggi da dispositivo a cloud a endpoint diversi](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [Esercitazione: Routing dell'hub IoT](tutorial-routing.md)