---
title: Versione di aggiornamento IoT Edge nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Come aggiornare i dispositivi IoT Edge per eseguire le versioni più recenti del daemon di sicurezza e del runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b24276974eba76aa841cdd7f02145210713474eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872286"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Aggiornare il daemon di sicurezza e il runtime di IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Quando il servizio IoT Edge rilascia nuove versioni, è opportuno aggiornare i dispositivi IoT Edge per le funzionalità più recenti e i miglioramenti della sicurezza. Questo articolo fornisce informazioni su come aggiornare i dispositivi IoT Edge quando è disponibile una nuova versione.

Se si vuole passare a una versione più recente, devono essere aggiornati due componenti in un dispositivo IoT Edge. Il primo è il daemon di sicurezza, che viene eseguito nel dispositivo e avvia i moduli runtime quando il dispositivo viene avviato. Attualmente, il daemon di sicurezza può essere aggiornato solo dal dispositivo stesso. Il secondo componente è il runtime, costituito dai moduli dell'agente IoT Edge e dell'hub di IoT Edge. A seconda del modo in cui si struttura la distribuzione, il runtime può essere aggiornato dal dispositivo o in modalità remota.

Per trovare la versione più recente di Azure IoT Edge, fare riferimento alle [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Aggiornare il daemon di sicurezza

Il daemon di sicurezza di IoT Edge è un componente nativo che deve essere aggiornato usando la gestione pacchetti nel dispositivo IoT Edge.

Controllare la versione del daemon di sicurezza in esecuzione nel dispositivo tramite il comando `iotedge version`.

>[!IMPORTANT]
>Se si sta aggiornando un dispositivo dalla versione 1,0 o 1,1 alla versione 1,2, esistono differenze nei processi di installazione e configurazione che richiedono passaggi aggiuntivi. Per ulteriori informazioni, vedere la procedura più avanti in questo articolo: [caso speciale: aggiornamento da 1,0 o 1,1 a 1,2](#special-case-update-from-10-or-11-to-12).

# <a name="linux"></a>[Linux](#tab/linux)

Nei dispositivi Linux x64 usare apt-get o il gestore di pacchetti appropriato per aggiornare il daemon di sicurezza alla versione più recente.

Ottenere la configurazione più recente del repository da Microsoft:

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi OS Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copiare l'elenco generato.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installare la chiave pubblica di Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Aggiornare apt.

   ```bash
   sudo apt-get update
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Verificare quali versioni di IoT Edge sono disponibili.

   ```bash
   apt list -a iotedge
   ```

Se si vuole eseguire l'aggiornamento alla versione più recente del daemon di sicurezza, usare il comando seguente che aggiorna anche **libiothsm-STD** alla versione più recente:

   ```bash
   sudo apt-get install iotedge
   ```

Se si vuole eseguire l'aggiornamento a una versione specifica del daemon di sicurezza, specificare la versione dall'output dell'elenco di apt. Ogni volta che **iotedge** viene aggiornato, tenta automaticamente di aggiornare il pacchetto **libiothsm-STD** alla versione più recente, che può causare un conflitto di dipendenza. Se non si passa alla versione più recente, assicurarsi di fare riferimento a entrambi i pacchetti per la stessa versione. Ad esempio, il comando seguente installa una versione specifica della versione di 1.0.9:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Se la versione che si vuole installare non è disponibile tramite apt-get, è possibile usare curl per avere come destinazione qualsiasi versione dal repository [IOT Edge releases](https://github.com/Azure/azure-iotedge/releases) . Per qualsiasi versione che si vuole installare, individuare i file **libiothsm-STD** e **iotedge** appropriati per il dispositivo. Per ogni file, fare clic con il pulsante destro del mouse sul collegamento file e copiare l'indirizzo del collegamento. Utilizzare l'indirizzo del collegamento per installare le versioni specifiche di tali componenti:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```
<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Verificare quali versioni di IoT Edge sono disponibili.

   ```bash
   apt list -a aziot-edge
   ```

Se si desidera eseguire l'aggiornamento alla versione più recente di IoT Edge, utilizzare il comando seguente che aggiorna anche il servizio di identità alla versione più recente:

   ```bash
   sudo apt-get install aziot-edge
   ```
<!-- end 1.2 -->
:::moniker-end

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Con IoT Edge per Linux in Windows, IoT Edge viene eseguito in una macchina virtuale Linux ospitata in un dispositivo Windows. Questa macchina virtuale è preinstallata con IoT Edge e viene gestita con Microsoft Update per mantenerne aggiornati i componenti. Se sono abilitati gli aggiornamenti automatici, i nuovi aggiornamenti verranno scaricati e installati ogni volta che sono disponibili.

Con IoT Edge per Windows, IoT Edge viene eseguito direttamente nel dispositivo Windows. Per istruzioni sull'aggiornamento che usano gli script di PowerShell, vedere [installare e gestire Azure IOT Edge per Windows](how-to-install-iot-edge-windows-on-windows.md).
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Attualmente non è disponibile il supporto per IoT Edge versione 1,2 in esecuzione nei dispositivi Windows.

:::moniker-end

---

## <a name="update-the-runtime-containers"></a>Aggiornare i contenitori del runtime

La modalità di aggiornamento dell'agente IoT Edge e dei contenitori di hub IoT Edge dipende dal fatto che si usino tag in sequenza (ad esempio 1,0) o tag specifici (ad esempio 1.0.7) nella distribuzione.

Controllare la versione dei moduli dell'agente IoT Edge e dell'hub di IoT Edge attualmente presenti nel dispositivo usando i comandi `iotedge logs edgeAgent` o `iotedge logs edgeHub`.

  ![Trovare la versione contenitore nei log](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Informazioni sui tag di IoT Edge

Le immagini dell'agente IoT Edge e dell'hub di IoT Edge vengono contrassegnate con la versione di IoT Edge a cui sono associate. Esistono due diversi modi per usare i tag con le immagini di runtime:

* **Tag di versioni**. Vengono usati solo i primi due valori del numero di versione per ottenere l'immagine più recente corrispondente a tali cifre. Ad esempio, 1,1 viene aggiornato ogni volta che è presente una nuova versione per puntare alla versione 1.1. x più recente. Se il runtime del contenitore nel dispositivo IoT Edge esegue nuovamente il pull dell'immagine, i moduli del runtime vengono aggiornati alla versione più recente. Nelle distribuzioni dal portale di Azure vengono usati i tag di versioni per impostazione predefinita. *Questo approccio è consigliato per finalità di sviluppo.*

* **Tag specifici**. Vengono usati tutti e tre i valori del numero di versione per impostare in modo esplicito la versione dell'immagine. Ad esempio, 1.1.0 non cambierà dopo la versione iniziale. Quando si è pronti per l'aggiornamento, è possibile dichiarare un nuovo numero di versione nel manifesto della distribuzione. *Questo approccio è consigliato per finalità di produzione.*

### <a name="update-a-rolling-tag-image"></a>Aggiornare un'immagine con tag di versioni

Se si usano i tag di versioni nella distribuzione (ad esempio, mcr.microsoft.com/azureiotedge-hub:**1.0**), è necessario forzare il runtime del contenitore nel dispositivo per eseguire il pull della versione più recente dell'immagine.

Eliminare la versione locale dell'immagine dal dispositivo IoT Edge. Nei computer Windows la disinstallazione del daemon di sicurezza rimuove anche le immagini di runtime, pertanto non è necessario eseguire nuovamente questo passaggio.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Potrebbe essere necessario usare il flag force `-f` per rimuovere le immagini.

Il servizio IoT Edge eseguirà il pull delle versioni più recenti delle immagini di runtime e le riavvierà automaticamente nel dispositivo.

### <a name="update-a-specific-tag-image"></a>Aggiornare un'immagine con tag specifici

Se si usano tag specifici nella distribuzione (ad esempio, mcr.microsoft.com/azureiotedge-hub:**1.0.8**), è sufficiente aggiornare il tag nel manifesto di distribuzione e applicare le modifiche al dispositivo.

1. Nell'hub Internet delle cose del portale di Azure selezionare il dispositivo IoT Edge e selezionare **imposta moduli**.

1. Nella sezione **moduli di IOT Edge** selezionare **impostazioni di runtime**.

   ![Configurare le impostazioni di runtime](./media/how-to-update-iot-edge/configure-runtime.png)

1. In **impostazioni runtime** aggiornare il valore dell' **immagine** per **Hub Edge** con la versione desiderata. Non selezionare **Salva** ancora.

   ![Aggiornare la versione dell'immagine dell'Hub Edge](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Comprimere le impostazioni dell' **Hub perimetrale** o scorrere verso il basso e aggiornare il valore dell' **immagine** per l' **agente Edge** con la stessa versione desiderata.

   ![Aggiornare la versione dell'agente Hub Edge](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Selezionare **Salva**.

1. Selezionare **Verifica e crea**, esaminare la distribuzione e selezionare **Crea**.

## <a name="special-case-update-from-10-or-11-to-12"></a>Caso speciale: aggiornamento da 1,0 o 1,1 a 1,2

A partire dalla versione 1,2, il servizio IoT Edge usa un nuovo nome di pacchetto e presenta alcune differenze nei processi di installazione e configurazione. Se si dispone di un dispositivo IoT Edge che esegue la versione 1,0 o 1,1, seguire queste istruzioni per informazioni su come eseguire l'aggiornamento a 1,2.

>[!NOTE]
>Attualmente non è disponibile alcun supporto per IoT Edge versione 1,2 in esecuzione nei dispositivi Windows.

Di seguito sono riportate alcune delle differenze principali tra 1,2 e versioni precedenti:

* Il nome del pacchetto è stato modificato da **iotedge** a **aziot-Edge**.
* Il pacchetto **libiothsm-STD** non è più usato. Se è stato usato il pacchetto standard fornito come parte della versione IoT Edge, le configurazioni possono essere trasferite alla nuova versione. Se è stata usata un'implementazione diversa di libiothsm-STD, sarà necessario riconfigurare tutti i certificati forniti dall'utente come il certificato di identità del dispositivo, la CA del dispositivo e il bundle di attendibilità.
* Un nuovo servizio di identità, **aziot-Identity-Service** , è stato introdotto come parte della versione 1,2. Questo servizio gestisce il provisioning e la gestione delle identità per IoT Edge e per altri componenti del dispositivo che devono comunicare con l'hub Internet, ad esempio l'aggiornamento del dispositivo dell'hub Azure. <!--TODO: add link to ADU when available -->
* Il file di configurazione predefinito ha un nuovo nome e un nuovo percorso. `/etc/iotedge/config.yaml`In precedenza, le informazioni di configurazione del dispositivo sono ora previste per `/etc/aziot/config.toml` impostazione predefinita. Il `iotedge config import` comando può essere utilizzato per facilitare la migrazione delle informazioni di configurazione nel percorso e nella nuova sintassi precedenti.
* I moduli che usano l'API del carico di lavoro IoT Edge per crittografare o decrittografare i dati persistenti non possono essere decrittografati dopo l'aggiornamento. IoT Edge genera dinamicamente una chiave di crittografia master e una chiave di crittografia per uso interno. Questa chiave non verrà trasferita al nuovo servizio. IoT Edge v 1.2 ne genererà uno nuovo.

Prima di automatizzare i processi di aggiornamento, verificare che funzioni nei computer di test.

Quando si è pronti, seguire questa procedura per aggiornare IoT Edge nei dispositivi:

1. Ottenere la configurazione più recente del repository da Microsoft:

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Copiare l'elenco generato.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Installare la chiave pubblica di Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

4. Aggiornare apt.

   ```bash
   sudo apt-get update
   ```

5. Disinstallare la versione precedente di IoT Edge, lasciando inalterati i file di configurazione.

   ```bash
   sudo apt-get remove iotedge
   ```

6. Installare la versione più recente di IoT Edge, insieme al servizio di identità Internet delle cose.

   ```bash
   sudo apt-get install aziot-edge
   ```

7. Importare il file config. YAML precedente nel nuovo formato e applicare le informazioni di configurazione.

   ```bash
   sudo iotedge config import
   ```

Ora che il servizio IoT Edge in esecuzione nei dispositivi è stato aggiornato, seguire la procedura descritta in questo articolo per [aggiornare anche i contenitori di runtime](#update-the-runtime-containers).

## <a name="special-case-update-to-a-release-candidate-version"></a>Caso speciale: aggiornamento a una versione finale candidata

Azure IoT Edge rilascia periodicamente nuove versioni del servizio IoT Edge. Prima di ogni versione stabile, sono presenti una o più versioni di Release Candidate (RC). Le versioni RC includono tutte le funzionalità pianificate per la versione, ma sono ancora in fase di test e convalida. Se si vuole testare una nuova funzionalità in anticipo, è possibile installare una versione RC e inviare commenti e suggerimenti tramite GitHub.

Le versioni finali candidate seguono la stessa convenzione di numerazione delle versioni, ma hanno **-RC** più un numero incrementale aggiunto alla fine. È possibile visualizzare i candidati della versione nello stesso elenco di [Azure IOT Edge](https://github.com/Azure/azure-iotedge/releases) versioni stabili. Trovare, ad esempio, **1.0.9-RC5** e **1.0.9-RC6**, due dei candidati alla versione precedenti a **1.0.9**. È anche possibile notare che le versioni RC sono contrassegnate con le etichette della **versione non definitiva** .

I moduli dell'agente IoT Edge e dell'Hub hanno versioni RC contrassegnate con la stessa convenzione. Ad esempio, **MCR.Microsoft.com/azureiotedge-Hub:1.0.9-RC6**.

Come anteprime, le versioni release candidate non sono incluse come ultima versione di destinazione dei programmi di installazione regolari. Al contrario, è necessario assegnare manualmente gli asset per la versione RC che si desidera testare. Nella maggior parte dei casi, l'installazione o l'aggiornamento a una versione RC equivale alla definizione di una versione specifica di IoT Edge.

Usare le sezioni di questo articolo per informazioni su come aggiornare un dispositivo IoT Edge a una versione specifica del daemon di sicurezza o dei moduli di Runtime.

Se si sta installando IoT Edge, anziché eseguire l'aggiornamento di un'installazione esistente, attenersi alla procedura descritta in [installazione di versioni offline o specifiche](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

## <a name="next-steps"></a>Passaggi successivi

Visualizzare le [versioni di Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) più recenti.

Per rimanere sempre aggiornati sui recenti aggiornamenti e annunci nel [blog Internet delle cose](https://azure.microsoft.com/blog/topics/internet-of-things/)
