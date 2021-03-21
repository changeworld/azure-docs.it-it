---
title: 'Esercitazione: configurare un dispositivo di Azure IoT Edge Machine Learning su IoT Edge'
description: In questa esercitazione verrà configurata una macchina virtuale di Azure che esegue Linux come dispositivo Azure IoT Edge che funge da gateway trasparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: b59f8343c9dff07a32accd471f70ddf9f5309b8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463086"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Esercitazione: configurare un dispositivo Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In questo articolo viene configurata una macchina virtuale di Azure che esegue Linux come dispositivo Azure IoT Edge che funge da gateway trasparente. La configurazione del gateway trasparente consente ai dispositivi di connettersi all'hub IoT di Azure tramite il gateway senza riconoscerne l'esistenza. Allo stesso tempo, un utente può interagire con i dispositivi nell'hub IoT senza essere a conoscenza del dispositivo gateway intermedio. Infine, si aggiungeranno analisi perimetrali al sistema aggiungendo IoT Edge moduli al gateway trasparente.

>[!NOTE]
>I concetti di questa esercitazione si applicano a tutte le versioni di IoT Edge, ma il dispositivo di esempio creato per provare lo scenario viene eseguito IoT Edge versione 1,1.

I passaggi di questo articolo vengono in genere eseguiti da sviluppatori cloud.

In questa sezione dell'esercitazione si apprende come:

> [!div class="checklist"]
>
> * Creare certificati per consentire al dispositivo gateway di connettersi in modo sicuro ai dispositivi downstream.
> * Creare un dispositivo IoT Edge.
> * Creare una macchina virtuale Windows che simuli il dispositivo IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo fa parte di una serie di documenti relativi a un'esercitazione sull'uso di Azure Machine Learning in IoT Edge. Ogni articolo della serie si basa sulle attività di quello precedente. Se si è arrivati direttamente a questo articolo, vedere il [primo articolo](tutorial-machine-learning-edge-01-intro.md) della serie.

## <a name="create-certificates"></a>Creare i certificati

Per il funzionamento di un dispositivo come gateway, è necessario connettersi in modo sicuro ai dispositivi downstream. Con IoT Edge, è possibile usare un'infrastruttura a chiave pubblica (PKI) per configurare connessioni sicure tra i dispositivi. Nel caso illustrato si consente la connessione di un dispositivo IoT downstream a un dispositivo IoT Edge che funge da gateway trasparente. Per mantenere un livello di sicurezza ragionevole, il dispositivo downstream deve confermare l'identità del dispositivo IoT Edge. Per altre informazioni su come vengono usati i certificati dai dispositivi IoT Edge, vedere [Dettagli di utilizzo dei certificati di Azure IoT Edge](iot-edge-certs.md).

In questa sezione vengono creati i certificati autofirmati usando un'immagine Docker che viene quindi compilata ed eseguita. Si è scelto di usare un'immagine Docker per completare questo passaggio, perché riduce il numero di passaggi necessari per creare i certificati nel computer di sviluppo Windows. Per comprendere cosa è stato automatizzato con l'immagine Docker, vedere [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md).

1. Accedere alla macchina virtuale di sviluppo.
1. Creare una nuova cartella con il percorso e il nome **c:\edgeCertificates**.

1. Se non è già in esecuzione, avviare **Docker per Windows** dal menu Start di Windows.

1. Aprire Visual Studio Code.

1. Selezionare **file**  >  **Apri cartella**, quindi selezionare **C: \\ source \\ IoTEdgeAndMlSample \\ CreateCertificates**.

1. Nel riquadro di **esplorazione** , fare clic con il pulsante destro del mouse su **Dockerfile** e scegliere **Compila immagine**.

