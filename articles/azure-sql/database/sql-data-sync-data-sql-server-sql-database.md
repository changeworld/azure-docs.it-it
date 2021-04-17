---
title: Che cos'sincronizzazione dati SQL per Azure?
description: Questa panoramica presenta sincronizzazione dati SQL per Azure, che consente di sincronizzare i dati tra più database cloud e locali.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: 695409740348e78ae51b263b44d9ed1cbadc1054
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531924"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Che cos'sincronizzazione dati SQL per Azure?

sincronizzazione dati SQL è un servizio basato su database SQL di Azure che consente di sincronizzare i dati selezionati in modo bidirezionale tra più database, sia in locale che nel cloud. 

> [!IMPORTANT]
> sincronizzazione dati SQL di Azure non supporta Istanza gestita di SQL di Azure in questo momento.


## <a name="overview"></a>Panoramica 

sincronizzazione dati si basa sul concetto di gruppo di sincronizzazione. Un gruppo di sincronizzazione è un gruppo di database da sincronizzare.

La sincronizzazione dati usa una topologia hub-spoke per sincronizzare i dati. Uno dei database nel gruppo di sincronizzazione viene definito come database hub. Il resto dei database sono database membri. La sincronizzazione viene eseguita solo tra l'hub e i singoli membri.

- Il **database hub** deve essere un database SQL di Azure.
- I **database membri** possono essere database in database SQL di Azure o in istanze di SQL Server.
- Il **database dei metadati di** sincronizzazione contiene i metadati e il log per sincronizzazione dati. Il database dei metadati di sincronizzazione deve database SQL di Azure nella stessa area del database hub. Il database dei metadati di sincronizzazione viene creato dal cliente e di proprietà del cliente. È possibile avere un solo database di metadati di sincronizzazione per area e sottoscrizione. Non è possibile eliminare o rinominare il database dei metadati di sincronizzazione mentre esistono gruppi di sincronizzazione o agenti di sincronizzazione. Microsoft consiglia di creare un nuovo database vuoto da usare come Database dei metadati di sincronizzazione. La sincronizzazione dati crea tabelle in questo database ed esegue un carico di lavoro frequente.

