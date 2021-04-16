---
title: Azure SQL controllo per database SQL di Azure e Azure Synapse Analytics
description: Usare database SQL di Azure controllo per tenere traccia degli eventi del database in un log di controllo.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/17/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: bc7ac6b97d10e5941e46b8be3e12baff32bded4a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483062"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Controllo per database SQL di Azure e Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Il controllo [per](sql-database-paas-overview.md) database SQL di Azure e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tiene traccia degli eventi del database e li scrive in un log di controllo nell'account di archiviazione di Azure, nell'area di lavoro Log Analytics o in Hub eventi.

Inoltre, il servizio di controllo:

- Consente di gestire la conformità alle normative, ottenere informazioni sull'attività del database e rilevare discrepanze e anomalie che possono indicare problemi aziendali o possibili violazioni della sicurezza.

- Supporta e facilita il rispetto degli standard di conformità, pur non garantendo la conformità. Per altre informazioni sui programmi di Azure che supportano la conformità agli standard, vedere il [Centro protezione di Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), dove è possibile trovare l'elenco più recente di certificazioni di conformità per Azure SQL.

> [!NOTE]
> Per informazioni sul Istanza gestita di SQL di Azure controllo, vedere l'articolo seguente Introduzione a [SQL Istanza gestita controllo](../managed-instance/auditing-configure.md).

## <a name="overview"></a><a id="overview"></a>Panoramica

È possibile usare il servizio di controllo del database SQL per eseguire le operazioni seguenti:

- **Mantenere** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
- **Report sull'attività** del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l'attività e la segnalazione di eventi.
- **Analizzare i** report. È possibile individuare eventi sospetti, attività insolite e tendenze.

> [!IMPORTANT]
> Il controllo per database SQL di Azure e Azure Synapse è ottimizzato per la disponibilità e le prestazioni. Durante un'attività molto elevata o un carico di rete elevato, database SQL di Azure e Azure Synapse le operazioni possono continuare e potrebbero non registrare alcuni eventi controllati.

### <a name="auditing-limitations"></a>Limitazioni del controllo

- **L'archiviazione Premium** non **è attualmente supportata.**
- **Lo spazio dei** **nomi gerarchico Azure Data Lake Storage Gen2'account di** archiviazione non è attualmente **supportato.**
- L'abilitazione del controllo **in** un Azure Synapse sospeso non è supportata. Per abilitare il controllo, riprendere Azure Synapse.
- Il controllo per i **Azure Synapse SQL supporta** solo i gruppi di azioni di controllo **predefiniti.**


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definire criteri di controllo a livello di server o a livello di database

È possibile definire criteri di controllo per un database specifico o come criteri [server](logical-servers.md) predefiniti in Azure (che ospita database SQL o Azure Synapse):

- I criteri server si applicano a tutti i database nuovi ed esistenti in un server.

- Se *il controllo del server è abilitato,* si applica sempre al *database*. Il database verrà controllato, indipendentemente dalle impostazioni di controllo del database.

- Quando i criteri di controllo vengono definiti a livello di database in un'area di lavoro Log Analytics o in una destinazione dell'hub eventi, le operazioni seguenti non manderanno i criteri di controllo a livello di database di origine:
    - [Copia del database](database-copy.md)
    - [Ripristino temporizzato](recovery-using-backups.md)
    - [Replica geografica](active-geo-replication-overview.md) (il database secondario non avrà il controllo a livello di database)

- L'abilitazione del controllo nel database, oltre a  abilitarlo nel server, non esegue l'override o la modifica delle impostazioni del controllo del server. I due controlli coesisteranno. In altre parole, il database viene controllato due volte in parallelo, una volta con i criteri del server e una volta con i criteri del database.

   > [!NOTE]
   > È consigliabile evitare di abilitare insieme il controllo del server e il controllo BLOB del database, a meno che:
    >
    > - Si vuole usare un account di archiviazione, *un* periodo di *conservazione* o un'area di lavoro Log *Analytics diversa* per un database specifico.
    > - Per un database specifico si vogliono controllare tipi o categorie di eventi diversi dagli altri database nel server. Ad esempio, potrebbe essere necessario controllare gli inserimenti di tabella solo per un database specifico.
   >
   > In caso contrario, è consigliabile abilitare solo il controllo a livello di server e lasciare disabilitato il controllo a livello di database per tutti i database.

