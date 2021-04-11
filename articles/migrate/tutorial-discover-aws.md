---
title: Individuare le istanze di AWS con Azure Migrate individuazione e valutazione
description: Informazioni su come individuare le istanze di AWS con Azure Migrate individuazione e valutazione.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/11/2021
ms.custom: mvc
ms.openlocfilehash: 295cd5a6831cb64d146bb92bca74b82ff7ab29df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771482"
---
# <a name="tutorial-discover-aws-instances-with-azure-migrate-discovery-and-assessment"></a>Esercitazione: individuare le istanze di AWS con Azure Migrate: individuazione e valutazione

Il percorso di migrazione ad Azure prevede l'individuazione dei server per la valutazione e la migrazione.

Questa esercitazione illustra come individuare le istanze di Amazon Web Services (AWS) con lo strumento Azure Migrate: Discovery and Assessment, usando un appliance Azure Migrate leggero. L'appliance viene distribuita come server fisico, per individuare in modo continuativo i metadati relativi alle macchine virtuali e alle prestazioni.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un account Azure.
> * Preparare le istanze di AWS per l'individuazione.
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
**Appliance** | È necessario una macchina virtuale EC2 in cui eseguire l'appliance di Azure Migrate. La macchina deve soddisfare i requisiti seguenti:<br/><br/> - Windows Server 2016 installato.<br/> _L'esecuzione dell'appliance in un computer con Windows Server 2019 non è supportata_.<br/><br/> - 16 GB di RAM, otto CPU virtuali, circa 80 GB di spazio di archiviazione su disco e un commutatore virtuale esterno.<br/><br/> - Un indirizzo IP statico o dinamico con accesso a Internet, direttamente o tramite un proxy.
**Istanze di Windows** | Consentire le connessioni in ingresso sulla porta WinRM 5985 (HTTP), in modo che l'appliance possa recuperare i metadati relativi a configurazione e prestazioni.
**Istanze di Linux** | Consentire le connessioni in ingresso sulla porta 22 (TCP).<br/><br/> Le istanze dovranno usare `bash` come shell predefinita, altrimenti l'individuazione non riesce.

## <a name="prepare-an-azure-user-account"></a>Preparare un account utente Azure

Per creare un progetto e registrare il dispositivo di Azure Migrate, è necessario un account con:

* Autorizzazioni di collaboratore o proprietario per una sottoscrizione di Azure.
* Autorizzazioni per la registrazione di app Azure Active Directory (AAD).

Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione. Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare le autorizzazioni nel modo seguente:

1. Nel portale di Azure cercare "sottoscrizioni" e in **Servizi** selezionare **Sottoscrizioni**.

    ![Casella di ricerca per cercare la sottoscrizione di Azure](./media/tutorial-discover-aws/search-subscription.png)

