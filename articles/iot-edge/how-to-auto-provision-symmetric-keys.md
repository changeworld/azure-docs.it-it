---
title: Effettuare il provisioning del dispositivo usando l'attestazione della chiave simmetrica - Azure IoT Edge
description: Usare l'attestazione della chiave simmetrica per testare il provisioning automatico dei dispositivi Azure IoT Edge con il servizio Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66e1e561c14b169d41028e151ac054888830b881
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481974"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Creare ed effettuare il provisioning di un IoT Edge usando l'attestazione della chiave simmetrica

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge il provisioning automatico dei dispositivi tramite il servizio [Device Provisioning,](../iot-dps/index.yml) proprio come i dispositivi che non sono abilitati per i dispositivi perimetrali. Se non si ha familiarità con il processo di provisioning automatico, vedere la panoramica sul [provisioning](../iot-dps/about-iot-dps.md#provisioning-process) prima di continuare.

Questo articolo illustra come creare una registrazione individuale o di gruppo del servizio Device Provisioning usando l'attestazione della chiave simmetrica in un dispositivo IoT Edge con la procedura seguente:

* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare una registrazione per il dispositivo.
* Installare il IoT Edge runtime e connettersi all'hub IoT.

L'attestazione con chiave simmetrica costituisce un approccio semplice per autenticare un dispositivo con un'istanza del servizio Device Provisioning. Questo metodo di attestazione rappresenta un'esperienza di "Hello World" per gli sviluppatori che non hanno familiarità con il provisioning dei dispositivi o che non possiedono requisiti di sicurezza restrittivi. L'attestazione del dispositivo con certificati [TPM](../iot-dps/concepts-tpm-attestation.md) o [X.509](../iot-dps/concepts-x509-attestation.md) è più sicura e deve essere usata per requisiti di sicurezza più stringenti.

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT attivo
* Un dispositivo fisico o virtuale

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Scegliere un ID di registrazione univoco per il dispositivo

È necessario definire un ID di registrazione univoco per identificare ogni dispositivo. È possibile usare l'indirizzo MAC, il numero di serie o informazioni univoche dal dispositivo.

In questo esempio viene utilizzata una combinazione di un indirizzo MAC e un numero di serie che formano la stringa seguente per un ID di registrazione: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Creare un ID di registrazione univoco per il dispositivo. I caratteri validi sono i caratteri alfanumerici minuscoli e il trattino ("-").

## <a name="create-a-dps-enrollment"></a>Creare una registrazione nel servizio Device Provisioning

Usare l'ID registrazione del dispositivo per creare una registrazione singola in DPS.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-at-scale.md).

> [!TIP]
> Le registrazioni di gruppo sono possibili anche quando si usa l'attestazione della chiave simmetrica e comportano le stesse decisioni delle registrazioni singole.

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device Provisioning in hub IoT.

1. In **le impostazioni** selezionare **Gestisci registrazioni**.

1. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   1. Per **Meccanismo** selezionare **Chiave simmetrica**.

   1. Selezionare la **casella di controllo Genera automaticamente** chiavi .

   1. Specificare **l'ID** registrazione creato per il dispositivo.

   1. Se si **desidera, specificare un ID** dispositivo dell'hub IoT per il dispositivo. È possibile usare gli ID dispositivo per identificare come destinazione un singolo dispositivo per la distribuzione di moduli. Se non si specifica un ID dispositivo, viene usato l'ID di registrazione.

   1. Selezionare **True** per dichiarare che la registrazione è per un IoT Edge dispositivo. Per la registrazione di un gruppo, tutti i dispositivi devono IoT Edge dispositivi o nessuno di essi può esserlo.

      > [!TIP]
      > Nell'interfaccia della riga di [](/cli/azure/iot/dps/enrollment) comando [](/cli/azure/iot/dps/enrollment-group) di Azure è possibile creare una registrazione o un gruppo di registrazione e usare il flag abilitato per **Edge** per specificare che un dispositivo, o gruppo di dispositivi, è un dispositivo IoT Edge dispositivo.

   1. Accettare il valore predefinito dei criteri di allocazione del servizio Device Provisioning per la modalità di assegnazione dei dispositivi **agli hub** o scegliere un valore diverso specifico per questa registrazione.

   1. Scegliere l'**hub IoT** collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   1. Scegliere **come si vuole che i dati dei dispositivi siano gestiti** al nuovo provisioning quando i dispositivi richiedono il provisioning dopo la prima volta.

   1. Aggiungere un valore di tag allo **stato dispositivo gemello iniziale**, se si desidera. È possibile usare tag per identificare come destinazione gruppi di dispositivi per la distribuzione di moduli. Ad esempio:

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

   1. Assicurarsi **che l'opzione Abilita** sia impostata su **Abilita**.

   1. Selezionare **Salva**.

Ora che esiste una registrazione per questo dispositivo, il runtime IoT Edge eseguire automaticamente il provisioning del dispositivo durante l'installazione. Assicurarsi di copiare il  valore della chiave primaria della registrazione da usare durante l'installazione del runtime di IoT Edge o se si stanno creando chiavi del dispositivo da usare con una registrazione di gruppo.

## <a name="derive-a-device-key"></a>Derivare una chiave di dispositivo

> [!NOTE]
> Questa sezione è necessaria solo se si usa una registrazione di gruppo.

Ogni dispositivo usa la chiave del dispositivo derivata con l'ID di registrazione univoco per eseguire l'attestazione della chiave simmetrica con la registrazione durante il provisioning. Per generare la chiave del dispositivo, usare la chiave copiata dalla registrazione DPS per calcolare [un valore HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID di registrazione univoco per il dispositivo e convertire il risultato in formato Base64.

Non includere la chiave primaria o secondaria della registrazione nel codice del dispositivo.

### <a name="linux-workstations"></a>Workstation di Linux

Se si usa una workstation di Linux, è possibile usare openssl per generare la chiave di dispositivo derivata come illustrato nell'esempio seguente.

Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

Sostituire il valore di **REG_ID** con l'ID registrazione del dispositivo.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Workstation basate su Windows

Se si usan una workstation basata su Windows, è possibile usare PowerShell per generare le chiavi di dispositivo derivate come illustrato nell'esempio seguente.

Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

Sostituire il valore di **REG_ID** con l'ID registrazione del dispositivo.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale.

Seguire la procedura descritta in [Installare il runtime Azure IoT Edge](how-to-install-iot-edge.md), quindi tornare a questo articolo per effettuare il provisioning del dispositivo.

## <a name="configure-the-device-with-provisioning-information"></a>Configurare il dispositivo con le informazioni di provisioning

Dopo aver installato il runtime nel dispositivo, configurare il dispositivo con le informazioni utilizzate per connettersi al servizio Device Provisioning e all'hub IoT.

Avere le informazioni seguenti pronte:

* Valore ambito **ID DPS**
* ID di **registrazione del** dispositivo creato
* Chiave **primaria copiata** dalla registrazione DPS

> [!TIP]
> Per le registrazioni di gruppo, è necessaria la chiave derivata [di](#derive-a-device-key) ogni dispositivo anziché la chiave primaria di registrazione DPS.

### <a name="linux-device"></a>Dispositivo Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. Aprire il file di configurazione nel IoT Edge dispositivo.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Trovare la sezione delle configurazioni di provisioning del file. Rimuovere il commento delle righe per il provisioning delle chiavi simmetriche DPS e assicurarsi che tutte le altre righe di provisioning siano commentate.

   La `provisioning:` riga non deve contenere spazi vuoti precedenti e gli elementi annidati devono essere rientrati di due spazi.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "symmetric_key"
       registration_id: "<REGISTRATION_ID>"
       symmetric_key: "<SYMMETRIC_KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Aggiornare i valori di `scope_id` , e con le informazioni sul dispositivo e sul `registration_id` `symmetric_key` DPS.

1. Facoltativamente, usare le righe o per configurare il comportamento di `always_reprovision_on_startup` `dynamic_reprovisioning` reprovisioning del dispositivo. Se un dispositivo è impostato per il nuovo provisioning all'avvio, tenterà sempre di effettuare il provisioning con DPS e quindi eseguirà il fall back al backup di provisioning in caso di errore. Se un dispositivo è impostato per il nuovo provisioning dinamico, IoT Edge riavvio e di nuovo il provisioning se viene rilevato un evento di reprovisioning. Per altre informazioni, vedere Concetti relativi al [reprovisioning](../iot-dps/concepts-device-reprovision.md)dei dispositivi dell'hub IoT.

1. Riavviare il runtime IoT Edge in modo che accetti tutte le modifiche alla configurazione apportate nel dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

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

1. Trovare la **sezione Provisioning** del file. Rimuovere il commento delle righe per il provisioning DPS con chiave simmetrica e assicurarsi che tutte le altre righe di provisioning siano commentate.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "<REGISTRATION_ID>"

   symmetric_key = "<PRIMARY_KEY OR DERIVED_KEY>"
   ```

1. Aggiornare i valori di , e con il servizio Device Backup e `id_scope` `registration_id` le informazioni sul `symmetric_key` dispositivo.

   Il parametro della chiave simmetrica può accettare un valore di una chiave inline, un URI di file o un URI PKCS#11. Rimuovere il commento da una sola riga di chiave simmetrica, in base al formato in uso.

   Se si usano URI PKCS#11, trovare la sezione **PKCS#11** nel file di configurazione e fornire informazioni sulla configurazione PKCS#11.

1. Salvare e chiudere il file config.toml.

1. Applicare le modifiche di configurazione apportate a IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Dispositivo Windows

1. Aprire una finestra di PowerShell in modalità amministratore. Assicurarsi di usare una sessione AMD64 di PowerShell durante l'IoT Edge, non PowerShell (x86).

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. Per impostazione predefinita, il comando usa il provisioning manuale con contenitori Windows, quindi usare il flag per usare il `-DpsSymmetricKey` provisioning automatico con l'autenticazione con chiave simmetrica.

   Sostituire i valori segnaposto `{scope_id}` per , e con i dati raccolti in `{registration_id}` `{symmetric_key}` precedenza.

   Aggiungere il `-ContainerOs Linux` parametro se si usano contenitori Linux in Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se il runtime è stato avviato correttamente, è possibile passare all'hub IoT e iniziare la distribuzione di moduli IoT Edge nel dispositivo. Usare i comandi seguenti sul dispositivo per verificare che il runtime sia stato installato e avviato correttamente.

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

È possibile verificare che sia stata usata la registrazione singola creata nel servizio Device Provisioning. Passare all'istanza del servizio Device Provisioning nella portale di Azure. Aprire i dettagli di registrazione per la registrazione singola creata. Si noti che lo stato della registrazione è **assegnato e** l'ID dispositivo è elencato.

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come distribuire [e monitorare i moduli IoT Edge su](how-to-deploy-at-scale.md) larga scala usando il portale di Azure o [l'interfaccia della riga di comando di Azure.](how-to-deploy-cli-at-scale.md)
