---
title: Cenni preliminari sulla sicurezza
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Informazioni sulla sicurezza in database SQL di Azure e Istanza gestita di SQL di Azure, incluso il modo in cui differisce da SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 39012e1f5a0282da7dda6bab216719e31fdc5061
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752175"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Panoramica delle funzionalità di database SQL di Azure sql Istanza gestita sicurezza
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Questo articolo illustra le nozioni di base sulla protezione del livello dati di un'applicazione [usando](sql-database-paas-overview.md)database SQL di Azure , [Istanza gestita di SQL di Azure](../managed-instance/sql-managed-instance-paas-overview.md)e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). La strategia di sicurezza descritta segue l'approccio a più livelli di difesa avanzata come illustrato nell'immagine seguente e si sposta dall'esterno in:

![Diagramma della difesa a più livelli in modo approfondito. I dati dei clienti sono racchiusi in livelli di sicurezza di rete, gestione degli accessi e protezione delle minacce e delle informazioni.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Sicurezza di rete

database SQL di Microsoft Azure, SQL Istanza gestita e Azure Synapse Analytics un servizio di database relazionale per le applicazioni cloud e aziendali. Per proteggere i dati dei clienti, i firewall impediscono l'accesso alla rete al server fino a quando l'accesso non viene concesso in modo esplicito in base all'indirizzo IP o all'origine del traffico di rete virtuale di Azure.

### <a name="ip-firewall-rules"></a>Regole del firewall IP

Le regole del firewall IP concedono l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta. Per altre informazioni, vedere [Panoramica delle regole database SQL di Azure e Azure Synapse Analytics firewall.](firewall-configure.md)

### <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale

[Gli endpoint del servizio di](../../virtual-network/virtual-network-service-endpoints-overview.md) rete virtuale estendono la connettività di rete virtuale sul backbone di Azure e database SQL di Azure identificare la subnet di rete virtuale da cui ha origine il traffico. Per consentire al traffico di raggiungere il database SQL di Azure, usare i [tag di servizio](../../virtual-network/network-security-groups-overview.md) SQL per consentire il traffico in uscita tramite gruppi di sicurezza di rete.

Le [regole di rete virtuale](vnet-service-endpoint-rule-overview.md) consentono al database SQL di Azure di accettare solo le comunicazioni inviate da subnet specifiche all'interno di una rete virtuale.

> [!NOTE]
> Il controllo dell'accesso con le *regole* del firewall non si applica **a SQL Istanza gestita**. Per altre informazioni sulla configurazione di rete necessaria, vedere [Connessione a un'istanza gestita](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Gestione degli accessi

> [!IMPORTANT]
> La gestione dei database e dei server in Azure è controllata dalle assegnazioni di ruolo dell'account del portale utenti. Per altre informazioni su questo articolo, vedere Controllo [degli accessi in](../../role-based-access-control/overview.md)base al ruolo di Azure nella portale di Azure .

### <a name="authentication"></a>Authentication

L'autenticazione è il processo atto a dimostrare che l'utente sia effettivamente chi dichiara di essere. database SQL di Azure e SQL Istanza gestita supportano due tipi di autenticazione:

- **Autenticazione SQL**:

    L'autenticazione SQL si riferisce all'autenticazione di un utente durante la connessione a database SQL di Azure o Istanza gestita di SQL di Azure tramite nome utente e password. Quando **si crea il server,** è necessario specificare un account di accesso amministratore del server con nome utente e password. Usando queste credenziali, un amministratore **del server** può eseguire l'autenticazione a qualsiasi database in tale server o istanza come proprietario del database. In seguito, l'amministratore del server può creare altri utenti e accessi SQL, che consentono agli utenti di connettersi tramite nome utente e password.

- **Azure Active Directory autenticazione :**

    Azure Active Directory'autenticazione è un meccanismo di connessione [a database SQL di Azure](sql-database-paas-overview.md), [Istanza gestita di SQL di Azure](../managed-instance/sql-managed-instance-paas-overview.md) [e Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tramite identità in Azure Active Directory (Azure AD). Azure AD'autenticazione consente agli amministratori di gestire centralmente le identità e le autorizzazioni degli utenti del database insieme ad altri servizi di Azure in un'unica posizione centrale. Ciò include la riduzione dell'archiviazione di password e abilita i criteri centralizzati di rotazione delle password.

     Occorre creare un amministratore del server denominato **Amministratore di Active Directory Domain Services** per usare l'autenticazione di Azure AD con il database SQL. Per altre informazioni, vedere [Connessione al database SQL tramite l'autenticazione di Azure Active Directory](authentication-aad-overview.md). L'autenticazione di Azure AD supporta sia gli account gestiti che quelli federati. Gli account federati supportano gli utenti di Windows e gruppi per un dominio utente federato con Azure AD.

    Altre opzioni di autenticazione di Azure AD disponibili sono le connessioni [Autenticazione universale di Active Directory per SQL Server Management Studio](authentication-mfa-ssms-overview.md) che includono [Autenticazione a più fattori](../../active-directory/authentication/concept-mfa-howitworks.md) e [ Accesso condizionale](conditional-access-configure.md).

