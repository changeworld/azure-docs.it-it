---
title: Installare Azure IoT Edge per Windows | Microsoft Docs
description: Installare Azure IoT Edge per i contenitori Windows nei dispositivi Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: bb87d09b67658f9a3d7c68f635bfcd9a29de675c
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201639"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Installare e gestire Azure IoT Edge con i contenitori di Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Quando un dispositivo viene configurato con il runtime IoT Edge, è possibile iniziare a distribuirvi la logica di business dal cloud. Per altre informazioni, vedere [comprendere il runtime di Azure IOT Edge e la relativa architettura](iot-edge-runtime.md).

Per configurare un dispositivo di IoT Edge, è necessario eseguire due passaggi. Il primo passaggio consiste nell'installare il runtime e le relative dipendenze. Il secondo passaggio consiste nel connettere il dispositivo alla propria identità nel cloud e configurare l'autenticazione con l'hub Internet.

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge con i contenitori di Windows. Per usare i contenitori Linux in un dispositivo Windows, vedere l'articolo [Azure IOT Edge per Linux in Windows](how-to-install-iot-edge-on-windows.md) .

>[!NOTE]
>Azure IoT Edge con i contenitori di Windows non saranno supportati a partire dalla versione 1,2 di Azure IoT Edge.
>
>Provare a usare il nuovo metodo per l'esecuzione di IoT Edge nei dispositivi Windows [Azure IOT Edge per Linux in Windows](iot-edge-for-linux-on-windows.md).

## <a name="prerequisites"></a>Prerequisiti

* Un dispositivo Windows

  IoT Edge con i contenitori di Windows richiede Windows versione 1809/Build 17763, ovvero la build più recente per il [supporto a lungo termine di Windows](/windows/release-information/). Assicurarsi di esaminare l' [elenco dei sistemi supportati](support.md#operating-systems) per un elenco degli SKU supportati.

* [ID dispositivo registrato](how-to-register-device.md)

  Se il dispositivo è stato registrato con l'autenticazione con chiave simmetrica, è possibile usare la stringa di connessione del dispositivo.

  Se il dispositivo è stato registrato con l'autenticazione con certificato autofirmato X. 509, avere almeno uno dei certificati di identità usati per registrare il dispositivo e la relativa chiave privata privata corrispondente disponibile nel dispositivo.

## <a name="install-a-container-engine"></a>Installare un motore di contenitori

