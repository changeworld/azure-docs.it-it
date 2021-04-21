---
title: Transparent Data Encryption
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Panoramica di Transparent Data Encryption per database SQL di Azure, Istanza gestita di SQL di Azure e Azure Synapse Analytics. Il documento illustra i vantaggi e le opzioni per la configurazione, che include crittografia TDE gestita dal servizio e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: f93d65b4d10c1a8454a8e24b5cb081dae4d6943e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812805"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Transparent Data Encryption per database SQL, sql Istanza gestita e Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) consente di proteggere database SQL di Azure, Istanza gestita di SQL di Azure e Azure Synapse Analytics dalla minaccia di attività offline dannose crittografando i dati in pausa. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Per impostazione predefinita, TDE è abilitato per tutti i database SQL appena distribuiti e deve essere abilitato manualmente per i database meno recenti di database SQL di Azure, Istanza gestita di SQL di Azure. La funzionalità TDE deve essere abilitata manualmente per Azure Synapse Analytics.

TDE esegue in tempo reale la crittografia e decrittografia dell'I/O dei dati a livello di pagina. Ogni pagina viene decrittografata quando letta in memoria e quindi crittografata prima di essere scritta su disco. TDE crittografa l'archiviazione di un intero database usando una chiave simmetrica denominata chiave di crittografia del database (DEK). All'avvio del database, la chiave DEK crittografata viene decrittografata e quindi usata per decrittografare e ricrittografare i file di database nel SQL Server del motore di database. La dek è protetta dalla protezione TDE. La protezione TDE è un certificato gestito dal servizio (Transparent Data Encryption gestita dal servizio) o una chiave asimmetrica archiviata in [Azure Key Vault](../../key-vault/general/security-features.md) (Transparent Data Encryption gestita dal cliente).

Per database SQL di Azure e Azure Synapse, la protezione TDE è impostata a livello di [server](logical-servers.md) e viene ereditata da tutti i database associati a tale server. Per l'istanza gestita di database SQL di Azure, la protezione TDE è impostata a livello di istanza e viene ereditata da tutti i database crittografati nell'istanza. Salvo diversa indicazione, in questo documento il termine *server* fa riferimento sia al server che all'istanza.

> [!IMPORTANT]
> Tutti i database appena creati nel database SQL vengono crittografati per impostazione predefinita usando Transparent Data Encryption gestita dal servizio. I database SQL esistenti creati prima di maggio 2017 e i database SQL creati tramite ripristino, replica geografica e copia del database non sono crittografati per impostazione predefinita. I database SQL Istanza gestita creati prima di febbraio 2019 non vengono crittografati per impostazione predefinita. I Istanza gestita SQL creati tramite il ripristino ereditano lo stato della crittografia dall'origine.