> [!IMPORTANT]
> La gestione dei database e dei server in Azure è controllata dalle assegnazioni di ruolo dell'account del portale utenti. Per altre informazioni su questo articolo, vedere Controllo [degli accessi in base al ruolo di Azure in portale di Azure](../../role-based-access-control/overview.md). Il controllo dell'accesso con le regole del firewall *non* si applica **a SQL Istanza gestita**. Per altre informazioni sulla configurazione di rete [necessaria,](../managed-instance/connect-application-instance.md) vedere l'articolo seguente sulla connessione a un'istanza gestita.

## <a name="authorization"></a>Autorizzazione

L'autorizzazione si riferisce alle autorizzazioni assegnate a un utente all'interno di un database in database SQL di Azure o Istanza gestita di SQL di Azure e determina le attività consentite all'utente. Le autorizzazioni vengono controllate aggiungendo account utente ai ruoli del [database](/sql/relational-databases/security/authentication-access/database-level-roles) e assegnando autorizzazioni a livello di database a tali ruoli o concedendo all'utente determinate autorizzazioni a [livello di oggetto.](/sql/relational-databases/security/permissions-database-engine) Per altre informazioni, vedere [Accessi e utenti](logins-create-manage.md)

Come procedura consigliata, creare ruoli personalizzati quando necessario. Aggiungere utenti al ruolo con i privilegi minimi necessari per eseguire la funzione. Non assegnare le autorizzazioni direttamente agli utenti. L'account amministratore del server è membro del ruolo predefinito db_owner, che dispone di autorizzazioni estese e deve essere concesso solo a pochi utenti con compiti amministrativi. Per le applicazioni, usare [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) per specificare il contesto di esecuzione del modulo chiamato o usare [i ruoli applicazione](/sql/relational-databases/security/authentication-access/application-roles) con autorizzazioni limitate. Questa procedura garantisce che l'applicazione che si connette al database abbia i privilegi minimi necessari per l'applicazione. L'applicazione di queste procedure consigliate favorisce anche la separazione dei compiti.

### <a name="row-level-security"></a>Sicurezza a livello di riga

