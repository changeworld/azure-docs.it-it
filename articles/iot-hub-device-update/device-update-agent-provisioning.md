---
title: Provisioning dell'aggiornamento del dispositivo hub IoT di Azure agent| Microsoft Docs
description: Provisioning dell'aggiornamento del dispositivo per hub IoT di Azure dispositivo
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 812de4850c6c3577346915a0072ea11c60f7ba73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365451"
---
# <a name="device-update-agent-provisioning"></a>Provisioning dell'agente di aggiornamento dispositivi

L'agente device update module può essere eseguito insieme ad altri processi di sistema [e moduli IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) che si connettono all'hub IoT come parte dello stesso dispositivo logico. Questa sezione descrive come effettuare il provisioning dell'agente di aggiornamento dispositivi come identità del modulo. 


## <a name="module-identity-vs-device-identity"></a>Identità del modulo e identità del dispositivo

Nell'hub IoT, sotto ogni identità del dispositivo, è possibile creare fino a 50 identità del modulo. Ogni identità del modulo genera implicitamente un modulo gemello. Sul lato del dispositivo, gli SDK per dispositivi dell'hub IoT consentono di creare moduli ognuno dei quali apre una connessione indipendente all'hub IoT. L'identità del modulo e il modulo gemello offrono funzionalità simili all'identità del dispositivo e al dispositivo gemello, ma con una granularità più fine. [Altre informazioni sulle identità dei moduli nell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Supporto per l'aggiornamento del dispositivo

I tipi di dispositivi IoT seguenti sono attualmente supportati con l'aggiornamento del dispositivo:

* Dispositivi Linux (IoT Edge e non IoT Edge dispositivi):
    * Aggiornamento dell'immagine A/B:
        - Yocto - ARM64 (immagine di riferimento), estendibile tramite open source [per](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) creare immagini personalizzate per altre architetture in base alle esigenze.
        - Simulatore Ubuntu 18.04
       
    * L'agente di pacchetti supporta le compilazioni per le piattaforme/architetture seguenti:
        - Ubuntu Server 18.04 x64 Package Agent 
        - Debian 9 
        
* Dispositivi vincolati:
    * Esempi dell'agente di aggiornamento del dispositivo AzureRTOS: Esercitazione sull'aggiornamento hub IoT di Azure dispositivo [per Azure-Real-Time-Operating-System](device-update-azure-real-time-operating-system.md)

