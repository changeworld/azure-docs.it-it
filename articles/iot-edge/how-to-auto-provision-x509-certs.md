---
title: Effettuare automaticamente il provisioning dei dispositivi con DPS usando certificati X.509 - Azure IoT Edge | Microsoft Docs
description: Usare i certificati X.509 per testare il provisioning automatico dei dispositivi Azure IoT Edge con il servizio Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: f3c783c57b49b45943882703aec6d735d12bf830
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481957"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Creare ed effettuare il provisioning di IoT Edge dispositivo con certificati X.509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Con il [hub IoT di Azure Device Provisioning Service (DPS)](../iot-dps/index.yml)è possibile effettuare automaticamente il provisioning IoT Edge dispositivi usando certificati X.509. Se non si ha familiarità con il processo di provisioning automatico, vedere la panoramica sul [provisioning](../iot-dps/about-iot-dps.md#provisioning-process) prima di continuare.

Questo articolo illustra come creare una registrazione del servizio Device Provisioning usando certificati X.509 in un dispositivo IoT Edge con la procedura seguente:

* Generare certificati e chiavi.
* Creare una registrazione singola per un dispositivo o una registrazione di gruppo per un set di dispositivi.
* Installare il IoT Edge runtime e registrare il dispositivo con l'hub IoT.

L'uso di certificati X.509 come meccanismo di attestazione è un ottimo approccio per ridimensionare la produzione e semplificare il provisioning di dispositivi. In genere, i certificati X.509 sono disposti in una catena di certificati di attendibilità. A partire da un certificato radice autofirmato o attendibile, ogni certificato nella catena firma il certificato inferiore successivo. Questo modello crea una catena delegata di attendibilità dal certificato radice fino a ogni certificato intermedio fino al certificato "foglia" finale installato in un dispositivo.

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT attivo.
* Un dispositivo fisico o virtuale da IoT Edge dispositivo.
* La versione più recente di [Git](https://git-scm.com/download/) installata.
* Istanza del servizio Device Provisioning dell'hub IoT in Azure, collegata all'hub IoT.
  * Se non si ha un'istanza del servizio Device Provisioning, seguire le istruzioni in [Configurare il DPS dell'hub IoT.](../iot-dps/quick-setup-auto-provision.md)
  * Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="generate-device-identity-certificates"></a>Generare certificati di identità del dispositivo

Il certificato di identità del dispositivo è un certificato foglia che si connette tramite una catena di certificati di attendibilità al certificato dell'autorità di certificazione (CA) X.509 superiore. Il certificato di identità del dispositivo deve avere il nome comune (CN) impostato sull'ID dispositivo che si vuole che il dispositivo abbia nell'hub IoT.

I certificati di identità del dispositivo vengono usati solo per il provisioning IoT Edge dispositivo e l'autenticazione del dispositivo con hub IoT di Azure. Non firmano i certificati, a differenza dei certificati CA che il dispositivo IoT Edge presenta ai moduli o ai dispositivi foglia per la verifica. Per altre informazioni, vedere dettagli [sull'utilizzo Azure IoT Edge certificato.](iot-edge-certs.md)

Dopo aver creato il certificato di identità del dispositivo, è necessario avere due file: un file con estensione cer o pem contenente la parte pubblica del certificato e un file con estensione cer o pem con la chiave privata del certificato. Se si prevede di usare la registrazione di gruppo in DPS, è necessaria anche la parte pubblica di un certificato CA intermedio o radice nella stessa catena di certificati di attendibilità.

Per configurare il provisioning automatico con X.509 sono necessari i file seguenti:

* Il certificato di identità del dispositivo e il relativo certificato di chiave privata. Il certificato di identità del dispositivo viene caricato in DPS se si crea una registrazione singola. La chiave privata viene passata al IoT Edge runtime.
* Certificato a catena completo, che deve contenere almeno l'identità del dispositivo e i certificati intermedi. Il certificato della catena completa viene passato al IoT Edge runtime.
* Un certificato CA intermedio o radice dalla catena di certificati di attendibilità. Questo certificato viene caricato in DPS se si crea una registrazione di gruppo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> Attualmente, una limitazione in libiothsm impedisce l'uso di certificati che scadono il 1° gennaio 2038 o dopo il 1° gennaio 2038.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Usare certificati di test (facoltativo)

Se non è disponibile un'autorità di certificazione per creare nuovi certificati di identità e si vuole provare questo scenario, il repository Git di Azure IoT Edge contiene script che è possibile usare per generare certificati di test. Questi certificati sono progettati solo per i test di sviluppo e non devono essere usati nell'ambiente di produzione.