La sicurezza a livello di riga consente ai clienti di controllare l'accesso alle righe in una tabella del database in base alle caratteristiche dell'utente che esegue una query (ad esempio l'appartenenza al gruppo o il contesto di esecuzione). Row-Level sicurezza basata su etichette può essere usato anche per implementare concetti di sicurezza personalizzati basati su etichette. Per altre informazioni, vedere [Sicurezza a livello di riga.](/sql/relational-databases/security/row-level-security)

![Diagramma che mostra Row-Level sicurezza protegge singole righe di un database SQL dall'accesso da parte degli utenti tramite un'app client.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Protezione dalle minacce

Il database SQL e SQL Istanza gestita proteggere i dati dei clienti fornendo funzionalità di controllo e rilevamento delle minacce.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Controllo SQL nei log Monitoraggio di Azure e in Hub eventi

Il database SQL e SQL Istanza gestita controllo tiene traccia delle attività del database e consente di mantenere la conformità agli standard di sicurezza registrando gli eventi del database in un log di controllo in un account di archiviazione di Azure di proprietà del cliente. Il servizio di controllo consente agli utenti di monitorare le attività del database in corso e di analizzare ed esaminare l'attività cronologica per identificare potenziali minacce o uso improprio sospetto e violazioni della sicurezza. Per altre informazioni, vedere Introduzione al controllo [del database SQL.](../../azure-sql/database/auditing-overview.md)  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Advanced Threat Protection analizza i log per rilevare comportamenti insoliti e tentativi potenzialmente dannosi di accedere o sfruttare i database. Vengono creati avvisi per attività sospette come SQL injection, potenziali infiltrazioni di dati e attacchi di forza bruta o per anomalie nei modelli di accesso per rilevare le escalation dei privilegi e l'uso delle credenziali violate. Gli avvisi vengono visualizzati dal  [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), in cui vengono forniti i dettagli delle attività sospette e vengono forniti consigli per un'ulteriore analisi e azioni per attenuare la minaccia. Advanced Threat Protection può essere abilitato per ogni server a pagamento aggiuntivo. Per altre informazioni, vedere [Introduzione a Sql Database Advanced Threat Protection.](threat-detection-configure.md)

![Diagramma che mostra il rilevamento delle minacce SQL che monitora l'accesso al database SQL per un'app Web da un utente malintenzionato esterno e da utenti interni malintenzionati.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Crittografia e protezione delle informazioni

### <a name="transport-layer-security-encryption-in-transit"></a>Transport Layer Security (crittografia in transito)

Database SQL, sql Istanza gestita e Azure Synapse Analytics dati dei clienti protetti crittografando i dati in movimento [con Transport Layer Security (TLS).](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

Database SQL, SQL Istanza gestita e Azure Synapse Analytics la crittografia (SSL/TLS) in qualsiasi momento per tutte le connessioni. In questo modo tutti i dati vengono crittografati "in transito" tra il client e il server indipendentemente dall'impostazione di **Encrypt** o **TrustServerCertificate** nella stringa di connessione.

Come procedura consigliata, nella stringa di connessione usata dall'applicazione è __ consigliabile specificare una connessione crittografata e non considerare attendibile il certificato del server. In questo modo l'applicazione deve verificare il certificato del server e quindi impedisce che l'applicazione sia vulnerabile agli attacchi di tipo man in the middle.

Quando si usa il driver ADO.NET, ad esempio, questa operazione viene eseguita tramite **Encrypt=True** e **TrustServerCertificate=False.** Se si ottiene la stringa di connessione dal portale di Azure, le impostazioni saranno corrette.

> [!IMPORTANT]
> Si noti che alcuni driver non Microsoft potrebbero non usare TLS per impostazione predefinita o basarsi su una versione precedente di TLS (<1.2) per funzionare. In questo caso il server consente comunque di connettersi al database. Tuttavia, è consigliabile valutare i rischi per la sicurezza derivanti dalla possibilità che tali driver e applicazioni si connettono al database SQL, soprattutto se si archiviano dati sensibili.
>
> Per altre informazioni su TLS e sulla connettività, vedere [Considerazioni su TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (TDE - crittografia inattiva)

[Transparent Data Encryption (TDE)](transparent-data-encryption-tde-overview.md) per database SQL, SQL Istanza gestita e Azure Synapse Analytics aggiunge un livello di sicurezza che consente di proteggere i dati in stato di inquieto da accessi non autorizzati o offline a file o backup non elaborati. Gli scenari comuni includono data center furto o eliminazione non protetta di hardware o supporti, ad esempio unità disco e nastri di backup.TDE crittografa l'intero database usando un algoritmo di crittografia AES, che non richiede agli sviluppatori di applicazioni di apportare modifiche alle applicazioni esistenti.

In Azure tutti i database appena creati vengono crittografati per impostazione predefinita e la chiave di crittografia del database è protetta da un certificato server predefinito.  La manutenzione e la rotazione dei certificati vengono gestite dal servizio e non richiedono alcun input da parte dell'utente. I clienti che preferiscono assumere il controllo delle chiavi di crittografia possono gestire le chiavi presenti in [Azure Key Vault](../../key-vault/general/security-overview.md).

### <a name="key-management-with-azure-key-vault"></a>Gestione delle chiavi con Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-overview.md) (BYOK) per [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) consente ai clienti di assumere la proprietà della gestione e della rotazione delle chiavi [usando Azure Key Vault](../../key-vault/general/security-overview.md), il sistema di gestione delle chiavi esterne basato sul cloud di Azure. Se viene revocato l'accesso del database per l'insieme di credenziali delle chiavi, il database non può essere decrittografato e letto in memoria. Azure Key Vault offre una piattaforma di gestione centralizzata delle chiavi, sfrutta i moduli di protezione hardware (HSM) accuratamente monitorati e consente la separazione dei compiti tra la gestione delle chiavi e i dati per contribuire a rispettare i requisiti di conformità alle norme di sicurezza.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (crittografia in uso)

![Diagramma che illustra le nozioni di base Always Encrypted funzionalità. Un database SQL con blocco è accessibile solo da un'app contenente una chiave.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) è una funzionalità progettata per proteggere da accessi i dati sensibili archiviati in colonne specifiche del database (ad esempio, i numeri delle carte di credito o i numeri di identificazione nazionale o dati sulla base della _necessità di conoscere_). Sono inclusi gli amministratori del database o altri utenti con privilegi che sono autorizzati ad accedere al database per eseguire attività di gestione, ma che non hanno esigenza di accedere a dati particolari nelle colonne crittografate. I dati vengono sempre crittografati, ossia i dati crittografati vengono decrittografati solo per l'elaborazione da parte di applicazioni client con accesso alla chiave di crittografia. La chiave di crittografia non viene mai esposta al database SQL o Istanza gestita SQL e può essere archiviata nell'archivio certificati [di Windows](always-encrypted-certificate-store-configure.md) o in [Azure Key Vault](always-encrypted-azure-key-vault-configure.md).

### <a name="dynamic-data-masking"></a>Maschera dati dinamica

![Diagramma che mostra la maschera dati dinamica. Un'app aziendale invia i dati a un database SQL che maschera i dati prima di inviarli all'app aziendale.](./media/security-overview/azure-database-ddm.png)

La maschera dati dinamica limita l'esposizione dei dati sensibili nascondendoli agli utenti senza privilegi. La maschera dati dinamica individua automaticamente i dati potenzialmente sensibili in database SQL di Azure e SQL Istanza gestita e fornisce raccomandazioni utili per mascherare questi campi, con un impatto minimo sul livello dell'applicazione. Questa funzionalità si basa sull'offuscamento dei dati sensibili nel set dei risultati di una query su campi designati del database, mentre i dati del database non subiscono modifiche. Per altre informazioni, vedere Introduzione al database SQL e [a SQL Istanza gestita maschera dati dinamica](dynamic-data-masking-overview.md).

## <a name="security-management"></a>Gestione della sicurezza

### <a name="vulnerability-assessment"></a>Valutazione della vulnerabilità

[Valutazione della vulnerabilità](sql-vulnerability-assessment.md) è un servizio semplice da configurare che consente di individuare, tenere traccia e risolvere potenziali vulnerabilità del database, con l'obiettivo di migliorare in modo proattivo la sicurezza generale del database. La valutazione della vulnerabilità fa parte dell'offerta Azure Defender per SQL, che è un pacchetto unificato per funzionalità di sicurezza SQL avanzate. La valutazione della vulnerabilità è accessibile e gestita tramite il portale Azure Defender per SQL.

### <a name="data-discovery-and-classification"></a>Individuazione e classificazione dei dati

L'individuazione e la classificazione dei dati (attualmente in anteprima) offre funzionalità avanzate integrate in database SQL di Azure e SQL Istanza gestita per l'individuazione, la classificazione, l'etichettatura e la protezione dei dati sensibili nei database. L'individuazione e la classificazione dei dati più sensibili (dati commerciali e finanziari, dati relativi all'assistenza sanitaria, informazioni personali e così via) possono svolgere un ruolo fondamentale per il livello di protezione delle informazioni aziendali. Individuazione dati e classificazione può svolgere la funzione di infrastruttura per:

- Vari scenari di sicurezza, ad esempio monitoraggio (controllo) e invio di avvisi sulle anomalie di accesso a dati sensibili.
- Controllare l'accesso ai database che contengono dati molto sensibili e rafforzarne la sicurezza.
- Contribuire a soddisfare gli standard e i requisiti di conformità alle normative sulla privacy dei dati.

Per altre informazioni, vedere [Introduzione all'individuazione e alla classificazione dei dati.](data-discovery-and-classification-overview.md)

### <a name="compliance"></a>Conformità

Oltre alle caratteristiche e alle funzionalità sopra descritte, che consentono all'applicazione di soddisfare vari requisiti di sicurezza, il database SQL di Azure è inoltre sottoposto a controlli regolari ed ha ottenuto la certificazione per diversi standard di conformità. Per altre informazioni, vedere [l'Microsoft Azure Trust Center](https://www.microsoft.com/trust-center/compliance/compliance-overview) in cui è possibile trovare l'elenco più attuale di certificazioni di conformità al database SQL.

## <a name="next-steps"></a>Passaggi successivi

- Per una descrizione dell'uso di account di accesso, account utente, ruoli del database e autorizzazioni nel database SQL e sql Istanza gestita, vedere Gestire account di accesso e [account utente](logins-create-manage.md).
- Per una descrizione del controllo del database, vedere [Controllo](../../azure-sql/database/auditing-overview.md)di .
- Per una descrizione del rilevamento delle minacce, vedere [Rilevamento delle minacce](threat-detection-configure.md).