---
title: Gestire i certificati dei dispositivi-Azure IoT Edge | Microsoft Docs
description: Creare certificati di test, installarli e gestirli in un dispositivo Azure IoT Edge per prepararsi alla distribuzione di produzione.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e5c85d2c3049ea8718d0a9e0e574c13d0d99394c
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200268"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Gestire i certificati in un dispositivo IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Tutti i dispositivi IoT Edge usano i certificati per creare connessioni sicure tra il runtime e i moduli in esecuzione nel dispositivo. IoT Edge i dispositivi che funzionano come gateway usano questi stessi certificati per connettersi anche ai dispositivi downstream.

## <a name="install-production-certificates"></a>Installare i certificati di produzione

Quando si installa per la prima volta IoT Edge e si effettua il provisioning del dispositivo, il dispositivo viene configurato con certificati temporanei per poter testare il servizio.
Questi certificati temporanei scadono entro 90 giorni oppure possono essere reimpostati riavviando il computer.
Quando ci si sposta in uno scenario di produzione o si vuole creare un dispositivo gateway, è necessario fornire i propri certificati.
Questo articolo illustra i passaggi per installare i certificati nei dispositivi IoT Edge.

Per altre informazioni sui diversi tipi di certificati e sui relativi ruoli, vedere informazioni su [come Azure IOT Edge usa i certificati](iot-edge-certs.md).

>[!NOTE]
>Il termine "CA radice" usato in questo articolo si riferisce al certificato pubblico dell'autorità superiore della catena di certificati per la soluzione Internet delle cose. Non è necessario utilizzare la radice del certificato di un'autorità di certificazione con diffusore o la radice dell'autorità di certificazione dell'organizzazione. In molti casi, si tratta in realtà di un certificato pubblico CA intermedio.

### <a name="prerequisites"></a>Prerequisiti

* Un dispositivo IoT Edge.

  Se non si dispone di un dispositivo IoT Edge configurato, è possibile crearne uno in una macchina virtuale di Azure. Per [creare un dispositivo Linux virtuale](quickstart-linux.md) o [creare un dispositivo Windows virtuale](quickstart.md), seguire la procedura descritta in uno degli articoli introduttivi.

* Disporre di un certificato dell'autorità di certificazione (CA) radice, autofirmato o acquistato da un'autorità di certificazione commerciale attendibile come Baltimora, Verisign, DigiCert o GlobalSign.

  Se non si ha ancora un'autorità di certificazione radice, ma si vuole provare IoT Edge funzionalità che richiedono certificati di produzione (ad esempio, scenari di gateway), è possibile [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Creare certificati di produzione

Per creare i file seguenti, è necessario usare la propria autorità di certificazione:

* CA radice
* Certificato CA del dispositivo
* Chiave privata della CA del dispositivo

In questo articolo si fa riferimento alla *CA radice* non è l'autorità di certificazione in primo piano per un'organizzazione. Si tratta dell'autorità di certificazione superiore per lo scenario IoT Edge, che il modulo dell'hub IoT Edge, i moduli utente e tutti i dispositivi downstream usano per stabilire una relazione di trust tra loro.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Attualmente, una limitazione in libiothsm impedisce l'utilizzo di certificati che scadono il 1 ° gennaio 2038 o successivo.

:::moniker-end

