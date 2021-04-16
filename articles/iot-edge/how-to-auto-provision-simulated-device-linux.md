---
title: Effettuare il provisioning del dispositivo con un TPM virtuale in una macchina virtuale Linux - Azure IoT Edge
description: Usare un TPM simulato in una macchina virtuale Linux per testare il servizio Device Provisioning di Azure per Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 79fe8acd06084c58b0cf9b47bf93e933c648510c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481991"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Creare ed effettuare il provisioning IoT Edge dispositivo con un TPM in Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Questo articolo illustra come testare il provisioning automatico in un dispositivo Linux IoT Edge usando un Trusted Platform Module (TPM). È possibile effettuare automaticamente il provisioning Azure IoT Edge dispositivi con [il servizio Device Provisioning.](../iot-dps/index.yml) Se non si ha familiarità con il processo di provisioning automatico, vedere la panoramica sul [provisioning](../iot-dps/about-iot-dps.md#provisioning-process) prima di continuare.

Le attività sono le seguenti:

1. Creare una macchina virtuale (VM) Linux in Hyper-V con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
1. Creare un'istanza del servizio Device Provisioning in hub IoT.
1. Creare una singola registrazione per il dispositivo.
1. Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

> [!TIP]
> Questo articolo descrive come testare il provisioning del servizio Device Provisioning usando un simulatore TPM, ma gran parte di esso si applica all'hardware TPM fisico, ad esempio [infineon OPTIGA &trade; TPM,](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)un dispositivo Azure Certified per IoT.
>
> Se si usa un dispositivo fisico, è possibile passare direttamente alla sezione Recuperare le informazioni di [provisioning](#retrieve-provisioning-information-from-a-physical-device) da un dispositivo fisico di questo articolo.

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo Windows con [Hyper-V abilitato](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Questo articolo usa Windows 10 in cui è in esecuzione una macchina virtuale Ubuntu Server.
* Un hub IoT attivo.

> [!NOTE]
> TPM 2.0 è necessario quando si usa l'attestazione TPM con DPS e può essere usato solo per creare registrazioni singole, non di gruppo.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Creare una macchina virtuale Linux con un TPM virtuale

In questa sezione viene creata una nuova macchina virtuale Linux in Hyper-V. Configurare questa macchina virtuale con un TPM simulato per testare il funzionamento del provisioning automatico con IoT Edge.

### <a name="create-a-virtual-switch"></a>Creare un commutatore virtuale

Un commutatore virtuale consente alla macchina virtuale di connettersi a una rete fisica.

1. Aprire La console di gestione di Hyper-V nel computer Windows.

2. Nel menu **Azioni** selezionare **Gestione commutatori virtuali**.

3. Scegliere un commutatore virtuale **esterno**, quindi selezionare **Crea commutatore virtuale**.

4. Assegnare un nome al nuovo commutatore virtuale, ad esempio **EdgeSwitch**. Verificare che il tipo di connessione sia impostato su **Rete esterna**, quindi selezionare **Ok**.

5. Un elemento pop-up avvisa l'utente che la connettività di rete potrebbe essere interrotta. Selezionare **Sì** per continuare.

Se si verificano errori in fase di creazione del nuovo commutatore virtuale, assicurarsi che nessun altro commutatore stia usando l'adattatore Ethernet e che nessun altro commutatore usi lo stesso nome.

### <a name="create-virtual-machine"></a>Creare macchina virtuale

1. Scaricare un file di immagine del disco da usare per la macchina virtuale e salvarlo in locale. Ad esempio, [Ubuntu server 18.04](http://releases.ubuntu.com/18.04/). Per informazioni sui sistemi operativi supportati per i IoT Edge, vedere Azure IoT Edge [sistemi supportati.](support.md)

2. Nella console di gestione di Hyper-V selezionare **di nuovo**  >  **Azione Nuova** macchina  >  **virtuale** nel menu Azioni. 

3. Completare la **Creazione guidata macchina virtuale** con le configurazioni specifiche seguenti:

   1. **Impostazione generazione**: selezionare **Generazione 2**. Per le macchine virtuali di seconda generazione è abilitata la virtualizzazione annidata, necessaria per eseguire IoT Edge in una macchina virtuale.
   2. **Configura rete**: impostare il valore di **Connessione** sul commutatore virtuale creato nella sezione precedente.
   3. **Opzioni di installazione**: selezionare **Installa un sistema operativo da un file immagine di avvio** e individuare il file di immagine del disco che è stato salvato in locale.

4. Selezionare **Fine** nella procedura guidata per creare la macchina virtuale.

La creazione della macchina virtuale può richiedere alcuni minuti.

### <a name="enable-virtual-tpm"></a>Abilitare il TPM virtuale

Dopo aver creato la macchina virtuale, aprire le relative impostazioni per abilitare il modulo TPM (Virtual Trusted Platform Module) che consente di effettuare il provisioning automatico del dispositivo.

1. Nella console di gestione di Hyper-V fare clic con il pulsante destro del mouse sulla macchina virtuale e **scegliere Impostazioni.**

2. Passare a **Sicurezza**.

3. Deselezionare l'opzione **Abilita avvio protetto**.

4. Selezionare **Abilita Trusted Platform Module**.

5. Fare clic su **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Avviare la macchina virtuale e raccogliere i dati del modulo TPM

Nella macchina virtuale creare uno strumento che è possibile usare per recuperare **l'ID** registrazione e la chiave **di approvazione del dispositivo.**

1. Nella console di gestione di Hyper-V avviare la macchina virtuale e connettersi a essa.

1. Seguire le istruzioni all'interno della macchina virtuale per completare il processo di installazione e riavviare la macchina.

1. Accedere alla macchina virtuale, quindi seguire la procedura descritta in Configurare un ambiente di sviluppo [Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) per installare e compilare l'SDK Azure IoT dispositivo per C.

   >[!TIP]
   >Nel corso di questo articolo verranno copiati e incollati nella macchina virtuale, operazione non semplice tramite l'applicazione di connessione di Gestione Hyper-V. È possibile connettersi alla macchina virtuale tramite la console di gestione di Hyper-V una sola volta per recuperarne l'indirizzo IP. Eseguire prima `sudo apt install net-tools` e quindi `hostname -I` . È quindi possibile usare l'indirizzo IP per connettersi tramite SSH: `ssh <username>@<ipaddress>` .

1. Eseguire i comandi seguenti per compilare lo strumento SDK che recupera le informazioni sul provisioning dei dispositivi dal TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Nella finestra di output vengono visualizzati **l'ID** registrazione del dispositivo e la chiave **di approvazione**. Copiare questi valori per usarli in un secondo momento quando si crea una singola registrazione per il dispositivo.

Dopo aver creato l'ID di registrazione e la chiave di approvazione, passare alla sezione [Configurare il servizio Device Provisioning in hub IoT](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Recuperare le informazioni di provisioning da un dispositivo fisico

Se si usa un dispositivo IoT Edge anziché una macchina virtuale, creare uno strumento che è possibile usare per recuperare le informazioni di provisioning del dispositivo.

1. Seguire la procedura descritta in [Configurare un ambiente di sviluppo Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) per installare e compilare Azure IoT SDK per dispositivi per C.

1. Eseguire i comandi seguenti per compilare lo strumento SDK che recupera le informazioni sul provisioning dei dispositivi dal dispositivo TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copiare i valori per **ID registrazione** e chiave **di approvazione**. Usare questi valori per creare una registrazione singola per il dispositivo nel servizio Device Provisioning.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="create-a-dps-enrollment"></a>Creare una registrazione nel servizio Device Provisioning

Recuperare le informazioni di provisioning dalla macchina virtuale e usarle per creare una registrazione singola nel servizio Device Provisioning.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-at-scale.md).

> [!TIP]
> Nell'interfaccia della riga di [](/cli/azure/iot/dps/enrollment) comando di Azure è possibile creare una registrazione e usare il flag **abilitato** per i dispositivi perimetrali per specificare che un dispositivo è un IoT Edge dispositivo.

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device Provisioning in hub IoT.

2. In **le impostazioni** selezionare **Gestisci registrazioni**.

3. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   1. In **Meccanismo** selezionare **TPM**.

   2. Specificare la **chiave di approvazione** e **l'ID** registrazione copiati dalla macchina virtuale.

      > [!TIP]
      > Se si usa un dispositivo TPM fisico, è necessario determinare la chiave di verifica dell'approvazione, che è univoca per ogni chip TPM e viene ottenuta dal produttore del chip TPM associato. È possibile derivare un **ID** di registrazione univoco per il dispositivo TPM creando, ad esempio, un hash SHA-256 della chiave di verifica dell'approvazione.

   3. Se si desidera, specificare un ID per il dispositivo. Se non si specifica un ID dispositivo, viene usato l'ID registrazione.

   4. Selezionare **True** per dichiarare che questa macchina virtuale è un IoT Edge dispositivo.

   5. Scegliere l'hub IoT collegato a cui si vuole connettere il dispositivo oppure selezionare **Collega al nuovo hub IoT.** È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di assegnazione selezionati.

   6. Aggiungere un valore di tag allo **stato dispositivo gemello iniziale**, se si desidera. È possibile usare tag per identificare come destinazione gruppi di dispositivi per la distribuzione di moduli. Per altre informazioni, vedere [Distribuire IoT Edge moduli su larga scala.](how-to-deploy-at-scale.md)

   7. Selezionare **Salva**.

Ora che esiste una registrazione per questo dispositivo, il runtime IoT Edge eseguire automaticamente il provisioning del dispositivo durante l'installazione.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale. Installare il runtime IoT Edge nella macchina virtuale.

Seguire la procedura descritta in [Installare il runtime Azure IoT Edge](how-to-install-iot-edge.md), quindi tornare a questo articolo per effettuare il provisioning del dispositivo.

## <a name="configure-the-device-with-provisioning-information"></a>Configurare il dispositivo con le informazioni di provisioning

Dopo aver installato il runtime nel dispositivo, configurare il dispositivo con le informazioni che usa per connettersi al servizio Device Provisioning e all'hub IoT.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Conoscere l'ambito **id DPS** e **l'ID** registrazione del dispositivo raccolti nelle sezioni precedenti.

1. Aprire il file di configurazione nel IoT Edge dispositivo.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Trovare la sezione relativa alle configurazioni di provisioning del file. Rimuovere il commento dalle righe per il provisioning TPM e assicurarsi che tutte le altre righe di provisioning siano commentate.

   La riga non deve contenere spazi vuoti precedenti e gli elementi annidati `provisioning:` devono essere rientrati di due spazi.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Aggiornare i valori di `scope_id` e con le informazioni sul servizio Device Backup e sul `registration_id` dispositivo.

1. Facoltativamente, usare le `always_reprovision_on_startup` righe o per configurare il comportamento di `dynamic_reprovisioning` reprovisioning del dispositivo. Se un dispositivo è impostato per eseguire di nuovo il provisioning all'avvio, tenterà sempre di effettuare il provisioning con DPS e quindi eseguirà il fall back al backup di provisioning in caso di errore. Se un dispositivo è impostato per il nuovo provisioning dinamico, IoT Edge riavvio e il provisioning se viene rilevato un evento di reprovisioning. Per altre informazioni, vedere Concetti relativi al [reprovisioning](../iot-dps/concepts-device-reprovision.md)dei dispositivi dell'hub IoT.

1. Salvare e chiudere il file.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Conoscere **l'ambito id DPS** e l'ID registrazione del dispositivo raccolti nelle sezioni precedenti. 

1. Aprire il file di configurazione nel IoT Edge dispositivo.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Trovare la sezione delle configurazioni di provisioning del file. Rimuovere il commento dalle righe per il provisioning TPM e assicurarsi che tutte le altre righe di provisioning siano commentate.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Aggiornare i valori di `id_scope` e con le informazioni sul dispositivo e sul `registration_id` DPS.

1. Facoltativamente, trovare la sezione relativa alla modalità di provisioning automatico del file. Usare il `auto_reprovisioning_mode` parametro per configurare il comportamento di reprovisioning del dispositivo su , o `Dynamic` `AlwaysOnStartup` `OnErrorOnly` . Per altre informazioni, vedere Concetti relativi al [reprovisioning](../iot-dps/concepts-device-reprovision.md)dei dispositivi dell'hub IoT.

1. Salvare e chiudere il file.
:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Concedere l'accesso IoT Edge al TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Il IoT Edge runtime deve accedere al TPM per effettuare automaticamente il provisioning del dispositivo.

È possibile concedere l'accesso TPM al runtime IoT Edge eseguendo l'override delle impostazioni di sistema in modo che il servizio `iotedge` abbia privilegi radice. Se non si vuole elevare i privilegi di servizio, è possibile usare la procedura seguente per concedere manualmente l'accesso al modulo TPM.

1. Trovare il percorso file al modulo hardware TPM sul dispositivo e salvarlo come variabile locale.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Creare una nuova regola che consentirà l'accesso del runtime IoT Edge a tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Aprire il file delle regole.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copiare le informazioni di accesso seguenti nel file di regole.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. Salvare e chiudere il file.

6. Attivare il sistema udev per valutare la nuova regola.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Verificare che la regola sia stata applicata.

   ```bash
   ls -l /dev/tpm0
   ```

   L'output con esito positivo viene visualizzato come segue:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se si riscontra che non state applicate le autorizzazioni corrette, provare a riavviare la macchina per aggiornare udev.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Il IoT Edge runtime si basa su un servizio TPM che broker accede al TPM di un dispositivo. Questo servizio deve accedere al TPM per effettuare automaticamente il provisioning del dispositivo.

È possibile concedere l'accesso al TPM eseguendo l'override delle impostazioni di sistema in modo che il `aziottpm` servizio abbia privilegi radice. Se non si vuole elevare i privilegi di servizio, è possibile usare la procedura seguente per concedere manualmente l'accesso al modulo TPM.

1. Trovare il percorso file al modulo hardware TPM sul dispositivo e salvarlo come variabile locale.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Creare una nuova regola che consentirà l'accesso del runtime IoT Edge a tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Aprire il file delle regole.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copiare le informazioni di accesso seguenti nel file di regole.

   ```input
   # allow aziottpm access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0600"
   ```

5. Salvare e chiudere il file.

6. Attivare il sistema udev per valutare la nuova regola.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Verificare che la regola sia stata applicata.

   ```bash
   ls -l /dev/tpm0
   ```

   L'output con esito positivo viene visualizzato come segue:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Se si riscontra che non state applicate le autorizzazioni corrette, provare a riavviare la macchina per aggiornare udev.
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>Riavviare IoT Edge e verificare la corretta installazione

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Riavviare il runtime IoT Edge in modo che accetti tutte le modifiche alla configurazione apportate nel dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

Verificare che il runtime IoT Edge sia in esecuzione.

   ```bash
   sudo systemctl status iotedge
   ```

Esaminare i log del daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Se vengono visualizzati errori di provisioning, è possibile che le modifiche di configurazione non abbiano ancora avuto effetto. Provare a riavviare di nuovo il daemon IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

In alternativa, provare a riavviare la macchina virtuale per verificare se le modifiche diventano effettive al nuovo avvio.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Applicare le modifiche di configurazione apportate al dispositivo.

   ```bash
   sudo iotedge config apply
   ```

Verificare che il runtime IoT Edge sia in esecuzione.

   ```bash
   sudo iotedge system status
   ```

Esaminare i log del daemon.

   ```cmd/sh
   sudo iotedge system logs
   ```

Se vengono visualizzati errori di provisioning, è possibile che le modifiche di configurazione non abbiano ancora avuto effetto. Provare a riavviare IoT Edge daemon.

   ```bash
   sudo systemctl daemon-reload
   ```

In alternativa, provare a riavviare la macchina virtuale per verificare se le modifiche diventano effettive al nuovo avvio.
:::moniker-end
<!-- end 1.2 -->

Se il runtime viene avviato correttamente, è possibile accedere all'hub IoT e verificare se è stato eseguito il provisioning automatico del nuovo dispositivo. Il dispositivo è ora è pronto per l'esecuzione di moduli IoT Edge.

Elencare i moduli in esecuzione.

```cmd/sh
iotedge list
```

È possibile verificare che sia stata usata la registrazione singola creata nel servizio Device Provisioning. Passare all'istanza del servizio Device Provisioning nella portale di Azure. Aprire i dettagli di registrazione per la registrazione singola creata. Si noti che lo stato della registrazione è **assegnato ed** è elencato l'ID dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag dei dispositivi gemelli contemporaneamente al provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come distribuire [e monitorare i moduli IoT Edge su](how-to-deploy-at-scale.md) larga scala usando il portale di Azure o [l'interfaccia della riga di comando di Azure.](how-to-deploy-cli-at-scale.md)
