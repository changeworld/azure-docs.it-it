---
title: Installare Azure IoT Edge per Linux in Windows | Microsoft Docs
description: Azure IoT Edge le istruzioni di installazione nei dispositivi Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8b549d868aed443e19d639ba6f6df7db20e014b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612119"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Installare Azure IoT Edge per Linux in un dispositivo Windows ed effettuare il provisioning (anteprima)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi dalla classe PC ai server industriali. Quando un dispositivo viene configurato con il runtime IoT Edge, è possibile iniziare a distribuirvi la logica di business dal cloud. Per altre informazioni, vedere [comprendere il runtime di Azure IOT Edge e la relativa architettura](iot-edge-runtime.md).

Azure IoT Edge per Linux in Windows consente di usare Azure IoT Edge nei dispositivi Windows usando macchine virtuali Linux. La versione Linux di Azure IoT Edge e di tutti i moduli Linux distribuiti con l'esecuzione nella macchina virtuale. Da qui, le applicazioni e il codice di Windows e il runtime di IoT Edge e i moduli possono interagire liberamente tra loro.

Questo articolo elenca i passaggi per configurare IoT Edge in un dispositivo Windows. Questi passaggi consentono di distribuire una macchina virtuale Linux che contiene il runtime di IoT Edge da eseguire nel dispositivo Windows, quindi effettuare il provisioning del dispositivo con l'identità del dispositivo dell'hub Internet.

