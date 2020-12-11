---
title: Eseguire query sui dati Azure Cosmos DB usando un pool SQL senza server in Azure sinapsi link Preview
description: Questo articolo illustra come eseguire query Azure Cosmos DB usando un pool SQL senza server nell'anteprima del collegamento di sinapsi di Azure.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 12/04/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 22103ad580fa474f44eaf42c696d19bbbd137c8e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095101"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link-preview"></a>Eseguire query Azure Cosmos DB dati con un pool SQL senza server nell'anteprima di Azure sinapsi link

> [!IMPORTANT]
> Il supporto del pool SQL senza server per il collegamento a sinapsi di Azure per Azure Cosmos DB è attualmente in fase di anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere le [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Un pool SQL senza server consente di analizzare i dati nei contenitori Azure Cosmos DB abilitati con il [collegamento sinapsi di Azure](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) in tempo quasi reale senza influire sulle prestazioni dei carichi di lavoro transazionali. Offre una nota sintassi T-SQL per eseguire query sui dati dall' [Archivio analitico](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e la connettività integrata a un'ampia gamma di strumenti di query di Business Intelligence (BI) ed ad hoc tramite l'interfaccia t-SQL.

Per eseguire query Azure Cosmos DB, la superficie di attacco di [selezione](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) completa è supportata tramite la funzione [OPENROWSET](develop-openrowset.md) , che include la maggior parte degli [operatori e delle funzioni SQL](overview-features.md). È anche possibile archiviare i risultati della query che legge i dati da Azure Cosmos DB insieme ai dati nell'archivio BLOB di Azure o Azure Data Lake Storage usando [Create external table As Select](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS). Attualmente non è possibile archiviare i risultati di query del pool SQL senza server per Azure Cosmos DB usando CETAS.

Questo articolo illustra come scrivere una query con un pool SQL senza server che eseguirà query sui dati da contenitori di Azure Cosmos DB abilitati con il collegamento sinapsi di Azure. È quindi possibile ottenere altre informazioni sulla creazione di viste del pool SQL senza server su Azure Cosmos DB contenitori e sulla connessione ai modelli di Power BI in [questa esercitazione](./tutorial-data-analyst.md).

> [!IMPORTANT]
> Questa esercitazione usa un contenitore con uno [schema Azure Cosmos DB ben definito](../../cosmos-db/analytical-store-introduction.md#schema-representation). L'esperienza di query fornita dal pool SQL senza server per un [Azure Cosmos DB schema di fedeltà completa](#full-fidelity-schema) è un comportamento temporaneo che cambierà in base ai commenti in anteprima. Non fare affidamento sullo schema del set di risultati della `OPENROWSET` funzione senza la `WITH` clausola che legge i dati da un contenitore con uno schema di fedeltà completa perché l'esperienza di query potrebbe essere allineata con e modificare in base allo schema ben definito. È possibile inviare commenti e suggerimenti nel [Forum di commenti e suggerimenti su Azure sinapsi Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics). È anche possibile contattare il [team del prodotto di collegamento di Azure sinapsi](mailto:cosmosdbsynapselink@microsoft.com) per fornire commenti e suggerimenti.

## <a name="overview"></a>Panoramica

Il pool SQL senza server consente di eseguire query Azure Cosmos DB archiviazione analitica utilizzando la `OPENROWSET` funzione. 
- `OPENROWSET` con chiave inline. Questa sintassi può essere usata per eseguire query Azure Cosmos DB raccolte senza dover preparare le credenziali.
- `OPENROWSET` che ha fatto riferimento a Credential che contiene la chiave dell'account Cosmos DB. Questa sintassi può essere utilizzata per creare visualizzazioni in raccolte Azure Cosmos DB.

### <a name="openrowset-with-key"></a>[OPENROWSET con chiave](#tab/openrowset-key)

Per supportare l'esecuzione di query e l'analisi dei dati in un Azure Cosmos DB archivio analitico, un pool SQL senza server utilizza la `OPENROWSET` sintassi seguente:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

La stringa di connessione Azure Cosmos DB specifica il nome dell'account di Azure Cosmos DB, il nome del database, la chiave master dell'account del database e un nome di area facoltativo per la `OPENROWSET` funzione.

Il formato della stringa di connessione è il seguente:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Il nome del contenitore Azure Cosmos DB viene specificato senza virgolette nella `OPENROWSET` sintassi. Se il nome del contenitore contiene caratteri speciali, ad esempio un trattino (-), il nome deve essere racchiuso tra parentesi quadre ( `[]` ) nella `OPENROWSET` sintassi.

### <a name="openrowset-with-credential"></a>[OPENROWSET con credenziale](#tab/openrowset-credential)

È possibile utilizzare `OPENROWSET` la sintassi che fa riferimento a Credential:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

La stringa di connessione Azure Cosmos DB non contiene la chiave in questo caso. Il formato della stringa di connessione è il seguente:
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

La chiave master dell'account del database viene inserita in credenziali a livello di server o credenziali con ambito database. 

---

> [!IMPORTANT]
> Assicurarsi di utilizzare le regole di confronto del database UTF-8, ad esempio, `Latin1_General_100_CI_AS_SC_UTF8` perché i valori stringa in un Azure Cosmos DB archivio analitico sono codificati come testo UTF-8.
> Una mancata corrispondenza tra la codifica del testo nel file e le regole di confronto può provocare errori di conversione del testo imprevisti.
> È possibile modificare facilmente le regole di confronto predefinite del database corrente usando l'istruzione T-SQL `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

> [!NOTE]
> Un pool SQL senza server non supporta l'esecuzione di query su un archivio transazionale Azure Cosmos DB.

## <a name="sample-dataset"></a>Set di dati di esempio

Gli esempi in questo articolo si basano sui dati del [Centro europeo per la prevenzione e il controllo delle patologie (ECDC) COVID-19 Cases](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) e [COVID-19 Open Research set di dati (Cord-19), DOI: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

È possibile visualizzare la licenza e la struttura dei dati in queste pagine. È anche possibile scaricare i dati di esempio per i set di dati [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) e [Cord-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Per seguire la procedura illustrata in questo articolo come eseguire query Azure Cosmos DB dati con un pool SQL senza server, assicurarsi di creare le risorse seguenti:

* Un account di database Azure Cosmos DB di cui è [abilitato il collegamento sinapsi di Azure](../../cosmos-db/configure-synapse-link.md).
* Un database Azure Cosmos DB denominato `covid` .
* Due Azure Cosmos DB contenitori denominati `Ecdc` e `Cord19` caricati con i set di impostazioni di esempio precedenti.

Per finalità di test, è possibile usare la stringa di connessione seguente: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` . Si noti che questa connessione non garantisce prestazioni perché questo account potrebbe trovarsi in un'area remota rispetto all'endpoint SQL sinapsi.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Esplorazione dei dati di Azure Cosmos DB con inferenza automatica dello schema

Il modo più semplice per esplorare i dati in Azure Cosmos DB consiste nell'utilizzare la funzionalità di inferenza automatica dello schema. Omettendo la `WITH` clausola dall' `OPENROWSET` istruzione, è possibile indicare al pool SQL senza server di rilevare automaticamente (dedurre) lo schema dell'archivio analitico del contenitore Azure Cosmos DB.

### <a name="openrowset-with-key"></a>[OPENROWSET con chiave](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OPENROWSET con credenziale](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

Nell'esempio precedente è stato indicato al pool SQL senza server di connettersi al `covid` database nell'account Azure Cosmos DB `MyCosmosDbAccount` autenticato usando la chiave di Azure Cosmos DB (il manichino nell'esempio precedente). È stato quindi eseguito l'accesso all' `Ecdc` Archivio analitico del contenitore nell' `West US 2` area. Poiché non è presente alcuna proiezione di proprietà specifiche, la `OPENROWSET` funzione restituirà tutte le proprietà dagli elementi Azure Cosmos DB.

Supponendo che gli elementi nel contenitore Azure Cosmos DB dispongano delle `date_rep` `cases` proprietà, e `geo_id` , i risultati della query sono illustrati nella tabella seguente:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Se è necessario esplorare i dati dall'altro contenitore nello stesso database di Azure Cosmos DB, è possibile usare la stessa stringa di connessione e fare riferimento al contenitore necessario come terzo parametro:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Specifica in modo esplicito lo schema

Sebbene la funzionalità di inferenza automatica dello schema in `OPENROWSET` fornisca un querience semplice e facile da utilizzare, gli scenari aziendali potrebbero richiedere di specificare in modo esplicito lo schema per le proprietà rilevanti di sola lettura dei dati Azure Cosmos DB.

La `OPENROWSET` funzione consente di specificare in modo esplicito le proprietà che si desidera leggere dai dati nel contenitore e di specificare i relativi tipi di dati.

Si supponga di aver importato alcuni dati dal set di dati [COVID di ECDC](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) con la seguente struttura in Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Questi documenti JSON flat in Azure Cosmos DB possono essere rappresentati come un set di righe e colonne in sinapsi SQL. La `OPENROWSET` funzione consente di specificare un subset di proprietà che si desidera leggere e i tipi di colonna esatti nella `WITH` clausola:

### <a name="openrowset-with-key"></a>[OPENROWSET con chiave](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### <a name="openrowset-with-credential"></a>[OPENROWSET con credenziale](#tab/openrowset-credential)
```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
---
Il risultato di questa query potrebbe essere simile a quello della tabella seguente:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Per ulteriori informazioni sui tipi SQL da utilizzare per i valori Azure Cosmos DB, vedere le [regole per i mapping dei tipi SQL](#azure-cosmos-db-to-sql-type-mappings) alla fine dell'articolo.

## <a name="create-view"></a>Crea vista

Una volta identificato lo schema, è possibile preparare una visualizzazione sopra i dati Azure Cosmos DB. È necessario inserire la chiave dell'account Azure Cosmos DB in una credenziale separata e fare riferimento a questa credenziale dalla `OPENROWSET` funzione. Non tenere la chiave dell'account nella definizione della vista.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Non usare `OPENROWSET` senza uno schema definito in modo esplicito perché potrebbe avere un effetto sulle prestazioni. Assicurarsi di usare le dimensioni più piccole possibili per le colonne, ad esempio VARCHAR (100) anziché VARCHAR default (8000). È consigliabile utilizzare alcune regole di confronto UTF-8 come regole di confronto predefinite del database o impostarle come regole di confronto della colonna esplicite per evitare il [problema di conversione UTF-8](/azure/synapse-analytics/troubleshoot/reading-utf8-text). Le regole di confronto `Latin1_General_100_BIN2_UTF8` offrono prestazioni ottimali quando Yu filtra i dati usando alcune colonne stringa.

## <a name="query-nested-objects-and-arrays"></a>Eseguire query su oggetti e matrici annidati

Con Azure Cosmos DB è possibile rappresentare modelli di dati più complessi componendoli come oggetti annidati o matrici. La funzionalità di sincronizzazione automatica del collegamento sinapsi di Azure per Azure Cosmos DB gestisce la rappresentazione dello schema nell'archivio analitico, che include la gestione dei tipi di dati annidati che consentono di eseguire query avanzate dal pool SQL senza server.

Il set di dati [Cord-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) , ad esempio, contiene documenti JSON che seguono questa struttura:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Gli oggetti annidati e le matrici in Azure Cosmos DB sono rappresentati come stringhe JSON nel risultato della query quando vengono `OPENROWSET` letti dalla funzione. È possibile specificare i percorsi dei valori annidati negli oggetti quando si utilizza la `WITH` clausola:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

Il risultato di questa query potrebbe essere simile a quello della tabella seguente:

| paper_id | title | metadata | authors |
| --- | --- | --- |
| bb11206963e831f... | Informazioni supplementari su Eco-epidemi... | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1... | Uso dei sieri di convalescenza in immune-E... | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649... | Tylosema esculentum (Marama) Tuber e B... | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

Altre informazioni sull'analisi di [tipi di dati complessi nel collegamento sinapsi di Azure](../how-to-analyze-complex-schema.md) e [nelle strutture annidate in un pool SQL senza server](query-parquet-nested-types.md).

> [!IMPORTANT]
> Se vengono visualizzati caratteri imprevisti nel testo `MÃƒÂ©lade` `Mélade` , ad esempio anziché, le regole di confronto del database non sono impostate per le regole di confronto [UTF-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) .
> [Modificare le regole di confronto del database con le](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) regole di confronto UTF-8 utilizzando un'istruzione SQL come `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>Flat array annidati

Azure Cosmos DB dati potrebbero avere sottomatrici annidate, come la matrice dell'autore, da un set di dati [Cord-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

In alcuni casi, potrebbe essere necessario "unire" le proprietà dall'elemento principale (metadati) con tutti gli elementi della matrice (autori). Un pool SQL senza server consente di rendere flat le strutture annidate applicando la `OPENJSON` funzione nella matrice annidata:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Il risultato di questa query potrebbe essere simile a quello della tabella seguente:

| title | authors | first | last | associazione |
| --- | --- | --- | --- | --- |
| Informazioni supplementari su Eco-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Informazioni supplementari su Eco-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Informazioni supplementari su Eco-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Informazioni supplementari su Eco-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Se vengono visualizzati caratteri imprevisti nel testo `MÃƒÂ©lade` `Mélade` , ad esempio anziché, le regole di confronto del database non sono impostate per le regole di confronto [UTF-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) . [Modificare le regole di confronto del database con le](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) regole di confronto UTF-8 utilizzando un'istruzione SQL come `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB ai mapping dei tipi SQL

Sebbene Azure Cosmos DB archivio transazionale sia indipendente dallo schema, l'archivio analitico è schematizzato per ottimizzare le prestazioni delle query analitiche. Con la funzionalità di sincronizzazione automatica del collegamento sinapsi di Azure, Azure Cosmos DB gestisce la rappresentazione dello schema nell'archivio analitico, che include la gestione dei tipi di dati annidati. Poiché un pool SQL senza server esegue query nell'archivio analitico, è importante comprendere come eseguire il mapping di Azure Cosmos DB tipi di dati di input ai tipi di dati SQL.

Gli account Azure Cosmos DB dell'API SQL (Core) supportano i tipi di proprietà JSON di Number, String, Boolean, null, oggetto annidato o matrice. Se si usa la clausola in, è necessario scegliere i tipi SQL che corrispondono a questi tipi JSON `WITH` `OPENROWSET` . La tabella seguente illustra i tipi di colonna SQL da usare per diversi tipi di proprietà in Azure Cosmos DB.

| Tipo di proprietà Azure Cosmos DB | Tipo di colonna SQL |
| --- | --- |
| Boolean | bit |
| Integer | bigint |
| Decimal | float |
| string | varchar (regole di confronto del database UTF-8) |
| Data/ora (stringa in formato ISO) | varchar (30) |
| Data/ora (timestamp UNIX) | bigint |
| Null | `any SQL type` 
| Oggetto annidato o matrice | varchar (max) (regole di confronto del database UTF-8), serializzate come testo JSON |

## <a name="full-fidelity-schema"></a>Schema di fedeltà completa

Azure Cosmos DB schema di fedeltà completa registra sia i valori che i tipi di corrispondenza migliori per ogni proprietà in un contenitore. La `OPENROWSET` funzione in un contenitore con schema di fedeltà completa fornisce sia il tipo che il valore effettivo in ogni cella. Si supponga che la query seguente legga gli elementi da un contenitore con lo schema di fedeltà completa:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

Il risultato di questa query restituirà i tipi e i valori formattati come testo JSON:

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date": "2020-08-13"} | {"Int32": "254"} | {"String": "RS"} |
| {"date": "2020-08-12"} | {"Int32": "235"}| {"String": "RS"} |
| {"date": "2020-08-11"} | {"Int32": "316"} | {"String": "RS"} |
| {"date": "2020-08-10"} | {"Int32": "281"} | {"String": "RS"} |
| {"date": "2020-08-09"} | {"Int32": "295"} | {"String": "RS"} |
| {"String": "2020/08/08"} | {"Int32": "312"} | {"String": "RS"} |
| {"date": "2020-08-07"} | {"float64": "339.0"} | {"String": "RS"} |

Per ogni valore, è possibile visualizzare il tipo identificato in un elemento contenitore Azure Cosmos DB. La maggior parte dei valori per la `date_rep` proprietà contiene `date` valori, ma alcuni di essi vengono archiviati erroneamente come stringhe in Azure Cosmos DB. Lo schema di fedeltà completa restituirà i valori tipizzati correttamente `date` e i valori formattati in modo errato `string` .
Il numero di case è costituito da informazioni archiviate come `int32` valore, ma è presente un valore immesso come numero decimale. Questo valore è di `float64` tipo. Se sono presenti alcuni valori che superano il `int32` numero più alto, vengono archiviati come `int64` tipo. Tutti `geo_id` i valori in questo esempio vengono archiviati come `string` tipi.

> [!IMPORTANT]
> La `OPENROWSET` funzione senza una `WITH` clausola espone entrambi i valori con i tipi previsti e i valori con tipi immessi in modo errato. Questa funzione è progettata per l'esplorazione dei dati e non per la creazione di report. Non analizzare i valori JSON restituiti da questa funzione per compilare i report. Usare una [clausola with](#query-items-with-full-fidelity-schema) esplicita per creare i report. È necessario eseguire la pulizia dei valori con tipi non corretti nel contenitore Azure Cosmos DB per applicare correzioni nell'archivio analitico Full Fidelity.

Se è necessario eseguire query Azure Cosmos DB account del tipo di API Mongo DB, è possibile ottenere altre informazioni sulla rappresentazione dello schema con fedeltà completa nell'archivio analitico e i nomi di proprietà estese da usare in informazioni su [Azure Cosmos DB archivio analitico (anteprima)](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="query-items-with-full-fidelity-schema"></a>Eseguire query su elementi con schema con fedeltà completa

Quando si esegue una query sullo schema di fedeltà completa, è necessario specificare in modo esplicito il tipo SQL e il tipo di proprietà Azure Cosmos DB previsto nella `WITH` clausola. Non utilizzare `OPENROWSET` senza una `WITH` clausola nei report poiché il formato del set di risultati può essere modificato nell'anteprima in base ai commenti e suggerimenti.

Nell'esempio seguente si presuppone che `string` sia il tipo corretto per la `geo_id` proprietà ed `int32` è il tipo corretto per la `cases` proprietà:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

I valori per `geo_id` e con `cases` altri tipi verranno restituiti come `NULL` valori. Questa query fa riferimento solo all'oggetto `cases` con il tipo specificato nell'espressione ( `cases.int32` ).

Se sono presenti valori con altri tipi ( `cases.int64` , `cases.float64` ) che non possono essere puliti in un contenitore Azure Cosmos DB, è necessario farvi riferimento in modo esplicito in una `WITH` clausola e combinare i risultati. Nella query seguente vengono aggregati sia `int32` , `int64` che `float64` archiviati nella `cases` colonna:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

In questo esempio il numero di case viene archiviato come `int32` `int64` valori, o `float64` . Per calcolare il numero di case per paese, è necessario estrarre tutti i valori.

## <a name="known-issues"></a>Problemi noti

- L'esperienza di query fornita da un pool SQL senza server per [Azure Cosmos DB lo schema di fedeltà completa](#full-fidelity-schema) è un comportamento temporaneo che verrà modificato in base al feedback dell'anteprima. Non fare affidamento sullo schema che la `OPENROWSET` funzione senza la `WITH` clausola fornisce durante l'anteprima pubblica perché l'esperienza di query potrebbe essere allineata con uno schema ben definito in base ai commenti dei clienti. Per inviare commenti e suggerimenti, contattare il [team del prodotto collegamento di sinapsi di Azure](mailto:cosmosdbsynapselink@microsoft.com).
- Un pool SQL senza server restituirà un avviso in fase di compilazione se le `OPENROWSET` regole di confronto della colonna non includono la codifica UTF-8. È possibile modificare facilmente le regole di confronto predefinite per tutte le `OPENROWSET` funzioni in esecuzione nel database corrente usando l'istruzione T-SQL `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` .

Nella tabella seguente sono elencati i possibili errori e le azioni per la risoluzione dei problemi.

| Errore | Causa radice |
| --- | --- |
| Errori di sintassi:<br/> -Sintassi non corretta in prossimità `Openrowset`<br/> - `...` non è un' `BULK OPENROWSET` opzione del provider riconosciuta.<br/> -Sintassi non corretta in prossimità `...` | Possibili cause principali:<br/> -Non si utilizza CosmosDB come primo parametro.<br/> -Utilizzo di un valore letterale stringa anziché di un identificatore nel terzo parametro.<br/> -Non specifica il terzo parametro (nome del contenitore). |
| Si è verificato un errore nella stringa di connessione CosmosDB. | -L'account, il database o la chiave non è specificata. <br/> -Esiste un'opzione in una stringa di connessione non riconosciuta.<br/> -Un punto e virgola ( `;` ) viene inserito alla fine di una stringa di connessione. |
| La risoluzione del percorso di CosmosDB non è riuscita con l'errore "nome account errato" o "nome database errato". | Il nome dell'account, il nome del database o il contenitore specificato non è stato trovato oppure l'archiviazione analitica non è stata abilitata per la raccolta specificata.|
| La risoluzione del percorso di CosmosDB non è riuscita con l'errore "valore segreto errato" o "segreto null o vuoto". | La chiave dell'account non è valida o è mancante. |
| La colonna `column name` del tipo `type name` non è compatibile con il tipo di dati esterno `type name` . | Il tipo di colonna specificato nella `WITH` clausola non corrisponde al tipo nel contenitore Azure Cosmos DB. Provare a modificare il tipo di colonna come descritto nella sezione [Azure Cosmos DB ai mapping dei tipi SQL](#azure-cosmos-db-to-sql-type-mappings)oppure utilizzare il `VARCHAR` tipo. |
| La colonna contiene `NULL` i valori in tutte le celle. | Probabilmente è presente un nome di colonna o un'espressione di percorso errato nella `WITH` clausola. Il nome della colonna (o l'espressione di percorso dopo il tipo di colonna) nella `WITH` clausola deve corrispondere a un nome di proprietà nella raccolta di Azure Cosmos DB. Il confronto fa *distinzione tra maiuscole e* minuscole. Ad esempio, `productCode` e `ProductCode` sono proprietà diverse. |

È possibile segnalare suggerimenti e problemi nella pagina dei commenti e suggerimenti su [Azure sinapsi Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti:

- [Usare Power BI e il pool SQL senza server con il collegamento a sinapsi di Azure](../../cosmos-db/synapse-link-power-bi.md)
- [Creazione e uso di viste in un pool SQL senza server](create-use-views.md)
- [Esercitazione sulla creazione di viste del pool SQL senza server su Azure Cosmos DB e sulla connessione a modelli di Power BI tramite DirectQuery](./tutorial-data-analyst.md)
