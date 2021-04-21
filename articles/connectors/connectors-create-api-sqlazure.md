---
title: Connettersi SQL Server, database SQL di Azure o Istanza gestita di SQL di Azure
description: Automatizzare le attività per i database SQL in locale o nel cloud usando App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
tags: connectors
ms.openlocfilehash: 2e06616914f1e78a71a540fbd64021c0e1bfcbab
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785974"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatizzare i flussi di lavoro per un database SQL usando App per la logica di Azure

Questo articolo illustra come accedere ai dati del database SQL da un'app per la logica mediante il connettore SQL Server. In questo modo, è possibile automatizzare le attività, i processi o i flussi di lavoro che gestiscono i dati e le risorse SQL tramite la creazione di app per la logica. Il SQL Server connettore funziona [per](/sql/sql-server/sql-server-technical-documentation) SQL Server, nonché per database SQL di Azure [e](../azure-sql/database/sql-database-paas-overview.md) [Istanza gestita di SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

È possibile creare app per la logica che vengono eseguite in seguito all'attivazione da eventi che si verificano nel database SQL o in altri sistemi, ad esempio Dynamics CRM Online. Le app per la logica possono anche recuperare, inserire ed eliminare dati, oltre a eseguire query SQL e stored procedure. È possibile, ad esempio, creare un'app per la logica che verifichi automaticamente la presenza di nuovi record in Dynamics CRM Online, che aggiunga elementi al database SQL per ogni nuovo record e che invii avvisi tramite posta elettronica sugli elementi aggiunti.

Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per informazioni tecniche specifiche del connettore, limitazioni e problemi noti, vedere la [pagina di riferimento del connettore SQL Server](/connectors/sql/).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [SQL Server database,](/sql/relational-databases/databases/create-a-database) [database SQL di Azure](../azure-sql/database/single-database-create-quickstart.md)o [Istanza gestita di SQL di Azure](../azure-sql/managed-instance/instance-create-quickstart.md).

  È necessario che le tabelle contengano dati in modo che l'app per la logica possa restituire un risultato quando si chiama un'operazione. Se si usa database SQL di Azure, è possibile usare i database di esempio inclusi.

* Il nome del server SQL, il nome del database, il nome utente e la password. Queste credenziali sono necessarie per autorizzare la logica ad accedere al server SQL.

  * Per i SQL Server locali, è possibile trovare questi dettagli nella stringa di connessione:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Ad database SQL di Azure, è possibile trovare questi dettagli nella stringa di connessione.
  
    Ad esempio, per trovare questa stringa nel portale di Azure, aprire il database. Nel menu del database selezionare Stringhe **di connessione** o **Proprietà**:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* A seconda che le app per la logica siano eseguite in Azure multi-tenant globale o in un ambiente del servizio di integrazione [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)di seguito sono disponibili altri requisiti per la connessione a applicazioni SQL Server:

  * Per le app per la logica in Azure multi-tenant globale che si connettono a SQL Server locale, è necessario che il [gateway](../logic-apps/logic-apps-gateway-install.md) dati locale sia installato in un computer locale e in una risorsa gateway dati già creata [in Azure.](../logic-apps/logic-apps-gateway-connection.md)

  * Per le app per la logica in un ise che si connettono al SQL Server locale e usano autenticazione di Windows, il connettore SQL Server con versione ISE non supporta autenticazione di Windows. È quindi comunque necessario usare il gateway dati e il connettore SQL Server non ISE. Per altri tipi di autenticazione, non è necessario usare il gateway dati e può usare il connettore con versione ISE.

* L'app per la logica da cui accedere al database SQL. Per avviare l'app per la logica con un trigger SQL, è necessario un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Connettersi al database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

A questo punto, continuare con questi passaggi:

* [Connettersi a applicazioni basate sul cloud database SQL di Azure o Istanza gestita](#connect-azure-sql-db)
* [Connettersi a SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Connettersi a database SQL di Azure o Istanza gestita

Per accedere a un Istanza gestita di SQL di Azure senza usare il gateway dati locale o l'ambiente del servizio di integrazione, è necessario configurare [l'endpoint](../azure-sql/managed-instance/public-endpoint-configure.md)pubblico nel Istanza gestita di SQL di Azure . L'endpoint pubblico usa la porta 3342, quindi assicurarsi di specificare questo numero di porta quando si crea la connessione dall'app per la logica.


La prima volta che si aggiunge un [trigger SQL](#add-sql-trigger) o un'azione [SQL](#add-sql-action)e non è stata creata in precedenza una connessione al database, viene richiesto di completare questi passaggi:

1. Per **Tipo di** autenticazione selezionare l'autenticazione necessaria e abilitata nel database in database SQL di Azure o Istanza gestita di SQL di Azure:

   | Authentication | Descrizione |
   |----------------|-------------|
   | [**Azure AD integrata**](../azure-sql/database/authentication-aad-overview.md) | - Supporta sia il connettore non ISE che il SQL Server ISE. <p><p>- Richiede un'identità valida in Azure Active Directory (Azure AD) che abbia accesso al database. <p>Per altre informazioni, vedere gli argomenti seguenti: <p>- [Azure SQL Panoramica della sicurezza - Autenticazione](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizzare l'accesso al database Azure SQL- Autenticazione e autorizzazione](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL - Autenticazione Azure AD integrata](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server autenticazione**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Supporta sia il connettore non ISE che il SQL Server ISE. <p><p>- Richiede un nome utente e una password complessa validi creati e archiviati nel database. <p>Per altre informazioni, vedere gli argomenti seguenti: <p>- [Azure SQL Panoramica della sicurezza - Autenticazione](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizzare l'accesso al database Azure SQL - Autenticazione e autorizzazione](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Questo esempio continua con Azure AD **integrato**:

   ![Screenshot che mostra la finestra di SQL Server", con l'elenco "Tipo di autenticazione" aperto e l'opzione "Azure AD integrata".](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Dopo aver selezionato **Azure AD Integrated (Integrata),** **selezionare Sign In (Accedi).** A seconda che si usi o database SQL di Azure o Istanza gestita di SQL di Azure, selezionare le credenziali utente per l'autenticazione.

1. Selezionare questi valori per il database:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome server** | Sì | L'indirizzo del server SQL, ad esempio `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nome database** | Sì | Nome del database SQL, ad esempio `Fabrikam-Azure-SQL-DB` |
   | **Nome tabella** | Sì | Tabella da usare, ad esempio `SalesLT.Customer` |
   ||||

   > [!TIP]
   > Per fornire le informazioni sul database e sulla tabella, sono disponibili le opzioni seguenti:
   > 
   > * Trovare queste informazioni nella stringa di connessione del database. Ad esempio, nel portale di Azure trovare e aprire il database. Nel menu del database selezionare Stringhe **di connessione** o **Proprietà**, in cui è possibile trovare questa stringa:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * Per impostazione predefinita, le tabelle nei database di sistema vengono filtrate, pertanto potrebbero non essere visualizzate automaticamente quando si seleziona un database di sistema. In alternativa, è possibile immettere manualmente il nome della tabella dopo aver selezionato **Immettere un valore personalizzato** nell'elenco del database.
   >

   Questo esempio illustra l'aspetto di questi valori:

   ![Creare una connessione al database SQL](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. A questo punto, continuare con i passaggi non ancora completati in Aggiungere un [trigger SQL](#add-sql-trigger) o [Aggiungere un'azione SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Connettersi a SQL Server

La prima volta che si aggiunge un [trigger SQL](#add-sql-trigger) o un'azione [SQL](#add-sql-action)e non è stata creata in precedenza una connessione al database, viene richiesto di completare questi passaggi:

1. Per le connessioni al server SQL locale che richiedono il gateway dati locale, assicurarsi di aver completato [questi prerequisiti.](#multi-tenant-or-ise)

   In caso contrario, la risorsa gateway dati non verrà visualizzata nell'elenco **Gateway di** connessione quando si crea la connessione.

1. Per **Tipo di** autenticazione selezionare l'autenticazione necessaria e abilitata nel SQL Server:

   | Authentication | Descrizione |
   |----------------|-------------|
   | [**Autenticazione di Windows**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | - Supporta solo il connettore SQL Server non ISE, che richiede una risorsa gateway dati creata in precedenza in Azure per la connessione, indipendentemente dal fatto che si usi Azure multi-tenant o un ISE. <p><p>- Per confermare l'identità tramite l'account di Windows, sono necessari un nome utente e una password di Windows validi. <p>Per altre informazioni, vedere Autenticazione [di Windows](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**SQL Server autenticazione**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Supporta sia il connettore non ISE che il SQL Server ISE. <p><p>- Richiede un nome utente e una password complessa validi che vengono creati e archiviati nel SQL Server. <p>Per altre informazioni, vedere Autenticazione [SQL Server.](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) |
   |||

   Questo esempio continua con **l'autenticazione di Windows:**

   ![Selezionare il tipo di autenticazione da usare](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Selezionare o specificare i valori seguenti per il database SQL:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome del server SQL** | Sì | L'indirizzo del server SQL, ad esempio `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nome database SQL** | Sì | Nome del database SQL Server, ad esempio `Fabrikam-Azure-SQL-DB` |
   | **Nome utente** | Sì | Nome utente per il server e il database SQL |
   | **Password** | Sì | Password per il server e il database SQL |
   | **Sottoscrizione** |  Sì, per autenticazione di Windows | Sottoscrizione di Azure per la risorsa gateway dati creata in precedenza in Azure |
   | **Gateway di connessione** | Sì, per autenticazione di Windows | Nome della risorsa gateway dati creata in precedenza in Azure <p><p>**Suggerimento:** se il gateway non viene visualizzato nell'elenco, verificare di aver [configurato correttamente il gateway.](../logic-apps/logic-apps-gateway-connection.md) |
   |||

   > [!TIP]
   > È possibile trovare queste informazioni nella stringa di connessione del database:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Questo esempio illustra l'aspetto di questi valori:

   ![Creazione connessione SQL Server completata](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Al termine, selezionare **Crea**.

1. A questo punto, continuare con i passaggi non ancora completati in Aggiungere un [trigger SQL](#add-sql-trigger) o [Aggiungere un'azione SQL](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Aggiungere un trigger SQL

1. Nella finestra [portale di Azure](https://portal.azure.com) o in Visual Studio creare un'app per la logica vuota, che apre Progettazione app per la logica. Questo esempio continua con l'portale di Azure.

1. Nella casella di ricerca della finestra di progettazione immettere `sql server` . Nell'elenco dei trigger selezionare il trigger SQL desiderato. In questo esempio viene utilizzato il trigger **Quando viene creato un elemento.**

   ![Selezionare il trigger "Quando viene creato un elemento"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se ci si connette al database SQL per la prima volta, viene richiesto di creare [la connessione al database SQL.](#create-connection) Dopo aver creato questa connessione, è possibile continuare con il passaggio successivo.

1. Nel trigger specificare l'intervallo e la frequenza con cui il trigger controlla la tabella.

1. Per aggiungere altre proprietà disponibili per questo trigger, aprire **l'elenco Aggiungi nuovo** parametro.

   Questo trigger restituisce solo una riga dalla tabella selezionata e non altro. Per eseguire altre attività, continuare aggiungendo un'azione del connettore [SQL](#add-sql-action) o un'altra azione che esegue l'attività successiva desiderata nel flusso di lavoro dell'app per la logica. [](../connectors/apis-list.md)

   Ad esempio, per visualizzare i dati in questa riga, è possibile aggiungere altre azioni per la creazione di un file che include i campi dalla riga restituita e quindi inviare avvisi di posta elettronica. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](/connectors/sql/).

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Anche se questo passaggio abilita e pubblica automaticamente l'app per la logica in tempo reale in Azure, l'unica azione attualmente eseguita dall'app per la logica consiste nel controllare il database in base all'intervallo e alla frequenza specificati.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Attivare lo spostamento e la deriva della ricorrenza

I trigger basati sulla connessione in cui è necessario creare prima una connessione, ad esempio il trigger SQL, differiscono dai trigger predefiniti eseguiti in modo nativo in App per la logica di Azure, ad esempio il [trigger Ricorrenza](../connectors/connectors-native-recurrence.md). Nei trigger ricorrenti basati sulla connessione, la pianificazione della ricorrenza non è l'unico driver che controlla l'esecuzione e il fuso orario determina solo l'ora di inizio iniziale. Le esecuzioni successive dipendono dalla pianificazione della ricorrenza, dall'ultima esecuzione del trigger e da altri fattori che potrebbero causare la deriva dei tempi di esecuzione o produrre un comportamento imprevisto, ad esempio la non gestione della pianificazione specificata all'avvio e alla fine dell'ora legale.  Per assicurarsi che il tempo di ricorrenza non si svii quando l'ora legale ha effetto, modificare manualmente la ricorrenza in modo che l'app per la logica continui a essere eseguita nel momento previsto. In caso contrario, l'ora di inizio viene spostata avanti di un'ora all'inizio dell'ora legale e di un'ora indietro al termine dell'ora legale. Per altre informazioni, [vedere Ricorrenza per i trigger basati sulla connessione.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Aggiungere un'azione SQL

In questo esempio, l'app per la logica inizia con il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md)e chiama un'azione che ottiene una riga da un database SQL.

1. Nella finestra di [portale di Azure](https://portal.azure.com) o in Visual Studio aprire l'app per la logica in Progettazione app per la logica. Questo esempio continua l'portale di Azure.

1. Nel trigger o nell'azione in cui si vuole aggiungere l'azione SQL selezionare **Nuovo passaggio**.

   ![Aggiungere un'azione all'app per la logica](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   In caso contrario, per aggiungere un'azione tra i passaggi esistenti, spostare il puntatore del mouse sulla freccia di connessione. Selezionare il segno più ( **+** ) visualizzato e quindi **Aggiungi un'azione**.

1. In **Scegliere un'azione** immettere `sql server` nella casella di ricerca. Nell'elenco delle azioni scegliere l'azione SQL desiderata. Questo esempio usa l'azione **Ottieni riga**, che ottiene un singolo record.

   ![Selezionare l'azione "Ottieni riga" di SQL](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Se ci si connette al database SQL per la prima volta, viene richiesto di creare [la connessione al database SQL ora](#create-connection). Dopo aver creato la connessione, è possibile continuare con il passaggio successivo.

1. Selezionare il **nome della tabella**, che in questo esempio è `SalesLT.Customer` . Immettere **l'ID** riga per il record desiderato.

   ![Selezionare il nome della tabella e specificare l'ID riga](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Questa azione restituisce solo una riga della tabella selezionata. Pertanto, per visualizzare i dati in questa riga, è possibile aggiungere altre azioni che creano un file che include i campi della riga restituita e archiviano il file in un account di archiviazione cloud. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](/connectors/sql/).

1. Al termine, nella barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Con questo passaggio l'app per la logica viene automaticamente abilitata e pubblicata in Azure.

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>Gestire i dati in blocco

A volte, è necessario usare set di risultati di dimensioni tali che il connettore non riesce a restituire tutti i risultati contemporaneamente oppure si vuole un controllo migliore sulle dimensioni e sulla struttura dei set di risultati. Ecco alcuni modi in cui è possibile gestire set di risultati di grandi dimensioni:

* Per gestire i risultati come set più piccoli, attivare la *paginazione*. Per altre informazioni, vedere [Ottenere dati in blocco, record ed elementi usando la paginazione](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md). Per altre informazioni, vedere [Paginazione SQL per il trasferimento bulk dei dati con App per la logica.](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

* Creare un [*stored procedure*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) che organizza i risultati nel modo desiderato. Il connettore SQL offre molte funzionalità back-end a cui è possibile accedere usando App per la logica di Azure per automatizzare più facilmente le attività aziendali che funzionano con le tabelle di database SQL.

  Se si ottengono o si inseriscono più righe, l'app per la logica può scorrere queste righe usando un [*ciclo Until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) nell'ambito di questi [limiti](../logic-apps/logic-apps-limits-and-config.md). In alcuni casi, tuttavia, l'app per la logica deve interagire con set di record talmente grandi, ad esempio migliaia o milioni di righe, che si vuole ridurre al minimo i costi risultanti dalle chiamate al database.

  Per organizzare i risultati nel modo desiderato, è possibile creare una stored procedure che viene eseguita nell'istanza di SQL e usa l'istruzione **SELECT - ORDER BY**. Questa soluzione consente di controllare meglio le dimensioni e la struttura dei risultati. L'app per logica chiama la stored procedure usando l'azione **Esegui stored procedure** del connettore SQL Server. Per altre informazioni, vedere [Clausola SELECT - ORDER BY](/sql/t-sql/queries/select-order-by-clause-transact-sql).

  > [!NOTE]
  > Il connettore SQL ha stored procedure di timeout massimo inferiore [a 2 minuti.](/connectors/sql/#known-issues-and-limitations) Il completamento di alcune stored procedure potrebbe richiedere più tempo rispetto a questo limite, causando un `504 Timeout` errore. È possibile risolvere questo problema usando un trigger di completamento SQL, una query pass-through SQL nativa, una tabella di stato e processi sul lato server.
  > 
  > Per questa attività, è possibile usare [l'agente processo elastico di Azure](../azure-sql/database/elastic-jobs-overview.md) per [database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md). Per [SQL Server locale e](/sql/sql-server/sql-server-technical-documentation) Istanza gestita di SQL di Azure [](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), è possibile usare il SQL Server Agent . [](/sql/ssms/agent/sql-server-agent) Per altre informazioni, vedere Gestire i timeout stored procedure esecuzione lunga [nel connettore SQL per App per la logica di Azure](../logic-apps/handle-long-running-stored-procedures-sql-connector.md).

### <a name="handle-dynamic-bulk-data"></a>Gestire i dati in blocco dinamici

Quando si chiama un stored procedure usando il connettore SQL Server, l'output restituito è talvolta dinamico. In questo scenario, seguire questi passaggi:

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Visualizzare il formato di output eseguendo un'esecuzione dei test. Copiare e salvare l'output di esempio.

1. Nella finestra di progettazione, in corrispondenza dell'azione in cui è stata effettuata la chiamata alla stored procedure, selezionare **Nuovo passaggio**.

1. In **Scegliere un'azione** individuare e selezionare l'azione [**Analizza JSON.**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)

1. Nell'azione **Analizza JSON** selezionare **Usare il payload di esempio per generare lo schema**.

1. Nella casella **Immettere o incollare un payload JSON** di esempio incollare l'output di esempio e selezionare **Fine.**

   > [!NOTE]
   > Se viene restituito un errore che indica che App per la logica non può generare uno schema, verificare che la sintassi dell'output di esempio sia formattata correttamente. Se non è ancora possibile generare  lo schema, immettere manualmente lo schema nella casella Schema.

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

1. Per fare riferimento alle proprietà del contenuto JSON, fare clic all'interno delle caselle di modifica in cui si vuole fare riferimento a tali proprietà in modo che venga visualizzato l'elenco di contenuto dinamico. Nell'elenco, sotto [**l'intestazione Analizza JSON,**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) selezionare i token di dati per le proprietà di contenuto JSON desiderate.

## <a name="troubleshoot-problems"></a>Risolvere i problemi

<a name="connection-problems"></a>

### <a name="connection-problems"></a>Problemi di connessione

I problemi di connessione possono in genere verificarsi, quindi per risolvere questi tipi di problemi, vedere Risoluzione degli errori di connettività [SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Ecco alcuni esempi:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti di questo connettore, vedere la pagina di riferimento del connettore [,](/connectors/sql/)generata dalla descrizione di Swagger.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori per App per la logica di Azure](../connectors/apis-list.md)
