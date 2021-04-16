---
title: Installare Azure IoT Edge per Linux in Windows | Microsoft Docs
description: Azure IoT Edge istruzioni di installazione nei dispositivi Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 2799e25dbd84ff07b375c6fa1b103789aae82b49
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538428"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Installare Azure IoT Edge per Linux in un dispositivo Windows ed effettuare il provisioning (anteprima)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi dalla classe PC ai server industriali. Quando un dispositivo viene configurato con il runtime IoT Edge, è possibile iniziare a distribuirvi la logica di business dal cloud. Per altre informazioni, vedere [Comprendere il runtime Azure IoT Edge e la relativa architettura.](iot-edge-runtime.md)

Azure IoT Edge per Linux in Windows consente di usare Azure IoT Edge nei dispositivi Windows usando macchine virtuali Linux. La versione Linux di Azure IoT Edge e tutti i moduli Linux distribuiti con esso vengono eseguiti nella macchina virtuale. Da qui, le applicazioni Windows e il codice e IoT Edge runtime e moduli possono interagire liberamente tra loro.

Questo articolo elenca i passaggi per configurare IoT Edge in un dispositivo Windows. Questi passaggi distribuiscono una macchina virtuale Linux che contiene il runtime IoT Edge per l'esecuzione nel dispositivo Windows, quindi effettuano il provisioning del dispositivo con l'identità del dispositivo dell'hub IoT.

