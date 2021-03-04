---
title: Risolvere i problemi di Azure Data Factory connettori
description: Informazioni su come risolvere i problemi relativi ai connettori in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 9d8f940e3900c00b1c6f6623dfeff2d92ca85aa3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042437"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Risolvere i problemi di Azure Data Factory connettori

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i modi comuni per risolvere i problemi relativi ai connettori Azure Data Factory.

## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

### <a name="error-code-azurebloboperationfailed"></a>Codice di errore: AzureBlobOperationFailed

- **Messaggio**: "operazione BLOB non riuscita. ContainerName:% ContainerName;, percorso:% Path;. "

- **Causa**: un problema con l'operazione di archiviazione BLOB.

- **Raccomandazione**: per controllare i dettagli dell'errore, vedere [codici di errore di archiviazione BLOB](/rest/api/storageservices/blob-service-error-codes). Per ulteriore assistenza, contattare il team di archiviazione BLOB.


### <a name="invalid-property-during-copy-activity"></a>Proprietà non valida durante l'attività di copia

- **Messaggio**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Causa**: il tipo definito nel set di dati non è coerente con il tipo di origine o di sink definito nell'attività di copia.

- **Soluzione**: modificare il set di dati o la definizione JSON della pipeline per rendere coerenti i tipi, quindi eseguire di nuovo la distribuzione.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Messaggio di errore: Le dimensioni della richiesta sono troppo grandi

- **Sintomi**: quando si copiano i dati in Azure Cosmos DB con una dimensione del batch di scrittura predefinita, viene visualizzato l'errore seguente: `Request size is too large.`

- **Motivo**: Azure Cosmos DB limita le dimensioni di una singola richiesta a 2 MB. La formula è *size della richiesta = dimensioni del \* batch di scrittura del singolo documento*. Se le dimensioni del documento sono elevate, il comportamento predefinito comporterà una dimensione della richiesta troppo grande. È possibile ottimizzare le dimensioni del batch di scrittura.

- **Soluzione**: nel sink dell'attività di copia ridurre il valore delle *dimensioni del batch di scrittura* (il valore predefinito è 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Messaggio di errore: Violazione di vincolo di indice univoco

- **Sintomi**: quando si copiano i dati in Azure Cosmos DB, viene visualizzato l'errore seguente:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Causa**: Le cause possono essere due:

    - Causa 1: se si usa **Insert** come comportamento di scrittura, questo errore indica che i dati di origine hanno righe o oggetti con lo stesso ID.
    - Causa 2: se si usa **Upsert** come comportamento di scrittura e si imposta un'altra chiave univoca per il contenitore, questo errore indica che i dati di origine hanno righe o oggetti con ID diversi, ma lo stesso valore per la chiave univoca definita.

- **Risoluzione**: 

    - Per la prima ragione, impostare **Upsert** come comportamento di scrittura.
    - Per la seconda ragione, assicurarsi che ogni documento abbia un valore diverso per la chiave univoca definita.

### <a name="error-message-request-rate-is-large"></a>Messaggio di errore: La frequenza delle richieste è troppo elevata

- **Sintomi**: quando si copiano i dati in Azure Cosmos DB, viene visualizzato l'errore seguente:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Motivo**: il numero di unità richiesta (UR) usate è maggiore delle UR disponibili configurate in Azure Cosmos DB. Per informazioni su come Azure Cosmos DB calcola le UR, vedere [unità richiesta in Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Soluzione**: provare una delle due soluzioni seguenti:

    - Aumentare il numero *ur del contenitore* con un valore maggiore nel Azure Cosmos DB. Questa soluzione consentirà di migliorare le prestazioni dell'attività di copia, ma comporterà un maggiore costo in Azure Cosmos DB. 
    - Ridurre *writeBatchSize* a un valore minore, ad esempio 1000, e ridurre *parallelCopies* a un valore inferiore, ad esempio 1. Questa soluzione consentirà di ridurre le prestazioni dell'esecuzione della copia, ma non comporta costi maggiori in Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Colonne mancanti nel mapping delle colonne

- **Sintomi**: quando si importa uno schema per Azure Cosmos DB per il mapping di colonne, alcune colonne risultano mancanti. 

- **Motivo**: data factory deduce lo schema dai primi 10 Azure Cosmos DB documenti. Se alcune proprietà o colonne del documento non contengono valori, lo schema non viene rilevato da Data Factory e di conseguenza non viene visualizzato.

- **Soluzione**: è possibile ottimizzare la query come illustrato nel codice seguente per forzare la visualizzazione dei valori della colonna nel set di risultati con valori vuoti. Si supponga che la colonna *Impossibile* non sia presente nei primi 10 documenti. In alternativa, è possibile aggiungere manualmente la colonna per il mapping.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Messaggio di errore: GuidRepresentation per il lettore è CSharpLegacy

- **Sintomi**: quando si copiano dati da Azure Cosmos DB MongoAPI o MongoDB con il campo identificatore univoco universale (UUID), viene visualizzato l'errore seguente:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Motivo**: esistono due modi per rappresentare l'UUID in Binary JSON (BSON): UuidStardard e UuidLegacy. Per impostazione predefinita, viene usato UuidLegacy per leggere i dati. Se i dati UUID in MongoDB sono UuidStandard, verrà visualizzato un errore.

- **Soluzione**: nella stringa di connessione di MongoDB aggiungere l'opzione *uuidRepresentation = standard* . Per altre informazioni, vedere [Stringa di connessione MongoDB](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (API SQL)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Codice di errore: CosmosDbSqlApiOperationFailed

- **Messaggio**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Causa**: un problema con l'operazione CosmosDbSqlApi.

- **Raccomandazione**: per controllare i dettagli dell'errore, vedere [Azure Cosmos DB documento della Guida](../cosmos-db/troubleshoot-dot-net-sdk.md). Per ulteriori informazioni, contattare il team di Azure Cosmos DB.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Messaggio di errore: connessione sottostante chiusa: Impossibile stabilire una relazione di trust per il canale sicuro SSL/TLS.

- **Sintomi**: l'attività di copia ha esito negativo con l'errore seguente: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Motivo**: la convalida del certificato non è riuscita durante l'handshake TLS.

