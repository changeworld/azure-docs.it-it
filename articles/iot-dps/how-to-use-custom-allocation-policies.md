---
title: Criteri di allocazione personalizzati con hub IoT di Azure Device Provisioning
description: Come usare i criteri di allocazione personalizzati con il hub IoT di Azure Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/26/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1432aee341509d8a5bdc9fffe89dd9bad33fc7de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766498"
---
# <a name="how-to-use-custom-allocation-policies"></a>Come usare i criteri di allocazione personalizzati

I criteri di allocazione personalizzati offrono maggiore controllo sulle modalità di assegnazione dei dispositivi a un hub IoT. A tale scopo viene usato un codice personalizzato in una [funzione di Azure](../azure-functions/functions-overview.md) per assegnare dispositivi a un hub IoT. Il servizio Device Provisioning chiama il codice della funzione di Azure per fornire tutte le informazioni rilevanti sul dispositivo e la registrazione. Viene eseguito il codice della funzione, che restituisce le informazioni dell'hub IoT usate per il provisioning del dispositivo.

È possibile definire e usare criteri di allocazione personalizzati qualora quelli forniti dal servizio di Device Provisioning non soddisfino i requisiti di uno specifico scenario.

Si potrebbe ad esempio scegliere di esaminare il certificato in uso su un dispositivo durante il provisioning e assegnare il dispositivo a un hub IoT in base a una proprietà del certificato. Oppure si potrebbero avere informazioni sui dispositivi archiviate in un database su cui è necessario eseguire una query per stabilire a quale hub IoT assegnare un dispositivo.

Questo articolo illustra i criteri di allocazione personalizzati che usano una funzione di Azure scritta in C#. Vengono creati due nuovi hub IoT per rappresentare una *divisione toaster di Contoso* e una *divisione pompe di calore di Contoso*. Per essere accettati per il provisioning, i dispositivi che richiedono questo servizio devono avere un ID di registrazione con uno dei suffissi di seguito:

* **-contoso-tstrsd-007**: divisione toaster di Contoso
* **-contoso-hpsd-088**: divisione pompe di calore di Contoso

Il provisioning dei dispositivi verrà effettuato sulla base di uno di questi suffissi richiesti per l'ID di registrazione. Questi dispositivi verranno simulati usando un esempio di provisioning incluso in [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

In questo articolo si esegue la procedura seguente:

* Usare l'interfaccia della riga di comando di Azure per creare due hub IoT per le divisioni di Contoso (**divisione toaster di Contoso** e **divisione pompe di calore di Contoso**)
* Creare una nuova registrazione di gruppo con una funzione di Azure per i criteri di allocazione personalizzati
* Creare le chiavi di dispositivo per due simulazioni di dispositivo.
* Configurare un ambiente di sviluppo per Azure IoT C SDK
* Simulare i dispositivi e verificare che ne sia stato effettuato il provisioning in base al codice di esempio nei criteri di allocazione personalizzati

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti si riferiscono a un ambiente di sviluppo Windows. Per Linux o macOS, vedere la sezione appropriata in [Preparare l'ambiente di sviluppo](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) nella documentazione dell'SDK.

- [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) con il carico di lavoro ['Sviluppo di applicazioni desktop con C++'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) abilitato. Sono supportati anche Visual Studio 2015 e Visual Studio 2017.

- La versione più recente di [Git](https://git-scm.com/download/) installata.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Creare il servizio di provisioning e due hub IoT divisionali

In questa sezione si usa il Azure Cloud Shell per creare un servizio di provisioning e due hub IoT che rappresentano la **divisione Toasters di Contoso** e la divisione Pompe di calore di **Contoso.**

> [!TIP]
> I comandi usati in questo articolo creano il servizio di provisioning e altre risorse nella località Stati Uniti occidentali. È consigliabile creare le risorse nell'area più vicina che supporta il servizio Device Provisioning. Per un elenco delle località disponibili, è possibile eseguire il comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` oppure passare alla pagina [Stato di Azure](https://azure.microsoft.com/status/) e cercare "Servizio Device Provisioning". Nei comandi è possibile specificare le località nel formato a una o più parole, ad esempio westus, Stati Uniti occidentali, Stati Uniti occidentali e così via. Il valore non distingue tra maiuscole e minuscole. Se si usa il formato a più parole per specificare la località, racchiudere il valore tra virgolette, ad esempio `-- location "West US"`.
>

1. Usare Azure Cloud Shell per creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

    Nell'esempio seguente viene creato un gruppo di risorse *denominato contoso-us-resource-group* nell'area *westus.* È consigliabile usare questo gruppo per tutte le risorse create in questo articolo. Questo approccio semplifica la pulizia al termine.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Usare il Azure Cloud Shell per creare un servizio device provisioning (DPS) con [il comando az iot dps create.](/cli/azure/iot/dps#az_iot_dps_create) Il servizio di provisioning verrà aggiunto a *contoso-us-resource-group*.

    L'esempio seguente crea un servizio di provisioning *denominato contoso-provisioning-service-1098* nella *località westus.* È necessario usare un nome di servizio univoco. Creare un suffisso personalizzato nel nome del servizio al posto **di 1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Il completamento del comando può richiedere alcuni minuti.

3. Usare Azure Cloud Shell per creare l'hub IoT della **divisione toaster di Contoso** con il comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create). L'hub IoT verrà aggiunto a *contoso-us-resource-group*.

    L'esempio seguente crea un hub IoT denominato *contoso-toasters-hub-1098* nella *località westus.* È necessario usare un nome di hub univoco. Creare il proprio suffisso nel nome dell'hub da sostituire a **1098**. 

    > [!CAUTION]
    > Il codice di funzione di Azure di esempio per i criteri di allocazione personalizzati richiede la `-toasters-` sottostringa nel nome dell'hub. Assicurarsi di usare un nome contenente la sottostringa toaster richiesta.
    
    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Il completamento del comando può richiedere alcuni minuti.

4. Usare Azure Cloud Shell per creare l'hub IoT della **divisione pompe di calore di Contoso** con il comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create). Questo hub IoT verrà aggiunto anche a *contoso-us-resource-group*.

    L'esempio seguente crea un hub IoT denominato *contoso-heatpumps-hub-1098* nella *località westus.* È necessario usare un nome di hub univoco. Creare il proprio suffisso nel nome dell'hub da sostituire a **1098**. 

    > [!CAUTION]
    > Il codice di esempio della funzione di Azure per i criteri di allocazione personalizzati richiede la `-heatpumps-` sottostringa nel nome dell'hub. Assicurarsi di usare un nome contenente la sottostringa heatpumps richiesta.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Il completamento del comando può richiedere alcuni minuti.

5. Gli hub IoT devono essere collegati alla risorsa DPS. 

    Eseguire i due comandi seguenti per ottenere le stringhe di connessione per gli hub appena creati. Sostituire i nomi delle risorse hub con i nomi scelti in ogni comando:

    ```azurecli-interactive 
    hubToastersConnectionString=$(az iot hub connection-string show --hub-name contoso-toasters-hub-1098 --key primary --query connectionString -o tsv)
    hubHeatpumpsConnectionString=$(az iot hub connection-string show --hub-name contoso-heatpumps-hub-1098 --key primary --query connectionString -o tsv)
    ```

    Eseguire i comandi seguenti per collegare gli hub alla risorsa DPS. Sostituire il nome della risorsa DPS con il nome scelto in ogni comando:

    ```azurecli-interactive 
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubToastersConnectionString --location westus
    az iot dps linked-hub create --dps-name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --connection-string $hubHeatpumpsConnectionString --location westus
    ```




## <a name="create-the-custom-allocation-function"></a>Creare la funzione di allocazione personalizzata

In questa sezione viene creata una funzione di Azure che implementa il criterio di allocazione personalizzato. Questa funzione decide a quale hub IoT di divisione deve essere registrato un dispositivo in base al fatto che il relativo ID di registrazione contenga la stringa **-contoso-tstrsd-007** o **-contoso-hpsd-088**. Imposta anche lo stato iniziale del dispositivo gemello a seconda che il dispositivo sia un tostapane o una pompa di calore.

1. Accedere al [portale di Azure](https://portal.azure.com). Nella home page selezionare **+ Crea una risorsa**.

2. Nella casella di ricerca *Cerca nel Marketplace* digitare "App per le funzioni". Nell'elenco a discesa selezionare **App per le funzioni**, quindi selezionare **Crea**.

3. Nella pagina di creazione di **App per le funzioni**, nella scheda **Informazioni di base**, immettere le impostazioni seguenti per la nuova app per le funzioni e selezionare **Rivedi e crea**:

    **Gruppo di** risorse: selezionare **contoso-us-resource-group** per mantenere insieme tutte le risorse create in questo articolo.

    **Nome dell'app per le funzioni**: immettere un nome univoco per l'app per le funzioni. Questo esempio usa **contoso-function-app-1098**.

    **Publish**: verificare che sia selezionata l'opzione **Codice**.

    **Stack di runtime**: selezionare **.NET Core** nell'elenco a discesa.

    **Versione:** **selezionare 3.1** dall'elenco a discesa.

    **Area**: selezionare la stessa area del gruppo di risorse. Questo esempio usa **Stati Uniti occidentali**.

    > [!NOTE]
    > Per impostazione predefinita, la funzionalità Application Insights è abilitata. Questa funzionalità non è necessaria per l'articolo, ma potrebbe risultare utile per identificare e analizzare i problemi che si verificano con l'allocazione personalizzata. Se si preferisce, è possibile disabilitare Application Insights selezionando la scheda **Monitoraggio** e quindi **No** per **Abilita Application Insights**.

    ![Creare un'app per le funzioni in cui ospitare la funzione di allocazione personalizzata](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Nella pagina **Riepilogo** selezionare **Crea** per creare l'app per le funzioni. Questa operazione può richiedere alcuni minuti. Al termine, selezionare **Vai alla risorsa**.

5. Nel riquadro sinistro della pagina Panoramica dell'app **per le** funzioni fare clic su **Funzioni** e quindi su **+ Aggiungi** per aggiungere una nuova funzione.

6. Nella pagina **Aggiungi funzione** fare clic su **Trigger HTTP** e quindi sul **pulsante** Aggiungi.

7. Nella pagina successiva fare clic su **Codice e test.** In questo modo è possibile modificare il codice per la funzione denominata **HttpTrigger1.** Il file **di codice run.csx** deve essere aperto per la modifica.

8. Fare riferimento ai pacchetti NuGet necessari. Per creare il dispositivo gemello iniziale, la funzione di allocazione personalizzata usa classi definite in due pacchetti NuGet che devono essere caricati nell'ambiente di hosting. Con Funzioni di Azure, ai pacchetti NuGet viene fatto riferimento usando un file *function.proj.* In questo passaggio si salva e si carica un file *function.proj* per gli assembly necessari.  Per altre informazioni, vedere [Uso di pacchetti NuGet con Funzioni di Azure](../azure-functions/functions-reference-csharp.md#using-nuget-packages).

    1. Copiare le righe seguenti nell'editor preferito e salvare il file nel computer *come function.proj*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.16.3" />
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.27.0" />
            </ItemGroup>  
        </Project>
        ```

    2. Fare clic **sul pulsante** Carica sopra l'editor di codice per caricare il file *function.proj.* Dopo il caricamento, selezionare il file nell'editor di codice usando la casella a discesa per verificare il contenuto.

9. Assicurarsi che *run.csx per* **HttpTrigger1** sia selezionato nell'editor di codice. Sostituire il codice per la **funzione HttpTrigger1** con il codice seguente e selezionare **Salva**:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Creare la registrazione

In questa sezione si creerà un nuovo gruppo di registrazioni che usa il criterio di allocazione personalizzato. Per semplicità, in questo articolo viene usata l'[attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md) per la registrazione. Per una soluzione più sicura è consigliabile usare l'[attestazione del certificato X.509](concepts-x509-attestation.md) con una catena di certificati.

1. Sempre nel [portale di Azure](https://portal.azure.com) aprire il servizio di provisioning.

2. Selezionare **Gestisci registrazioni** nel riquadro sinistro, quindi selezionare il pulsante **Aggiungi gruppo di registrazioni** nella parte superiore della pagina.

3. In **Aggiungi gruppo di registrazioni** immettere le informazioni seguenti e selezionare il **pulsante** Salva.

    **Nome del gruppo**: immettere **contoso-custom-allocated-devices**.

    **Tipo di attestazione**: selezionare **Chiave simmetrica**.

    **Genera chiavi automaticamente**: questa casella di controllo dovrebbe essere già selezionata.

    **Selezionare la modalità secondo cui assegnare i dispositivi agli hub**: selezionare **Personalizzata (usa funzione di Azure)**.

    **Sottoscrizione:** selezionare la sottoscrizione in cui è stata creata la funzione di Azure.

    **App per le** funzioni: selezionare l'app per le funzioni in base al nome. **Contoso-function-app-1098** è stato usato in questo esempio.

    **Funzione:** selezionare la **funzione HttpTrigger1.**

    ![Aggiungere il gruppo di registrazioni dell'allocazione personalizzata per l'attestazione con chiave simmetrica](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Dopo aver salvato la registrazione, aprirla nuovamente e annotare la **Chiave primaria**. È necessario salvare la registrazione prima di poter generare le chiavi. Questa chiave verrà usata per generare chiavi univoche di dispositivo per simulare i dispositivi in un secondo momento.

## <a name="derive-unique-device-keys"></a>Derivare le chiavi univoche di dispositivo

In questa sezione si creeranno due chiavi univoche per i dispositivi. Una chiave verrà utilizzata per simulare un dispositivo toaster, l'altra chiave per simulare un dispositivo pompa di calore.

Per generare la chiave del  dispositivo, usare la chiave primaria specificata in precedenza per calcolare il valore [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID di registrazione del dispositivo per ogni dispositivo e convertire il risultato in formato Base64. Per altre informazioni sulla creazione di chiavi di dispositivo derivate con i gruppi di registrazione, vedere la sezione sulle registrazioni di gruppo dell'articolo [Attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md).

Per l'esempio di questo articolo, usare i due ID di registrazione del dispositivo seguenti e calcolare una chiave di dispositivo per entrambi i dispositivi. Entrambi gli ID di registrazione presentano un suffisso valido per il funzionamento con il codice di esempio per i criteri di allocazione personalizzati:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**


# <a name="windows"></a>[Windows](#tab/windows)

Se si usa una workstation basata su Windows, è possibile usare PowerShell per generare la chiave di dispositivo derivata come illustrato nell'esempio seguente.

Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='breakroom499-contoso-tstrsd-007'
$REG_ID2='mainbuilding167-contoso-hpsd-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

# <a name="linux"></a>[Linux](#tab/linux)

Se si usa una workstation Linux, è possibile usare openssl per generare le chiavi del dispositivo derivate, come illustrato nell'esempio seguente.

Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=breakroom499-contoso-tstrsd-007
REG_ID2=mainbuilding167-contoso-hpsd-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---

I dispositivi simulati useranno le chiavi di dispositivo derivate con ogni ID di registrazione per eseguire l'attestazione con chiave simmetrica.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Preparare un ambiente di sviluppo per Azure IoT C SDK

In questa sezione si prepara l'ambiente di sviluppo da usare per creare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). L'SDK include il codice di esempio per il dispositivo simulato. Il dispositivo simulato tenterà di effettuare il provisioning durante la sequenza di avvio del dispositivo.

Questa sezione si riferisce a una workstation basata su Windows. Per un esempio di Linux, vedere la configurazione delle macchine virtuali in [Come effettuare il provisioning per la multi-tenancy](how-to-provision-multitenant.md).

1. Scaricare il [sistema di compilazione CMake](https://cmake.org/download/).

    È importante che nel computer siano installati i prerequisiti di Visual Studio (Visual Studio e carico di lavoro 'Sviluppo di applicazioni desktop con C++') **prima** di avviare l'installazione di `CMake`. Quando i prerequisiti sono pronti e il download è stato verificato, installare il sistema di compilazione CMake.

2. Trovare il nome del tag per la [versione più recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) dell'SDK.

3. Aprire un prompt dei comandi o la shell Git Bash. Eseguire i comandi seguenti per clonare la versione più recente del repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Usare il tag trovato nel passaggio precedente come valore per il parametro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

4. Creare una sottodirectory `cmake` nella directory radice del repository Git e passare a tale cartella. Eseguire i comandi seguenti dalla directory `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Eseguire il comando seguente che compila una versione dell'SDK specifica per la piattaforma di sviluppo client. Verrà generata una soluzione di Visual Studio per il dispositivo simulato nella directory `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Se `cmake` non trova il compilatore C++, è possibile che si verifichino errori di compilazione durante l'esecuzione del comando. In tal caso, provare a eseguire il comando nel [prompt dei comandi di Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

    Al termine della compilazione, le ultime righe di output saranno simili all'output seguente:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>Simulare i dispositivi

In questa sezione si aggiorna un esempio di provisioning denominato **prov\_dev\_client\_sample** che si trova in Azure IoT C SDK precedentemente configurato.

Questo codice di esempio simula una sequenza di avvio di dispositivo che invia la richiesta di provisioning all'istanza del servizio Device Provisioning. La sequenza di avvio riconoscerà il dispositivo toaster e lo assegnerà all'hub IoT usando i criteri di allocazione personalizzati.

1. Nel portale di Azure selezionare la scheda **Panoramica** per il servizio Device Provisioning e prendere nota del valore di **_Ambito ID_**.

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. In Visual Studio aprire il file di soluzione **azure_iot_sdks.sln** generato in precedenza tramite l'esecuzione di CMake. Il file di soluzione deve trovarsi nel percorso seguente:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Nella finestra *Esplora soluzioni* di Visual Studio passare alla cartella **Provision\_Samples**. Espandere il progetto di esempio denominato **prov\_dev\_client\_sample**. Espandere **File di origine** e aprire **prov\_dev\_client\_sample.c**.

4. Trovare la costante `id_scope` e sostituire il valore con il valore **Ambito ID** copiato in precedenza. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Trovare la definizione per la funzione `main()` nello stesso file. Assicurarsi che la variabile`hsm_type` sia impostata su `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` come illustrato di seguito:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Fare clic con il pulsante destro del mouse sul progetto **prov\_dev\_client\_sample** e scegliere **Imposta come progetto di avvio**.

### <a name="simulate-the-contoso-toaster-device"></a>Simulare il dispositivo toaster di Contoso

1. Per simulare il dispositivo toaster, individuare la chiamata a `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** che è impostata come commento.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Rimuovere il commento dalla chiamata alla funzione e sostituire i valori segnaposto (incluse le parentesi angolari) con l'ID registrazione del toaster e la chiave di dispositivo derivata generata in precedenza. Il valore di chiave **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** visualizzato di seguito viene fornito solo come esempio.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Salvare il file.

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilare il progetto fare clic su **Sì** per ricompilare il progetto prima dell'esecuzione.

    L'output seguente è un esempio del dispositivo toaster simulato che esegue correttamente l'avvio e la connessione all'istanza del servizio di provisioning da assegnare all'hub IoT dei toaster tramite i criteri di allocazione personalizzati:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Simulare il dispositivo pompa di calore di Contoso

1. Per simulare il dispositivo pompa di calore, aggiornare di nuovo la chiamata a `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c** con l'ID registrazione del dispositivo pompa di calore e la chiave di dispositivo derivata generata in precedenza. Il valore di chiave **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** visualizzato di seguito viene fornito solo come esempio.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Salvare il file.

2. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Nella richiesta di ricompilare il progetto fare clic su **Sì** per ricompilare il progetto prima dell'esecuzione.

    L'output seguente è un esempio del dispositivo a pompa di calore simulato che si avvia correttamente e si connette all'istanza del servizio di provisioning da assegnare all'hub IoT delle pompe di calore Contoso tramite i criteri di allocazione personalizzati:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Risoluzione dei problemi relativi ai criteri di allocazione personalizzati

La tabella seguente illustra gli scenari previsti e i codici di errore dei risultati che è possibile ricevere. Usare questa tabella come supporto per la risoluzione dei problemi relativi ai criteri di allocazione personalizzati con le Funzioni di Azure.

| Scenario | Risultato della registrazione dal servizio di provisioning | Effettuare il provisioning dei risultati SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Il webhook restituisce 200 OK con 'iotHubHostName' impostato su un nome host dell'hub IoT valido | Stato del risultato: Assegnato  | L'SDK restituisce PROV_DEVICE_RESULT_OK insieme alle informazioni dell'hub |
| Il webhook restituisce 200 OK con 'iotHubHostName' incluso nella risposta ma impostato su una stringa vuota o Null | Stato del risultato: Non riuscito<br><br> Codice di errore: CustomAllocationIotHubNotSpecified (400208) | L'SDK restituisce PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Il webhook restituisce 401 - Non autorizzato | Stato del risultato: Non riuscito<br><br>Codice di errore: CustomAllocationUnauthorizedAccess (400209) | L'SDK restituisce PROV_DEVICE_RESULT_UNAUTHORIZED |
| È stata creata una registrazione singola per disabilitare il dispositivo | Stato del risultato: Non disponibile | L'SDK restituisce PROV_DEVICE_RESULT_DISABLED |
| Il webhook restituisce il codice di errore > = 429 | L'orchestrazione di DPS effettuerà diversi nuovi tentativi. I criteri di ripetizione sono attualmente:<br><br>&nbsp;&nbsp;- Numero di tentativi: 10<br>&nbsp;&nbsp;- Intervallo iniziale: 1 s<br>&nbsp;&nbsp;- Incremento: 9 s | L'SDK ignorerà l'errore e invierà un altro messaggio per l'ottenimento dello stato nel periodo specificato |
| Il webhook restituisce qualsiasi altro codice di stato | Stato del risultato: Non riuscito<br><br>Codice di errore: CustomAllocationFailed (400207) | L'SDK restituisce PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende continuare a usare le risorse create in questo articolo, è possibile mantenerle. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create in questo articolo in modo da evitare inutili addebiti.

Questi passaggi presuppongono che tutte le risorse in questo articolo siano state create come indicato nello stesso gruppo di risorse denominato **contoso-us-resource-group**.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se si è creato l'hub IoT all'interno di un gruppo di risorse esistente che contiene risorse che si vogliono conservare, eliminare solo la risorsa dell'hub IoT invece di eliminare il gruppo di risorse.
>

Per eliminare il gruppo di risorse per nome:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Nella casella di testo **Filtra per nome...** digitare il nome del gruppo di risorse che contiene le risorse di interesse, **contoso-us-resource-group**. 

3. A destra del gruppo di risorse nell'elenco dei risultati selezionare **...** quindi su **Elimina gruppo di risorse**.

4. Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Digitare di nuovo il nome del gruppo di risorse per confermare e quindi selezionare **Elimina**. Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul reprovisioning, vedere Concetti di reprovisioning dei dispositivi [dell'hub IoT](concepts-device-reprovision.md) 
* Per altre informazioni sul deprovisioning, vedere Come eseguire il [deprovisioning](how-to-unprovision-devices.md) dei dispositivi precedentemente con provisioning automatico