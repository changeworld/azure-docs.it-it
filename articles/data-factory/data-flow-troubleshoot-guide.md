---
title: Risolvere i problemi di mapping dei flussi di dati
description: Informazioni su come risolvere i problemi del flusso di dati in Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.openlocfilehash: 77007f0d9ffac39ecb6f21957dd8cd1bf7a0b945
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565708"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Risolvere i problemi relativi al mapping dei flussi di dati in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi relativi al mapping dei flussi di dati in Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Messaggi e codici di errore comuni 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Codice errore: DF-Executor-SourceInvalidPayload
- **Messaggio**: data preview, debug, and pipeline data flow execution failed because container does not exist (esecuzione del flusso di dati di anteprima, debug e pipeline non riuscita. Il contenitore non esiste)
- **Motivo**: un set di dati contiene un contenitore che non esiste nell'archivio.
- **Raccomandazione**: assicurarsi che il contenitore a cui si fa riferimento nel set di dati esista e che sia accessibile.

### <a name="error-code-df-executor-systeminvalidjson"></a>Codice errore: DF-Executor-SystemInvalidJson

- **Messaggio**: JSON parsing error, unsupported encoding or multiline (errore di analisi JSON, codifica non supportata o su più righe)
- **Causa**: possibili problemi con il file JSON: codifica non supportata, byte danneggiati o uso dell'origine JSON come documento singolo su molte righe nidificate.
- **Raccomandazione**: verificare che la codifica del file JSON sia supportata. Nella trasformazione di origine che usa un set di dati JSON, espandere **Impostazioni JSON** e attivare un **singolo documento**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Codice errore: DF-Executor-BroadcastTimeout

- **Messaggio**: Broadcast join timeout error, make sure broadcast stream produces data within 60 secs in debug runs and 300 secs in job runs (errore di timeout del join di trasmissione. Assicurarsi che il flusso di trasmissione generi i dati entro 60 secondi nelle esecuzioni di debug ed entro 300 secondi nelle esecuzioni di processi)
- **Motivo**: la trasmissione ha un timeout predefinito di 60 secondi sulle esecuzioni di debug e di 300 secondi durante l'esecuzione del processo. Il flusso scelto per la trasmissione è troppo grande per produrre dati entro questo limite.
- **Raccomandazione**: selezionare la scheda **optimize (Ottimizza** ) per le trasformazioni del flusso di dati per join, EXISTS e Lookup. L'opzione predefinita per broadcast è **auto**. Se è impostato **auto** o se si imposta manualmente il lato sinistro o destro su broadcast in **fixed**, è possibile impostare una configurazione di runtime di integrazione di Azure (IR) più ampia o disattivare la trasmissione. Per prestazioni ottimali nei flussi di dati, è consigliabile consentire a Spark di trasmettere usando **auto** e usare un Azure IR ottimizzato per la memoria. 
 
  Se si esegue il flusso di dati in un'esecuzione di test di debug da un'esecuzione di pipeline di debug, è possibile che questa condizione venga eseguita più di frequente. Questo perché Azure Data Factory limita il timeout di trasmissione a 60 secondi per mantenere un'esperienza di debug più veloce. È possibile estendere il timeout al timeout di 300 secondi di un'esecuzione attivata. A tale scopo, è possibile usare l'opzione **debug**  >  **use Activity Runtime** per usare la Azure IR definita nell'attività Esegui pipeline flusso di dati.

