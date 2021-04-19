---
title: Risolvere i Azure Data Factory connettori
description: Informazioni su come risolvere i problemi relativi ai connettori in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 21b5522f07519e9a0c3353cb2463e0ec49063f34
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713427"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Risolvere i Azure Data Factory connettori

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i modi comuni per risolvere i problemi relativi Azure Data Factory connettori.

## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

### <a name="error-code-azurebloboperationfailed"></a>Codice errore: AzureBlobOperationFailed

- **Messaggio:**"Operazione BLOB non riuscita. ContainerName: %containerName;, path: %path;."

- **Causa:** problema con l'operazione di archiviazione BLOB.

- **Raccomandazione:** per controllare i dettagli dell'errore, vedere [i codici di errore di Archiviazione BLOB.](/rest/api/storageservices/blob-service-error-codes) Per altre informazioni, contattare il team di archiviazione BLOB.


### <a name="invalid-property-during-copy-activity"></a>Proprietà non valida durante l'attività di copia

- **Messaggio**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Causa:** il tipo definito nel set di dati non è coerente con il tipo di origine o sink definito nell'attività di copia.

- **Soluzione:** modificare la definizione JSON del set di dati o della pipeline per rendere coerenti i tipi e quindi eseguire nuovamente la distribuzione.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Messaggio di errore: Le dimensioni della richiesta sono troppo grandi

- **Sintomi:** quando si copiano dati in Azure Cosmos DB con dimensioni predefinite del batch di scrittura, viene visualizzato l'errore seguente: `Request size is too large.`

- **Causa:** Azure Cosmos DB limita le dimensioni di una singola richiesta a 2 MB. La formula è *request size = single document size write batch \* size*. Se le dimensioni del documento sono grandi, il comportamento predefinito comporta una dimensione della richiesta troppo grande. È possibile ottimizzare le dimensioni del batch di scrittura.

- **Soluzione:** nel sink dell'attività di copia ridurre il *valore delle dimensioni del batch di* scrittura (il valore predefinito è 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Messaggio di errore: Violazione di vincolo di indice univoco

- **Sintomi:** quando si copiano dati Azure Cosmos DB, viene visualizzato l'errore seguente:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Causa**: Le cause possono essere due:

    - Causa 1: se si usa **Insert** come comportamento di scrittura, questo errore indica che i dati di origine hanno righe o oggetti con lo stesso ID.
    - Causa 2: se si usa **Upsert** come comportamento di scrittura e si imposta un'altra chiave univoca per il contenitore, questo errore indica che i dati di origine hanno righe o oggetti con ID diversi ma lo stesso valore per la chiave univoca definita.

- **Risoluzione**: 

    - Per la causa 1, impostare **Upsert** come comportamento di scrittura.
    - Per la causa 2, assicurarsi che ogni documento abbia un valore diverso per la chiave univoca definita.

### <a name="error-message-request-rate-is-large"></a>Messaggio di errore: La frequenza delle richieste è troppo elevata

- **Sintomi:** quando si copiano dati Azure Cosmos DB, viene visualizzato l'errore seguente:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Causa:** il numero di unità richiesta usate è maggiore delle unità richiesta disponibili configurate in Azure Cosmos DB. Per informazioni su come Azure Cosmos DB le unità richiesta, vedere [Unità richiesta in Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Soluzione:** provare una delle due soluzioni seguenti:

    - Aumentare il *numero di UNITÀ* del contenitore a un valore maggiore in Azure Cosmos DB. Questa soluzione migliorerà le prestazioni dell'attività di copia, ma incorrerà in costi più Azure Cosmos DB. 
    - Ridurre *writeBatchSize* a un valore minore, ad esempio 1000, e *ridurre parallelCopies* a un valore minore, ad esempio 1. Questa soluzione ridurrà le prestazioni di esecuzione della copia, ma non incorrerà in costi aggiuntivi Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Colonne mancanti nel mapping delle colonne

- **Sintomi:** quando si importa uno schema per Azure Cosmos DB mapping delle colonne, alcune colonne sono mancanti. 

- **Causa:** Data Factory lo schema viene dedotto dai primi 10 Azure Cosmos DB documenti. Se alcune colonne o proprietà del documento non contengono valori, lo schema non viene rilevato da Data Factory e di conseguenza non viene visualizzato.

- **Soluzione:** è possibile ottimizzare la query come illustrato nel codice seguente per forzare la visualizzazione dei valori di colonna nel set di risultati con valori vuoti. Si supponga che *la colonna* impossibile non sia presente nei primi 10 documenti. In alternativa, è possibile aggiungere manualmente la colonna per il mapping.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Messaggio di errore: GuidRepresentation per il lettore è CSharpLegacy

- **Sintomi:** quando si copiano dati da Azure Cosmos DB MongoAPI o MongoDB con il campo UUID (Universally Unique Identifier), viene visualizzato l'errore seguente:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Causa:** esistono due modi per rappresentare l'UUID in BINARY JSON (BSON): UuidStardard e UuidLegacy. Per impostazione predefinita, viene usato UuidLegacy per leggere i dati. Verrà visualizzato un errore se i dati UUID in MongoDB sono UuidStandard.

- **Soluzione:** nella stringa di connessione MongoDB aggiungere *l'opzione uuidRepresentation=standard.* Per altre informazioni, vedere [Stringa di connessione MongoDB](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (API SQL)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Codice errore: CosmosDbSqlApiOperationFailed

- **Messaggio**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Causa:** problema con l'operazione CosmosDbSqlApi.

- **Raccomandazione:** per controllare i dettagli dell'errore, [Azure Cosmos DB documento della Guida.](../cosmos-db/troubleshoot-dot-net-sdk.md) Per altre informazioni, contattare il team Azure Cosmos DB.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Messaggio di errore: La connessione sottostante è stata chiusa: Non è stato possibile stabilire una relazione di trust per il canale sicuro SSL/TLS.

- **Sintomi:** attività Copy ha esito negativo con l'errore seguente: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Causa:** la convalida del certificato non è riuscita durante l'handshake TLS.

