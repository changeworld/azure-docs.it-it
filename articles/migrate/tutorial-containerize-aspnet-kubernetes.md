---
title: ASP.NET di contenitori di app Azure; Contenitori e migrazione di applicazioni ASP.NET in Azure Kubernetes.
description: 'Esercitazione: distribuire & eseguire la migrazione di applicazioni ASP.NET al servizio Azure Kubernetes.'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: 7c9b4032346e61eb3bfd21c0c4067e2364bc28af
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466576"
---
# <a name="containerize-aspnet-applications-and-migrate-to-azure-kubernetes-service"></a>Distribuire ASP.NET Applications e migrate to Azure Kubernetes Service

In questo articolo si apprenderà come distribuire le applicazioni ASP.NET ed eseguirne la migrazione al [servizio Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/) usando lo strumento di Azure migrate: app container. Il processo di contenitori non richiede l'accesso alla codebase e fornisce un modo semplice per distribuire le applicazioni esistenti. Lo strumento funziona utilizzando lo stato di esecuzione delle applicazioni in un server per determinare i componenti dell'applicazione e consente di creare un pacchetto in un'immagine del contenitore. L'applicazione in contenitori può quindi essere distribuita in Azure Kubernetes Service (AKS).

Il Azure Migrate: lo strumento di contenitori di app supporta attualmente

- Inserimento ASP.NET app e distribuirle in contenitori Windows in AKS.
- Inserimento le app Web Java su Apache Tomcat (su server Linux) e distribuirle in contenitori Linux in AKS. [Altre informazioni](./tutorial-containerize-java-kubernetes.md)


Il Azure Migrate: lo strumento di contenitori di app consente di

- **Individuare l'applicazione**: lo strumento si connette in remoto ai server applicazioni che eseguono l'applicazione ASP.NET e individua i componenti dell'applicazione. Lo strumento crea un Dockerfile che può essere usato per creare un'immagine del contenitore per l'applicazione.
- **Compilare l'immagine del contenitore**: è possibile controllare e personalizzare ulteriormente il Dockerfile in base ai requisiti dell'applicazione e usarlo per compilare l'immagine del contenitore dell'applicazione. Viene eseguito il push dell'immagine del contenitore dell'applicazione in un Container Registry di Azure specificato.
- **Eseguire la distribuzione nel servizio Azure Kubernetes**: lo strumento genera quindi i file YAML della definizione di risorsa Kubernetes necessari per distribuire l'applicazione in contenitori nel cluster del servizio Kubernetes di Azure. È possibile personalizzare i file YAML e usarli per distribuire l'applicazione in AKS.

> [!NOTE]
> Lo strumento di Azure Migrate: app Container consente di individuare specifici tipi di applicazioni (ASP.NET e app Web Java su Apache Tomcat) e i relativi componenti in un server applicazioni. Per individuare i server e l'inventario di app, ruoli e funzionalità in esecuzione nei computer locali, usare Azure Migrate: funzionalità di individuazione e valutazione. [Altre informazioni](./tutorial-discover-vmware.md)

Sebbene tutte le applicazioni non possano trarre vantaggio da un semplice passaggio ai contenitori senza una riprogettazione significativa, alcuni dei vantaggi dello spostamento delle app esistenti nei contenitori senza riscrivere includono:

- **Maggiore utilizzo dell'infrastruttura:** Con i contenitori, più applicazioni possono condividere le risorse ed essere ospitate nella stessa infrastruttura. Ciò consente di consolidare l'infrastruttura e migliorare l'utilizzo.
- **Gestione semplificata:** Ospitando le applicazioni in una piattaforma di infrastruttura gestita moderna come AKS, è possibile semplificare le procedure di gestione mantenendo al tempo stesso il controllo sull'infrastruttura. Per ottenere questo risultato, è possibile ritirare o ridurre i processi di gestione e manutenzione dell'infrastruttura eseguiti tradizionalmente con l'infrastruttura di proprietà.
- **Portabilità delle applicazioni:** Con una maggiore adozione e standardizzazione dei formati di specifica del contenitore e delle piattaforme di orchestrazione, la portabilità delle applicazioni non è più un problema.
- **Adottare la gestione moderna con DevOps:** Consente di adottare e standardizzare le procedure moderne per la gestione e la sicurezza con l'infrastruttura come codice e la transizione a DevOps.


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare un account Azure.
> * Installare lo strumento di Azure Migrate: app container.
> * Individuare l'applicazione ASP.NET.
> * Compilare l'immagine del contenitore.
> * Distribuire l'applicazione in contenitori in AKS.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