> [!NOTE]
> Se si usa un database locale come database membro, è necessario [installare e configurare un agente di sincronizzazione locale](sql-data-sync-sql-server-configure.md#add-on-prem).

![Sincronizzare i dati tra database](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Un gruppo di sincronizzazione ha le proprietà seguenti:

- Lo **schema di sincronizzazione** descrive i dati da sincronizzare.
- La **direzione di sincronizzazione** può essere bidirezionale o unidirezionale. Ovvero, la direzione di sincronizzazione può essere *dall'hub al membro* o *dal membro all'hub* oppure entrambe.
- L'**intervallo di sincronizzazione** descrive la frequenza con cui viene eseguita la sincronizzazione.
- I **criteri di risoluzione dei conflitti** sono criteri a livello di gruppo e le impostazioni possono essere *Priorità hub* o *Priorità client*.

## <a name="when-to-use"></a>Utilizzo

sincronizzazione dati è utile nei casi in cui i dati devono essere mantenuti aggiornati in più database in database SQL di Azure o SQL Server. Questi sono i principali casi d'uso per la sincronizzazione dati:

- **Sincronizzazione dei dati ibrida:** Con sincronizzazione dati, è possibile mantenere i dati sincronizzati tra i database in SQL Server e database SQL di Azure abilitare le applicazioni ibride. Questa funzionalità può essere interessante per i clienti che stanno valutando il passaggio al cloud e vorrebbero trasferire alcune applicazioni in Azure.
- **Applicazioni distribuite:** in molti casi è vantaggioso separare carichi di lavoro diversi in database differenti. Ad esempio, se si dispone di un database di produzione di grandi dimensioni, ma è anche necessario eseguire un carico di lavoro di report o analisi su tali dati, può essere utile avere un secondo database per questo carico di lavoro aggiuntivo. Questo approccio riduce al minimo l'impatto a livello di prestazioni sul carico di lavoro di produzione. È possibile usare la sincronizzazione dati per mantenere sincronizzati i due database.
- **Applicazioni distribuite a livello globale:** Molte aziende si estendono in diverse aree geografiche e anche in diversi paesi/aree geografiche. Per ridurre al minimo la latenza di rete, è consigliabile posizionare i dati in un'area vicina. Con sincronizzazione dati è possibile mantenere facilmente sincronizzati i database in aree in tutto il mondo.

sincronizzazione dati non è la soluzione preferita per gli scenari seguenti:

| Scenario | Alcune soluzioni raccomandate |
|----------|----------------------------|
| Ripristino di emergenza | [Backup con ridondanza geografica di Azure](automated-backups-overview.md) |
| Scalabilità in lettura | [Usare le repliche di sola lettura per bilanciare il carico dei carichi di lavoro di query di sola lettura (anteprima)](read-scale-out.md) |
| ETL (da OLTP a OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) o [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migrazione da SQL Server a database SQL di Azure. Tuttavia, sincronizzazione dati SQL può essere usato dopo il completamento della migrazione, per garantire che l'origine e la destinazione siano mantenute sincronizzate.  | [Servizio Migrazione del database di Azure](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Funzionamento

- **Rilevamento delle modifiche ai dati:** sincronizzazione dati tiene traccia delle modifiche tramite trigger di inserimento, aggiornamento ed eliminazione. Le modifiche vengono registrate in una tabella laterale nel database utente. Si noti BULK INSERT non attiva i trigger per impostazione predefinita. Se FIRE_TRIGGERS non è specificato, non viene eseguito alcun trigger di inserimento. Aggiungere l'opzione FIRE_TRIGGERS in modo che la sincronizzazione dei dati possa tenere traccia di tali inserimenti. 
- **Sincronizzazione dei dati:** sincronizzazione dati è progettato in un modello hub e spoke. L'hub viene sincronizzato con ogni membro singolarmente. Le modifiche dall'hub vengono scaricate nel membro e quindi vengono caricate nell'hub.
- **Risoluzione dei conflitti:** sincronizzazione dati offre due opzioni per la risoluzione dei conflitti, ovvero *Priorità hub* o *Priorità client*.
  - Se si seleziona *Priorità hub*, le modifiche nell'hub sovrascrivono sempre le modifiche nel membro.
  - Se si seleziona *Priorità client*, le modifiche nel membro sovrascrivono sempre le modifiche nell'hub. In presenza di più di un membro, il valore finale dipende dal membro sincronizzato per primo.

## <a name="compare-with-transactional-replication"></a>Confronto con la replica transazionale

| | Sincronizzazione dei dati | Replica transazionale |
|---|---|---|
| **Vantaggi** | - Supporto attivo/attivo<br/>- Bidirezionale tra database locali e database SQL di Azure | - Latenza inferiore<br/>- Coerenza delle transazioni<br/>- Riutilizzo topologia esistente dopo la migrazione <br/>-Istanza gestita di SQL di Azure supporto |
| **Svantaggi** | - Nessuna coerenza delle transazioni<br/>- Maggiore impatto sulle prestazioni | - Non è possibile pubblicare da database SQL di Azure <br/>-    Alti costi di manutenzione |

## <a name="private-link-for-data-sync-preview"></a>Collegamento privato per sincronizzazione dati (anteprima)
La nuova funzionalità collegamento privato (anteprima) consente di scegliere un endpoint privato gestito dal servizio per stabilire una connessione sicura tra il servizio di sincronizzazione e i database membro/hub durante il processo di sincronizzazione dei dati. Un endpoint privato gestito dal servizio è un indirizzo IP privato all'interno di una rete virtuale e di una subnet specifici. In sincronizzazione dati, l'endpoint privato gestito dal servizio viene creato da Microsoft e viene usato esclusivamente dal servizio sincronizzazione dati per una determinata operazione di sincronizzazione. Prima di configurare il collegamento privato, leggere i [requisiti generali](sql-data-sync-data-sql-server-sql-database.md#general-requirements) per la funzionalità. 

![Collegamento privato per sincronizzazione dati](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> È necessario approvare manualmente l'endpoint privato gestito dal servizio nella pagina **Connessioni endpoint** privato del portale di Azure durante la distribuzione del gruppo di sincronizzazione o tramite PowerShell.

## <a name="get-started"></a>Introduzione 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurare la sincronizzazione dati nel portale di Azure

- [Configurare la sincronizzazione dati SQL](sql-data-sync-sql-server-configure.md)
- Agente di sincronizzazione dei dati: [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL di Azure](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurare la sincronizzazione dati con PowerShell

- [Usare PowerShell per eseguire la sincronizzazione tra più database in database SQL di Azure](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Usare PowerShell per eseguire la sincronizzazione tra un database in database SQL di Azure e un database in un'SQL Server istanza](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>Configurare le sincronizzazione dati con l'API REST
- [Usare l'API REST per la sincronizzazione tra più database in database SQL di Azure](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Rivedere le procedure consigliate per la sincronizzazione dati

- [Procedure consigliate per la sincronizzazione dati SQL di Azure](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Nel caso in cui si siano verificati problemi

- [Risolvere i problemi della sincronizzazione dati SQL di Azure](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Coerenza e prestazioni

### <a name="eventual-consistency"></a>Coerenza finale

Poiché sincronizzazione dati è basata su trigger, la coerenza transazionale non è garantita. Microsoft garantisce che tutte le modifiche siano apportate alla fine e che sincronizzazione dati non causerà la perdita di dati.

### <a name="performance-impact"></a>Impatto sulle prestazioni

La sincronizzazione dati usa trigger di inserimento, aggiornamento ed eliminazione per il rilevamento delle modifiche e crea tabelle laterali nel database utente per il rilevamento delle modifiche. Queste attività di rilevamento delle modifiche hanno un impatto sul carico di lavoro del database. Valutare il livello di servizio e aggiornare se necessario.

Sulle prestazioni del database possono incidere anche il provisioning e il deprovisioning eseguiti durante la creazione, l'aggiornamento e l'eliminazione dei gruppi di sincronizzazione.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Requisiti e limitazioni

### <a name="general-requirements"></a>Requisiti generali

- Ogni tabella deve avere una chiave primaria. Non modificare il valore della chiave primaria in alcuna riga. Se è necessario modificare un valore della chiave primaria, eliminare la riga e ricrearla con il nuovo valore della chiave primaria.

> [!IMPORTANT]
> La modifica del valore di una chiave primaria esistente comporta il comportamento di errore seguente:
> - I dati tra hub e membro possono essere persi anche se la sincronizzazione non segnala alcun problema.
> - La sincronizzazione può non riuscire perché la tabella di rilevamento ha una riga non esistente dall'origine a causa della modifica della chiave primaria.

- L'isolamento dello snapshot deve essere abilitato sia per i membri di sincronizzazione che per l'hub. Per altre informazioni, vedere [Isolamento dello snapshot in SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- Per usare il collegamento privato con sincronizzazione dati, entrambi i database del membro e dell'hub devono essere ospitati in Azure (stessa o aree diverse), nello stesso tipo di cloud ,ad esempio sia nel cloud pubblico che in entrambi nel cloud per enti pubblici. Inoltre, per usare il collegamento privato, i provider di risorse Microsoft.Network devono essere registrati per le sottoscrizioni che ospitano l'hub e i server membri. Infine, è necessario approvare manualmente il collegamento privato per sincronizzazione dati durante la configurazione di sincronizzazione, all'interno della sezione "Connessioni endpoint privati" nel portale di Azure o tramite PowerShell. Per altre informazioni su come approvare il collegamento privato, vedere [Configurare sincronizzazione dati SQL](./sql-data-sync-sql-server-configure.md). Dopo aver approvato l'endpoint privato gestito dal servizio, tutte le comunicazioni tra il servizio di sincronizzazione e i database membro/hub si verificano tramite il collegamento privato. I gruppi di sincronizzazione esistenti possono essere aggiornati per avere questa funzionalità abilitata.

### <a name="general-limitations"></a>Limitazioni generali

- Una tabella non può avere una colonna Identity che non sia la chiave primaria.
- Una tabella deve avere un indice cluster per usare la sincronizzazione dei dati.
- Una chiave primaria non può avere i tipi di dati seguenti: sql_variant, binary, varbinary, image, xml.
- Usare i tipi di dati che seguono come chiave primaria con la massima cautela, perché supportano solo la precisione al secondo: time, datetime, datetime2, datetimeoffset.
- I nomi degli oggetti (database, tabelle e colonne) non possono contenere il punto di caratteri stampabili (.), la parentesi quadra sinistra ([) o la parentesi quadra chiusa (]).
- Un nome di tabella non può contenere caratteri stampabili: ! " # $ % ' ( ) * + - spazio
- Azure Active Directory'autenticazione non è supportata.
- Se sono presenti tabelle con lo stesso nome ma schema diverso ,ad esempio dbo.customers e sales.customers, solo una delle tabelle può essere aggiunta alla sincronizzazione.
- Le colonne User-Defined tipi di dati non sono supportate
- Lo spostamento di server tra sottoscrizioni diverse non è supportato. 
- Se due chiavi primarie sono diverse solo nel caso in cui , ad esempio Foo e foo, sincronizzazione dati questo scenario non sarà supportato.

#### <a name="unsupported-data-types"></a>Tipi di dati non supportati

- FileStream
- Tipo definito dall'utente (UDT) SQL/CLR
- XMLSchemaCollection (supportato da XML)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Tipi di colonna non supportati

La sincronizzazione dati non sincronizza le colonne di sola lettura o generate dal sistema. Ad esempio:

- Colonne calcolate.
- Colonne generate dal sistema per le tabelle temporali.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitazioni alle dimensioni del servizio e del database

| **Dimensioni**                                                  | **Limite**              | **Soluzione alternativa**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Numero massimo di gruppi di sincronizzazione a cui può appartenere qualsiasi database.       | 5                      |                             |
| Numero massimo di endpoint in un singolo gruppo di sincronizzazione              | 30                     |                             |
| Numero massimo di endpoint locali in un singolo gruppo di sincronizzazione. | 5                      | Creare più gruppi di sincronizzazione |
| Nomi di database, tabella, schema e colonna                       | 50 caratteri per nome |                             |
| Tabelle in un gruppo di sincronizzazione                                          | 500                    | Creare più gruppi di sincronizzazione |
| Colonne in una tabella in un gruppo di sincronizzazione                              | 1000                   |                             |
| Dimensioni delle righe di dati in una tabella                                        | 24 MB                  |                             |

> [!NOTE]
> Se è presente un unico gruppo di sincronizzazione, questo può contenere fino a 30 endpoint. Se esistono più gruppi di sincronizzazione, il numero totale di endpoint in tutti i gruppi di sincronizzazione non può essere maggiore di 30. Se un database appartiene a più gruppi di sincronizzazione, vale come più endpoint.

### <a name="network-requirements"></a>Requisiti di rete

> [!NOTE]
> Se si usa il collegamento privato, questi requisiti di rete non si applicano. 

Quando viene stabilito il gruppo di sincronizzazione, il sincronizzazione dati deve connettersi al database hub. Quando si stabilisce il gruppo di sincronizzazione, il server Azure SQL deve avere la configurazione seguente nelle `Firewalls and virtual networks` impostazioni:

 * *Nega accesso alla rete pubblica* deve essere impostato su *Disattivato.*
 * *Consentire ai servizi e alle* risorse di Azure di accedere a questo server deve essere impostato su Sì oppure è necessario creare regole IP per gli indirizzi IP usati da sincronizzazione dati [servizio](network-access-controls-overview.md#data-sync).

Dopo aver creato ed eseguito il provisioning del gruppo di sincronizzazione, è possibile disabilitare queste impostazioni. L'agente di sincronizzazione si connetterà direttamente al database hub ed è possibile usare le regole IP del [firewall](firewall-configure.md) del server o [gli endpoint privati](private-endpoint-overview.md) per consentire all'agente di accedere al server hub.

> [!NOTE]
> Se si modificano le impostazioni dello schema del gruppo di sincronizzazione, sarà necessario consentire al servizio sincronizzazione dati di accedere di nuovo al server in modo che sia possibile effettuare nuovamente il provisioning del database hub.

## <a name="faq-about-sql-data-sync"></a>Domande frequenti sulla sincronizzazione dati SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto costa il servizio di sincronizzazione dati SQL?

Non sono addebitati costi per il sincronizzazione dati SQL stesso. Tuttavia, si raccolgono comunque gli addebiti per il trasferimento dei dati all'inizio e all'uscita dall'istanza del database SQL. Per altre informazioni, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quali aree supportano la sincronizzazione dei dati?

La sincronizzazione dati SQL è disponibile in tutte le aree geografiche.

### <a name="is-a-sql-database-account-required"></a>È necessario disporre di un account di database SQL?

Sì. È necessario disporre di un account di database SQL per ospitare il database hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>È possibile usare sincronizzazione dati per la sincronizzazione tra SQL Server database

Non direttamente. È tuttavia possibile eseguire la sincronizzazione SQL Server database indirettamente creando un database hub in Azure e quindi aggiungendo i database locali al gruppo di sincronizzazione.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>È possibile usare sincronizzazione dati per la sincronizzazione tra database nel database SQL che appartengono a sottoscrizioni diverse

Sì. È possibile eseguire la sincronizzazione tra database appartenenti a gruppi di risorse appartenenti a sottoscrizioni diverse.

- Se le sottoscrizioni appartengono allo stesso tenant e sono disponibili le autorizzazioni per tutte le sottoscrizioni, è possibile configurare il gruppo di sincronizzazione nel portale di Azure.
- In caso contrario, è necessario usare PowerShell per aggiungere i membri di sincronizzazione che appartengono a sottoscrizioni diverse.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>È possibile usare sincronizzazione dati per la sincronizzazione tra database nel database SQL appartenenti a cloud diversi,ad esempio Cloud pubblico di Azure e Azure China (21Vianet)

Sì. È possibile eseguire la sincronizzazione tra database appartenenti a cloud diversi. È necessario usare PowerShell per aggiungere i membri di sincronizzazione che appartengono alle diverse sottoscrizioni.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>È possibile usare la sincronizzazione dei dati per effettuare il seeding dei dati da un database di produzione a un database vuoto e quindi sincronizzarli?

Sì. Creare manualmente lo schema nel nuovo database effettuando lo scripting dall'originale. Dopo aver creato lo schema, aggiungere le tabelle a un gruppo di sincronizzazione per copiare i dati e mantenerli sincronizzati.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>È opportuno usare la sincronizzazione dei dati SQL per effettuare il backup e ripristino dei database?

Non è consigliabile usare sincronizzazione dati SQL per creare un backup dei dati. Non è possibile eseguire il backup e il ripristino in un momento specifico perché sincronizzazione dati SQL le sincronizzazioni non hanno il controllo delle versioni. Inoltre, sincronizzazione dati SQL non viene eseguita il backup di altri oggetti SQL, ad esempio stored procedure, e non viene eseguita rapidamente l'equivalente di un'operazione di ripristino.

Per una tecnica di backup consigliata, vedere [Copiare un database in database SQL di Azure](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Con la sincronizzazione dei dati si possono sincronizzare tabelle e colonne crittografate?

- Se un database usa Always Encrypted, è possibile sincronizzare solo le tabelle e colonne *non* crittografate. Non è possibile sincronizzare le colonne crittografate, perché la sincronizzazione dei dati non può decrittografare i dati.
- Se una colonna usa la crittografia a livello di colonna (CLE), è possibile sincronizzare la colonna, purché le dimensioni delle righe siano minori rispetto alle dimensioni massime di 24 MB. La sincronizzazione dei dati considera la colonna crittografata in base alla chiave (CLE) come normali dati binari. Per decrittografare i dati in altri membri di sincronizzazione, è necessario avere lo stesso certificato.

### <a name="is-collation-supported-in-sql-data-sync"></a>Le regole di confronto sono supportate nella sincronizzazione dei dati SQL?

Sì. La sincronizzazione dati SQL supporta le regole di confronto negli scenari seguenti:

- Se le tabelle dello schema di sincronizzazione selezionate non sono già presenti nell'hub o nei database membri, quando si distribuisce il gruppo di sincronizzazione, il servizio crea automaticamente le tabelle e le colonne corrispondenti con le impostazioni delle regole di confronto selezionate nei database di destinazione vuoti.
- Se le tabelle da sincronizzare sono già presenti sia nel database hub che nel database membro, la sincronizzazione dati SQL richiede che le colonne chiavi primarie abbiano le stesse regole di confronto in entrambi i database hub e membro per una distribuzione corretta del gruppo di sincronizzazione. Per le colonne diverse dalle colonne chiavi primarie non sono previste restrizioni relative alle regole di confronto.

### <a name="is-federation-supported-in-sql-data-sync"></a>La federazione è supportata nella sincronizzazione dei dati SQL?

Il database radice di federazione può essere usato nel servizio di sincronizzazione dati SQL senza alcuna limitazione. Non è possibile aggiungere l'endpoint del database federato alla versione corrente di sincronizzazione dati SQL.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>È possibile usare sincronizzazione dati per sincronizzare i dati esportati da Dynamics 365 usando la funzionalità BRING Your Own Database (BYOD) ?

La funzionalità bring your database di Dynamics 365 consente agli amministratori di esportare entità di dati dall'applicazione nel proprio database SQL Microsoft Azure database SQL. sincronizzazione dati può essere usato per sincronizzare questi dati in altri database se i dati vengono esportati tramite **push incrementale** (il push completo non è supportato) e l'abilitazione **dei trigger** nel database di destinazione è impostata su **sì.**

## <a name="next-steps"></a>Passaggi successivi

### <a name="update-the-schema-of-a-synced-database"></a>Aggiornare lo schema di un database sincronizzato

È necessario aggiornare lo schema di un database in un gruppo di sincronizzazione? Le modifiche dello schema non vengono replicate automaticamente. Per alcune soluzioni, vedere gli articoli seguenti:

- [Automatizzare la replica delle modifiche dello schema con sincronizzazione dati SQL in Azure](./sql-data-sync-update-sync-schema.md)
- [Usare PowerShell per aggiornare lo schema di sincronizzazione in un gruppo di sincronizzazione esistente](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Monitorare e risolvere i problemi

È sincronizzazione dati SQL come previsto? Per monitorare l'attività e risolvere i problemi, vedere gli articoli seguenti:

- [Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure](./monitor-tune-overview.md)
- [Risolvere i problemi della sincronizzazione dati SQL di Azure](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Ulteriori informazioni sul database SQL di Azure

Per altre informazioni sui database SQL di Azure, vedere gli articoli seguenti:

- [Panoramica del database SQL](sql-database-paas-overview.md)
- [Gestione del ciclo di vita del database](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