>[!NOTE]
>IoT Edge per Linux in Windows è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Anche se IoT Edge per Linux in Windows è l'esperienza consigliata per l'uso di Azure IoT Edge in un ambiente Windows, i contenitori di Windows sono ancora disponibili. Se si preferisce usare i contenitori di Windows, vedere la Guida alle procedure per [l'installazione e la gestione di Azure IOT Edge per Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione valida. Se non si ha una [sottoscrizione di Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Gratuito o Standard in Azure.

* Un dispositivo Windows con i requisiti di sistema minimi seguenti:

  * Windows 10 versione 1809 o successiva; Build 17763 o versione successiva
  * Edizioni Professional, Enterprise o server
  * Memoria minima disponibile: 2 GB
  * Spazio minimo disponibile su disco: 10 GB
  * Se si sta creando una nuova distribuzione usando Windows 10, assicurarsi di abilitare Hyper-V. Per ulteriori informazioni, vedere come [installare Hyper-V in Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v.md).
  * Se si sta creando una nuova distribuzione usando Windows Server, assicurarsi di installare il ruolo Hyper-V. Per ulteriori informazioni, vedere come [installare il ruolo Hyper-V in Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server.md).
  * Se si sta creando una nuova distribuzione usando una macchina virtuale, assicurarsi di configurare correttamente la virtualizzazione annidata. Per ulteriori informazioni, vedere la guida alla [virtualizzazione annidata](nested-virtualization.md) .

* Accesso all'interfaccia di amministrazione di Windows con l'estensione Azure IoT Edge per l'interfaccia di amministrazione di Windows installata:

   1. Scaricare il [programma di installazione di Windows Admin Center](https://aka.ms/wacdownload).

   1. Eseguire il programma di installazione scaricato e seguire le istruzioni dell'installazione guidata per installare l'interfaccia di amministrazione di Windows. 

   1. Una volta installato, usare un browser supportato per aprire l'interfaccia di amministrazione di Windows. I browser supportati includono Microsoft Edge (Windows 10, versione 1709 o successiva), Google Chrome e Microsoft Edge Insider.

   1. Al primo utilizzo dell'interfaccia di amministrazione di Windows verrà richiesto di selezionare un certificato da utilizzare. Selezionare **Windows Admin Center client** come certificato.

   1. È il momento di installare l'estensione Azure IoT Edge. Selezionare l'icona a forma di ingranaggio nella parte superiore destra del dashboard dell'interfaccia di amministrazione di Windows.

      ![Selezionare l'icona a forma di ingranaggio nella parte superiore destra del dashboard per accedere alle impostazioni.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. Nel menu **Impostazioni** , in **gateway**, selezionare **estensioni**.

   1. Nella scheda **estensioni disponibili** trovare **Azure IOT Edge** nell'elenco di estensioni. Selezionarlo e selezionare la richiesta di **installazione** sopra l'elenco di estensioni.

   1. Al termine dell'installazione, nella scheda **estensioni installate** verrà visualizzato Azure IOT Edge nell'elenco delle estensioni installate.

## <a name="choose-your-provisioning-method"></a>Scegliere il metodo di provisioning

Azure IoT Edge per Linux in Windows supporta i metodi di provisioning seguenti:

* Provisioning manuale usando la stringa di connessione del dispositivo IoT Edge. Per usare questo metodo, registrare il dispositivo e recuperare una stringa di connessione usando la procedura descritta in [registrare un dispositivo IOT Edge nell'hub](how-to-register-device.md)Internet.
  * Scegliere l'opzione di autenticazione con chiave simmetrica perché i certificati autofirmati X. 509 non sono attualmente supportati da IoT Edge per Linux in Windows.
* Provisioning automatico tramite il servizio Device provisioning (DPS) e le chiavi simmetriche. Altre informazioni sulla [creazione e sul provisioning di un dispositivo di IOT Edge con chiavi DPS e simmetriche](how-to-auto-provision-symmetric-keys.md).
* Provisioning automatico con certificati DPS e X. 509. Altre informazioni sulla [creazione e sul provisioning di un dispositivo IOT Edge con certificati DPS e X. 509](how-to-auto-provision-x509-certs.md).

Il provisioning manuale è più facile da iniziare con pochi dispositivi. Il servizio Device provisioning è utile per il provisioning di molti dispositivi.

Se si prevede di usare uno dei metodi DPS per eseguire il provisioning del dispositivo o dei dispositivi, seguire i passaggi nell'articolo appropriato collegato sopra per creare un'istanza di DPS, collegare l'istanza di DPS all'hub Internet e creare una registrazione DPS. È possibile creare una *registrazione singola* per un singolo dispositivo o una registrazione di *gruppo* per un gruppo di dispositivi. Per altre informazioni sui tipi di registrazione, vedere [concetti relativi al servizio Device provisioning in hub Azure](../iot-dps/concepts-service.md#enrollment).

## <a name="create-a-new-deployment"></a>Creare una nuova distribuzione

Creare la distribuzione di Azure IoT Edge per Linux in Windows sul dispositivo di destinazione.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Nella pagina iniziale dell'interfaccia di amministrazione di Windows, sotto l'elenco delle connessioni, viene visualizzata una connessione host locale che rappresenta il PC in cui è in esecuzione l'interfaccia di amministrazione di Windows. Qui vengono visualizzati anche eventuali server, PC o cluster aggiuntivi gestiti.

È possibile usare l'interfaccia di amministrazione di Windows per eseguire l'installazione e la gestione di Azure IoT Edge per Linux in Windows nel dispositivo locale o nei dispositivi gestiti in remoto. In questa guida la connessione host locale fungerà da dispositivo di destinazione per la distribuzione di Azure IoT Edge per Linux in Windows.

Se si vuole eseguire la distribuzione in un dispositivo di destinazione remoto anziché nel dispositivo locale e il dispositivo di destinazione desiderato non è visualizzato nell'elenco, seguire le [istruzioni per aggiungere il dispositivo.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

   ![Dashboard iniziale del centro di amministrazione di Windows con dispositivo di destinazione elencato](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Selezionare **Aggiungi**.

1. Nel riquadro **Aggiungi o crea risorse** individuare il riquadro **Azure IOT Edge** . Selezionare **Crea nuovo** per installare una nuova istanza di Azure IOT Edge per Linux in Windows in un dispositivo.

   Se si dispone già di IoT Edge per Linux in Windows in esecuzione in un dispositivo, è possibile selezionare **Aggiungi** per connettersi al dispositivo IOT Edge esistente e gestirlo con l'interfaccia di amministrazione di Windows.

   ![Selezionare Crea nuovo nel riquadro Azure IoT Edge nell'interfaccia di amministrazione di Windows](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Viene aperto il riquadro **creare un Azure IOT Edge per Linux in Windows Deployment** . In **1. Introduzione** scheda, verificare che il dispositivo di destinazione soddisfi i requisiti minimi e selezionare **Avanti**.

1. Esaminare le condizioni di licenza, selezionare **Accetto** e fare clic su **Avanti**.

1. È possibile attivare o disattivare **i dati di diagnostica facoltativi** , a seconda delle proprie preferenze.

1. Selezionare **Avanti: Distribuisci**.

   ![Selezionare il pulsante Avanti: Distribuisci dopo aver attivato o disattivato i dati di diagnostica facoltativi.](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. In **2. Scheda Distribuisci** in **selezionare un dispositivo di destinazione** fare clic sul dispositivo elencato per convalidare che soddisfi i requisiti minimi. Quando lo stato è confermato come supportato, fare clic su **Avanti**.

   ![Selezionare il dispositivo per verificare che sia supportato](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Nella scheda **impostazioni 2,2** esaminare le impostazioni di configurazione della distribuzione. Una volta soddisfatte le impostazioni, fare clic su **Avanti**.

   ![Esaminare le impostazioni di configurazione della distribuzione](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >Se si usa una macchina virtuale Windows, è consigliabile usare un'opzione predefinita anziché un commutire esterno per assicurarsi che la macchina virtuale Linux creata nella distribuzione possa ottenere un indirizzo IP.
   >
   >L'uso di un'opzione predefinita assegna alla macchina virtuale Linux un indirizzo IP interno. Questo indirizzo IP interno non può essere raggiunto dall'esterno della macchina virtuale Windows, ma può essere connesso localmente durante l'accesso alla macchina virtuale Windows.
   >
   >Se si usa Windows Server, tenere presente che Azure IoT Edge per Linux in Windows non supporta automaticamente l'opzione predefinita. Per una macchina virtuale locale di Windows Server, assicurarsi che la macchina virtuale Linux possa ottenere un indirizzo IP tramite il Commuter esterno. Per una macchina virtuale Windows Server in Azure, configurare uno switch interno prima di distribuire IoT Edge per Linux in Windows.

1. Nella scheda **distribuzione 2,3** è possibile controllare lo stato di avanzamento della distribuzione. Il processo completo include il download del pacchetto Azure IoT Edge per Linux in Windows, l'installazione del pacchetto, la configurazione del dispositivo host e la configurazione della macchina virtuale Linux. Il completamento di questo processo potrebbe richiedere diversi minuti. Una distribuzione corretta è illustrata di seguito.

   ![Una distribuzione corretta visualizzerà ogni passaggio con un segno di spunta verde e un'etichetta ' completa '](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Al termine della distribuzione, si è pronti per eseguire il provisioning del dispositivo. Selezionare **Avanti: Connetti** per continuare con **3. Scheda Connetti** , che consente di gestire Azure IOT Edge il provisioning dei dispositivi.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installare IoT Edge per Linux in Windows sul dispositivo di destinazione, se non è già stato fatto.

> [!NOTE]
> Il processo di PowerShell seguente illustra come creare una distribuzione host locale di Azure IoT Edge per Linux in Windows. Per creare una distribuzione in un dispositivo di destinazione remoto usando PowerShell, è possibile usare [PowerShell remoto](/powershell/module/microsoft.powershell.core/about/about_remote) per stabilire una connessione a un dispositivo remoto ed eseguire questi comandi in modalità remota su tale dispositivo.

1. In una sessione di PowerShell con privilegi elevati eseguire ognuno dei comandi seguenti per scaricare IoT Edge per Linux in Windows.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installare IoT Edge per Linux in Windows sul dispositivo.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > È possibile specificare IoT Edge personalizzati per Linux nelle directory di installazione di Windows e VHDX aggiungendo i parametri INSTALLDIR = "<FULLY_QUALIFIED_PATH>" e VHDXDIR = "<FULLY_QUALIFIED_PATH>" al comando di installazione precedente.

1. Per una corretta esecuzione della distribuzione, è necessario impostare i criteri di esecuzione sul dispositivo di destinazione su `AllSigned` se non sono già presenti. È possibile controllare i criteri di esecuzione correnti in un prompt di PowerShell con privilegi elevati usando:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Se i criteri di esecuzione di `local machine` non sono `AllSigned` , è possibile impostare i criteri di esecuzione utilizzando:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Creare il IoT Edge per Linux per la distribuzione di Windows.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > È possibile eseguire questo comando senza parametri o facoltativamente personalizzare la distribuzione con i parametri. È possibile fare riferimento alle informazioni di [riferimento sugli script IOT Edge per Linux in Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) per visualizzare i significati dei parametri e i valori predefiniti.

1. Immettere "Y" per accettare le condizioni di licenza.

1. Immettere ' O ' o ' R ' per attivare o disattivare **i dati di diagnostica facoltativi** , a seconda delle preferenze. Una distribuzione corretta è illustrata di seguito.

   ![Una distribuzione riuscita dirà "distribuzione completata" alla fine dei messaggi](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Al termine della distribuzione, si è pronti per eseguire il provisioning del dispositivo.

---

Per eseguire il provisioning del dispositivo, è possibile seguire i collegamenti seguenti per passare alla sezione relativa al metodo di provisioning selezionato:

* [Opzione 1: provisioning manuale con la stringa di connessione del dispositivo IoT Edge](#option-1-provisioning-manually-using-the-connection-string)
* [Opzione 2: provisioning automatico tramite il servizio Device provisioning (DPS) e chiavi simmetriche](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Opzione 3: provisioning automatico con certificati DPS e X. 509](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Effettuare il provisioning del dispositivo

Scegliere un metodo per il provisioning del dispositivo e seguire le istruzioni nella sezione appropriata. Per il provisioning dei dispositivi, è possibile usare l'interfaccia di amministrazione di Windows o una sessione di PowerShell con privilegi elevati.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Opzione 1: provisioning manuale usando la stringa di connessione

Questa sezione descrive come eseguire manualmente il provisioning del dispositivo usando la stringa di connessione del dispositivo Azure IoT Edge.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Nel riquadro **Azure IOT Edge provisioning del dispositivo** selezionare **stringa di connessione (manuale)** nell'elenco a discesa Metodo di provisioning.

1. Nella [portale di Azure](https://ms.portal.azure.com/)passare alla scheda **IOT Edge** dell'hub Internet.

1. Fare clic sull'ID dispositivo del dispositivo. Copiare il campo della **stringa di connessione primaria** .

1. Incollarlo nel campo stringa di connessione del dispositivo nell'interfaccia di amministrazione di Windows. Scegliere quindi **provisioning con il metodo selezionato**.

   ![Scegliere il provisioning con il metodo selezionato dopo aver incollato la stringa di connessione del dispositivo](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Al termine del provisioning, selezionare **fine**. Verrà ripristinato il dashboard principale. A questo punto, dovrebbe essere visualizzato un nuovo dispositivo, il cui tipo è `IoT Edge Devices` . È possibile selezionare l'IoT Edge dispositivo a cui connettersi. Nella pagina **Panoramica** è possibile visualizzare l' **elenco dei moduli IOT Edge** e **IOT Edge lo stato** del dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nella [portale di Azure](https://ms.portal.azure.com/)passare alla scheda **IOT Edge** dell'hub Internet.

1. Fare clic sull'ID dispositivo del dispositivo. Copiare il campo della **stringa di connessione primaria** .

1. Incollare il testo segnaposto nel comando seguente ed eseguirlo in una sessione di PowerShell con privilegi elevati nel dispositivo di destinazione.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Opzione 2: provisioning tramite DPS con chiavi simmetriche

Questa sezione descrive come eseguire automaticamente il provisioning del dispositivo usando le chiavi simmetriche e di DPS.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Nel riquadro **Azure IOT Edge provisioning del dispositivo** selezionare **chiave simmetrica (DPS)** nell'elenco a discesa Metodo di provisioning.

1. Nella [portale di Azure](https://ms.portal.azure.com/)passare all'istanza di DPS.

1. Nella scheda **Panoramica** , copiare il valore di **ambito ID** . Incollarlo nel campo ID ambito nell'interfaccia di amministrazione di Windows.

1. Nella scheda **Gestisci registrazioni** del portale di Azure selezionare la registrazione creata. Copiare il valore della **chiave primaria** nei dettagli di registrazione. Incollarlo nel campo chiave simmetrica nell'interfaccia di amministrazione di Windows.

1. Fornire l'ID registrazione del dispositivo nel campo ID registrazione nell'interfaccia di amministrazione di Windows.

1. Scegliere **provisioning con il metodo selezionato**.

   ![Scegliere il provisioning con il metodo selezionato dopo aver compilato i campi obbligatori per il provisioning delle chiavi simmetriche](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Al termine del provisioning, selezionare **fine**. Verrà ripristinato il dashboard principale. A questo punto, dovrebbe essere visualizzato un nuovo dispositivo, il cui tipo è `IoT Edge Devices` . È possibile selezionare l'IoT Edge dispositivo a cui connettersi. Nella pagina **Panoramica** è possibile visualizzare l' **elenco dei moduli IOT Edge** e **IOT Edge lo stato** del dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copiare il comando seguente in un editor di testo. Sostituire il testo segnaposto con le informazioni come descritto in dettaglio.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. Nella [portale di Azure](https://ms.portal.azure.com/)passare all'istanza di DPS.

1. Nella scheda **Panoramica** , copiare il valore di **ambito ID** . Incollarlo sul testo segnaposto appropriato nel comando.

1. Nella scheda **Gestisci registrazioni** del portale di Azure selezionare la registrazione creata. Copiare il valore della **chiave primaria** nei dettagli di registrazione. Incollarlo sul testo segnaposto appropriato nel comando.

1. Fornire l'ID registrazione del dispositivo per sostituire il testo segnaposto appropriato nel comando.

1. Eseguire il comando in una sessione di PowerShell con privilegi elevati nel dispositivo di destinazione.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Opzione 3: provisioning tramite DPS con certificati X. 509

Questa sezione descrive come eseguire automaticamente il provisioning del dispositivo usando i certificati DPS e X. 509.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Nel riquadro **Azure IOT Edge provisioning del dispositivo** selezionare **certificato X. 509 (DPS)** nell'elenco a discesa Metodo di provisioning.

1. Nella [portale di Azure](https://ms.portal.azure.com/)passare all'istanza di DPS.

1. Nella scheda **Panoramica** , copiare il valore di **ambito ID** . Incollarlo nel campo ID ambito nell'interfaccia di amministrazione di Windows.

1. Fornire l'ID registrazione del dispositivo nel campo ID registrazione nell'interfaccia di amministrazione di Windows.

1. Caricare i file del certificato e della chiave privata.

1. Scegliere **provisioning con il metodo selezionato**.

   ![Scegliere il provisioning con il metodo selezionato dopo aver compilato i campi obbligatori per il provisioning dei certificati X. 509](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Al termine del provisioning, selezionare **fine**. Verrà ripristinato il dashboard principale. A questo punto, dovrebbe essere visualizzato un nuovo dispositivo, il cui tipo è `IoT Edge Devices` . È possibile selezionare l'IoT Edge dispositivo a cui connettersi. Nella pagina **Panoramica** è possibile visualizzare l' **elenco dei moduli IOT Edge** e **IOT Edge lo stato** del dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copiare il comando seguente in un editor di testo. Sostituire il testo segnaposto con le informazioni come descritto in dettaglio.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. Nella [portale di Azure](https://ms.portal.azure.com/)passare all'istanza di DPS.

1. Nella scheda **Panoramica** , copiare il valore di **ambito ID** . Incollarlo sul testo segnaposto appropriato nel comando.

1. Fornire l'ID registrazione del dispositivo per sostituire il testo segnaposto appropriato nel comando.

1. Sostituire il testo segnaposto appropriato con il percorso di origine assoluto del file del certificato.

1. Sostituire il testo segnaposto appropriato con il percorso di origine assoluto per il file di chiave privata.

1. Eseguire il comando in una sessione di PowerShell con privilegi elevati nel dispositivo di destinazione.

---

## <a name="verify-successful-configuration"></a>Verificare la corretta configurazione

Verificare che IoT Edge per Linux in Windows sia stato installato e configurato correttamente nel dispositivo IoT Edge.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Selezionare il dispositivo IoT Edge dall'elenco dei dispositivi connessi nell'interfaccia di amministrazione di Windows per la connessione.

1. Nella pagina Panoramica del dispositivo sono visualizzate alcune informazioni sul dispositivo:

    1. La sezione **IOT Edge Module list** Mostra i moduli in esecuzione nel dispositivo. Quando il servizio IoT Edge viene avviato per la prima volta, dovrebbe essere visualizzato solo il modulo **edgeAgent** in esecuzione. Il modulo edgeAgent viene eseguito per impostazione predefinita e permette di installare e avviare tutti i moduli aggiuntivi distribuiti nel dispositivo.
    1. Nella sezione **stato IOT Edge** viene visualizzato lo stato del servizio e deve essere segnalato come **attivo (in esecuzione)**.

1. Se è necessario risolvere i problemi relativi al servizio IoT Edge, usare lo strumento della **Shell dei comandi** nella pagina del dispositivo per SSH (Secure Shell) nella macchina virtuale ed eseguire i comandi di Linux.

    1. Se è necessario risolvere problemi del servizio, recuperare i log di servizio.

       ```bash
       journalctl -u iotedge
       ```

    2. Usare lo `check` strumento per verificare lo stato di configurazione e connessione del dispositivo.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Accedere alla IoT Edge per Linux in una macchina virtuale Windows usando il comando seguente nella sessione di PowerShell:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >L'unico account a cui è consentita la connessione SSH alla macchina virtuale è l'utente che l'ha creata.

1. Una volta effettuato l'accesso, è possibile controllare l'elenco dei moduli in esecuzione IoT Edge usando il comando Linux seguente:

   ```bash
   iotedge list
   ```

1. Se è necessario risolvere i problemi relativi al servizio IoT Edge, usare i seguenti comandi Linux.

    1. Se è necessario risolvere problemi del servizio, recuperare i log di servizio.

       ```bash
       journalctl -u iotedge
       ```

    2. Usare lo `check` strumento per verificare lo stato di configurazione e connessione del dispositivo.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Passaggi successivi

* Continuare a [distribuire i moduli IOT Edge](how-to-deploy-modules-portal.md) per informazioni su come distribuire i moduli nel dispositivo.
* Informazioni su come [gestire i certificati nel IOT Edge per Linux nella macchina virtuale Windows](how-to-manage-device-certificates.md) e trasferire i file dal sistema operativo host alla macchina virtuale Linux.
* Informazioni su come [configurare i dispositivi IOT Edge per la comunicazione tramite un server proxy](how-to-configure-proxy-support.md).
