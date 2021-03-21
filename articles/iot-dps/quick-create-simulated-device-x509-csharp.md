---
title: "Avvio rapido: Effettuare il provisioning di un dispositivo X.509 simulato nell'hub IoT di Azure con C#"
description: 'Guida introduttiva: creare ed effettuare il provisioning di un dispositivo X. 509 usando C# Device SDK per il servizio Device provisioning in hub Azure. Questa guida introduttiva usa registrazioni singole.'
author: wesmc7777
ms.author: wesmc
ms.date: 02/01/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 7d2a21a30cefbc6e83e48c29d81191323387b8f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101705544"
---
# <a name="quickstart-create-and-provision-an-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Guida introduttiva: creare ed effettuare il provisioning di un dispositivo X. 509 usando C# Device SDK per il servizio Device provisioning in hub Internet

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Questi passaggi illustrano come usare il codice del dispositivo degli [esempi di Azure per](https://github.com/Azure-Samples/azure-iot-samples-csharp) il provisioning di un dispositivo X. 509 per C#. In questo articolo si eseguirà il codice di esempio del dispositivo nel computer di sviluppo per connettersi a un hub Internet delle cose usando il servizio Device provisioning.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha familiarità con il processo di provisioning automatico, vedere la panoramica relativa al [provisioning](about-iot-dps.md#provisioning-process). È anche necessario aver completato la procedura descritta in [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare.

Il servizio Device Provisioning in Azure IoT supporta due tipi di registrazione:
- [Gruppi di registrazioni](concepts-service.md#enrollment-group): Usato per registrare più dispositivi correlati.
- [Registrazioni singole](concepts-service.md#individual-enrollment): Usato per registrare un singolo dispositivo.

Questo articolo descrive le registrazioni singole.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo 

1. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per la versione più recente degli strumenti `git` da installare, tra cui **Git Bash**, l'app da riga di comando che è possibile usare per interagire con il repository Git locale. 

1. Aprire un prompt dei comandi o Git Bash. Clonare gli esempi di Azure IoT per il repository GitHub C#:
    
    ```bash
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. Assicurarsi che nel computer sia installato [.NET Core 3.0.0 SDK o versione successiva](https://www.microsoft.com/net/download/windows) . Per controllare la versione, è possibile usare il comando seguente.

    ```bash
    dotnet --info
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Creare un certificato per un dispositivo X.509 autofirmato

In questa sezione verrà creato un certificato di test X. 509 autofirmato utilizzando `iothubx509device1` come nome comune del soggetto. Tenere presente questi punti importanti:

* I certificati autofirmati sono destinati solo alle operazioni di testing e non dovrebbero essere usati nell'ambiente di produzione.
* La data di scadenza predefinita per un certificato autofirmato è un anno.
* L'ID dispositivo del dispositivo Internet è il nome comune del soggetto nel certificato. Assicurarsi di usare un nome soggetto conforme ai [requisiti della stringa ID dispositivo](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

Si userà il codice di esempio di [X509Sample](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) per creare il certificato da usare con la voce di registrazione singola per il dispositivo.


1. In un prompt di PowerShell passare alla directory del progetto per l'esempio di provisioning dei dispositivi X. 509.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Il codice di esempio è configurato per usare i certificati X.509 archiviati in un file di formato PKCS12 protetto da password (certificate.pfx). È anche necessario un file di certificato di chiave pubblica (certificate.cer) per creare una registrazione singola più avanti in questa guida di avvio rapido. Per generare il certificato autofirmato e i file con estensione CER e PFX associati, eseguire il comando seguente:

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. Lo script chiede una password PFX. Ricordare questa password. è necessario riutilizzarla successivamente quando si esegue l'esempio. È possibile eseguire `certutil` per eseguire il dump del certificato e verificare il nome del soggetto.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

 ## <a name="create-an-individual-enrollment-entry-for-the-device"></a>Creare una voce di registrazione singola per il dispositivo


1. Accedere al portale di Azure, selezionare il pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio di provisioning.

2. Nel menu del servizio Device Provisioning selezionare **Gestisci registrazioni**. Selezionare la scheda **Registrazioni singole** e quindi il pulsante **Aggiungi registrazione singola** in alto. 

3. Nel pannello **Aggiungi registrazione** immettere le informazioni seguenti:
   - Selezionare **X.509** come *meccanismo* di attestazione dell'identità.
   - In *File di certificato primario con estensione pem o cer* scegliere *Selezionare un file* per selezionare il file di certificato **certificate.cer** creato nei passaggi precedenti.
   - Lasciare **ID dispositivo** vuoto. Il provisioning del dispositivo verrà effettuato con l'ID dispositivo impostato sul nome comune del certificato X.509, **iothubx509device1**. Questo nome comune sarà anche il nome usato per l'ID registrazione per la voce di registrazione singola. 
   - Facoltativamente, è possibile specificare le informazioni seguenti:
       - Selezionare un hub IoT collegato al servizio di provisioning.
       - Aggiornare lo **stato iniziale del dispositivo gemello** con la configurazione iniziale desiderata per il dispositivo.
   - Al termine, fare clic sul pulsante **Salva**. 

     [![Aggiungere una registrazione singola per l'attestazione X.509 nel portale](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Al termine della registrazione, la voce di registrazione X.509 verrà visualizzata come **iothubx509device1** nella colonna *ID registrazione* della scheda *Registrazioni singole*. 



## <a name="provision-the-device"></a>Effettuare il provisioning del dispositivo

1. Nel pannello **Panoramica** del servizio di provisioning prendere nota del valore di **_Ambito ID_** .

    ![Estrarre le informazioni dell'endpoint del servizio Device Provisioning dal pannello del portale](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Digitare il comando seguente per compilare ed eseguire l'esempio di provisioning di dispositivo X.509. Sostituire il valore di `<IDScope>` con l'ID ambito del servizio di provisioning. 

    Per impostazione predefinita, il file del certificato viene impostato su *./Certificate.pfx* e viene richiesta la password. pfx.  

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Se si vuole passare tutto come parametro, è possibile usare il formato di esempio seguente.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```


3. Il dispositivo si connetterà a DPS e verrà assegnato a un hub Internet. Il dispositivo invierà inoltre un messaggio di telemetria all'hub.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

4. Verificare che il provisioning del dispositivo sia stato effettuato. Al completamento del provisioning del dispositivo nell'hub Internet delle cose collegato al servizio di provisioning, l'ID del dispositivo viene visualizzato nel pannello **dispositivi** dell'hub. 

    ![Il dispositivo viene registrato con l'hub IoT](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Se si è modificato lo *stato iniziale del dispositivo gemello* rispetto al valore predefinito della voce di registrazione del dispositivo, è possibile eseguire il pull dello stato del dispositivo desiderato dall'hub e agire di conseguenza. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare ed esplorare l'esempio di client dispositivo, non eseguire la pulizia delle risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare tutte le risorse create in questa guida.

1. Chiudere la finestra di output di esempio di client del dispositivo sul computer.
1. Chiudere la finestra del simulatore TPM sul computer.
1. Nel portale di Azure selezionare **Tutte le risorse** nel menu a sinistra e quindi il servizio Device Provisioning. Nella parte superiore del pannello **Panoramica** fare clic su **Elimina** in alto nel riquadro.  
1. Nel menu a sinistra nel portale di Azure selezionare **Tutte le risorse** e quindi selezionare l'hub IoT. Nella parte superiore del pannello **Panoramica** fare clic su **Elimina** in alto nel riquadro.  

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato effettuato il provisioning di un dispositivo X. 509 nell'hub Internet delle cose usando il servizio Device provisioning in hub Azure. Per informazioni su come registrare il dispositivo X.509 a livello di codice, passare alla guida di avvio rapido per la registrazione a livello di codice dei dispositivi X.509. 

> [!div class="nextstepaction"]
> [Avvio rapido di Azure: Registrare dispositivi X.509 nel servizio Device Provisioning in hub IoT di Azure](quick-enroll-device-x509-csharp.md)