Per creare certificati di test, seguire la procedura descritta in Creare certificati demo per testare IoT Edge [funzionalità del dispositivo.](how-to-create-test-certificates.md) Completare le due sezioni necessarie per configurare gli script di generazione del certificato e per creare un certificato ca radice. Seguire quindi la procedura per creare un certificato di identità del dispositivo. Al termine, si dovrebbero avere la catena di certificati e la coppia di chiavi seguenti:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Entrambi i certificati sono necessari nel dispositivo IoT Edge dispositivo. Se si usa la registrazione singola in DPS, caricare il file con estensione cert.pem. Se si usa la registrazione di gruppo in DPS, è necessario anche un certificato CA intermedio o radice nella stessa catena di certificati di attendibilità per il caricamento. Se si usano certificati demo, usare il certificato per la `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` registrazione del gruppo.

## <a name="create-a-dps-individual-enrollment"></a>Creare una registrazione singola DPS

Usare i certificati e le chiavi generati per creare una registrazione singola in DPS per un singolo IoT Edge dispositivo. Le registrazioni singole accettano la parte pubblica del certificato di identità di un dispositivo e corrispondono al certificato nel dispositivo.

Se si sta cercando di effettuare il provisioning di IoT Edge dispositivi, seguire la procedura descritta nella sezione successiva Creare una registrazione del gruppo [DPS.](#create-a-dps-group-enrollment)

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-at-scale.md).

Per altre informazioni sulle registrazioni nel servizio Device Provisioning, vedere [Come gestire le registrazioni dei dispositivi.](../iot-dps/how-to-manage-enrollments.md)

   > [!TIP]
   > Nell'interfaccia della riga di [](/cli/azure/iot/dps/enrollment) comando [](/cli/azure/iot/dps/enrollment-group) di Azure è possibile creare una registrazione o un gruppo di registrazione e usare il **flag** abilitato per le funzionalità perimetrali per specificare che un dispositivo o un gruppo di dispositivi è un dispositivo IoT Edge dispositivo.

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device Provisioning dell'hub IoT.

1. In **le impostazioni** selezionare **Gestisci registrazioni**.

1. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   * **Meccanismo:** selezionare **X.509**.

   * **File con estensione pem o cer** del certificato primario: caricare il file pubblico dal certificato di identità del dispositivo. Se sono stati usati gli script per generare un certificato di test, scegliere il file seguente:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **ID dispositivo dell'hub IoT:** specificare un ID per il dispositivo, se lo si desidera. È possibile usare gli ID dispositivo per identificare come destinazione un singolo dispositivo per la distribuzione di moduli. Se non si specifica un ID dispositivo, viene usato il nome comune (CN) nel certificato X.509.

   * **IoT Edge dispositivo:** selezionare **True** per dichiarare che la registrazione è per un IoT Edge dispositivo.

   * **Selezionare gli hub IoT a** cui è possibile assegnare il dispositivo: scegliere l'hub IoT collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   * **Stato iniziale del dispositivo gemello:** aggiungere un valore di tag da aggiungere al dispositivo gemello, se si desidera. È possibile usare i tag per impostare come destinazione gruppi di dispositivi per la distribuzione automatica. Ad esempio:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selezionare **Salva**.

Ora che esiste una registrazione per questo dispositivo, il runtime IoT Edge eseguire automaticamente il provisioning del dispositivo durante l'installazione. Passare alla [sezione Installare il runtime IoT Edge](#install-the-iot-edge-runtime) per configurare il IoT Edge dispositivo.

## <a name="create-a-dps-group-enrollment"></a>Creare una registrazione del gruppo DPS

Usare i certificati e le chiavi generati per creare una registrazione di gruppo nel servizio Device IoT Edge dispositivi. Le registrazioni di gruppo usano un certificato CA intermedio o radice della catena di certificati usata per generare i singoli certificati di identità del dispositivo.

Se invece si sta cercando di effettuare il provisioning di un singolo dispositivo IoT Edge, seguire la procedura descritta nella sezione precedente Creare una registrazione singola del servizio Device [Provisioning.](#create-a-dps-individual-enrollment)

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-at-scale.md).

### <a name="verify-your-root-certificate"></a>Verificare il certificato radice

Quando si crea un gruppo di registrazioni, è possibile usare un certificato verificato. È possibile verificare un certificato con DPS dimostrando di avere la proprietà del certificato radice. Per altre informazioni, vedere [How to do proof-of-possession for X.509 CA certificates (Come eseguire il proof-of-possession per i certificati della CA X.509).](../iot-dps/how-to-verify-certificates.md)

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device Provisioning in hub IoT.

1. Selezionare **Certificati** dal menu a sinistra.

1. Selezionare **Aggiungi** per aggiungere un nuovo certificato.