- **Messaggio**: errore di timeout di broadcast join. per evitare questo problema, è possibile scegliere ' off ' dell'opzione broadcast in join/exists/Lookup Transformation. Se si intende trasmettere l'opzione di join per migliorare le prestazioni, assicurarsi che Stream Broadcast possa produrre dati entro 60 secondi nelle esecuzioni di debug e 300 secondi nelle esecuzioni del processo.
- **Motivo**: la trasmissione ha un timeout predefinito di 60 secondi nelle esecuzioni di debug e di 300 secondi durante l'esecuzione del processo. Nel join broadcast il flusso scelto per la trasmissione è troppo grande per produrre i dati entro questo limite. Se non viene usato un join broadcast, l'impostazione predefinita broadcast by Dataflow può raggiungere lo stesso limite.
- **Consiglio**: disattivare l'opzione di trasmissione o evitare di trasmettere flussi di dati di grandi dimensioni per i quali l'elaborazione può richiedere più di 60 secondi. Scegliere un flusso più piccolo da trasmettere. Le tabelle Azure SQL Data Warehouse di grandi dimensioni e i file di origine non sono in genere scelte valide. In assenza di un join di broadcast, usare un cluster di dimensioni maggiori se si verifica questo errore.

### <a name="error-code-df-executor-conversion"></a>Codice errore: DF-Executor-Conversion

- **Messaggio**: converting to a date or time failed due to an invalid character (conversione in una data o ora non riuscita a causa di un carattere non valido)
- **Motivo**: i dati non sono nel formato previsto.
- **Raccomandazione**: usare il tipo di dati corretto.

### <a name="error-code-df-executor-invalidcolumn"></a>Codice errore: DF-Executor-InvalidColumn
- **Messaggio**: column name needs to be specified in the query, set an alias if using a SQL function (specificare il nome della colonna nella query, impostare un alias se si usa una funzione SQL)
- **Motivo**: non è stato specificato alcun nome di colonna.
- **Raccomandazione**: impostare un alias se si usa una funzione SQL, ad esempio min () o Max ().

### <a name="error-code-df-executor-drivererror"></a>Codice di errore: DF-Executor-DriverError
- **Messaggio**: INT96 è di tipo timestamp legacy che non è supportato dal flusso di file ADF. Si consiglia di aggiornare il tipo di colonna ai tipi più recenti.
- **Cause**: errore del driver.
- **Raccomandazione**: INT96 è un tipo di timestamp legacy non supportato dal flusso di dati Azure Data Factory. Provare ad aggiornare il tipo di colonna al tipo più recente.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Codice di errore: DF-Executor-BlockCountExceedsLimitError
- **Messaggio**: il conteggio dei blocchi di cui non è stato eseguito il commit non può superare il limite massimo di 100.000 blocchi. Controllare la configurazione del BLOB.
- **Motivo**: il numero massimo di blocchi di cui non è stato eseguito il commit in un blob è 100.000.
- **Consiglio**: per informazioni dettagliate su questo problema, contattare il team del prodotto Microsoft.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Codice di errore: DF-Executor-PartitionDirectoryError
- **Messaggio**: il percorso di origine specificato ha più directory partizionate, ad esempio <Source Path> /<directory radice della partizione 1>/a = 10/b = 20, <Source Path> /<directory radice partizione 2>/c = 10/d = 30) o directory partizionata con altro file o directory non partizionata (ad esempio <Source Path> /<directory radice partizione 1>/a = 10/b = 20, <Source Path> /Directory 2/file1), rimuovere la directory radice della partizione dal percorso di origine e leggerla tramite una trasformazione di origine separata.
- **Causa**: il percorso di origine contiene più directory partizionate o una directory partizionata con un altro file o una directory non partizionata.
- **Raccomandazione**: rimuovere la directory radice partizionata dal percorso di origine e leggerla tramite una trasformazione di origine separata.

### <a name="error-code-df-executor-invalidtype"></a>Codice di errore: DF-Executor-InvalidType
- **Messaggio**: assicurarsi che il tipo di parametro corrisponda al tipo di valore passato. Il passaggio di parametri float dalle pipeline non è attualmente supportato.
- **Motivo**: il tipo di dati per il tipo dichiarato non è compatibile con il valore del parametro effettivo.
- **Raccomandazione**: controllare che i valori dei parametri passati nel flusso di dati corrispondano al tipo dichiarato.