#### <a name="remarks"></a>Commenti

- I log di controllo vengono scritti in **Aggiungere BLOB** in un archivio BLOB di Azure nella sottoscrizione di Azure
- I log di controllo sono in formato xel e possono essere aperti [usando SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms)
- Per configurare un archivio log non modificabile per il server o gli eventi di controllo a livello di database, seguire le istruzioni fornite [da Archiviazione di Azure](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). Assicurarsi di aver selezionato Consenti **aggiunte aggiuntive quando** si configura l'archiviazione BLOB non modificabile.
- È possibile scrivere log di controllo in un account Archiviazione di Azure dietro una rete virtuale o un firewall. Per istruzioni specifiche, vedere [Scrivere il controllo in un account di archiviazione dietro la rete virtuale e il firewall.](audit-write-storage-account-behind-vnet-firewall.md)
- Per dettagli sul formato dei log, sulla gerarchia della cartella di archiviazione e sulle convenzioni di denominazione, vedere le [informazioni di riferimento sul formato dei log del controllo BLOB](./audit-log-format.md).
- Il controllo sulle [repliche di sola lettura](read-scale-out.md) viene abilitato automaticamente. Per altre informazioni sulla gerarchia delle cartelle di archiviazione, sulle convenzioni di denominazione e sul formato dei log, vedere Il formato del log di [controllo del database SQL](audit-log-format.md).
- Quando si Autenticazione di Azure AD, i record degli account di accesso non riusciti *non verranno* visualizzati nel log di controllo SQL. Per visualizzare i record di controllo degli accessi non riusciti, è necessario visitare il [portale di Azure Active Directory](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), che registra i dettagli di questi eventi.
- Gli accessi vengono instradati dal gateway all'istanza specifica in cui si trova il database.  Nel caso degli accessi di AAD, le credenziali vengono verificate prima di provare a usare l'utente per accedere al database richiesto.  In caso di errore, non viene mai eseguito l'accesso al database richiesto, quindi non si verifica alcun controllo.  Nel caso di account di accesso SQL, le credenziali vengono verificate sui dati richiesti, quindi in questo caso possono essere controllati.  Gli accessi riusciti, che ovviamente raggiungono il database, vengono controllati in entrambi i casi.
- Dopo aver configurato le impostazioni di controllo, è possibile attivare la nuova funzionalità di rilevamento delle minacce e configurare gli indirizzi di posta elettronica per ricevere gli avvisi di sicurezza. Quando si usa il rilevamento delle minacce, si ricevono avvisi proattivi sulle attività di database anomale che possono indicare potenziali minacce per la sicurezza. Per altre informazioni, vedere [Introduzione al rilevamento delle minacce](threat-detection-overview.md).

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Configurare il controllo per il server