Azure IoT Edge si basa su un runtime del contenitore compatibile con OCI come [Moby](https://github.com/moby/moby). Un motore basato su Moby incluso nello script di installazione. Non sono previsti passaggi aggiuntivi per installare il motore di.

## <a name="install-the-iot-edge-security-daemon"></a>Installare il daemon di sicurezza di IoT Edge

1. Eseguire PowerShell come amministratore.

   Usare una sessione AMD64 di PowerShell, non PowerShell (x86). Se non si è certi del tipo di sessione in uso, eseguire il comando seguente:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Eseguire il comando [deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , che esegue le attività seguenti:

   * Verifica che il computer Windows sia in una versione supportata.
   * Attiva la funzionalità dei contenitori.
   * Scarica il motore Moby e il IoT Edge Runtime.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Riavviare il dispositivo, se richiesto.

Quando si installa IoT Edge in un dispositivo, è possibile usare parametri aggiuntivi per modificare il processo, tra cui:

* Indirizzare il traffico attraverso un server proxy
* Puntare il programma di installazione a una directory locale per l'installazione offline.

Per ulteriori informazioni su questi parametri aggiuntivi, vedere [script di PowerShell per IOT Edge con i contenitori di Windows](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Eseguire il provisioning del dispositivo con la relativa identità cloud

Ora che il motore del contenitore e il runtime di IoT Edge sono installati nel dispositivo, si è pronti per il passaggio successivo, ovvero per configurare il dispositivo con le informazioni di autenticazione e identità cloud.

Scegliere la sezione successiva in base al tipo di autenticazione che si vuole usare:

* [Opzione 1: eseguire l'autenticazione con chiavi simmetriche](#option-1-authenticate-with-symmetric-keys)
* [Opzione 2: eseguire l'autenticazione con certificati X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opzione 1: eseguire l'autenticazione con chiavi simmetriche

A questo punto, il runtime di IoT Edge viene installato nel dispositivo Windows ed è necessario eseguire il provisioning del dispositivo con le informazioni di autenticazione e identità cloud.

Questa sezione illustra i passaggi per eseguire il provisioning di un dispositivo con autenticazione con chiave simmetrica. È necessario che il dispositivo sia stato registrato nell'hub Internet e che sia stata recuperata la stringa di connessione dalle informazioni sul dispositivo. In caso contrario, seguire la procedura descritta in [registrare un dispositivo IOT Edge nell'hub](how-to-register-device.md)Internet.

1. Nel dispositivo IoT Edge eseguire PowerShell come amministratore.

2. Usare il comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) per configurare il runtime di IOT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Se è stato scaricato lo script IoTEdgeSecurityDaemon.ps1 nel dispositivo per l'installazione offline o specifica della versione, assicurarsi di fare riferimento alla copia locale dello script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando richiesto, specificare la stringa di connessione del dispositivo recuperata nella sezione precedente. La stringa di connessione del dispositivo associa il dispositivo fisico a un ID dispositivo nell'hub e fornisce informazioni di autenticazione.

   La stringa di connessione del dispositivo ha il formato seguente e non deve includere le virgolette: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Quando si esegue il provisioning di un dispositivo manualmente, è possibile usare parametri aggiuntivi per modificare il processo, tra cui:

* Indirizzare il traffico attraverso un server proxy
* Dichiarare un'immagine del contenitore edgeAgent specifica e fornire le credenziali se si trova in un registro privato

Per ulteriori informazioni su questi parametri aggiuntivi, vedere [script di PowerShell per IOT Edge con i contenitori di Windows](reference-windows-scripts.md).

### <a name="option-2-authenticate-with-x509-certificates"></a>Opzione 2: eseguire l'autenticazione con certificati X. 509

A questo punto, il runtime di IoT Edge viene installato nel dispositivo Windows ed è necessario eseguire il provisioning del dispositivo con le informazioni di autenticazione e identità cloud.

Questa sezione illustra i passaggi per eseguire il provisioning di un dispositivo con l'autenticazione del certificato X. 509. È necessario aver registrato il dispositivo nell'hub Internet, fornendo le identificazioni personali che corrispondono al certificato e alla chiave privata presenti nel dispositivo IoT Edge. In caso contrario, seguire la procedura descritta in [registrare un dispositivo IOT Edge nell'hub](how-to-register-device.md)Internet.

1. Nel dispositivo IoT Edge eseguire PowerShell come amministratore.

2. Usare il comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) per configurare il runtime di IOT Edge nel computer.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Se è stato scaricato lo script IoTEdgeSecurityDaemon.ps1 nel dispositivo per l'installazione offline o specifica della versione, assicurarsi di fare riferimento alla copia locale dello script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando richiesto, specificare le informazioni seguenti:

   * **IotHubHostName**: nome host dell'hub Internet delle cose a cui si connetterà il dispositivo. Ad esempio: `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: ID specificato durante la registrazione del dispositivo.
   * **X509IdentityCertificate**: percorso assoluto di un certificato di identità nel dispositivo. Ad esempio: `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: percorso assoluto del file di chiave privata per il certificato di identità fornito. Ad esempio: `C:\path\identity_key.pem`.

Quando si esegue il provisioning di un dispositivo manualmente, è possibile usare parametri aggiuntivi per modificare il processo, tra cui:

* Indirizzare il traffico attraverso un server proxy
* Dichiarare un'immagine del contenitore edgeAgent specifica e fornire le credenziali se si trova in un registro privato

Per ulteriori informazioni su questi parametri aggiuntivi, vedere [script di PowerShell per IOT Edge con i contenitori di Windows](reference-windows-scripts.md).

## <a name="offline-or-specific-version-installation-optional"></a>Installazione di versioni offline o specifiche (facoltativo)

I passaggi descritti in questa sezione si riferiscono a scenari non inclusi nei passaggi di installazione standard. Questo può includere:

* Installare IoT Edge offline
* Installare una versione finale candidata
* Installare una versione diversa da quella più recente

Durante l'installazione vengono scaricati tre file:

* Uno script di PowerShell che contiene le istruzioni di installazione
* Microsoft Azure IoT Edge CAB, che contiene il daemon di sicurezza IoT Edge (iotedged), il motore di contenitori di Moby e l'interfaccia della riga di comando di Moby
* Programma di installazione di Visual C++ Redistributable Package (runtime VC)

Se il dispositivo sarà offline durante l'installazione o se si vuole installare una versione specifica di IoT Edge, è possibile scaricare questi file in anticipo nel dispositivo. Al termine dell'installazione, puntare lo script di installazione alla directory che contiene i file scaricati. Il programma di installazione controlla prima di tutto la directory e quindi Scarica solo i componenti che non sono stati trovati. Se tutti i file sono disponibili offline, è possibile eseguire l'installazione senza connessione Internet.

1. Per i file di installazione IoT Edge più recenti insieme alle versioni precedenti, vedere [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases).

2. Trovare la versione che si vuole installare e scaricare i file seguenti dalla sezione **Asset** delle note sulla versione sul dispositivo Internet:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab dal canale della versione 1,1.

   È importante usare lo script di PowerShell della stessa versione del file con estensione cab usato perché la funzionalità Cambia per supportare le funzionalità in ogni versione.

3. Se il file con estensione cab scaricato contiene un suffisso di architettura, rinominare il file semplicemente **Microsoft-Azure-IoTEdge.cab**.

4. Facoltativamente, scaricare un programma di installazione per Visual C++ ridistribuibile. Ad esempio, lo script di PowerShell usa questa versione: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Salvare il programma di installazione nella stessa cartella del dispositivo Internet dell'utente come file di IoT Edge.

5. Per eseguire l'installazione con i componenti offline, il [punto di origine](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) è la copia locale dello script di PowerShell.

6. Eseguire il comando [deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) con il `-OfflineInstallationPath` parametro. Consente di specificare il percorso assoluto della directory di file. Ad esempio,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Il comando di distribuzione userà tutti i componenti trovati nella directory di file locale specificata. Se il file con estensione cab o il programma di installazione Visual C++ è mancante, tenterà di scaricarli.

## <a name="update-iot-edge"></a>Aggiorna IoT Edge

Usare il `Update-IoTEdge` comando per aggiornare il daemon di sicurezza. Lo script esegue automaticamente il pull della versione più recente del daemon di sicurezza.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

L'esecuzione del comando Update-IoTEdge rimuove e aggiorna il daemon di sicurezza dal dispositivo, insieme alle due immagini del contenitore di Runtime. Il file config.yaml viene mantenuto nel dispositivo, così come i dati del motore del contenitore Moby. Mantenendo le informazioni di configurazione significa che non è necessario fornire di nuovo la stringa di connessione o le informazioni sul servizio Device provisioning per il dispositivo durante il processo di aggiornamento.

Se si vuole eseguire l'aggiornamento a una versione specifica del daemon di sicurezza, trovare la versione dal canale della versione 1,1 di destinazione da [IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases). In tale versione, scaricare il file di **Microsoft-Azure-IoTEdge.cab** . Quindi, usare il `-OfflineInstallationPath` parametro per puntare al percorso del file locale. Ad esempio:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Il `-OfflineInstallationPath` parametro Cerca un file denominato **Microsoft-Azure-IoTEdge.cab** nella directory specificata. Rinominare il file per rimuovere il suffisso di architettura se ne è presente uno.

Se si vuole aggiornare un dispositivo offline, trovare la versione di destinazione da [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases). In tale versione, scaricare i file di *IoTEdgeSecurityDaemon.ps1* e *Microsoft-Azure-IoTEdge.cab* . È importante usare lo script di PowerShell della stessa versione del file con estensione cab usato perché la funzionalità Cambia per supportare le funzionalità in ogni versione.

Se il file con estensione cab scaricato contiene un suffisso di architettura, rinominare il file semplicemente **Microsoft-Azure-IoTEdge.cab**.

Per eseguire l'aggiornamento con i componenti offline, il [punto di origine](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) è la copia locale dello script di PowerShell. Usare quindi il `-OfflineInstallationPath` parametro come parte del `Update-IoTEdge` comando e fornire il percorso assoluto alla directory di file. Ad esempio,

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Per ulteriori informazioni sulle opzioni di aggiornamento, utilizzare il comando `Get-Help Update-IoTEdge -full` o fare riferimento agli [script di PowerShell per IOT Edge con i contenitori di Windows](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

Se si vuole rimuovere l'installazione IoT Edge dal dispositivo, usare i comandi seguenti.

Se si desidera rimuovere l'installazione di IoT Edge dal dispositivo Windows, utilizzare il comando [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) da una finestra di PowerShell amministrativa. Questo comando rimuove il runtime di IoT Edge, insieme alla configurazione esistente e ai dati del motore Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Per ulteriori informazioni sulle opzioni di disinstallazione, utilizzare il comando `Get-Help Uninstall-IoTEdge -full` .

## <a name="next-steps"></a>Passaggi successivi

Continuare a [distribuire i moduli IOT Edge](how-to-deploy-modules-portal.md) per informazioni su come distribuire i moduli nel dispositivo.