1. Immettere un nome descrittivo per il certificato, quindi passare al file cer o pem che rappresenta la parte pubblica del certificato X.509.

   Se si usano i certificati demo, caricare il `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certificato.

1. Selezionare **Salva**.

1. Il certificato dovrebbe ora essere elencato nella **pagina** Certificati. Selezionarlo per aprire i dettagli del certificato.

1. Selezionare **Genera codice di verifica** e quindi copiare il codice generato.

1. Indipendentemente dal fatto che sia stato portato il proprio certificato ca o che si usino i certificati demo, è possibile usare lo strumento di verifica fornito nel repository IoT Edge per verificare la prova di possesso. Lo strumento di verifica usa il certificato della CA per firmare un nuovo certificato con il codice di verifica fornito come nome soggetto.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Nella stessa pagina dei dettagli del certificato nel portale di Azure caricare il certificato di verifica appena generato.

1. Selezionare **Verifica**.

### <a name="create-enrollment-group"></a>Creare un gruppo di registrazione

Per altre informazioni sulle registrazioni nel servizio Device Provisioning, vedere [Come gestire le registrazioni dei dispositivi.](../iot-dps/how-to-manage-enrollments.md)

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device Provisioning in hub IoT.

1. In **le impostazioni** selezionare **Gestisci registrazioni**.

1. Selezionare **Aggiungi gruppo di registrazione** e quindi completare la procedura seguente per configurare la registrazione:

   * **Nome gruppo:** specificare un nome facile da ricordare per la registrazione del gruppo.

   * **Tipo di attestazione:** selezionare **Certificato**.

   * **IoT Edge dispositivo**: selezionare **True**. Per la registrazione di un gruppo, tutti i dispositivi devono IoT Edge dispositivi o nessuno di essi può esserlo.

   * **Tipo di certificato:** selezionare **Certificato CA** se si dispone  di un certificato CA verificato archiviato con DPS o Certificato intermedio se si vuole caricare un nuovo file solo per questa registrazione.

   * **Certificato primario:** se è stato scelto il certificato della CA nell'ultima sezione, scegliere il certificato nell'elenco a discesa. Se si sceglie un certificato intermedio, caricare il file pubblico da un certificato CA nella catena di certificati di attendibilità usata per generare i certificati di identità del dispositivo.

   * **Selezionare gli hub IoT a** cui il dispositivo può essere assegnato: scegliere l'hub IoT collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   * **Stato iniziale del dispositivo gemello:** aggiungere un valore di tag da aggiungere al dispositivo gemello, se lo si desidera. È possibile usare i tag per impostare come destinazione gruppi di dispositivi per la distribuzione automatica. Ad esempio:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selezionare **Salva**.

Ora che esiste una registrazione per questo dispositivo, il runtime IoT Edge eseguire automaticamente il provisioning del dispositivo durante l'installazione. Passare alla sezione successiva per configurare il dispositivo IoT Edge dispositivo.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale.

Seguire la procedura descritta in [Installare il runtime Azure IoT Edge](how-to-install-iot-edge.md), quindi tornare a questo articolo per effettuare il provisioning del dispositivo.

Il provisioning X.509 con DPS è supportato solo IoT Edge versione 1.0.9 o successiva.

## <a name="configure-the-device-with-provisioning-information"></a>Configurare il dispositivo con le informazioni di provisioning

Dopo aver installato il runtime nel dispositivo, configurare il dispositivo con le informazioni che usa per connettersi al servizio Device Provisioning e all'hub IoT.

Disporre delle informazioni seguenti:

* Valore ambito **ID DPS.** È possibile recuperare questo valore dalla pagina di panoramica dell'istanza del servizio Data Services nel portale di Azure.
* File della catena di certificati di identità del dispositivo nel dispositivo.
* File della chiave di identità del dispositivo nel dispositivo.
* ID di registrazione facoltativo. Se non viene specificato, l'ID viene estratto dal nome comune nel certificato di identità del dispositivo.

### <a name="linux-device"></a>Dispositivo Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Aprire il file di configurazione nel IoT Edge dispositivo.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Trovare la sezione relativa alle configurazioni di provisioning del file. Rimuovere il commento delle righe per il provisioning del certificato X.509 DPS e assicurarsi che tutte le altre righe di provisioning siano commentate.

   La riga non deve contenere spazi vuoti precedenti e gli elementi annidati `provisioning:` devono essere rientrati di due spazi.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Aggiornare i valori di , e con il servizio Device Backup e `scope_id` `identity_cert` le informazioni sul `identity_pk` dispositivo.

   Quando si aggiungono il certificato X.509 e le informazioni sulla chiave al file config.yaml, i percorsi devono essere specificati come URI del file. Ad esempio:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Facoltativamente, specificare un `registration_id` per il dispositivo. In caso contrario, lasciare la riga come commento per registrare il dispositivo con il nome CN del certificato di identità.

1. Facoltativamente, usare le righe o per configurare il comportamento di `always_reprovision_on_startup` `dynamic_reprovisioning` reprovisioning del dispositivo. Se un dispositivo è impostato per il nuovo provisioning all'avvio, tenterà sempre di effettuare il provisioning con DPS e quindi eseguirà il fall back al backup di provisioning in caso di errore. Se un dispositivo è impostato per il nuovo provisioning dinamico, IoT Edge riavvio e di nuovo il provisioning se viene rilevato un evento di reprovisioning. Per altre informazioni, vedere Concetti relativi al [reprovisioning](../iot-dps/concepts-device-reprovision.md)dei dispositivi dell'hub IoT.

1. Salvare e chiudere il file config.yaml.

1. Riavviare il runtime IoT Edge in modo che accetti tutte le modifiche alla configurazione apportate nel dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Creare un file di configurazione per il dispositivo in base a un file modello fornito come parte dell IoT Edge installazione.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Aprire il file di configurazione nel IoT Edge dispositivo.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Trovare la **sezione Provisioning** del file. Rimuovere il commento delle righe per il provisioning DPS con il certificato X.509 e assicurarsi che tutte le altre righe di provisioning siano commentate.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Aggiornare i valori di `id_scope` , e con le informazioni sul dispositivo e sul `identity_cert` `identity_pk` DPS.

   Il valore del certificato di identità può essere fornito come URI di file o può essere rilasciato dinamicamente usando EST o un'autorità di certificazione locale. Rimuovere il commento da una sola riga, in base al formato che si sceglie di usare.

   Il valore della chiave privata identity può essere fornito come URI di file o PKCS#11. Rimuovere il commento da una sola riga, in base al formato che si sceglie di usare.

   Se si usano URI PKCS#11, trovare la sezione **PKCS#11** nel file di configurazione e fornire informazioni sulla configurazione PKCS#11.

1. Facoltativamente, specificare un `registration_id` per il dispositivo. In caso contrario, lasciare la riga come commento per registrare il dispositivo con il nome comune del certificato di identità.

1. Salvare e chiudere il file.

1. Applicare le modifiche di configurazione apportate a IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Dispositivo Windows

1. Aprire una finestra di PowerShell in modalità amministratore. Assicurarsi di usare una sessione AMD64 di PowerShell durante l'IoT Edge, non PowerShell (x86).

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. Per impostazione predefinita, il comando usa il provisioning manuale con contenitori Windows, quindi usare il flag per usare il provisioning automatico con l'autenticazione del certificato `-DpsX509` X.509.

   Sostituire i valori segnaposto per `{scope_id}` , e con i valori appropriati dall'istanza del servizio Device Backup `{identity cert chain path}` e i percorsi di file nel `{identity key path}` dispositivo.

   Aggiungere se si vuole impostare l'ID dispositivo come diverso `-RegistrationId {registration_id}` dal nome CN del certificato di identità.

   Aggiungere il `-ContainerOs Linux` parametro se si usano contenitori Linux in Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Il file di configurazione archivia le informazioni relative al certificato e alla chiave come URI di file. Tuttavia, il Initialize-IoTEdge di formattazione gestisce automaticamente questo passaggio di formattazione, quindi è possibile specificare il percorso assoluto dei file di certificato e di chiave nel dispositivo.

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se il runtime è stato avviato correttamente, è possibile passare all'hub IoT e iniziare la distribuzione di moduli IoT Edge nel dispositivo.

È possibile verificare che sia stata usata la registrazione singola creata nel servizio Device Provisioning. Passare all'istanza del servizio Device Provisioning nella portale di Azure. Aprire i dettagli di registrazione per la registrazione singola creata. Si noti che lo stato della registrazione è **assegnato e** l'ID dispositivo è elencato.

Usare i comandi seguenti sul dispositivo per verificare che il runtime sia stato installato e avviato correttamente.

### <a name="linux-device"></a>Dispositivo Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Verificare lo stato del servizio IoT Edge.

```cmd/sh
systemctl status iotedge
```

Esaminare i log del servizio.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Elencare i moduli in esecuzione.

```cmd/sh
iotedge list
```
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Verificare lo stato del servizio IoT Edge.

```cmd/sh
sudo iotedge system status
```

Esaminare i log del servizio.

```cmd/sh
sudo iotedge system logs
```

Elencare i moduli in esecuzione.

```cmd/sh
sudo iotedge list
```
:::moniker-end

### <a name="windows-device"></a>Dispositivo Windows

Verificare lo stato del servizio IoT Edge.

```powershell
Get-Service iotedge
```

Esaminare i log del servizio.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come distribuire [e monitorare i moduli IoT Edge su](how-to-deploy-at-scale.md) larga scala usando il portale di Azure o [l'interfaccia della riga di comando di Azure.](how-to-deploy-cli-at-scale.md)