> [!NOTE]
> Non è possibile usare TDE per crittografare i database di sistema, ad esempio il database **master,** database SQL di Azure e Istanza gestita di SQL di Azure. Il database **master** contiene gli oggetti necessari a eseguire le operazioni di Transparent Data Encryption nei database utente. È consigliabile non archiviare dati sensibili nei database di sistema. [È in corso](transparent-data-encryption-byok-overview.md#doubleencryption) l'implementazione della crittografia dell'infrastruttura che crittografa i database di sistema, incluso il database master. 

## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption gestita dal servizio

In Azure l'impostazione predefinita per TDE è che la chiave DEK è protetta da un certificato server predefinito. Il certificato del server predefinito è univoco per ogni server e l'algoritmo di crittografia usato è AES 256. Se un database si trova in una relazione di replica geografica, sia il database primario che quello secondario sono protetti dalla chiave del server padre del database primario. Se due database sono connessi allo stesso server, condividono anche lo stesso certificato predefinito. Microsoft ruota automaticamente questi certificati in conformità con i criteri di sicurezza interni e la chiave radice è protetta da un archivio segreti interno di Microsoft. I clienti possono verificare la conformità del database SQL e di SQL Istanza gestita ai criteri di sicurezza interni nei report di controllo di terze parti indipendenti disponibili in [Microsoft Trust Center.](https://servicetrust.microsoft.com/)

Microsoft inoltre sposta e gestisce le chiavi senza problemi in base alle esigenze per la replica geografica e ne esegue il ripristino.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparent Data Encryption gestita dal cliente: Bring Your Own Key

La funzionalità TDE gestita dal cliente viene definita anche supporto Bring Your Own Key (BYOK) per TDE. In questo scenario, la protezione TDE che crittografa la chiave DEK è una chiave asimmetrica gestita dal cliente, archiviata in un Azure Key Vault di proprietà del cliente e gestito (sistema di gestione delle chiavi esterne basato sul cloud di Azure) e non lascia mai l'insieme di credenziali delle chiavi. La protezione TDE [](../../key-vault/keys/hsm-protected-keys.md) può essere generata dall'insieme di credenziali delle chiavi o trasferita all'insieme di credenziali delle chiavi da un dispositivo HSM (Hardware Security Module) locale. Per decrittografare e Istanza gestita crittografare la chiave DEK, è Azure Synapse database SQL, sql e Azure Synapse all'insieme di credenziali delle chiavi di proprietà del cliente. Se vengono revocate le autorizzazioni del server per l'insieme di credenziali delle chiavi, un database non sarà accessibile e tutti i dati verranno crittografati

Grazie all'integrazione di TDE con Azure Key Vault, gli utenti possono controllare le attività di gestione delle chiavi, tra cui le rotazioni, i backup e le autorizzazioni dell'insieme di credenziali, nonché abilitare il controllo o il reporting per tutte le protezioni TDE usando Azure Key Vault. Key Vault la gestione centralizzata delle chiavi, sfrutta i sistemi HMS strettamente monitorati e consente la separazione dei compiti tra la gestione delle chiavi e dei dati per garantire la conformità ai criteri di sicurezza.
Per altre informazioni sulla modalità BYOK per database SQL di Azure e Azure Synapse, vedere [Transparent Data Encryption con l'Azure Key Vault dati.](transparent-data-encryption-byok-overview.md)

Per iniziare a usare TDE con Azure Key Vault, vedere la guida alle procedura per attivare [Transparent Data Encryption](transparent-data-encryption-byok-configure.md)usando la propria chiave Key Vault .

## <a name="move-a-transparent-data-encryption-protected-database"></a>Spostare un database protetto con TDE

Non è necessario decrittografare i database per le operazioni all'interno di Azure. Le impostazioni di Transparent Data Encryption nel database di origine o nel database primario vengono ereditate in modo trasparente nel database di destinazione. Le operazioni incluse fanno riferimento a:

- Ripristino geografico
- Ripristino temporizzato self-service
- Ripristino di un database eliminato
- Replica geografica attiva
- Creazione di una copia del database
- Ripristino di un file di backup per Istanza gestita di database SQL di Azure

> [!IMPORTANT]
> L'esecuzione manuale del backup COPY-ONLY di un database crittografato da TDE gestita dal servizio non è supportata in Istanza gestita di SQL di Azure, poiché il certificato usato per la crittografia non è accessibile. Usare la funzionalità di ripristino tempormente utile per spostare questo tipo di database in un altro Istanza gestita SQL o passare alla chiave gestita dal cliente.

Quando si esporta un database protetto da TDE, il contenuto esportato del database non viene crittografato. Questo contenuto esportato viene archiviato in file BACPAC non crittografati. Assicurarsi di proteggere i file BACPAC in modo appropriato e abilitare TDE al termine dell'importazione del nuovo database.

Ad esempio, se il file BACPAC viene esportato da un'istanza di SQL Server, il contenuto importato del nuovo database non viene crittografato automaticamente. Analogamente, se il file BACPAC viene importato in un'istanza SQL Server, anche il nuovo database non viene crittografato automaticamente.

L'unica eccezione si verifica quando si esporta un database da e verso il database SQL. TDE è abilitato nel nuovo database, ma il file BACPAC non è ancora crittografato.

## <a name="manage-transparent-data-encryption"></a>Gestire Transparent Data Encryption

# <a name="the-azure-portal"></a>[Il portale di Azure](#tab/azure-portal)

Gestire TDE nel portale di Azure.

Per configurare TDE tramite il portale di Azure, è necessario essere connessi come proprietario, collaboratore o gestore della sicurezza SQL di Azure.

Abilitare e disabilitare TDE a livello di database. Ad Istanza gestita di SQL di Azure usare Transact-SQL (T-SQL) per attivare e disattivare TDE in un database. Per database SQL di Azure e Azure Synapse, è possibile gestire TDE per il database nel [portale di Azure](https://portal.azure.com) dopo aver eseguito l'accesso con l'account amministratore o collaboratore di Azure. Le impostazioni di TDE si trovano nel database utente. Per impostazione predefinita, viene usata TDE gestita dal servizio. Viene generato automaticamente un certificato TDE per il server che contiene il database.

![Transparent Data Encryption gestita dal servizio](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

La chiave master TDE, nota come protezione TDE, viene impostata a livello di server o di istanza. Per usare TDE con supporto BYOK e proteggere i database con una chiave Key Vault, aprire le impostazioni TDE nel server.

![TDE con supporto Bring Your Own Key](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gestire TDE tramite PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager PowerShell è ancora supportato, ma tutto lo sviluppo futuro è per il modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Per configurare TDE tramite PowerShell è necessario essere connessi come proprietario, collaboratore o Gestore Sicurezza SQL di Azure.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlet per database SQL di Azure e Azure Synapse

Usare i cmdlet seguenti per database SQL di Azure e Azure Synapse:

| Cmdlet | Descrizione |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Abilita o disabilita transparent data encryption per un database.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Ottiene lo stato transparent data encryption per un database. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Controlla lo stato di crittografia per un database. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Aggiunge una Key Vault chiave a un server. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Ottiene le Key Vault per un server  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Imposta la protezione transparent data encryption per un server. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Ottiene la protezione TDE |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Rimuove una Key Vault chiave da un server. |
|  | |

> [!IMPORTANT]
> Ad Istanza gestita di SQL di Azure, usare il comando T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) per attivare e disattivare TDE a livello di database e controllare lo script [di PowerShell](transparent-data-encryption-byok-configure.md) di esempio per gestire TDE a livello di istanza.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Gestire TDE tramite Transact-SQL.

Connettere il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.

| Comando | Descrizione |
| --- | --- |
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF consente di crittografare o decrittografare un database |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di un database e delle chiavi di crittografia associate al database |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di ogni nodo Azure Synapse e delle chiavi di crittografia del database associate |
|  | |

Non è possibile passare la protezione TDE a una chiave da Key Vault usando Transact-SQL. Usare PowerShell o il portale Azure.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

Gestire TDE usando l'API REST.

Per configurare TDE tramite l'API REST, è necessario essere connessi come proprietario, collaboratore o gestore della sicurezza SQL di Azure.
Usare il set di comandi seguente per database SQL di Azure e Azure Synapse:

| Comando | Descrizione |
| --- | --- |
|[Crea o aggiorna il server](/rest/api/sql/servers/createorupdate)|Aggiunge un'Azure Active Directory a un server. (usato per concedere l'accesso Key Vault)|
|[Crea o aggiorna la chiave del server](/rest/api/sql/serverkeys/createorupdate)|Aggiunge una Key Vault chiave a un server.|
|[Elimina la chiave del server](/rest/api/sql/serverkeys/delete)|Rimuove una Key Vault chiave da un server. |
|[Ottieni la chiave del server](/rest/api/sql/serverkeys/get)|Ottiene una chiave Key Vault specifica da un server.|
|[Elenca le chiavi del server dal server](/rest/api/sql/serverkeys/listbyserver)|Ottiene le Key Vault per un server. |
|[Crea o aggiorna la protezione della crittografia](/rest/api/sql/encryptionprotectors/createorupdate)|Imposta la protezione TDE per un server.|
|[Ottieni la protezione della crittografia](/rest/api/sql/encryptionprotectors/get)|Ottiene la protezione TDE per un server.|
|[Elenca le protezioni di crittografia dal server](/rest/api/sql/encryptionprotectors/listbyserver)|Ottiene le protezione TDE per un server. |
|[Crea o aggiorna la configurazione di Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/createorupdate)|Abilita o disabilita TDE per un database.|
|[Ottieni la configurazione di Transparent Data Encryption](/rest/api/sql/transparentdataencryptions/get)|Ottiene la configurazione di TDE per un database.|
|[Elenca i risultati della configurazione di Transparent Data Encryption](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Ottiene il risultato della crittografia per un database.|

## <a name="see-also"></a>Vedere anche

- SQL Server eseguito in una macchina virtuale di Azure può anche usare una chiave asimmetrica di Key Vault. I passaggi della configurazione sono diversi rispetto all'uso di una chiave asimmetrica nel database SQL e in Istanza gestita di database SQL di Azure. Per altre informazioni, vedere [Extensible Key Management tramite Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Per una descrizione generale di TDE, vedere [Transparent Data Encryption.](/sql/relational-databases/security/encryption/transparent-data-encryption)
- Per altre informazioni su TDE con supporto BYOK per database SQL di Azure, Istanza gestita di SQL di Azure e Azure Synapse, vedere [Transparent Data Encryption con](transparent-data-encryption-byok-overview.md)Bring Your Own Key supporto .
- Per iniziare a usare TDE con Bring Your Own Key, vedere la guida alle procedura, Attivare [Transparent Data Encryption](transparent-data-encryption-byok-configure.md)usando la propria chiave Key Vault .
- Per altre informazioni sui Key Vault, vedere Proteggere [l'accesso a un insieme di credenziali delle chiavi.](../../key-vault/general/security-features.md)