1. Nella finestra di dialogo accettare il valore predefinito per il nome dell'immagine e il tag: **createcertificates: Latest**.

    ![Screenshot che mostra la creazione di certificati in Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Attendere il completamento del processo di compilazione.

    > [!NOTE]
    > Potrebbe essere visualizzato un avviso relativo a una chiave pubblica mancante. È possibile ignorare questo avviso. Analogamente, verrà visualizzato un avviso di sicurezza che consiglia di controllare o reimpostare le autorizzazioni per l'immagine, che è sicuro ignorare per questa immagine.

1. Nella finestra del terminale di Visual Studio Code e seguire il contenitore createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker chiederà l'accesso all'unità **c:\\** . Selezionare **Share it** (Condividila).

1. Specificare le credenziali quando richiesto.

1. Al termine dell'esecuzione del contenitore, verificare i seguenti file in **c: \\ edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Caricare i certificati in Azure Key Vault

Per archiviare i certificati in modo sicuro e renderli accessibili da più dispositivi, i certificati verranno caricati in Azure Key Vault. Come si può notare dall'elenco precedente, sono disponibili due tipi di file di certificato: PFX e PEM. Il file PFX verrà trattato come Key Vault certificati da caricare Key Vault. I file PEM sono testo normale e verranno trattati come Key Vault segreti. Si userà l'istanza di Key Vault associata all'area di lavoro Azure Machine Learning creata eseguendo i [notebook di Jupyter](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks).

1. Dal [portale di Azure](https://portal.azure.com)passare all'area di lavoro Azure Machine Learning.

1. Nella pagina Panoramica dell'area di lavoro Machine Learning trovare il nome per **Key Vault**.

    ![Screenshot che mostra la copia del nome dell'insieme di credenziali delle chiavi.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Nel computer di sviluppo caricare i certificati in Key Vault. Sostituire **\<subscriptionId\>** e **\<keyvaultname\>** con le informazioni delle risorse.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Se richiesto, accedere ad Azure.

1. Lo script viene eseguito per alcuni minuti con l'output che elenca le nuove voci di Key Vault.

    ![Screenshot che mostra Key Vault output dello script.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Creare un dispositivo IoT Edge

Per connettere un dispositivo Azure IoT Edge a un hub IoT, creare prima di tutto un'identità per il dispositivo nell'hub. La stringa di connessione dell'identità del dispositivo nel cloud verrà usata per configurare il runtime nel dispositivo IoT Edge. Dopo che un dispositivo configurato si connette all'hub, è possibile distribuire i moduli e inviare messaggi. È anche possibile modificare la configurazione del dispositivo IoT Edge fisico cambiando l'identità del dispositivo corrispondente nell'hub Internet.

Per questa esercitazione viene creata la nuova identità del dispositivo usando Visual Studio Code. È anche possibile completare questi passaggi usando il portale di Azure o l'interfaccia della riga di comando di Azure.

1. Nel computer di sviluppo aprire Visual Studio Code.

1. Espandere il frame dell' **Hub Azure** Internet Explorer dalla visualizzazione Visual Studio Code **Explorer** .

1. Selezionare i puntini di sospensione e selezionare **crea IOT Edge dispositivo**.

1. Assegnare un nome al dispositivo. Per praticità, viene usato il nome **aaTurbofanEdgeDevice** in modo che venga ordinata in cima ai dispositivi elencati.

1. Il nuovo dispositivo viene visualizzato nell'elenco dei dispositivi.

    ![Screenshot che mostra una visualizzazione del dispositivo in Visual Studio Code Explorer.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Distribuire una macchina virtuale di Azure

Per creare il dispositivo IoT Edge per questa esercitazione viene usato il [Azure IOT Edge su](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) un'immagine Ubuntu di Azure Marketplace. Il Azure IoT Edge sull'immagine Ubuntu installa la versione più recente di IoT Edge Runtime e le relative dipendenze all'avvio. La macchina virtuale viene distribuita usando:

- Uno script di PowerShell `Create-EdgeVM.ps1` .
- Un modello di Azure Resource Manager, `IoTEdgeVMTemplate.json` .
- Uno script della shell `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Abilitare la distribuzione a livello di codice

Per usare l'immagine da Azure Marketplace in una distribuzione con script, è necessario abilitare la distribuzione a livello di codice per l'immagine.

1. Accedere al portale di Azure.

1. Selezionare **Tutti i servizi**.

1. Nella barra di ricerca immettere e selezionare **Marketplace**.

1. Nella barra di ricerca del Marketplace immettere e selezionare **Azure IoT Edge on Ubuntu**.

1. Selezionare il collegamento ipertestuale **Per iniziare** per eseguire la distribuzione a livello di codice.

1. Selezionare il pulsante **Enable (Abilita** ) e quindi selezionare Save ( **Salva**).

    ![Screenshot che Mostra come abilitare la distribuzione a livello di codice per una macchina virtuale.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Verrà visualizzata una notifica di esito positivo.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Eseguire quindi lo script per creare la macchina virtuale per il dispositivo IoT Edge.

1. Aprire una finestra di PowerShell e passare alla directory **EdgeVM**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Eseguire lo script per creare la macchina virtuale.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Quando viene richiesto, specificare i valori per ogni parametro. Per la sottoscrizione, il gruppo di risorse e la località, si consiglia di usare gli stessi valori disponibili per tutte le risorse in questa esercitazione.

    * **ID sottoscrizione di Azure**: disponibile nell'portale di Azure.
    * **Nome del gruppo di risorse**: nome memorabile per raggruppare le risorse per questa esercitazione.
    * **Località**: un'area di Azure in cui verrà creata la macchina virtuale, ad esempio westus2 o northeurope. Per altre informazioni, vedere tutte le [aree di Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Con valori AdminUsername**: nome dell'account amministratore che verrà usato per accedere alla macchina virtuale.
    * **AdminPassword**: la password da impostare per il nome utente amministratore nella macchina virtuale.

1. Per lo script per la configurazione della macchina virtuale, accedere ad Azure con le credenziali associate alla sottoscrizione di Azure in uso.

1. Lo script verifica le informazioni per la creazione della VM. Selezionare **s** o **Accedi** per continuare.

1. Lo script viene eseguito per diversi minuti mentre vengono completati i passaggi seguenti:

    * Crea il gruppo di risorse, se non esiste già
    * Crea la macchina virtuale
    * Aggiunge eccezioni NSG per la macchina virtuale per le porte 22 (SSH), 5671 (AMQP), 5672 (AMPQ) e 443 (TLS)
    * Installa l' [interfaccia](/cli/azure/install-azure-cli-apt) della riga di comando di Azure

1. L'output dello script è la stringa di connessione SSH per connettersi alla VM. Copiare la stringa di connessione per il passaggio successivo.

    ![Screenshot che mostra la copia della stringa di connessione SSH per una macchina virtuale.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Stabilire la connessione al dispositivo IoT Edge

Nelle diverse sezioni successive verrà configurata la macchina virtuale di Azure creata. Il primo passaggio consiste nel connettersi alla macchina virtuale.

1. Aprire un prompt dei comandi e incollare la stringa di connessione SSH copiata dall'output dello script. Immettere le informazioni per nome utente, suffisso e area in base ai valori specificati nello script di PowerShell nella sezione precedente.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Quando viene richiesto di convalidare l'autenticità dell'host, immettere **Sì** e premere **invio**.

1. Quando viene richiesto, specificare la password.

1. Ubuntu Visualizza un messaggio di benvenuto e quindi verrà visualizzato un prompt come `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Scaricare i certificati di Key Vault

In precedenza in questo articolo i certificati sono stati caricati in Key Vault per renderli disponibili per il dispositivo IoT Edge e il dispositivo foglia. Il dispositivo foglia è un dispositivo downstream che usa il dispositivo IoT Edge come gateway per comunicare con l'hub IoT.

Il dispositivo foglia verrà affrontato più avanti nell'esercitazione. In questa sezione scaricare i certificati nel dispositivo IoT Edge.

1. Nella sessione SSH della macchina virtuale Linux accedere ad Azure con l'interfaccia della riga di comando di Azure.

    ```azurecli
    az login
    ```

1. Verrà richiesto di aprire un browser a una pagina di [accesso del dispositivo Microsoft](https://microsoft.com/devicelogin) e fornire un codice univoco. Questi passaggi possono essere eseguiti nel computer locale. Dopo aver completato l'autenticazione, chiudere la finestra del browser.

1. Quando l'autenticazione viene completata correttamente, la VM Linux accede e visualizza l'elenco delle sottoscrizioni di Azure.

1. Impostare la sottoscrizione di Azure da usare per i comandi dell'interfaccia della riga di comando di Azure.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Nella VM creare una directory per i certificati.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Scaricare i certificati archiviati nell'insieme di credenziali delle chiavi: New-Edge-Device-full-chain. cert. pem, New-Edge-Device. Key. pem e Azure-IOT-Test-only. root. ca. cert. pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Aggiornare la configurazione del dispositivo IoT Edge

Il runtime IoT Edge usa il file /etc/iotedge/config.yaml per rendere persistente la configurazione. È necessario aggiornare tre informazioni in questo file:

* **Stringa di connessione del dispositivo**: la stringa di connessione dall'identità del dispositivo nell'hub Internet
* **Certificati**: i certificati da usare per le connessioni effettuate con i dispositivi downstream
* **Hostname**: il nome di dominio completo (FQDN) della macchina virtuale IOT Edge dispositivo

Il Azure IoT Edge sull'immagine Ubuntu usata per creare la macchina virtuale IoT Edge include uno script della shell che aggiorna il file config. YAML con la stringa di connessione.

1. In Visual Studio Code fare clic con il pulsante destro del mouse sul dispositivo IoT Edge, quindi selezionare **copia stringa di connessione del dispositivo**.

    ![Screenshot che mostra la copia della stringa di connessione da Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. Nella sessione SSH eseguire il comando per aggiornare il file config.yaml con la stringa di connessione del dispositivo.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Verranno quindi aggiornati i certificati e il nome host modificando direttamente il file config. yaml.

1. Aprire il file config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Aggiornare la sezione Certificates (certificati) del file config. YAML rimuovendo il **#** percorso e impostando il percorso in modo che il file sia simile all'esempio seguente:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Verificare che la riga **certificati:** non includa spazi vuoti precedenti e che ogni certificato annidato venga rientrato da due spazi.

    Fare clic con il pulsante destro del mouse su nano per incollare il contenuto degli Appunti nella posizione corrente del cursore. Per sostituire la stringa, usare le frecce della tastiera per passare alla stringa che si vuole sostituire, eliminare la stringa e quindi fare clic con il pulsante destro del mouse per incollare dal buffer.

1. Nella portale di Azure passare alla macchina virtuale. Copiare il nome DNS (FQDN del computer) dalla sezione **Panoramica**.

1. Incollare il nome di dominio completo nella sezione hostname del file config. yml. Verificare che il nome sia in lettere minuscole.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Salvare e chiudere il file selezionando **CTRL + X**, **Y** e **invio**.

1. Riavviare il daemon IoT Edge.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Verificare lo stato del daemon IoT Edge. Dopo il comando, immettere **: q** per uscire.

    ```bash
    systemctl status iotedge
    ```

1. Se si verificano errori (testo colorato con prefisso " \[ Error \] ") nello stato, esaminare i log del daemon per informazioni dettagliate sull'errore.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Pulire le risorse

Questa esercitazione fa parte di un set in cui ogni articolo si basa sul lavoro svolto nei precedenti. Attendere per pulire le risorse fino al completamento dell'esercitazione finale.

## <a name="next-steps"></a>Passaggi successivi

È stata appena completata la configurazione di una macchina virtuale di Azure come gateway trasparente IoT Edge. È stata avviata la generazione di certificati di test caricati in Key Vault. Successivamente, è stato usato uno script e un modello di Gestione risorse per distribuire la VM con l'immagine di runtime di Ubuntu Server 16,04 LTS + Azure IoT Edge da Azure Marketplace. Con la macchina virtuale in esecuzione, è stata eseguita la connessione tramite SSH. È stato quindi effettuato l'accesso ad Azure e sono stati scaricati i certificati da Key Vault. Sono stati apportati diversi aggiornamenti alla configurazione del runtime di IoT Edge aggiornando il file config. yaml.

Continuare con l'articolo successivo per creare i moduli IoT Edge.

> [!div class="nextstepaction"]
> [Creare e distribuire moduli IoT Edge personalizzati](tutorial-machine-learning-edge-06-custom-modules.md)
