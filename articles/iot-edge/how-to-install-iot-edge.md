---
title: Installare Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge le istruzioni di installazione nei dispositivi Windows o Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
ms.openlocfilehash: efbae71162bdd0c126287191f7ad35cf903db138
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378078"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Installare o disinstallare Azure IoT Edge per Linux

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Quando un dispositivo viene configurato con il runtime IoT Edge, è possibile iniziare a distribuirvi la logica di business dal cloud. Per altre informazioni, vedere [comprendere il runtime di Azure IOT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge nei dispositivi Linux.

## <a name="prerequisites"></a>Prerequisiti

* [ID dispositivo registrato](how-to-register-device.md)

  Se il dispositivo è stato registrato con l'autenticazione con chiave simmetrica, è possibile usare la stringa di connessione del dispositivo.

  Se il dispositivo è stato registrato con l'autenticazione con certificato autofirmato X. 509, avere almeno uno dei certificati di identità usati per registrare il dispositivo e la relativa chiave privata corrispondente disponibile nel dispositivo.

* Un dispositivo Linux

  Avere un dispositivo Linux x64, ARM32 o ARM64. Microsoft fornisce i pacchetti di installazione per i sistemi operativi di estensione del sistema operativo Ubuntu server 18,04 e Raspberry Pi.

  Per informazioni aggiornate sui sistemi operativi attualmente supportati per gli scenari di produzione, vedere [Azure IOT Edge sistemi supportati](support.md#operating-systems)

  >[!NOTE]
  >Il supporto per i dispositivi ARM64 è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Preparare il dispositivo per l'accesso ai pacchetti di installazione Microsoft.

  Installare la configurazione del repository che corrisponde al sistema operativo del dispositivo.

  * **Ubuntu Server 18.04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS Stretch**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Copiare l'elenco generato nella directory sources.list.d.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Installare la chiave pubblica GPG Microsoft.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

I pacchetti software Azure IoT Edge sono soggetti alle condizioni di licenza presenti in ogni pacchetto ( `usr/share/doc/{package-name}` o `LICENSE` Directory). Leggere le condizioni di licenza prima di utilizzare un pacchetto. L'installazione e l'utilizzo di un pacchetto costituiscono l'accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non utilizzare il pacchetto.

## <a name="install-a-container-engine"></a>Installare un motore di contenitori

Azure IoT Edge si basa su un runtime per contenitori compatibile con OCI. Per gli scenari di produzione, è consigliabile usare il motore Moby. Il motore Moby è l'unico motore di contenitori supportato ufficialmente con Azure IoT Edge. Le immagini del contenitore Docker CE/EE sono compatibili con il runtime di Moby.

Aggiornare gli elenchi di pacchetti nel dispositivo.

   ```bash
   sudo apt-get update
   ```

Installare il motore Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Se si verificano errori durante l'installazione del motore di contenitori di Moby, verificare il kernel Linux per la compatibilità con Moby. Alcuni produttori di dispositivi incorporati spediscono immagini del dispositivo che contengono kernel Linux personalizzati senza le funzionalità richieste per la compatibilità del motore del contenitore. Eseguire il comando seguente, che usa lo [script Check-Config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) fornito da Moby per verificare la configurazione del kernel:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Nell'output dello script verificare che tutti gli elementi in `Generally Necessary` e `Network Drivers` siano abilitati. Se mancano funzionalità, abilitarle ricompilando il kernel dall'origine e selezionando i moduli associati da includere nel file kernel. config appropriato. Analogamente, se si usa un generatore di configurazione kernel come `defconfig` o `menuconfig` , trovare e abilitare le rispettive funzionalità e ricompilare il kernel di conseguenza. Dopo aver distribuito il kernel appena modificato, eseguire di nuovo lo script Check-config per verificare che tutte le funzionalità necessarie siano state abilitate correttamente.

## <a name="install-the-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di IoT Edge

Il daemon di sicurezza di IoT Edge fornisce e gestisce gli standard di sicurezza sul dispositivo IoT Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando la parte restante del runtime IoT Edge.

I passaggi descritti in questa sezione rappresentano il processo tipico per installare la versione più recente in un dispositivo con connessione a Internet. Se è necessario installare una versione specifica, ad esempio una versione non definitiva o se è necessario installarla mentre è offline, seguire i passaggi di [installazione della versione offline o specifica](#offline-or-specific-version-installation-optional) nella sezione successiva.

Aggiornare gli elenchi di pacchetti nel dispositivo.

   ```bash
   sudo apt-get update
   ```

Verificare quali versioni di IoT Edge sono disponibili.

   ```bash
   apt list -a iotedge
   ```

Se si vuole installare la versione più recente del daemon di sicurezza, usare il comando seguente che installa anche la versione più recente del pacchetto **libiothsm-STD** :

   ```bash
   sudo apt-get install iotedge
   ```

In alternativa, se si vuole installare una versione specifica del daemon di sicurezza, specificare la versione dall'output dell'elenco apt. Specificare anche la stessa versione per il pacchetto **libiothsm-STD** , che altrimenti installerebbe la versione più recente. Ad esempio, il comando seguente installa la versione più recente della versione di 1.0.10:

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

Se la versione che si vuole installare non è elencata, seguire la procedura di [installazione offline o specifica della versione](#offline-or-specific-version-installation-optional) più avanti in questo articolo. In questa sezione viene illustrato come specificare come destinazione qualsiasi versione precedente del daemon di sicurezza IoT Edge o versione finale candidata.

## <a name="provision-the-device-with-its-cloud-identity"></a>Eseguire il provisioning del dispositivo con la relativa identità cloud

Ora che il motore del contenitore e il runtime di IoT Edge sono installati nel dispositivo, si è pronti per il passaggio successivo, ovvero per configurare il dispositivo con le informazioni di autenticazione e identità cloud.

Scegliere la sezione successiva in base al tipo di autenticazione che si vuole usare:

* [Opzione 1: eseguire l'autenticazione con chiavi simmetriche](#option-1-authenticate-with-symmetric-keys)
* [Opzione 2: eseguire l'autenticazione con certificati X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opzione 1: eseguire l'autenticazione con chiavi simmetriche

A questo punto, il runtime di IoT Edge viene installato nel dispositivo Linux ed è necessario eseguire il provisioning del dispositivo con le informazioni di autenticazione e identità cloud.

Questa sezione illustra i passaggi per eseguire il provisioning di un dispositivo con autenticazione con chiave simmetrica. È necessario che il dispositivo sia stato registrato nell'hub Internet e che sia stata recuperata la stringa di connessione dalle informazioni sul dispositivo. In caso contrario, seguire la procedura descritta in [registrare un dispositivo IOT Edge nell'hub](how-to-register-device.md)Internet.

Nel dispositivo IoT Edge aprire il file di configurazione.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Trovare le configurazioni del provisioning del file e rimuovere il commento dalla sezione **Manual provisioning configuration using a connection string**.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge. Verificare che tutte le altre sezioni di provisioning siano impostate come commento. Verificare che il **provisioning:** la riga non includa spazi vuoti precedenti e che gli elementi nidificati vengano rientrati da due spazi.

Per incollare il contenuto degli Appunti in Nano, premere `Shift+Right Click` o `Shift+Insert`.

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="option-2-authenticate-with-x509-certificates"></a>Opzione 2: eseguire l'autenticazione con certificati X. 509

A questo punto, il runtime di IoT Edge viene installato nel dispositivo Linux ed è necessario eseguire il provisioning del dispositivo con le informazioni di autenticazione e identità cloud.

Questa sezione illustra i passaggi per eseguire il provisioning di un dispositivo con l'autenticazione del certificato X. 509. È necessario aver registrato il dispositivo nell'hub Internet, fornendo le identificazioni personali che corrispondono al certificato e alla chiave privata presenti nel dispositivo IoT Edge. In caso contrario, seguire la procedura descritta in [registrare un dispositivo IOT Edge nell'hub](how-to-register-device.md)Internet.

Nel dispositivo IoT Edge aprire il file di configurazione.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Trovare la sezione relativa alle configurazioni del provisioning del file e rimuovere il commento dalla **configurazione del provisioning manuale usando una sezione relativa al certificato di identità X. 509** . Verificare che tutte le altre sezioni di provisioning siano impostate come commento. Verificare che il **provisioning:** la riga non includa spazi vuoti precedenti e che gli elementi nidificati vengano rientrati da due spazi.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

Aggiornare i campi seguenti:

* **iothub_hostname**: nome host dell'hub Internet delle cose a cui si connetterà il dispositivo. Ad esempio: `{IoT hub name}.azure-devices.net`.
* **DEVICE_ID**: ID specificato durante la registrazione del dispositivo.
* **identity_cert**: URI di un certificato di identità nel dispositivo. Ad esempio: `file:///path/identity_certificate.pem`.
* **identity_pk**: URI del file di chiave privata per il certificato di identità fornito. Ad esempio: `file:///path/identity_key.pem`.

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-successful-configuration"></a>Verificare la corretta configurazione

Verificare che il runtime sia stato installato e configurato correttamente nel dispositivo IoT Edge.

1. Verificare che il daemon di sicurezza IoT Edge sia in esecuzione come servizio di sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   >[!TIP]
   >Per eseguire comandi `iotedge` sono necessari privilegi elevati. Quando ci si disconnette dal computer e si accede di nuovo per la prima volta dopo l'installazione del runtime IoT Edge, le autorizzazioni vengono aggiornate automaticamente. Fino ad allora, usare `sudo` davanti ai comandi.

2. Se è necessario risolvere problemi del servizio, recuperare i log di servizio.

   ```bash
   journalctl -u iotedge
   ```

3. Usare lo `check` strumento per verificare lo stato di configurazione e connessione del dispositivo.

   ```bash
   sudo iotedge check
   ```

   >[!TIP]
   >Usare sempre `sudo` per eseguire lo strumento di controllo anche dopo l'aggiornamento delle autorizzazioni. Lo strumento necessita di privilegi elevati per accedere al file **config. YAML** per verificare lo stato della configurazione.

4. Visualizzare tutti i moduli in esecuzione nel dispositivo IoT Edge. Quando il servizio viene avviato per la prima volta, dovrebbe essere visualizzato solo il modulo **edgeAgent** in esecuzione. Il modulo edgeAgent viene eseguito per impostazione predefinita e permette di installare e avviare tutti i moduli aggiuntivi distribuiti nel dispositivo.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Installazione di versioni offline o specifiche (facoltativo)

I passaggi descritti in questa sezione si riferiscono a scenari non inclusi nei passaggi di installazione standard. Questo può includere:

* Installare IoT Edge offline
* Installare una versione finale candidata

Usare i passaggi in questa sezione se si vuole installare una versione specifica del runtime di Azure IoT Edge che non è disponibile tramite `apt-get install` . L'elenco di pacchetti Microsoft contiene solo un set limitato di versioni recenti e le relative versioni secondarie, quindi questi passaggi sono destinati a chiunque voglia installare una versione precedente o una versione finale candidata.

Usando i comandi CURL, è possibile indirizzare i file dei componenti direttamente dal repository GitHub IoT Edge.

1. Passare alla [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases)e individuare la versione di rilascio di destinazione.

2. Espandere la sezione **Asset** per la versione.

3. Ogni versione deve avere nuovi file per il daemon di sicurezza IoT Edge e hsmlib. Usare i comandi seguenti per aggiornare tali componenti.

   1. Trovare il file **libiothsm-STD** che corrisponde all'architettura del dispositivo IOT Edge. Fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento.

   2. Usare il collegamento copiato nel comando seguente per installare la versione di hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Trovare il file **iotedge** corrispondente all'architettura del dispositivo IOT Edge. Fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento.

   4. Usare il collegamento copiato nel comando seguente per installare la versione del daemon di sicurezza IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Ora che il motore del contenitore e il runtime di IoT Edge sono installati nel dispositivo, si è pronti per il passaggio successivo, ovvero per eseguire il [provisioning del dispositivo con la relativa identità cloud](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

Se si vuole rimuovere l'installazione IoT Edge dal dispositivo, usare i comandi seguenti.

Rimuovere il runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Quando il runtime di IoT Edge viene rimosso, tutti i contenitori creati vengono arrestati, ma ancora presenti nel dispositivo. Verificare tutti i contenitori per vedere quali vengono conservati.

```bash
sudo docker ps -a
```

Eliminare i contenitori dal dispositivo, inclusi i due contenitori di runtime.

```bash
sudo docker rm -f <container name>
```

Infine, rimuovere il runtime del contenitore dal dispositivo.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Passaggi successivi

Continuare a [distribuire i moduli IOT Edge](how-to-deploy-modules-portal.md) per informazioni su come distribuire i moduli nel dispositivo.