- **Soluzione:** come soluzione alternativa, usare la copia di stage per ignorare la convalida Transport Layer Security (TLS) per Azure Data Lake Storage Gen1. È necessario riprodurre questo problema e raccogliere la traccia di network monitor (netmon) e quindi coinvolgere il team di rete per controllare la configurazione della rete locale.

    ![Diagramma delle connessioni Azure Data Lake Storage Gen1 per la risoluzione dei problemi.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Messaggio di errore: Errore del server remoto: (403) Accesso negato

- **Sintomi**: L'attività di copia ha esito negativo con l'errore seguente: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Causa:** una possibile causa è che l'entità servizio o l'identità gestita in uso non dispone dell'autorizzazione per accedere a determinate cartelle o file.

- **Soluzione:** concedere le autorizzazioni appropriate a tutte le cartelle e sottocartelle che è necessario copiare. Per altre informazioni, vedere [Copiare dati in o da Azure Data Lake Storage Gen1 usando Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Messaggio di errore: Non è stato possibile ottenere il token di accesso tramite l'entità servizio. Errore ADAL: service_unavailable

- **Sintomi:** attività Copy ha esito negativo con l'errore seguente:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Causa:** quando il server token di servizio di proprietà di Azure Active Directory non è disponibile, significa che è troppo occupato per gestire le richieste e restituisce l'errore HTTP 503. 

- **Soluzione**: Eseguire di nuovo l'attività di copia dopo alcuni minuti.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Codice errore: ADLSGen2OperationFailed

- **Messaggio**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Cause e raccomandazioni:** cause diverse possono causare questo errore. Controllare l'elenco seguente per informazioni su possibili analisi delle cause e raccomandazioni correlate.

  | Analisi delle cause                                               | Raccomandazione                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Se Azure Data Lake Storage Gen2 genera un errore che indica che un'operazione non è riuscita.| Controllare il messaggio di errore dettagliato generato da Azure Data Lake Storage Gen2. Se l'errore è temporaneo, ripetere l'operazione. Per altre informazioni, contattare Archiviazione di Azure supporto tecnico e specificare l'ID richiesta nel messaggio di errore. |
  | Se il messaggio di errore contiene la stringa "Accesso negato", l'entità servizio o l'identità gestita in uso potrebbe non disporre di autorizzazioni sufficienti per accedere Azure Data Lake Storage Gen2. | Per risolvere questo errore, vedere [Copiare e trasformare](./connector-azure-data-lake-storage.md#service-principal-authentication)i dati in Azure Data Lake Storage Gen2 usando Azure Data Factory . |
  | Se il messaggio di errore contiene la stringa "InternalServerError", l'errore viene restituito da Azure Data Lake Storage Gen2. | L'errore potrebbe essere causato da un errore temporaneo. In questo caso, provare a ripetere l'operazione. Se il problema persiste, contattare Archiviazione di Azure supporto tecnico e fornire l'ID richiesta dal messaggio di errore. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>La richiesta di Azure Data Lake Storage Gen2'account ha causato un errore di timeout

- **Messaggio**: 
  * Codice errore = `UserErrorFailedBlobFSOperation`
  * Messaggio di errore = `BlobFS operation failed for: A task was canceled.`

- **Causa:** il problema è causato dall'Azure Data Lake Storage Gen2 di timeout del sink, che in genere si verifica nel computer self-hosted Integration Runtime (IR).

- **Raccomandazione**: 

    - Inserire il computer a matrice self-hosted e l'account Azure Data Lake Storage Gen2 destinazione nella stessa area, se possibile. Ciò consente di evitare un errore di timeout casuale e di ottenere prestazioni migliori.

    - Verificare se è presente un'impostazione di rete speciale, ad esempio ExpressRoute, e verificare che la rete abbia larghezza di banda sufficiente. È consigliabile ridurre l'impostazione Processi simultanei self-hosted IR quando la larghezza di banda complessiva è bassa. In questo modo è possibile evitare la concorrenza delle risorse di rete tra più processi simultanei.

    - Se le dimensioni del file sono moderate o piccole, usare dimensioni di blocco inferiori per la copia nonbinaria per attenuare tale errore di timeout. Per altre informazioni, vedere [Blocco put di archiviazione BLOB](/rest/api/storageservices/put-block).

       Per specificare la dimensione del blocco personalizzata, modificare la proprietà nell'editor di file JSON come illustrato di seguito:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>File di Azure archiviazione

### <a name="error-code-azurefileoperationfailed"></a>Codice errore: AzureFileOperationFailed

- **Messaggio**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Causa:** problema con l'File di Azure di archiviazione.

- **Raccomandazione:** per controllare i dettagli dell'errore, [File di Azure guida.](/rest/api/storageservices/file-service-error-codes) Per altre informazioni, contattare il team File di Azure.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, database SQL di Azure e SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Codice errore: SqlFailedToConnect

- **Messaggio**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Cause e raccomandazioni:** cause diverse possono causare questo errore. Controllare l'elenco seguente per informazioni su possibili analisi delle cause e raccomandazioni correlate.

    | Analisi delle cause                                               | Raccomandazione                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Ad Azure SQL, se il messaggio di errore contiene la stringa "SqlErrorNumber=47073", significa che l'accesso alla rete pubblica viene negato nell'impostazione di connettività. | Nel firewall Azure SQL impostare **l'opzione Nega** accesso alla rete pubblica su *No.* Per altre informazioni, vedere Impostazioni [Azure SQL connettività.](../azure-sql/database/connectivity-settings.md#deny-public-network-access) |
    | Per Azure SQL, se il messaggio di errore contiene un codice di errore SQL, ad esempio "SqlErrorNumber=[errorcode]", vedere la guida alla risoluzione Azure SQL risoluzione dei problemi. | Per una raccomandazione, vedere [Risolvere i problemi di connettività](../azure-sql/database/troubleshoot-common-errors-issues.md)e altri errori database SQL di Azure e Istanza gestita di SQL di Azure . |
    | Verificare se la porta 1433 è presente nell'elenco dei firewall consentiti. | Per altre informazioni, vedere [Porte usate da SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Se il messaggio di errore contiene la stringa "SqlException", nel database SQL l'errore indica che un'operazione specifica non è riuscita. | Per altre informazioni, cercare in base al codice di errore SQL in [Errori del motore di database.](/sql/relational-databases/errors-events/database-engine-events-and-errors) Per altre informazioni, contattare il Azure SQL supporto tecnico. |
    | Se si tratta di un problema temporaneo, ad esempio una connessione di rete non modificabile, aggiungere nuovi tentativi nei criteri di attività per attenuare il problema. | Per altre informazioni, vedere [Pipeline e attività in Azure Data Factory](./concepts-pipelines-activities.md#activity-policy). |
    | Se il messaggio di errore contiene la stringa "Client con indirizzo IP '...' non è consentito accedere al server" e si sta tentando di connettersi a database SQL di Azure, l'errore è in genere causato da un problema database SQL di Azure firewall. | Nella configurazione del firewall Azure SQL Server abilitare l'opzione Consenti a servizi e risorse di **Azure di accedere a questo server.** Per altre informazioni, vedere regole [database SQL di Azure e Azure Synapse del firewall IP.](../azure-sql/database/firewall-configure.md) |
    
### <a name="error-code-sqloperationfailed"></a>Codice errore: SqlOperationFailed

- **Messaggio**: `A database operation failed. Please search error to get more details.`

- **Cause e raccomandazioni:** cause diverse possono causare questo errore. Controllare l'elenco seguente per le possibili analisi delle cause e le raccomandazioni correlate.

    | Analisi delle cause                                               | Raccomandazione                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Se il messaggio di errore contiene la stringa "SqlException", il database SQL genera un errore che indica che un'operazione specifica non è riuscita. | Se l'errore SQL non è chiaro, provare a modificare il database al livello di compatibilità più recente "150". Può generare gli errori SQL della versione più recente. Per altre informazioni, vedere la [documentazione](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Per altre informazioni sulla risoluzione dei problemi di SQL, cercare il codice di errore SQL in [Errori del motore di database](/sql/relational-databases/errors-events/database-engine-events-and-errors). Per altre informazioni, contattare il Azure SQL supporto tecnico. |
    | Se il messaggio di errore contiene la stringa "PdwManagedToNativeInteropException", è in genere causato da una mancata corrispondenza tra le dimensioni delle colonne di origine e sink. | Controllare le dimensioni delle colonne di origine e sink. Per altre informazioni, contattare il Azure SQL supporto tecnico. |
    | Se il messaggio di errore contiene la stringa "InvalidOperationException", è in genere causato da dati di input non validi. | Per identificare la riga in cui si è verificato il problema, abilitare la funzionalità di tolleranza di errore nell'attività di copia, che può reindirizzare le righe problematiche all'archiviazione per un'ulteriore analisi. Per altre informazioni, vedere [Tolleranza di errore dell'attività di](./copy-activity-fault-tolerance.md)copia in Azure Data Factory . |


### <a name="error-code-sqlunauthorizedaccess"></a>Codice errore: SqlUnauthorizedAccess

- **Messaggio**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa:** le credenziali non sono corrette o l'account di accesso non può accedere al database SQL.

- **Raccomandazione:** verificare che l'account di accesso disponga di autorizzazioni sufficienti per accedere al database SQL.


### <a name="error-code-sqlopenconnectiontimeout"></a>Codice errore: SqlOpenConnectionTimeout

- **Messaggio**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa:** il problema potrebbe essere un errore temporaneo del database SQL.

- **Raccomandazione:** ripetere l'operazione per aggiornare la stringa di connessione del servizio collegato con un valore di timeout di connessione maggiore.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Codice errore: SqlAutoCreateTableTypeMapFailed

- **Messaggio**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa:** la tabella di creazione automatica non può soddisfare il requisito di origine.

- **Raccomandazione:** aggiornare il tipo di colonna *nei mapping* o creare manualmente la tabella sink nel server di destinazione.


### <a name="error-code-sqldatatypenotsupported"></a>Codice errore: SqlDataTypeNotSupported

- **Messaggio**: `A database operation failed. Check the SQL errors.`

- **Causa:** se il problema si verifica nell'origine SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati supera l'intervallo del tipo di logica (1/1/1753 12:00:00 AM - 31/12/9999 11:59:59 PM).

- **Raccomandazione:** eseguire il cast del tipo alla stringa nella query SQL di origine o, nel mapping della colonna dell'attività di copia, modificare il tipo di colonna in *Stringa*.

- **Causa:** se il problema si verifica nel sink SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati supera l'intervallo consentito nella tabella sink.

- **Raccomandazione:** aggiornare il tipo di colonna corrispondente al *tipo datetime2* nella tabella sink.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Codice errore: SqlInvalidDbStoredProcedure

- **Messaggio**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa:** l'stored procedure specificata non è valida. La causa potrebbe essere che stored procedure non restituisce dati.

- **Raccomandazione:** convalidare stored procedure usando gli strumenti SQL. Assicurarsi che il stored procedure possa restituire dati.


### <a name="error-code-sqlinvaliddbquerystring"></a>Codice errore: SqlInvalidDbQueryString

- **Messaggio**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa:** la query SQL specificata non è valida. La causa potrebbe essere che la query non restituisce dati.

- **Raccomandazione:** convalidare la query SQL usando SQL Tools. Assicurarsi che la query possa restituire dati.


### <a name="error-code-sqlinvalidcolumnname"></a>Codice errore: SqlInvalidColumnName

- **Messaggio**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa:** impossibile trovare la colonna perché la configurazione potrebbe non essere corretta.

- **Raccomandazione:** verificare la colonna nella query, *la struttura* nel set di dati e i *mapping nell'attività.*


### <a name="error-code-sqlbatchwritetimeout"></a>Codice errore: SqlBatchWriteTimeout

- **Messaggio**: `Timeouts in SQL write operation.`

- **Causa:** il problema potrebbe essere causato da un errore temporaneo del database SQL.

- **Raccomandazione:** ripetere l'operazione. Se il problema persiste, contattare Azure SQL supporto tecnico.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Codice errore: SqlBatchWriteTransactionFailed

- **Messaggio**: `SQL transaction commits failed.`

- **Causa:** se i dettagli dell'eccezione indicano costantemente un timeout della transazione, la latenza di rete tra il runtime di integrazione e il database è maggiore della soglia predefinita di 30 secondi.

- **Raccomandazione:** aggiornare la stringa di connessione del servizio collegato a SQL con un valore di *timeout* di connessione uguale o maggiore di 120 ed eseguire nuovamente l'attività.

- **Causa:** se i dettagli dell'eccezione indicano in modo intermittente che la connessione SQL è interrotta, potrebbe trattarsi di un errore di rete temporaneo o di un problema sul lato database SQL.

- **Raccomandazione:** ripetere l'attività ed esaminare le metriche sul lato database SQL.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Codice errore: SqlBulkCopyInvalidColumnLength

- **Messaggio**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa:** la copia bulk SQL non è riuscita perché ha ricevuto una lunghezza di colonna non valida dal client dell'utilità di programma di copia bulk (bcp).

- **Raccomandazione:** per identificare la riga in cui si è verificato il problema, abilitare la funzionalità di tolleranza di errore nell'attività di copia. In questo modo è possibile reindirizzare le righe problematiche all'archiviazione per un'ulteriore analisi. Per altre informazioni, vedere [Tolleranza di errore dell'attività di](./copy-activity-fault-tolerance.md)copia in Azure Data Factory .


### <a name="error-code-sqlconnectionisclosed"></a>Codice errore: SqlConnectionIsClosed

- **Messaggio**: `The connection is closed by SQL Database.`

- **Causa:** la connessione SQL viene chiusa dal database SQL quando viene eseguita un'esecuzione simultanea elevata e il server termina la connessione.

- **Raccomandazione:** ripetere la connessione. Se il problema persiste, contattare Azure SQL supporto tecnico.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Messaggio di errore: Conversione non riuscita durante la conversione di una stringa di caratteri nel tipo di dati uniqueidentifier

- **Sintomi:** quando si copiano dati da un'origine dati tabulare (ad esempio SQL Server) in Azure Synapse Analytics usando la copia di gestione locale e PolyBase, viene visualizzato l'errore seguente:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Causa:** Azure Synapse Analytics PolyBase non può convertire una stringa vuota in un GUID.

- **Soluzione:** nel sink dell'attività di copia, nelle impostazioni di PolyBase, impostare **l'opzione use type default** su *false.*


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Messaggio di errore: Tipo di dati previsto: DECIMAL(x, x), valore danneggiato

- **Sintomi:** quando si copiano dati da un'origine dati tabulare (ad esempio SQL Server) in Azure Synapse Analytics usando la copia di gestione di gestione locale e PolyBase, viene visualizzato l'errore seguente:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Causa:** Azure Synapse Analytics PolyBase non può inserire una stringa vuota (valore Null) in una colonna decimale.

- **Risoluzione:** nel sink dell'attività di copia, nelle impostazioni di PolyBase, impostare **l'opzione use type default** su false.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Messaggio di errore: Messaggio di eccezione Java: HdfsBridge::CreateRecordReader

- **Sintomi:** i dati vengono copiati Azure Synapse Analytics usando PolyBase e viene visualizzato l'errore seguente:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Causa:** la causa potrebbe essere che lo schema (larghezza totale delle colonne) è troppo grande (maggiore di 1 MB). Controllare lo schema della tabella di Azure Synapse Analytics di destinazione aggiungendo le dimensioni di tutte le colonne:

    - Int = 4 byte
    - Bigint = 8 byte
    - Varchar(n), char(n), binary(n), varbinary(n) = n byte
    - Nvarchar(n), nchar(n) = n*2 byte
    - Data = 6 byte
    - Datetime/(2), smalldatetime = 16 byte
    - Datetimeoffset = 20 byte
    - Decimale = 19 byte
    - Float = 8 byte
    - Money = 8 byte
    - Smallmoney = 4 byte
    - Reale = 4 byte
    - Smallint = 2 byte
    - Tempo = 12 byte
    - Tinyint = 1 byte

- **Risoluzione**: 
    - Ridurre la larghezza della colonna a meno di 1 MB.
    - Oppure usare un approccio di inserimento bulk disabilitando PolyBase.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Messaggio di errore: La condizione specificata con le intestazioni condizionali HTTP non è stata soddisfatta

- **Sintomi:** si usa la query SQL per eseguire il pull dei Azure Synapse Analytics e viene visualizzato l'errore seguente:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Causa:** Azure Synapse Analytics si è verificato un problema durante l'esecuzione di query sulla tabella esterna Archiviazione di Azure.

- **Soluzione:** eseguire la stessa query in SQL Server Management Studio (SSMS) e verificare se si ottiene lo stesso risultato. In caso contrario, aprire un ticket di supporto per Azure Synapse Analytics e specificare il nome Azure Synapse Analytics server e il nome del database.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Il livello di prestazioni è basso e causa un errore di copia

- **Sintomi:** i dati vengono copiati database SQL di Azure e viene visualizzato l'errore seguente: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Causa:** database SQL di Azure s1 ha raggiunto i limiti di input/output (I/O).

- **Soluzione:** aggiornare il livello database SQL di Azure prestazioni per risolvere il problema. 


### <a name="sql-table-cant-be-found"></a>Impossibile trovare la tabella SQL 

- **Sintomi:** i dati vengono copiati da un ambiente ibrido in una tabella SQL Server locale e viene visualizzato l'errore seguente:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Causa:** l'account SQL corrente non dispone di autorizzazioni sufficienti per eseguire le richieste emesse da SqlBulkCopy.WriteToServer .NET.

- **Soluzione:** passare a un account SQL con privilegi più elevati.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Messaggio di errore: Dati stringa o binari troncati

- **Sintomi:** si verifica un errore quando si copiano dati in una tabella Azure SQL Server locale. 

- **Causa:** la definizione dello schema della tabella SQL Cx include una o più colonne con lunghezza minore del previsto.

- **Soluzione:** per risolvere il problema, provare a eseguire le operazioni seguenti:

    1. Per risolvere il problema relativo alle righe, applicare la tolleranza di errore [del](./copy-activity-fault-tolerance.md)sink SQL, in particolare "redirectIncompatibleRowSettings".

        > [!NOTE]
        > La tolleranza di errore potrebbe richiedere tempi di esecuzione aggiuntivi, con un potenziale aumento dei costi.

    2. Controllare i dati reindirizzati rispetto alla lunghezza della colonna dello schema della tabella SQL per vedere quali colonne devono essere aggiornate.

    3. Aggiornare lo schema della tabella di conseguenza.


## <a name="azure-table-storage"></a>Archiviazione tabelle di Azure

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Codice errore: AzureTableDuplicateColumnsFromSource

- **Messaggio**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Causa:** le colonne di origine duplicate possono verificarsi per uno dei motivi seguenti:
   * Si usa il database come origine e i join di tabella applicati.
   * Nella riga di intestazione sono presenti file CSV non strutturati con nomi di colonna duplicati.

- **Raccomandazione:** controllare e correggere le colonne di origine, se necessario.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Codice errore: DB2DriverRunFailed

- **Messaggio**: `Error thrown from driver. Sql code: '%code;'`

- **Causa:** se il messaggio di errore contiene la stringa "SQLSTATE=51002 SQLCODE=-805", seguire il "suggerimento" in Copiare dati da [DB2 usando Azure Data Factory](./connector-db2.md#linked-service-properties).

- **Raccomandazione:** provare a impostare "NULLID" nella `packageCollection`  proprietà .


## <a name="delimited-text-format"></a>Formato di testo delimitato

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Codice errore: DelimitedTextColumnNameNotAllowNull

- **Messaggio**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa:** quando 'firstRowAsHeader' è impostato nell'attività, come nome della colonna viene usata la prima riga. Questo errore indica che la prima riga contiene un valore vuoto, ad esempio 'ColumnA, ColumnB'.

- **Raccomandazione:** controllare la prima riga e correggere il valore se è vuoto.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Codice errore: DelimitedTextMoreColumnsThanDefined

- **Messaggio**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Cause e raccomandazioni:** cause diverse possono causare questo errore. Controllare l'elenco seguente per le possibili analisi delle cause e le raccomandazioni correlate.

  | Analisi delle cause                                               | Raccomandazione                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Il numero di colonne della riga problematica è maggiore del numero di colonne della prima riga. Potrebbe essere causato da un problema di dati o da impostazioni non corrette del delimitatore di colonna o delle virgolette. | Ottenere il numero di righe dal messaggio di errore, controllare la colonna della riga e correggere i dati. |
  | Se il numero di colonne previsto è "1" in un messaggio di errore, è possibile che sia stata specificata una compressione o impostazioni di formato erre, che Data Factory analizzare i file in modo non corretto. | Controllare le impostazioni del formato per assicurarsi che corrispondano ai file di origine. |
  | Se l'origine è una cartella, i file nella cartella specificata potrebbero avere uno schema diverso. | Assicurarsi che i file nella cartella specificata hanno uno schema identico. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service e Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Codice errore: DynamicsCreateServiceClientError

- **Messaggio**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Causa:** il problema è un problema temporaneo sul lato server Dynamics.

- **Raccomandazione**:  Eseguire di nuovo la pipeline. Se non riesce di nuovo, provare a ridurre il parallelismo. Se il problema persiste, contattare il supporto di Dynamics.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Colonne mancanti durante l'importazione di uno schema o l'anteprima dei dati

- **Sintomi:** alcune colonne non sono presenti quando si importa uno schema o si visualizzano in anteprima i dati. Messaggio di errore: `The valid structure information (column name and type) are required for Dynamics source.`

- **Causa:** questo problema è dovuto alla progettazione, perché Data Factory non è in grado di visualizzare colonne che non contengono valori nei primi 10 record. Assicurarsi che le colonne aggiunte siano nel formato corretto. 

- **Raccomandazione:** aggiungere manualmente le colonne nella scheda mapping.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Codice errore: DynamicsMissingTargetForMultiTargetLookupField

- **Messaggio**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Causa:** la colonna di destinazione non esiste nell'origine o nel mapping delle colonne.

- **Raccomandazione**:  
  1. Assicurarsi che l'origine contenga la colonna di destinazione. 
  2. Aggiungere la colonna di destinazione nel mapping delle colonne. Assicurarsi che la colonna sink sia nel formato *{fieldName} @EntityReference*.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Codice errore: DynamicsInvalidTargetForMultiTargetLookupField

- **Messaggio**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Causa:** viene fornito un nome di entità errato come entità di destinazione di un campo di ricerca multi-destinazione.

- **Raccomandazione:** specificare un nome di entità valido per il campo di ricerca multi-destinazione.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Codice errore: DynamicsInvalidTypeForMultiTargetLookupField

- **Messaggio**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Causa:** il valore nella colonna di destinazione non è una stringa.

- **Raccomandazione:** specificare una stringa valida nella colonna di destinazione della ricerca multi-destinazione.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Codice errore: DynamicsFailedToRequetServer

- **Messaggio**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Causa:** il server Dynamics è inaccessibile o inaccessibile oppure la rete sta riscontrando problemi.

- **Raccomandazione:** per altri dettagli, controllare la connettività di rete o controllare il log del server Dynamics. Per altre informazioni, contattare il supporto di Dynamics.


### <a name="error-code--dynamicsfailedtoconnect"></a>Codice errore: DynamicsFailedToConnect 
 
 - **Messaggio**: `Failed to connect to Dynamics: %message;` 
 

 - **Causa:** se viene visualizzato nel messaggio di errore, significa che il server potrebbe avere alcune configurazioni `Office 365 auth with OAuth failed` non compatibili con OAuth. 
 
 - **Raccomandazione**: 
    1. Per assistenza, contattare il team di supporto di Dynamics con il messaggio di errore dettagliato.  
    1. Usare l'autenticazione dell'entità servizio ed è possibile fare riferimento a questo [articolo: Esempio: Dynamics online usando Azure AD'entità](https://docs.microsoft.com/azure/data-factory/connector-dynamics-crm-office-365#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication)servizio e l'autenticazione del certificato. 
 

 - **Causa:** se viene visualizzato nel messaggio di errore, significa che è stato immesso l'URL del servizio Dynamics errato o `Unable to retrieve authentication parameters from the serviceUri` il proxy/firewall per intercettare il traffico. 
 
 - **Raccomandazione**:
    1. Assicurarsi di aver inserito l'URI del servizio corretto nel servizio collegato. 
    1. Se si usa il self-hosted IR, assicurarsi che il firewall/proxy non intercetti le richieste al server Dynamics. 
   
 
 - **Causa:** se viene visualizzato nel messaggio di errore, significa che sono state restituite risposte impreviste `An unsecured or incorrectly secured fault was received from the other party` dal lato server. 
 
 - **Raccomandazione**: 
    1. Assicurarsi che il nome utente e la password siano corretti se si usa l'autenticazione di Office 365. 
    1. Assicurarsi di avere immesso l'URI del servizio corretto. 
    1. Se si usa l'URL CRM a livello di regione (l'URL ha un numero dopo 'crm'), assicurarsi di usare l'identificatore di regione corretto.
    1. Per assistenza, contattare il team di supporto di Dynamics. 
 

 - **Causa:** se viene visualizzato nel messaggio di errore, significa che il nome dell'organizzazione non è corretto o che è stato usato un identificatore di area CRM errato `No Organizations Found` nell'URL del servizio. 
 
 - **Raccomandazione**: 
    1. Assicurarsi di avere immesso l'URI del servizio corretto.
    1. Se si usa l'URL CRM a livello di regione (l'URL ha un numero dopo 'crm'), assicurarsi di usare l'identificatore di impostazioni internazionali corretto. 
    1. Per assistenza, contattare il team di supporto di Dynamics. 

 
 - **Causa:** se viene visualizzato un messaggio di errore correlato ad AAD, significa che si è verificato `401 Unauthorized` un problema con l'entità servizio. 

 - **Raccomandazione:** seguire le indicazioni nel messaggio di errore per risolvere il problema dell'entità servizio.  
 
 
 - **Causa:** per altri errori, in genere il problema si verifica sul lato server. 

 - **Raccomandazione:** usare [XrmToolBox](https://www.xrmtoolbox.com/) per stabilire la connessione. Se l'errore persiste, contattare il team di supporto di Dynamics per assistenza. 
 
 
### <a name="error-code--dynamicsoperationfailed"></a>Codice errore: DynamicsOperationFailed 
 
- **Messaggio**: `Dynamics operation failed with error code: %code;, error message: %message;.` 

- **Causa:** operazione non riuscita sul lato server. 

- **Raccomandazione:** estrarre il codice di errore dell'operazione dynamics dal messaggio di errore e fare riferimento all'articolo Codici di errore del servizio Web per `Dynamics operation failed with error code: {code}` informazioni più dettagliate. [](https://docs.microsoft.com/powerapps/developer/data-platform/org-service/web-service-error-codes) Se necessario, è possibile contattare il team di supporto di Dynamics. 
 
 
### <a name="error-code--dynamicsinvalidfetchxml"></a>Codice errore: DynamicsInvalidFetchXml 
  
- **Messaggio**: `The Fetch Xml query specified is invalid.` 

- **Causa:** si è verificato un errore nel recupero XML.  

- **Raccomandazione:** correggere l'errore nel recupero XML. 
 
 
### <a name="error-code--dynamicsmissingkeycolumns"></a>Codice errore: DynamicsMissingKeyColumns 
 
- **Messaggio**: `Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **Causa:** i dati di origine non contengono la colonna chiave per l'entità sink. 

- **Raccomandazione:** verificare che le colonne chiave siano presenti nei dati di origine o eseguire il mapping di una colonna di origine alla colonna chiave nell'entità sink. 
 
 
### <a name="error-code--dynamicsprimarykeymustbeguid"></a>Codice errore: DynamicsPrimaryKeyMustBeGuid 
 
- **Messaggio**: `The primary key attribute '%attribute;' must be of type guid.` 
 
- **Causa:** il tipo della colonna chiave primaria non è 'Guid'. 
 
- **Raccomandazione:** assicurarsi che la colonna chiave primaria nei dati di origine sia di tipo 'Guid'. 
 

### <a name="error-code--dynamicsalternatekeynotfound"></a>Codice errore: DynamicsAlternateKeyNotFound 
 
- **Messaggio**: `Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **Causa:** la chiave alternativa specificata non esiste, il che può essere causato da nomi di chiave erre o da autorizzazioni insufficienti. 
 
- **Raccomandazione**: <br/> 
    1. Correggere gli errori di digitazione nel nome della chiave.<br/> 
    1. Assicurarsi di avere autorizzazioni sufficienti per l'entità. 
 
 
### <a name="error-code--dynamicsinvalidschemadefinition"></a>Codice errore: DynamicsInvalidSchemaDefinition 
 
- **Messaggio**: `The valid structure information (column name and type) are required for Dynamics source.` 
 
- **Causa:** le colonne sink nel mapping delle colonne non hanno la proprietà 'type'. 
 
- **Raccomandazione:** è possibile aggiungere la proprietà 'type' a tali colonne nel mapping delle colonne usando l'editor JSON nel portale. 


## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Codice errore: FtpFailedToConnectToFtpServer

- **Messaggio**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Causa:** per il server FTP potrebbe essere usato un tipo di servizio collegato non corretto, ad esempio il servizio collegato SECURE FTP (SFTP) per connettersi a un server FTP.

- **Raccomandazione:** controllare la porta del server di destinazione. FTP usa la porta 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Codice errore: HttpFileFailedToRead

- **Messaggio**: `Failed to read data from http server. Check the error from http server：%message;`

- **Causa:** questo errore si verifica quando Azure Data Factory con il server HTTP, ma l'operazione di richiesta HTTP ha esito negativo.

- **Raccomandazione:** controllare il codice di stato HTTP nel messaggio di errore e correggere il problema del server remoto.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Codice errore: ArgumentOutOfRangeException

- **Messaggio**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Causa:** in Data Factory i valori DateTime sono supportati nell'intervallo compreso tra 0001-01-01 00:00:00 a 9999-12-31 23:59:59. Oracle, tuttavia, supporta un intervallo più ampio di valori DateTime, ad esempio bc century o min/sec>59, causando un errore Data Factory.

- **Raccomandazione**: 

    Per verificare se il valore in Oracle è compreso nell'intervallo di Data Factory, eseguire `select dump(<column name>)` . 

    Per informazioni sulla sequenza di byte nel risultato, vedere [Come vengono archiviate le date in Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>Formato ORC

### <a name="error-code-orcjavainvocationexception"></a>Codice errore: OrcJavaInvocationException

- **Messaggio**: `An error occurred when invoking Java, message: %javaException;.`
- **Cause e raccomandazioni:** cause diverse possono causare questo errore. Controllare l'elenco seguente per le possibili analisi delle cause e le raccomandazioni correlate.

    | Analisi delle cause                                               | Raccomandazione                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Quando il messaggio di errore contiene le stringhe "java.lang.OutOfMemory", "Spazio heap Java" e "doubleCapacity", si tratta in genere di un problema di gestione della memoria in una versione precedente del runtime di integrazione. | Se si usa self-hosted Integration Runtime, è consigliabile eseguire l'aggiornamento alla versione più recente. |
    | Quando il messaggio di errore contiene la stringa "java.lang.OutOfMemory", il runtime di integrazione non dispone di risorse sufficienti per elaborare i file. | Limitare le esecuzioni simultanee nel runtime di integrazione. Per il self-hosted IR, scalare fino a un computer potente con memoria uguale o superiore a 8 GB. |
    |Quando il messaggio di errore contiene la stringa "NullPointerReference", la causa potrebbe essere un errore temporaneo. | Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico. |
    | Quando il messaggio di errore contiene la stringa "BufferOverflowException", la causa potrebbe essere un errore temporaneo. | Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico. |
    | Quando il messaggio di errore contiene la stringa "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can't be cast to org.apache.hadoop.io.Text", la causa potrebbe essere un problema di conversione dei tipi nel runtime Java. In genere, significa che i dati di origine non possono essere gestiti correttamente in Java Runtime. | Si tratta di un problema di dati. Provare a usare una stringa anziché char o varchar nei dati in formato ORC. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Codice errore: OrcDateTimeExceedLimit

- **Messaggio**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Causa:** se il valore datetime è "0001-01-01 00:00:00", potrebbe essere causato dalle differenze tra il calendario gregoriano e il calendario [gregoriano](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Raccomandazione:** controllare il valore dei tick ed evitare di usare il valore datetime '0001-01-01 00:00:00'.


## <a name="parquet-format"></a>Formato Parquet

### <a name="error-code-parquetjavainvocationexception"></a>Codice errore: ParquetJavaInvocationException

- **Messaggio**: `An error occurred when invoking java, message: %javaException;.`

- **Cause e raccomandazioni:** cause diverse possono causare questo errore. Controllare l'elenco seguente per informazioni su possibili analisi delle cause e raccomandazioni correlate.

    | Analisi delle cause                                               | Raccomandazione                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Quando il messaggio di errore contiene le stringhe "java.lang.OutOfMemory", "Spazio heap Java" e "doubleCapacity", si tratta in genere di un problema di gestione della memoria in una versione precedente di Integration Runtime. | Se si usa il self-hosted IR e la versione è precedente alla 3.20.7159.1, è consigliabile eseguire l'aggiornamento alla versione più recente. |
    | Quando il messaggio di errore contiene la stringa "java.lang.OutOfMemory", il runtime di integrazione non ha risorse sufficienti per elaborare i file. | Limitare le esecuzioni simultanee nel runtime di integrazione. Per il self-hosted IR, scalare fino a un computer potente con memoria uguale o superiore a 8 GB. |
    | Quando il messaggio di errore contiene la stringa "NullPointerReference", potrebbe trattarsi di un errore temporaneo. | Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico. |

### <a name="error-code-parquetinvalidfile"></a>Codice errore: ParquetInvalidFile

- **Messaggio**: `File is not a valid Parquet file.`

- **Causa:** si tratta di un problema di file Parquet.

- **Raccomandazione:** verificare se l'input è un file Parquet valido.


### <a name="error-code-parquetnotsupportedtype"></a>Codice errore: ParquetNotSupportedType

- **Messaggio**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa:** il formato Parquet non è supportato in Azure Data Factory.

- **Raccomandazione:** controllare i dati di origine selezionando [Formati di file](./supported-file-formats-and-compression-codecs.md)supportati e codec di compressione tramite l'attività di copia in Azure Data Factory .


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Codice errore: ParquetMissedDecimalPrecisionScale

- **Messaggio**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa:** la precisione e la scala dei numeri sono state analizzate, ma tali informazioni non sono state fornite.

- **Raccomandazione:** l'origine non restituisce le informazioni corrette su precisione e scala. Controllare la colonna del problema per le informazioni.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Codice errore: ParquetInvalidDecimalPrecisionScale

- **Messaggio**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa**: Lo schema non è valido.

- **Raccomandazione:** controllare la precisione e la scala della colonna del problema.


### <a name="error-code-parquetcolumnnotfound"></a>Codice errore: ParquetColumnNotFound

- **Messaggio**: `Column %column; does not exist in Parquet file.`

- **Causa:** lo schema di origine non corrisponde allo schema sink.

- **Raccomandazione:** controllare i mapping nell'attività. Assicurarsi che sia possibile eseguire il mapping della colonna di origine alla colonna sink corretta.


### <a name="error-code-parquetinvaliddataformat"></a>Codice errore: ParquetInvalidDataFormat

- **Messaggio**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa:** i dati non possono essere convertiti nel tipo specificato in mappings.source.

- **Raccomandazione:** controllare i dati di origine o specificare il tipo di dati corretto per questa colonna nel mapping della colonna dell'attività di copia. Per altre informazioni, vedere [Formati di file e codec di compressione supportati dall'attività di copia in Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Codice errore: ParquetDataCountNotMatchColumnCount

- **Messaggio**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa:** mancata corrispondenza tra il conteggio delle colonne di origine e il numero di colonne sink.

- **Raccomandazione:** verificare che il conteggio delle colonne di origine sia uguale al numero di colonne sink in 'mapping'.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Codice errore: ParquetDataTypeNotMatchColumnType

- **Messaggio**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Causa:** i dati dell'origine non possono essere convertiti nel tipo definito nel sink.

- **Raccomandazione:** specificare un tipo corretto in mapping.sink.


### <a name="error-code-parquetbridgeinvaliddata"></a>Codice errore: ParquetBridgeInvalidData

- **Messaggio**: `%message;`

- **Causa:** il valore dei dati ha superato il limite.

- **Raccomandazione:** ripetere l'operazione. Se il problema persiste, contattare Microsoft.


### <a name="error-code-parquetunsupportedinterpretation"></a>Codice errore: ParquetUnsupportedInterpretation

- **Messaggio**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Causa:** questo scenario non è supportato.

- **Raccomandazione**:  'ParquetInterpretFor' non deve essere 'sparkSql'.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Codice errore: ParquetUnsupportFileLevelCompressionOption

- **Messaggio**: `File level compression is not supported for Parquet.`

- **Causa:** questo scenario non è supportato.

- **Raccomandazione:** rimuovere 'CompressionType' nel payload.


### <a name="error-code-usererrorjniexception"></a>Codice errore: UserErrorJniException

- **Messaggio**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Causa:** non Java Virtual Machine possibile creare una macchina virtuale (JVM) perché sono impostati alcuni argomenti (globali) non validi.

- **Raccomandazione:** accedere al computer che ospita *ogni nodo* del proprio IR self-hosted. Verificare che la variabile di sistema sia impostata correttamente, come indicato di seguito: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Riavviare tutti i nodi di IR e quindi rieseguire la pipeline.


### <a name="arithmetic-overflow"></a>Overflow aritmetico

- **Sintomi:** si è verificato un messaggio di errore durante la copia dei file Parquet: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Causa:** attualmente sono supportati solo il decimale di precisione <= 38 e la lunghezza della parte integer <= 20 quando si copiano file da Oracle a Parquet. 

- **Soluzione:** come soluzione alternativa, è possibile convertire tutte le colonne con questo problema in VARCHAR2.


### <a name="no-enum-constant"></a>Nessuna costante di enumerazione

- **Sintomi:** si è verificato un messaggio di errore durante la copia dei dati in formato Parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` o: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Causa**: 

    Il problema potrebbe essere causato da spazi vuoti o caratteri speciali non supportati(ad esempio, {} ()\n\t=) nel nome della colonna, perché Parquet non supporta tale formato. 

    Ad esempio, un nome di colonna, ad *esempio contoso(test)* an parserà il tipo tra parentesi quadre dal [codice](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . L'errore viene generato perché non esiste un tipo di "test" di questo tipo.

    Per controllare i tipi supportati, passare al sito [apache/parquet-mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)di GitHub.

- **Risoluzione**: 

    Verificare se:
    - Nel nome della colonna sink sono presenti spazi vuoti.
    - Come nome della colonna viene usata la prima riga con spazi vuoti.
    - Il tipo OriginalType è supportato. Provare a evitare di usare questi caratteri speciali: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Codice errore: RestSinkCallFailed

- **Messaggio**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Causa:** questo errore si verifica quando Azure Data Factory l'endpoint REST tramite il protocollo HTTP e l'operazione di richiesta non riesce.

- **Raccomandazione:** controllare il codice di stato HTTP o il messaggio nel messaggio di errore e correggere il problema del server remoto.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Risposta di rete imprevista dal connettore REST

- **Sintomi:** l'endpoint riceve talvolta una risposta imprevista (400, 401, 403, 500) dal connettore REST.

- **Causa:** il connettore di origine REST usa l'URL e il metodo/intestazione/corpo HTTP dal servizio collegato/set di dati/copia dell'origine come parametri quando costruisce una richiesta HTTP. Il problema è probabilmente causato da alcuni errori in uno o più parametri specificati.

- **Risoluzione**: 
    - Usare 'curl' in una finestra del prompt dei comandi per verificare se il parametro è la causa **(devono** essere sempre incluse le intestazioni Accept e **User-Agent):**
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Se il comando restituisce la stessa risposta imprevista, correggere i parametri precedenti con 'curl' fino a quando non restituisce la risposta prevista. 

      È anche possibile usare "curl--help" per un utilizzo più avanzato del comando.

    - Se solo il connettore REST Data Factory restituisce una risposta imprevista, contattare il supporto tecnico Microsoft per altre informazioni sulla risoluzione dei problemi.
    
    - Si noti che 'curl' potrebbe non essere adatto per riprodurre un problema di convalida del certificato SSL. In alcuni scenari, il comando 'curl' è stato eseguito correttamente senza riscontrare problemi di convalida del certificato SSL. Tuttavia, quando lo stesso URL viene eseguito in un browser, non viene effettivamente restituito alcun certificato SSL per il client per stabilire una relazione di trust con il server.

      Per il caso precedente sono consigliati strumenti come **Postman** e **Fiddler.**


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Codice errore: SftpOperationFail

- **Messaggio**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Causa:** problema con l'operazione SFTP.

- **Raccomandazione:** controllare i dettagli dell'errore da SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Codice errore: SftpRenameOperationFail

- **Messaggio**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Causa:** il server SFTP non supporta la ridenominazione del file temporaneo.

- **Raccomandazione:** impostare "useTempFileRename" su false nel sink di copia per disabilitare il caricamento nel file temporaneo.


### <a name="error-code-sftpinvalidsftpcredential"></a>Codice errore: SftpInvalidSftpCredential

- **Messaggio**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Causa:** il contenuto della chiave privata viene recuperato dall'SDK o dall'insieme di credenziali delle chiavi di Azure, ma non è codificato correttamente.

- **Raccomandazione**:  

    Se il contenuto della chiave privata deriva dall'insieme di credenziali delle chiavi, il file di chiave originale può funzionare se viene caricato direttamente nel servizio collegato SFTP.

    Per altre informazioni, vedere [Copiare dati da e verso il server SFTP usando Azure Data Factory](./connector-sftp.md#use-ssh-public-key-authentication). Il contenuto della chiave privata è il contenuto della chiave privata SSH con codifica Base64.

    Codificare *l'intero* file di chiave privata originale con codifica base64 e archiviare la stringa codificata nell'insieme di credenziali delle chiavi. Il file di chiave privata originale è quello che può funzionare nel servizio collegato SFTP se si seleziona **Carica** dal file.

    Ecco alcuni esempi che è possibile usare per generare la stringa:

    - Usare il codice C#:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Usare il codice Python:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Usare uno strumento di conversione base64 di terze parti. È consigliabile [usare lo strumento di codifica in formato Base64.](https://www.base64encode.org/)

- **Causa:** è stato scelto il formato del contenuto della chiave errato.

- **Raccomandazione**:  

    La chiave privata SSH in formato PKCS#8 (inizia con "-----BEGIN ENCRYPTED PRIVATE KEY-----") non è attualmente supportata per accedere al server SFTP in Data Factory. 

    Per convertire la chiave nel formato di chiave SSH tradizionale, a partire da "-----BEGIN RSA PRIVATE KEY-----", eseguire i comandi seguenti:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Causa:** credenziali o contenuto della chiave privata non validi.

- **Raccomandazione:** per verificare se il file di chiave o la password sono corretti, verificare con strumenti come WinSCP.

### <a name="sftp-copy-activity-failed"></a>L'attività di copia SFTP non è riuscita

- **Sintomi:** 
  * Codice errore: UserErrorInvalidColumnMappingColumnNotFound 
  * Messaggio di errore: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Causa:** l'origine non include una colonna denominata "AccMngr".

- **Soluzione:** per determinare se la colonna "AccMngr" esiste, controllare la configurazione del set di dati mappando la colonna del set di dati di destinazione.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Codice errore: SftpFailedToConnectToSftpServer

- **Messaggio**: `Failed to connect to SFTP server '%server;'.`

- **Causa:** se il messaggio di errore contiene la stringa "L'operazione di lettura socket è scaduta dopo 30.000 millisecondi", una possibile causa è che per il server SFTP viene usato un tipo di servizio collegato non corretto. Ad esempio, si potrebbe usare il servizio collegato FTP per connettersi al server SFTP.

- **Raccomandazione:** controllare la porta del server di destinazione. Per impostazione predefinita, SFTP usa la porta 22.

- **Causa:** se il messaggio di errore contiene la stringa "La risposta del server non contiene l'identificazione del protocollo SSH", una possibile causa è che il server SFTP ha limitato la connessione. Data Factory più connessioni da scaricare dal server SFTP in parallelo e talvolta si verifica una limitazione del server SFTP. In genere, server diversi restituiscono errori diversi quando si verificano limitazioni.

- **Raccomandazione**:  

    Specificare il numero massimo di connessioni simultanee del set di dati SFTP come 1 ed eseguire nuovamente l'attività di copia. Se l'attività ha esito positivo, è possibile assicurarsi che la limitazione sia la causa.

    Se si vuole alzare di livello la velocità effettiva bassa, contattare l'amministratore di SFTP per aumentare il limite del numero di connessioni simultanee oppure è possibile eseguire una delle operazioni seguenti:

    * Se si usa il self-hosted IR, aggiungere l'INDIRIZZO IP del computer con self-hosted IR all'elenco elementi consentiti.
    * Se si usa l'Azure IR, aggiungere [Azure Integration Runtime indirizzi IP.](./azure-integration-runtime-ip-addresses.md) Se non si vuole aggiungere un intervallo di indirizzi IP all'elenco di indirizzi IP consentiti del server SFTP, usare invece il servizio di IR self-hosted.

## <a name="sharepoint-online-list"></a>Elenco SharePoint Online

### <a name="error-code-sharepointonlineauthfailed"></a>Codice errore: SharePointOnlineAuthFailed

- **Messaggio**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Causa:** l'ID e la chiave dell'entità servizio potrebbero non essere impostati correttamente.

- **Raccomandazione:** controllare l'applicazione registrata (ID entità servizio) e la chiave per verificare se sono impostate correttamente.


## <a name="xml-format"></a>Formato XML

### <a name="error-code-xmlsinknotsupported"></a>Codice errore: XmlSinkNotSupported

- **Messaggio**: `Write data in XML format is not supported yet, choose a different format!`

- **Causa:** un set di dati XML è stato usato come set di dati sink nell'attività di copia.

- **Raccomandazione:** usare un set di dati in un formato diverso da quello del set di dati sink.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Codice errore: XmlAttributeColumnNameConflict

- **Messaggio**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Causa:** è stato usato un prefisso di attributo che ha causato il conflitto.

- **Raccomandazione:** impostare un valore diverso per la proprietà "attributePrefix".


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Codice errore: XmlValueColumnNameConflict

- **Messaggio**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Causa:** uno dei nomi degli elementi figlio è stato usato come nome di colonna per il valore dell'elemento.

- **Raccomandazione:** impostare un valore diverso per la proprietà "valueColumn".


### <a name="error-code-xmlinvalid"></a>Codice errore: XmlInvalid

- **Messaggio**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Causa:** il formato del file XML di input non è corretto.

- **Raccomandazione:** correggere il file XML per renderlo ben formato.


## <a name="general-copy-activity-error"></a>Errore generale dell'attività di copia

### <a name="error-code-jrenotfound"></a>Codice errore: JreNotFound

- **Messaggio**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa:** il runtime di IR self-hosted non è in grado di trovare il runtime Java. Il runtime Java è necessario per la lettura di origini specifiche.

- **Raccomandazione:** controllare l'ambiente di runtime di integrazione, vedere [Usare l'Integration Runtime](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Codice errore: WildcardPathSinkNotSupported

- **Messaggio**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa:** il set di dati sink non supporta i valori con caratteri jolly.

- **Raccomandazione:** controllare il set di dati sink e riscrivere il percorso senza usare un valore jolly.


### <a name="fips-issue"></a>Problema FIPS

- **Sintomi:** attività Copy si verifica un errore in un computer a registrazione self-hosted abilitato per FIPS con il messaggio di errore seguente: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Causa:** questo errore può verificarsi quando si copiano dati con connettori come BLOB di Azure, SFTP e così via. FiPS (Federal Information Processing Standards) definisce un determinato set di algoritmi crittografici che possono essere usati. Quando la modalità FIPS è abilitata nel computer, alcune classi crittografiche da cui dipende l'attività di copia vengono bloccate in alcuni scenari.

- **Risoluzione:** informazioni sul motivo per cui non è più [consigliabile usare la "modalità FIPS"](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)e valutare se è possibile disabilitare FIPS nel computer con il ripristino di errore self-hosted.

    In alternativa, se si vuole consentire solo Azure Data Factory FIPS e fare in modo che le esecuzioni dell'attività riescano, eseguire le operazioni seguenti:

    1. Aprire la cartella in cui è installato il self-hosted IR. Il percorso è in *genere C:\Programmi\Microsoft Integration Runtime \<IR version> \Shared*.

    2. Aprire il file *diawp.exe.config* e quindi, alla fine della `<runtime>` sezione, aggiungere `<enforceFIPSPolicy enabled="false"/>` , come illustrato di seguito:

        ![Screenshot di una sezione del file diawp.exe.config con FIPS disabilitato.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Salvare il file e quindi riavviare il computer del servizio di installazione self-hosted.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Pagina Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)