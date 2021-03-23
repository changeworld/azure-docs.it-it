---
title: Configurare un'appliance Azure Migrate per Hyper-V
description: Informazioni su come configurare un'appliance Azure Migrate per la valutazione e la migrazione dei server in Hyper-V.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 85ab07e0b81bf258a56956f5f0f7e80bad6a32fe
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775222"
---
# <a name="set-up-an-appliance-for-servers-on-hyper-v"></a>Configurare un'appliance per i server in Hyper-V

Seguire questo articolo per configurare l'appliance Azure Migrate per l'individuazione e la valutazione dei server in Hyper-V con lo strumento [Azure migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

Il [Azure migrate Appliance](migrate-appliance.md)  è un'appliance semplice usata da Azure migrate: individuazione e valutazione/migrazione per individuare i server locali in Hyper-V e inviare i dati relativi a metadati/prestazioni del server in Azure.

È possibile distribuire l'appliance usando due metodi:

- Configurare su un server in Hyper-V usando un disco rigido virtuale scaricato. Questo metodo è descritto in questo articolo.
- Configurare in un server in Hyper-V o in un server fisico con uno script del programma di installazione di PowerShell. [Questo metodo](deploy-appliance-script.md) deve essere usato se non è possibile configurare un server usando un disco rigido virtuale o se si è in Azure per enti pubblici.

Dopo aver creato l'appliance, verificare che sia in grado di connettersi a Azure Migrate: individuazione e valutazione, configurazione per la prima volta e registrazione con il progetto Azure Migrate.

## <a name="appliance-deployment-vhd"></a>Distribuzione Appliance (VHD)

Per configurare l'appliance usando un modello VHD:

- Specificare un nome di appliance e generare una chiave del progetto di Azure Migrate nel portale.
- Scaricare un disco rigido virtuale Hyper-V compresso dal portale di Azure.
- Creare l'appliance e verificare che sia in grado di connettersi a Azure Migrate: individuazione e valutazione.
- Configurare l'appliance per la prima volta e registrarla nel progetto di Azure Migrate con la chiave del progetto di Azure Migrate.

### <a name="generate-the-azure-migrate-project-key"></a>Generare la chiave del progetto Azure Migrate

1. Negli **obiettivi di migrazione**  >  **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, selezionare **individua**.
2. In **individua server**  >  **i server sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3. In **1: genera chiave progetto** specificare un nome per il dispositivo Azure migrate che verrà configurato per l'individuazione dei server in Hyper-V. il nome deve essere alfanumerico con un massimo di 14 caratteri.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Individua server durante la creazione delle risorse.
1. Al termine della creazione delle risorse di Azure, viene generata una **Chiave progetto Azure Migrate**.
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="download-the-vhd"></a>Scaricare il disco rigido virtuale

In **2: Scaricare l'appliance di Azure Migrate** selezionare il file VHD e fare clic su **Scarica**.

   ![Selezioni per Individua macchine virtuali](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Selezioni per Genera chiave](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Aprire una finestra di comando amministratore nel server in cui è stato scaricato il file.
2. Eseguire il comando seguente per generare il codice hash per il disco rigido virtuale
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance"></a>Creare l'appliance

Importare il file scaricato e creare un'appliance.

1. Estrarre il file VHD compresso in una cartella nell'host Hyper-V in cui è ospitata l'appliance. Vengono estratte tre cartelle.
2. Aprire la console di gestione di Hyper-V. In **Azioni** fare clic su **Importa macchina virtuale**.

    ![Distribuire il disco rigido virtuale](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Nella procedura guidata Importa macchina virtuale selezionare **Prima di iniziare**, quindi fare clic su **Avanti**.
3. In **Individua cartella** specificare la cartella contenente il disco rigido virtuale estratto. Quindi fare clic su **Next**.
1. In **Selezione macchina virtuale** fare clic su **Avanti**.
2. In **Scegliere il tipo di importazione** fare clic su **Copia macchina virtuale (crea nuovo ID univoco)** . Quindi fare clic su **Next**.
3. In **Selezione destinazione** lasciare l'impostazione predefinita. Fare clic su **Avanti**.
4. In **Cartelle di archiviazione** lasciare l'impostazione predefinita. Fare clic su **Avanti**.
5. In **Choose Network (Scegli rete**) specificare il Commuter virtuale che il server utilizzerà. È necessario che il commutatore abbia connettività Internet per inviare dati ad Azure.
6. In **Riepilogo** rivedere le impostazioni. Fare clic su **Fine**.
7. Nella console di gestione di Hyper-V selezionare **Macchine virtuali** e avviare la VM.


### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che l'appliance possa connettersi agli URL di Azure per i cloud [pubblici](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).

### <a name="configure-the-appliance"></a>Configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [script PowerShell](deploy-appliance-script.md) invece del disco rigido virtuale scaricato, i primi due passaggi di questa procedura non sono pertinenti.

1. Nella console di gestione di Hyper-V > **macchine virtuali** fare clic con il pulsante destro del mouse sul server > **Connetti**.
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in qualsiasi sistema in grado di connettersi al dispositivo e aprire l'URL dell'app Web dell'appliance: **nome dell'*Appliance https://o indirizzo IP*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance facendo clic sul relativo collegamento.
1. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **Connectivity** (Connettività): l'app verifica che il server abbia accesso a Internet. Se il server usa un proxy:
      - Fare clic su **setup proxy** to e specificare l'indirizzo proxy (nel formato http://ProxyIPAddress o nella http://ProxyFQDN) porta di ascolto.
      - Se il proxy richiede l'autenticazione, specificare le credenziali.
      - È supportato solo il proxy HTTP.
      - Se sono stati aggiunti dettagli del proxy o sono stati disabilitati il proxy e/o l'autenticazione, fare clic su **Save** (Salva) per attivare di nuovo il controllo della connettività.
    - **Time sync** (Sincronizzazione ora): viene verificata l'ora. Per il corretto funzionamento dell'individuazione di server, l'ora dell'appliance deve essere sincronizzata con l'ora di Internet.
    - **Installare gli aggiornamenti**: Azure migrate: verifiche di individuazione e valutazione per cui nel dispositivo sono installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **View appliance services** (Visualizza servizi appliance) per visualizzare lo stato e le versioni dei componenti in esecuzione nel dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave del progetto** copiata dal portale. Se non si dispone della chiave, passare a **Azure migrate: individuazione e valutazione> individua> Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Per eseguire l'autenticazione con Azure, è necessario un codice del dispositivo. Facendo clic su **Accedi** si aprirà una finestra modale con il codice del dispositivo, come illustrato di seguito.

    ![Finestra modale con il codice del dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Fare clic su **Copy code & Login** (Copia il codice e accedi) per copiare il codice del dispositivo e aprire una richiesta di accesso ad Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella scheda nuovo incollare il codice del dispositivo e accedere usando il nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Se si chiude la scheda per errore senza eseguire l'accesso, è necessario aggiornare la scheda del browser di gestione configurazione dell'appliance per abilitare di nuovo il pulsante di accesso.
1. Dopo aver eseguito l'accesso, tornare nella scheda precedente di gestione configurazione dell'appliance.
4. Se l'account utente di Azure usato per la registrazione ha le [autorizzazioni](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Al termine della registrazione dell'appliance è possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.



### <a name="delegate-credentials-for-smb-vhds"></a>Delegare le credenziali per i dischi rigidi virtuali SMB

Se i dischi rigidi virtuali sono in esecuzione in SMB, è necessario abilitare la delega delle credenziali dall'appliance agli host Hyper-V. Per eseguire questa operazione dall'appliance:

1. Nel dispositivo eseguire questo comando. HyperVHost1/HyperVHost2 sono nomi host di esempio.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. In alternativa, eseguire questa operazione nell'editor Criteri di gruppo locali dell'appliance:
    - In **Criteri del computer locale** > **Configurazione computer** fare clic su **Modelli amministrativi** > **Sistema** > **Delega di credenziali**.
    - Fare doppio clic su **Consenti delega credenziali nuove** e selezionare **Abilitata**.
    - In **Opzioni** fare clic su **Mostra** e aggiungere all'elenco ogni host Hyper-V da individuare, con **wsman/** come prefisso.
    - In **Delega di credenziali** fare doppio clic su **Consenti delega credenziali nuove solo con autenticazione server NTLM**. Anche in questo caso, aggiungere all'elenco ogni host Hyper-V da individuare, con **wsman/** come prefisso.

## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

Connettersi dal dispositivo a host o cluster Hyper-V e avviare l'individuazione.

1. In **passaggio 1: fornire le credenziali dell'host Hyper-v** fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **nome utente** e **password** per un host o cluster Hyper-v che l'appliance utilizzerà per individuare i server. Fare clic su **Save**.
1. Per aggiungere più credenziali contemporaneamente e salvarle, fare clic su **Aggiungi altre**. Sono supportate più credenziali per l'individuazione di server in Hyper-V.
1. In **Passaggio 2: Specificare i dettagli per host/cluster Hyper-V** fare clic su **Aggiungi origine di individuazione** per specificare **Indirizzo IP/FQDN** dell'host/cluster Hyper-V e il nome descrittivo per le credenziali per la connessione all'host/cluster.
1. È possibile selezionare **Add single item** (Aggiungi singolo elemento) o **Add multiple items** (Aggiungi più elementi). È disponibile anche un'opzione per fornire i dettagli dell'host/cluster Hyper-V tramite **Importa CSV**.

    ![Selezioni per l'aggiunta dell'origine di individuazione](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Se si sceglie **Add single item** (Aggiungi singolo elemento) è necessario specificare un nome descrittivo per le credenziali e **Indirizzo IP/FQDN** dell'host/cluster Hyper-V, quindi fare clic su **Salva**.
    - Se si sceglie **Aggiungi più elementi** _(selezionato per impostazione predefinita)_, è possibile aggiungere più record contemporaneamente specificando l' **indirizzo IP/FQDN** del cluster o dell'host Hyper-V con il nome descrittivo per le credenziali nella casella di testo. Verificare * * i record aggiunti e fare clic su **Salva**.
    - Se si sceglie **Importa CSV** è possibile scaricare un file modello CSV, quindi compilare il file con **Indirizzo IP/FQDN** dell'host/cluster Hyper-V e il nome descrittivo per le credenziali. Importare quindi il file nell'appliance, **verificare** i record nel file e fare clic su **Salva**.

1. Quando si fa clic su Salva, l'appliance tenterà di convalidare la connessione agli host/cluster Hyper-V aggiunti e visualizzerà lo **Stato di convalida** nella tabella per ogni host/cluster.
    - Per gli host o i cluster convalidati correttamente, è possibile visualizzare altri dettagli facendo clic sul relativo indirizzo IP/FQDN.
    - Se la convalida non riesce per un host, esaminare l'errore facendo clic su **Convalida non riuscita** nella colonna Stato della tabella. Risolvere il problema e ripetere la convalida.
    - Per rimuovere gli host o i cluster, fare clic su **Elimina**.
    - Non è possibile rimuovere un host specifico da un cluster. È possibile rimuovere solo l'intero cluster.
    - È possibile aggiungere un cluster, anche in caso di problemi con host specifici al suo interno.
1. È possibile **rivalidare** la connettività a host/cluster in qualsiasi momento prima di avviare l'individuazione.
1. Fare clic su **Avvia individuazione per avviare** l'individuazione server da host/cluster convalidati correttamente. Dopo l'avvio dell'individuazione, è possibile controllare lo stato dell'individuazione per ogni host/server nella tabella.

Viene avviata l'individuazione. Per la visualizzazione dei metadati dei server individuati nel portale di Azure sono necessari circa 2 minuti per ogni host.

## <a name="verify-servers-in-the-portal"></a>Verificare i server nel portale

Dopo l'individuazione, è possibile verificare se i server vengono visualizzati nel portale.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure migrate-Windows, Linux e SQL Server**  >  **Azure migrate: pagina individuazione e valutazione** , fare clic sull'icona che Visualizza il conteggio per i **server individuati**.

## <a name="next-steps"></a>Passaggi successivi

Prova [Hyper-V Assessment](tutorial-assess-hyper-v.md) con Azure migrate: individuazione e valutazione.