---
title: Provisioning dell'aggiornamento del dispositivo per l'agente Hub Azure Internet | Microsoft Docs
description: Provisioning dell'aggiornamento del dispositivo per l'agente Hub Azure
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: e778c7ee14d2115bf6d8cf7f12ceaa61e364a4a2
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120181"
---
# <a name="device-update-agent-provisioning"></a>Provisioning dell'agente di aggiornamento del dispositivo

L'agente del modulo di aggiornamento del dispositivo può essere eseguito insieme ad altri processi di sistema e [IOT Edge moduli](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules) che si connettono all'hub Internet come parte dello stesso dispositivo logico. Questa sezione descrive come eseguire il provisioning dell'agente di aggiornamento del dispositivo come identità del modulo. 


## <a name="module-identity-vs-device-identity"></a>Identità del modulo rispetto all'identità del dispositivo

Nell'hub tutto, in ogni identità del dispositivo, è possibile creare fino a 50 identità del modulo. Ogni identità del modulo genera implicitamente un modulo gemello. Sul lato del dispositivo, gli SDK per dispositivi dell'hub IoT consentono di creare moduli ognuno dei quali apre una connessione indipendente all'hub IoT. Il modulo Identity e il modulo gemello forniscono le funzionalità simili all'identità del dispositivo e al dispositivo gemello, ma con una granularità più fine. [Altre informazioni sulle identità dei moduli nell'hub Internet](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)


## <a name="support-for-device-update"></a>Supporto per l'aggiornamento del dispositivo

Con l'aggiornamento del dispositivo sono attualmente supportati i seguenti tipi di dispositivi:

* Dispositivi Linux (dispositivi IoT Edge e non IoT Edge):
    * Aggiornamento A/B dell'immagine:
        - Yocto-ARM64 (immagine di riferimento), estendibile tramite Open Source per [compilare immagini personalizzate](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) per altre architetture in base alle esigenze.
        - Simulatore Ubuntu 18,04
       
    * Compilazioni supportate dall'agente pacchetti per le piattaforme/architetture seguenti:
        - Agente pacchetto di Ubuntu server 18,04 x64 
        - Debian 9 
        
* Dispositivi vincolati:
    * Esempi di agente di aggiornamento del dispositivo AzureRTOS: [esercitazione sull'aggiornamento del dispositivo per l'hub Azure per Azure-in tempo reale-sistema operativo](device-update-azure-real-time-operating-system.md)

* Dispositivi disconnessi: 
    * [Informazioni sul supporto per l'aggiornamento dei dispositivi disconnessi](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Prerequisiti  

Se si sta configurando il dispositivo o il dispositivo IoT Edge per [gli aggiornamenti basati su pacchetti](https://docs.microsoft.com/azure/iot-hub-device-update/understand-device-update#support-for-a-wide-range-of-update-artifacts), aggiungere Packages.Microsoft.com ai repository del computer attenendosi alla procedura seguente:

1. Accedere al computer o al dispositivo dell'utente in cui si vuole installare l'agente di aggiornamento del dispositivo.

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

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>Come eseguire il provisioning dell'agente di aggiornamento del dispositivo come identità del modulo

Questa sezione descrive come eseguire il provisioning dell'agente di aggiornamento del dispositivo come identità del modulo nei dispositivi IoT Edge abilitati, nei dispositivi non perimetrali e in altri dispositivi.


### <a name="on-iot-edge-enabled-devices"></a>Nei dispositivi IoT Edge abilitati

Seguire queste istruzioni per eseguire il provisioning dell'agente di aggiornamento del dispositivo nei [dispositivi IOT Edge abilitati](https://docs.microsoft.com/azure/iot-edge).

1. Seguire le istruzioni per [installare ed effettuare il provisioning del Azure IOT Edge Runtime](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2020-11&preserve-view=true).

1. Quindi installare l'agente di aggiornamento del dispositivo da [elementi](https://github.com/Azure/iot-hub-device-update/releases) e a questo punto è possibile avviare l'agente di aggiornamento del dispositivo nel dispositivo IOT Edge.


### <a name="on-non-edge-iot-linux-devices"></a>Nei dispositivi Linux non perimetrali

Seguire queste istruzioni per eseguire il provisioning dell'agente di aggiornamento del dispositivo nei dispositivi Linux.

1. Installare il servizio di identità delle cose e aggiungere la versione più recente al dispositivo Internet delle cose. 
    1. Accedere al computer o al dispositivo.
    1. Aprire una finestra del terminale.
    1.  Installare la versione più recente del [servizio di identità](https://github.com/Azure/iot-identity-service/blob/main/docs/packaging.md#installing-and-configuring-the-package) delle cose nel dispositivo Internet delle cose usando questo comando:
    
        ```shell
        sudo apt-get install aziot-identity-service
        ```
        
1. Provisioning del servizio di identità delle cose per ottenere informazioni sul dispositivo.
    * Creare una copia personalizzata del modello di configurazione in modo da poter aggiungere le informazioni di provisioning. In un terminale immettere il comando seguente.
      
        ```shell
        sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
        ```
   
1. Modificare quindi il file di configurazione per includere la stringa di connessione del dispositivo che si vuole usare come provisioning per questo dispositivo o computer. In un terminale immettere il comando seguente.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Verrà visualizzato un messaggio simile all'esempio seguente:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagramma del file di configurazione del servizio identità di Internet." lightbox="media/understand-device-update/config.png":::

    1. Nella stessa finestra nano trovare il blocco con "provisioning manuale con stringa di connessione".
    1. Nella finestra eliminare il simbolo "#" prima di "provisioning"
    1. Nella finestra eliminare il simbolo "#" prima di "Source" 
    1. Nella finestra eliminare il simbolo "#" prima di "connection_string"
    1. Nella finestra eliminare la stringa racchiusa tra virgolette a destra di "connection_string" e quindi aggiungere la stringa di connessione 
    1. Salvare le modifiche apportate al file con ' Ctrl + X ' e quindi ' Y ' e premere il tasto ' invio ' per salvare le modifiche. 
    
1.  A questo punto, applicare e riavviare il servizio di identità delle cose con il comando seguente. A questo punto dovrebbe essere visualizzata una "operazione eseguita" stampa che indica che è stato configurato correttamente il servizio di identità Internet delle cose.

    > [!Note]
    > Il servizio di identità Internet delle cose registra le identità dei moduli con l'hub di Internet delle cose usando attualmente chiavi simmetriche.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1.  Infine, installare l'agente di aggiornamento del dispositivo dagli [elementi](https://github.com/Azure/iot-hub-device-update/releases) ed è ora possibile avviare l'agente di aggiornamento del dispositivo nel dispositivo IOT Edge.


### <a name="other-iot-devices"></a>Altri dispositivi

L'agente di aggiornamento del dispositivo può anche essere configurato senza il servizio di identità di Internet delle cose per i test o per i dispositivi vincolati. Seguire questa procedura per eseguire il provisioning dell'agente di aggiornamento del dispositivo usando una stringa di connessione (dal modulo o dal dispositivo).

1.  Installare Device Update Agent dagli [artefatti](https://github.com/Azure/iot-hub-device-update/releases).

1.  Accedere al computer o al dispositivo IoT Edge dispositivo.
    
1.  Aprire una finestra del terminale.

1.  Aggiungere la stringa di connessione al [file di configurazione dell'aggiornamento del dispositivo](device-update-configuration-file.md):
    1. Immettere quanto segue nella finestra del terminale:
        - Uso degli [aggiornamenti del pacchetto](device-update-ubuntu-agent.md) : sudo nano/etc/Adu/adu-conf.txt
        - Uso degli [aggiornamenti delle immagini](device-update-raspberry-pi.md) : sudo nano/Adu/adu-conf.txt
       
    1. Verrà visualizzata una finestra con un testo. Eliminare l'intera stringa dopo ' connection_String =' la prima volta che si esegue il provisioning dell'agente di aggiornamento del dispositivo nel dispositivo. È sufficiente inserire il testo del segnaposto.
    
    1. Nel terminale sostituire <la stringa di connessione> con la stringa di connessione del dispositivo per l'istanza dell'agente di aggiornamento del dispositivo.
    
        > [!Important]
        > Non aggiungere virgolette intorno alla stringa di connessione.
        
        - connection_string =<la stringa di connessione>
       
    1. Immettere e salvare.
    
1.  A questo punto è possibile avviare l'agente di aggiornamento del dispositivo nel dispositivo IoT Edge. 


## <a name="how-to-start-the-device-update-agent"></a>Come avviare l'agente di aggiornamento del dispositivo

Questa sezione descrive come avviare e verificare l'agente di aggiornamento del dispositivo come un'identità del modulo eseguita correttamente nel dispositivo.

1.  Accedere al computer o al dispositivo in cui è installato l'agente di aggiornamento del dispositivo.

1.  Aprire una finestra del terminale e immettere il comando seguente.
    ```shell
    sudo systemctl restart adu-agent
    ```
    
1.  È possibile controllare lo stato dell'agente usando il comando seguente. Se si verificano problemi, fare riferimento a questa [Guida alla risoluzione dei problemi](troubleshoot-device-update.md).
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Verrà visualizzato lo stato OK.

1.  Nel portale dell'hub Internet delle cose, passare a dispositivo Internet o dispositivi IoT Edge per trovare il dispositivo configurato con l'agente di aggiornamento del dispositivo. Qui si noterà che l'agente di aggiornamento del dispositivo è in esecuzione come modulo. Ad esempio:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagramma del nome del modulo di aggiornamento del dispositivo." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Come compilare ed eseguire l'agente di aggiornamento dispositivi

È anche possibile compilare e modificare il proprio agente di aggiornamento dei dispositivi del cliente.

Seguire le istruzioni per [creare](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) l'agente di aggiornamento del dispositivo dall'origine.

Una volta che l'agente è stato compilato correttamente, è il momento di [eseguire](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) l'agente.

A questo punto, apportare le modifiche necessarie per incorporare l'agente nell'immagine.  Per informazioni aggiuntive, vedere come [modificare](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) l'agente di aggiornamento del dispositivo.


## <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Se si verificano problemi, vedere la [Guida alla risoluzione dei](troubleshoot-device-update.md) problemi dell'hub di gestione delle cose per sbloccare i possibili problemi e raccogliere le informazioni necessarie per fornire a Microsoft.


## <a name="next-steps"></a>Passaggi successivi

È possibile usare le immagini e i file binari predefiniti seguenti per una semplice dimostrazione dell'aggiornamento del dispositivo per l'hub Internet:

- [Aggiornamento immagine: introduzione con Raspberry Pi 3 B + riferimento Yocto image](device-update-raspberry-pi.md) estendibile tramite Open Source per compilare immagini personalizzate per altre architetture in base alle esigenze.

- [Introduzione con l'agente di riferimento del simulatore Ubuntu (18,04 x64)](device-update-simulator.md)

- [Aggiornamento del pacchetto: Introduzione con l'agente di pacchetti di Ubuntu server 18,04 x64](device-update-ubuntu-agent.md)

- [Esercitazione sull'aggiornamento del dispositivo per l'hub Azure per Azure-Real-Time-sistema operativo](device-update-azure-real-time-operating-system.md)

