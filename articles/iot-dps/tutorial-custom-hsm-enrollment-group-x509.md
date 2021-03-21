---
title: Esercitazione - Effettuare il provisioning di dispositivi X.509 nell'hub IoT di Azure usando un modulo di protezione hardware (HSM) personalizzato
description: Questa esercitazione usa i gruppi di registrazioni. Questa esercitazione illustra come effettuare il provisioning di dispositivi X.509 usando un modulo di protezione hardware (HSM) personalizzato e l'SDK per dispositivi C per il servizio Device Provisioning in hub IoT di Azure.
author: wesmc7777
ms.author: wesmc
ms.date: 01/28/2021
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b178aa4a524cb7fcc85c7fc68ac5f772747787a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052364"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>Esercitazione: Effettuare il provisioning di più dispositivi X.509 usando i gruppi di registrazioni

In questa esercitazione si apprenderà come effettuare il provisioning di gruppi di dispositivi IoT che usano certificati X.509 per l'autenticazione. Il codice del dispositivo di esempio dell' [SDK Azure](https://github.com/Azure/azure-iot-sdk-c) per la distribuzione dei dispositivi di Azure verrà eseguito nel computer di sviluppo per simulare il provisioning di dispositivi X. 509. Sui dispositivi reali, il codice del dispositivo verrebbe distribuito ed eseguito dal dispositivo Internet delle cose.

Assicurarsi di aver completato almeno i passaggi descritti in [configurare il servizio Device provisioning in hub Internet con il portale di Azure](quick-setup-auto-provision.md) prima di continuare con questa esercitazione. Inoltre, se non si ha familiarità con il processo di provisioning automatico, vedere Panoramica del [provisioning](about-iot-dps.md#provisioning-process) . 

Il servizio Device provisioning in Azure è compatibile con due tipi di registrazione per i dispositivi di provisioning:

* [Gruppi di registrazioni](concepts-service.md#enrollment-group): Usato per registrare più dispositivi correlati.
* [Registrazioni singole](concepts-service.md#individual-enrollment): Usato per registrare un singolo dispositivo.

Questa esercitazione è simile alle esercitazioni precedenti che illustrano come usare i gruppi di registrazioni per effettuare il provisioning di gruppi di dispositivi. In questa esercitazione verranno però usati certificati X.509 al posto delle chiavi simmetriche. Rivedere le esercitazioni precedenti in questa sezione per un approccio semplice tramite l'uso delle [chiavi simmetriche](./concepts-symmetric-key-attestation.md).

In questa esercitazione verrà illustrato l'[esempio di modulo di protezione hardware personalizzato](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example) che fornisce un'implementazione stub per l'interazione con l'archiviazione sicura basata su hardware. Un [modulo di protezione hardware (HSM)](./concepts-service.md#hardware-security-module) viene usato per l'archiviazione sicura basata su hardware dei segreti dei dispositivi. Può essere usato con una chiave simmetrica, un certificato X.509 o un'attestazione TPM per fornire uno spazio di archiviazione sicuro per i segreti. L'archiviazione basata su hardware dei segreti del dispositivo non è obbligatoria, ma è consigliabile per proteggere le informazioni riservate come la chiave privata del certificato del dispositivo.


In questa esercitazione verranno completati gli obiettivi seguenti:

> [!div class="checklist"]
> * Creare una catena di certificati di trust per organizzare un gruppo di dispositivi che usano certificati X.509.
> * Completare il processo di verifica del possesso con un certificato di firma usato con la catena di certificati.
> * Creare una nuova registrazione di gruppo che usa la catena di certificati.
> * Configurare l'ambiente di sviluppo per il provisioning di un dispositivo usando codice di [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).
> * Effettuare il provisioning di un dispositivo usando la catena di certificati con l'esempio di modulo di protezione hardware (HSM) nell'SDK.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti seguenti sono per un ambiente di sviluppo Windows usato per simulare i dispositivi. Per Linux o macOS, vedere la sezione appropriata in [Preparare l'ambiente di sviluppo](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) nella documentazione dell'SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) con il carico di lavoro ['Sviluppo di applicazioni desktop con C++'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) abilitato. Sono supportati anche Visual Studio 2015 e Visual Studio 2017. 

    In questo articolo viene usato Visual Studio per compilare il codice di esempio del dispositivo che verrebbe distribuito nei dispositivi Internet.  Ciò non implica che Visual Studio sia necessario sul dispositivo stesso.

* La versione più recente di [Git](https://git-scm.com/download/) installata.

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Preparare l'ambiente di sviluppo per Azure IoT C SDK

In questa sezione si preparerà un ambiente di sviluppo usato per compilare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). L'SDK include il codice di esempio e strumenti usati dal provisioning dei dispositivi X.509 con il servizio Device Provisioning.

1. Scaricare il [sistema di compilazione CMake](https://cmake.org/download/).

    È importante che i prerequisiti di Visual Studio ([Visual Studio](https://visualstudio.microsoft.com/vs/) e il carico di lavoro ["Sviluppo di applicazioni desktop con C++"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)) siano installati nel computer **prima** di avviare l'installazione di `CMake`. Quando i prerequisiti sono pronti e il download è stato verificato, installare il sistema di compilazione CMake.

2. Trovare il nome del tag per la [versione più recente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) di Azure IoT C SDK.

3. Aprire un prompt dei comandi o la shell Git Bash. Eseguire i comandi seguenti per clonare la versione più recente del repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Usare il tag trovato nel passaggio precedente come valore per il parametro `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

4. Creare una sottodirectory `cmake` nella directory radice del repository Git e passare a tale cartella. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. La directory `cmake` creata conterrà l'esempio di modulo di protezione hardware personalizzato e il codice di provisioning dei dispositivi di esempio che usa il modulo di protezione hardware personalizzato per fornire l'autenticazione X.509. 

    Eseguire il comando seguente nella directory `cmake` per compilare una versione dell'SDK specifica per la piattaforma di sviluppo in uso. La compilazione includerà un riferimento all'esempio di modulo di protezione hardware personalizzato. 

    Quando si specifica il percorso usato con `-Dhsm_custom_lib` di seguito, assicurarsi di usare il percorso relativo alla directory `cmake` creata in precedenza. Il percorso relativo riportato di seguito è solo un esempio.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    Se `cmake` non trova il compilatore C++, si potrebbero verificare errori di compilazione durante l'esecuzione del comando precedente. In tal caso, provare a eseguire questo comando nel [prompt dei comandi di Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs).

    Al termine della compilazione, nella directory `cmake` verrà generata una soluzione di Visual Studio. Le ultime righe di output saranno simili all'output seguente:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>Creare una catena di certificati X.509

In questa sezione verrà generata una catena di certificati X. 509 di tre certificati per testare ogni dispositivo con questa esercitazione. I certificati avranno la gerarchia seguente.

![Catena di certificati dei dispositivi dell'esercitazione](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[Certificato radice](concepts-x509-attestation.md#root-certificate): occorre completare la [verifica del possesso](how-to-verify-certificates.md) per verificare il certificato radice. Questa verifica consentirà al servizio Device Provisioning di considerare attendibile il certificato e di verificare i certificati firmati con questo certificato.

[Certificato intermedio](concepts-x509-attestation.md#intermediate-certificate): i certificati intermedi vengono comunemente usati per raggruppare i dispositivi in modo logico per linea di prodotti, reparto aziendale o altri criteri. In questa esercitazione si userà una catena di certificati composta da un certificato intermedio. Il certificato intermedio sarà firmato tramite il certificato radice. Questo certificato verrà usato anche nel gruppo di registrazioni creato nel servizio Device Provisioning per raggruppare in modo logico un set di dispositivi. Questa configurazione consente di gestire un intero gruppo di dispositivi i cui certificati del dispositivo sono firmati con lo stesso certificato intermedio. È possibile creare gruppi di registrazioni per abilitare o disabilitare un gruppo di dispositivi. Per altre informazioni su come disabilitare un gruppo di dispositivi, vedere [Non consentire un certificato X.509 intermedio o un certificato CA radice usando un gruppo di registrazioni](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group).

[Certificati](concepts-x509-attestation.md#end-entity-leaf-certificate)del dispositivo: i certificati del dispositivo (foglia) verranno firmati dal certificato intermedio e archiviati nel dispositivo insieme alla relativa chiave privata. Idealmente, questi elementi sensibili verranno archiviati in modo sicuro con un modulo di protezione hardware. Ogni dispositivo presenta il certificato e la chiave privata, insieme alla catena di certificati quando si tenta di eseguire il provisioning. 

#### <a name="create-root-and-intermediate-certificates"></a>Creare certificati radice e intermedi

Per creare le parti radice e intermedia della catena di certificati:

1. Aprire un prompt dei comandi di Git Bash. Completare i passaggi 1 e 2 usando le istruzioni della shell Bash disponibili in [Gestione dei certificati CA di prova per esempi ed esercitazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials).

    Viene creata una directory di lavoro per gli script del certificato e vengono generati la radice di esempio e il certificato intermedio per la catena di certificati utilizzando OpenSSL. 
    
2. L'output mostra la posizione del certificato radice autofirmato. In un secondo momento questo certificato verrà sottoposto al processo di [verifica del possesso](how-to-verify-certificates.md) per verificarne la proprietà.

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        
    
3. L'output mostra la posizione del certificato intermedio firmato/emesso dal certificato radice. Questo certificato verrà usato con il gruppo di registrazioni che si creerà successivamente.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    
    
#### <a name="create-device-certificates"></a>Creare certificati del dispositivo

Per creare i certificati del dispositivo firmati dal certificato intermedio nella catena:

1. Eseguire il comando seguente per creare un nuovo certificato dispositivo/foglia con un nome soggetto fornito come parametro. Usare il nome soggetto di esempio fornito per questa esercitazione, `custom-hsm-device-01`. Questo nome soggetto sarà l'ID dispositivo del dispositivo IoT. 

    > [!WARNING]
    > Non usare un nome soggetto che contiene spazi. Questo nome soggetto è l'ID dispositivo del dispositivo IoT di cui effettuare il provisioning. Deve seguire le regole per gli ID dispositivo. Per altre informazioni, vedere [Proprietà delle identità dei dispositivi](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    L'output seguente mostra la posizione del nuovo certificato del dispositivo. Il certificato del dispositivo è firmato (emesso) dal certificato intermedio.

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
2. Eseguire il comando seguente per creare un file con estensione PEM della catena di certificati completo che include il nuovo certificato del dispositivo per `custom-hsm-device-01` .

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-01-full-chain.cert.pem && cd ..
    ```

    Usare un editor di testo e aprire il file della catena di certificati, *./certs/New-Device-01-full-chain.cert.pem*. Il testo della catena di certificati contiene la catena completa di tutti e tre i certificati. Questo testo verrà usato come catena di certificati con nel codice del dispositivo HSM personalizzato, più avanti in questa esercitazione per `custom-hsm-device-01` .

    Il testo della catena completa ha il formato seguente:
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

3. La chiave privata del nuovo certificato del dispositivo è scritta in *./private/new-device.key.pem*. Rinominare questo file di chiave *./private/New-Device-01.Key.pem* per il `custom-hsm-device-01` dispositivo. Il testo di questa chiave servirà al dispositivo durante il provisioning. Verrà aggiunto all'esempio di modulo di protezione hardware personalizzato più avanti.

    ```bash
    $ mv private/new-device.key.pem private/new-device-01.key.pem
    ```

    > [!WARNING]
    > Il testo per i certificati contiene solo le informazioni sulla chiave pubblica. 
    >
    > Il dispositivo deve però avere accesso anche alla chiave privata del certificato del dispositivo. Questa informazione è necessaria perché il dispositivo deve eseguire la verifica usando tale chiave in fase di esecuzione durante il tentativo di provisioning. La natura sensibile di questa chiave è uno dei motivi principali per cui è consigliabile usare l'archiviazione basata su hardware in un modulo di protezione hardware reale per proteggere le chiavi private.

4. Ripetere i passaggi 1-3 per un secondo dispositivo con ID dispositivo `custom-hsm-device-02` . Usare i valori seguenti per il dispositivo:

    |   Descrizione                 |  Valore  |
    | :---------------------------- | :--------- |
    | Nome soggetto                  | `custom-hsm-device-02` |
    | File catena di certificati completo   | *./certs/new-device-02-full-chain.cert.pem* |
    | Nome file della chiave privata          | *private/New-Device-02. Key. pem* |
    

## <a name="verify-ownership-of-the-root-certificate"></a>Verificare la proprietà del certificato radice

1. Usando le istruzioni disponibili in [Registrare la parte pubblica di un certificato X.509 e ottenere un codice di verifica](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code), caricare il certificato radice (`./certs/azure-iot-test-only.root.ca.cert.pem`) e ottenere un codice di verifica dal servizio Device Provisioning.

2. Dopo aver ottenuto dal servizio Device Provisioning un codice di verifica per il certificato radice, eseguire il comando seguente dalla directory di lavoro degli script dei certificati per generare un certificato di verifica.
 
    Il codice di verifica fornito in questa esercitazione è solo un esempio. Usare quello generato dal servizio Device Provisioning.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    Questo script crea un certificato firmato dal certificato radice con il nome soggetto impostato sul codice di verifica. Questo certificato consente al servizio Device Provisioning di verificare se si ha accesso alla chiave privata del certificato radice. Si noti la posizione del certificato di verifica nell'output dello script. Questo certificato viene generato nel formato `.pfx`.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. Come indicato in [Caricare il certificato di verifica firmato](how-to-verify-certificates.md#upload-the-signed-verification-certificate), caricare il certificato di verifica e fare clic su **Verifica** nel servizio Device Provisioning per completare il processo di verifica del possesso per il certificato radice.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Aggiornare l'archivio certificati nei dispositivi basati su Windows

Nei dispositivi non Windows è possibile passare la catena di certificati dal codice come archivio certificati.

Nei dispositivi basati su Windows è necessario aggiungere i certificati di firma (radice e intermedio) a un [archivio certificati](/windows/win32/secauthn/certificate-stores) Windows. In caso contrario, i certificati di firma non verranno trasportati al servizio Device Provisioning tramite un canale protetto con TLS (Transport Layer Security).

> [!TIP]
> È anche possibile usare OpenSSL anziché il canale sicuro (Schannel) con C SDK. Per altre informazioni sull'uso di OpenSSL, vedere [uso di OpenSSL nell'SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk).

Per aggiungere i certificati di firma all'archivio certificati nei dispositivi basati su Windows:

1. In un prompt di Git Bash passare alla sottodirectory `certs` che contiene i certificati di firma e convertirli in `.pfx` come indicato di seguito.

    Certificato radice:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    Certificato intermedio:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Fare clic con il pulsante destro del mouse sul pulsante **Start** di Windows. Fare clic su **Esegui**. Immettere *certmgr.mcs* e fare clic su **OK** per avviare lo snap-in di MMC per la gestione dei certificati.

3. Nel gestore certificati, in **Certificati - Utente corrente**, fare clic su **Autorità di certificazione radice attendibili**. Quindi nel menu fare clic su **Azione** > **Tutte le attività** > **Importa** per importare `root.pfx`.

    * Assicurarsi di eseguire la ricerca per **Scambio informazioni personali (.pfx)**
    * Usare `1234` come password.
    * Inserire il certificato nell'archivio certificati **Autorità di certificazione radice attendibili**.

4. Nel gestore certificati, in **Certificati - Utente corrente**, fare clic su **Autorità di certificazione intermedie**. Quindi nel menu fare clic su **Azione** > **Tutte le attività** > **Importa** per importare `intermediate.pfx`.

    * Assicurarsi di eseguire la ricerca per **Scambio informazioni personali (.pfx)**
    * Usare `1234` come password.
    * Inserire il certificato nell'archivio certificati **Autorità di certificazione intermedie**.

I certificati di firma sono ora considerati attendibili nel dispositivo basato su Windows e la catena completa può essere trasportata nel servizio Device Provisioning.



## <a name="create-an-enrollment-group"></a>Creare un gruppo di registrazioni

1. Accedere al portale di Azure, selezionare il pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning.

2. Selezionare la scheda **Gestisci registrazioni** e quindi selezionare il pulsante **Aggiungi gruppo di registrazione**.

3. Nel pannello **Aggiungi gruppo di registrazione** immettere le informazioni seguenti e quindi selezionare il pulsante **Salva**.

      ![Aggiungi gruppo di registrazione per l'attestazione X.509 nel portale](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | Campo        | Valore           |
    | :----------- | :-------------- |
    | **Nome gruppo** | Per questa esercitazione immettere **custom-hsm-x509-devices** |
    | **Tipo di attestazione** | Selezionare **Certificato** |
    | **Dispositivo IoT Edge** | Selezionare **False** |
    | **Tipo di certificato** | Selezionare **Certificato intermedio** |
    | **File di certificato primario con estensione pem o cer** | Passare al certificato intermedio creato in precedenza ( *./certs/azure-iot-test-only.intermediate.cert.pem*) |


## <a name="configure-the-provisioning-device-code"></a>Configurare il codice del dispositivo di provisioning

In questa sezione si aggiorna il codice di esempio con le informazioni sull'istanza del servizio Device provisioning. Se un dispositivo viene autenticato, verrà assegnato a un hub Internet delle cose collegato all'istanza del servizio Device provisioning configurata in questa sezione.

1. Nel portale di Azure selezionare la scheda **Panoramica** per il servizio Device Provisioning e prendere nota del valore di **_Ambito ID_** .

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Avviare Visual Studio e aprire il nuovo file di soluzione che è stato creato nella directory `cmake` creata nella radice del repository Git azure-iot-sdk-c. Il nome del file di soluzione è `azure_iot_sdks.sln`.

3. In Esplora soluzioni di Visual Studio passare a **Provisioning_Samples > prov_dev_client_sample > Source Files** e aprire *prov_dev_client_sample.c*.

4. Trovare la costante `id_scope` e sostituire il valore con il valore **Ambito ID** copiato in precedenza. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

5. Trovare la definizione per la funzione `main()` nello stesso file. Assicurarsi che la variabile `hsm_type` sia impostata su `SECURE_DEVICE_TYPE_X509` come illustrato di seguito.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Fare clic con il pulsante destro del mouse sul progetto **prov\_dev\_client\_sample** e scegliere **Imposta come progetto di avvio**.


## <a name="configure-the-custom-hsm-stub-code"></a>Configurare il codice stub del modulo di protezione hardware personalizzato

Le specifiche di interazione con uno spazio di archiviazione sicuro reale basato su hardware variano a seconda dell'hardware. Di conseguenza, le catene di certificati usati dai dispositivi simulati in questa esercitazione verranno hardcoded nel codice stub del modulo di protezione hardware personalizzato. In uno scenario reale, la catena di certificati verrebbe archiviata nell'hardware del modulo di protezione hardware effettivo per offrire una maggiore sicurezza per le informazioni sensibili. Metodi simili ai metodi stub usati in questo esempio vengono quindi implementati per leggere i segreti da tale archiviazione basata su hardware. 

Sebbene l'hardware HSM non sia necessario, è consigliabile proteggere le informazioni riservate, ad esempio la chiave privata del certificato. Se dall'esempio è stato chiamato un modulo di protezione hardware effettivo, la chiave privata non sarà presente nel codice sorgente. Se la chiave nel codice sorgente espone la chiave a chiunque possa visualizzare il codice. quindi viene eseguita in questo articolo solo per facilitare l'apprendimento.

Per aggiornare il codice dello stub del modulo di protezione hardware personalizzato per simulare l'identità del dispositivo con ID `custom-hsm-device-01` , seguire questa procedura:

1. In Esplora soluzioni di Visual Studio passare a **Provisioning_Samples > custom_hsm_example > Source Files** e aprire *custom_hsm_example.c*.

2. Aggiornare il valore stringa della costante stringa `COMMON_NAME` usando il nome comune usato per generare il certificato del dispositivo.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

3. Nello stesso file è necessario aggiornare il valore stringa della `CERTIFICATE` stringa costante usando il testo della catena di certificati salvato in *./certs/New-Device-01-full-chain.cert.pem* dopo la generazione dei certificati.

    La sintassi del testo del certificato deve essere conforme al modello seguente senza spazi aggiuntivi né analisi eseguita da Visual Studio.

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    L'aggiornamento corretto di questo valore stringa in questo passaggio può essere molto complicato e soggetto a errori. Per generare la sintassi corretta nel prompt di Git Bash, copiare e incollare i comandi della shell bash seguenti nel prompt dei comandi di Git Bash e premere **INVIO**. Questi comandi consentiranno di generare la sintassi per il valore della costante della stringa `CERTIFICATE`.

    ```Bash
    input="./certs/new-device-01-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Copiare e incollare il testo del certificato di output per il nuovo valore della costante. 


4. Nello stesso file è necessario aggiornare il valore stringa della costante `PRIVATE_KEY` con la chiave privata del certificato del dispositivo.

    La sintassi del testo della chiave privata deve essere conforme al modello seguente senza spazi aggiuntivi né analisi eseguita da Visual Studio.

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    Anche l'aggiornamento corretto di questo valore stringa in questo passaggio può essere molto complicato e soggetto a errori. Per generare la sintassi corretta nel prompt di Git Bash, copiare e incollare i comandi della shell bash seguenti e premere **INVIO**. Questi comandi consentiranno di generare la sintassi per il valore della costante della stringa `PRIVATE_KEY`.

    ```Bash
    input="./private/new-device-01.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Copiare e incollare il testo della chiave privata di output per il nuovo valore della costante. 

5. Salvare *custom_hsm_example.c*.

6. Nel menu di Visual Studio selezionare **Debug** > **Avvia senza eseguire debug** per eseguire la soluzione. Quando viene chiesto di ricompilare il progetto, selezionare **Sì** per ricompilare il progetto prima dell'esecuzione.

    L'output seguente è un esempio di dispositivo simulato `custom-hsm-device-01` che esegue correttamente l'avvio e la connessione al servizio di provisioning. Il dispositivo è stato assegnato a un hub IoT ed è stato registrato:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. Nel portale passare all'hub IoT collegato al servizio di provisioning e selezionare la scheda **Dispositivi IoT**. Al termine del provisioning del dispositivo X.509 nell'hub, il relativo ID dispositivo verrà visualizzato nel pannello **Dispositivi IoT** con *STATO* **abilitato**. Potrebbe essere necessario premere il pulsante **Aggiorna** nella parte superiore. 

    ![Il dispositivo del modulo di protezione hardware personalizzato viene registrato con l'hub IoT](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

8. Ripetere i passaggi 1-7 per un secondo dispositivo con ID dispositivo `custom-hsm-device-02` . Usare i valori seguenti per il dispositivo:

    |   Descrizione                 |  Valore  |
    | :---------------------------- | :--------- |
    | `COMMON_NAME`                 | `"custom-hsm-device-02"` |
    | Catena di certificati completa        | Genera il testo usando `input="./certs/new-device-02-full-chain.cert.pem"` |
    | Chiave privata                   | Genera il testo usando `input="./private/new-device-02.key.pem"` |

    L'output seguente è un esempio di dispositivo simulato `custom-hsm-device-02` che esegue correttamente l'avvio e la connessione al servizio di provisioning. Il dispositivo è stato assegnato a un hub IoT ed è stato registrato:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-02
    Press enter key to exit:
    ```


## <a name="clean-up-resources"></a>Pulire le risorse

Una volta terminati il test e l'esplorazione di questo client del dispositivo di esempio, usare la procedura seguente per eliminare tutte le risorse create da questa esercitazione.

1. Chiudere la finestra di output di esempio di client del dispositivo sul computer.
1. Nel portale di Azure selezionare **Tutte le risorse** nel menu a sinistra e quindi il servizio Device Provisioning. Aprire **Gestisci registrazioni** per il servizio e quindi selezionare la scheda **Gruppi di registrazioni**. Selezionare la casella di controllo accanto a *Nome gruppo* per il gruppo di dispositivi creato in questa esercitazione e selezionare il pulsante **Elimina** nella parte superiore del riquadro. 
1. Fare clic su **Certificati** nel servizio Device Provisioning. Fare clic su ogni certificato caricato e verificato in questa esercitazione e quindi sul pulsante **Elimina** per rimuoverlo.
1. Nel menu a sinistra nel portale di Azure selezionare **Tutte le risorse** e quindi selezionare l'hub IoT. Aprire **Dispositivi IoT** per l'hub. Selezionare la casella di controllo accanto a *ID DISPOSITIVO* per il dispositivo registrato in questa esercitazione. Fare clic sul pulsante **Elimina** nella parte superiore della pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato effettuato il provisioning di un dispositivo X.509 usando un modulo di protezione hardware personalizzato per l'hub IoT. Per informazioni su come effettuare il provisioning di dispositivi in più hub, continuare con l'esercitazione successiva. 

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il provisioning dei dispositivi in più hub IoT con bilanciamento del carico](tutorial-provision-multiple-hubs.md)