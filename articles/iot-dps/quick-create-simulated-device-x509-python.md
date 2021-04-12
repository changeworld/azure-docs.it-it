---
title: "Avvio rapido: Effettuare il provisioning di un dispositivo X.509 simulato nell'hub IoT di Azure con Python"
description: "Avvio rapido: creare un dispositivo X.509 simulato ed effettuarne il provisioning con l'SDK per dispositivi Python per il servizio Device Provisioning in hub IoT. Questa guida introduttiva usa registrazioni singole."
author: wesmc7777
ms.author: wesmc
ms.date: 01/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 5db02f8ca1f0c311617a787525ee2fa5856eb5dc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045264"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Avvio rapido: Creare ed effettuare il provisioning di un dispositivo simulato X.509 usando l'SDK per dispositivi Python per il servizio Device Provisioning in hub IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In questa Guida introduttiva viene effettuato il provisioning di un computer di sviluppo come dispositivo Python X. 509. Usare il codice del dispositivo di esempio dell' [SDK Python di Azure](https://github.com/Azure/azure-iot-sdk-python) per connettere il dispositivo all'hub Internet delle cose. In questo esempio viene usata una registrazione singola con il servizio Device provisioning (DPS).

## <a name="prerequisites"></a>Prerequisiti

- Conoscenza dei concetti relativi al [provisioning](about-iot-dps.md#provisioning-process).
- Completamento dell'esercitazione [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md).
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3,6 o versione successiva](https://www.python.org/downloads/)
- [Git](https://git-scm.com/download/).


[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Preparare l'ambiente 

1. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per la versione più recente degli strumenti `git` da installare, tra cui **Git Bash**, l'app da riga di comando che è possibile usare per interagire con il repository Git locale. 

2. Aprire un prompt di git bash. Clonare il repository GitHub per l' [SDK Python di Azure](https://github.com/Azure/azure-iot-sdk-python).
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```


## <a name="create-a-self-signed-x509-device-certificate"></a>Creare un certificato per un dispositivo X.509 autofirmato 

In questa sezione verrà creato un certificato X. 509 autofirmato. Tenere presente questi punti importanti:

* I certificati autofirmati sono destinati solo alle operazioni di testing e non dovrebbero essere usati nell'ambiente di produzione.
* La data di scadenza predefinita per un certificato autofirmato è un anno.

Se non si hanno già i certificati del dispositivo per autenticare un dispositivo, è possibile creare un certificato autofirmato con OpenSSL per il test in questo articolo.  OpenSSL è incluso nell'installazione di git. 

1. Eseguire il comando seguente nel prompt di git bash.

    # <a name="windows"></a>[Windows](#tab/windows)
    
    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > La barra supplementare specificata per il nome del soggetto ( `//CN=Python-device-01` ) è necessaria solo per l'escape della stringa con git sulle piattaforme Windows. 

    # <a name="linux"></a>[Linux](#tab/linux)
    
    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```
    
    ---
    
2. Quando viene richiesto di **immettere la passphrase pass PEM:**, usare la passphrase `1234` per i test con questo articolo.    

3. Quando viene richiesta **di nuovo verifica, immettere la passphrase pass PEM:**, usare nuovamente la passphrase `1234` .    

Un file del certificato di test (*Python-Device. pem*) e un file di chiave privata (*Python-Device. Key. pem*) vengono generati nella directory in cui è stato eseguito il `openssl` comando.


## <a name="create-an-individual-enrollment-entry-in-dps"></a>Creare una singola voce di registrazione in DPS


Il servizio Device Provisioning in Azure IoT supporta due tipi di registrazione:

- [Gruppi di registrazioni](concepts-service.md#enrollment-group): Usato per registrare più dispositivi correlati.
- [Registrazioni singole](concepts-service.md#individual-enrollment): Usato per registrare un singolo dispositivo.

Questo articolo illustra una registrazione singola per eseguire il provisioning di un singolo dispositivo con un hub Internet.

1. Accedere al portale di Azure, selezionare il pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio di provisioning.

2. Nel menu del servizio Device Provisioning selezionare **Gestisci registrazioni**. Selezionare la scheda **Registrazioni singole** e quindi il pulsante **Aggiungi registrazione singola** in alto. 

3. Nel pannello **Aggiungi registrazione** immettere le informazioni seguenti:
   - Selezionare **X.509** come *meccanismo* di attestazione dell'identità.
   - Nel file con estensione *PEM o cer del certificato primario* scegliere *Seleziona un file* per selezionare il file del certificato **Python-Device. pem** se si usa il certificato di test creato in precedenza.
   - Facoltativamente, è possibile specificare le informazioni seguenti:
     - Selezionare un hub IoT collegato al servizio di provisioning.
     - Aggiornare lo **stato iniziale del dispositivo gemello** con la configurazione iniziale desiderata per il dispositivo.
   - Al termine, fare clic sul pulsante **Salva**. 

     [![Aggiungere una registrazione singola per l'attestazione X.509 nel portale](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Al termine della registrazione, il dispositivo X. 509 verrà visualizzato come **Python-Device-01** nella colonna *ID registrazione* della scheda registrazioni *singole* . Questo valore di registrazione deriva dal nome del soggetto nel certificato del dispositivo. 

## <a name="simulate-the-device"></a>Simulare il dispositivo

L'esempio di provisioning di Python, [provision_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) si trova nella `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios` Directory. Questo esempio usa sei variabili di ambiente per autenticare ed effettuare il provisioning di un dispositivo Internet delle cose usando DPS. Queste variabili di ambiente sono:

| Nome variabile              | Descrizione                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Questo valore è l'endpoint globale usato per la connessione alla risorsa DPS |    
| `PROVISIONING_IDSCOPE`     |  Questo valore è l'ambito ID per la risorsa DPS |    
| `DPS_X509_REGISTRATION_ID` |  Questo valore è l'ID del dispositivo. Deve corrispondere anche al nome del soggetto nel certificato del dispositivo |    
| `X509_CERT_FILE`           |  Nome file del certificato del dispositivo |    
| `X509_KEY_FILE`            |  Nome file della chiave privata per il certificato del dispositivo |
| `PASS_PHRASE`              |  Passphrase utilizzata per crittografare il certificato e il file di chiave privata ( `1234` ). |    

1. Nel menu del servizio Device Provisioning selezionare **Panoramica**. Prendere nota dell' _ambito ID_ e dell' _endpoint globale del dispositivo_.

    ![Informazioni sul servizio](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Nel prompt di git bash usare i comandi seguenti per aggiungere le variabili di ambiente per l'endpoint dispositivo globale e l'ambito ID.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

3. L'ID registrazione per il dispositivo Internet delle cose deve corrispondere al nome del soggetto nel certificato del dispositivo. Se è stato generato un certificato di prova autofirmato, `Python-device-01` è il nome del soggetto e l'ID di registrazione per il dispositivo. 

    Se si dispone già di un certificato del dispositivo, è possibile usare `certutil` per verificare il nome comune del soggetto usato per il dispositivo come illustrato di seguito per un certificato di prova autofirmato:

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

    Nel prompt di git bash impostare la variabile di ambiente per l'ID registrazione come indicato di seguito:

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

4. Nel prompt di git bash impostare le variabili di ambiente per il file di certificato, il file di chiave privata e la passphrase.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

5. Esaminare il codice per [provision_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) se non si usa **Python versione 3,7** o successiva, apportare la [modifica del codice indicata qui](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) per sostituire `asyncio.run(main())` e salvare la modifica. 

6. Eseguire l'esempio. L'esempio si connetterà, effettuerà il provisioning del dispositivo a un hub e invierà alcuni messaggi di prova all'hub.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

7. Nel portale passare all'hub Internet delle cose collegato al servizio di provisioning e aprire il pannello **dispositivi** Internet all'interno della sezione **esploratori** nel menu a sinistra. Al termine del provisioning del dispositivo X.509 simulato nell'hub, il relativo ID dispositivo verrà visualizzato nel pannello **Esplora dispositivi** con *STATO* **abilitato**. Potrebbe essere necessario premere il pulsante **Aggiorna** nella parte superiore se il pannello è stato aperto prima dell'esecuzione dell'applicazione del dispositivo di esempio. 

    ![Il dispositivo viene registrato con l'hub IoT](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Se si è modificato lo *stato iniziale del dispositivo gemello* rispetto al valore predefinito della voce di registrazione del dispositivo, è possibile eseguire il pull dello stato del dispositivo desiderato dall'hub e agire di conseguenza. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare a usare ed esplorare l'esempio di client dispositivo, non eseguire la pulizia delle risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare tutte le risorse create in questa guida.

1. Chiudere la finestra di output di esempio di client del dispositivo sul computer.
2. Nel portale di Azure selezionare **Tutte le risorse** nel menu a sinistra e quindi il servizio Device Provisioning. Aprire il pannello **Gestisci registrazioni** per il servizio e quindi selezionare la scheda **Registrazioni singole**. Selezionare la casella di controllo accanto all'*ID registrazione* del dispositivo registrato in questa guida di avvio rapido e fare clic sul pulsante **Elimina** nella parte superiore del riquadro. 
3. Nel menu a sinistra nel portale di Azure selezionare **Tutte le risorse** e quindi selezionare l'hub IoT. Aprire il pannello **Dispositivi IoT** per l'hub, selezionare la casella di controllo accanto all'*ID dispositivo* del dispositivo registrato in questa guida di avvio rapido e quindi fare clic sul pulsante **Elimina** nella parte superiore del riquadro.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato creato un dispositivo X. 509 simulato nel computer di sviluppo e ne è stato effettuato il provisioning nell'hub Internet delle cose usando il servizio Device provisioning in hub Azure Azure nel portale. Per informazioni su come registrare il dispositivo X.509 a livello di codice, passare alla guida di avvio rapido per la registrazione a livello di codice dei dispositivi X.509. 

> [!div class="nextstepaction"]
> [Avvio rapido di Azure: Registrare dispositivi X.509 nel servizio Device Provisioning in hub IoT di Azure](quick-enroll-device-x509-python.md)
