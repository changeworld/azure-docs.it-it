---
title: Configurare Azure Percept DK
description: Connettere il Dev Kit ad Azure e distribuire il primo modello di intelligenza artificiale
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 3540204d66bb589c567514f92a9a8acb2159e343
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665517"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Configurare Azure Percept DK e distribuire il primo modello di intelligenza artificiale

Introduzione ad Azure Percept DK e Azure Percept studio con l'esperienza di installazione di Azure Percept DK per connettere il dispositivo ad Azure e distribuire il primo modello di intelligenza artificiale. Dopo aver verificato che l'account Azure è compatibile con Azure Percept studio, è necessario completare l'esperienza di installazione per assicurarsi che Azure Percept DK sia configurato per la creazione di modelli di prova di intelligenza artificiale di Edge.

Se si verificano problemi durante questo Avvio rapido, vedere la guida alla [risoluzione dei problemi](./troubleshoot-dev-kit.md) per le possibili soluzioni.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK.
- Un computer host basato su Windows, Linux o OS X con funzionalità Wi-Fi e un Web browser.
- Un account Azure con una sottoscrizione attiva. [Crea gratuitamente un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- L'account Azure deve avere il ruolo "proprietario" o "collaboratore" per la sottoscrizione. Scopri di più sulle definizioni dei ruoli di Azure

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

Per verificare se l'account Azure è un "proprietario" o "collaboratore" nella sottoscrizione, seguire questa procedura.

1. Passare alla portale di Azure e accedere con lo stesso account di Azure che si intende usare con Azure Percept Studio. 

    > [!NOTE]
    > Se si hanno più account Azure, il browser può memorizzare nella cache le credenziali di un altro account. Per ulteriori informazioni su come verificare di avere eseguito l'accesso con l'account corretto, vedere la guida alla risoluzione dei problemi.

1. Espandere il menu principale dall'angolo superiore sinistro della schermata e fare clic su "sottoscrizioni" oppure selezionare "sottoscrizioni" dal menu di icone nella home page. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. Selezionare la sottoscrizione dall'elenco. Se la sottoscrizione non viene visualizzata nell'elenco, verificare di aver eseguito l'accesso con l'account Azure corretto. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
Se si desidera creare una nuova sottoscrizione, attenersi alla [seguente procedura](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. Dal menu sottoscrizione selezionare "controllo di accesso (IAM)".
1. Fai clic sul pulsante "Visualizza accesso personale"
1. Controllare il ruolo
    - Se viene visualizzato il ruolo di "Reader" o se viene visualizzato un messaggio che indica che non si dispone delle autorizzazioni per visualizzare i ruoli, sarà necessario seguire il processo necessario nell'organizzazione per ottenere il ruolo di account con privilegi elevati.
    - Se viene visualizzato il ruolo "proprietario" o "collaboratore", l'account funzionerà con Azure Percept Studio. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Avviare l'esperienza di installazione di Azure Percept DK

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. Connettere il computer host direttamente al punto di accesso Wi-Fi del kit di sviluppo. Questa operazione viene eseguita esattamente come la connessione ad altre reti Wi-Fi,
    - **nome di rete**: SCZ-xxxx (dove "xxxx" è le ultime quattro cifre dell'indirizzo di rete Mac del Dev Kit)
    - **password**: si trova nella scheda di benvenuto fornita con Dev Kit

    > [!WARNING]
    > Quando si è connessi al punto di accesso Wi-Fi di Azure Percept DK, il computer host perderà temporaneamente la connessione a Internet. Le chiamate alle conferenze video attive, lo streaming web o altre esperienze basate sulla rete verranno interrotte fino al completamento del passaggio 3 dell'esperienza di configurazione di Azure Percept DK.

1. Una volta stabilita la connessione al punto di accesso Wi-Fi del kit di sviluppo, il dispositivo host avvierà automaticamente l'esperienza di installazione di Azure Percept DK in una nuova finestra del browser. Se non viene aperto automaticamente, è possibile avviarlo manualmente aprendo una finestra del browser e passando a [http://10.1.1.1](http://10.1.1.1) . 

1. Ora che è stata avviata l'esperienza di installazione di Azure Percept, è possibile procedere con l'esperienza di installazione. 

    > [!NOTE]
    > Il servizio Web di installazione di Azure Percept DK verrà arrestato dopo 30 minuti di inutilizzo e al completamento dell'esperienza di installazione. Quando si verifica questo problema, è consigliabile riavviare il kit di sviluppo per evitare problemi di connettività con il punto di accesso Wi-Fi del kit di sviluppo.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Completare l'esperienza di installazione di Azure Percept DK

1. Per iniziare, fare clic su **Avanti** nella schermata iniziale.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Pagina iniziale.":::

1. Connetti a Wi-Fi: nella pagina connessione di rete fare clic su **Connetti a una nuova rete Wi** -Fi per connettere la DevKit alla rete Wi-Fi.

    Se questo Dev Kit è stato precedentemente connesso alla rete Wi-Fi o se si è già connessi all'esperienza di installazione di Azure Percept DK tramite la rete Wi-Fi, fare clic sul collegamento **Ignora** .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Connettersi al Wi-Fi.":::

1. Selezionare la rete Wi-Fi dalle connessioni disponibili e connettersi. (Richiede la password Wi-Fi locale)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Selezionare rete Wi-Fi."::: 

1. Copiare l'indirizzo IP: una volta che la DevKit è stata connessa correttamente alla rete preferita, annotare l' **indirizzo IPv4** visualizzato nella pagina. **Questo indirizzo IP sarà necessario più avanti in questa guida** introduttiva. 

    > [!NOTE]
    > L'indirizzo IP può variare a ogni avvio del dispositivo.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Copiare l'indirizzo IP.":::

1. Esaminare e accettare il contratto di licenza. leggere il contratto di licenza, selezionare **dichiaro di aver letto e accetto il contratto di licenza (è** necessario scorrere fino alla fine del contratto) e fare clic su **Avanti**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Accettare il contratto di licenza.":::

1. Creare l'account di accesso SSH: configurare SSH per l'accesso remoto alla devkit. Creare un nome utente e una password SSH. 

    > [!NOTE]
    > SSH (Secure Shell) è un protocollo di rete usato per la comunicazione sicura tra il dispositivo host e Dev Kit. Per altre informazioni su SSH, vedere [questo articolo](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="Creare un account SSH."::: 

1. Avviare il processo di connessione a Dev Kit. nella schermata successiva fare clic su **Connetti con un nuovo dispositivo** per avviare il processo di connessione di Dev Kit all'hub Azure. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Connettersi ad Azure."::: 

1. Copiare il codice del dispositivo: fare clic sul collegamento **copia** per copiare il codice del dispositivo. Fare quindi clic su **Accedi ad Azure**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Copiare il codice del dispositivo."::: 

1. Incollare il codice del dispositivo: verrà aperta una nuova scheda del browser con una finestra che richiede il codice del dispositivo copiato. Incollare il codice nella finestra e fare clic su **Avanti**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Immettere il codice del dispositivo."::: 

1. Accedere ad Azure: la finestra successiva richiede l'accesso con l'account Azure verificato all'inizio della Avvio rapido. Immettere le credenziali di accesso e fare clic su **Avanti**. 

    > [!NOTE]
    > Il browser può memorizzare nella cache automaticamente altre credenziali. Verificare di aver effettuato l'accesso con l'account corretto.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Accedere ad Azure.":::
 
1. Non chiudere la scheda Esplorazione dell'esperienza di installazione in questo passaggio: dopo l'accesso verrà visualizzata una schermata che conferma che è stato effettuato l'accesso. Sebbene si richiuda la finestra, è **consigliabile non chiudere le finestre**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="Accesso riuscito."::: 

1. Tornare alla scheda del browser in cui è ospitata l'esperienza di installazione.
1. Selezionare l'opzione dell'hub Internet delle cose
    - Se si dispone già di un hub Internet e questo è elencato in questa pagina, è possibile selezionarlo e **passare al passaggio 17**.
    - Se non si dispone di un hub o si vuole crearne uno nuovo, passare alla parte inferiore dell'elenco e fare clic su **Crea un nuovo hub Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Selezionare un hub Internet."::: 

1. Creare il nuovo hub Internet. compilare tutti i campi in questa pagina. 
    - Selezionare la sottoscrizione di Azure che si userà con Azure Percept Studio
    - Selezionare un gruppo di risorse esistente. Se non ne esiste uno, fare clic su "Crea nuovo" e seguire le istruzioni. 
    - Selezionare l'area di Azure. 
    - Assegnare un nome al nuovo hub Internet
    - Selezionare il piano tariffario (corrisponde in genere alla sottoscrizione)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="Creare un nuovo hub Internet."::: 

1. Attendere la distribuzione dell'hub Internet delle cose. potrebbero essere necessari alcuni minuti.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="Distribuire l'hub Internet."::: 

1. Registrare il Dev Kit: al termine della distribuzione, fare clic sul pulsante **Register (registra** ).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="L'hub Internet è stato distribuito correttamente."::: 

1. Assegnare un nome a Dev Kit. Immettere il nome di un dispositivo per Dev Kit e fare clic su **Avanti**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="Assegnare un nome al dispositivo."::: 

1. Attendere il download dei modelli di intelligenza artificiale predefiniti. l'operazione potrebbe richiedere alcuni minuti

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Finalizzazione del programma di installazione."::: 

1. Vedere visione artificiale in azione: il DevKit è stato collegato correttamente all'hub Azure Internet e ha ricevuto il modello di rilevamento oggetti visione artificiale predefinito. La videocamera per la visione di Azure Percept è ora in grado di eseguire il rilevamento degli oggetti senza una connessione al cloud. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="Fare clic su Anteprima video output."::: 
       
    > [!NOTE]
    > Il processo di onboarding e la connessione all'accesso Wi-Fi del dispositivo al computer host si arrestano a questo punto, ma il kit dev resterà connesso a Internet.   È possibile riavviare l'esperienza di onboarding con un riavvio del kit di sviluppo. in questo modo sarà possibile tornare al processo di onboarding e riconnettere il dispositivo a un hub Internet diverso associato alla stessa sottoscrizione di Azure o a una sottoscrizione diversa.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Avviso di disconnessione esperienza di installazione."::: 

1. Continuare con il portale di Azure. tornare alla finestra di installazione e fare clic sul pulsante **continuare con il portale di Azure** per iniziare a creare i modelli di intelligenza artificiale personalizzati in Azure Percept Studio.

    > [!NOTE]
    > Verificare che il computer host non sia più connesso al punto di accesso Dev Kit nelle impostazioni Wi-Fi ed è ora riconnesso al Wi-Fi locale.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Passare ad Azure Percept Studio."::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Visualizzare il dispositivo in Azure Percept studio e distribuire app di esempio predefinite comuni

1. Visualizzare l'elenco dei dispositivi nella pagina Panoramica di Azure Percept. La pagina Panoramica di Azure Percept è il punto di avvio per l'accesso a molti flussi di lavoro diversi per lo sviluppo di soluzioni e modelli Edge per intelligenza artificiale avanzati e avanzati

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Visualizzare l'elenco dei dispositivi.":::
    
1. Verificare che il dispositivo appena creato sia connesso e fare clic su di esso.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Selezionare il dispositivo.":::

1. Visualizzare il flusso del dispositivo per visualizzare le informazioni visualizzate dalla videocamera Dev Kit. Un modello di rilevamento di oggetti predefinito viene distribuito automaticamente e rileverà una serie di oggetti comuni.

    > [!NOTE]
    > la prima volta che si esegue questa operazione in un nuovo dispositivo si riceverà una notifica che indica che è in corso la distribuzione di un nuovo modulo nell'angolo superiore destro.  Una volta eseguita la competizione, sarà possibile avviare la finestra con il flusso video della fotocamera. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Visualizzare il flusso video.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Vedere Rilevamento oggetti.":::

1. Esplora i modelli di intelligenza artificiale di esempio predefiniti.   Azure Precett Studio include numerosi esempi predefiniti comuni che possono essere distribuiti con un solo clic.   Selezionare "Distribuisci un modello di esempio" per visualizzarli e distribuirli.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Esplora i modelli predefiniti.":::
    
1. Distribuire un nuovo esempio predefinito nel dispositivo connesso. Selezionare un esempio dalla libreria e fare clic su "Distribuisci nel dispositivo"

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-1-select-prebuilt.png" alt-text="Selezionare un predefinito.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Vedere Rilevamento di oggetti in azione.":::

## <a name="next-steps"></a>Passaggi successivi

È possibile seguire un flusso simile per provare i [modelli di riconoscimento vocale predefiniti](./tutorial-no-code-speech.md).