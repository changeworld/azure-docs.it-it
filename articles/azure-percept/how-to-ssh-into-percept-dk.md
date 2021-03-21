---
title: Connettersi ad Azure Percept DK tramite SSH
description: Informazioni su come eseguire SSH in Azure Percept DK con PuTTy
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721478"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Connettersi ad Azure Percept DK tramite SSH

Seguire questa procedura per configurare una connessione SSH ad Azure Percept DK tramite OpenSSH o [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Prerequisiti

- Un computer host basato su Windows, Linux o OS X con funzionalità Wi-Fi
- Un client SSH (vedere la sezione successiva per informazioni aggiuntive sull'installazione)
- Azure Percept DK (Dev Kit)
- Accesso SSH, creato durante l' [esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md)

## <a name="install-your-preferred-ssh-client"></a>Installare il client SSH preferito

Se il computer host esegue Linux o OS X, i servizi SSH sono inclusi in tali sistemi operativi e possono essere eseguiti senza un'applicazione client separata. Per ulteriori informazioni su come eseguire i servizi SSH, consultare la documentazione del prodotto del sistema operativo.

Se il computer host esegue Windows, è possibile scegliere tra due opzioni client SSH: OpenSSH e PuTTy.

### <a name="openssh"></a>OpenSSH

Windows 10 include un client SSH incorporato denominato OpenSSH che può essere eseguito con un semplice comando all'interno di un prompt dei comandi. È consigliabile usare OpenSSH con Azure Percept, se disponibile. Per verificare se nel computer Windows è installato OpenSSH, attenersi alla procedura seguente:

1. Passare a   ->  **Impostazioni** di avvio.

1. Selezionare **App**.

1. In **app & funzionalità** selezionare **funzionalità facoltative**.

1. Digitare **openssh client** nella barra di ricerca delle **funzionalità installate** . Se viene visualizzato OpenSSH, il client è già installato ed è possibile passare alla sezione successiva. Se OpenSSH non è visibile, fare clic su **Aggiungi una funzionalità**.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Screenshot delle impostazioni che mostrano lo stato di installazione di OpenSSH.":::

1. Selezionare **openssh client** e fare clic su **Installa**. È ora possibile passare alla sezione successiva. Se OpenSSH non è disponibile per l'installazione nel computer, attenersi alla procedura seguente per installare PuTTy, un client SSH di terze parti.

### <a name="putty"></a>PuTTY

Se il computer Windows non include OpenSSH, è consigliabile usare [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). Per scaricare e installare PuTTy, completare i passaggi seguenti:

1. Passare alla [pagina di download di Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. In **file di pacchetto** fare clic sul file con estensione msi a 32 bit o 64 bit per scaricare il programma di installazione. Se non si è certi di quale versione scegliere, consultare le [domande frequenti](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit).

1. Fare clic sul programma di installazione per avviare il processo di installazione. Seguire le istruzioni necessarie.

1. Congratulazioni! Il client SSH PuTTy è stato installato correttamente.

## <a name="initiate-the-ssh-connection"></a>Avviare la connessione SSH

1. Accendere Azure Percept DK.

1. Se il kit dev è già connesso a una rete tramite Ethernet o Wi-Fi, andare al passaggio successivo. In caso contrario, connettere il computer host direttamente al punto di accesso Wi-Fi del kit di sviluppo. Come la connessione a qualsiasi altra rete Wi-Fi, aprire le impostazioni di rete e Internet nel computer, fare clic sulla rete seguente e immettere la password di rete quando richiesto:

    - **Nome di rete**: a seconda della versione del sistema operativo del kit di sviluppo, il nome del punto di accesso Wi-Fi è **SCZ-xxxx** o **APD-xxxx** (dove "xxxx" è costituito dalle ultime quattro cifre dell'indirizzo MAC del Dev Kit)
    - **Password**: si trova nella scheda di benvenuto fornita con Dev Kit

    > [!WARNING]
    > Quando si è connessi al punto di accesso Wi-Fi DK di Azure Percept, il computer host perderà temporaneamente la connessione a Internet. Le chiamate alle conferenze video attive, il flusso Web o altre esperienze basate sulla rete verranno interrotte.

1. Completare il processo di connessione SSH in base al client SSH.

### <a name="using-openssh"></a>Uso di OpenSSH

1. Aprire un prompt dei comandi (prompt dei comandi di **avvio**  ->  ).

1. Immettere quanto segue nel prompt dei comandi:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Se il computer è connesso al punto di accesso Wi-Fi del kit di sviluppo, l'indirizzo IP sarà 10.1.1.1. Se il kit di sviluppo è connesso tramite Ethernet, usare l'indirizzo IP locale del dispositivo, che è possibile ottenere dall'hub o dal router Ethernet. Se il kit di sviluppo è connesso tramite Wi-Fi, è necessario usare l'indirizzo IP assegnato a Dev Kit durante l'esperienza di [installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Se il kit dev è connesso a una rete Wi-Fi ma non si conosce l'indirizzo IP, passare ad Azure Percept studio e [aprire il flusso video del dispositivo](./how-to-view-video-stream.md). La barra degli indirizzi nella scheda Visualizzatore flusso video visualizzerà l'indirizzo IP del dispositivo.

1. Immettere la password SSH quando richiesto.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Screenshot dell'accesso al prompt dei comandi SSH aperto.":::

1. Se è la prima volta che si esegue la connessione a dev kit tramite OpenSSH, potrebbe essere richiesto di accettare anche la chiave dell'host. Immettere **Yes** per accettare la chiave.

1. Congratulazioni! Si è connessi correttamente a dev kit tramite SSH.

### <a name="using-putty"></a>Uso di PuTTy

1. Aprire PuTTY. Immettere quanto segue nella finestra di **configurazione di Putty** e fare clic su **Apri** per SSH in dev Kit:

    1. Nome host: [indirizzo IP]
    1. Porta: 22
    1. Tipo di connessione: SSH

    Il **nome host** corrisponde all'indirizzo IP del Dev Kit. Se il computer è connesso al punto di accesso Wi-Fi del kit di sviluppo, l'indirizzo IP sarà 10.1.1.1. Se il kit di sviluppo è connesso tramite Ethernet, usare l'indirizzo IP locale del dispositivo, che è possibile ottenere dall'hub o dal router Ethernet. Se il kit di sviluppo è connesso tramite Wi-Fi, è necessario usare l'indirizzo IP assegnato a Dev Kit durante l'esperienza di [installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Se il kit dev è connesso a una rete Wi-Fi ma non si conosce l'indirizzo IP, passare ad Azure Percept studio e [aprire il flusso video del dispositivo](./how-to-view-video-stream.md). La barra degli indirizzi nella scheda Visualizzatore flusso video visualizzerà l'indirizzo IP del dispositivo.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Screenshot della finestra di configurazione di PuTTy.":::

1. Si aprirà un terminale PuTTy. Quando richiesto, immettere il nome utente e la password SSH nel terminale.

1. Congratulazioni! Si è connessi correttamente a dev kit tramite SSH.

## <a name="next-steps"></a>Passaggi successivi

Dopo la connessione ad Azure Percept DK tramite SSH, è possibile eseguire una serie di attività, tra cui la [risoluzione dei problemi del dispositivo](./troubleshoot-dev-kit.md) e [gli aggiornamenti USB](./how-to-update-via-usb.md).