* Dispositivi disconnessi: 
    * [Informazioni sul supporto per l'aggiornamento del dispositivo disconnesso](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Prerequisiti  

Se si configura il dispositivo IoT o il dispositivo IoT Edge per gli aggiornamenti basati su [pacchetti,](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts)aggiungere packages.microsoft.com ai repository del computer seguendo questa procedura:

1. Accedere al computer o al dispositivo IoT in cui si intende installare l'agente di aggiornamento del dispositivo.

1. Aprire una finestra del terminale.

1. Installare la configurazione del repository corrispondente al sistema operativo del dispositivo.
    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Copiare l'elenco generato nella directory sources.list.d.
    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Installare la chiave pubblica GPG Microsoft.
    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Come effettuare il provisioning dell'agente di aggiornamento dispositivo come identità del modulo

Questa sezione descrive come effettuare il provisioning dell'agente di aggiornamento dispositivi come identità del modulo IoT Edge dispositivi abilitati per IoT Edge, dispositivi IoT non Edge e altri dispositivi IoT.


### <a name="on-iot-edge-enabled-devices"></a>Nei IoT Edge abilitati

Seguire queste istruzioni per effettuare il provisioning dell'agente di aggiornamento dispositivi [IoT Edge dispositivi abilitati.](https://docs.microsoft.com/azure/iot-edge)

1. Seguire le istruzioni per installare [ed effettuare il provisioning del runtime Azure IoT Edge .](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true)

1. Installare l'agente di aggiornamento dell'immagine di aggiornamento del dispositivo
    - In [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) sono disponibili immagini di esempio per provare le implementazioni degli aggiornamenti delle immagini in versioni diverse usando un'immagine di base (adu-base-image) e un'immagine di aggiornamento (adu-update-image). Vedere [l'esempio di come eseguire il flash dell'immagine nel dispositivo dell'hub IoT.](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)  

1. Installare l'agente di aggiornamento del pacchetto di aggiornamento del dispositivo  
    - Per le versioni più recenti dell'agente packages.miscrosoft.com: aggiornare gli elenchi di pacchetti nel dispositivo e installare il pacchetto dell'agente di aggiornamento dispositivi e le relative dipendenze usando:   
    ```shell
    sudo apt-get update
    ```
    
    ```shell
    sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
    ```
    
    - Per le prossime versioni candidate della versione finale da [Artifacts:](https://github.com/Azure/iot-hub-device-update/releases) scaricare il file con estensione dep nel computer in cui si vuole installare l'agente di aggiornamento del dispositivo, quindi:
     ```shell
    Sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
     ```
    
1. A questo punto è possibile avviare l'agente di aggiornamento del dispositivo nel IoT Edge dispositivo. 

### <a name="on-non-edge-iot-linux-devices"></a>Nei dispositivi IoT Linux non Edge

Seguire queste istruzioni per effettuare il provisioning dell'agente di aggiornamento dei dispositivi nei dispositivi IoT Linux.

1. Installare il servizio di gestione delle identità IoT e aggiungere la versione più recente al dispositivo IoT. 
    1. Accedere al computer o al dispositivo IoT.
    1. Aprire una finestra del terminale.
    1.  Installare il servizio [di identità IoT più](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) recente nel dispositivo IoT usando questo comando:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. Provisioning del servizio IoT Identity per ottenere le informazioni sul dispositivo IoT.
    * Creare una copia personalizzata del modello di configurazione in modo da poter aggiungere le informazioni di provisioning. In un terminale immettere il comando seguente.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Modificare quindi il file di configurazione in modo da includere la stringa di connessione del dispositivo che si vuole fungere da provisioning per questo dispositivo o computer. In un terminale immettere il comando seguente.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Verrà visualizzato un messaggio simile all'esempio seguente:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagramma del file di configurazione del servizio di identità IoT." lightbox="media/understand-device-update/config.png":::

    1. Nella stessa finestra nano trovare il blocco con "Provisioning manuale con stringa di connessione".
    1. Nella finestra eliminare il simbolo "#" prima di "provisioning"
    1. Nella finestra eliminare il simbolo "#" prima di 'source' 
    1. Nella finestra eliminare il simbolo "#" prima di "connection_string"
    1. Nella finestra eliminare la stringa tra virgolette a destra di "connection_string" e quindi aggiungere la stringa di connessione 
    1. Salvare le modifiche apportate al file con "CTRL+X" e quindi "Y" e premere "INVIO" per salvare le modifiche. 
    
1.  A questo punto applicare e riavviare il servizio IoT Identity con il comando seguente. A questo punto dovrebbe essere visualizzato il messaggio "Done!" printout significa che il servizio ioT Identity Service è stato configurato correttamente.

    > [!Note]
    > Il servizio IoT Identity registra le identità del modulo con l'hub IoT usando attualmente chiavi simmetriche.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Installare infine l'agente di aggiornamento dispositivi. In [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) sono disponibili immagini di esempio per provare le implementazioni degli aggiornamenti delle immagini in versioni diverse usando un'immagine di base (adu-base-image) e un'immagine di aggiornamento (adu-update-image). Vedere [l'esempio di come eseguire il flash dell'immagine nel dispositivo dell'hub IoT.](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)

1.  A questo punto è possibile avviare l'agente di aggiornamento del dispositivo nel dispositivo IoT. 

### <a name="other-iot-devices"></a>Altri dispositivi IoT

L'agente di aggiornamento del dispositivo può anche essere configurato senza il servizio IoT Identity per il test o nei dispositivi vincolati. Seguire questa procedura per effettuare il provisioning dell'agente di aggiornamento del dispositivo usando una stringa di connessione (dal modulo o dal dispositivo).

1.  In [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) sono disponibili immagini di esempio per provare le implementazioni degli aggiornamenti delle immagini in versioni diverse usando un'immagine di base (adu-base-image) e un'immagine di aggiornamento (adu-update-image). Vedere un esempio [di come eseguire il flash dell'immagine nel dispositivo dell'hub IoT.](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-raspberry-pi#flash-sd-card-with-image)

1.  Accedere al computer o IoT Edge dispositivo/Dispositivo IoT.
    
1.  Aprire una finestra del terminale.

1.  Aggiungere la stringa di connessione al [file di configurazione dell'aggiornamento del dispositivo:](device-update-configuration-file.md)
    1. Immettere il codice seguente nella finestra del terminale:
        - [Uso degli aggiornamenti](device-update-ubuntu-agent.md) dei pacchetti: sudo nano /etc/adu/adu-conf.txt
        - [Uso degli aggiornamenti](device-update-raspberry-pi.md) delle immagini: sudo nano /adu/adu-conf.txt
       
    1. Dovrebbe essere aperta una finestra con del testo. Eliminare l'intera stringa che segue "connection_String=" la prima volta che si effettua il provisioning dell'agente di aggiornamento del dispositivo nel dispositivo IoT. È solo il testo segnaposto.
    
    1. Nel terminale sostituire "<your-connection-string>" con la stringa di connessione del dispositivo per l'istanza dell'agente di aggiornamento del dispositivo.
    
        > [!Important]
        > Non aggiungere virgolette per la stringa di connessione.
        ```shell
        connection_string=<ADD CONNECTION STRING HERE>
        ```
       
    1. Immettere e salvare.
    
1.  A questo punto è possibile avviare l'agente di aggiornamento del dispositivo nel dispositivo IoT. 


## <a name="how-to-start-the-device-update-agent"></a>Come avviare l'agente di aggiornamento dispositivi

Questa sezione descrive come avviare e verificare l'agente di Aggiornamento dispositivi come identità del modulo in esecuzione correttamente nel dispositivo IoT.

1.  Accedere al computer o al dispositivo in cui è installato l'agente di aggiornamento dispositivi.

1.  Aprire una finestra terminale e immettere il comando seguente.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  È possibile controllare lo stato dell'agente usando il comando seguente. Se vengono visualizzati problemi, fare riferimento a questa guida [alla risoluzione dei problemi](troubleshoot-device-update.md).
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Verrà visualizzato lo stato OK.

1.  Nel portale dell'hub IoT passare al dispositivo IoT o IoT Edge dispositivi per trovare il dispositivo configurato con l'agente di aggiornamento dispositivi. Verrà visualizzato l'agente di aggiornamento dei dispositivi in esecuzione come modulo. Ad esempio:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagramma del nome del modulo Device Update." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Come compilare ed eseguire l'agente di aggiornamento dispositivi

È anche possibile compilare e modificare il proprio agente di aggiornamento del dispositivo del cliente.

Seguire le istruzioni per [compilare l'agente](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) di aggiornamento dispositivi dall'origine.

Una volta completata la compilazione dell'agente, è possibile [eseguirlo.](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md)

A questo punto, apportare le modifiche necessarie per incorporare l'agente nell'immagine.  Per istruzioni, vedere [come modificare l'agente](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) di aggiornamento dei dispositivi.


## <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

In caso di problemi, vedere la Guida [](troubleshoot-device-update.md) alla risoluzione dei problemi dell'aggiornamento del dispositivo per l'hub IoT per sbloccare eventuali problemi e raccogliere le informazioni necessarie da fornire a Microsoft.


## <a name="next-steps"></a>Passaggi successivi

È possibile usare le immagini e i file binari predefiniti seguenti per una semplice dimostrazione dell'aggiornamento dei dispositivi per l'hub IoT:

- [Aggiornamento delle immagini: Attività iniziali con Raspberry Pi 3 B+ Reference Yocto Image](device-update-raspberry-pi.md) estendibile tramite open source per creare immagini personalizzate per altre architetture in base alle esigenze.

- [Attività iniziali'agente di riferimento del simulatore Ubuntu (18.04 x64)](device-update-simulator.md)

- [Aggiornamento del pacchetto:Attività iniziali l'agente di pacchetti Ubuntu Server 18.04 x64](device-update-ubuntu-agent.md)

- [Esercitazione sull'hub IoT di Azure dispositivo per azure-real-time-operating-system](device-update-azure-real-time-operating-system.md)