### <a name="error-code-df-executor-parseerror"></a>Codice di errore: DF-Executor-ParseError
- **Messaggio**: non è possibile analizzare l'espressione
- **Causa**: un'espressione ha generato errori di analisi a causa di una formattazione non corretta.
- **Raccomandazione**: controllare la formattazione nell'espressione.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Codice errore: DF-Executor-SystemImplicitCartesian
- **Messaggio**: implicit cartesian product for INNER join is not supported, use CROSS JOIN instead (il prodotto cartesiano implicito per INNER JOIN non è supportato. Usare CROSS JOIN). Le colonne usate nel join devono creare una chiave univoca per le righe.
- **Motivo**: i prodotti cartesiani impliciti per Inner join tra piani logici non sono supportati. Se si usano colonne nel join, creare una chiave univoca.
- **Raccomandazione**: per i join non basati sull'uguaglianza, usare cross join.

### <a name="error-code-getcommand-outputasync-failed"></a>Codice errore: GetCommand OutputAsync failed
- **Messaggio**: durante il debug di Flusso di dati e l'anteprima dei dati: GetCommand OutputAsync failed with ... (l'operazione GetCommand OutputAsync non è riuscita con ...)
- **Motivo**: si tratta di un errore del servizio back-end. 
- **Raccomandazione**: ripetere l'operazione e riavviare la sessione di debug. Se il nuovo tentativo e il riavvio non risolvono il problema, contattare il supporto tecnico. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Codice di errore: DF-Executor-OutOfMemoryError
 
