---
title: Individuare i server in Hyper-V con Azure Migrate individuazione e valutazione
description: Informazioni su come individuare i server locali in Hyper-V con lo strumento Azure Migrate Discovery and Assessment.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: mvc
ms.openlocfilehash: d108774aea2d7ac7773dab06efc977dd9e501eb5
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108775"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Esercitazione: individuazione di server in esecuzione in Hyper-V con Azure Migrate: individuazione e valutazione

Il percorso di migrazione ad Azure prevede l'individuazione dei carichi di lavoro e dell'inventario locale. 

Questa esercitazione illustra come individuare i server locali negli host Hyper-V con lo strumento Azure Migrate: Discovery and Assessment, usando un'appliance di Azure Migrate Lightweight. Si distribuisce l'appliance come server nell'host Hyper-V per individuare continuamente i metadati del computer e delle prestazioni.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un account Azure
> * Preparare l'ambiente Hyper-V per l'individuazione.
> * Creare un progetto.
> * Configurare l'appliance di Azure Migrate.
> * Avviare l'individuazione continua.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e prevedono l'uso delle opzioni predefinite.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, verificare che siano rispettati i prerequisiti seguenti.

**Requisito** | **Dettagli**
--- | ---
**Host Hyper-V** | Gli host Hyper-V in cui si trovano i server possono essere autonomi o in un cluster.<br/><br/> L'host deve eseguire Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2.<br/><br/> Verificare che le connessioni in ingresso siano consentite sulla porta WinRM 5985 (HTTP), in modo che l'appliance possa connettersi ai metadati del server di pull e ai dati sulle prestazioni usando una sessione di Common Information Model (CIM).
**Distribuzione dell'appliance** | L'host Hyper-V necessita di risorse per allocare un server per l'appliance:<br/><br/> -16 GB di RAM, 8 vCPU e circa 80 GB di spazio di archiviazione su disco.<br/><br/> : Un Commuter virtuale esterno e l'accesso a Internet nell'appliance, direttamente o tramite un proxy.
**Server** | I server possono eseguire qualsiasi sistema operativo Windows o Linux. 

## <a name="prepare-an-azure-user-account"></a>Preparare un account utente Azure

Per creare un progetto e registrare il dispositivo di Azure Migrate, è necessario un account con:
- Autorizzazioni di collaboratore o proprietario per una sottoscrizione di Azure.
- Autorizzazioni per la registrazione di app Azure Active Directory (AAD).

Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione. Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare le autorizzazioni nel modo seguente:

1. Nel portale di Azure cercare "sottoscrizioni" e in **Servizi** selezionare **Sottoscrizioni**.

    ![Casella di ricerca per cercare la sottoscrizione di Azure](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Nella pagina **sottoscrizioni** selezionare la sottoscrizione in cui si desidera creare un progetto. 
3. Nella sottoscrizione selezionare **Controllo di accesso (IAM)**  > **Verifica l'accesso**.
4. In **Verifica l'accesso** cercare l'account utente pertinente.
5. In **Aggiungi un'assegnazione di ruolo** fare clic su **Aggiungi**.

    ![Cercare un account utente per verificare l'accesso e assegnare un ruolo](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. In **Aggiungi un'assegnazione di ruolo** selezionare il ruolo Collaboratore o Proprietario e selezionare l'account (azmigrateuser nell'esempio). Fare quindi clic su **Salva**.

    ![Viene aperta la pagina Aggiungi assegnazione di ruolo per assegnare un ruolo all'account](./media/tutorial-discover-hyper-v/assign-role.png)

1. Per registrare l'appliance, l'account Azure deve avere le **autorizzazioni per registrare le app di AAD.**
1. In portale di Azure passare a **Azure Active Directory**  >    >  **impostazioni utente** utenti.
1. In **Impostazioni utente** verificare che gli utenti di Azure AD possano registrare le applicazione (impostato su **Sì** per impostazione predefinita).

    ![Verificare che in Impostazioni utente che gli utenti possano registrare le app Active Directory](./media/tutorial-discover-hyper-v/register-apps.png)

9. Nel caso in cui le impostazioni di ' Registrazioni app ' siano impostate su' No ', richiedere all'amministratore globale o tenant di assegnare l'autorizzazione necessaria. In alternativa, l'amministratore globale o tenant può assegnare il ruolo **sviluppatore applicazione** a un account per consentire la registrazione dell'app AAD. [Altre informazioni](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-hyper-v-hosts"></a>Preparare gli host Hyper-V

È possibile preparare gli host Hyper-V manualmente o tramite uno script. I passaggi di preparazione sono riepilogati nella tabella. Lo script li prepara automaticamente.

**Step** | **Script** | **Manuale**
--- | --- | ---
Verificare i requisiti dell'host | Verifica che l'host esegua una versione supportata di Hyper-V e il ruolo Hyper-V.<br/><br/>Abilita il servizio Gestione remota Windows e apre le porte 5985 (HTTP) e 5986 (HTTPS) nell'host (necessario per la raccolta dei metadati). | L'host deve eseguire Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2.<br/><br/> Verificare che le connessioni in ingresso siano consentite sulla porta WinRM 5985 (HTTP), in modo che l'appliance possa connettersi ai metadati del server di pull e ai dati sulle prestazioni usando una sessione di Common Information Model (CIM).<br/><br/> Lo script non è attualmente supportato in host con impostazioni locali diverse dall'inglese.  
Verificare la versione di PowerShell | Verifica che lo script sia in esecuzione in una versione di PowerShell supportata. | Verificare di eseguire PowerShell versione 4.0 o successiva nell'host Hyper-V.
Creare un account | Verifica che siano disponibili le autorizzazioni corrette nell'host Hyper-V.<br/><br/> Consente di creare un account utente locale con le autorizzazioni corrette. | Opzione 1: Preparare un account con accesso come amministratore al computer host Hyper-V.<br/><br/> Opzione 2: Preparare un account amministratore locale o un account amministratore di dominio e aggiungere l'account a questi gruppi: Remote Management Users, Hyper-V Administrators e Performance Monitor Users.
Abilitare la comunicazione remota di PowerShell | Abilita la comunicazione remota di PowerShell nell'host, in modo che l'appliance Azure Migrate possa eseguire i comandi di PowerShell nell'host, tramite una connessione WinRM. | Per configurare, in ogni host aprire una console di PowerShell come amministratore ed eseguire il comando seguente: ``` powershell Enable-PSRemoting -force ```
Configurare i servizi di integrazione Hyper-V | Verifica che l'Integration Services Hyper-V sia abilitata in tutti i server gestiti dall'host. | [Abilitare Integration Services Hyper-V](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) in ogni server.<br/><br/> Se si esegue Windows Server 2003, [seguire queste istruzioni](prepare-windows-server-2003-migration.md).
Delega credenziali se i dischi server si trovano in condivisioni SMB remote | Delega le credenziali | Eseguire questo comando per consentire a CredSSP di delegare le credenziali in host che eseguono Server con dischi in condivisioni SMB: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> È possibile eseguire questo comando in remoto in tutti gli host Hyper-V.<br/><br/> Se si aggiungono nuovi nodi host in un cluster, vengono aggiunti automaticamente per l'individuazione, ma è necessario abilitare CredSSP manualmente.<br/><br/> Quando si configura l'appliance, si completa la configurazione di CredSSP [abilitandolo nell'appliance](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>Eseguire lo script

1. Scaricare lo script dall'[Area download Microsoft](https://aka.ms/migrate/script/hyperv). Lo script è firmato da Microsoft per la crittografia.
2. Convalidare l'integrità dello script usando i file hash MD5 o SHA256. I valori hashtag sono riportati di seguito. Eseguire questo comando per generare l'hash per lo script:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Esempio di utilizzo:

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Dopo aver convalidato l'integrità dello script, eseguire lo script in ogni host Hyper-V con questo comando di PowerShell con autorizzazioni elevate:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
I valori degli hash sono:

**Hash** |  **Valore**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Configurare un progetto

Configurare un nuovo progetto.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Panoramica** selezionare **Crea progetto**.
5. In **Crea progetto** selezionare il gruppo di risorse e la sottoscrizione di Azure. Creare un gruppo di risorse, se non è presente.
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caselle per il nome del progetto e l'area](./media/tutorial-discover-hyper-v/new-project.png)

7. Selezionare **Crea**.
8. Attendere alcuni minuti per la distribuzione del progetto. Lo strumento **Azure migrate: individuazione e valutazione** viene aggiunto per impostazione predefinita al nuovo progetto.

![Pagina che mostra Azure Migrate: strumento di individuazione e valutazione aggiunto per impostazione predefinita](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Se è già stato creato un progetto, è possibile usare lo stesso progetto per registrare appliance aggiuntive per individuare e valutare un numero maggiore di server. [Altre informazioni](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurare l'appliance

Azure Migrate si basa su un'appliance leggera di Azure Migrate. L'Appliance esegue l'individuazione del server e invia i metadati di configurazione e prestazioni del server a Azure Migrate. È possibile configurare l'appliance distribuendo un file VHD che può essere scaricato dal progetto.

> [!NOTE]
> Se per qualche motivo non è possibile configurare l'appliance usando il modello, è possibile configurarlo usando uno script di PowerShell in un server Windows Server 2016 esistente. [Altre informazioni](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)

Questa esercitazione configura l'appliance in un server in esecuzione in un ambiente Hyper-V, come indicato di seguito:

1. Specificare un nome per il dispositivo e generare una chiave del progetto nel portale.
1. Scaricare un disco rigido virtuale Hyper-V compresso dal portale di Azure.
1. Creare l'appliance e verificare che sia in grado di connettersi a Azure Migrate: individuazione e valutazione.
1. Configurare il dispositivo per la prima volta e registrarlo con il progetto usando la chiave del progetto.

### <a name="1-generate-the-project-key"></a>1. generare la chiave del progetto

1. In server degli **obiettivi di migrazione**  >    >  **Azure migrate: individuazione e valutazione** selezionare **individua**.
2. In **individua server**  >  **i server sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3. In **1: genera chiave progetto** specificare un nome per il dispositivo Azure migrate che si configurerà per l'individuazione dei server. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Discover server durante la creazione delle risorse.
1. Una volta completata la creazione delle risorse di Azure, viene generata una **chiave del progetto** .
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="2-download-the-vhd"></a>2. scaricare il disco rigido virtuale

In **2: Scaricare l'appliance di Azure Migrate** selezionare il file VHD e fare clic su **Scarica**.

### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.

2. Eseguire il comando di PowerShell seguente per generare il codice hash per il file ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Esempio di utilizzo: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Verificare le versioni più recenti dell'appliance e i valori hash:

    - Per il cloud pubblico di Azure:

        **Scenario** | **Scaricare** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140422) |  40aa037987771794428b1c6ebee2614b092e6d69ac56d48a2bbc75eeef86c99a

    - Per Azure per enti pubblici:

        **Scenario** _ | _ *Download** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-an-appliance"></a>3. creare un'appliance

Importare il file scaricato e creare un'appliance.

1. Estrarre il file VHD compresso in una cartella nell'host Hyper-V in cui è ospitata l'appliance. Vengono estratte tre cartelle.
2. Aprire la console di gestione di Hyper-V. In **Azioni** fare clic su **Importa macchina virtuale**.
2. Nella procedura guidata Importa macchina virtuale selezionare **Prima di iniziare**, quindi fare clic su **Avanti**.
3. In **Individua cartella** specificare la cartella contenente il disco rigido virtuale estratto. Quindi fare clic su **Next**.
1. In **Selezione macchina virtuale** fare clic su **Avanti**.
2. In **Scegliere il tipo di importazione** fare clic su **Copia macchina virtuale (crea nuovo ID univoco)** . Quindi fare clic su **Next**.
3. In **Selezione destinazione** lasciare l'impostazione predefinita. Fare clic su **Avanti**.
4. In **Cartelle di archiviazione** lasciare l'impostazione predefinita. Fare clic su **Avanti**.
5. In **Choose Network (Scegli rete**) specificare il Commuter virtuale che userà l'appliance. È necessario che il commutatore abbia connettività Internet per inviare dati ad Azure.
6. In **Riepilogo** rivedere le impostazioni. Fare clic su **Fine**.
7. Nella console di gestione di Hyper-V > le **macchine virtuali**, avviare l'appliance.

### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che l'appliance possa connettersi agli URL di Azure per i cloud [pubblici](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).

### <a name="4-configure-the-appliance"></a>4. configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [script PowerShell](deploy-appliance-script.md) invece del disco rigido virtuale scaricato, i primi due passaggi di questa procedura non sono pertinenti.

1. Nella console di gestione di Hyper-V > **macchine virtuali** fare clic con il pulsante destro del mouse sull'appliance > **Connetti**.
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in un computer in grado di connettersi all'appliance, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop dell'appliance facendo clic sul relativo collegamento.
1. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
1. Nell'app Web selezionare **Set up prerequisites** (Configura i prerequisiti) ed eseguire le operazioni seguenti:
    - **Connettività**: l'app verifica che l'appliance abbia accesso a Internet. Se l'appliance usa un proxy:
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
4. Se l'account utente di Azure usato per la registrazione ha le autorizzazioni corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
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

Connettersi dal dispositivo a host o cluster Hyper-V e avviare l'individuazione del server.

1. In **passaggio 1: fornire le credenziali dell'host Hyper-v** fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **nome utente** e **password** per un host o cluster Hyper-v che l'appliance utilizzerà per individuare i server. Fare clic su **Save**.
1. Per aggiungere più credenziali contemporaneamente e salvarle, fare clic su **Aggiungi altre**. Sono supportate più credenziali per l'individuazione di server nell'ambiente Hyper-V.
1. In **Passaggio 2: Specificare i dettagli per host/cluster Hyper-V** fare clic su **Aggiungi origine di individuazione** per specificare **Indirizzo IP/FQDN** dell'host/cluster Hyper-V e il nome descrittivo per le credenziali per la connessione all'host/cluster.
1. È possibile selezionare **Add single item** (Aggiungi singolo elemento) o **Add multiple items** (Aggiungi più elementi). È disponibile anche un'opzione per fornire i dettagli dell'host/cluster Hyper-V tramite **Importa CSV**.

    - Se si sceglie **Add single item** (Aggiungi singolo elemento) è necessario specificare un nome descrittivo per le credenziali e **Indirizzo IP/FQDN** dell'host/cluster Hyper-V, quindi fare clic su **Salva**.
    - Se si sceglie **Aggiungi più elementi** _(selezionato per impostazione predefinita)_, è possibile aggiungere più record contemporaneamente specificando l' **indirizzo IP/FQDN** del cluster o dell'host Hyper-V con il nome descrittivo per le credenziali nella casella di testo. **Verificare** i record aggiunti e fare clic su **Salva**.
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
2. Nella pagina **Azure migrate-server**  >  **Azure migrate: individuazione e valutazione** fare clic sull'icona che Visualizza il conteggio per i **server individuati**.

## <a name="next-steps"></a>Passaggi successivi

- [Valutare i server nell'ambiente Hyper-V](tutorial-assess-hyper-v.md) per la migrazione alle macchine virtuali di Azure.
- [Esaminare i dati](migrate-appliance.md#collected-data---hyper-v) raccolti dall'appliance durante l'individuazione.