Per visualizzare un esempio di questi certificati, esaminare gli script che creano certificati demo in [gestione dei certificati della CA di test per esempi ed esercitazioni](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Installare i certificati nel dispositivo

Installare la catena di certificati nel dispositivo IoT Edge e configurare il runtime IoT Edge per fare riferimento ai nuovi certificati.

Copiare i tre file di certificato e di chiave nel dispositivo IoT Edge. È possibile usare un servizio come [Azure Key Vault](../key-vault/index.yml) o una funzione come il [protocollo Secure Copy](https://www.ssh.com/ssh/scp/) per spostare i file di certificato.  Se i certificati sono stati generati nel dispositivo IoT Edge stesso, è possibile ignorare questo passaggio e usare il percorso della directory di lavoro.

Ad esempio, se sono stati usati gli script di esempio per [creare i certificati demo](how-to-create-test-certificates.md), copiare i file seguenti nel dispositivo IoT-Edge:

* Certificato CA dispositivo: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chiave privata CA del dispositivo: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* CA radice: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Aprire il file di configurazione del daemon di sicurezza di IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. Impostare le proprietà del **certificato** in config. YAML sul percorso dell'URI del file del certificato e dei file di chiave nel dispositivo IOT Edge. Rimuovere il `#` carattere prima delle proprietà del certificato per rimuovere il commento dalle quattro righe. Verificare che la riga **certificati:** non includa spazi vuoti precedenti e che gli elementi nidificati siano rientrati in due spazi. Ad esempio:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. Nei dispositivi Linux, assicurarsi che l'utente **iotedge** disponga delle autorizzazioni di lettura per la directory che contiene i certificati.

1. Se sono stati usati altri certificati per IoT Edge sul dispositivo prima, eliminare i file nelle due directory seguenti prima di avviare o riavviare IoT Edge:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` e `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` e `/var/lib/iotedge/hsm/cert_keys`
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Aprire il file di configurazione del daemon di sicurezza IoT Edge: `/etc/aziot/config.toml`

1. Trovare il `trust_bundle_cert` parametro all'inizio del file. Rimuovere il commento da questa riga e fornire l'URI del file al certificato CA radice nel dispositivo.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. Trovare la `[edge_ca]` sezione nel file config. toml. Rimuovere il commento dalle righe di questa sezione e fornire i percorsi URI del file per i file di certificato e di chiave nel dispositivo IoT Edge.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Assicurarsi che l'utente **iotedge** disponga delle autorizzazioni di lettura per la directory che contiene i certificati.

1. Se sono stati usati altri certificati per IoT Edge sul dispositivo prima, eliminare i file nelle due directory seguenti prima di avviare o riavviare IoT Edge:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1. -->
<!-- Temporarily, customizable certificate lifetime not available in 1.2. Update before GA. -->
:::moniker range="iotedge-2018-06"

## <a name="customize-certificate-lifetime"></a>Personalizzare la durata del certificato

IoT Edge genera automaticamente i certificati sul dispositivo in diversi casi, tra cui:

* Se non si forniscono certificati di produzione personalizzati quando si installa e si esegue il provisioning di IoT Edge, IoT Edge Security Manager genera automaticamente un **certificato della CA del dispositivo**. Questo certificato autofirmato è destinato solo a scenari di sviluppo e test, non alla produzione. Il certificato scade dopo 90 giorni.
* Il IoT Edge Security Manager genera anche un **certificato CA del carico di lavoro** firmato dal certificato della CA del dispositivo

Per altre informazioni sulla funzione dei diversi certificati in un dispositivo IoT Edge, vedere informazioni su [come Azure IOT Edge usa i certificati](iot-edge-certs.md).

Per questi due certificati generati automaticamente, è possibile impostare il flag **auto_generated_ca_lifetime_days** nel file di configurazione per configurare il numero di giorni per la durata dei certificati.

>[!NOTE]
>È disponibile un terzo certificato generato automaticamente creato dal gestore della sicurezza IoT Edge, il **certificato del server dell'hub IOT Edge**. Questo certificato ha sempre una durata di 90 giorni, ma viene rinnovato automaticamente prima della scadenza. Il valore di **auto_generated_ca_lifetime_days** non influisce sul certificato.

Alla scadenza dopo il numero di giorni specificato, è necessario riavviare IoT Edge per rigenerare il certificato della CA del dispositivo. Il certificato della CA del dispositivo non verrà rinnovato automaticamente.

1. Per configurare la scadenza del certificato su un valore diverso da quello predefinito di 90 giorni, aggiungere il valore in giorni alla sezione **certificati** del file di configurazione.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Attualmente, una limitazione in libiothsm impedisce l'utilizzo di certificati che scadono il 1 ° gennaio 2038 o successivo.

1. Eliminare il contenuto della `hsm` cartella per rimuovere tutti i certificati generati in precedenza.

   Windows: `C:\ProgramData\iotedge\hsm\certs` e `C:\ProgramData\iotedge\hsm\cert_keys` Linux: `/var/lib/iotedge/hsm/certs` e `/var/lib/iotedge/hsm/cert_keys`

1. Riavviare il servizio IoT Edge.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Confermare l'impostazione della durata.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Controllare l'output della preparazione per la **produzione: verifica certificati** , che elenca il numero di giorni prima della scadenza dei certificati della CA del dispositivo generati automaticamente.

:::moniker-end
<!-- end 1.1 -->

<!-- 
<!-- 1.2 --
:::moniker range=">=iotedge-2020-11"

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```toml
   [certificates]
   device_ca_cert = "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
   device_ca_pk = "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
   trusted_ca_certs = "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
   auto_generated_ca_lifetime_days = <value>
   ```

1. Delete the contents of the `certd` and `keyd` folders to remove any previously generated certificates: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

1. Confirm the new lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.
:::moniker-end
<!-- end 1.2 --
-->

## <a name="next-steps"></a>Passaggi successivi

L'installazione dei certificati in un dispositivo IoT Edge è un passaggio necessario prima di distribuire la soluzione nell'ambiente di produzione. Scopri di più su come [preparare la distribuzione della tua IOT Edge soluzione nell'](production-checklist.md)ambiente di produzione.
