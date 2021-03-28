---
title: Installare Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge le istruzioni di installazione nei dispositivi Windows o Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: kgremban
ms.openlocfilehash: a98eed61904b580988fe34302999f3ec6a24ac9e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640918"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Installare o disinstallare Azure IoT Edge per Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

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

## <a name="install-iot-edge"></a>Installare IoT Edge

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Il daemon di sicurezza di IoT Edge fornisce e gestisce gli standard di sicurezza sul dispositivo IoT Edge. Il daemon viene avviato a ogni avvio del dispositivo e ne esegue il bootstrap avviando la parte restante del runtime IoT Edge.

I passaggi descritti in questa sezione rappresentano il processo tipico per installare la versione più recente in un dispositivo con connessione a Internet. Se è necessario installare una versione specifica, ad esempio una versione non definitiva o se è necessario installarla mentre è offline, seguire i passaggi di [installazione di versioni offline o specifiche](#offline-or-specific-version-installation-optional) più avanti in questo articolo.

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

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Il servizio IoT Edge fornisce e gestisce gli standard di sicurezza sul dispositivo IoT Edge. Il servizio viene avviato a ogni avvio e bootstrap del dispositivo avviando il resto del runtime del IoT Edge.

Il servizio di identità Internet delle cose è stato introdotto insieme alla versione 1,2 di IoT Edge. Questo servizio gestisce il provisioning e la gestione delle identità per IoT Edge e per altri componenti del dispositivo che devono comunicare con l'hub Internet.

I passaggi descritti in questa sezione rappresentano il processo tipico per installare la versione più recente in un dispositivo con connessione a Internet. Se è necessario installare una versione specifica, ad esempio una versione non definitiva o se è necessario installarla mentre è offline, seguire i passaggi di [installazione di versioni offline o specifiche](#offline-or-specific-version-installation-optional) più avanti in questo articolo.

>[!NOTE]
>I passaggi descritti in questa sezione illustrano come installare IoT Edge versione 1,2, attualmente disponibile in anteprima pubblica. Se si stanno cercando i passaggi per installare la versione più recente disponibile a livello generale di IoT Edge, vedere la versione [1,1 (LTS)](?view=iotedge-2018-06&preserve-view=true) di questo articolo.
>
>Se si dispone già di un dispositivo IoT Edge che esegue una versione precedente e si desidera eseguire l'aggiornamento a 1,2, attenersi alla procedura descritta in [aggiornare il daemon di sicurezza di IOT Edge e il runtime](how-to-update-iot-edge.md). La versione 1,2 è sufficientemente diversa dalle versioni precedenti di IoT Edge che sono necessari passaggi specifici per l'aggiornamento.

Aggiornare gli elenchi di pacchetti nel dispositivo.

   ```bash
   sudo apt-get update
   ```

Verificare quali versioni di IoT Edge sono disponibili.

   ```bash
   apt list -a aziot-edge
   ```

Se si desidera installare la versione più recente di IoT Edge, utilizzare il comando seguente che installa anche la versione più recente del pacchetto del servizio di identità:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- commenting out for public preview. reintroduce at GA

Or, if you want to install a specific version of IoT Edge and the identity service, specify the versions from the apt list output. Specify the same versions for both services.. For example, the following command installs the most recent version of the 1.2 release:

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

-->

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Eseguire il provisioning del dispositivo con la relativa identità cloud

Ora che il motore del contenitore e il runtime di IoT Edge sono installati nel dispositivo, si è pronti per il passaggio successivo, ovvero per configurare il dispositivo con le informazioni di autenticazione e identità cloud.

Scegliere la sezione successiva in base al tipo di autenticazione che si vuole usare:

* [Opzione 1: eseguire l'autenticazione con chiavi simmetriche](#option-1-authenticate-with-symmetric-keys)
* [Opzione 2: eseguire l'autenticazione con certificati X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opzione 1: eseguire l'autenticazione con chiavi simmetriche

A questo punto, il runtime di IoT Edge viene installato nel dispositivo Linux ed è necessario eseguire il provisioning del dispositivo con le informazioni di autenticazione e identità cloud.

Questa sezione illustra i passaggi per eseguire il provisioning di un dispositivo con autenticazione con chiave simmetrica. È necessario che il dispositivo sia stato registrato nell'hub Internet e che sia stata recuperata la stringa di connessione dalle informazioni sul dispositivo. In caso contrario, seguire la procedura descritta in [registrare un dispositivo IOT Edge nell'hub](how-to-register-device.md)Internet.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Nel dispositivo IoT Edge aprire il file di configurazione.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Trovare le configurazioni del provisioning del file e rimuovere il commento dalla **configurazione del provisioning manuale usando una** sezione della stringa di connessione, se non è già stata annullata.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Aggiornare il valore di **device_connection_string** con la stringa di connessione del dispositivo IoT Edge. Verificare che tutte le altre sezioni di provisioning siano impostate come commento. Verificare che il **provisioning:** la riga non includa spazi vuoti precedenti e che gli elementi nidificati vengano rientrati da due spazi.

Per incollare il contenuto degli Appunti in Nano, premere `Shift+Right Click` o `Shift+Insert`.

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, riavviare il daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Creare il file di configurazione per il dispositivo in base a un file di modello fornito come parte dell'installazione del IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Nel dispositivo IoT Edge aprire il file di configurazione.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Trovare la sezione **provisioning** del file e rimuovere il commento dal provisioning manuale con le righe della stringa di connessione.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Aggiornare il valore di **CONNECTION_STRING** con la stringa di connessione dal dispositivo IOT Edge.

Per incollare il contenuto degli Appunti in Nano, premere `Shift+Right Click` o `Shift+Insert`.

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, applicare le modifiche:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>Opzione 2: eseguire l'autenticazione con certificati X. 509

A questo punto, il runtime di IoT Edge viene installato nel dispositivo Linux ed è necessario eseguire il provisioning del dispositivo con le informazioni di autenticazione e identità cloud.

Questa sezione illustra i passaggi per eseguire il provisioning di un dispositivo con l'autenticazione del certificato X. 509. È necessario aver registrato il dispositivo nell'hub Internet, fornendo le identificazioni personali che corrispondono al certificato e alla chiave privata presenti nel dispositivo IoT Edge. In caso contrario, seguire la procedura descritta in [registrare un dispositivo IOT Edge nell'hub](how-to-register-device.md)Internet.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Nel dispositivo IoT Edge aprire il file di configurazione.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Trovare la sezione relativa alle configurazioni del provisioning del file e rimuovere il commento dalla **configurazione del provisioning manuale usando una sezione relativa al certificato di identità X. 509** . Verificare che tutte le altre sezioni di provisioning siano impostate come commento. Verificare che il **provisioning:** la riga non includa spazi vuoti precedenti e che gli elementi nidificati vengano rientrati da due spazi.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
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

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Creare il file di configurazione per il dispositivo in base a un file di modello fornito come parte dell'installazione del IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Nel dispositivo IoT Edge aprire il file di configurazione.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Trovare la sezione **provisioning** del file e rimuovere il commento dalle righe per il provisioning manuale con il certificato di identità X. 509. Verificare che tutte le altre sezioni di provisioning siano impostate come commento.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Aggiornare i campi seguenti:

* **iothub_hostname**: nome host dell'hub Internet delle cose a cui si connetterà il dispositivo. Ad esempio: `{IoT hub name}.azure-devices.net`.
* **DEVICE_ID**: ID specificato durante la registrazione del dispositivo.
* **identity_cert**: URI di un certificato di identità sul dispositivo, ad esempio: `file:///path/identity_certificate.pem` . In alternativa, emettere in modo dinamico il certificato usando EST o un'autorità di certificazione locale.
* **identity_pk**: URI del file di chiave privata per il certificato di identità fornito, ad esempio: `file:///path/identity_key.pem` . In alternativa, specificare un URI PKCS # 11 e quindi fornire le informazioni di configurazione nella sezione **PKCS # 11** più avanti nel file config.

Salvare e chiudere il file.

   `CTRL + X`, `Y`, `Enter`

Dopo aver immesso le informazioni di provisioning nel file di configurazione, applicare le modifiche:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Verificare la corretta configurazione

Verificare che il runtime sia stato installato e configurato correttamente nel dispositivo IoT Edge.

>[!TIP]
>Per eseguire comandi `iotedge` sono necessari privilegi elevati. Quando ci si disconnette dal computer e si accede di nuovo per la prima volta dopo l'installazione del runtime IoT Edge, le autorizzazioni vengono aggiornate automaticamente. Fino ad allora, usare `sudo` davanti ai comandi.

Verificare che il servizio di sistema IoT Edge sia in esecuzione.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

Una risposta di stato corretta è `Ok` .

::: moniker-end

Se è necessario risolvere problemi del servizio, recuperare i log di servizio.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Usare lo `check` strumento per verificare lo stato di configurazione e connessione del dispositivo.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Usare sempre `sudo` per eseguire lo strumento di controllo anche dopo l'aggiornamento delle autorizzazioni. Lo strumento necessita di privilegi elevati per accedere al file di configurazione per verificare lo stato della configurazione.

Visualizzare tutti i moduli in esecuzione nel dispositivo IoT Edge. Quando il servizio viene avviato per la prima volta, dovrebbe essere visualizzato solo il modulo **edgeAgent** in esecuzione. Il modulo edgeAgent viene eseguito per impostazione predefinita e permette di installare e avviare tutti i moduli aggiuntivi distribuiti nel dispositivo.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Installazione di versioni offline o specifiche (facoltativo)

I passaggi descritti in questa sezione si riferiscono a scenari non inclusi nei passaggi di installazione standard. Questo può includere:

* Installare IoT Edge offline
* Installare una versione finale candidata

Usare i passaggi in questa sezione se si vuole installare una versione specifica del runtime di Azure IoT Edge che non è disponibile tramite `apt-get install` . L'elenco di pacchetti Microsoft contiene solo un set limitato di versioni recenti e le relative versioni secondarie, quindi questi passaggi sono destinati a chiunque voglia installare una versione precedente o una versione finale candidata.

Usando i comandi CURL, è possibile indirizzare i file dei componenti direttamente dal repository GitHub IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Passare alla [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases)e individuare la versione di rilascio di destinazione.

2. Espandere la sezione **Asset** per la versione.

3. Ogni versione deve avere nuovi file per il daemon di sicurezza IoT Edge e hsmlib. Se si intende installare IoT Edge in un dispositivo offline, scaricare questi file in anticipo. In caso contrario, usare i comandi seguenti per aggiornare tali componenti.

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

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Se il dispositivo è attualmente in esecuzione IoT Edge versione 1,1 o precedente, disinstallare i pacchetti **iotedge** e **libiothsm-STD** prima di seguire i passaggi descritti in questa sezione. Per ulteriori informazioni, vedere [aggiornamento da 1,0 o 1,1 a 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Passare alla [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases)e individuare la versione di rilascio di destinazione.

2. Espandere la sezione **Asset** per la versione.

3. Ogni versione deve avere nuovi file per IoT Edge e il servizio di identità. Se si intende installare IoT Edge in un dispositivo offline, scaricare questi file in anticipo. In caso contrario, usare i comandi seguenti per aggiornare tali componenti.

   1. Trovare il file **aziot-Identity-Service** che corrisponde all'architettura del dispositivo IOT Edge. Fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento.

   2. Usare il collegamento copiato nel comando seguente per installare la versione del servizio di identità:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. Trovare il file **aziot-Edge** corrispondente all'architettura del dispositivo IOT Edge. Fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento.

   4. Usare il collegamento copiato nel comando seguente per installare la versione di IoT Edge.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Ora che il motore del contenitore e il runtime di IoT Edge sono installati nel dispositivo, si è pronti per il passaggio successivo, ovvero per eseguire il [provisioning del dispositivo con la relativa identità cloud](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

Se si vuole rimuovere l'installazione IoT Edge dal dispositivo, usare i comandi seguenti.

Rimuovere il runtime IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Utilizzare il `--purge` flag se si desidera eliminare tutti i file associati a IOT Edge, inclusi i file di configurazione. Lasciare questo flag se si desidera reinstallare IoT Edge e utilizzare le stesse informazioni di configurazione in futuro.

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