>[!NOTE]
>IoT Edge per Linux in Windows è in [anteprima pubblica.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>
>Sebbene IoT Edge per Linux in Windows sia l'esperienza consigliata per l'uso di Azure IoT Edge in un ambiente Windows, i contenitori di Windows sono ancora disponibili. Se si preferisce usare i contenitori di Windows, vedere la guida per l'installazione e la gestione di [Azure IoT Edge per Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione valida. Se non si ha una [sottoscrizione di Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.

* Un dispositivo Windows con i requisiti minimi di sistema seguenti:

  * Windows 10 versione 1809 o successiva; build 17763 o successiva
  * Edizioni Professional, Enterprise o Server
  * Memoria disponibile minima: 1 GB
  * Spazio minimo su disco disponibile: 10 GB
  * Se si crea una nuova distribuzione usando Windows 10, assicurarsi di abilitare Hyper-V. Per altre informazioni, vedere Come installare [Hyper-V in Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
  * Se si crea una nuova distribuzione con Windows Server, assicurarsi di installare il ruolo Hyper-V. Per altre informazioni, vedere Come [installare il ruolo Hyper-V in Windows Server.](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  * Se si sta creando una nuova distribuzione usando una macchina virtuale, assicurarsi di configurare correttamente la virtualizzazione annidata. Per altre informazioni, vedere la guida [alla virtualizzazione annidata.](nested-virtualization.md)

* Accesso a Windows Admin Center con l'estensione Azure IoT Edge per Windows Admin Center installata:

   1. Scaricare il programma [Windows Admin Center di installazione .](https://aka.ms/wacdownload)

   1. Eseguire il programma di installazione scaricato e seguire le istruzioni dell'installazione guidata per installare Windows Admin Center. 

   1. Dopo l'installazione, usare un browser supportato per aprire Windows Admin Center. I browser supportati includono Microsoft Edge (Windows 10, versione 1709 o successiva), Google Chrome e Microsoft Edge Insider.

   1. Al primo utilizzo di Windows Admin Center, verrà richiesto di selezionare un certificato da usare. Selezionare **Windows Admin Center client** come certificato.

   1. È il momento di installare l'Azure IoT Edge predefinita. Selezionare l'icona a forma di ingranaggio in alto a destra Windows Admin Center dashboard.

      ![Selezionare l'icona a forma di ingranaggio in alto a destra nel dashboard per accedere alle impostazioni.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. Nel menu **Impostazioni,** in **Gateway,** selezionare **Estensioni.**

   1. Nella scheda **Estensioni disponibili** individuare **Azure IoT Edge** nell'elenco delle estensioni. Selezionarlo e selezionare il prompt **Installa** sopra l'elenco delle estensioni.

   1. Al termine dell'installazione, verrà visualizzato Azure IoT Edge nell'elenco delle estensioni installate nella **scheda Estensioni** installate .

## <a name="choose-your-provisioning-method"></a>Scegliere il metodo di provisioning

Azure IoT Edge per Linux in Windows supporta i metodi di provisioning seguenti:

* Provisioning manuale con la IoT Edge di connessione del dispositivo. Per usare questo metodo, registrare il dispositivo e recuperare una stringa di connessione usando la procedura descritta in Registrare un dispositivo [IoT Edge nell'hub IoT.](how-to-register-device.md)
  * Scegliere l'opzione di autenticazione con chiave simmetrica, perché i certificati autofirmati X.509 non sono attualmente supportati da IoT Edge per Linux in Windows.
* Provisioning automatico con il servizio Device Provisioning (DPS) e chiavi simmetriche. Altre informazioni sulla [creazione e il provisioning di un IoT Edge dispositivo con DPS e chiavi simmetriche.](how-to-auto-provision-symmetric-keys.md)
* Provisioning automatico con certificati DPS e X.509. Altre informazioni sulla [creazione e il provisioning di un IoT Edge con certificati DPS e X.509.](how-to-auto-provision-x509-certs.md)

Il provisioning manuale è più semplice per iniziare a usare alcuni dispositivi. Il servizio Device Provisioning è utile per il provisioning di molti dispositivi.

Se si prevede di usare uno dei metodi DPS per effettuare il provisioning del dispositivo o dei dispositivi, seguire i passaggi nell'articolo appropriato collegato in precedenza per creare un'istanza di DPS, collegare l'istanza di DPS all'hub IoT e creare una registrazione DPS. È possibile creare una *registrazione singola* per un singolo dispositivo o una registrazione *di gruppo* per un gruppo di dispositivi. Per altre informazioni sui tipi di registrazione, vedere i [hub IoT di Azure del servizio Device Provisioning](../iot-dps/concepts-service.md#enrollment).

## <a name="create-a-new-deployment"></a>Creare una nuova distribuzione

Creare la distribuzione di Azure IoT Edge per Linux in Windows nel dispositivo di destinazione.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Nella pagina Windows Admin Center iniziale, nell'elenco delle connessioni, verrà visualizzata una connessione host locale che rappresenta il PC in cui si esegue Windows Admin Center. Verranno visualizzati anche eventuali server, PC o cluster aggiuntivi gestiti.

È possibile usare Windows Admin Center per installare e gestire Azure IoT Edge per Linux in Windows nel dispositivo locale o in dispositivi gestiti remoti. In questa guida la connessione host locale fungerà da dispositivo di destinazione per la distribuzione di Azure IoT Edge per Linux in Windows.

Se si vuole eseguire la distribuzione in un dispositivo di destinazione remoto anziché nel dispositivo locale e non viene visualizzato il dispositivo di destinazione desiderato nell'elenco, seguire le istruzioni per [aggiungere il dispositivo.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

   ![Dashboard Windows Admin Center con il dispositivo di destinazione elencato](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Selezionare **Aggiungi**.

1. Nel riquadro **Aggiungi o crea risorse** individuare il riquadro Azure IoT Edge risorse.  Selezionare **Crea nuovo** per installare una nuova istanza di Azure IoT Edge per Linux in Windows in un dispositivo.

   Se si dispone già di IoT Edge per Linux in Windows  in esecuzione in un dispositivo, è possibile selezionare Aggiungi per connettersi al dispositivo IoT Edge esistente e gestirlo con Windows Admin Center.

   ![Selezionare Crea nuovo nel riquadro Azure IoT Edge in Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Verrà aperto il riquadro Create an Azure IoT Edge for Linux on Windows deployment (Crea un Azure IoT Edge per Linux in **Windows).** Nella scheda **1. Attività iniziali,** verificare che il dispositivo di destinazione soddisfi i requisiti minimi e selezionare **Avanti.**

1. Esaminare le condizioni di licenza, **selezionare Accetto** e selezionare **Avanti.**

1. È possibile attivare o **disattivare i dati di** diagnostica facoltativi, a seconda delle preferenze.

1. Selezionare **Avanti: Distribuisci**.

   ![Selezionare il pulsante Avanti: Distribuisci dopo aver selezionato i dati di diagnostica facoltativi in base alle preferenze](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Nella scheda **2. Nella** scheda Deploy (Distribuisci) in **Select a target device**(Seleziona un dispositivo di destinazione) fare clic sul dispositivo elencato per convalidare che soddisfi i requisiti minimi. Dopo che lo stato è stato confermato come supportato, selezionare **Avanti.**

   ![Selezionare il dispositivo per verificare che sia supportato](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Nella scheda **Impostazioni 2.2** esaminare le impostazioni di configurazione della distribuzione. Dopo aver soddisfatto le impostazioni, selezionare **Avanti.**

   ![Esaminare le impostazioni di configurazione della distribuzione](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >Se si usa una macchina virtuale Windows, è consigliabile usare un commutatore predefinito anziché un commutatore esterno per assicurarsi che la macchina virtuale Linux creata nella distribuzione possa ottenere un indirizzo IP.
   >
   >L'uso di un commutatore predefinito assegna alla macchina virtuale Linux un indirizzo IP interno. Questo indirizzo IP interno non può essere raggiunto dall'esterno della macchina virtuale Windows, ma può essere connesso in locale durante l'accesso alla macchina virtuale Windows.
   >
   >Se si usa Windows Server, tenere presente che Azure IoT Edge linux in Windows non supporta automaticamente l'opzione predefinita. Per una macchina virtuale Windows Server locale, assicurarsi che la macchina virtuale Linux possa ottenere un indirizzo IP tramite il commutatore esterno. Per una macchina virtuale Windows Server in Azure, configurare un commutatore interno prima di distribuire IoT Edge per Linux in Windows.

1. Nella scheda **Distribuzione 2.3** è possibile controllare lo stato della distribuzione. Il processo completo include il download del pacchetto Azure IoT Edge per Linux in Windows, l'installazione del pacchetto, la configurazione del dispositivo host e la configurazione della macchina virtuale Linux. Il completamento di questo processo può richiedere alcuni minuti. Una distribuzione riuscita è illustrata di seguito.

   ![Una distribuzione riuscita mostrerà ogni passaggio con un segno di spunta verde e un'etichetta "Completa"](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Al termine della distribuzione, è possibile effettuare il provisioning del dispositivo. Selezionare **Avanti: Connetti** per passare alla **versione 3. Scheda** Connetti, che gestisce il provisioning Azure IoT Edge dispositivi.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installare IoT Edge per Linux in Windows nel dispositivo di destinazione, se non è già stato fatto.

> [!NOTE]
> Il processo di PowerShell seguente illustra come creare una distribuzione host locale di Azure IoT Edge per Linux in Windows. Per creare una distribuzione in un dispositivo di destinazione remoto tramite PowerShell, è possibile usare [PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) remoto per stabilire una connessione a un dispositivo remoto ed eseguire questi comandi in remoto su tale dispositivo.

1. In una sessione di PowerShell con privilegi elevati eseguire ognuno dei comandi seguenti per scaricare IoT Edge per Linux in Windows.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installare IoT Edge per Linux in Windows nel dispositivo.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > È possibile specificare un IoT Edge personalizzato per Linux nell'installazione di Windows e nelle directory VHDX aggiungendo i parametri INSTALLDIR="<FULLY_QUALIFIED_PATH>" e VHDXDIR="<FULLY_QUALIFIED_PATH>" al comando di installazione precedente.

1. Per la corretta esecuzione della distribuzione, è necessario impostare i criteri di esecuzione nel dispositivo di destinazione `AllSigned` su se non lo è già. È possibile controllare i criteri di esecuzione correnti in un prompt di PowerShell con privilegi elevati usando:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Se il criterio di esecuzione `local machine` di non è , è possibile impostare i criteri di esecuzione `AllSigned` usando:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Creare il IoT Edge per la distribuzione linux in Windows.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > È possibile eseguire questo comando senza parametri o facoltativamente personalizzare la distribuzione con i parametri. È possibile fare riferimento [al riferimento IoT Edge per Linux](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) Windows PowerShell script per visualizzare i significati dei parametri e i valori predefiniti.

1. Immettere "Y" per accettare le condizioni di licenza.

1. Immettere "O" o "R" per attivare o disattivare i dati **di diagnostica** facoltativi, a seconda delle preferenze. Di seguito è illustrata una distribuzione corretta.

   ![Una distribuzione riuscita dirà "Distribuzione completata" alla fine dei messaggi](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Al termine della distribuzione, è possibile effettuare il provisioning del dispositivo.

---

Per effettuare il provisioning del dispositivo, è possibile seguire i collegamenti seguenti per passare alla sezione per il metodo di provisioning selezionato:

* [Opzione 1: Provisioning manuale con la IoT Edge di connessione del dispositivo](#option-1-provisioning-manually-using-the-connection-string)
* [Opzione 2: Provisioning automatico con il servizio Device Provisioning (DPS) e chiavi simmetriche](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Opzione 3: Provisioning automatico con certificati DPS e X.509](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Effettuare il provisioning del dispositivo

Scegliere un metodo per il provisioning del dispositivo e seguire le istruzioni nella sezione appropriata. È possibile usare il Windows Admin Center o una sessione di PowerShell con privilegi elevati per effettuare il provisioning dei dispositivi.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Opzione 1: Provisioning manuale usando la stringa di connessione

Questa sezione illustra il provisioning manuale del dispositivo usando Azure IoT Edge stringa di connessione del dispositivo.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Nel riquadro **Azure IoT Edge provisioning dei dispositivi** selezionare Stringa di connessione **(manuale) nell'elenco** a discesa metodo di provisioning.

1. Nel [portale di Azure](https://ms.portal.azure.com/)passare alla scheda IoT Edge **dell'hub** IoT.

1. Fare clic sull'ID dispositivo del dispositivo. Copiare **il campo Primary Connection String (Stringa di connessione** primaria).

1. Incollarlo nel campo della stringa di connessione del dispositivo nella Windows Admin Center. Scegliere quindi **Provisioning con il metodo selezionato.**

   ![Scegliere il provisioning con il metodo selezionato dopo aver incollato la stringa di connessione del dispositivo](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Al termine del provisioning, selezionare **Fine**. Verrà visualizzato di nuovo il dashboard principale. A questo punto, dovrebbe essere elencato un nuovo dispositivo, il cui tipo è `IoT Edge Devices` . È possibile selezionare il IoT Edge dispositivo per connettersi al dispositivo. Una volta visualizzata **la pagina Panoramica,** è possibile visualizzare l'IoT Edge **dei** moduli e IoT Edge **stato** del dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nel [portale di Azure](https://ms.portal.azure.com/)passare alla scheda IoT Edge **dell'hub** IoT.

1. Fare clic sull'ID dispositivo del dispositivo. Copiare **il campo Primary Connection String (Stringa di connessione** primaria).

1. Incollare il testo segnaposto nel comando seguente ed eseguirlo in una sessione di PowerShell con privilegi elevati nel dispositivo di destinazione.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Opzione 2: Provisioning tramite DPS con chiavi simmetriche

Questa sezione illustra il provisioning automatico del dispositivo usando DPS e chiavi simmetriche.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Nel riquadro **Azure IoT Edge provisioning dei dispositivi** selezionare Chiave **simmetrica (DPS) nell'elenco** a discesa metodo di provisioning.

1. Nel [portale di Azure](https://ms.portal.azure.com/)passare all'istanza di DPS.

1. Nella scheda **Panoramica** copiare il **valore Ambito** ID. Incollarlo nel campo ID ambito nella Windows Admin Center.

1. Nella scheda **Gestisci registrazioni** nella portale di Azure selezionare la registrazione creata. Copiare **il valore chiave** primaria nei dettagli della registrazione. Incollarlo nel campo chiave simmetrica nella Windows Admin Center.

1. Specificare l'ID registrazione del dispositivo nel campo ID registrazione nella Windows Admin Center.

1. Scegliere **Provisioning con il metodo selezionato.**

   ![Scegliere il provisioning con il metodo selezionato dopo aver compilato i campi obbligatori per il provisioning delle chiavi simmetriche](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Al termine del provisioning, selezionare **Fine**. Verrà visualizzato di nuovo il dashboard principale. A questo punto, dovrebbe essere elencato un nuovo dispositivo il cui tipo è `IoT Edge Devices` . È possibile selezionare il IoT Edge dispositivo per connettersi al dispositivo. Una volta visualizzata **la pagina Panoramica,** è possibile visualizzare l'IoT Edge **dei** moduli e IoT Edge **stato** del dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copiare il comando seguente in un editor di testo. Sostituire il testo segnaposto con le informazioni come descritto in dettaglio.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. Nel [portale di Azure](https://ms.portal.azure.com/)passare all'istanza di DPS.

1. Nella scheda **Panoramica** copiare il **valore Ambito** ID. Incollarlo sul testo segnaposto appropriato nel comando.

1. Nella scheda **Gestisci registrazioni** nella portale di Azure selezionare la registrazione creata. Copiare **il valore chiave** primaria nei dettagli della registrazione. Incollarlo sul testo segnaposto appropriato nel comando.

1. Specificare l'ID di registrazione del dispositivo per sostituire il testo segnaposto appropriato nel comando.

1. Eseguire il comando in una sessione di PowerShell con privilegi elevati nel dispositivo di destinazione.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Opzione 3: Provisioning tramite DPS con certificati X.509

Questa sezione illustra il provisioning automatico del dispositivo usando certificati DPS e X.509.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Nel riquadro **Azure IoT Edge provisioning dei dispositivi** selezionare Certificato **X.509 (DPS)** nell'elenco a discesa metodo di provisioning.

1. Nel [portale di Azure](https://ms.portal.azure.com/)passare all'istanza di DPS.

1. Nella scheda **Panoramica** copiare il **valore Ambito** ID. Incollarlo nel campo ID ambito nella Windows Admin Center.

1. Specificare l'ID registrazione del dispositivo nel campo ID registrazione nella Windows Admin Center.

1. Caricare il certificato e i file di chiave privata.

1. Scegliere **Provisioning con il metodo selezionato.**

   ![Scegliere il provisioning con il metodo selezionato dopo aver compilato i campi obbligatori per il provisioning dei certificati X.509](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Al termine del provisioning, selezionare **Fine**. Verrà visualizzato di nuovo il dashboard principale. A questo punto, dovrebbe essere elencato un nuovo dispositivo, il cui tipo è `IoT Edge Devices` . È possibile selezionare il IoT Edge dispositivo per connettersi al dispositivo. Una volta visualizzata **la pagina Panoramica,** è possibile visualizzare l'IoT Edge **dei** moduli e IoT Edge **stato** del dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copiare il comando seguente in un editor di testo. Sostituire il testo segnaposto con le informazioni come descritto in dettaglio.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. Nel [portale di Azure](https://ms.portal.azure.com/)passare all'istanza di DPS.

1. Nella scheda **Panoramica** copiare il **valore Ambito** ID. Incollarlo sul testo segnaposto appropriato nel comando.

1. Specificare l'ID di registrazione del dispositivo per sostituire il testo segnaposto appropriato nel comando.

1. Sostituire il testo segnaposto appropriato con il percorso di origine assoluto del file del certificato.

1. Sostituire il testo segnaposto appropriato con il percorso di origine assoluto del file di chiave privata.

1. Eseguire il comando in una sessione di PowerShell con privilegi elevati nel dispositivo di destinazione.

---

## <a name="verify-successful-configuration"></a>Verificare la corretta configurazione

Verificare che IoT Edge per Linux in Windows sia stato installato e configurato correttamente nel IoT Edge dispositivo.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Selezionare il dispositivo IoT Edge dall'elenco dei dispositivi connessi in Windows Admin Center per connettersi a esso.

1. La pagina di panoramica del dispositivo visualizza alcune informazioni sul dispositivo:

    1. La **IoT Edge Elenco moduli mostra** i moduli in esecuzione nel dispositivo. Quando il IoT Edge viene avviato per la prima volta, verrà visualizzato solo il **modulo edgeAgent** in esecuzione. Il modulo edgeAgent viene eseguito per impostazione predefinita e permette di installare e avviare tutti i moduli aggiuntivi distribuiti nel dispositivo.
    1. La **IoT Edge stato del servizio** mostra lo stato del servizio e deve essere attiva **(in esecuzione)**.

1. Se è necessario risolvere i problemi del servizio IoT Edge, usare lo strumento **Shell** comandi nella pagina del dispositivo per eseguire ssh (shell sicura) nella macchina virtuale ed eseguire i comandi Linux.

    1. Se è necessario risolvere problemi del servizio, recuperare i log di servizio.

       ```bash
       journalctl -u iotedge
       ```

    2. Usare lo `check` strumento per verificare la configurazione e lo stato di connessione del dispositivo.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Accedere alla macchina virtuale IoT Edge per Linux in Windows usando il comando seguente nella sessione di PowerShell:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >L'unico account consentito a SSH per la macchina virtuale è l'utente che la ha creata.

1. Dopo aver eseguito l'accesso, è possibile controllare l'elenco dei moduli IoT Edge usando il comando Linux seguente:

   ```bash
   iotedge list
   ```

1. Se è necessario risolvere i problemi del IoT Edge, usare i comandi Linux seguenti.

    1. Se è necessario risolvere problemi del servizio, recuperare i log di servizio.

       ```bash
       journalctl -u iotedge
       ```

    2. Usare lo `check` strumento per verificare la configurazione e lo stato di connessione del dispositivo.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Passaggi successivi

* Continuare a [distribuire IoT Edge moduli per](how-to-deploy-modules-portal.md) informazioni su come distribuire moduli nel dispositivo.
* Informazioni su come [gestire i certificati nel IoT Edge per Linux](how-to-manage-device-certificates.md) in una macchina virtuale Windows e trasferire i file dal sistema operativo host alla macchina virtuale Linux.
* Informazioni su come [configurare i dispositivi IoT Edge per comunicare tramite un server proxy.](how-to-configure-proxy-support.md)