- **Soluzione: come** soluzione alternativa, usare la copia di staging per ignorare la convalida del Transport Layer Security (TLS) per Azure Data Lake storage Gen1. È necessario riprodurre questo problema e raccogliere la traccia di Network Monitor (Netmon), quindi coinvolgere il team di rete per verificare la configurazione della rete locale.

    ![Diagramma delle connessioni di Azure Data Lake Storage Gen1 per la risoluzione dei problemi.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Messaggio di errore: Errore del server remoto: (403) Accesso negato

- **Sintomi**: L'attività di copia ha esito negativo con l'errore seguente: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Motivo**: è possibile che l'entità servizio o l'identità gestita usata non disponga dell'autorizzazione per accedere a determinati file o cartelle.

- **Soluzione**: concedere le autorizzazioni appropriate per tutte le cartelle e le sottocartelle che è necessario copiare. Per altre informazioni, vedere [copiare dati da o verso Azure Data Lake storage Gen1 usando Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Messaggio di errore: Non è stato possibile ottenere il token di accesso tramite l'entità servizio. Errore ADAL: service_unavailable

- **Sintomi**: l'attività di copia ha esito negativo con l'errore seguente:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Motivo**: quando il server token di servizio (STS) di proprietà di Azure Active Directory non è disponibile, significa che è troppo occupato per gestire le richieste e restituisce l'errore HTTP 503. 

- **Soluzione**: Eseguire di nuovo l'attività di copia dopo alcuni minuti.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Codice di errore: ADLSGen2OperationFailed

- **Messaggio**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Cause e consigli**: cause diverse possono causare questo errore. Vedere l'elenco seguente per l'analisi delle cause possibili e le raccomandazioni correlate.

  | Analisi delle cause                                               | Recommendation                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Se Azure Data Lake Storage Gen2 genera un errore che indica che un'operazione non è riuscita.| Controllare il messaggio di errore dettagliato generato da Azure Data Lake Storage Gen2. Se si verifica un errore temporaneo, ripetere l'operazione. Per ulteriore assistenza, contattare il supporto di archiviazione di Azure e fornire l'ID richiesta in un messaggio di errore. |
  | Se il messaggio di errore contiene la stringa "Forbidden", l'entità servizio o l'identità gestita utilizzata potrebbe non disporre di autorizzazioni sufficienti per accedere a Azure Data Lake Storage Gen2. | Per risolvere questo errore, vedere [copiare e trasformare i dati in Azure Data Lake storage Gen2 usando Azure Data Factory](./connector-azure-data-lake-storage.md#service-principal-authentication). |
  | Se il messaggio di errore contiene la stringa "InternalServerError", l'errore viene restituito dal Azure Data Lake Storage Gen2. | L'errore potrebbe essere causato da un errore temporaneo. In questo caso, provare a ripetere l'operazione. Se il problema persiste, contattare il supporto di archiviazione di Azure e fornire l'ID richiesta dal messaggio di errore. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>La richiesta di Azure Data Lake Storage Gen2 account ha causato un errore di timeout

- **Messaggio**: 
  * Codice errore = `UserErrorFailedBlobFSOperation`
  * Messaggio di errore = `BlobFS operation failed for: A task was canceled.`

- **Causa**: il problema è causato dall'errore di timeout del sink Azure Data Lake storage Gen2, che in genere si verifica nel computer self-hosted Integration Runtime (IR).

- **Raccomandazione**: 

    - Inserire il computer IR indipendente e la destinazione Azure Data Lake Storage Gen2 account nella stessa area, se possibile. Ciò consente di evitare un errore di timeout casuale e ottenere prestazioni migliori.

    - Controllare se è presente un'impostazione di rete speciale, ad esempio ExpressRoute, e assicurarsi che la rete disponga di una larghezza di banda sufficiente. Si consiglia di ridurre l'impostazione dei processi simultanei del runtime di integrazione self-hosted quando la larghezza di banda complessiva è bassa. Questa operazione consente di evitare la competizione di risorse di rete tra più processi simultanei.

    - Se la dimensione del file è moderata o piccola, utilizzare una dimensione del blocco inferiore per la copia non binaria per attenuare tale errore di timeout. Per altre informazioni, vedere [blocco put di archiviazione BLOB](/rest/api/storageservices/put-block).

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

                  
## <a name="azure-files-storage"></a>Archiviazione File di Azure

### <a name="error-code-azurefileoperationfailed"></a>Codice di errore: AzureFileOperationFailed

- **Messaggio**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Causa**: è stato riscontrato un problema con l'operazione di archiviazione file di Azure.

- **Raccomandazione**: per controllare i dettagli dell'errore, vedere la [Guida file di Azure](/rest/api/storageservices/file-service-error-codes). Per ulteriori informazioni, contattare il team di File di Azure.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Analisi delle sinapsi di Azure, database SQL di Azure e SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Codice di errore: SqlFailedToConnect

- **Messaggio**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Cause e consigli**: cause diverse possono causare questo errore. Vedere l'elenco seguente per l'analisi delle cause possibili e le raccomandazioni correlate.

    | Analisi delle cause                                               | Recommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Per Azure SQL, se il messaggio di errore contiene la stringa "SqlErrorNumber = 47073", significa che l'accesso alla rete pubblica viene negato nell'impostazione di connettività. | Nel firewall SQL di Azure impostare l'opzione **Nega accesso alla rete pubblica** su *No*. Per altre informazioni, vedere [impostazioni di connettività SQL di Azure](../azure-sql/database/connectivity-settings.md#deny-public-network-access). |
    | Per Azure SQL, se il messaggio di errore contiene un codice di errore SQL, ad esempio "SqlErrorNumber = [ErrorCode]", vedere la guida alla risoluzione dei problemi di SQL Azure. | Per indicazioni, vedere [risolvere i problemi di connettività e altri errori con il database SQL di Azure e Azure sql istanza gestita](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | Verificare se la porta 1433 è nell'elenco Consenti del firewall. | Per ulteriori informazioni, vedere [porte utilizzate da SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Se il messaggio di errore contiene la stringa "SqlException", database SQL l'errore indica che un'operazione specifica non è riuscita. | Per ulteriori informazioni, eseguire una ricerca in base al codice di errore SQL in [errori del motore di database](/sql/relational-databases/errors-events/database-engine-events-and-errors). Per ulteriori informazioni, contattare il supporto SQL di Azure. |
    | Se si tratta di un problema temporaneo, ad esempio una connessione di rete instabile, aggiungere nuovi tentativi nei criteri attività per attenuare il problema. | Per altre informazioni, vedere [Pipeline e attività in Azure Data Factory](./concepts-pipelines-activities.md#activity-policy). |
    | Se il messaggio di errore contiene la stringa "client con indirizzo IP"... " non è consentito accedere al server e si sta provando a connettersi al database SQL di Azure. l'errore è in genere causato da un problema del firewall del database SQL di Azure. | Nella configurazione di Azure SQL Server Firewall abilitare l'opzione **Consenti ai servizi e alle risorse di Azure di accedere al server** . Per altre informazioni, vedere il [database SQL di Azure e le regole del firewall IP della sinapsi di Azure](../azure-sql/database/firewall-configure.md). |
    
### <a name="error-code-sqloperationfailed"></a>Codice di errore: SqlOperationFailed

- **Messaggio**: `A database operation failed. Please search error to get more details.`

- **Cause e consigli**: cause diverse possono causare questo errore. Vedere l'elenco seguente per l'analisi delle cause possibili e le raccomandazioni correlate.

    | Analisi delle cause                                               | Recommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Se il messaggio di errore contiene la stringa "SqlException", il database SQL genera un errore che indica che un'operazione specifica non è riuscita. | Se l'errore SQL non è chiaro, provare a modificare il database con il livello di compatibilità più recente "150". Può generare gli errori SQL più recenti della versione. Per altre informazioni, vedere la [documentazione](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Per ulteriori informazioni sulla risoluzione dei problemi di SQL, eseguire una ricerca in base al codice di errore SQL in [errori del motore di database](/sql/relational-databases/errors-events/database-engine-events-and-errors). Per ulteriori informazioni, contattare il supporto SQL di Azure. |
    | Se il messaggio di errore contiene la stringa "PdwManagedToNativeInteropException", è in genere causato da una mancata corrispondenza tra le dimensioni della colonna di origine e del sink. | Controllare le dimensioni delle colonne di origine e sink. Per ulteriori informazioni, contattare il supporto SQL di Azure. |
    | Se il messaggio di errore contiene la stringa "InvalidOperationException", è in genere causato da dati di input non validi. | Per identificare la riga in cui si è verificato il problema, abilitare la funzionalità di tolleranza di errore nell'attività di copia, che può reindirizzare le righe problematiche all'archiviazione per un'ulteriore analisi. Per ulteriori informazioni, vedere [tolleranza di errore dell'attività di copia in Azure Data Factory](./copy-activity-fault-tolerance.md). |


### <a name="error-code-sqlunauthorizedaccess"></a>Codice di errore: SqlUnauthorizedAccess

- **Messaggio**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: le credenziali non sono corrette oppure l'account di accesso non può accedere al database SQL.

- **Consiglio**: verificare che l'account di accesso disponga di autorizzazioni sufficienti per accedere al database SQL.


### <a name="error-code-sqlopenconnectiontimeout"></a>Codice di errore: SqlOpenConnectionTimeout

- **Messaggio**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa**: il problema potrebbe essere un errore temporaneo del database SQL.

- **Raccomandazione**: ripetere l'operazione per aggiornare la stringa di connessione del servizio collegato con un valore di timeout di connessione maggiore.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Codice di errore: SqlAutoCreateTableTypeMapFailed

- **Messaggio**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa**: la tabella di creazione non può soddisfare il requisito di origine.

- **Raccomandazione**: aggiornare il tipo di colonna nei *mapping* oppure creare manualmente la tabella di sink nel server di destinazione.


### <a name="error-code-sqldatatypenotsupported"></a>Codice di errore: SqlDataTypeNotSupported

- **Messaggio**: `A database operation failed. Check the SQL errors.`

- **Causa**: se il problema si verifica nell'origine SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati supera l'intervallo del tipo di logica (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Raccomandazione**: eseguire il cast del tipo alla stringa nella query SQL di origine o, nel mapping della colonna dell'attività di copia, modificare il tipo di colonna in *stringa*.

- **Causa**: se il problema si verifica nel sink SQL e l'errore è correlato all'overflow di SqlDateTime, il valore dei dati supera l'intervallo consentito nella tabella di sink.

- **Raccomandazione**: aggiornare il tipo di colonna corrispondente al tipo *datetime2* nella tabella sink.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Codice di errore: SqlInvalidDbStoredProcedure

- **Messaggio**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Motivo**: il stored procedure specificato non è valido. La ragione potrebbe essere che il stored procedure non restituisce alcun dato.

- **Raccomandazione**: convalidare il stored procedure usando gli strumenti di SQL. Verificare che il stored procedure possa restituire i dati.


### <a name="error-code-sqlinvaliddbquerystring"></a>Codice di errore: SqlInvalidDbQueryString

- **Messaggio**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Motivo**: la query SQL specificata non è valida. La ragione potrebbe essere che la query non restituisce alcun dato.

- **Raccomandazione**: convalidare la query SQL usando gli strumenti di SQL. Assicurarsi che la query possa restituire i dati.


### <a name="error-code-sqlinvalidcolumnname"></a>Codice di errore: SqlInvalidColumnName

- **Messaggio**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa**: Impossibile trovare la colonna perché la configurazione potrebbe non essere corretta.

- **Raccomandazione**: verificare la colonna nella query, *struttura* nel set di dati e *mapping* nell'attività.


### <a name="error-code-sqlbatchwritetimeout"></a>Codice di errore: SqlBatchWriteTimeout

- **Messaggio**: `Timeouts in SQL write operation.`

- **Causa**: il problema potrebbe essere causato da un errore temporaneo del database SQL.

- **Raccomandazione**: ripetere l'operazione. Se il problema persiste, contattare il supporto SQL di Azure.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Codice di errore: SqlBatchWriteTransactionFailed

- **Messaggio**: `SQL transaction commits failed.`

- **Motivo**: se i dettagli dell'eccezione indicano costantemente un timeout della transazione, la latenza di rete tra il runtime di integrazione e il database è superiore alla soglia predefinita di 30 secondi.

- **Raccomandazione**: aggiornare la stringa di connessione del servizio collegato a SQL con un valore di *timeout della connessione* maggiore o uguale a 120 e rieseguire l'attività.

- **Causa**: se i dettagli dell'eccezione indicano in modo intermittente che la connessione SQL è interruppe, potrebbe trattarsi di un errore di rete temporaneo o di un problema lato database SQL.

- **Raccomandazione**: ritentare l'attività ed esaminare le metriche sul lato del database SQL.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Codice di errore: SqlBulkCopyInvalidColumnLength

- **Messaggio**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: la copia bulk SQL non è riuscita perché è stata ricevuta una lunghezza di colonna non valida dal client bcp (Bulk Copy Program Utility).

- **Raccomandazione**: per identificare la riga in cui si è verificato il problema, abilitare la funzionalità di tolleranza di errore nell'attività di copia. In questo modo è possibile reindirizzare le righe problematiche all'archiviazione per un'ulteriore analisi. Per ulteriori informazioni, vedere [tolleranza di errore dell'attività di copia in Azure Data Factory](./copy-activity-fault-tolerance.md).


### <a name="error-code-sqlconnectionisclosed"></a>Codice di errore: SqlConnectionIsClosed

- **Messaggio**: `The connection is closed by SQL Database.`

- **Motivo**: la connessione SQL viene chiusa dal database SQL quando un'esecuzione simultanea elevata e il server terminano la connessione.

- **Raccomandazione**: ripetere la connessione. Se il problema persiste, contattare il supporto SQL di Azure.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Messaggio di errore: Conversione non riuscita durante la conversione di una stringa di caratteri nel tipo di dati uniqueidentifier

- **Sintomi**: quando si copiano dati da un'origine dati tabulare, ad esempio SQL Server, in Azure sinapsi Analytics usando la copia di staging e la polibase, viene visualizzato l'errore seguente:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Motivo**: la polibase di Azure sinapsi Analytics non può convertire una stringa vuota in un GUID.

- **Soluzione**: nel sink dell'attività di copia, in impostazioni di base, impostare l'opzione **Usa tipo predefinito** su *false*.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Messaggio di errore: Tipo di dati previsto: DECIMAL(x, x), valore danneggiato

- **Sintomi**: quando si copiano dati da un'origine dati tabulare, ad esempio SQL Server, in Azure sinapsi Analytics usando la copia di staging e la polibase, viene visualizzato l'errore seguente:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Motivo**: la polibase di Azure sinapsi Analytics non può inserire una stringa vuota (valore null) in una colonna decimale.

- **Soluzione**: nel sink dell'attività di copia, in impostazioni di base, impostare l'opzione **Usa tipo predefinito** su false.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Messaggio di errore: messaggio eccezione Java: HdfsBridge:: CreateRecordReader

- **Sintomi**: i dati vengono copiati in Azure sinapsi Analytics usando la polibase e viene visualizzato l'errore seguente:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Motivo**: la ragione potrebbe essere che lo schema (larghezza totale della colonna) è troppo grande (maggiore di 1 MB). Verificare lo schema della tabella di Azure sinapsi Analytics di destinazione aggiungendo la dimensione di tutte le colonne:

    - Int = 4 byte
    - Bigint = 8 byte
    - Varchar (n), char (n), Binary (n), varbinary (n) = n byte
    - Nvarchar (n), nchar (n) = n * 2 byte
    - Data = 6 byte
    - DateTime/(2), smalldatetime = 16 byte
    - DateTimeOffset = 20 byte
    - Decimale = 19 byte
    - Float = 8 byte
    - Denaro = 8 byte
    - Smallmoney = 4 byte
    - Real = 4 byte
    - Smallint = 2 byte
    - Tempo = 12 byte
    - Tinyint = 1 byte

- **Risoluzione**: 
    - Ridurre la larghezza della colonna a meno di 1 MB.
    - In alternativa, usare un approccio di inserimento bulk disabilitando la funzionalità di base.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Messaggio di errore: La condizione specificata con le intestazioni condizionali HTTP non è stata soddisfatta

- **Sintomi**: usare query SQL per eseguire il pull dei dati da Azure sinapsi Analytics e ricevere l'errore seguente:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Causa**: Azure sinapsi Analytics ha rilevato un problema durante l'esecuzione di query sulla tabella esterna in archiviazione di Azure.

- **Soluzione**: eseguire la stessa query in SQL Server Management Studio (SSMS) e verificare se si ottiene lo stesso risultato. In tal caso, aprire un ticket di supporto per Azure sinapsi Analytics e fornire il nome del server e del database di Azure sinapsi Analytics.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Il livello di prestazioni è basso e genera un errore di copia

- **Sintomi**: i dati vengono copiati nel database SQL di Azure e viene visualizzato l'errore seguente: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Motivo**: il database SQL di Azure S1 ha raggiunto I limiti di input/output (I/O).

- **Soluzione**: aggiornare il livello di prestazioni del database SQL di Azure per risolvere il problema. 


### <a name="sql-table-cant-be-found"></a>Impossibile trovare la tabella SQL 

- **Sintomi**: i dati vengono copiati da un ambiente ibrido a una tabella SQL Server locale e viene visualizzato l'errore seguente:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Causa**: l'account SQL corrente non dispone di autorizzazioni sufficienti per eseguire le richieste rilasciate da .NET SqlBulkCopy. WriteToServer.

- **Soluzione**: passare a un account SQL con privilegi più elevati.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Messaggio di errore: dati stringa o binari troncati

- **Sintomi**: si verifica un errore durante la copia dei dati in una tabella di Azure SQL Server locale. 

- **Motivo**: la definizione dello schema della tabella SQL CX ha una o più colonne con una lunghezza inferiore al previsto.

- **Soluzione**: per risolvere il problema, provare a eseguire le operazioni seguenti:

    1. Per risolvere il problema relativo alle righe, applicare la [tolleranza di errore](./copy-activity-fault-tolerance.md)di sink SQL, in particolare "redirectIncompatibleRowSettings".

        > [!NOTE]
        > La tolleranza di errore potrebbe richiedere tempi di esecuzione aggiuntivi, che potrebbero comportare costi più elevati.

    2. Controllare i dati reindirizzati rispetto alla lunghezza della colonna dello schema della tabella SQL per vedere quali colonne è necessario aggiornare.

    3. Aggiornare lo schema della tabella di conseguenza.


## <a name="azure-table-storage"></a>Archiviazione tabelle di Azure

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Codice di errore: AzureTableDuplicateColumnsFromSource

- **Messaggio**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Causa**: le colonne di origine duplicate potrebbero verificarsi per uno dei motivi seguenti:
   * Il database viene usato come join di tabella di origine e applicato.
   * Sono presenti file CSV non strutturati con nomi di colonna duplicati nella riga di intestazione.

- **Raccomandazione**: controllare e correggere le colonne di origine in base alle esigenze.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Codice di errore: DB2DriverRunFailed

- **Messaggio**: `Error thrown from driver. Sql code: '%code;'`

- **Motivo**: se il messaggio di errore contiene la stringa "SQLSTATE = 51002 SqlCode =-805", seguire il "suggerimento" in [copiare i dati da DB2 usando Azure Data Factory](./connector-db2.md#linked-service-properties).

- **Raccomandazione**: provare a impostare "NULLID" nella `packageCollection`  Proprietà.


## <a name="delimited-text-format"></a>Formato di testo delimitato

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Codice di errore: DelimitedTextColumnNameNotAllowNull

- **Messaggio**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Motivo**: quando ' firstRowAsHeader ' è impostato nell'attività, la prima riga viene utilizzata come nome di colonna. Questo errore indica che la prima riga contiene un valore vuoto (ad esempio, "Columna, ColumnB").

- **Raccomandazione**: controllare la prima riga e correggere il valore se è vuoto.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Codice di errore: DelimitedTextMoreColumnsThanDefined

- **Messaggio**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Cause e consigli**: cause diverse possono causare questo errore. Vedere l'elenco seguente per l'analisi delle cause possibili e le raccomandazioni correlate.

  | Analisi delle cause                                               | Recommendation                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Il numero di colonne della riga problematica è maggiore del numero di colonne della prima riga. Potrebbe essere causato da un problema di dati o da un delimitatore di colonna non corretto o da impostazioni carattere virgolette. | Ottenere il conteggio delle righe dal messaggio di errore, controllare la colonna della riga e correggere i dati. |
  | Se il numero di colonne previsto è "1" in un messaggio di errore, è possibile che siano state specificate impostazioni di compressione o di formato errate, che hanno causato la Data Factory di analizzare i file in modo errato. | Controllare le impostazioni di formato per assicurarsi che corrispondano ai file di origine. |
  | Se l'origine è una cartella, i file nella cartella specificata potrebbero avere uno schema diverso. | Verificare che i file nella cartella specificata abbiano lo stesso schema. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service e Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Codice di errore: DynamicsCreateServiceClientError

- **Messaggio**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Causa**: il problema è un problema temporaneo sul lato server Dynamics.

- **Raccomandazione**:  Eseguire di nuovo la pipeline. Se l'errore persiste, provare a ridurre il parallelismo. Se il problema persiste, contattare il supporto tecnico Dynamics.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Colonne mancanti quando si importa uno schema o un'anteprima dei dati

- **Sintomi**: alcune colonne risultano mancanti quando si importa uno schema o si visualizza un'anteprima dei dati. Messaggio di errore: `The valid structure information (column name and type) are required for Dynamics source.`

- **Causa**: questo problema si verifica in base alla progettazione, perché Data Factory non è in grado di visualizzare colonne che non contengono valori nei primi 10 record. Assicurarsi che le colonne aggiunte siano nel formato corretto. 

- **Raccomandazione**: aggiungere manualmente le colonne nella scheda mapping.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Codice di errore: DynamicsMissingTargetForMultiTargetLookupField

- **Messaggio**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Causa**: la colonna di destinazione non esiste nell'origine o nel mapping delle colonne.

- **Raccomandazione**:  
  1. Verificare che l'origine contenga la colonna di destinazione. 
  2. Aggiungere la colonna di destinazione nel mapping delle colonne. Verificare che la colonna sink sia nel formato *{fieldname} @EntityReference*.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Codice di errore: DynamicsInvalidTargetForMultiTargetLookupField

- **Messaggio**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Motivo**: viene fornito un nome di entità errato come entità di destinazione di un campo di ricerca con più destinazioni.

- **Raccomandazione**: specificare un nome di entità valido per il campo di ricerca con più destinazioni.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Codice di errore: DynamicsInvalidTypeForMultiTargetLookupField

- **Messaggio**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Motivo**: il valore nella colonna di destinazione non è una stringa.

- **Raccomandazione**: specificare una stringa valida nella colonna destinazione di ricerca multitarget.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Codice di errore: DynamicsFailedToRequetServer

- **Messaggio**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Motivo**: il server Dynamics è instabile o inaccessibile oppure si sono verificati problemi nella rete.

- **Suggerimento**: per altre informazioni, controllare la connettività di rete o controllare il log di Dynamics server. Per ulteriori informazioni, contattare il supporto tecnico Dynamics.
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Codice di errore: FtpFailedToConnectToFtpServer

- **Messaggio**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Motivo**: per il server FTP è possibile utilizzare un tipo di servizio collegato errato, ad esempio utilizzando il servizio collegato FTP sicuro (SFTP) per connettersi a un server FTP.

- **Consiglio**: controllare la porta del server di destinazione. FTP utilizza la porta 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Codice di errore: HttpFileFailedToRead

- **Messaggio**: `Failed to read data from http server. Check the error from http server：%message;`

- **Motivo**: questo errore si verifica quando Azure Data Factory comunica con il server http, ma l'operazione di richiesta HTTP ha esito negativo.

- **Raccomandazione**: controllare il codice di stato HTTP nel messaggio di errore e correggere il problema del server remoto.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Codice di errore: ArgumentOutOfRangeException

- **Messaggio**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Motivo**: in data factory i valori DateTime sono supportati nell'intervallo compreso tra 0001-01-01 00:00:00 e 9999-12-31 23:59:59. Tuttavia, Oracle supporta un intervallo più ampio di valori DateTime, ad esempio BC Century o min/sec>59, che causa un errore in Data Factory.

- **Raccomandazione**: 

    Per verificare se il valore in Oracle è compreso nell'intervallo di Data Factory, eseguire `select dump(<column name>)` . 

    Per informazioni sulla sequenza di byte nel risultato, vedere [come vengono archiviate le date in Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>Formato ORC

### <a name="error-code-orcjavainvocationexception"></a>Codice di errore: OrcJavaInvocationException

- **Messaggio**: `An error occurred when invoking Java, message: %javaException;.`
- **Cause e consigli**: cause diverse possono causare questo errore. Vedere l'elenco seguente per l'analisi delle cause possibili e le raccomandazioni correlate.

    | Analisi delle cause                                               | Recommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Quando il messaggio di errore contiene le stringhe "Java. lang. OutOfMemory", "spazio heap Java" e "doubleCapacity", si tratta in genere di un problema di gestione della memoria in una versione precedente di Integration Runtime. | Se si usa Integration Runtime self-hosted, è consigliabile eseguire l'aggiornamento alla versione più recente. |
    | Quando il messaggio di errore contiene la stringa "Java. lang. OutOfMemory", il runtime di integrazione non dispone di risorse sufficienti per elaborare i file. | Limitare le esecuzioni simultanee nel runtime di integrazione. Per il runtime di integrazione self-hosted, passare a un computer potente con memoria uguale o maggiore di 8 GB. |
    |Se il messaggio di errore contiene la stringa "NullPointerReference", potrebbe trattarsi di un errore temporaneo. | Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico. |
    | Se il messaggio di errore contiene la stringa "BufferOverflowException", potrebbe trattarsi di un errore temporaneo. | Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico. |
    | Quando il messaggio di errore contiene la stringa "Java. lang. ClassCastException:org. Apache. Hadoop. hive. serde2. io. HiveCharWritable non può essere eseguito il cast in org. Apache. Hadoop. io. Text", la causa potrebbe essere un problema di conversione del tipo all'interno del runtime Java. In genere, significa che i dati di origine non possono essere gestiti correttamente nel runtime Java. | Si tratta di un problema di dati. Provare a usare una stringa anziché char o varchar nei dati in formato ORC. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Codice di errore: OrcDateTimeExceedLimit

- **Messaggio**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Causa**: se il valore DateTime è "0001-01-01 00:00:00", potrebbe essere causato dalle differenze tra il [Calendario Giuliano e il calendario gregoriano](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Raccomandazione**: controllare il valore dei segni di spunta ed evitare di usare il valore datetime ' 0001-01-01 00:00:00'.


## <a name="parquet-format"></a>Formato Parquet

### <a name="error-code-parquetjavainvocationexception"></a>Codice di errore: ParquetJavaInvocationException

- **Messaggio**: `An error occurred when invoking java, message: %javaException;.`

- **Cause e consigli**: cause diverse possono causare questo errore. Vedere l'elenco seguente per l'analisi delle cause possibili e le raccomandazioni correlate.

    | Analisi delle cause                                               | Recommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Quando il messaggio di errore contiene le stringhe "Java. lang. OutOfMemory", "spazio heap Java" e "doubleCapacity", si tratta in genere di un problema di gestione della memoria in una versione precedente di Integration Runtime. | Se si usa il runtime di integrazione self-hosted e la versione è precedente a 3.20.7159.1, è consigliabile eseguire l'aggiornamento alla versione più recente. |
    | Quando il messaggio di errore contiene la stringa "Java. lang. OutOfMemory", il runtime di integrazione non dispone di risorse sufficienti per elaborare i file. | Limitare le esecuzioni simultanee nel runtime di integrazione. Per il runtime di integrazione self-hosted, è possibile scalare fino a un computer potente con memoria uguale o maggiore di 8 GB. |
    | Quando il messaggio di errore contiene la stringa "NullPointerReference", potrebbe trattarsi di un errore temporaneo. | Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico. |

### <a name="error-code-parquetinvalidfile"></a>Codice di errore: ParquetInvalidFile

- **Messaggio**: `File is not a valid Parquet file.`

- **Causa**: si tratta di un problema di file parquet.

- **Raccomandazione**: verificare se l'input è un file parquet valido.


### <a name="error-code-parquetnotsupportedtype"></a>Codice di errore: ParquetNotSupportedType

- **Messaggio**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Motivo**: il formato parquet non è supportato in Azure Data Factory.

- **Raccomandazione**: controllare i dati di origine passando a [formati di file supportati e codec di compressione per attività di copia in Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Codice di errore: ParquetMissedDecimalPrecisionScale

- **Messaggio**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Motivo**: la precisione e la scala dei numeri sono state analizzate, ma non sono state fornite informazioni di questo tipo.

- **Raccomandazione**: l'origine non restituisce le informazioni corrette sulla precisione e sulla scala. Controllare le informazioni nella colonna problema.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Codice di errore: ParquetInvalidDecimalPrecisionScale

- **Messaggio**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa**: Lo schema non è valido.

- **Consiglio**: controllare la precisione e la scala della colonna problema.


### <a name="error-code-parquetcolumnnotfound"></a>Codice di errore: ParquetColumnNotFound

- **Messaggio**: `Column %column; does not exist in Parquet file.`

- **Causa**: lo schema di origine è una mancata corrispondenza con lo schema del sink.

- **Raccomandazione**: controllare i mapping nell'attività. Verificare che sia possibile eseguire il mapping della colonna di origine alla colonna di sink corretta.


### <a name="error-code-parquetinvaliddataformat"></a>Codice di errore: ParquetInvalidDataFormat

- **Messaggio**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: i dati non possono essere convertiti nel tipo specificato in mappings. Source.

- **Raccomandazione**: controllare i dati di origine o specificare il tipo di dati corretto per la colonna nel mapping delle colonne dell'attività di copia. Per ulteriori informazioni, vedere [formati di file supportati e codec di compressione per attività di copia in Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Codice di errore: ParquetDataCountNotMatchColumnCount

- **Messaggio**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa**: mancata corrispondenza tra il numero di colonne di origine e il numero di colonne del sink.

- **Raccomandazione**: verificare che il numero di colonne di origine corrisponda al numero di colonne del sink in ' mapping '.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Codice di errore: ParquetDataTypeNotMatchColumnType

- **Messaggio**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Causa**: i dati dell'origine non possono essere convertiti nel tipo definito nel sink.

- **Raccomandazione**: specificare un tipo corretto in mapping. sink.


### <a name="error-code-parquetbridgeinvaliddata"></a>Codice di errore: ParquetBridgeInvalidData

- **Messaggio**: `%message;`

- **Motivo**: il valore dei dati ha superato il limite.

- **Raccomandazione**: ripetere l'operazione. Se il problema persiste, contattare Microsoft.


### <a name="error-code-parquetunsupportedinterpretation"></a>Codice di errore: ParquetUnsupportedInterpretation

- **Messaggio**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Motivo**: questo scenario non è supportato.

- **Raccomandazione**:  'ParquetInterpretFor' non deve essere 'sparkSql'.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Codice di errore: ParquetUnsupportFileLevelCompressionOption

- **Messaggio**: `File level compression is not supported for Parquet.`

- **Motivo**: questo scenario non è supportato.

- **Raccomandazione**: rimuovere ' CompressionType ' nel payload.


### <a name="error-code-usererrorjniexception"></a>Codice di errore: UserErrorJniException

- **Messaggio**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Causa**: non è possibile creare un Java Virtual Machine (JVM) perché sono stati impostati alcuni argomenti non validi (globali).

- **Raccomandazione**: accedere al computer che ospita *ogni nodo* del runtime di integrazione self-hosted. Verificare che la variabile di sistema sia impostata correttamente, come segue: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Riavviare tutti i nodi IR e quindi eseguire di nuovo la pipeline.


### <a name="arithmetic-overflow"></a>Overflow aritmetico

- **Sintomi**: si è verificato un messaggio di errore durante la copia dei file parquet: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Motivo**: attualmente sono supportate solo le cifre decimali della precisione <= 38 e la lunghezza della parte Integer <= 20 quando si copiano i file da Oracle a parquet. 

- **Soluzione: come** soluzione alternativa, è possibile convertire qualsiasi colonna con questo problema in VARCHAR2.


### <a name="no-enum-constant"></a>Nessuna costante enum

- **Sintomi**: si è verificato un messaggio di errore durante la copia dei dati in formato parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` o: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Causa**: 

    Il problema potrebbe essere causato da spazi vuoti o caratteri speciali non supportati (ad esempio,; {} () \n\t =) nel nome della colonna, perché parquet non supporta tale formato. 

    Ad esempio, un nome di colonna come *Contoso (test)* analizzerà il tipo tra parentesi quadre dal [codice](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . L'errore viene generato perché non esiste un tipo di test di questo tipo.

    Per controllare i tipi supportati, visitare il [sito GitHub Apache/parquet-Mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Risoluzione**: 

    Controllare per verificare se:
    - Il nome della colonna sink contiene spazi vuoti.
    - La prima riga con spazi vuoti viene utilizzata come nome di colonna.
    - Il tipo OriginalType è supportato. Provare a evitare di usare i caratteri speciali seguenti: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Codice di errore: RestSinkCallFailed

- **Messaggio**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Motivo**: questo errore si verifica quando Azure Data Factory comunica con l'endpoint REST sul protocollo http e l'operazione di richiesta ha esito negativo.

- **Consiglio**: controllare il codice di stato http o il messaggio nel messaggio di errore e correggere il problema del server remoto.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Risposta di rete imprevista dal connettore REST

- **Sintomi**: l'endpoint a volte riceve una risposta imprevista (400, 401, 403, 500) dal connettore Rest.

- **Causa**: il connettore di origine Rest usa l'URL e il metodo/intestazione/corpo HTTP dal servizio collegato/set di dati/copia origine come parametri quando crea una richiesta HTTP. Il problema è probabilmente causato da alcuni errori in uno o più parametri specificati.

- **Risoluzione**: 
    - Usare ' curl ' in una finestra del prompt dei comandi per verificare se il parametro è la provocazione (le intestazioni **Accept** e **User-Agent** devono essere sempre incluse):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Se il comando restituisce la stessa risposta imprevista, correggere i parametri precedenti con ' curl ' fino a quando non viene restituita la risposta prevista. 

      È anche possibile usare "curl--Help" per un uso più avanzato del comando.

    - Se solo il connettore REST Data Factory restituisce una risposta imprevista, contattare il supporto tecnico Microsoft per ulteriori informazioni sulla risoluzione dei problemi.
    
    - Si noti che ' curl ' potrebbe non essere adatto per riprodurre un problema di convalida del certificato SSL. In alcuni scenari, il comando ' curl ' è stato eseguito correttamente senza riscontrare problemi di convalida del certificato SSL. Tuttavia, quando lo stesso URL viene eseguito in un browser, non viene effettivamente restituito alcun certificato SSL affinché il client stabilisca una relazione di trust con il server.

      Per il caso precedente è consigliabile usare strumenti come il **post** e **Fiddler** .


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Codice di errore: SftpOperationFail

- **Messaggio**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Causa**: è stato riscontrato un problema con l'operazione SFTP.

- **Consiglio**: controllare i dettagli dell'errore da SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Codice di errore: SftpRenameOperationFail

- **Messaggio**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Motivo**: il server SFTP non supporta la ridenominazione del file temporaneo.

- **Raccomandazione**: impostare "useTempFileRename" su false nel sink di copia per disabilitare il caricamento nel file temporaneo.


### <a name="error-code-sftpinvalidsftpcredential"></a>Codice di errore: SftpInvalidSftpCredential

- **Messaggio**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Motivo**: il contenuto della chiave privata viene recuperato da Azure Key Vault o SDK, ma non è codificato correttamente.

- **Raccomandazione**:  

    Se il contenuto della chiave privata è dall'insieme di credenziali delle chiavi, il file di chiave originale può funzionare se lo si carica direttamente nel servizio collegato SFTP.

    Per altre informazioni, vedere [copiare dati da e verso il server SFTP usando Azure Data Factory](./connector-sftp.md#use-ssh-public-key-authentication). Il contenuto della chiave privata è contenuto della chiave privata SSH con codifica Base64.

    Codificare l'intero file di chiave privata originale con codifica Base64 e archiviare la stringa codificata nell' *insieme* di credenziali delle chiavi. Il file di chiave privata originale è quello che può funzionare nel servizio collegato SFTP se si seleziona **carica** dal file.

    Di seguito sono riportati alcuni esempi che è possibile usare per generare la stringa:

    - Usa codice C#:

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

    - Usare uno strumento di conversione Base64 di terze parti. È consigliabile usare lo strumento [codifica in formato Base64](https://www.base64encode.org/) .

- **Motivo**: è stato scelto il formato del contenuto della chiave errato.

- **Raccomandazione**:  

    La chiave privata SSH del formato PKCS # 8 (inizia con "-----l'inizio della chiave privata CRITTOGRAFAta-----") non è attualmente supportata per accedere al server SFTP in Data Factory. 

    Per convertire la chiave nel formato di chiave SSH tradizionale, a partire da "-----avviare la chiave privata RSA-----", eseguire i comandi seguenti:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Cause**: credenziali non valide o contenuto della chiave privata.

- **Suggerimento**: per verificare se il file di chiave o la password è corretta, verificare con strumenti come WinSCP.

### <a name="sftp-copy-activity-failed"></a>L'attività di copia SFTP non è riuscita

- **Sintomi**: 
  * Codice di errore: UserErrorInvalidColumnMappingColumnNotFound 
  * Messaggio di errore: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Causa**: l'origine non include una colonna denominata "AccMngr".

- **Soluzione**: per determinare se la colonna "AccMngr" esiste, controllare la configurazione del set di dati eseguendo il mapping della colonna del set di dati di destinazione.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Codice di errore: SftpFailedToConnectToSftpServer

- **Messaggio**: `Failed to connect to SFTP server '%server;'.`

- **Motivo**: se il messaggio di errore contiene la stringa "si è verificato un timeout dell'operazione di lettura Socket dopo 30.000 millisecondi", è possibile che venga usato un tipo di servizio collegato errato per il server SFTP. Ad esempio, è possibile usare il servizio collegato FTP per connettersi al server SFTP.

- **Consiglio**: controllare la porta del server di destinazione. Per impostazione predefinita, SFTP usa la porta 22.

- **Motivo**: se il messaggio di errore contiene la stringa "risposta server non contiene l'identificazione del protocollo SSH", è possibile che il server SFTP abbia limitato la connessione. Data Factory creerà più connessioni da scaricare dal server SFTP in parallelo e talvolta rileverà la limitazione del server SFTP. In genere, server diversi restituiscono errori diversi quando si verifica la limitazione delle richieste.

- **Raccomandazione**:  

    Specificare il numero massimo di connessioni simultanee del set di dati SFTP come 1 e rieseguire l'attività di copia. Se l'attività ha esito positivo, è possibile assicurarsi che la limitazione sia la stessa.

    Per promuovere la velocità effettiva bassa, contattare l'amministratore SFTP per aumentare il limite del numero di connessioni simultanee oppure è possibile eseguire una delle operazioni seguenti:

    * Se si usa il runtime di integrazione self-hosted, aggiungere l'indirizzo IP del computer IR indipendente all'elenco Consenti.
    * Se si usa Azure IR, aggiungere [Azure Integration Runtime indirizzi IP](./azure-integration-runtime-ip-addresses.md). Se non si vuole aggiungere un intervallo di indirizzi IP all'elenco Consenti server SFTP, usare invece il runtime di integrazione self-hosted.

## <a name="sharepoint-online-list"></a>Elenco SharePoint Online

### <a name="error-code-sharepointonlineauthfailed"></a>Codice di errore: SharePointOnlineAuthFailed

- **Messaggio**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Motivo**: l'ID e la chiave dell'entità servizio potrebbero non essere impostate correttamente.

- **Consiglio**: controllare l'applicazione registrata (ID entità servizio) e la chiave per verificare se sono impostati correttamente.


## <a name="xml-format"></a>Formato XML

### <a name="error-code-xmlsinknotsupported"></a>Codice di errore: XmlSinkNotSupported

- **Messaggio**: `Write data in XML format is not supported yet, choose a different format!`

- **Motivo**: è stato usato un set di dati XML come set di dati sink nell'attività di copia.

- **Raccomandazione**: usare un set di dati in un formato diverso da quello del set di dati sink.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Codice di errore: XmlAttributeColumnNameConflict

- **Messaggio**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Causa**: è stato usato un prefisso di attributo, che ha causato il conflitto.

- **Raccomandazione**: impostare un valore diverso per la proprietà "attributePrefix".


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Codice di errore: XmlValueColumnNameConflict

- **Messaggio**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Motivo**: uno dei nomi di elementi figlio è stato usato come nome di colonna per il valore dell'elemento.

- **Raccomandazione**: impostare un valore diverso per la proprietà "valueColumn".


### <a name="error-code-xmlinvalid"></a>Codice di errore: xmlvalid

- **Messaggio**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Motivo**: il formato del file XML di input non è corretto.

- **Raccomandazione**: correggere il file XML per renderlo ben formato.


## <a name="general-copy-activity-error"></a>Errore di attività di copia generale

### <a name="error-code-jrenotfound"></a>Codice di errore: JreNotFound

- **Messaggio**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Motivo**: il runtime di integrazione self-hosted non riesce a trovare il runtime Java. Il runtime Java è necessario per la lettura di origini particolari.

- **Consiglio**: controllare l'ambiente di runtime di integrazione, vedere [usare Integration Runtime self-hosted](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Codice di errore: WildcardPathSinkNotSupported

- **Messaggio**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Motivo**: il set di dati sink non supporta i valori jolly.

- **Raccomandazione**: controllare il set di dati del sink e riscrivere il percorso senza usare un valore jolly.


### <a name="fips-issue"></a>Problema FIPS

- **Sintomi**: l'attività di copia non riesce in una macchina IR indipendente abilitata per FIPS con il messaggio di errore seguente: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Motivo**: questo errore può verificarsi quando si copiano dati con connettori come BLOB di Azure, SFTP e così via. FIPS (Federal Information Processing Standards) definisce un determinato set di algoritmi di crittografia che possono essere utilizzati. Quando la modalità FIPS è abilitata nel computer, alcune classi crittografiche da cui dipende l'attività di copia sono bloccate in alcuni scenari.

- **Soluzione**: informazioni sul motivo per cui [non viene più consigliata la "modalità FIPS"](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)e valutare se è possibile disabilitare FIPS nel computer del runtime di integrazione self-hosted.

    In alternativa, se si vuole solo consentire Azure Data Factory ignorare FIPS e fare in modo che l'attività venga eseguita correttamente, eseguire le operazioni seguenti:

    1. Aprire la cartella in cui è installato il runtime di integrazione self-hosted. Il percorso è in genere *c:\programmi\microsoft Integration runtime \<IR version> \Shared*.

    2. Aprire il file di *diawp.exe.config* e quindi, alla fine della `<runtime>` sezione, aggiungere `<enforceFIPSPolicy enabled="false"/>` , come illustrato di seguito:

        ![Screenshot di una sezione del file diawp.exe.config che mostra FIPS disabilitato.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Salvare il file, quindi riavviare il computer IR indipendente.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&una pagina](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)