I criteri di controllo predefinito includono tutte le azioni e il set di gruppi di azioni seguente, che controllerà tutte le query e le stored procedure eseguite sul database, nonché gli accessi riusciti e non riusciti:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
È possibile configurare il controllo per diversi tipi di azioni e gruppi di azioni usando PowerShell, come descritto nella sezione Gestire il controllo del [database SQL usando](#manage-auditing) Azure PowerShell.

database SQL di Azure e Azure Synapse audit archivia 4000 caratteri di dati per i campi carattere in un record di controllo. Quando i valori **statement** o **data_sensitivity_information** restituiti da un'azione controllabile contengono più di 4000 caratteri, i dati oltre i primi 4000 caratteri verranno **troncati e non controllati**.
Nella sezione seguente è descritta la configurazione del controllo mediante il portale di Azure.

  > [!NOTE]
  > - Non è possibile abilitare il controllo in un pool SQL dedicato sospeso. Per abilitare il controllo, annullare la sospensione del pool SQL dedicato. Altre informazioni sul [pool SQL dedicato.](../..//synapse-analytics/sql/best-practices-dedicated-sql-pool.md)
  > - Quando il controllo è configurato in un'area di lavoro Log Analytics o in [](../../azure-monitor/essentials/diagnostic-settings.md) una destinazione dell'hub eventi tramite il cmdlet portale di Azure o PowerShell, viene creata un'impostazione di diagnostica con la categoria "SQLSecurityAuditEvents" abilitata.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a **Controllo sotto** l'intestazione Sicurezza nel riquadro **Database SQL** o **SQL Server.**
3. Se si preferisce configurare un criterio di controllo del server, è possibile selezionare il collegamento **Visualizza impostazioni del server** nella pagina relativa al controllo del database. Si possono quindi visualizzare o modificare le impostazioni di controllo del server. I criteri di controllo del server si applicano a tutti i database esistenti e appena creati in questo server.

    ![Screenshot che mostra il collegamento Visualizza impostazioni server evidenziato nella pagina di controllo del database.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Se si preferisce abilitare il controllo a livello di database, impostare **Controllo** su **ATTIVA**. Se il controllo del server è abilitato, il controllo configurato del database coesisterà con il controllo del server.

5. Sono disponibili più opzioni per la configurazione della posizione in cui verranno scritti i log di controllo. È possibile scrivere log in un account di archiviazione di Azure, in un'area di lavoro Log Analytics per usarli Monitoraggio di Azure log o nell'hub eventi per l'utilizzo tramite l'hub eventi. È possibile configurare qualsiasi combinazione di queste opzioni e verranno scritti i log di controllo per ognuno.
  
   ![opzioni di archiviazione](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Controllo delle operazioni Supporto tecnico Microsoft operazioni

Il controllo delle Supporto tecnico Microsoft per Azure SQL Server consente di controllare le operazioni dei tecnici del supporto Tecnico Microsoft quando devono accedere al server durante una richiesta di supporto. L'uso di questa funzionalità, insieme al controllo, consente una maggiore trasparenza nella forza lavoro e consente il rilevamento delle anomalie, la visualizzazione delle tendenze e la prevenzione della perdita di dati.

Per abilitare il controllo delle Supporto tecnico Microsoft,  passare a Controllo sotto l'intestazione Sicurezza nel riquadro **del server Azure SQL** e impostare Controllo delle operazioni di supporto **Microsoft** su **ON.**

  > [!IMPORTANT]
  > Il controllo delle operazioni di supporto Microsoft non supporta la destinazione dell'account di archiviazione. Per abilitare la funzionalità, è necessario configurare un'area di lavoro Log Analytics o una destinazione dell'hub eventi.

![Screenshot di Supporto tecnico Microsoft operazioni](./media/auditing-overview/support-operations.png)

Per esaminare i log di controllo delle Supporto tecnico Microsoft nell'area di lavoro Log Analytics, usare la query seguente:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Controllare la destinazione di archiviazione

Per configurare la scrittura dei log di controllo in un account di archiviazione, selezionare **Archiviazione** quando si accede alla **sezione Controllo.** Selezionare l'account di archiviazione di Azure in cui verranno salvati i log e quindi selezionare il periodo di conservazione aprendo **Proprietà avanzate.** Fare quindi clic su **Salva**. I log antecedenti al periodo di conservazione vengono eliminati.

- Il valore predefinito per il periodo di conservazione è 0 (conservazione illimitata). È possibile modificare questo valore spostando il dispositivo di scorrimento **Conservazione (giorni)** in Proprietà **avanzate** durante la configurazione dell'account di archiviazione per il controllo.
  - Se si modifica il periodo di conservazione da 0 (conservazione illimitata) a qualsiasi altro valore, tenere presente che la conservazione verrà applicata solo ai log scritti dopo la modifica del valore di conservazione (i log scritti durante il periodo in cui la conservazione è stata impostata su illimitata vengono conservati, anche dopo l'abilitazione della conservazione).

  ![archiviazione di Azure](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Da controllo a destinazione di Log Analytics
  
Per configurare la scrittura dei log di controllo in un'area di lavoro **Log Analytics,** selezionare Log Analytics e aprire **Dettagli di Log Analytics.** Selezionare l'area di lavoro Log Analytics in cui verranno scritti i log e quindi fare clic su **OK.** Se non è stata creata un'area di lavoro Log Analytics, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../../azure-monitor/logs/quick-create-workspace.md).

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Per altre informazioni sull'Monitoraggio di Azure di lavoro Log Analytics, vedere [Progettazione della distribuzione Monitoraggio di Azure log di log](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Da controllo a destinazione hub eventi

Per configurare la scrittura dei log di controllo in un hub eventi, selezionare **Hub eventi.** Selezionare l'hub eventi in cui verranno scritti i log e quindi fare clic su **Salva**. Assicurarsi che l'hub eventi si trovi nella stessa area del database e server.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analizzare i log di controllo e i report

Se si sceglie di scrivere i log di controllo in Monitoraggio di Azure seguenti:

- Usare il [portale di Azure](https://portal.azure.com). Aprire il database corrispondente. Nella parte superiore della pagina **Controllo del** database selezionare Visualizza log **di controllo**.

    ![visualizzare i log di controllo](./media/auditing-overview/auditing-view-audit-logs.png)

- Sono quindi disponibili due modi per visualizzare i log:

    Facendo clic **su Log** Analytics  nella parte superiore della pagina Record di controllo verrà aperta la visualizzazione Log nell'area di lavoro Log Analytics, in cui è possibile personalizzare l'intervallo di tempo e la query di ricerca.

    ![aprire nell'area di lavoro Log Analytics](./media/auditing-overview/auditing-log-analytics.png)

    Facendo **clic su Visualizza**  dashboard nella parte superiore della pagina Record di controllo verrà aperto un dashboard con informazioni sui log di controllo, in cui è possibile eseguire il drill-down in Informazioni dettagliate sulla sicurezza, Accesso ai dati sensibili e altro ancora. Questo dashboard è progettato per consentire di ottenere informazioni dettagliate sulla sicurezza per i dati.
    È anche possibile personalizzare l'intervallo di tempo e la query di ricerca.
    ![Visualizzare il dashboard di Log Analytics](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics Dashboard](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Informazioni dettagliate sulla sicurezza di Log Analytics](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- In alternativa, è possibile accedere anche i log di controllo dal pannello Log Analytics. Aprire l'area di lavoro Log Analytics e nella sezione **generale** fare clic su **log**. È possibile iniziare con una query semplice, ad esempio: *cercare "SQLSecurityAuditEvents"* per visualizzare i log di controllo.
    Da qui è anche possibile usare i [log Monitoraggio di Azure per](../../azure-monitor/logs/log-query-overview.md)  eseguire ricerche avanzate sui dati del log di controllo. Log di Monitoraggio di Azure consente di ottenere informazioni operative in tempo reale tramite funzionalità di ricerca integrate e dashboard personalizzati per analizzare rapidamente milioni di record in tutti i carichi di lavoro e i server. Per altre informazioni utili sul linguaggio di ricerca e i comandi di Log di Monitoraggio di Azure, vedere [Guida di riferimento alla ricerca in Log di Monitoraggio di Azure](../../azure-monitor/logs/log-query-overview.md).

Se si sceglie di scrivere i log di controllo su Hub eventi:

- Per utilizzare i dati dei log di controllo da Hub eventi, è necessario configurare un flusso per utilizzare gli eventi e scriverli in una destinazione. Per altre informazioni, vedere la [documentazione Hub eventi di Azure .](../index.yml)
- I log di controllo in Hub eventi vengono acquisiti nel corpo degli eventi di [Apache Avro](https://avro.apache.org/) e archiviati usando la formattazione JSON con codifica UTF-8. Per leggere i log di controllo, è possibile usare [Avro Tools](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) o strumenti simili in grado di elaborare tale formato.

Se si sceglie di scrivere i log di controllo in un account di archiviazione di Azure, esistono diversi metodi che è possibile usare per visualizzare i log:

- I log di controllo vengono aggregati nell'account selezionato durante la configurazione. È possibile esplorare i log di controllo con uno strumento come [Azure Storage Explorer](https://storageexplorer.com/). Nell'archiviazione di Azure, i log del controllo vengono salvati come raccolta di file BLOB in un contenitore denominato **sqldbauditlogs**. Per altre informazioni sulla gerarchia delle cartelle di archiviazione, sulle convenzioni di denominazione e sul formato dei log, vedere Il formato del log di [controllo del database SQL](./audit-log-format.md).

- Usare il [portale di Azure](https://portal.azure.com).  Aprire il database corrispondente. Nella parte superiore della pagina **Controllo** del database fare clic su **Visualizza log di controllo**.

    ![visualizzare i log di controllo](./media/auditing-overview/auditing-view-audit-logs.png)

    Verrà aperto **Record di controllo**, da cui sarà possibile visualizzare i log.

  - È possibile visualizzare date specifiche facendo clic su **Filtro** nella parte superiore della pagina **Record di controllo**.
  - È possibile passare da un record di controllo all'altro creato dai *criteri di controllo del server* e dai *criteri di controllo del database* attivando o disattivando **Origine controllo**.

       ![Screenshot che mostra le opzioni per la visualizzazione dei record di controllo.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Usare la funzione di sistema **sys.fn_get_audit_file** (T-SQL) per tornare ai dati dei log di controllo in formato tabulare. Per altre informazioni su questa funzione, vedere [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Usare **Unisci file di controllo** in SQL Server Management Studio (a partire da SSMS 17):
    1. Dal menu SSMS selezionare **Apri file**  >    >  **di controllo unione**.

        ![Screenshot che mostra l'opzione di menu Unisci file di controllo.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Verrà visualizzata la finestra di dialogo **Aggiunti file di controllo**. Selezionare una delle opzioni **Aggiungi** per scegliere se unire i file di controllo da un disco locale oppure importarli da Archiviazione di Azure. È necessario specificare i dettagli e la chiave dell'account di Archiviazione di Azure.

    3. Dopo aver aggiunto tutti i file da unire, fare clic su **OK** per completare l'operazione di unione.

    4. Il file unito verrà aperto in SSMS, dove potrà essere visualizzato, analizzato ed esportato in un file XEL o CSV o in una tabella.

- Usare Power BI. È possibile visualizzare e analizzare i dati dei log di controllo in Power BI. Per altre informazioni e per accedere a un modello scaricabile, vedere [Analyze audit log data in Power BI](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895) (Analizzare i dati di log di controllo in Power BI).
- Scaricare i file di log Archiviazione di Azure contenitore BLOB tramite il portale o usando uno strumento come [Azure Storage Explorer](https://storageexplorer.com/).
  - Dopo aver scaricato un file di log in locale, fare doppio clic sul file per aprire, visualizzare e analizzare i log in SSMS.
  - È anche possibile scaricare più file contemporaneamente tramite Azure Storage Explorer. Per farlo, fare clic con il pulsante destro del mouse su una sottocartella specifica e scegliere **Salva con nome** per salvarla in una cartella locale.

- Altri metodi:

  - Dopo avere scaricato diversi file o una sottocartella contenente i file di log, è possibile unirli in locale come descritto nelle istruzioni relative all'unione di file di controllo in SSMS riportate in precedenza.
  - Visualizzare i log di controllo BLOB a livello di codice: [eseguire query su file di eventi estesi](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) usando PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Procedure nell'ambiente di produzione


### <a name="auditing-geo-replicated-databases"></a>Controllo dei database con replica geografica

Con i database con replica geografica, quando si abilita il controllo nel database primario il database secondario disporrà di un criterio di controllo identico. È anche possibile impostare il controllo nel database secondario abilitando il controllo nel **server secondario**, in modo indipendente dal database primario.

- A livello di server (**consigliato**): attivare il controllo sia nel **server primario** che nel **server secondario**. I database primari e secondari saranno controllati in modo indipendente in base ai rispettivi criteri a livello di server.
- A livello di database: il controllo a livello di database per i database secondari può essere configurato solo mediante le impostazioni di controllo del database primario.
  - Il controllo deve essere abilitato nello *stesso database primario* e non nel server.
  - Dopo che il controllo è stato abilitato nel database primario, verrà abilitato anche nel database secondario.

    > [!IMPORTANT]
    > In caso di controllo a livello di database, le impostazioni di archiviazione per il database secondario sono identiche a quelle del database primario, e causano traffico tra le aree. È consigliabile abilitare solo il controllo a livello di server e lasciare disabilitato il controllo a livello di database per tutti i database.

### <a name="storage-key-regeneration"></a>Rigenerazione delle chiavi di archiviazione

Durante la produzione è probabile che periodicamente vengano aggiornate le chiavi di archiviazione. Quando si scrivono i log di controllo nell'archiviazione di Azure, è necessario salvare nuovamente i criteri di controllo quando si aggiornano le chiavi. Il processo è il seguente:

1. Aprire **Proprietà avanzate in** **Archiviazione**. Nella casella **Chiave di accesso all'archiviazione** selezionare **Secondario.** Fare quindi clic su **Salva** nella parte superiore della pagina di configurazione del controllo.

    ![Screenshot che mostra il processo per la selezione di una chiave di accesso di archiviazione secondaria.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Passare alla pagina di configurazione dell'archiviazione e rigenerare la chiave di accesso primaria.

    ![Riquadro di spostamento](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Tornare alla pagina di configurazione del controllo, modificare la chiave di accesso alle risorse di archiviazione da secondaria a primaria e quindi fare clic su **OK**. Fare quindi clic su **Salva** nella parte superiore della pagina di configurazione del controllo.
4. Tornare alla pagina di configurazione dell'archiviazione e rigenerare la chiave di accesso secondaria (in preparazione al successivo ciclo di aggiornamento della chiave).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Gestire database SQL di Azure controllo

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

**Cmdlet PowerShell (incluso il supporto della clausola WHERE per altri filtri)**:

- [Creare o aggiornare il criterio di controllo del database (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Creare o aggiornare il criterio di controllo del server (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Ottenere i criteri di controllo del database (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Ottenere i criteri di controllo del server (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Rimuovere i criteri di controllo del database (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Rimuovere i criteri di controllo del server (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Per un esempio di script, vedere [Configurare il controllo del database SQL e il rilevamento delle minacce usando PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

### <a name="using-rest-api"></a>Uso dell'API REST

**API REST**:

- [Creare o aggiornare i criteri controllo del database](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Creare o aggiornare i criteri controllo del server](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Ottenere i criteri controllo del database](/rest/api/sql/database%20auditing%20settings/get)
- [Ottenere i criteri controllo del server](/rest/api/sql/server%20auditing%20settings/get)

Criteri estesi con il supporto della clausola WHERE per altri filtri:

- [Creare o aggiornare i criteri *di controllo* esteso del database](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Creare o aggiornare i criteri *di controllo* esteso del server](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Ottenere i *criteri di controllo* esteso del database](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Ottenere i criteri *di controllo* esteso del server](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

- [Gestire i criteri di controllo di un server](/cli/azure/sql/server/audit-policy)
- [Gestire i criteri di controllo di un database](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Uso di modelli di Azure Resource Manager

È possibile gestire database SQL di Azure controllo tramite [modelli Azure Resource Manager,](../../azure-resource-manager/management/overview.md) come illustrato negli esempi seguenti:

- [Distribuire un'database SQL di Azure con il controllo abilitato per scrivere i log di controllo nell'account di archiviazione BLOB di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Distribuire un database SQL di Azure con controllo abilitato per scrivere i log di controllo in Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Distribuire un database SQL di Azure con il controllo abilitato per scrivere i log di controllo in Hub eventi](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Gli esempi collegati si trova in un repository pubblico esterno e vengono forniti "così come sono", senza garanzia, e non sono supportati in alcun programma o servizio di supporto Tecnico Microsoft.
