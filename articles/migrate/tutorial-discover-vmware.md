---
title: Individuare i server in esecuzione nell'ambiente VMware con Azure Migrate individuazione e valutazione
description: Informazioni su come individuare i server locali in esecuzione nell'ambiente VMware con lo strumento di individuazione e valutazione Azure Migrate
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: 2e9dafc111478f35263dc9db0cc07d913327c936
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612059"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-discovery-and-assessment"></a>Esercitazione: individuare i server in esecuzione nell'ambiente VMware con Azure Migrate: individuazione e valutazione

Il percorso di migrazione ad Azure prevede l'individuazione dei carichi di lavoro e dell'inventario locale.

Questa esercitazione illustra come individuare i server in esecuzione nell'ambiente VMware con Azure Migrate: strumento di individuazione e valutazione, usando un'appliance di Azure Migrate Lightweight. Si distribuisce l'appliance come server in esecuzione nel server vCenter, per individuare continuamente i server e i relativi metadati delle prestazioni, le applicazioni in esecuzione su server, le dipendenze del server e SQL Server istanze e database.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un account Azure.
> * Preparare l'ambiente VMware per l'individuazione.
> * Creare un progetto.
> * Configurare l'appliance di Azure Migrate.
> * Avviare l'individuazione continua.

> [!NOTE]
> Le esercitazioni illustrano il percorso più rapido per provare uno scenario e, laddove possibile, prevedono l'uso delle opzioni predefinite.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, verificare che siano rispettati i prerequisiti seguenti.

**Requisito** | **Dettagli**
--- | ---
**Server vCenter/Host ESXi** | È necessario un server vCenter che esegue la versione 5.5, 6.0, 6.5 o 6.7.<br/><br/> I server devono essere ospitati in un host ESXi che esegue la versione 5,5 o successiva.<br/><br/> Nella server vCenter consentire le connessioni in ingresso sulla porta TCP 443, in modo che l'appliance possa raccogliere i metadati di configurazione e delle prestazioni.<br/><br/> L'appliance si connette a server vCenter sulla porta 443 per impostazione predefinita. Se il server vCenter è in ascolto su una porta diversa, è possibile modificare la porta quando si forniscono i dettagli server vCenter in Gestione configurazione Appliance.<br/><br/> Negli host ESXi verificare che l'accesso in ingresso sia consentito sulla porta TCP 443 per eseguire l'individuazione delle applicazioni installate e l'analisi delle dipendenze senza agenti nei server.
**Appliance** | server vCenter necessita di risorse per allocare un server per l'appliance Azure Migrate:<br/><br/> -32 GB di RAM, 8 vCPU e circa 80 GB di spazio di archiviazione su disco.<br/><br/> -Un Commuter virtuale esterno e accesso a Internet nel server appliance, direttamente o tramite un proxy.
**Server** | Tutte le versioni del sistema operativo Windows e Linux sono supportate per l'individuazione dei metadati di configurazione e delle prestazioni. <br/><br/> Per eseguire l'individuazione delle applicazioni sui server, sono supportate tutte le versioni del sistema operativo Windows e Linux. Vedere [qui](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) per le versioni del sistema operativo supportate per l'analisi delle dipendenze senza agenti.<br/><br/> Per eseguire l'individuazione delle applicazioni installate e l'analisi delle dipendenze senza agenti, è necessario che gli strumenti VMware (successivi a 10.2.0) siano installati e in esecuzione nei server. Per i server Windows deve essere installata la versione 2,0 o successiva di PowerShell.<br/><br/> Per individuare le istanze e i database di SQL Server, fare clic [qui](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) per le versioni ed edizioni supportate di SQL Server, le versioni del sistema operativo Windows supportate e i meccanismi di autenticazione.

## <a name="prepare-an-azure-user-account"></a>Preparare un account utente Azure

Per creare un progetto e registrare il dispositivo di Azure Migrate, è necessario un account con:

- Autorizzazioni di collaboratore o proprietario per la sottoscrizione di Azure
- Autorizzazioni per la registrazione di app Azure Active Directory (AAD)
- Proprietario o collaboratore più autorizzazioni di amministratore di accesso utente per la sottoscrizione di Azure per creare un Key Vault, usato durante la migrazione del server senza agenti

Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione. Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare le autorizzazioni nel modo seguente:

1. Nel portale di Azure cercare "sottoscrizioni" e in **Servizi** selezionare **Sottoscrizioni**.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Casella di ricerca per cercare la sottoscrizione di Azure":::


2. Nella pagina **sottoscrizioni** selezionare la sottoscrizione in cui si desidera creare un progetto.
3. Nella sottoscrizione selezionare **Controllo di accesso (IAM)**  > **Verifica l'accesso**.
4. In **Verifica l'accesso** cercare l'account utente pertinente.
5. In **Aggiungi un'assegnazione di ruolo** fare clic su **Aggiungi**.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Cercare un account utente per verificare l'accesso e assegnare un ruolo":::
    
6. In **Aggiungi un'assegnazione di ruolo** selezionare il ruolo Collaboratore o Proprietario e selezionare l'account (azmigrateuser nell'esempio). Fare quindi clic su **Salva**.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Viene aperta la pagina Aggiungi assegnazione di ruolo per assegnare un ruolo all'account":::

1. Per registrare l'appliance, l'account Azure deve avere le **autorizzazioni per registrare le app di AAD.**
1. In portale di Azure passare a **Azure Active Directory**  >    >  **impostazioni utente** utenti.
1. In **Impostazioni utente** verificare che gli utenti di Azure AD possano registrare le applicazione (impostato su **Sì** per impostazione predefinita).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Verificare che in Impostazioni utente che gli utenti possano registrare le app Active Directory":::

9. Nel caso in cui le impostazioni di ' Registrazioni app ' siano impostate su' No ', richiedere all'amministratore globale o tenant di assegnare l'autorizzazione necessaria. In alternativa, l'amministratore globale o tenant può assegnare il ruolo **sviluppatore applicazione** a un account per consentire la registrazione dell'app AAD. [Altre informazioni](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-vmware"></a>Preparare VMware

Nel server vCenter controllare che l'account abbia le autorizzazioni per creare una macchina virtuale mediante un file OVA. Queste autorizzazioni sono necessarie quando l'appliance di Azure Migrate viene distribuita come macchina virtuale VMware tramite un file OVA.

Azure Migrate necessita di un account server vCenter di sola lettura per l'individuazione e la valutazione dei server in esecuzione nell'ambiente VMware. Per eseguire l'individuazione delle applicazioni installate e l'analisi delle dipendenze senza agenti, per l'account devono essere abilitati i privilegi per le **macchine virtuali > operazioni Guest**.

### <a name="create-an-account-to-access-vcenter"></a>Creare un account per l'accesso a vCenter

In vSphere Web Client configurare un account come descritto di seguito:

1. Usando un account con privilegi di amministratore, in vSphere Web Client > selezionare **Administration** (Amministrazione).
2. Selezionare **Access** (Accesso) e quindi **SSO Users and Groups** (Utenti e gruppi SSO).
3. In **Users** (Users) aggiungere un nuovo utente.
4. In **New User** (Nuovo utente) digitare i dettagli dell'account. Fare quindi clic su **OK**.
5. In **Global Permissions** (Autorizzazioni globali) selezionare l'account utente e assegnare all'account il ruolo **Read-only** (Sola lettura). Fare quindi clic su **OK**.
6. Se si vuole anche eseguire l'individuazione delle applicazioni installate e dell'analisi delle dipendenze senza agente, passare a **ruoli** > selezionare il ruolo di sola **lettura** e in **privilegi** selezionare **operazioni Guest**. È possibile propagare i privilegi a tutti gli oggetti sotto la server vCenter selezionando la casella di controllo "propagazione ai figli".

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Casella di controllo per consentire le operazioni guest sul ruolo Read-only":::

> [!NOTE]
> È possibile limitare l'individuazione a specifici data center server vCenter, cluster, una cartella di cluster, host, una cartella di host o singoli server per ambito dell'account di server vCenter. [**Altre informazioni**](set-discovery-scope.md) su come definire l'ambito dell'account utente server vCenter.

### <a name="create-an-account-to-access-servers"></a>Creare un account per accedere ai server

È necessario disporre di un account utente con i privilegi necessari sui server per eseguire l'individuazione delle applicazioni installate, l'analisi delle dipendenze senza agenti e l'individuazione di SQL Server istanze e database. È possibile specificare l'account utente in Gestione configurazione Appliance. Il dispositivo non installa agenti nei server.

1. Per i server Windows, creare un account (locale o di dominio) con autorizzazioni amministrative sui server. Per individuare SQL Server istanze e i database, è necessario che l'account Windows o SQL Server sia un membro del ruolo del server sysadmin. [Altre informazioni](/sql/relational-databases/security/authentication-access/server-level-roles) su come assegnare il ruolo necessario all'account utente.
2. Per i server Linux, creare un account con privilegi radice. In alternativa, è possibile creare un account con queste autorizzazioni per i file/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.

> [!NOTE]
> È ora possibile aggiungere più credenziali del server in Gestione configurazione per eseguire l'individuazione delle applicazioni installate, l'analisi delle dipendenze senza agenti e l'individuazione di SQL Server istanze e database. È possibile aggiungere più credenziali di autenticazione di dominio/Windows (non di dominio)/Linux (non di dominio) e/o SQL Server. [**Scopri di più**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Configurare un progetto

Configurare un nuovo progetto.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.
3. In **Panoramica** selezionare **Crea progetto**.
5. In **Crea progetto** selezionare il gruppo di risorse e la sottoscrizione di Azure. Creare un gruppo di risorse, se non è presente.
6. In **Dettagli del progetto** specificare il nome del progetto e l'area geografica in cui lo si vuole creare. Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Caselle per il nome del progetto e l'area":::

7. Selezionare **Crea**.
8. Attendere alcuni minuti per la distribuzione del progetto. Lo strumento **Azure migrate: individuazione e valutazione** viene aggiunto per impostazione predefinita al nuovo progetto.

> [!NOTE]
> Se è già stato creato un progetto, è possibile usare lo stesso progetto per registrare appliance aggiuntive per individuare e valutare più no. di server. [ **Altre informazioni**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Configurare l'appliance

Azure Migrate: individuazione e valutazione usano un appliance Azure Migrate leggero. L'Appliance esegue l'individuazione del server e invia i metadati di configurazione e prestazioni del server a Azure Migrate. È possibile configurare l'appliance distribuendo un modello OVA che può essere scaricato dal progetto.

> [!NOTE]
> Se per qualche motivo non è possibile configurare l'appliance usando il modello, è possibile configurarlo usando uno script di PowerShell in un server Windows Server 2016 esistente. [**Scopri di più**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>Distribuire con OVA

Per configurare l'appliance con un modello OVA:

1. Specificare un nome per il dispositivo e generare una chiave del progetto nel portale.
1. Scaricare un file modello OVA e importarlo nel server vCenter. Verificare che gli OVA siano protetti.
1. Creare l'appliance dal file OVA e verificare che sia in grado di connettersi a Azure Migrate.
1. Configurare il dispositivo per la prima volta e registrarlo con il progetto usando la chiave del progetto.

### <a name="1-generate-the-project-key"></a>1. generare la chiave del progetto

1. Negli **obiettivi di migrazione**  >  **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, selezionare **individua**.
2. In **individua server**  >  **i server sono virtualizzati?** selezionare **Sì, con VMware vSphere Hypervisor**.
3. In **1: genera chiave progetto** specificare un nome per il dispositivo Azure migrate che si configurerà per l'individuazione dei server nell'ambiente VMware. Il nome deve essere costituito da un massimo di 14 caratteri alfanumerici.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina di individuazione durante la creazione delle risorse.
1. Una volta completata la creazione delle risorse di Azure, viene generata una **chiave del progetto** .
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="2-download-the-ova-template"></a>2. scaricare il modello OVA

In **2: Scaricare l'appliance di Azure Migrate** selezionare il file OVA e fare clic su **Scarica**.

### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file OVA, verificarne la sicurezza:

1. Aprire una finestra di comando amministratore nel server in cui è stato scaricato il file.
2. Eseguire il comando seguente per generare l'hash per il file OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Verificare le versioni più recenti dell'appliance e i valori hash:

    - Per il cloud pubblico di Azure:
    
        **Algoritmo** | **Scaricare** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Per Azure per enti pubblici:
    
        **Algoritmo** | **Scaricare** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-server"></a>3. creare il server appliance

Importare il file scaricato e creare un server in ambiente VMware

1. Nella console di vSphere Client fare clic su **File** > **Deploy OVF Template** (Distribuisci modello OVF).
2. Nella procedura guidata Distribuire il modello OVF > **Origine** specificare il percorso del file con estensione ova.
3. In **nome** e **percorso** specificare un nome descrittivo per il server. Selezionare l'oggetto inventario in cui verrà ospitato il server.
5. In **host/cluster** specificare l'host o il cluster in cui viene eseguito il server.
6. In **archiviazione** specificare la destinazione di archiviazione per il server.
7. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
8. In **mapping di rete** specificare la rete a cui si connetterà il server. La rete richiede la connettività Internet per inviare i metadati a Azure Migrate.
9. Rivedere e confermare le impostazioni e quindi fare clic su **Finish** (Fine).

### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Verificare che il server del dispositivo sia in grado di connettersi agli URL di Azure per i cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi](migrate-appliance.md#government-cloud-urls) .

### <a name="4-configure-the-appliance"></a>4. configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [**script di PowerShell**](deploy-appliance-script.md) invece degli OVA scaricati, i primi due passaggi di questa procedura non sono rilevanti.

1. Nella console del client di vSphere, fare clic con il pulsante destro del mouse sul server e quindi scegliere **Apri console**.
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in qualsiasi computer in grado di connettersi al dispositivo e aprire l'URL di gestione configurazione Appliance: `https://appliance name or IP address: 44368` .

   In alternativa, è possibile aprire Gestione configurazione dal desktop del server appliance selezionando il collegamento per Gestione configurazione.
1. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
1. In Configuration Manager > **configurare i prerequisiti**, procedere come segue:
   - **Connettività**: il dispositivo verifica che il server disponga di accesso a Internet. Se il server usa un proxy:
     - Fare clic sul **proxy di installazione** per specificare l'indirizzo proxy o la porta di `http://ProxyIPAddress` `http://ProxyFQDN` attesa.
     - Se il proxy richiede l'autenticazione, specificare le credenziali.
     - È supportato solo il proxy HTTP.
     - Se sono stati aggiunti dettagli del proxy o sono stati disabilitati il proxy e/o l'autenticazione, fare clic su **Save** (Salva) per attivare di nuovo il controllo della connettività.
   - **Time sync** (Sincronizzazione ora): Per il corretto funzionamento dell'individuazione delle macchine virtuali, l'ora dell'appliance deve essere sincronizzata con l'ora Internet.
   - **Install updates** (Installa aggiornamenti): l'appliance verifica che siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **Visualizza servizi Appliance** per visualizzare lo stato e le versioni dei servizi in esecuzione nel server appliance.
   - **Install VDDK** (Installa VDDK): l'appliance verifica che VMware vSphere Virtual Disk Development Kit (VDDK) sia installato. Se non è già installato, scaricare VDDK 6.7 da VMware ed estrarre il contenuto dello ZIP scaricato nel percorso specificato nell'appliance, come indicato nelle **istruzioni di installazione**.

     Azure Migrate migrazione del server USA VDDK per replicare i server durante la migrazione ad Azure. 
1. Se lo si desidera, è possibile **rieseguire i prerequisiti** in qualsiasi momento durante la configurazione dell'appliance per verificare se l'appliance soddisfa tutti i prerequisiti.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Pannello 1 in Gestione configurazione Appliance":::

### <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave del progetto** copiata dal portale. Se non si dispone della chiave, passare a **Azure migrate: individuazione e valutazione> individua> Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Per eseguire l'autenticazione con Azure, è necessario un codice del dispositivo. Facendo clic su **Accedi** si aprirà una finestra modale con il codice del dispositivo, come illustrato di seguito.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Finestra modale con il codice del dispositivo":::

1. Fare clic su **Copy code & Login** (Copia il codice e accedi) per copiare il codice del dispositivo e aprire una richiesta di accesso ad Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella scheda nuovo incollare il codice del dispositivo e accedere usando il nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Se si chiude la scheda per errore senza eseguire l'accesso, è necessario aggiornare la scheda del browser di gestione configurazione dell'appliance per abilitare di nuovo il pulsante di accesso.
1. Dopo aver eseguito l'accesso, tornare nella scheda precedente di gestione configurazione dell'appliance.
1. Se l'account utente di Azure usato per la registrazione ha le autorizzazioni corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Al termine della registrazione dell'appliance è possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Pannello 2 in Gestione configurazione Appliance":::

## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

### <a name="provide-vcenter-server-details"></a>Fornire dettagli server vCenter

L'appliance deve connettersi a server vCenter per individuare i dati relativi alla configurazione e alle prestazioni dei server.

1. In **passaggio 1: fornire le credenziali server vCenter** fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **nome utente** e **password** per l'account server vCenter che l'appliance userà per individuare i server in esecuzione nel server vCenter.
    - È necessario configurare un account con le autorizzazioni necessarie, come descritto in questo articolo.
    - Per definire l'ambito dell'individuazione di oggetti VMware specifici (server vCenter Data Center, cluster, una cartella di cluster, host, una cartella di host o singoli server), vedere le istruzioni in [questo articolo](set-discovery-scope.md) per limitare l'account usato da Azure migrate.
1. In **passaggio 2:** specificare i dettagli della server vCenter fare clic su **Aggiungi origine individuazione** per selezionare il nome descrittivo per le credenziali dall'elenco a discesa, specificare l' **indirizzo IP/FQDN** del server vCenter. È possibile lasciare l'impostazione predefinita per **Porta** (443) o specificare una porta personalizzata sulla quale sarà in ascolto il server vCenter e fare clic su **Salva**.
1. Quando si fa clic su **Salva**, il dispositivo tenterà di convalidare la connessione al server vCenter con le credenziali specificate e indicherà lo **stato di convalida** nella tabella rispetto al server vCenter indirizzo IP/FQDN.
1. È possibile **rivalidare** la connettività a server vCenter in qualsiasi momento prima di avviare l'individuazione.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Pannello 3 in Gestione configurazione Appliance per server vCenter dettagli":::

### <a name="provide-server-credentials"></a>Fornire le credenziali del server

In **passaggio 3: fornire le credenziali del server per eseguire l'inventario software, l'analisi delle dipendenze senza agenti e l'individuazione di istanze e database di SQL Server**, è possibile scegliere di fornire più credenziali del server o se non si desidera utilizzare queste funzionalità, è possibile scegliere di ignorare il passaggio e procedere con server vCenter individuazione. È possibile modificare l'intento in qualsiasi momento in un secondo momento.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Pannello 3 in Gestione configurazione Appliance per i dettagli del server":::

Se si desidera utilizzare queste funzionalità, è possibile specificare le credenziali del server attenendosi alla procedura descritta di seguito. Il dispositivo tenterà di eseguire automaticamente il mapping delle credenziali ai server per eseguire le funzionalità di individuazione.

- È possibile aggiungere le credenziali del server facendo clic sul pulsante **Aggiungi credenziali** . Verrà aperto un modale in cui è possibile scegliere il **tipo di credenziali** dall'elenco a discesa.
- È possibile specificare credenziali di autenticazione di dominio/Windows (non di dominio)/Linux (non di dominio)/SQL Server. [Altre](add-server-credentials.md) informazioni su come fornire le credenziali e su come gestirle.
- Per ogni tipo di credenziali, è necessario specificare un nome descrittivo per le credenziali, aggiungere il nome **utente** e la **password** e fare clic su **Salva**.
- Se si scelgono le credenziali del dominio, è necessario specificare anche il nome di dominio completo per il dominio. Il nome di dominio completo è necessario per convalidare l'autenticità delle credenziali con la Active Directory di tale dominio.
- Esaminare le [autorizzazioni necessarie](add-server-credentials.md#required-permissions) per l'account per l'individuazione delle applicazioni installate, l'analisi delle dipendenze senza agenti o l'individuazione di SQL Server istanze e database.
- Per aggiungere più credenziali contemporaneamente e salvarle, fare clic su **Aggiungi altre**.
- Quando si fa clic su **Salva** o **Aggiungi altro**, l'appliance convalida le credenziali del dominio con la Active Directory del dominio per la relativa autenticità. Questa operazione viene eseguita per evitare i blocchi degli account quando l'Appliance esegue più iterazioni per eseguire il mapping delle credenziali ai rispettivi server.
- È possibile visualizzare lo **stato di convalida** di tutte le credenziali del dominio nella tabella Credentials. Verranno convalidate solo le credenziali di dominio.
- Se la convalida ha esito negativo, è possibile fare clic su stato **non riuscito** per visualizzare l'errore rilevato e fare clic su riconvalida **credenziali** dopo la correzione del problema per convalidare di nuovo le credenziali di dominio non riuscite.

     :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Pannello 3 in Gestione configurazione Appliance per fornire più credenziali":::

### <a name="start-discovery"></a>Avviare l'individuazione

1. Fare clic su **Avvia individuazione per avviare** l'individuazione server vCenter. Dopo che l'individuazione è stata avviata correttamente, è possibile controllare lo stato di individuazione in base all'indirizzo IP/FQDN server vCenter nella tabella sources.
1. Se sono state specificate le credenziali del server, l'inventario software (individuazione delle applicazioni installate) verrà avviato automaticamente dopo il completamento dell'individuazione del server vCenter. L'inventario software viene eseguito una volta ogni 12 ore.
1. [Inventario software](how-to-discover-applications.md) identifica le istanze SQL Server in esecuzione nei server e usando le informazioni, il dispositivo tenta di connettersi alle istanze tramite l'autenticazione di Windows o le credenziali di autenticazione SQL Server fornite nel dispositivo e raccoglie i dati nei database SQL Server e le relative proprietà. L'individuazione SQL viene eseguita ogni 24 ore.
1. Durante l'inventario software, le credenziali dei server aggiunti verranno iterate sui server e convalidate per l'analisi delle dipendenze senza agenti. È possibile abilitare l'analisi delle dipendenze senza agenti per i server dal portale. È possibile selezionare solo i server in cui è stata eseguita la convalida per abilitare l'analisi delle dipendenze senza agenti.

> [!Note]
>Azure Migrate crittografa la comunicazione tra le istanze Azure Migrate Appliance e SQL Server di origine (con la proprietà Crittografa connessione impostata su TRUE). Queste connessioni vengono crittografate con [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (impostato su true); il livello trasporto userà SSL per crittografare il canale e ignorare la catena di certificati per convalidare l'attendibilità. Il server appliance deve essere configurato in modo da [**considerare attendibile l'autorità radice del certificato**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Se al momento dell'avvio non è stato eseguito il provisioning di alcun certificato sul server, SQL Server genera un certificato autofirmato che viene utilizzato per crittografare i pacchetti di accesso. [**Scopri di più**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

L'individuazione funziona come segue:

- Sono necessari circa 15 minuti per visualizzare l'inventario dei server individuati nel portale.
- L'individuazione delle applicazioni installate può richiedere del tempo. La durata dipende dal numero di server individuati. Per i server 500, la visualizzazione dell'inventario individuato nel portale di Azure Migrate richiede circa un'ora.
- Al termine dell'individuazione dei server, è possibile abilitare l'analisi delle dipendenze senza agenti nei server dal portale.
- SQL Server le istanze e i dati dei database inizieranno a essere visualizzati nel portale entro 24 ore dall'avvio dell'individuazione.

## <a name="next-steps"></a>Passaggi successivi

- [Valutare i server](./tutorial-assess-vmware-azure-vm.md) per la migrazione alle macchine virtuali di Azure.
- [Valutare i server SQL](./tutorial-assess-sql.md) per la migrazione ad Azure SQL.
- [Esaminare i dati](migrate-appliance.md#collected-data---vmware) raccolti dall'appliance durante l'individuazione.