- **Messaggio**: si è verificata un problema di memoria insufficiente durante l'esecuzione. riprovare a usare un runtime di integrazione con un numero di core maggiore e/o un tipo di calcolo con ottimizzazione per la memoria
- **Motivo**: memoria insufficiente per il cluster.
- **Raccomandazione**: i cluster di debug sono destinati allo sviluppo. Usare il campionamento dei dati e le dimensioni e il tipo di calcolo appropriati per eseguire il payload. Per suggerimenti sulle prestazioni, vedere [Guida alle prestazioni del flusso di dati di mapping](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Codice di errore: DF-Executor-illegalArgument

- **Messaggio**: assicurarsi che la chiave di accesso nel servizio collegato sia corretta
- **Causa**: il nome dell'account o la chiave di accesso non è corretta.
- **Raccomandazione**: assicurarsi che il nome dell'account o la chiave di accesso specificata nel servizio collegato sia corretta. 

### <a name="error-code-df-executor-columnunavailable"></a>Codice di errore: DF-Executor-ColumnUnavailable
- **Messaggio**: il nome della colonna utilizzato nell'espressione non è disponibile o non è valido.
- **Motivo**: un nome di colonna non valido o non disponibile viene utilizzato in un'espressione.
- **Raccomandazione**: controllare i nomi di colonna usati nelle espressioni.

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Codice di errore: DF-Executor-OutOfDiskSpaceError
- **Messaggio**: errore interno del server
- **Motivo**: lo spazio su disco del cluster è insufficiente.
- **Raccomandazione**: ripetere la pipeline. Se questa operazione non risolve il problema, contattare il supporto tecnico.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Codice di errore: DF-Executor-StoreIsNotDefined
- **Message**: la configurazione dell'archivio non è definita. Questo errore potrebbe essere causato da un'assegnazione di parametro non valida nella pipeline.
- **Cause**: non determinato.
- **Raccomandazione**: controllare l'assegnazione del valore del parametro nella pipeline. Un'espressione di parametro può contenere caratteri non validi.


### <a name="error-code-4502"></a>Codice di errore: 4502
- **Message**: sono presenti sostanziali esecuzioni di MappingDataflow simultanee che causano errori a causa della limitazione delle richieste in Integration Runtime.
- **Motivo**: un numero elevato di esecuzioni di attività flusso di dati si verifica simultaneamente nel runtime di integrazione. Per ulteriori informazioni, vedere [Azure Data Factory limiti](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Suggerimento**: se si desidera eseguire più attività flusso di dati in parallelo, distribuirle tra più runtime di integrazione.


### <a name="error-code-invalidtemplate"></a>Codice di errore: InvalidTemplate
- **Message**: Impossibile valutare l'espressione della pipeline.
- **Causa**: l'espressione della pipeline passata nell'attività flusso di dati non viene elaborata correttamente a causa di un errore di sintassi.
- **Consiglio**: controllare l'attività in Monitoraggio attività per verificare l'espressione.

### <a name="error-code-2011"></a>Codice di errore: 2011
- **Messaggio**: l'attività è in esecuzione in Azure Integration Runtime e non è stato possibile decrittografare le credenziali dell'archivio dati o del calcolo connesse tramite una Integration Runtime self-hosted. Controllare la configurazione dei servizi collegati associati a questa attività e assicurarsi di usare il tipo di runtime di integrazione appropriato.
- **Motivo**: il flusso di dati non supporta i servizi collegati nei runtime di integrazione self-hosted.
- **Consiglio**: configurare il flusso di dati per l'esecuzione in un runtime di integrazione della rete virtuale gestita.

### <a name="error-code-df-xml-invalidvalidationmode"></a>Codice di errore: DF-XML-InvalidValidationMode
- **Message**: è stata specificata la modalità di convalida XML non valida.
- **Raccomandazione**: controllare il valore del parametro e specificare la modalità di convalida corretta.

### <a name="error-code-df-xml-invaliddatafield"></a>Codice di errore: DF-XML-InvalidDataField
- **Messaggio**: il campo per i record danneggiati deve essere di tipo stringa e ammette valori null.
- **Raccomandazione**: assicurarsi che la colonna `\"_corrupt_record\"` nel progetto di origine abbia un tipo di dati String.

### <a name="error-code-df-xml-malformedfile"></a>Codice di errore: DF-XML-MalformedFile
- **Messaggio**: XML in formato non valido in ' FailFastMode '.
- **Raccomandazione**: aggiornare il contenuto del file XML nel formato corretto.

### <a name="error-code-df-xml-invaliddatatype"></a>Codice di errore: DF-XML-InvalidDataType
- **Messaggio**: l'elemento XML contiene elementi secondari o attributi e non può essere convertito.

### <a name="error-code-df-xml-invalidreferenceresource"></a>Codice di errore: DF-XML-InvalidReferenceResource
- **Messaggio**: Impossibile risolvere la risorsa di riferimento nel file di dati XML.
- **Raccomandazione**: è necessario controllare la risorsa di riferimento nel file di dati XML.

### <a name="error-code-df-xml-invalidschema"></a>Codice di errore: DF-XML-InvalidSchema
- **Messaggio**: convalida dello schema non riuscita.

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>Codice di errore: DF-XML-UnsupportedExternalReferenceResource
- **Messaggio**: la risorsa di riferimento esterna nel file di dati XML non è supportata.
- **Raccomandazione**: aggiornare il contenuto del file XML quando la risorsa di riferimento esterna non è attualmente supportata.

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>Codice di errore: DF-GEN2-InvalidAccountConfiguration
- **Messaggio**: è necessario specificare una delle chiavi dell'account o tenant/SpnId/SpnCredential/SpnCredentialType o MiServiceUri/miServiceToken.
- **Raccomandazione**: configurare l'account corretto nel servizio collegato Gen2 correlato.

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>Codice di errore: DF-GEN2-InvalidAuthConfiguration
- **Message**: è possibile specificare solo uno dei tre metodi di autenticazione (Key, SERVICEPRINCIPAL e mi). 
- **Raccomandazione**: scegliere il tipo di autenticazione corretto nel servizio collegato Gen2 correlato.

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>Codice di errore: DF-GEN2-InvalidServicePrincipalCredentialType
- **Messaggio**: ServicePrincipalCredentialType non valido.

### <a name="error-code-df-gen2-invaliddatatype"></a>Codice di errore: DF-GEN2-InvalidDataType
- **Messaggio**: tipo di cloud non valido.

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>Codice di errore: DF-BLOB-InvalidAccountConfiguration
- **Message**: è necessario specificare una delle chiavi dell'account o sas_token.

### <a name="error-code-df-blob-invalidauthconfiguration"></a>Codice di errore: DF-BLOB-InvalidAuthConfiguration
- **Message**: è possibile specificare solo uno dei due metodi di autenticazione (chiave, SAS).

### <a name="error-code-df-blob-invaliddatatype"></a>Codice di errore: DF-BLOB-InvalidDataType
- **Messaggio**: tipo di cloud non valido.

### <a name="error-code-df-cosmos-partitionkeymissed"></a>Codice di errore: DF-Cosmos-PartitionKeyMissed
- **Message**: per le operazioni di aggiornamento ed eliminazione è necessario specificare il percorso della chiave di partizione.
- **Raccomandazione**: usare la chiave di partizione specifica nelle impostazioni del sink Cosmos.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Codice di errore: DF-Cosmos-InvalidPartitionKey
- **Messaggio**: il percorso della chiave di partizione non può essere vuoto per le operazioni di aggiornamento ed eliminazione.
- **Raccomandazione**: usare la chiave di partizione specifica nelle impostazioni del sink Cosmos.

### <a name="error-code-df-cosmos-idpropertymissed"></a>Codice di errore: DF-Cosmos-IdPropertyMissed
- **Messaggio**: è necessario eseguire il mapping della proprietà' ID ' per le operazioni di eliminazione e aggiornamento.
- **Raccomandazione**: assicurarsi che i dati di input includano una `id` colonna nelle impostazioni del sink Cosmos. Se no, usare **Select o derivate Transformation** per generare questa colonna prima del sink.

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>Codice di errore: DF-Cosmos-InvalidPartitionKeyContent
- **Messaggio**: la chiave di partizione deve iniziare con/.
- **Raccomandazione**: fare in modo che la chiave di partizione inizi con `/` nelle impostazioni del sink Cosmos, ad esempio: `/movieId` .

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Codice di errore: DF-Cosmos-InvalidPartitionKey
- **Messaggio**: non è stato eseguito il mapping di partitionKey in sink per le operazioni di eliminazione e aggiornamento.
- **Raccomandazione**: nelle impostazioni del sink Cosmos usare la chiave di partizione uguale alla chiave di partizione del contenitore.

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>Codice di errore: DF-Cosmos-InvalidConnectionMode
- **Messaggio**: ConnectionMode non valido.
- **Raccomandazione**: verificare che la modalità supportata sia **gateway** e **DirectHttps** in impostazioni Cosmos.

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>Codice di errore: DF-Cosmos-InvalidAccountConfiguration
- **Messaggio**: è necessario specificare AccountName o accountEndpoint.

### <a name="error-code-df-github-writenotsupported"></a>Codice di errore: DF-GitHub-WriteNotSupported
- **Messaggio**: l'archivio GitHub non consente le Scritture.

### <a name="error-code-df-pgsql-invalidcredential"></a>Codice di errore: DF-PGSQL-InvalidCredential
- **Message**: è necessario specificare User/password.
- **Raccomandazione**: assicurarsi di avere le impostazioni appropriate per le credenziali nel servizio collegato PostgreSQL correlato.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Codice di errore: DF-fiocco di neve-InvalidStageConfiguration
- **Messaggio**: solo il tipo di archiviazione BLOB può essere usato come fase in un'operazione di lettura/scrittura a fiocco di neve.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Codice di errore: DF-fiocco di neve-InvalidStageConfiguration
- **Messaggio**: è necessario specificare le proprietà della fase fiocco di neve con l'autenticazione BLOB e SAS di Azure.

### <a name="error-code-df-snowflake-invaliddatatype"></a>Codice di errore: DF-fiocco di neve-InvalidDataType
- **Messaggio**: il tipo Spark non è supportato in fiocco di neve.
- **Raccomandazione**: usare la **trasformazione derivazione** per modificare la colonna correlata dei dati di input nel tipo stringa prima del sink di fiocco di neve. 

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>Codice di errore: DF-hive-InvalidBlobStagingConfiguration
- **Messaggio**: è necessario specificare le proprietà di staging dell'archiviazione BLOB.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Codice di errore: DF-hive-InvalidGen2StagingConfiguration
- **Messaggio**: ADLS Gen2 gestione temporanea archiviazione supporta solo le credenziali chiave dell'entità servizio.
- **Raccomandazione**: confermare di applicare la credenziale chiave dell'entità servizio nel servizio collegato ADLS Gen2 usato come gestione temporanea.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Codice di errore: DF-hive-InvalidGen2StagingConfiguration
- **Messaggio**: è necessario specificare le proprietà di gestione temporanea dell'archiviazione ADLS Gen2. È necessario specificare un valore per Key o tenant/spnId/spnKey o miServiceUri/miServiceToken.
- **Raccomandazione**: applicare la credenziale corretta usata come gestione temporanea nell'hive nel servizio collegato di ADLS Gen2 correlato. 

### <a name="error-code-df-hive-invaliddatatype"></a>Codice di errore: DF-hive-InvalidDataType
- **Messaggio**: colonne non supportate.
- **Raccomandazione**: aggiornare la colonna dei dati di input in modo che corrisponda al tipo di dati supportato da hive.

### <a name="error-code-df-hive-invalidstoragetype"></a>Codice di errore: DF-hive-InvalidStorageType
- **Messaggio**: il tipo di archiviazione può essere BLOB o Gen2.

### <a name="error-code-df-delimited-invalidconfiguration"></a>Codice di errore: DF-delimitato-InvalidConfiguration
- **Message**: è necessario specificare una o più righe vuote o un'intestazione personalizzata.
- **Raccomandazione**: specificare righe vuote o intestazioni personalizzate nelle impostazioni CSV.

### <a name="error-code-df-delimited-columndelimitermissed"></a>Codice di errore: DF-delimitato-ColumnDelimiterMissed
- **Message**: il delimitatore di colonna è necessario per l'analisi.
- **Raccomandazione**: verificare di avere il delimitatore di colonna nelle impostazioni del volume condiviso cluster.

### <a name="error-code-df-mssql-invalidcredential"></a>Codice di errore: DF-MSSQL-InvalidCredential
- **Message**: è necessario specificare un utente/pwd o tenant/SpnId/SpnKey o MiServiceUri/miServiceToken.
- **Raccomandazione**: applicare le credenziali corrette nel servizio collegato MSSQL correlato.

### <a name="error-code-df-mssql-invaliddatatype"></a>Codice di errore: DF-MSSQL-InvalidDataType
- **Messaggio**: campo/i non supportato.
- **Raccomandazione**: modificare la colonna di dati di input in modo che corrisponda al tipo di dati supportato da MSSQL.

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>Codice di errore: DF-MSSQL-InvalidAuthConfiguration
- **Message**: è possibile specificare solo uno dei tre metodi di autenticazione (Key, SERVICEPRINCIPAL e mi).
- **Raccomandazione**: è possibile specificare solo uno dei tre metodi di autenticazione (Key, SERVICEPRINCIPAL e mi) nel servizio collegato MSSQL correlato.

### <a name="error-code-df-mssql-invalidcloudtype"></a>Codice di errore: DF-MSSQL-InvalidCloudType
- **Messaggio**: tipo di cloud non valido.
- **Consiglio**: controllare il tipo di cloud nel servizio collegato MSSQL correlato.

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>Codice di errore: DF-SQLDW-InvalidBlobStagingConfiguration
- **Messaggio**: è necessario specificare le proprietà di staging dell'archiviazione BLOB.

### <a name="error-code-df-sqldw-invalidstoragetype"></a>Codice di errore: DF-SQLDW-InvalidStorageType
- **Messaggio**: il tipo di archiviazione può essere BLOB o Gen2.

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>Codice di errore: DF-SQLDW-InvalidGen2StagingConfiguration
- **Messaggio**: ADLS Gen2 gestione temporanea archiviazione supporta solo le credenziali chiave dell'entità servizio.

### <a name="error-code-df-sqldw-invalidconfiguration"></a>Codice di errore: DF-SQLDW-InvalidConfiguration
- **Messaggio**: è necessario specificare le proprietà di gestione temporanea dell'archiviazione ADLS Gen2. È necessario specificare un valore per Key o tenant/spnId/spnCredential/spnCredentialType o miServiceUri/miServiceToken.

### <a name="error-code-df-delta-invalidconfiguration"></a>Codice di errore: DF-DELTA-InvalidConfiguration
- **Messaggio**: non è possibile impostare il timestamp e la versione nello stesso momento.

### <a name="error-code-df-delta-keycolumnmissed"></a>Codice di errore: DF-DELTA-KeyColumnMissed
- **Messaggio**: per le operazioni non inseribili è necessario specificare le colonne chiave.

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>Codice di errore: DF-DELTA-InvalidTableOperationSettings
- **Messaggio**: non è possibile specificare entrambe le opzioni ricrea e tronca.

### <a name="error-code-df-excel-worksheetconfigmissed"></a>Codice di errore: DF-Excel-WorksheetConfigMissed
- **Messaggio**: il nome o l'indice del foglio di Excel è obbligatorio.
- **Raccomandazione**: controllare il valore del parametro e specificare il nome del foglio o l'indice per leggere i dati di Excel.

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>Codice di errore: DF-Excel-InvalidWorksheetConfiguration
- **Messaggio**: il nome e l'indice del foglio di Excel non possono esistere nello stesso momento.
- **Raccomandazione**: controllare il valore del parametro e specificare il nome del foglio o l'indice per leggere i dati di Excel.

### <a name="error-code-df-excel-invalidrange"></a>Codice di errore: DF-Excel-InvalidRange
- **Message**: è stato specificato un intervallo non valido.
- **Raccomandazione**: controllare il valore del parametro e specificare l'intervallo valido in base al [formato Excel nelle proprietà di Factory-Dataset dati di Azure](./format-excel.md#dataset-properties).

### <a name="error-code-df-excel-worksheetnotexist"></a>Codice di errore: DF-Excel-WorksheetNotExist
- **Messaggio**: foglio di lavoro di Excel inesistente.
- **Raccomandazione**: controllare il valore del parametro e specificare il nome o l'indice del foglio valido per leggere i dati di Excel.

### <a name="error-code-df-excel-differentschemanotsupport"></a>Codice di errore: DF-Excel-DifferentSchemaNotSupport
- **Messaggio**: la lettura dei file di Excel con schema diverso non è attualmente supportata.

### <a name="error-code-df-excel-invaliddatatype"></a>Codice di errore: DF-Excel-InvalidDataType
- **Messaggio**: tipo di dati non supportato.

### <a name="error-code-df-excel-invalidfile"></a>Codice di errore: DF-Excel-InvalidFile
- **Messaggio**: viene fornito un file di Excel non valido mentre sono supportati solo i file con estensione xlsx e xls.

### <a name="error-code-df-adobeintegration-invalidmaptofilter"></a>Codice di errore: DF-AdobeIntegration-InvalidMapToFilter
- **Messaggio**: la risorsa personalizzata può avere solo una chiave/ID mappata per filtrare.

### <a name="error-code-df-adobeintegration-invalidpartitionconfiguration"></a>Codice di errore: DF-AdobeIntegration-InvalidPartitionConfiguration
- **Messaggio**: è supportata una sola partizione. Lo schema della partizione può essere RoundRobin o hash.
- **Raccomandazione**: in impostazioni AdobeIntegration verificare che siano presenti solo partizioni singole. Lo schema della partizione può essere RoundRobin o hash.

### <a name="error-code-df-adobeintegration-keycolumnmissed"></a>Codice di errore: DF-AdobeIntegration-KeyColumnMissed
- **Message**: la chiave deve essere specificata per le operazioni non inseribili.
- **Raccomandazione**: specificare le colonne chiave nelle impostazioni AdobeIntegration per le operazioni non inseribili.

### <a name="error-code-df-adobeintegration-invalidpartitiontype"></a>Codice di errore: DF-AdobeIntegration-InvalidPartitionType
- **Message**: il tipo di partizione deve essere roundRobin.
- **Raccomandazione**: verificare che il tipo di partizione sia roundRobin nelle impostazioni di AdobeIntegration.

### <a name="error-code-df-adobeintegration-invalidprivacyregulation"></a>Codice di errore: DF-AdobeIntegration-InvalidPrivacyRegulation
- **Messaggio**: attualmente è gdpr solo la normativa sulla privacy supportata.
- **Raccomandazione**: verificare che il regolamento sulla privacy in impostazioni di AdobeIntegration sia **' GDPR '**.

## <a name="miscellaneous-troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi vari
- **Problema**: si è verificata un'eccezione imprevista ed esecuzione non riuscita.
    - **Messaggio**: durante l'esecuzione dell'attività Flusso di dati: hit unexpected exception and execution failed (eccezione imprevista ed esecuzione non riuscita).
    - **Motivo**: si tratta di un errore del servizio back-end. Ripetere l'operazione e riavviare la sessione di debug.
    - **Raccomandazione**: se il nuovo tentativo e il riavvio non risolvono il problema, contattare il supporto tecnico.

-  **Problema**: nessun dato di output in join durante l'anteprima dei dati di debug.
    - **Messaggio**: numero elevato di valori null o valori mancanti che potrebbero essere causati dalla presenza di un numero insufficiente di righe campionate. Provare ad aggiornare il limite di righe di debug e ad aggiornare i dati.
    - **Causa**: la condizione di join non corrisponde ad alcuna riga o ha restituito un numero elevato di valori null durante l'anteprima dei dati.
    - **Raccomandazione**: in **impostazioni di debug** aumentare il numero di righe nel limite di righe di origine. Assicurarsi di selezionare un Azure IR che disponga di un cluster di flussi di dati sufficientemente grande da poter gestire più dati.
    
- **Problema**: errore di convalida nell'origine con file CSV su più righe. 
    - **Message**: è possibile che venga visualizzato uno dei messaggi di errore seguenti:
        - L'ultima colonna è null o mancante.
        - La convalida dello schema nell'origine non riesce.
        - L'importazione dello schema non viene visualizzata correttamente nell'esperienza utente e l'ultima colonna contiene un carattere di nuova riga nel nome.
    - **Causa**: nel flusso di dati di mapping, i file di origine CSV su più righe non funzionano al momento quando \r\n viene usato come delimitatore di riga. Talvolta le righe aggiuntive nei ritorni a capo possono causare errori. 
    - **Raccomandazione**: generare il file nell'origine usando \n come delimitatore di riga anziché \r\n. In alternativa, usare l'attività di copia per convertire il file CSV in modo da usare \n come delimitatore di riga.

## <a name="general-troubleshooting-guidance"></a>Indicazioni generali sulla risoluzione dei problemi
1. Verificare lo stato delle connessioni del set di dati. In ogni trasformazione di origine e sink andare al servizio collegato per ogni set di dati in uso e testare le connessioni.
2. Verificare lo stato delle connessioni di file e tabelle nella finestra di progettazione del flusso di dati. In modalità di debug selezionare **Anteprima dati** nelle trasformazioni di origine per assicurarsi che sia possibile accedere ai dati.
3. Se tutti gli elementi sono corretti nell'anteprima dei dati, passare alla finestra di progettazione della pipeline e inserire il flusso di dati in un'attività della pipeline. Eseguire il debug della pipeline per un test end-to-end.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, vedere le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)