---
title: Configurare Azure Percept DK
description: Connettere il Dev Kit ad Azure e distribuire il primo modello di intelligenza artificiale
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 8cecd1b57395fa51fd95c824e88885d9c3ae3f09
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023300"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Configurare Azure Percept DK e distribuire il primo modello di intelligenza artificiale

Completare l'esperienza di configurazione di Azure Percept DK per configurare il kit di sviluppo e distribuire il primo modello di intelligenza artificiale. Dopo aver verificato che l'account Azure è compatibile con Azure Percept, sarà necessario:

- Connettere il kit di sviluppo a una rete Wi-Fi
- Configurare un accesso SSH per l'accesso remoto a Dev Kit
- Creare un nuovo hub delle cose da usare con Azure Percept
- Connetti il tuo Dev Kit all'hub e all'account Azure

Se si verificano problemi durante questo processo, vedere la [Guida alla risoluzione dei problemi di installazione](./how-to-troubleshoot-setup.md) per le possibili soluzioni.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (Dev Kit).
- Un computer host basato su Windows, Linux o OS X con funzionalità Wi-Fi e un Web browser.
- Un account Azure con una sottoscrizione attiva. [Crea gratuitamente un account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- L'account Azure deve avere il ruolo **proprietario** o **collaboratore** nella sottoscrizione. Attenersi alla procedura seguente per verificare il ruolo dell'account Azure. Per altre informazioni sulle definizioni dei ruoli di Azure, vedere la [documentazione relativa al controllo degli accessi in base al ruolo di Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

    > [!CAUTION]
    > Se si hanno più account Azure, il browser può memorizzare nella cache le credenziali di un altro account. Per evitare confusione, è consigliabile chiudere tutte le finestre del browser inutilizzate e accedere al [portale di Azure](https://portal.azure.com/) prima di iniziare l'installazione. Per ulteriori informazioni su come verificare di avere eseguito l'accesso con l'account corretto, vedere la [Guida alla risoluzione dei problemi dell'installazione](./how-to-troubleshoot-setup.md) .

### <a name="check-your-azure-account-role"></a>Verificare il ruolo dell'account Azure

Per verificare se l'account Azure è un "proprietario" o "collaboratore" all'interno della sottoscrizione, seguire questa procedura:

1. Passare alla [portale di Azure](https://portal.azure.com/) e accedere con lo stesso account di Azure che si intende usare con Azure Percept.

1. Fare clic sull'icona delle **sottoscrizioni** (sembra un tasto giallo).

1. Selezionare la sottoscrizione dall'elenco. Se la sottoscrizione non viene visualizzata, verificare di aver eseguito l'accesso con l'account Azure corretto. Se si desidera creare una nuova sottoscrizione, attenersi alla [seguente procedura](../cost-management-billing/manage/create-subscription.md).

1. Selezionare **controllo di accesso (IAM)** dal menu sottoscrizione.
1. Fare clic su **Visualizza accesso personale**.
1. Controllare il ruolo:
    - Se il ruolo è elencato come **Reader** o se viene visualizzato un messaggio che indica che non si dispone dell'autorizzazione per visualizzare i ruoli, sarà necessario seguire il processo necessario nell'organizzazione per elevare il ruolo di account.
    - Se il ruolo è elencato come **proprietario** o **collaboratore**, l'account funzionerà con Azure Percept e sarà possibile procedere con l'installazione.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Avviare l'esperienza di installazione di Azure Percept DK

1. Connettere il computer host direttamente al punto di accesso Wi-Fi del kit di sviluppo. Come la connessione a qualsiasi altra rete Wi-Fi, aprire le impostazioni di rete e Internet nel computer, fare clic sulla rete seguente e immettere la password di rete quando richiesto:

    - **Nome di rete**: a seconda della versione del sistema operativo del kit di sviluppo, il nome del punto di accesso Wi-Fi è **SCZ-xxxx** o **APD-xxxx** (dove "xxxx" è costituito dalle ultime quattro cifre dell'indirizzo MAC del Dev Kit)
    - **Password**: si trova nella scheda di benvenuto fornita con Dev Kit

    > [!WARNING]
    > Quando si è connessi al punto di accesso Wi-Fi DK di Azure Percept, il computer host perderà temporaneamente la connessione a Internet. Le chiamate alle conferenze video attive, il flusso Web o altre esperienze basate sulla rete verranno interrotte.

1. Una volta connessi al punto di accesso Wi-Fi del kit di sviluppo, il computer host avvierà automaticamente l'esperienza di installazione in una nuova finestra del browser con **il file con estensione New. Device/** nella barra degli indirizzi. Se la scheda non viene aperta automaticamente, avviare l'installazione di accedendo a [http://10.1.1.1](http://10.1.1.1) . Assicurarsi che il browser sia connesso con le stesse credenziali dell'account di Azure che si intende usare con Azure Percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Pagina iniziale.":::

    > [!CAUTION]
    > Il servizio Web dell'esperienza di installazione verrà arrestato dopo 30 minuti di non utilizzo. In tal caso, riavviare il kit di sviluppo per evitare problemi di connettività con il punto di accesso Wi-Fi del kit di sviluppo.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Completare l'esperienza di installazione di Azure Percept DK

1. Nella schermata **iniziale** fare clic su **Avanti** .

1. Nella pagina **connessione di rete** fare clic su **Connetti a una nuova rete Wi-Fi**.

    Se il kit di sviluppo è già stato connesso alla rete Wi-Fi, fare clic su **Ignora**.

1. Selezionare la rete Wi-Fi dall'elenco delle reti disponibili e fare clic su **Connetti**. Immettere la password di rete quando richiesto.

1. Una volta che il kit di sviluppo si è connesso correttamente alla rete preferita, nella pagina viene visualizzato l'indirizzo IPv4 assegnato a Dev Kit. **Annotare l'indirizzo IPv4 visualizzato nella pagina.** Per la risoluzione dei problemi e gli aggiornamenti dei dispositivi, è necessario l'indirizzo IP quando ci si connette a dev kit tramite SSH.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Copiare l'indirizzo IP.":::

    > [!NOTE]
    > L'indirizzo IP può variare a ogni avvio del dispositivo.

1. Leggere il contratto di licenza, selezionare **dichiaro di aver letto e accetto il contratto di licenza** . è necessario scorrere fino alla fine del contratto, quindi fare clic su **Avanti**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Accettare il contratto di licenza.":::

1. Immettere un nome e una password per l'account SSH e **annotare le informazioni di accesso per un uso successivo**.

    > [!NOTE]
    > SSH (Secure Shell) è un protocollo di rete che consente di connettersi a Dev Kit in modalità remota tramite un computer host.

1. Nella pagina successiva fare clic su **Setup come nuovo dispositivo** per creare un nuovo dispositivo nell'account Azure.

1. Fare clic su **copia** per copiare il codice del dispositivo. Successivamente, fare clic su **Accedi ad Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Copiare il codice del dispositivo.":::

1. Viene visualizzata una nuova scheda del browser con una finestra che indica di **immettere il codice**. Incollare il codice nella finestra e fare clic su **Avanti**. NON chiudere la scheda **iniziale** con l'esperienza di installazione.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Immettere il codice del dispositivo.":::

1. Accedere ad Azure Percept usando le credenziali dell'account di Azure che si useranno con il kit di sviluppo. Lasciare aperta la scheda del browser al termine dell'operazione.

    > [!CAUTION]
    > Il browser può memorizzare nella cache automaticamente altre credenziali. Verificare di aver effettuato l'accesso con l'account corretto.

    Dopo aver eseguito l'accesso ad Azure percent sul dispositivo, tornare alla scheda **Welcome (benvenuto** ) per continuare l'installazione.

1. Quando la pagina **assegnare il dispositivo alla propria Hub Azure** è visualizzata nella scheda **iniziale** , eseguire una delle operazioni seguenti:

    - Se si ha già un hub Internet che si vuole usare con Azure Percept ed è elencato in questa pagina, selezionarlo e passare al passaggio 15.
    - Se non si dispone di un hub o si vuole crearne uno nuovo, fare clic su **Crea un nuovo hub Azure**.

    > [!IMPORTANT]
    > Se si dispone di un hub tutto, ma non è presente nell'elenco, è possibile che sia stato effettuato l'accesso ad Azure Percept con le credenziali errate. Per informazioni, vedere la [Guida alla risoluzione dei problemi di installazione](./how-to-troubleshoot-setup.md) .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Selezionare un hub Internet.":::

1. Per creare un nuovo hub Internet, completare i campi seguenti:

    - Selezionare la sottoscrizione di Azure che si userà con Azure Percept.
    - Selezionare un gruppo di risorse esistente. Se non ne esiste uno, fare clic su **Crea nuovo** e seguire le istruzioni.
    - Selezionare l'area di Azure più vicina alla posizione fisica.
    - Assegnare un nome al nuovo hub Internet.
    - Selezionare il piano tariffario S1 (standard).

    > [!NOTE]
    > Se si ha bisogno di una [velocità effettiva dei messaggi](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput) più elevata per le applicazioni Edge per intelligenza artificiale, è possibile aggiornare l'hub Internet delle cose [a un livello standard superiore](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade) nel portale di Azure in qualsiasi momento. I livelli B e F non supportano Azure Percept.

1. La distribuzione dell'hub Internet può richiedere alcuni minuti. Al termine della distribuzione, fare clic su **registra**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="L'hub Internet è stato distribuito correttamente.":::

1. Immettere il nome di un dispositivo per Dev Kit e fare clic su **Avanti**.

1. Attendere il download dei moduli del dispositivo. l'operazione potrebbe richiedere alcuni minuti.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Finalizzazione del programma di installazione.":::

1. Quando l'installazione del **dispositivo è stata completata.** il kit di sviluppo è stato collegato correttamente all'hub Internet e ha scaricato il software necessario. Il kit di sviluppo si disconnetterà automaticamente dal punto di accesso Wi-Fi, con le due notifiche seguenti:

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Avviso di disconnessione esperienza di installazione.":::

1. Connettere il computer host al Wi-Fi rete a cui si è connessi DevKit nel passaggio 2.

1. Fare clic su **continua fino al portale di Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Passare ad Azure Percept Studio.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>Visualizzare il flusso video di Dev Kit e distribuire un modello di esempio

1. La [pagina di Panoramica di Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) è il punto di avvio per l'accesso a molti flussi di lavoro diversi per lo sviluppo di soluzioni per l'intelligenza artificiale Edge e avanzate. Per iniziare, fare clic su **dispositivi** nel menu a sinistra.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Visualizzare l'elenco dei dispositivi.":::

1. Verificare che il kit di sviluppo sia elencato come **connesso** e fare clic su di esso per visualizzare la pagina del dispositivo.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Selezionare il dispositivo.":::

1. Fare clic su **Visualizza flusso del dispositivo**. Se è la prima volta che si Visualizza il flusso video del dispositivo, verrà visualizzata una notifica che indica che è in corso la distribuzione di un nuovo modello nell'angolo superiore destro. L'operazione potrebbe richiedere alcuni minuti.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Visualizzare il flusso video.":::

    Una volta distribuito il modello, si otterrà un'altra notifica con un collegamento **Visualizza flusso** . Fare clic sul collegamento per visualizzare il flusso video dalla videocamera Azure Percept Vision in una nuova finestra del browser. Il kit dev viene precaricato con un modello di intelligenza artificiale che esegue automaticamente il rilevamento degli oggetti di molti oggetti comuni.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Vedere Rilevamento oggetti.":::

1. Azure Percept Studio include anche una serie di modelli di intelligenza artificiale di esempio. Per distribuire un modello di esempio in dev Kit, tornare alla pagina del dispositivo e fare clic su **Distribuisci un modello di esempio**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Esplora i modelli predefiniti.":::

1. Selezionare un modello di esempio dalla libreria e fare clic su **Distribuisci nel dispositivo**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Vedere Rilevamento di oggetti in azione.":::

1. Una volta che il modello è stato distribuito correttamente, viene visualizzata una notifica con un collegamento **Visualizza flusso** nell'angolo superiore destro dello schermo. Per visualizzare l'inferenza del modello in azione, fare clic sul collegamento nella notifica o tornare alla pagina del dispositivo e fare clic su **Visualizza flusso del dispositivo**. Tutti i modelli eseguiti in precedenza in dev Kit verranno ora sostituiti con il nuovo modello.

## <a name="video-walkthrough"></a>Procedura dettagliata video

Per una procedura dettagliata visiva dei passaggi descritti in precedenza, vedere il video seguente (l'esperienza di installazione inizia alle 0:50):

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una soluzione per la visione senza codice](./tutorial-nocode-vision.md)