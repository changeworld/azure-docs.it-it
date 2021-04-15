---
title: Copiare e trasformare i dati in database SQL di Azure
description: Informazioni su come copiare dati da e verso database SQL di Azure e trasformare i dati in database SQL di Azure usando Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 75615b4bb8773d0c0b8f72278e5598462c779ceb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365230"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Copiare e trasformare i dati in database SQL di Azure usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in uso:"]
>
> - [Versione 1](v1/data-factory-azure-sql-connector.md)
> - [Versione corrente](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso database SQL di Azure e usare Flusso di dati per trasformare i dati in database SQL di Azure. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo database SQL di Azure connettore è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con la tabella della [matrice di origine/sink supportati](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

Ad attività Copy, questo connettore database SQL di Azure supporta queste funzioni:

- Copia dei dati usando l'autenticazione SQL e Azure Active Directory (Azure AD) Autenticazione del token dell'applicazione con un'entità servizio o identità gestite per le risorse di Azure.
- Come origine, il recupero dei dati tramite una query SQL o un stored procedure. È anche possibile scegliere di eseguire la copia parallela da un'origine database SQL di Azure, vedere la sezione Copia parallela dal [database SQL](#parallel-copy-from-sql-database) per informazioni dettagliate.
- Come sink, creare automaticamente la tabella di destinazione se non esiste in base allo schema di origine; Aggiunta di dati a una tabella o chiamata di un stored procedure logica personalizzata durante la copia.

Se si usa un database SQL di Azure [serverless](../azure-sql/database/serverless-tier-overview.md), tenere presente che quando il server è sospeso, l'esecuzione dell'attività ha esito negativo anziché attendere che la ripresa automatica sia pronta. È possibile aggiungere un nuovo tentativo di attività o concatenare attività aggiuntive per assicurarsi che il server sia in tempo reale dopo l'esecuzione effettiva.

>[!NOTE]
> database SQL di Azure [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) non è attualmente supportato da questo connettore. Per risolvere il problema, è possibile usare un [connettore ODBC](connector-odbc.md) generico e un driver ODBC SQL Server tramite un runtime di integrazione self-hosted. Per altre informazioni, [vedere la Always Encrypted.](#using-always-encrypted) 

> [!IMPORTANT]
> Se si copiano dati usando il runtime di integrazione di Azure, configurare una [regola del firewall](../azure-sql/database/firewall-configure.md) a livello di server in modo che i servizi di Azure possano accedere al server.
> Se si copiano dati usando un runtime di integrazione self-hosted, configurare il firewall per consentire l'intervallo IP appropriato. Questo intervallo include l'indirizzo IP del computer usato per connettersi database SQL di Azure.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti forniscono informazioni dettagliate sulle proprietà usate per definire Azure Data Factory entità specifiche di un connettore database SQL di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per un servizio collegato al database SQL di Azure sono supportate queste proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureSqlDatabase**. | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi all'istanza database SQL di Azure per la **proprietà connectionString.** <br/>È anche possibile inserire una password o una chiave dell'entità servizio in Azure Key Vault. Se si tratta dell'autenticazione SQL, estrarre `password` la configurazione dalla stringa di connessione. Per altre informazioni, vedere l'esempio JSON che segue la tabella e [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'Azure AD con un'entità servizio |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo **come SecureString** per archiviarlo in modo sicuro in Azure Data Factory o fare [riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'Azure AD con un'entità servizio |
| tenant | Specificare le informazioni sul tenant, ad esempio il nome di dominio o l'ID tenant, in cui risiede l'applicazione. Recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'Azure AD con un'entità servizio |
| azureCloudType | Per l'autenticazione dell'entità servizio, specificare il tipo di ambiente cloud di Azure in cui è Azure AD'applicazione. <br/> I valori **consentiti sono AzurePublic,** **AzureChina,** **AzureUsGovernment** e **AzureGermany.** Per impostazione predefinita, viene data factory'ambiente cloud dell'applicazione. | No |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- [Azure AD'autenticazione del token dell'applicazione: entità servizio](#service-principal-authentication)
- [Azure AD'autenticazione del token dell'applicazione: identità gestite per le risorse di Azure](#managed-identity)

>[!TIP]
>Se si verifica un errore con il codice di errore "UserErrorFailedToConnectToSqlServer" e un messaggio simile a "Il limite di sessioni per il database è XXX ed è stato raggiunto", aggiungere alla stringa di connessione e `Pooling=false` riprovare. `Pooling=false`è consigliato anche per la configurazione del servizio collegato **di tipo SHIR(Self Hosted Integration Runtime).** Il pooling e altri parametri di connessione possono essere aggiunti come nuovi nomi e valori dei parametri nella **sezione Proprietà di connessione** aggiuntive del modulo di creazione del servizio collegato.

### <a name="sql-authentication"></a>Autenticazione SQL

**Esempio: uso dell'autenticazione SQL**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: password in Azure Key Vault**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per usare l'autenticazione token dell'applicazione di Azure AD basata sull'entità servizio, seguire questa procedura:

1. [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. [Effettuare il provisioning Azure Active Directory amministratore](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) del server nel portale di Azure se non è già stato fatto. L'amministratore di Azure AD deve essere un utente o un gruppo di Azure AD, ma non può essere un'entità servizio. Questo passaggio viene eseguito in modo che, nel passaggio successivo, sia possibile usare un'identità di Azure AD per creare un utente di database indipendente per l'entità servizio.

3. [Creare utenti del database indipendente](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) per l'entità servizio. Connettersi al database da o in cui si desidera copiare i dati usando strumenti come SQL Server Management Studio, con un'identità Azure AD con almeno l'autorizzazione ALTER ANY USER. Eseguire il codice T-SQL seguente:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Concedere all'entità servizio le autorizzazioni necessarie, come si fa di norma per gli utenti SQL o altri utenti. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Configurare un servizio collegato al database SQL di Azure in Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Esempio di servizio collegato tramite l'autenticazione basata su entità servizio

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Autenticazione di identità gestite per le risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare questa identità gestita per l'database SQL di Azure autenticazione. La factory specificata può accedere e copiare i dati dal database o nel database usando questa identità.

Per usare l'autenticazione dell'identità gestita, seguire questa procedura.

1. [Effettuare il provisioning Azure Active Directory amministratore](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) del server nel portale di Azure se non è già stato fatto. L Azure AD amministratore può essere un Azure AD utente o un Azure AD gruppo. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore ha accesso completo al database.

2. [Creare utenti del database indipendente](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) per l Azure Data Factory'identità gestita. Connettersi al database da o in cui si desidera copiare i dati usando strumenti come SQL Server Management Studio, con un'identità Azure AD con almeno l'autorizzazione ALTER ANY USER. Eseguire il codice T-SQL seguente:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Concedere al Data Factory le autorizzazioni necessarie per l'identità gestita come si fa normalmente per gli utenti SQL e altri utenti. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Configurare un servizio collegato al database SQL di Azure in Azure Data Factory.

**Esempio**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per definire i set di dati, vedere [Set di dati](./concepts-datasets-linked-services.md).

Le proprietà seguenti sono supportate per il set database SQL di Azure set di dati:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La **proprietà type** del set di dati deve essere impostata su **AzureSqlTable**. | Sì |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella/vista con schema. Questa proprietà è supportata per garantire la compatibilità con le versioni precedenti. Per i nuovi carichi di lavoro, usare `schema` e `table`. | No per l'origine, Sì per il sink |

### <a name="dataset-properties-example"></a>Esempio di proprietà dei set di dati

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink del database SQL di Azure.

### <a name="azure-sql-database-as-the-source"></a>Database SQL di Azure come origine

>[!TIP]
>Per caricare i dati da database SQL di Azure in modo efficiente usando il partizionamento dei dati, vedere [Copia parallela dal database SQL](#parallel-copy-from-sql-database).

Per copiare dati da database SQL di Azure, nella sezione origine dell'attività di copia sono supportate le proprietà **seguenti:**

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La **proprietà type** dell'origine dell'attività di copia deve essere impostata su **AzureSqlSource.** Il tipo "SqlSource" è ancora supportato per la compatibilità con le versioni precedenti. | Sì |
| sqlReaderQuery | Questa proprietà usa la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. | No |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. | No |
| storedProcedureParameters | Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e l'utilizzo di maiuscole e minuscole dei parametri devono corrispondere ai nomi e all'utilizzo di maiuscole e minuscole stored procedure parametri. | No |
| isolationLevel | Specifica il comportamento di blocco della transazione per l'origine SQL. I valori consentiti sono: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Se non specificato, viene utilizzato il livello di isolamento predefinito del database. Per altre informazioni dettagliate, vedere [questo documento](/dotnet/api/system.data.isolationlevel). | No |
| partitionOptions | Specifica le opzioni di partizionamento dei dati utilizzate per caricare i dati database SQL di Azure. <br>I valori consentiti **sono: None** (impostazione predefinita), **PhysicalPartitionsOfTable** e **DynamicRange.**<br>Quando un'opzione di partizione è abilitata (ovvero non ), il grado di parallelismo per caricare simultaneamente i dati da un database SQL di Azure è controllato dall'impostazione nell'attività `None` [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) di copia. | No |
| partitionSettings | Specifica il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione di partizione non è `None` . | No |
| ***In `partitionSettings` :*** | | |
| partitionColumnName | Specificare il nome della colonna di origine nel tipo integer o **date/datetime** ( , , , , , , o ) che verrà usato dal partizionamento dell'intervallo per la `int` `smallint` copia `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` parallela. Se non specificato, l'indice o la chiave primaria della tabella viene rilevata automaticamente e utilizzata come colonna di partizione.<br>Si applica quando l'opzione di partizione è `DynamicRange`. Se si usa una query per recuperare i dati di origine, eseguire l'hook  `?AdfDynamicRangePartitionCondition ` nella clausola WHERE. Per un esempio, vedere la [sezione Copia parallela dal database SQL.](#parallel-copy-from-sql-database) | No |
| partitionUpperBound | Valore massimo della colonna di partizione per la suddivisione dell'intervallo di partizioni. Questo valore viene usato per decidere lo stride della partizione, non per filtrare le righe nella tabella. Tutte le righe nella tabella o nel risultato della query verranno partizionate e copiate. Se non specificato, l'attività di copia rileva automaticamente il valore.  <br>Si applica quando l'opzione di partizione è `DynamicRange`. Per un esempio, vedere la [sezione Copia parallela dal database SQL.](#parallel-copy-from-sql-database) | No |
| partitionLowerBound | Valore minimo della colonna di partizione per la suddivisione dell'intervallo di partizioni. Questo valore viene usato per decidere lo stride della partizione, non per filtrare le righe nella tabella. Tutte le righe nella tabella o nel risultato della query verranno partizionate e copiate. Se non specificato, l'attività di copia rileva automaticamente il valore.<br>Si applica quando l'opzione di partizione è `DynamicRange`. Per un esempio, vedere la [sezione Copia parallela dal database SQL.](#parallel-copy-from-sql-database) | No |

**Tenere presente quanto segue:**

- Se **sqlReaderQuery viene** specificato per **AzureSqlSource,** l'attività di copia esegue questa query sull'origine database SQL di Azure per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters**, se la stored procedure accetta parametri.
- Quando si usa stored procedure nell'origine per recuperare i dati, si noti che se il stored procedure è progettato per restituire uno schema diverso quando viene passato un valore di parametro diverso, è possibile che si verifichi un errore o venga visualizzato un risultato imprevisto durante l'importazione dello schema dall'interfaccia utente o quando si copiano dati nel database SQL con la creazione automatica di tabelle.

#### <a name="sql-query-example"></a>Esempio di query SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Esempio di stored procedure

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definizione della stored procedure

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Database SQL di Azure come sink

> [!TIP]
> Per altre informazioni sui comportamenti di scrittura supportati, sulle configurazioni e sulle procedure consigliate, [vedere Procedure consigliate](#best-practice-for-loading-data-into-azure-sql-database)per il caricamento dei dati in database SQL di Azure .

Per copiare dati in database SQL di Azure, nella sezione sink dell'attività di copia sono supportate le proprietà **seguenti:**

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La **proprietà type** del sink dell'attività di copia deve essere impostata su **AzureSqlSink.** Il tipo "SqlSink" è ancora supportato per la compatibilità con le versioni precedenti. | Sì |
| preCopyScript | Specificare una query SQL per l'esecuzione dell'attività di copia prima di scrivere i dati database SQL di Azure. Viene richiamata solo una volta per ogni esecuzione della copia. Usare questa proprietà per pulire i dati precaricati. | No |
| tableOption | Specifica se creare [automaticamente la tabella sink se](copy-activity-overview.md#auto-create-sink-tables) non esiste in base allo schema di origine. <br>La creazione automatica di tabelle non è supportata quando il sink specifica stored procedure. <br>I valori consentiti sono: `none` (impostazione predefinita), `autoCreate`. | No |
| sqlWriterStoredProcedureName | Il nome della stored procedure che definisce come applicare i dati di origine in una tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per le operazioni eseguite una sola volta e che non hanno nulla a che fare con i dati di origine, ad esempio l'eliminazione o il troncamento, usare la `preCopyScript` proprietà .<br>Vedere l'esempio [da Richiamare un stored procedure da un sink SQL.](#invoke-a-stored-procedure-from-a-sql-sink) | No |
| storedProcedureTableTypeParameterName |Nome del parametro del tipo di tabella specificato nel stored procedure.  |No |
| sqlWriterTableType |Nome del tipo di tabella da usare nel stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| writeBatchSize | Numero di righe da inserire nella tabella SQL *per batch.*<br/> Il valore consentito è **integer** (numero di righe). Per impostazione predefinita, Azure Data Factory le dimensioni del batch appropriate in base alle dimensioni della riga. | No |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout.<br/> Il valore consentito è **timespan**. Un esempio è "00:30:00" (30 minuti). | No |
| disableMetricsCollection | Data Factory raccoglie metriche come l'database SQL di Azure DTO per l'ottimizzazione delle prestazioni di copia e le raccomandazioni, che introduce l'accesso al database master aggiuntivo. Se questo comportamento non è desiderato, specificare `true` per disattivarlo. | No (il valore predefinito è `false`) |
| maxConcurrentConnections |Limite massimo di connessioni simultanee stabilite all'archivio dati durante l'esecuzione dell'attività. Specificare un valore solo quando si desidera limitare le connessioni simultanee.| No |

**Esempio 1: Aggiungere dati**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Esempio 2: Richiamare un'stored procedure durante la copia**

Per altre informazioni, vedere [Richiamare una stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Copia parallela dal database SQL

Il database SQL di Azure nell'attività di copia fornisce il partizionamento dei dati incorporato per copiare i dati in parallelo. Le opzioni di partizionamento dei dati sono disponibili nella **scheda Origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-sql-server/connector-sql-partition-options.png)

Quando si abilita la copia partizionata, l'attività di copia esegue query parallele sull'origine database SQL di Azure per caricare i dati in base alle partizioni. Il grado di parallelismo è controllato dall'impostazione [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) sull'attività di copia. Ad esempio, se si imposta su quattro, Data Factory genera ed esegue contemporaneamente quattro query in base all'opzione di partizione e alle impostazioni specificate e ogni query recupera una parte dei dati dal `parallelCopies` database SQL di Azure.

È consigliabile abilitare la copia parallela con il partizionamento dei dati, soprattutto quando si caricano grandi quantità di dati dal database SQL di Azure. Di seguito sono riportate le configurazioni consigliate per i diversi scenari: Quando si copiano dati in un archivio dati basato su file, è consigliabile scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni consigliate                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carico completo da tabelle di grandi dimensioni, con partizioni fisiche.        | **Opzione di partizione**: partizioni fisiche della tabella. <br><br/>Durante l'Data Factory rileva automaticamente le partizioni fisiche e copia i dati in base alle partizioni. <br><br/>Per verificare se la tabella include o meno una partizione fisica, è possibile fare riferimento a [questa query](#sample-query-to-check-physical-partition). |
| Caricamento completo da tabelle di grandi dimensioni, senza partizioni fisiche, con una colonna integer o datetime per il partizionamento dei dati. | **Opzioni di partizione**: Partizione a intervalli dinamici.<br>**Colonna di partizione** (facoltativo): specificare la colonna usata per partizionare i dati. Se non specificato, viene utilizzato l'indice o la colonna chiave primaria.<br/>**Limite superiore della partizione** e **limite inferiore della partizione** (facoltativo): specificare se si vuole determinare lo stride della partizione. Questa operazione non consente di filtrare le righe della tabella. Tutte le righe della tabella verranno partizionate e copiate. Se non specificato, l'attività di copia rileva automaticamente i valori.<br><br>Ad esempio, se la colonna di partizione "ID" ha valori da 1 a 100 e si imposta il limite inferiore su 20 e il limite superiore su 80, con copia parallela su 4, Data Factory recupera i dati da 4 partizioni - ID nell'intervallo <=20, [21, 50], [51, 80] e >=81, rispettivamente. |
| Caricare una grande quantità di dati usando una query personalizzata, senza partizioni fisiche, mentre con una colonna integer o date/datetime per il partizionamento dei dati. | **Opzioni di partizione**: Partizione a intervalli dinamici.<br>**Query**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Colonna di partizione**: Specificare la colonna usata per partizionare i dati.<br>**Limite superiore della partizione** e **limite inferiore della partizione** (facoltativo): specificare se si vuole determinare lo stride della partizione. Non è per filtrare le righe nella tabella, tutte le righe nel risultato della query verranno partizionate e copiate. Se non specificato, l'attività di copia rileva automaticamente il valore.<br><br>Durante l'esecuzione, Data Factory con il nome della colonna e gli intervalli di valori effettivi per ogni partizione e viene inviato a `?AdfRangePartitionColumnName` database SQL di Azure. <br>Ad esempio, se la colonna di partizione "ID" ha valori da 1 a 100 e si imposta il limite inferiore su 20 e il limite superiore su 80, con copia parallela su 4, Data Factory recupera i dati da 4 partizioni- ID nell'intervallo <=20, [21, 50], [51, 80] e >=81, rispettivamente. <br><br>Di seguito sono disponibili altre query di esempio per scenari diversi:<br> 1. Eseguire una query sull'intera tabella: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Eseguire una query da una tabella con la selezione della colonna e filtri clausola where aggiuntivi: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Eseguire query con sottoquery: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Eseguire query con la partizione nella sottoquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Procedure consigliate per caricare i dati con l'opzione di partizione:

1. Scegliere colonna distintiva come colonna di partizione (ad esempio chiave primaria o chiave univoca) per evitare l'avallata dei dati. 
2. Se la tabella include una partizione predefinita, usare l'opzione di partizione "Partizioni fisiche della tabella" per ottenere prestazioni migliori.    
3. Se si usa Azure Integration Runtime per copiare i dati, è possibile impostare " Unità di integrazione dati[(DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) più grandi per usare più risorse di calcolo. Verificare gli scenari applicabili.
4. " Grado di[parallelismo](copy-activity-performance-features.md#parallel-copy)di copia " controlla i numeri di partizione, impostando questo numero troppo grande in alcuni casi si danneggiano le prestazioni, è consigliabile impostare questo numero su (DIU o numero di nodi di IR self-hosted) * (da 2 a 4).

**Esempio: carico completo da tabelle di grandi dimensioni con partizioni fisiche**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Esempio: query con partizione a intervalli dinamici**

```json
"source": {
    "type": "AzureSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Query di esempio per controllare la partizione fisica

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Se la tabella ha una partizione fisica, "HasPartition" viene visualizzato come "sì" come illustrato di seguito.

![Risultato della query SQL](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Procedura consigliata per il caricamento di dati in database SQL di Azure

Quando si copiano dati in database SQL di Azure, potrebbe essere necessario un comportamento di scrittura diverso:

- [Append:](#append-data)i dati di origine hanno solo nuovi record.
- [Upsert:](#upsert-data)i dati di origine hanno sia inserimenti che aggiornamenti.
- [Sovrascrivi:](#overwrite-the-entire-table)si desidera ricaricare ogni volta un'intera tabella delle dimensioni.
- [Scrivere con logica personalizzata:](#write-data-with-custom-logic)è necessaria un'elaborazione aggiuntiva prima dell'inserimento finale nella tabella di destinazione.

Fare riferimento alle rispettive sezioni su come configurare in Azure Data Factory e procedure consigliate.

### <a name="append-data"></a>Accodare dati

L'aggiunta di dati è il comportamento predefinito di questo database SQL di Azure sink. Azure Data Factory esegue un inserimento bulk per scrivere in modo efficiente nella tabella. È possibile configurare l'origine e il sink di conseguenza nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione 1:** Quando si dispone di una grande quantità di dati da copiare, è possibile eseguire il caricamento bulk di tutti i record in una tabella di staging usando l'attività di copia, quindi eseguire un'attività stored procedure per applicare un'istruzione [MERGE](/sql/t-sql/statements/merge-transact-sql) o INSERT/UPDATE in un'unica operazione. 

attività Copy attualmente non supporta in modo nativo il caricamento di dati in una tabella temporanea del database. Esiste un modo avanzato per configurarlo con una combinazione di più attività. Vedere Ottimizzare database SQL di Azure [di Upsert](https://github.com/scoriani/azuresqlbulkupsert)in blocco . Di seguito viene illustrato un esempio dell'uso di una tabella permanente come staging.

Ad esempio, in Azure Data Factory è possibile creare una pipeline con un attività Copy **concatenato** a un'attività **stored procedure**. La prima copia i dati dall'archivio di origine database SQL di Azure una tabella di staging, ad esempio **UpsertStagingTable,** come nome della tabella nel set di dati. Quest'ultimo richiama quindi un stored procedure per unire i dati di origine dalla tabella di staging alla tabella di destinazione e pulire la tabella di staging.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database definire un stored procedure con logica MERGE, come nell'esempio seguente, a cui punta l'attività stored procedure precedente. Si supponga che la destinazione sia la **tabella Marketing** con tre colonne: **ProfileID**, **State** e **Category**. Eseguire l'upsert in base alla **colonna ProfileID.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
   MERGE TargetTable AS target
   USING UpsertStagingTable AS source
   ON (target.[ProfileID] = source.[ProfileID])
   WHEN MATCHED THEN
      UPDATE SET State = source.State
    WHEN NOT matched THEN
       INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    TRUNCATE TABLE UpsertStagingTable
END
```

**Opzione 2:** È possibile scegliere di richiamare [un'stored procedure all'interno dell'attività di copia](#invoke-a-stored-procedure-from-a-sql-sink). Questo approccio esegue ogni batch (come regolato dalla proprietà ) nella tabella di origine anziché usare l'inserimento bulk come `writeBatchSize` approccio predefinito nell'attività di copia.

**Opzione 3:** È possibile usare [mapping Flusso di dati](#sink-transformation) che offre metodi di inserimento/upsert/aggiornamento predefiniti.

### <a name="overwrite-the-entire-table"></a>Sovrascrivere l'intera tabella

È possibile configurare la **proprietà preCopyScript** nel sink dell'attività di copia. In questo caso, per ogni attività di copia eseguita, Azure Data Factory lo script. Quindi esegue la copia per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, specificare uno script per eliminare prima tutti i record prima di caricare i nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere dati con logica personalizzata

I passaggi per scrivere dati con logica personalizzata sono simili a quelli descritti nella [sezione Upsert data (Upsert data).](#upsert-data) Quando è necessario applicare un'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione, è possibile caricare in una tabella di staging e quindi richiamare un'attività stored procedure o richiamare un stored procedure nel sink dell'attività di copia per applicare i dati oppure usare Mapping Flusso di dati.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati in database SQL di Azure, è anche possibile configurare e richiamare un stored procedure specificato dall'utente con parametri aggiuntivi in ogni batch della tabella di origine. La stored procedure utilizza i parametri [con valori di tabella](/dotnet/framework/data/adonet/sql/table-valued-parameters).

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Ad esempio, quando si desidera applicare un'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di elaborazione aggiuntivi si verificano quando si desidera unire colonne, cercare valori aggiuntivi e inserire in più di una tabella.

L'esempio seguente illustra come usare una stored procedure per eseguire un'operazione di upsert in una tabella del database SQL di Azure. Si supponga che i dati di input e la **tabella Marketing** sink siano ognuno con tre colonne: **ProfileID**, **State** e **Category**. Eseguire l'upsert in base **alla colonna ProfileID** e applicarlo solo per una categoria specifica denominata "ProductA".

1. Nel database definire il tipo di tabella con lo stesso nome di **sqlWriterTableType**. Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Nel database definire l'stored procedure con lo stesso nome di **sqlWriterStoredProcedureName**. che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure è uguale a quello di **tableName** definito nel set di dati.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. In Azure Data Factory definire la sezione **sink SQL** nell'attività di copia come segue:

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere nelle tabelle da database SQL di Azure. Per altre informazioni, vedere la [trasformazione origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nei flussi di dati per mapping.

### <a name="source-transformation"></a>Trasformazione origine

Le impostazioni specifiche database SQL di Azure sono disponibili nella **scheda Opzioni** origine della trasformazione di origine.

**Input:** specificare se l'origine deve puntare a una tabella (equivalente di ```Select * from <table-name>```) oppure immettere una query SQL personalizzata.

**Query**: se si seleziona Query nel campo di input, immettere una query SQL per l'origine. Questa impostazione esegue l'override di qualsiasi tabella scelta nel set di dati. Le clausole **Order By** non sono supportate, ma è possibile impostare un'istruzione SELECT FROM completa. È possibile usare anche funzioni di tabella definite dall'utente. **select * from udfGetData()** è un UDF in SQL che restituisce una tabella. Questa query produrrà una tabella di origine che può essere usata nel flusso di dati. L'uso di query è anche un ottimo modo per ridurre le righe per i test o le ricerche.

**Stored procedure:** scegliere questa opzione se si desidera generare una proiezione e dati di origine da un stored procedure eseguito dal database di origine. È possibile digitare lo schema, il nome della procedura e i parametri oppure fare clic su Aggiorna per chiedere ad AdF di individuare gli schemi e i nomi delle procedure. È quindi possibile fare clic su Importa per importare tutti i parametri della procedura usando il modulo ``@paraName`` .

![Stored procedure](media/data-flow/stored-procedure-2.png "Stored Procedure")

- Esempio SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```
- Esempio SQL con parametri: ``"select * from {$tablename} where orderyear > {$year}"``

**Dimensioni batch**: Immettere una dimensione batch per suddividere dati di grandi dimensioni in letture.

**Livello di isolamento**: Il valore predefinito per le origini SQL nel flusso di dati per mapping è Read Uncommitted. È possibile cambiare il livello di isolamento in uno dei valori seguenti:

- Read Committed
- Read Uncommitted
- Repeatable Read
- Serializable
- None (ignora il livello di isolamento)

![Livello di isolamento](media/data-flow/isolationlevel.png "Livello di isolamento")

### <a name="sink-transformation"></a>Trasformazione sink

Le impostazioni specifiche database SQL di Azure sono disponibili nella **scheda Impostazioni** della trasformazione sink.

**Update method** (Metodo di aggiornamento): determina le operazioni consentite nella destinazione del database. Per impostazione predefinita, sono consentiti solo gli inserimenti. Per eseguire operazioni di aggiornamento, upsert o eliminazione di righe, è necessaria una trasformazione alter-row che applichi alle righe i tag corrispondenti alle azioni. Per le operazioni di aggiornamento, upsert ed eliminazione è necessario impostare una o più colonne chiave per determinare quale riga modificare.

![Colonne chiave](media/data-flow/keycolumn.png "Colonne chiave")

Il nome della colonna scelto come chiave verrà usato da ADF come parte dell'aggiornamento successivo, upsert, delete. Pertanto, è necessario selezionare una colonna presente nel mapping sink. Se non si vuole scrivere il valore in questa colonna chiave, fare clic su "Ignora scrittura colonne chiave".

È possibile parametrizzare la colonna chiave usata qui per aggiornare la tabella database SQL di Azure destinazione. Se sono presenti più colonne per una chiave composita, fare clic su "Espressione personalizzata" e sarà possibile aggiungere contenuto dinamico usando il linguaggio delle espressioni del flusso di dati ADF, che può includere una matrice di stringhe con nomi di colonna per una chiave composita.

**Table action** (Azione tabella): determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.

- None (Nessuna): non verrà eseguita alcuna azione sulla tabella.
- Recreate table (Ricrea tabella): la tabella verrà eliminata e ricreata. Questa opzione è obbligatoria se si crea una nuova tabella in modo dinamico.
- Truncate table (Tronca tabella): verranno rimosse tutte le righe della tabella di destinazione.

**Dimensioni batch**: controlla il numero di righe scritte in ogni bucket. Dimensioni batch più grandi migliorano l'ottimizzazione della compressione e della memoria, ma rischiano di causare eccezioni di memoria insufficiente durante la memorizzazione nella cache dei dati.

**Usare TempDB:** Per impostazione predefinita, Data Factory una tabella temporanea globale per archiviare i dati come parte del processo di caricamento. In alternativa, è possibile deselezionare l'opzione "Usa TempDB" e chiedere a Data Factory di archiviare la tabella di memorizzazione temporanea in un database utente che si trova nel database usato per questo sink.

![Usare il database temporaneo](media/data-flow/tempdb.png "Usare il database temporaneo")

**Pre and Post SQL scripts** (Pre-script e post-script SQL): immettere script SQL a più righe che verranno eseguiti prima (pre-elaborazione) e dopo (post-elaborazione) la scrittura dei dati nel database sink.

![Script di pre-elaborazione e post-elaborazione](media/data-flow/prepost1.png "Script di elaborazione SQL")

### <a name="error-row-handling"></a>Gestione delle righe con errori

Quando si scrive nel Azure SQL database, alcune righe di dati potrebbero non riuscire a causa di vincoli impostati dalla destinazione. Alcuni errori comuni includono:

*    I dati stringa o binari verrebbero troncati nella tabella
*    Impossibile inserire il valore NULL nella colonna
*    Istruzione INSERT in conflitto con il vincolo CHECK

Per impostazione predefinita, l'esecuzione di un flusso di dati avrà esito negativo al primo errore che riceve. È possibile scegliere Continua **in caso di errore** che consente il completamento del flusso di dati anche se si verificano errori nelle singole righe. Azure Data Factory offre diverse opzioni per gestire queste righe di errore.

**Commit della transazione:** Scegliere se i dati vengono scritti in una singola transazione o in batch. Una singola transazione fornirà prestazioni peggiori, ma nessun dato scritto sarà visibile ad altri utenti fino al completamento della transazione.  

**Output dei dati rifiutati:** Se abilitata, è possibile eseguire l'output delle righe di errore in un file CSV Archiviazione BLOB di Azure o un account Azure Data Lake Storage Gen2 di propria scelta. Le righe di errore verranno così inserite con tre colonne aggiuntive: l'operazione SQL, ad esempio INSERT o UPDATE, il codice di errore del flusso di dati e il messaggio di errore nella riga.

**Segnalare l'esito positivo in base all'errore:** Se abilitata, il flusso di dati verrà contrassegnato come operazione riuscita anche se vengono trovate righe di errore. 

![Gestione delle righe con errori](media/data-flow/sql-error-row-handling.png "Gestione delle righe con errori")


## <a name="data-type-mapping-for-azure-sql-database"></a>Mapping dei tipi di dati per il database SQL di Azure

Quando i dati vengono copiati da o database SQL di Azure, vengono utilizzati i mapping seguenti da database SQL di Azure tipi di dati a Azure Data Factory dati provvisori. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipi di dati del database SQL di Azure | Tipo di dati provvisorio di Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| Data |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Oggetto |
| text |String, Char[] |
| time |TimeSpan |
|  timestamp |Byte[] |
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |string |

>[!NOTE]
> Per i tipi di dati mappati al tipo provvisorio Decimal, attualmente attività Copy supporta una precisione fino a 28. Se si dispone di dati con una precisione maggiore di 28, provare a eseguire la conversione in una stringa nella query SQL.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per altre informazioni sulle proprietà, vedere [Attività GetMetadata](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>Uso di Always Encrypted

Quando si copiano dati da/in database SQL di Azure con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), usare il connettore [ODBC](connector-odbc.md) generico e il driver ODBC SQL Server tramite Integration Runtime. Questo database SQL di Azure connettore non supporta Always Encrypted ora. 

Più in particolare:

1. Configurare un'istanza self-hosted Integration Runtime se non è già stata impostata. Per [informazioni dettagliate, vedere Integration Runtime self-hosted](create-self-hosted-integration-runtime.md) .

2. Scaricare il driver ODBC a 64 bit per SQL Server da [qui](/sql/connect/odbc/download-odbc-driver-for-sql-server)e installare nel computer Integration Runtime. Per altre informazioni sul funzionamento di questo [driver, vedere Using Always Encrypted with the ODBC Driver for SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Creare un servizio collegato con tipo ODBC per connettersi al database SQL. Vedere gli esempi seguenti:

    - Per usare **l'autenticazione SQL:** specificare la stringa di connessione ODBC come indicato di seguito e selezionare **Autenticazione** di base per impostare il nome utente e la password.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Se si esegue l'autenticazione self-hosted Integration Runtime in  una macchina virtuale di Azure, è possibile usare l'autenticazione dell'identità gestita con l'identità della macchina virtuale di Azure:

        1. Seguire gli stessi [prerequisiti per](#managed-identity) creare l'utente del database per l'identità gestita e concedere il ruolo appropriato nel database.
        2. Nel servizio collegato specificare la stringa di connessione ODBC come indicato di seguito e selezionare **Autenticazione** anonima come la stringa di connessione stessa indica `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Creare di conseguenza un set di dati e un'attività di copia con il tipo ODBC. Per altre informazioni, [vedere l'articolo connettore ODBC.](connector-odbc.md)

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere Archivi dati [e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