**Requisito** | **Dettagli**
--- | ---
**Identificare un computer per installare lo strumento** | Un computer Windows per installare ed eseguire lo strumento di Azure Migrate: app container. Il computer Windows potrebbe essere un sistema operativo server (Windows Server 2016 o versione successiva) o client (Windows 10), ovvero lo strumento può essere eseguito anche sul desktop. <br/><br/> Il computer Windows che esegue lo strumento deve disporre della connettività di rete per i server o le macchine virtuali che ospitano le applicazioni ASP.NET da includere nel contenitore.<br/><br/> Verificare che nel computer Windows in cui è in esecuzione lo strumento di Azure Migrate: app container per archiviare gli elementi dell'applicazione siano disponibili 6 GB di spazio. <br/><br/> Il computer Windows deve disporre di accesso a Internet, direttamente o tramite un proxy. <br/> <br/>Installare lo strumento Microsoft Distribuzione Web nel computer che esegue lo strumento di supporto per la gestione dei contenitori delle app e il server applicazioni, se non è già installato. È possibile scaricare lo strumento da [qui](https://aka.ms/webdeploy3.6)
**Server applicazioni** | Abilitare la comunicazione remota di PowerShell nei server applicazioni: accedere al server applicazioni e seguire [queste](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting) istruzioni per attivare la comunicazione remota di PowerShell. <br/><br/> Se il server applicazioni esegue Windows Server 2008 R2, assicurarsi che PowerShell 5,1 sia installato nel server applicazioni. Seguire le istruzioni riportate [qui](https://docs.microsoft.com/powershell/scripting/windows-powershell/wmf/setup/install-configure) per scaricare e installare PowerShell 5,1 nel server applicazioni. <br/><br/> Installare lo strumento Microsoft Distribuzione Web nel computer che esegue lo strumento di supporto per la gestione dei contenitori delle app e il server applicazioni, se non è già installato. È possibile scaricare lo strumento da [qui](https://aka.ms/webdeploy3.6)
**Applicazione ASP.NET** | Lo strumento attualmente supporta <br/><br/> -ASP.NET applicazioni che usano Microsoft .NET Framework 3,5 o versione successiva.<br/> -Server applicazioni che eseguono Windows Server 2008 R2 o versione successiva (i server applicazioni devono eseguire PowerShell versione 5,1). <br/> -Applicazioni in esecuzione in Internet Information Services (IIS) 7,5 o versione successiva. <br/><br/> Lo strumento non supporta attualmente <br/><br/> -Applicazioni che richiedono l'autenticazione di Windows (AKS non supporta attualmente gMSA). <br/> -Applicazioni che dipendono da altri servizi di Windows ospitati all'esterno di IIS.


## <a name="prepare-an-azure-user-account"></a>Preparare un account utente Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

Una volta configurata la sottoscrizione, è necessario un account utente di Azure con:
- Autorizzazioni proprietario per la sottoscrizione di Azure
- Autorizzazioni per la registrazione di app Azure Active Directory

Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione. Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare le autorizzazioni nel modo seguente:

1. Nel portale di Azure cercare "sottoscrizioni" e in **Servizi** selezionare **Sottoscrizioni**.

    ![Casella di ricerca per cercare la sottoscrizione di Azure.](./media/tutorial-discover-vmware/search-subscription.png)

2. Nella pagina **Sottoscrizioni** selezionare la sottoscrizione in cui creare un progetto di Azure Migrate.
3. Nella sottoscrizione selezionare **Controllo di accesso (IAM)**  > **Verifica l'accesso**.
4. In **Verifica l'accesso** cercare l'account utente pertinente.
5. In **Aggiungi un'assegnazione di ruolo** fare clic su **Aggiungi**.

    ![Cercare un account utente per controllare l'accesso e assegnare un ruolo.](./media/tutorial-discover-vmware/azure-account-access.png)

6. In **Aggiungi assegnazione ruolo** selezionare il ruolo proprietario e selezionare l'account (azmigrateuser nell'esempio). Fare quindi clic su **Salva**.

    ![Apre la pagina Aggiungi assegnazione ruolo per assegnare un ruolo all'account.](./media/tutorial-discover-vmware/assign-role.png)

7. L'account Azure richiede anche le **autorizzazioni per registrare Azure Active Directory app.**
8. In portale di Azure passare a **Azure Active Directory**  >    >  **impostazioni utente** utenti.
9. In **Impostazioni utente** verificare che gli utenti di Azure AD possano registrare le applicazione (impostato su **Sì** per impostazione predefinita).

      ![Verificare nelle impostazioni utente che gli utenti possono registrare Active Directory app.](./media/tutorial-discover-vmware/register-apps.png)

10. Nel caso in cui le impostazioni di ' Registrazioni app ' siano impostate su' No ', richiedere all'amministratore globale o tenant di assegnare l'autorizzazione necessaria. In alternativa, l'amministratore globale o tenant può assegnare il ruolo **sviluppatore applicazione** a un account per consentire la registrazione di Azure Active Directory app. [Altre informazioni](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Scaricare e installare Azure Migrate: strumento di contenitori di app

1. [Scaricare](https://go.microsoft.com/fwlink/?linkid=2134571) il Azure migrate: programma di installazione di contenitori di app in un computer Windows.
2. Avviare PowerShell in modalità amministratore e modificare la directory di PowerShell nella cartella che contiene il programma di installazione.
3. Eseguire lo script di installazione usando il comando

   ```powershell
   .\App ContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Avviare lo strumento di contenitori di app

1. Aprire un browser in qualsiasi computer in grado di connettersi al computer Windows che esegue lo strumento di contenitori di app e aprire l'URL dello strumento: **https://*nome computer o indirizzo IP*: 44368**.

   In alternativa, è possibile aprire l'app dal desktop selezionando il collegamento all'app.

2. Se viene visualizzato un avviso che informa che la connessione non è privata, fare clic su Avanzate e scegliere di continuare con il sito Web. Questo avviso viene visualizzato quando l'interfaccia web usa un certificato TLS/SSL autofirmato.
3. Nella schermata di accesso usare l'account amministratore locale nel computer per accedere.
4. Per specificare il tipo di applicazione selezionare **ASP.NET Web App** come tipo di applicazione che si vuole distribuire.

    ![Caricamento predefinito per lo strumento di contenitori di app.](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Prerequisiti completi dello strumento
1. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
6. Nell'app Web dello strumento > **configurare i prerequisiti**, seguire questa procedura:
   - **Connettività**: lo strumento verifica che il computer Windows disponga di accesso a Internet. Se il computer usa un proxy:
     - Fare clic su **Configura proxy** per specificare l'indirizzo proxy (nel formato indirizzo IP o FQDN) e la porta di ascolto.
     - Se il proxy richiede l'autenticazione, specificare le credenziali.
     - È supportato solo il proxy HTTP.
     - Se sono stati aggiunti dettagli del proxy o è stato disabilitato il proxy e/o l'autenticazione, fare clic su **Salva** per attivare nuovamente il controllo della connettività.
   - **Installare gli aggiornamenti**: lo strumento verificherà automaticamente la disponibilità di aggiornamenti più recenti e li installerà. È anche possibile installare manualmente la versione più recente dello strumento da [qui](https://go.microsoft.com/fwlink/?linkid=2134571).
   - **Installare microsoft distribuzione Web Tool**: lo strumento verificherà che lo strumento Microsoft distribuzione Web sia installato nel computer Windows che esegue lo strumento di Azure migrate: app container.
   - **Abilitare la comunicazione remota di PowerShell**: lo strumento informa che è necessario assicurarsi che la comunicazione remota di PowerShell sia abilitata nei server applicazioni che eseguono le applicazioni ASP.NET per il contenitore.


## <a name="log-in-to-azure"></a>Accedere ad Azure

Fare clic su **Accedi** per accedere al proprio account Azure.

1. È necessario un codice del dispositivo per l'autenticazione con Azure. Quando si fa clic su login, viene aperto un modale con il codice del dispositivo.
2. Fare clic su **Copy code & Login** (Copia il codice e accedi) per copiare il codice del dispositivo e aprire una richiesta di accesso ad Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.

    ![Modale che mostra il codice del dispositivo.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Nella scheda nuovo incollare il codice del dispositivo e completare l'accesso usando le credenziali dell'account Azure. È possibile chiudere la scheda del browser dopo aver completato l'accesso e tornare all'interfaccia Web dello strumento di contenitori di applicazioni.
4. Selezionare il **tenant di Azure** che si vuole usare.
5. Specificare la **sottoscrizione di Azure** che si vuole usare.

## <a name="discover-aspnet-applications"></a>Scopri le applicazioni ASP.NET

Lo strumento di supporto per la gestione dei contenitori di app si connette in remoto ai server applicazioni usando le credenziali specificate e tenta di individuare le applicazioni ASP.NET ospitate nei server applicazioni.

1. Specificare l' **indirizzo IP/FQDN e le credenziali** del server che esegue l'applicazione ASP.NET da usare per connettersi in remoto al server per l'individuazione delle applicazioni.
    - Le credenziali specificate devono essere per un amministratore locale (Windows) nel server applicazioni.
    - Per gli account di dominio (l'utente deve essere un amministratore nel server applicazioni), anteporre al nome utente il nome di dominio nel formato *<dominio\nomeutente>*.
    - È possibile eseguire l'individuazione delle applicazioni per fino a cinque server alla volta.

2. Fare clic su **convalida** per verificare che il server applicazioni sia raggiungibile dal computer che esegue lo strumento e che le credenziali siano valide. Al completamento della convalida, nella colonna stato viene visualizzato lo stato **mappato**.  

    ![Screenshot per l'indirizzo IP e le credenziali del server.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. Fare clic su **continua** per avviare l'individuazione dell'applicazione nei server applicazioni selezionati.

4. Al termine dell'individuazione dell'applicazione, è possibile selezionare l'elenco di applicazioni per distribuire.

    ![Screenshot per l'applicazione ASP.NET individuata.](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. Utilizzare la casella di controllo per selezionare le applicazioni da distribuire.
5. **Specifica nome contenitore**: specificare un nome per il contenitore di destinazione per ogni applicazione selezionata. Il nome del contenitore deve essere specificato come *nome <: tag*> dove viene usato il tag per l'immagine del contenitore. Ad esempio, è possibile specificare il nome del contenitore di destinazione come *AppName: V1*.   

### <a name="parameterize-application-configurations"></a>Parametrizzare le configurazioni dell'applicazione
Parametrizzazione la configurazione lo rende disponibile come parametro della fase di distribuzione. In questo modo è possibile configurare questa impostazione mentre si distribuisce l'applicazione anziché impostarla come hardcoded su un valore specifico nell'immagine del contenitore. Questa opzione, ad esempio, è utile per parametri come le stringhe di connessione del database.
1. Fare clic su **configurazioni dell'app** per esaminare le configurazioni rilevate.
2. Selezionare la casella di controllo per parametrizzare le configurazioni dell'applicazione rilevate.
3. Fare clic su **applica** dopo aver selezionato le configurazioni da parametrizzare.

   ![Screenshot per la parametrizzazione della configurazione dell'app ASP.NET.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Dipendenze file system Externalize

 È possibile aggiungere altre cartelle utilizzate dall'applicazione. Specificare se devono far parte dell'immagine del contenitore o devono essere esternalizzate tramite volumi permanenti nella condivisione file di Azure. L'uso di volumi permanenti funziona perfettamente per le applicazioni con stato che archiviano lo stato all'esterno del contenitore o che contengono altro contenuto statico archiviato nel file system. [Altre informazioni](https://docs.microsoft.com/azure/aks/concepts-storage)

1. Fare clic su **modifica** in cartelle app per esaminare le cartelle dell'applicazione rilevate. Le cartelle dell'applicazione rilevate sono state identificate come elementi obbligatori necessari per l'applicazione e verranno copiati nell'immagine del contenitore.

2. Fare clic su **Aggiungi cartelle** e specificare i percorsi della cartella da aggiungere.
3. Per aggiungere più cartelle allo stesso volume, specificare valori separati da virgole ( `,` ).
4. Selezionare **volume permanente** come opzione di archiviazione se si desidera che le cartelle vengano archiviate al di fuori del contenitore in un volume permanente.
5. Fare clic su **Salva** dopo aver esaminato le cartelle dell'applicazione.
   ![Screenshot della selezione dell'archiviazione dei volumi delle app.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Fare clic su **continua** per passare alla fase di compilazione dell'immagine del contenitore.

## <a name="build-container-image"></a>Compilare l'immagine del contenitore


1. **Selezionare container Registry di Azure**: usare l'elenco a discesa per selezionare una [container Registry di Azure](https://docs.microsoft.com/azure/container-registry/) che verrà usata per compilare e archiviare le immagini del contenitore per le app. È possibile usare un Container Registry di Azure esistente o scegliere di crearne uno nuovo usando l'opzione Crea nuovo registro di sistema.

    ![Screenshot per la selezione di app ACR.](./media/tutorial-containerize-apps-aks/build-aspnet-app.png)


2. **Esaminare il Dockerfile**: il Dockerfile necessario per compilare le immagini del contenitore per ogni applicazione selezionata viene generato all'inizio dell'istruzione di compilazione. Fare clic su **Verifica** per esaminare Dockerfile. È anche possibile aggiungere le personalizzazioni necessarie a Dockerfile nel passaggio di revisione e salvare le modifiche prima di avviare il processo di compilazione.

3. **Trigger processo di compilazione**: selezionare le applicazioni per cui compilare le immagini e fare clic su **Compila**. Facendo clic su Compila viene avviata la compilazione dell'immagine del contenitore per ogni applicazione. Lo strumento continua a monitorare lo stato di compilazione in modo continuo e consente di procedere al passaggio successivo al completamento della compilazione.

4. **Tenere traccia dello stato della compilazione**: è inoltre possibile monitorare lo stato di avanzamento dell'istruzione di compilazione facendo clic sul collegamento **Compila in corso** nella colonna stato. Il collegamento richiede un paio di minuti per essere attivo dopo l'attivazione del processo di compilazione.  

5. Al termine della compilazione, fare clic su **continua** per specificare le impostazioni di distribuzione.

    ![Screenshot per il completamento della compilazione dell'immagine del contenitore di app.](./media/tutorial-containerize-apps-aks/build-aspnet-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>Distribuire l'app in contenitori in AKS

Una volta compilata l'immagine del contenitore, il passaggio successivo consiste nel distribuire l'applicazione come contenitore in [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/).

1. **Selezionare il cluster Azure Kubernetes Service**: specificare il cluster AKS in cui deve essere distribuita l'applicazione.

     - Il cluster AKS selezionato deve avere un pool di nodi Windows.
     - Il cluster deve essere configurato in modo da consentire il pull di immagini dal Container Registry di Azure selezionato per archiviare le immagini.
         - Eseguire il comando seguente nell'interfaccia della riga di comando di Azure per aggiungere il cluster AKS al registro contenitori di Azure.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Se non si dispone di un cluster AKS o si vuole creare un nuovo cluster AKS in cui distribuire l'applicazione, è possibile scegliere di crearlo dallo strumento facendo clic su **Crea nuovo cluster AKS**.      
          - Il cluster AKS creato con lo strumento verrà creato con un pool di nodi di Windows. Il cluster verrà configurato in modo da consentirgli di eseguire il pull delle immagini dal Container Registry di Azure creato in precedenza, se è stata scelta l'opzione Crea nuovo registro di sistema.
     - Fare clic su **continua** dopo aver selezionato il cluster AKS.

2. **Specificare la condivisione file di Azure**: se sono state aggiunte altre cartelle ed è stata selezionata l'opzione volume persistente, specificare la condivisione file di Azure che deve essere usata da Azure migrate: strumento di contenitori di app durante il processo di distribuzione. Lo strumento creerà nuove directory nella condivisione file di Azure per copiare le cartelle dell'applicazione configurate per l'archiviazione del volume permanente. Al termine della distribuzione dell'applicazione, lo strumento eseguirà la pulizia della condivisione file di Azure eliminando le directory che aveva creato.

     - Se non si ha una condivisione file di Azure o si vuole creare una nuova condivisione file di Azure, è possibile scegliere di crearla dallo strumento facendo clic su **Crea nuovo account di archiviazione e condivisione file**.  

3. **Configurazione della distribuzione dell'applicazione**: dopo aver completato i passaggi precedenti, è necessario specificare la configurazione di distribuzione per l'applicazione. Fare clic su **Configura** per personalizzare la distribuzione per l'applicazione. Nel passaggio di configurazione è possibile fornire le seguenti personalizzazioni:
     - **Stringa di prefisso**: specificare una stringa di prefisso da usare nel nome per tutte le risorse create per l'applicazione in contenitori nel cluster AKS.
     - **Certificato SSL**: se l'applicazione richiede un'associazione sito HTTPS, specificare il file PFX contenente il certificato da usare per l'associazione. Il file PFX non deve essere protetto da password e il sito originale non deve avere più associazioni.
     - **Set di repliche**: specificare il numero di istanze dell'applicazione (POD) che devono essere eseguite all'interno dei contenitori.
     - **Tipo** di servizio di bilanciamento del carico: selezionare *esterno* se l'applicazione in contenitori deve essere raggiungibile dalle reti pubbliche.
     - **Configurazione dell'applicazione**: per tutte le configurazioni dell'applicazione con parametri, fornire i valori da usare per la distribuzione corrente.
     - **Archiviazione**: per qualsiasi cartella dell'applicazione configurata per l'archiviazione del volume permanente, specificare se il volume deve essere condiviso tra le istanze dell'applicazione o deve essere inizializzato singolarmente con ogni istanza nel contenitore. Per impostazione predefinita, tutte le cartelle dell'applicazione nei volumi permanenti vengono configurate come condivise.  
     - Fare clic su **applica** per salvare la configurazione della distribuzione.
     - Fare clic su **continua** per distribuire l'applicazione.

    ![Screenshot per la configurazione dell'app di distribuzione.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-config.png)

4. **Distribuire l'applicazione**: dopo aver salvato la configurazione di distribuzione per l'applicazione, lo strumento genererà la distribuzione Kubernetes YAML per l'applicazione.
     - Fare clic su **modifica** per rivedere e personalizzare la distribuzione di Kubernetes YAML per le applicazioni.
     - Selezionare l'applicazione da distribuire.
     - Fare clic su **Distribuisci** per avviare le distribuzioni per le applicazioni selezionate

         ![Screenshot per la configurazione della distribuzione dell'app.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-deploy.png)

     - Una volta distribuita l'applicazione, è possibile fare clic sulla colonna *stato distribuzione* per tenere traccia delle risorse distribuite per l'applicazione.

## <a name="download-generated-artifacts"></a>Scarica elementi generati

Tutti gli elementi usati per compilare e distribuire l'applicazione in AKS, inclusi i file di specifica YAML Dockerfile e Kubernetes, vengono archiviati nel computer che esegue lo strumento. Gli elementi si trovano in *C:\ProgramData\Microsoft Azure migrate contenitori di app*.

Viene creata una singola cartella per ogni server applicazioni. È possibile visualizzare e scaricare tutti gli elementi intermedi usati nel processo di contenitori passando a questa cartella. La cartella corrispondente al server applicazioni verrà pulita all'inizio di ogni esecuzione dello strumento per un determinato server.

## <a name="troubleshoot-issues"></a>Risolvere i problemi

Per risolvere eventuali problemi con lo strumento, è possibile esaminare i file di log nel computer Windows che esegue lo strumento di contenitori di app. I file di log degli strumenti si trovano in *C:\ProgramData\Microsoft Azure migrate cartella Containerization\Logs dell'app* .

## <a name="next-steps"></a>Passaggi successivi

- Inserimento le app Web Java su Apache Tomcat (su server Linux) e distribuirle in contenitori Linux in AKS. [Altre informazioni](./tutorial-containerize-java-kubernetes.md)
