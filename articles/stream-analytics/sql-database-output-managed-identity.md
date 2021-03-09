---
title: Usare le identità gestite per accedere al database SQL di Azure o ad Azure sinapsi Analytics-analisi di flusso di Azure
description: Questo articolo descrive come usare le identità gestite per autenticare il processo di analisi di flusso di Azure nel database SQL di Azure o nell'output di Azure sinapsi Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: e491c421f4af256b2e74fa61eb442d269bdb9e34
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487917"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Usare le identità gestite per accedere al database SQL di Azure o a Azure sinapsi Analytics da un processo di analisi di flusso di Azure (anteprima)

Analisi di flusso di Azure supporta [l'autenticazione dell'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per il database SQL di Azure e i sink di output di Azure sinapsi Analytics. Le identità gestite eliminano le limitazioni associate ai metodi di autenticazione basata sull'utente, ad esempio la necessità di ripetere l'autenticazione a causa di modifiche della password o di scadenze dei token utente ogni 90 giorni. Quando si elimina la necessità di eseguire l'autenticazione manualmente, le distribuzioni di Analisi di flusso possono essere completamente automatizzate.

Un'identità gestita è un'applicazione gestita registrata in Azure Active Directory che rappresenta un determinato processo di Analisi di flusso. L'applicazione gestita viene usata per l'autenticazione in una risorsa di destinazione. Questo articolo illustra come abilitare l'identità gestita per un database SQL di Azure o un output di analisi della sinapsi di Azure di un processo di analisi di flusso tramite il portale di Azure.

## <a name="overview"></a>Panoramica

Questo articolo illustra i passaggi necessari per connettere il processo di analisi di flusso al database SQL di Azure o al pool SQL di Azure sinapsi Analytics usando la modalità di autenticazione identità gestita. 

- Si crea prima un'identità gestita assegnata dal sistema per il processo di analisi di flusso. Si tratta dell'identità del processo in Azure Active Directory.  

- Aggiungere un amministratore Active Directory all'area di lavoro di SQL Server o sinapsi, che consente l'autenticazione Azure AD (identità gestita) per tale risorsa.

- Successivamente, creare un utente indipendente che rappresenti l'identità del processo di analisi di flusso nel database. Ogni volta che il processo di analisi di flusso interagisce con il database SQL o la risorsa del database SQL sinapsi, si tratta dell'identità a cui si riferisce per verificare le autorizzazioni del processo di analisi di flusso.

- Concedere le autorizzazioni al processo di analisi di flusso per accedere al database SQL o ai pool SQL sinapsi.

- Aggiungere infine il database SQL di Azure o l'analisi delle sinapsi di Azure come output nel processo di analisi di flusso.

## <a name="prerequisites"></a>Prerequisiti

#### <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/azure-sql)

Per usare questa funzionalità, è necessario quanto segue:

- Un processo di Analisi di flusso di Azure.

- Una risorsa del database SQL di Azure.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Per usare questa funzionalità, è necessario quanto segue:

- Un processo di Analisi di flusso di Azure.

- Un pool SQL di Azure sinapsi Analytics.

- Un account di archiviazione di Azure [configurato per il processo di analisi di flusso](azure-synapse-analytics-output.md).

- Nota: il file MSI di archiviazione dell'account di analisi di flusso integrato con sinapsi SQL MSI non è attualmente disponibile.

---

## <a name="create-a-managed-identity"></a>Creare un'identità gestita

Per prima cosa, si crea un'identità gestita per il processo di Analisi di flusso di Azure.

1. Nel [portale di Azure](https://portal.azure.com) aprire il processo di Analisi di flusso di Azure.

1. Nel menu di spostamento a sinistra selezionare **Identità gestita** in **Configura**. Selezionare quindi la casella accanto a **Usa identità gestita assegnata dal sistema** e selezionare **Salva**.

   ![Selezionare l'identità gestita assegnata dal sistema](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)

   In Azure Active Directory viene creata un'entità servizio per l'identità del processo di Analisi di flusso. Il ciclo di vita dell'identità creata viene gestito da Azure. Quando si elimina il processo di Analisi di flusso, l'identità associata (ovvero, l'entità servizio) viene eliminata automaticamente da Azure.

1. Quando si salva la configurazione, l'ID oggetto (OID) dell'entità servizio viene indicato come ID entità, come illustrato di seguito: 

   ![ID oggetto indicato come ID entità](./media/sql-db-output-managed-identity/principal-id.png)

   L'entità servizio ha lo stesso nome del processo di Analisi di flusso. Ad esempio, se il nome del processo è *MyASAJob*, anche il nome dell'entità servizio è *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Selezionare un amministratore di Active Directory

Dopo aver creato un'identità gestita, è necessario selezionare un amministratore di Active Directory.

1. Passare al database SQL di Azure o alla risorsa del pool SQL di Azure sinapsi Analytics e selezionare l'area di lavoro SQL Server o sinapsi in cui si trova rispettivamente la risorsa. È possibile trovare il collegamento a questi nella pagina Panoramica risorse accanto a nome *Server* o *nome area di lavoro*.

1. Selezionare **Active Directory amministratore** o **SQL Active Directory amministratore** in **Impostazioni**, per SQL Server e l'area di lavoro sinapsi rispettivamente. Selezionare quindi **Imposta amministratore**.

   ![Pagina Amministratore di Active Directory](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. Nella pagina Amministratore di Active Directory cercare un utente o un gruppo da impostare come amministratore per SQL Server e fare clic su **Seleziona**. Si tratta dell'utente che sarà in grado di creare l' **utente del database indipendente** nella sezione successiva.

   ![Aggiungere un amministratore di Active Directory](./media/sql-db-output-managed-identity/add-admin.png)

   La pagina Amministratore di Active Directory mostra tutti i membri e i gruppi di Active Directory. Non è possibile selezionare utenti o gruppi in grigio perché non sono supportati come amministratori Azure Active Directory. Vedere l'elenco degli amministratori supportati nella sezione **funzionalità e limitazioni**   del Azure Active Directory di [usare l'autenticazione Azure Active Directory per l'autenticazione con il database SQL o la sinapsi di Azure](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

1. Selezionare **Salva** nella pagina **Amministratore di Active Directory**. Il processo per la modifica dell'amministratore può durare alcuni minuti.

## <a name="create-a-contained-database-user"></a>Creazione di un utente di database indipendente

Successivamente, si crea un utente di database indipendente nel database SQL di Azure o in Azure sinapsi di cui viene eseguito il mapping all'identità del Azure Active Directory. L'utente del database indipendente non dispone di un account di accesso per il database primario, ma esegue il mapping a un'identità nella directory associata al database. L'identità di Azure Active Directory può essere un singolo account utente o un gruppo. In questo caso, si vuole creare un utente di un database indipendente per il processo di Analisi di flusso. 

Per altre informazioni, vedere l'articolo seguente per informazioni di base sull'integrazione di Azure AD: [autenticazione universale con database SQL e Azure sinapsi Analytics (supporto di SSMS per l'autenticazione a](../azure-sql/database/authentication-mfa-ssms-overview.md) più fattori)

1. Connettersi al database SQL di Azure o alle sinapsi di Azure usando SQL Server Management Studio. Il **Nome utente** è un utente Azure Active Directory con l'autorizzazione **ALTER ANY USER**. L'amministratore impostato in SQL Server è un esempio. Usare l'autenticazione **Azure Active Directory - Universale con supporto MFA**. 

   ![Connessione a SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Il nome del server `<SQL Server name>.database.windows.net` può essere differente nelle diverse aree. Ad esempio, l'area Cina deve usare `<SQL Server name>.database.chinacloudapi.cn`.
 
   È possibile specificare un database SQL di Azure o di Azure sinapsi specifico selezionando **opzioni > proprietà di connessione > Connetti al database**.  

   ![Proprietà di connessione SQL Server](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Quando ci si connette per la prima volta, è possibile che venga visualizzata la finestra seguente:

   ![Finestra Nuova regola del firewall](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. In tal caso, passare alla risorsa dell'area di lavoro SQL Server/sinapsi nel portale di Azure. Nella sezione **sicurezza** aprire la pagina **firewall e rete virtuale/firewall** . 
   1. Aggiungere una nuova regola con un nome qualsiasi.
   1. Usare l'indirizzo IP *Da* della finestra **Nuova regola del firewall** per *IP iniziale*.
   1. Usare l'indirizzo IP *A* della finestra **Nuova regola del firewall** per *IP finale*. 
   1. Selezionare **Salva** e provare di nuovo a connettersi da SQL Server Management Studio. 

1. Quando si è connessi, creare l'utente del database indipendente. Il comando SQL seguente crea un utente del database indipendente con lo stesso nome del processo di Analisi di flusso. Assicurarsi di includere le parentesi quadre intorno ad *ASA_JOB_NAME*. Usare la sintassi T-SQL seguente ed eseguire la query. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```
   
    Per verificare se l'utente del database indipendente è stato aggiunto correttamente, eseguire il comando seguente in SSMS nel database pertinente e verificare se il *ASA_JOB_NAME* si trova nella colonna "nome".

   ```sql
   SELECT * FROM <SQL_DB_NAME>.sys.database_principals 
   WHERE type_desc = 'EXTERNAL_USER' 
   ```

1. Affinché Azure Active Directory di Microsoft verifichi se il processo di Analisi di flusso ha accesso al database SQL, è necessario concedere ad Azure Active Directory autorizzazione per la comunicazione con il database. A tale scopo, passare alla pagina "firewall e rete virtuale"/"firewall" di nuovo portale di Azure e abilitare "Consenti ai servizi e alle risorse di Azure di accedere al server/area di lavoro".

   ![Firewall e rete virtuale](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Concedere autorizzazioni al processo di Analisi di flusso

#### <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/azure-sql)

Dopo aver creato un utente di database indipendente e avere concesso l'accesso ai servizi di Azure nel portale, come descritto nella sezione precedente, il processo di analisi di flusso dispone dell'autorizzazione dall'identità gestita per **connettersi** alla risorsa del database SQL di Azure tramite identità gestita. Si consiglia di concedere le autorizzazioni **Select** e **Insert** al processo di analisi di flusso, perché saranno necessarie in un secondo momento nel flusso di lavoro di analisi di flusso. L'autorizzazione **Select** consente al processo di testare la connessione alla tabella nel database SQL di Azure. L'autorizzazione **Insert** consente di testare le query di analisi di flusso end-to-end dopo aver configurato un input e l'output del database SQL di Azure.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Dopo aver creato un utente di database indipendente e avere concesso l'accesso ai servizi di Azure nel portale, come descritto nella sezione precedente, il processo di analisi di flusso dispone dell'autorizzazione dall'identità gestita per **connettersi** alla risorsa del database di Azure sinapsi tramite l'identità gestita. Si consiglia di concedere altre autorizzazioni per le **operazioni bulk del database** **Select**, **Insert** e amministrate al processo di analisi di flusso, perché saranno necessarie in un secondo momento nel flusso di lavoro di analisi di flusso. L'autorizzazione **Select** consente al processo di testare la connessione alla tabella nel database di sinapsi di Azure. Le autorizzazioni per **operazioni bulk** di **inserimento** e amministrazione del database consentono di testare le query di analisi di flusso end-to-end dopo aver configurato un input e l'output del database di Azure sinapsi.

Per concedere l'autorizzazione **Gestisci operazioni bulk del database** , è necessario concedere tutte le autorizzazioni etichettate come **controllo** in [implicito dall'autorizzazione database](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) per il processo di analisi di flusso. Questa autorizzazione è necessaria perché il processo di analisi di flusso esegue l'istruzione **Copy** , che richiede l' [amministrazione delle operazioni bulk del database e l'inserimento](/sql/t-sql/statements/copy-into-transact-sql).

---

È possibile concedere queste autorizzazioni al processo di Analisi di flusso usando SQL Server Management Studio. Per altre informazioni, vedere la guida di riferimento a GRANT (Transact-SQL).

Per concedere solo l'autorizzazione a una determinata tabella od oggetto nel database, utilizzare la sintassi T-SQL seguente ed eseguire la query. 

#### <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/azure-sql)

```sql
GRANT CONNECT, SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT CONNECT, SELECT, INSERT, CONTROL, ADMINISTER DATABASE BULK OPERATIONS OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

In alternativa, è possibile fare clic con il pulsante destro del mouse sul database SQL di Azure o di Azure sinapsi in SQL Server Management Studio e selezionare **proprietà > autorizzazioni**. Dal menu Autorizzazioni è possibile visualizzare il processo di Analisi di flusso aggiunto in precedenza e concedere o negare manualmente le autorizzazioni in base alle esigenze.

Per esaminare tutte le autorizzazioni aggiunte all'utente *ASA_JOB_NAME* , eseguire il comando seguente in SSMS nel database pertinente: 

```sql
SELECT dbprin.name, dbprin.type_desc, dbperm.permission_name, dbperm.state_desc, dbperm.class_desc, object_name(dbperm.major_id) 
FROM sys.database_principals dbprin 
LEFT JOIN sys.database_permissions dbperm 
ON dbperm.grantee_principal_id = dbprin.principal_id 
WHERE dbprin.name = '<ASA_JOB_NAME>' 
```

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Creare un database SQL di Azure o un output di Azure sinapsi

#### <a name="azure-sql-database"></a>[Database SQL di Azure](#tab/azure-sql)

Ora che l'identità gestita è configurata, si è pronti per aggiungere un database SQL di Azure o un output di Azure sinapsi al processo di analisi di flusso.

Assicurarsi di aver creato una tabella nel database SQL con lo schema di output appropriato. Il nome di questa tabella è una delle proprietà obbligatorie da compilare quando si aggiunge l'output del database SQL al processo di Analisi di flusso. Assicurarsi anche che il processo abbia le autorizzazioni **SELECT** e **INSERT** per testare la connessione ed eseguire query di Analisi di flusso. Vedere la sezione [Concedere autorizzazioni al processo di Analisi di flusso](#grant-stream-analytics-job-permissions) se non è già stato fatto. 

1. Tornare al processo di Analisi di flusso e passare alla pagina **Output** in **Topologia processo**. 

1. Selezionare **Aggiungi > Database SQL**. Nella finestra delle proprietà di output del sink di output del database SQL selezionare **Identità gestita** dall'elenco a discesa Modalità di autenticazione.

1. Compilare le proprietà rimanenti. Per altre informazioni sulla creazione di un output del database SQL, vedere [Creare un output del database SQL con Analisi di flusso](sql-database-output.md). Al termine dell'operazione, selezionare **Salva**.

1. Dopo aver fatto clic su **Salva**, viene attivato automaticamente un test di connessione alla risorsa. Una volta completata l'operazione, il processo di analisi di flusso è stato configurato correttamente per la connessione al database SQL di Azure o al database SQL sinapsi usando la modalità di autenticazione identità gestita. 

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Ora che l'identità gestita e l'account di archiviazione sono stati configurati, si è pronti per aggiungere un database SQL di Azure o l'output di Azure sinapsi al processo di analisi di flusso.

Assicurarsi di aver creato una tabella nel database di Azure sinapsi con lo schema di output appropriato. Il nome di questa tabella è una delle proprietà obbligatorie da compilare quando si aggiunge l'output di Azure sinapsi al processo di analisi di flusso. Assicurarsi anche che il processo abbia le autorizzazioni **SELECT** e **INSERT** per testare la connessione ed eseguire query di Analisi di flusso. Vedere la sezione [Concedere autorizzazioni al processo di Analisi di flusso](#grant-stream-analytics-job-permissions) se non è già stato fatto.

1. Tornare al processo di Analisi di flusso e passare alla pagina **Output** in **Topologia processo**.

1. Selezionare **aggiungi > Azure sinapsi Analytics**. Nella finestra delle proprietà di output del sink di output del database SQL selezionare **Identità gestita** dall'elenco a discesa Modalità di autenticazione.

1. Compilare le proprietà rimanenti. Per altre informazioni sulla creazione di un output di Azure sinapsi, vedere l' [output di analisi di sinapsi di Azure da analisi di flusso di Azure](azure-synapse-analytics-output.md). Al termine dell'operazione, selezionare **Salva**.

1. Dopo aver fatto clic su **Salva**, viene attivato automaticamente un test di connessione alla risorsa. Una volta completata l'operazione, è ora possibile procedere con l'uso dell'identità gestita per la risorsa di analisi delle sinapsi di Azure con analisi di flusso. 

---

## <a name="remove-managed-identity"></a>Rimuovi identità gestita

L'identità gestita creata per un processo di analisi di flusso viene eliminata solo quando il processo viene eliminato. Non è possibile eliminare l'identità gestita senza eliminare il processo. Se non si vuole più usare l'identità gestita, è possibile modificare il metodo di autenticazione per l'output. L'identità gestita continuerà a esistere fino a quando il processo non verrà eliminato e verrà usato se si decide di usare di nuovo l'autenticazione dell'identità gestita.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sugli output di Analisi di flusso di Azure](stream-analytics-define-outputs.md)
* [Output di Analisi di flusso di Azure nel database SQL di Azure](stream-analytics-sql-output-perf.md)
* [Output di analisi di sinapsi di Azure da analisi di flusso di Azure](azure-synapse-analytics-output.md)