2. Nella pagina **sottoscrizioni** selezionare la sottoscrizione in cui si desidera creare un progetto.
3. Nella sottoscrizione selezionare **Controllo di accesso (IAM)**  > **Verifica l'accesso**.
4. In **Verifica l'accesso** cercare l'account utente pertinente.
5. In **Aggiungi un'assegnazione di ruolo** fare clic su **Aggiungi**.

    ![Cercare un account utente per verificare l'accesso e assegnare un ruolo](./media/tutorial-discover-aws/azure-account-access.png)

6. In **Aggiungi un'assegnazione di ruolo** selezionare il ruolo Collaboratore o Proprietario e selezionare l'account (azmigrateuser nell'esempio). Fare quindi clic su **Salva**.

    ![Viene aperta la pagina Aggiungi assegnazione di ruolo per assegnare un ruolo all'account](./media/tutorial-discover-aws/assign-role.png)

1. Per registrare l'appliance, l'account Azure deve avere le **autorizzazioni per registrare le app di AAD.**
1. In portale di Azure passare a **Azure Active Directory**  >    >  **impostazioni utente** utenti.
1. In **Impostazioni utente** verificare che gli utenti di Azure AD possano registrare le applicazione (impostato su **Sì** per impostazione predefinita).

    ![Verificare che in Impostazioni utente che gli utenti possano registrare le app Active Directory](./media/tutorial-discover-aws/register-apps.png)

1. Nel caso in cui le impostazioni di ' Registrazioni app ' siano impostate su' No ', richiedere all'amministratore globale o tenant di assegnare l'autorizzazione necessaria. In alternativa, l'amministratore globale o tenant può assegnare il ruolo **sviluppatore applicazione** a un account per consentire la registrazione dell'app AAD. [Altre informazioni](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-aws-instances"></a>Preparare le istanze di AWS

Configurare un account utilizzabile dall'appliance per accedere alle istanze di AWS.

- Per i **server Windows**, configurare un account utente locale in tutti i server Windows che si desidera includere nell'individuazione. Aggiungere l'account utente ai gruppi seguenti: - Remote Management Users - Performance Monitor Users - Performance Log Users.
 - Per i **server Linux** è necessario un account radice nei server Linux che si desidera individuare. Per un'alternativa, fare riferimento alle istruzioni disponibili nella [matrice di supporto](migrate-support-matrix-physical.md#physical-server-requirements) .
- Azure Migrate usa l'autenticazione della password durante l'individuazione delle istanze di AWS. Le istanze di AWS non supportano l'autenticazione della password per impostazione predefinita. Prima di poter individuare l'istanza, è necessario abilitare l'autenticazione della password.
    - Per i server Windows, consentire la porta WinRM 5985 (HTTP). per permettere le chiamate WMI remote.
    - Per server Linux:
        1. Accedere a ogni computer Linux.
        2. Aprire il file sshd_config: vi /etc/ssh/sshd_config
        3. Nel file individuare la riga **PasswordAuthentication** e impostare il valore su **yes**.
        4. Salvare il file e chiuderlo. Riavviare il servizio ssh.
    - Se si usa un utente root per individuare i server Linux, assicurarsi che l'accesso alla radice sia consentito nei server.
        1. Accedere a ogni computer Linux
        2. Aprire il file sshd_config: vi /etc/ssh/sshd_config
        3. Nel file individuare la riga **PermitRootLogin** e impostare il valore su **yes**.
        4. Salvare il file e chiuderlo. Riavviare il servizio ssh.

## <a name="set-up-a-project"></a>Configurare un progetto

Configurare un nuovo progetto.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Panoramica** selezionare **Crea progetto**.
5. In **Crea progetto** selezionare il gruppo di risorse e la sottoscrizione di Azure. Creare un gruppo di risorse, se non è presente.
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Caselle per il nome del progetto e l'area](./media/tutorial-discover-aws/new-project.png)

7. Selezionare **Crea**.
8. Attendere alcuni minuti per la distribuzione del progetto. Lo strumento **Azure migrate: individuazione e valutazione** viene aggiunto per impostazione predefinita al nuovo progetto.

![Pagina che mostra lo strumento Valutazione server aggiunto per impostazione predefinita](./media/tutorial-discover-aws/added-tool.png)

> [!NOTE]
> Se è già stato creato un progetto, è possibile usare lo stesso progetto per registrare appliance aggiuntive per individuare e valutare un numero maggiore di server. [Altre informazioni](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurare l'appliance

Il Azure Migrate Appliance è un'appliance semplice, usata da Azure Migrate: individuazione e valutazione per eseguire le operazioni seguenti:

- Individuare i server locali.
- Inviare i metadati e i dati sulle prestazioni per i server individuati Azure Migrate: individuazione e valutazione.

[Altre informazioni](migrate-appliance.md) sull'appliance di Azure Migrate.

Per configurare l'appliance occorre:

1. Specificare un nome per il dispositivo e generare una chiave del progetto nel portale.
1. Scaricare un file compresso con lo script del programma di installazione di Azure Migrate dal portale di Azure.
1. Estrarre il contenuto del file compresso. Avviare la console PowerShell con privilegi amministrativi.
1. Eseguire lo script di PowerShell per avviare l'applicazione Web dell'appliance.
1. Configurare il dispositivo per la prima volta e registrarlo con il progetto usando la chiave del progetto.

### <a name="1-generate-the-project-key"></a>1. generare la chiave del progetto

1. Negli **obiettivi di migrazione**  >  **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, selezionare **individua**.
2. In **individua server**  >  **i server sono virtualizzati?** selezionare **fisico o altro (AWS, GCP, Xen e così via)**.
3. In **1: genera chiave progetto** specificare un nome per il dispositivo Azure migrate che si configurerà per l'individuazione di server fisici o virtuali. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina Individua server durante la creazione delle risorse.
1. Una volta completata la creazione delle risorse di Azure, viene generata una **chiave del progetto** .
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="2-download-the-installer-script"></a>2. scaricare lo script del programma di installazione

In **2: Scaricare l'appliance di Azure Migrate** fare clic su **Scarica**.

### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel computer in cui è stato scaricato il file aprire una finestra di comando con privilegi di amministratore.
2. Eseguire il comando seguente per generare l'hash per il file compresso:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio di utilizzo per il cloud pubblico: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Esempio di utilizzo per il cloud per enti pubblici: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Verificare le versioni più recenti dell'appliance e i valori hash:
    - Per il cloud pubblico:

        **Scenario** | **Scaricare** _ | _ *Valore hash**
        --- | --- | ---
        Fisico (85 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - Per Azure per enti pubblici:

        **Scenario** | **Scaricare** _ | _ *Valore hash**
        --- | --- | ---
        Fisico (85 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. eseguire lo script del programma di installazione Azure Migrate
Lo script del programma di installazione esegue le operazioni seguenti:

- Installa gli agenti e un'applicazione Web per l'individuazione e la valutazione dei server fisici.
- Installa i ruoli di Windows, tra cui il Servizio Attivazione Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo di IIS riscrivibile. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM) con i dettagli delle impostazioni permanenti per Azure Migrate.
- Crea i seguenti file nei relativi percorsi:
    - **File di configurazione**: %Programdata%\Microsoft Azure\Config
    - **File di log**: %Programdata%\Microsoft Azure\Logs

Eseguire lo script nel modo seguente:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance.  Assicurarsi di non eseguire lo script in un computer in un'appliance di Azure Migrate esistente.
2. Avviare PowerShell nello stesso server con privilegi amministrativi (elevati).
3. Modificare la directory di PowerShell nella cartella in cui è stato estratto il contenuto del file compresso scaricato.
4. Eseguire lo script denominato **AzureMigrateInstaller.ps1** eseguendo il comando seguente:

    - Per il cloud pubblico: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Per Azure per enti pubblici: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Lo script avvierà l'applicazione Web dell'appliance al termine dell'operazione.

In caso di problemi, è possibile accedere ai log degli script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log per la risoluzione dei problemi.

### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Assicurarsi che l'appliance possa connettersi agli URL di Azure per i cloud [pubblici](migrate-appliance.md#public-cloud-urls) e per [enti pubblici](migrate-appliance.md#government-cloud-urls).

### <a name="4-configure-the-appliance"></a>4. configurare l'appliance

Configurare l'appliance per la prima volta.

1. Aprire un browser in un computer in grado di connettersi all'appliance, quindi aprire l'URL dell'app Web dell'appliance: **https://*nome o indirizzo IP dell'appliance*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop facendo clic sul relativo collegamento.
2. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
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
4. Se l'account utente di Azure usato per la registrazione ha le [autorizzazioni](./tutorial-discover-physical.md) corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Al termine della registrazione dell'appliance è possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.


## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

A questo punto, connettersi dall'appliance ai server fisici da individuare e avviare l'individuazione.

1. In **Passaggio 1: Fornire le credenziali per l'individuazione di server fisici o virtuali Windows e Linux**, fare clic su **Aggiungi credenziali**.
1. Per Windows Server, selezionare il tipo di origine come **Windows Server**, specificare un nome descrittivo per le credenziali, aggiungere il nome utente e la password. Fare clic su **Save**.
1. Se si usa l'autenticazione basata su password per il server Linux, selezionare il tipo di origine come **server Linux (basato su password)**, specificare un nome descrittivo per le credenziali, aggiungere il nome utente e la password. Fare clic su **Save**.
1. Se si usa l'autenticazione basata su chiavi SSH per il server Linux, è possibile selezionare il tipo di origine come **server Linux (basato su chiave SSH)**, specificare un nome descrittivo per le credenziali, aggiungere il nome utente, sfogliare e selezionare il file di chiave privata SSH. Fare clic su **Save**.

    * Azure Migrate supporta la chiave privata SSH generata dal comando ssh-keygen usando gli algoritmi RSA, DSA, ECDSA e ed25519.
    * Attualmente Azure Migrate non supporta la chiave SSH basata su passphrase. Usare una chiave SSH senza passphrase.
    * Attualmente Azure Migrate non supporta il file di chiave privata SSH generato da PuTTy.
    * Azure Migrate supporta il formato OpenSSH del file di chiave privata SSH, come illustrato di seguito:
    
    ![Formato supportato della chiave privata SSH](./media/tutorial-discover-physical/key-format.png)


1. Per aggiungere più credenziali contemporaneamente e salvarle, fare clic su **Aggiungi altre**. Sono supportate più credenziali per l'individuazione di server fisici.
1. In **Passaggio 2: Specificare i dettagli del server fisico o virtuale** fare clic su **Aggiungi origine di individuazione** per specificare **Indirizzo IP/FQDN** del server e il nome descrittivo per le credenziali per la connessione al server.
1. È possibile selezionare **Add single item** (Aggiungi singolo elemento) o **Add multiple items** (Aggiungi più elementi). È disponibile anche un'opzione per fornire i dettagli del server tramite **Importa CSV**.


    - Se si sceglie **Aggiungi elemento singolo**, è possibile scegliere il tipo di sistema operativo, specificare un nome descrittivo per le credenziali, aggiungere un valore per **Indirizzo IP/FQDN** del server e fare clic su **Salva**.
    - Se si sceglie **Aggiungi più elementi**, è possibile aggiungere più record contemporaneamente, specificando l' **indirizzo IP del server/FQDN** con il nome descrittivo per le credenziali nella casella di testo. Verificare * * i record aggiunti e fare clic su **Salva**.
    - Se si sceglie **Importa CSV** _(selezionato per impostazione predefinita)_ , è possibile scaricare un file modello CSV, quindi compilare il file con **Indirizzo IP/FQDN** del server e il nome descrittivo per le credenziali. Importare quindi il file nell'appliance, **verificare** i record nel file e fare clic su **Salva**.

1. Quando si fa clic su Salva, l'appliance tenterà di convalidare la connessione ai server aggiunti e visualizzerà lo **Stato di convalida** nella tabella per ogni server.
    - Se la convalida non riesce per un server, esaminare l'errore facendo clic su **Convalida non riuscita** nella colonna Stato della tabella. Risolvere il problema e ripetere la convalida.
    - Per rimuovere un server, fare clic su **Elimina**.
1. È possibile **rivalidare** la connettività ai server in qualsiasi momento prima di avviare l'individuazione.
1. Fare clic su **Avvia individuazione** per avviare l'individuazione dei server convalidati correttamente. Dopo l'avvio dell'individuazione, è possibile controllare lo stato dell'individuazione per ogni server nella tabella.


Viene avviata l'individuazione. Per la visualizzazione dei metadati del server individuato nel portale di Azure sono necessari circa 2 minuti per ogni server.

## <a name="verify-servers-in-the-portal"></a>Verificare i server nel portale

Dopo l'individuazione, è possibile verificare se i server vengono visualizzati nel portale.

1. Aprire il dashboard di Azure Migrate.
2. In **Azure migrate-Windows, Linux e SQL Server**  >  **Azure migrate: pagina individuazione e valutazione** , fare clic sull'icona che Visualizza il conteggio per i **server individuati**.

## <a name="next-steps"></a>Passaggi successivi

- [Valutare i server fisici](tutorial-migrate-aws-virtual-machines.md) per la migrazione alle macchine virtuali di Azure.
- [Esaminare i dati](migrate-appliance.md#collected-data---physical) raccolti dall'appliance durante l'individuazione.
