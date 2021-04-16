---
title: Eseguire il backup di Database di Azure per PostgreSQL
description: Informazioni sul backup di Database di Azure per PostgreSQL con conservazione a lungo termine (anteprima)
ms.topic: conceptual
ms.date: 04/12/2021
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 8fd69e016c7f0b175ef49b98add5692743858f62
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480070"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Backup di Database di Azure per PostgreSQL con conservazione a lungo termine (anteprima)

Backup di Azure e Servizi di database di Azure si sono uniti per creare una soluzione di backup di livello aziendale per i server di Database di Azure per PostgreSQL che mantiene i backup per un massimo di 10 anni.

Oltre alla conservazione a lungo termine, la soluzione include anche molte altre funzionalità, come elencato di seguito:

- Controllo degli accessi in base al ruolo di Azure al database usando l Azure Active Directory e l'autenticazione dell'identità del servizio gestito.
- Backup pianificato e su richiesta controllato dal cliente a livello di database singolo.
- Ripristini a livello di database in qualsiasi server Postgres o direttamente nell'archiviazione BLOB.
- Conservazione a lungo termine.
- Monitoraggio centrale di tutte le operazioni e i processi.
- I backup vengono archiviati in domini di sicurezza e di errore separati. Pertanto, anche se il server di origine fosse compromesso o addirittura stertizzato, i backup resteranno al sicuro [nell'insieme di credenziali di backup.](backup-vault-overview.md)
- **L'pg_dump** consente una maggiore flessibilità nei ripristini in modo che sia possibile eseguire il ripristino tra le versioni del database o persino ripristinare solo una parte del backup.

È possibile usare questa soluzione in modo indipendente o in aggiunta alla soluzione di backup nativa offerta da Azure PostgreSQL che offre la conservazione fino a 35 giorni. La soluzione nativa è adatta per i ripresi operativi, ad esempio quando si vuole eseguire il ripristino dai backup più recenti. La Backup di Azure soluzione consente di soddisfare le esigenze di conformità e di eseguire backup e ripristino più granulari e flessibili.

## <a name="support-matrix"></a>Matrice di supporto

|Supporto tecnico  |Dettagli  |
|---------|---------|
|Distribuzioni supportate   |  [Database di Azure per PostgreSQL - Server singolo](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Aree di Azure supportate |  Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Stati Uniti centro-occidentali, Brasile meridionale, Canada centrale, Europa settentrionale, Europa occidentale, Regno Unito meridionale, Regno Unito occidentale, Germania centro-occidentale, Svizzera settentrionale, Svizzera occidentale, Asia orientale, Asia orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, India centrale, Australia orientale, Australia centrale, Australia centrale 2, Emirati Arabi Uniti settentrionali  |
|Versioni supportate di Azure PostgreSQL    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Considerazioni e limitazioni sulle funzionalità

- Tutte le operazioni sono supportate solo dal portale di Azure.
- Il limite consigliato per le dimensioni massime del database è 400 GB.
- Il backup tra aree non è supportato. Ciò significa che non è possibile eseguire il backup di un server PostgreSQL di Azure in un insieme di credenziali in un'altra area. Analogamente, è possibile ripristinare solo un backup in un server all'interno della stessa area dell'insieme di credenziali.
- Solo i dati vengono recuperati al momento del ripristino. I "ruoli" non vengono ripristinati.
- In anteprima è consigliabile eseguire la soluzione solo nell'ambiente di test.

## <a name="backup-process"></a>Processo di backup

1. Questa soluzione usa **pg_dump** per eseguire backup dei database PostgreSQL di Azure.

2. Dopo aver specificato i database PostgreSQL di Azure di cui si vuole eseguire il backup, il servizio verifica se dispone del set di autorizzazioni e dell'accesso giusto per eseguire l'operazione di backup nel server e nel database.

3. Backup di Azure un ruolo di lavoro in cui è installata un'estensione di backup. Questa estensione comunica con il server Postgres.

4. Questa estensione è costituita da un coordinatore e da un plug-in Di Azure Postgres. Mentre il coordinatore è responsabile dell'attivazione dei flussi di lavoro per varie operazioni, ad esempio la configurazione di backup, backup e ripristino, il plug-in è responsabile del flusso di dati effettivo.
  
5. Dopo aver attivato la configurazione della protezione nei database selezionati, il servizio di backup configura il coordinatore con le pianificazioni dei backup e altri dettagli dei criteri.

6. All'ora pianificata, il coordinatore comunica con il plug-in e inizia a trasmettere i dati di backup dal server Postgres **usando pg_dump**.

7. Il plug-in invia i dati direttamente all'insieme di credenziali di Backup, eliminando la necessità di un percorso di gestione temporanea. I dati vengono crittografati usando chiavi gestite da Microsoft e archiviati dal servizio Backup di Azure negli account di archiviazione.

8. Al termine del trasferimento dei dati, il coordinatore conferma il commit con il servizio di backup.

    ![Processo di backup](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Configurare il backup nei database PostgreSQL di Azure

È possibile configurare il backup in più database in più server PostgreSQL di Azure. Assicurarsi che le [autorizzazioni prerequisiti](#prerequisite-permissions-for-configure-backup-and-restore) richieste dal servizio per eseguire il backup dei server Postgres siano già configurate.

Le istruzioni seguenti sono una guida dettagliata alla configurazione del backup nei database Di Azure PostgreSQL usando Backup di Azure:

1. Il processo può essere iniziato in due modi:

    1. Passare a [Backup Center](backup-center-overview.md)  ->  **Overview**  ->  **Backup**.

        ![Passare a Centro backup](./media/backup-azure-database-postgresql/backup-center.png)

        In **Avvia: Configura backup** selezionare Il tipo di origine **dati** come Database di Azure **per PostgreSQL**.

        ![In Avvia: Configura backup selezionare Tipo di origine dati](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. In alternativa, è possibile passare direttamente a [Backup vaults](backup-vault-overview.md)  ->  **Backup**.

        ![Passare a Insiemi di credenziali di backup](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Selezionare Backup nell'insieme di credenziali di backup](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. In **Configura backup** selezionare **l'insieme di credenziali** di backup in cui si vuole eseguire il backup dei database Postgres. Queste informazioni sono precompilato se si è già nel contesto dell'insieme di credenziali.

    ![Selezionare Backup vault (Insieme di credenziali di backup) in Configure backup (Configura backup)](./media/backup-azure-database-postgresql/configure-backup.png)

1. Selezionare o creare un **criterio di backup.**

    ![Scegliere Criteri di backup](./media/backup-azure-database-postgresql/backup-policy.png)

1. Selezionare le risorse o i database Postgres di cui eseguire il backup.

    ![Selezionare le risorse di cui eseguire il backup](./media/backup-azure-database-postgresql/select-resources.png)

1. È possibile scegliere dall'elenco di tutti i server Di Azure PostgreSQL tra sottoscrizioni, se si tratta della stessa area dell'insieme di credenziali. Espandere la freccia per visualizzare l'elenco dei database all'interno di un server.

    ![Scegliere i server](./media/backup-azure-database-postgresql/choose-servers.png)

1. Il servizio esegue questi controlli sui database selezionati per verificare se l'insieme di credenziali dispone delle autorizzazioni per eseguire il backup dei server e dei database Postgres selezionati.
    1. **Per continuare, l'idoneità** per il backup per tutti i database deve **essere completata.**
    1. Se si verifica un errore, **correggere** l'errore e **riconvalidare o** rimuovere il database dalle selezioni.

    ![Errori di convalida da correggere](./media/backup-azure-database-postgresql/validation-errors.png)

1. Confermare tutti i dettagli in **Rivedi e configura e** selezionare **Configura backup** per inviare l'operazione.

    ![Verificare i dettagli in Rivedi e configura](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Dopo l'attivazione, **l'operazione Configura backup** creerà un'istanza di backup. È possibile tenere traccia dello stato dell'operazione nel riquadro [Istanze di backup](backup-center-monitor-operate.md#backup-instances) nella visualizzazione Centro backup o insieme di credenziali.

    ![Istanze di backup](./media/backup-azure-database-postgresql/backup-instances.png)

1. I backup vengono attivati in base alla pianificazione dei backup definita nei criteri. I processi possono essere monitorati in [Processi di backup](backup-center-monitor-operate.md#backup-jobs). Attualmente, è possibile visualizzare i processi per gli ultimi sette giorni.

    ![Processi di backup](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Creare criteri di backup

1. Passare a **Backup Center** Backup  ->  **policies (Criteri di backup di Centro**  ->  **backup) Aggiungi**. In alternativa, è possibile passare a Criteri di **backup dell'insieme di credenziali** di  ->  **backup**  ->  **Aggiungi**.

    ![Aggiungere criteri di backup](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Immettere un **nome** per il nuovo criterio.

    ![Immettere il nome dei criteri](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Definire la pianificazione del backup. Attualmente è disponibile il supporto **per il** backup settimanale. È possibile pianificare i backup in uno o più giorni della settimana.

    ![Definire la pianificazione del backup](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Definire **le impostazioni di** conservazione. È possibile aggiungere una o più regole di conservazione. Ogni regola di conservazione presuppone input per backup specifici e la durata dell'archivio dati e della conservazione per tali backup.

1. È possibile scegliere di archiviare i backup in uno dei due archivi dati (o **livelli):** Archivio dati di backup (livello standard) o Archivio dati **archivio** (in anteprima).

   È possibile scegliere **In scadenza per** spostare il backup nell'archivio dati di archiviazione alla scadenza nell'archivio dati di backup.

1. La **regola di conservazione predefinita** viene applicata in assenza di qualsiasi altra regola di conservazione e ha un valore predefinito di tre mesi.

    - La durata della conservazione varia da sette giorni a 10 anni **nell'archivio dati di backup.**
    - La durata della conservazione varia da sei mesi a 10 anni **nell'archivio dati archivio**.

    ![Modificare la durata della conservazione](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Le regole di conservazione vengono valutate in un ordine di priorità predeterminato. La priorità è massima per la **regola annuale,** seguita dalla regola **mensile** e quindi **dalla regola settimanale.** Le impostazioni di conservazione predefinite vengono applicate quando non sono idonee altre regole. Ad esempio, lo stesso punto di ripristino può essere il primo backup eseguito correttamente ogni settimana e il primo backup eseguito correttamente ogni mese. Tuttavia, poiché la priorità della regola mensile è superiore a quella della regola settimanale, viene applicata la conservazione corrispondente al primo backup eseguito ogni mese.

## <a name="restore"></a>Restore

È possibile ripristinare un database in qualsiasi server PostgreSQL di Azure all'interno della stessa sottoscrizione, se il servizio dispone del set appropriato di autorizzazioni nel server di destinazione. Verificare che [le autorizzazioni necessarie](#prerequisite-permissions-for-configure-backup-and-restore) per il servizio per il backup dei server Postgres siano già configurate.

Seguire questa guida dettagliata per attivare un ripristino:

1. Esistono due modi per avviare il processo di ripristino:
    1. Passare a [Backup Center](backup-center-overview.md)Overview Restore  ->  **(Panoramica del Centro**  ->  **backup).**

    ![Selezionare Ripristina in Centro backup](./media/backup-azure-database-postgresql/backup-center-restore.png)

    In **Initiate: Restore (Avvia: Ripristina)** selezionare **Il tipo di origine dati** è Database di Azure per **PostgreSQL.** Selezionare **l'istanza di Backup**.

    ![Selezionare il tipo di origine dati in Initiate:Restore](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. In alternativa, è possibile passare direttamente a Backup vault Backup Instances **(Istanze**  ->  **di backup dell'insieme di credenziali di backup).** Selezionare **Istanza di backup** corrispondente al database da ripristinare.

    ![Istanze di backup per il ripristino](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Elenco delle istanze di backup](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Selezionare Ripristina](./media/backup-azure-database-postgresql/select-restore.png)

1. **Selezionare il punto di** ripristino dall'elenco di tutti i backup completi disponibili per l'istanza di backup selezionata. Per impostazione predefinita, è selezionato il punto di ripristino più recente.

    ![Selezionare il punto di ripristino](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Elenco dei punti di ripristino](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Input **Parametri di ripristino**. A questo punto, è possibile scegliere tra due tipi di ripristini: Ripristina **come database** e Ripristina **come file**.

1. **Ripristina come database:** ripristinare i dati di backup per creare un nuovo database nel server PostgreSQL di destinazione.

    - Il server di destinazione può essere uguale al server di origine. Tuttavia, la sovrascrittura del database originale non è supportata.
    - È possibile scegliere dal server in tutte le sottoscrizioni, ma nella stessa area dell'insieme di credenziali.
    - Selezionare **Rivedi e ripristina.** Verrà attivata la convalida per verificare se il servizio dispone delle autorizzazioni di ripristino appropriate nel server di destinazione.

    ![Eseguire il ripristino come database](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Ripristina come file:** eseguire il dump dei file di backup nell'account di archiviazione di destinazione (BLOB).

    - È possibile scegliere tra gli account di archiviazione in tutte le sottoscrizioni, ma nella stessa area dell'insieme di credenziali.
    - Selezionare il contenitore di destinazione dall'elenco dei contenitori filtrato per l'account di archiviazione selezionato.
    - Selezionare **Rivedi e ripristina**. Verrà attivata la convalida per verificare se il servizio dispone delle autorizzazioni di ripristino appropriate nel server di destinazione.

    ![Ripristinare come file](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Se il punto di ripristino si trova nel livello di archiviazione, è necessario ripristinare il punto di ripristino prima del ripristino.
   
   ![Impostazioni di riidratazione](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   Specificare i parametri aggiuntivi seguenti necessari per la riidratazione:
   - **Priorità di riidratazione:** Il valore predefinito è **Standard.**
   - **Durata della riidratazione:** La durata massima della riidratazione è di 30 giorni e la durata minima della riidratazione è di 10 giorni. Il valore predefinito è **15**.
   
   Il punto di ripristino viene archiviato nell'archivio **dati di backup per** la durata di riidratazione specificata.


1. Esaminare le informazioni e selezionare **Ripristina**. Verrà attivato un processo di ripristino corrispondente che può essere monitorato in **Processi di backup**.

>[!NOTE]
>Il supporto dell'archivio per Database di Azure per PostgreSQL è in anteprima pubblica limitata.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Autorizzazioni prerequisiti per configurare il backup e il ripristino

Backup di Azure linee guida di sicurezza rigorose. Anche se è ™ servizio nativo di Azure, le autorizzazioni per la risorsa non vengono presupposte e devono essere concesse in modo esplicito dall'utente.  Analogamente, le credenziali per la connessione al database non vengono archiviate. Questo è importante per proteggere i dati. Viene invece utilizzata l'Azure Active Directory autenticazione.

[Scaricare questo documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) per ottenere uno script automatizzato e le istruzioni correlate. Concederà un set appropriato di autorizzazioni a un server PostgreSQL di Azure per il backup e il ripristino.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Gestire i database PostgreSQL di Azure di cui è stato eseguito il backup

Queste sono le operazioni di gestione che è possibile eseguire nelle istanze **di backup:**

### <a name="on-demand-backup"></a>Backup su richiesta

Per attivare un backup non in base alla pianificazione specificata nei criteri, passare a Backup instances Backup instances Backup Now **(Backup delle istanze**  ->  **di Backup ora).**
Scegliere dall'elenco delle regole di conservazione definite nei criteri di backup associati.

![Attiva backup ora](./media/backup-azure-database-postgresql/backup-now.png)

![Scegliere dall'elenco delle regole di conservazione](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Arresta protezione

È possibile arrestare la protezione per un elemento di backup. Verranno eliminati anche i punti di ripristino associati per l'elemento di backup. Se i punti di ripristino non sono nel livello archivio per un minimo di sei mesi, l'eliminazione di tali punti di ripristino comporta un costo di eliminazione anticipata. Non è ancora disponibile l'opzione di arresto della protezione mantenendo i punti di ripristino esistenti.

![Arresta protezione](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Modificare i criteri

È possibile modificare i criteri associati con un'istanza di backup.

1. Selezionare Criteri di **modifica dell'istanza**  ->  **di backup**.

    ![Modificare i criteri](./media/backup-azure-database-postgresql/change-policy.png)

1. Selezionare i nuovi criteri da applicare al database.

    ![Riassegnare i criteri](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Questa sezione fornisce informazioni sulla risoluzione dei problemi per il backup di database PostgreSQL di Azure con Backup di Azure.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Concedere l'accesso **in** lettura all'istanza di Backup Vault MSI nel server PG di cui si vuole eseguire il backup o il ripristino.

Per stabilire una connessione sicura al database PostgreSQL, Backup di Azure usa il modello di autenticazione dell'identità del servizio gestito [(MSI).](../active-directory/managed-identities-azure-resources/overview.md) Ciò significa che l'insieme di credenziali di backup avrà accesso solo alle risorse a cui l'utente ha concesso esplicitamente l'autorizzazione.

Un file MSI di sistema viene assegnato automaticamente all'insieme di credenziali al momento della creazione. È necessario concedere a questo insieme di credenziali l'accesso ai server PostgreSQL da cui si intende eseguire il backup dei database.

Passaggi:

1. Nel server Postgres passare al riquadro Controllo di accesso **(IAM).**

    ![Riquadro Controllo di accesso](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Selezionare **Aggiungi assegnazioni di ruolo**.

    ![Aggiungi un'assegnazione di ruolo](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. Nel riquadro di destra del contesto visualizzato immettere quanto segue:<br>

   - **Ruolo:** Scegliere il **ruolo Lettore** nell'elenco a discesa.<br>
   - **Assegnare l'accesso a:** Scegliere **l'opzione Utente, gruppo o entità** servizio nell'elenco a discesa.<br>
   - **Selezionare:** Immettere il nome dell'insieme di credenziali di backup in cui si vuole eseguire il backup di questo server e dei relativi database.<br>

    ![Seleziona il ruolo](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Creare un utente di backup del database in grado di eseguire l'autenticazione con Azure Active Directory:

Questo errore può derivare da un'assenza di un amministratore Azure Active Directory per il server PostgreSQL o da un utente di backup in grado di eseguire l'autenticazione Azure Active Directory.

Passaggi:

Aggiungere un amministratore di Active Directory al server OSS:

Questo passaggio è necessario per connettersi al database tramite un utente che può eseguire l'autenticazione con Azure Active Directory anziché con una password. L'Azure AD admin in Database di Azure per PostgreSQL avrà il ruolo **azure_ad_admin**. Solo un **ruolo azure_ad_admin** può creare nuovi utenti del database che possono eseguire l'autenticazione con Azure AD.

1. Passare alla scheda Amministratore di Active Directory nel riquadro di spostamento sinistro della visualizzazione server e aggiungere se stessi (o un altro utente) come amministratore di Active Directory.

    ![Impostare l'amministratore di Active Directory](./media/backup-azure-database-postgresql/set-admin.png)

1. Assicurarsi di salvare **l'impostazione** dell'utente amministratore di AD.

    ![Salvare l'impostazione utente amministratore di Active Directory](./media/backup-azure-database-postgresql/save-admin-setting.png)

Fare riferimento [a questo documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) per l'elenco dei passaggi da eseguire per completare i passaggi di concessione delle autorizzazioni.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Stabilire la linea di vista della rete abilitando il flag Consenti **l'accesso** ai servizi di Azure nella visualizzazione server. Nella visualizzazione server, nel riquadro **Sicurezza connessione,** impostare il flag Consenti **l'accesso** ai servizi di Azure su **Sì.**

![Consentire l'accesso ai servizi di Azure](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Autorizzazione per il ripristino in un contenitore dell'account di archiviazione durante il ripristino come file

1. Concedere all'msi dell'insieme di credenziali di backup l'autorizzazione per accedere ai contenitori dell'account di archiviazione usando il portale di Azure.
    1. Passare a Controllo **di accesso**  ->  **dell'account di archiviazione**  ->  **Aggiungi assegnazione di ruolo.**
    1. Assegnare **il ruolo Collaboratore ai dati dei BLOB di** archiviazione all'msi dell'insieme di credenziali di Backup.

    ![Assegnare il ruolo Collaboratore ai dati dei BLOB di archiviazione](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. In alternativa, concedere autorizzazioni granulari al contenitore specifico in cui si esegue il ripristino usando il comando [az role assignment create](/cli/azure/role/assignment) dell'interfaccia della riga di comando di Azure.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Sostituire il parametro assignee con **l'ID** applicazione dell'identità del servizio msi dell'insieme di credenziali e il parametro scope per fare riferimento al contenitore specifico.
    1. Per ottenere **l'ID applicazione dell'identità** del servizio msi dell'insieme di credenziali, selezionare **Tutte le applicazioni** in Tipo **di applicazione:**

        ![Selezionare Tutte le applicazioni](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Cercare il nome dell'insieme di credenziali e copiare l'ID applicazione:

        ![Cercare il nome dell'insieme di credenziali](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica degli insiemi di credenziali di backup](backup-vault-overview.md)
