---
title: Effettuare automaticamente il provisioning dei dispositivi Windows con DPS - Azure IoT Edge | Microsoft Docs
description: Usare un dispositivo simulato nel computer Windows per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bd027773a5759caee19228f56ba4b3dfe8c2cf
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482008"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Creare ed effettuare il provisioning di un dispositivo IoT Edge simulato con un TPM virtuale in Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge possibile eseguire il provisioning automatico dei dispositivi usando il servizio [Device Provisioning](../iot-dps/index.yml) esattamente come i dispositivi non abilitati per i dispositivi perimetrali. Se non si ha familiarità con il processo di provisioning automatico, vedere la panoramica sul [provisioning](../iot-dps/about-iot-dps.md#provisioning-process) prima di continuare.

DPS supporta l'attestazione con chiave simmetrica per IoT Edge dispositivi sia nella registrazione singola che nella registrazione di gruppo. Per la registrazione di gruppo, se si seleziona l'opzione "is IoT Edge device" (è un dispositivo) come true nell'attestazione con chiave simmetrica, tutti i dispositivi registrati in tale gruppo di registrazione verranno contrassegnati come dispositivi IoT Edge dispositivo.

Questo articolo illustra come testare il provisioning automatico in un dispositivo IoT Edge simulato con la procedura seguente:

* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare un dispositivo simulato nel computer Windows con un Trusted Platform Module (TPM) simulato per la sicurezza dell'hardware.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime IoT Edge e connettere il dispositivo all'hub IoT.

> [!TIP]
> Questo articolo descrive come testare il provisioning automatico usando l'attestazione TPM nei dispositivi virtuali, ma la maggior parte si applica anche all'uso di hardware TPM fisico.

## <a name="prerequisites"></a>Prerequisiti

* Un computer di sviluppo Windows. In questo articolo viene usato Windows 10.
* Un hub IoT attivo.

> [!NOTE]
> TPM 2.0 è necessario quando si usa l'attestazione TPM con DPS e può essere usato solo per creare registrazioni singole, non di gruppo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

> [!TIP]
> Se si usa un dispositivo TPM fisico, è necessario determinare la chiave di verifica dell'approvazione, che è univoca per ogni chip TPM e viene ottenuta dal produttore del chip TPM associato. È possibile derivare un **ID** di registrazione univoco per il dispositivo TPM, ad esempio creando un hash SHA-256 della chiave di verifica dell'approvazione.
>
> Seguire le istruzioni nell'articolo Come gestire le registrazioni dei dispositivi con il portale di [Azure](../iot-dps/how-to-manage-enrollments.md) per creare la registrazione in DPS e quindi procedere con la sezione Installare [il runtime di IoT Edge](#install-the-iot-edge-runtime) in questo articolo per continuare.

## <a name="simulate-a-tpm-device"></a>Simulare un dispositivo TPM

Creare un dispositivo TPM simulato nel computer di sviluppo Windows. Recuperare **l'ID registrazione** e **la chiave di** verifica dell'approvazione per il dispositivo e usarli per creare una voce di registrazione singola in DPS.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-at-scale.md).

Scegliere il linguaggio di SDK che si vuole usare per creare il dispositivo simulato e seguire i passaggi per creare la singola registrazione.

Quando si crea la registrazione singola, selezionare **True** per dichiarare che il dispositivo TPM simulato nel computer di sviluppo Windows è **IoT Edge dispositivo .**

> [!TIP]
> Nell'interfaccia della riga di [](/cli/azure/iot/dps/enrollment) comando [](/cli/azure/iot/dps/enrollment-group) di Azure è possibile creare una registrazione o un gruppo di registrazione e usare il **flag** abilitato per le funzionalità perimetrali per specificare che un dispositivo o un gruppo di dispositivi è un IoT Edge dispositivo.

Guide relative ai dispositivi simulati e alle singole registrazioni:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Dopo aver creato la singola registrazione, salvare il valore di **ID registrazione**. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale. Installare il IoT Edge runtime nel dispositivo che esegue il TPM simulato.

Seguire la procedura descritta in [Installare il runtime Azure IoT Edge](how-to-install-iot-edge.md), quindi tornare a questo articolo per eseguire il provisioning del dispositivo.

> [!TIP]
> Mantenere aperta la finestra di esecuzione del simulatore TPM durante l'installazione e il test.

## <a name="configure-the-device-with-provisioning-information"></a>Configurare il dispositivo con le informazioni di provisioning

Dopo aver installato il runtime nel dispositivo, configurare il dispositivo con le informazioni utilizzate per connettersi al servizio Device Provisioning e all'hub IoT.

1. Conoscere **l'ambito id DPS** e l'ID registrazione del dispositivo raccolti nelle sezioni precedenti. 

1. Aprire una finestra di PowerShell in modalità amministratore. Assicurarsi di usare una sessione AMD64 di PowerShell durante l'IoT Edge, non PowerShell (x86).

1. Il **comando Deploy-IoTEdge** verifica che il computer Windows sia in una versione supportata, attiva la funzionalità contenitori e quindi scarica il runtime moby e il runtime IoT Edge runtime. Il comando usa per impostazione predefinita i contenitori di Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. A questo punto, i dispositivi IoT Core potrebbero essere riavviati automaticamente. Windows 10 o i dispositivi Windows Server potrebbero richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows. Usare il `-Dps` flag per usare il servizio Device Provisioning anziché il provisioning manuale.

   Sostituire i valori segnaposto `{scope_id}` per e con i dati raccolti in `{registration_id}` precedenza.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se il runtime è stato avviato correttamente, è possibile passare all'hub IoT e iniziare la distribuzione di moduli IoT Edge nel dispositivo. Usare i comandi seguenti sul dispositivo per verificare che il runtime sia stato installato e avviato correttamente.  

Verificare lo stato del servizio IoT Edge.

```powershell
Get-Service iotedge
```

Esaminare i log del servizio generati negli ultimi cinque minuti.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come [Distribuire e monitorare i moduli IoT Edge su larga scala tramite il portale di Azure](how-to-deploy-at-scale.md) o usando l'[interfaccia della riga di comando di Azure](how-to-deploy-cli-at-scale.md)
