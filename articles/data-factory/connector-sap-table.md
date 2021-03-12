---
title: Copiare dati da una tabella SAP
description: Informazioni su come copiare dati da una tabella SAP in archivi dati di sink supportati usando un'attività di copia in una pipeline Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2021
ms.openlocfilehash: 4026d2f987ca37834231ac4d7e827ff543af9d2e
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232393"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiare dati da una tabella SAP usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da una tabella SAP. Per altre informazioni, vedere [Panoramica dell'attività di copia](copy-activity-overview.md).

>[!TIP]
>Per informazioni sul supporto generale di ADF sullo scenario di integrazione dei dati SAP, vedere l'articolo relativo all' [integrazione dei dati SAP con Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) con un'introduzione dettagliata su ogni connettore SAP, comparsing e linee guida.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore della tabella SAP è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da una tabella SAP in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats) .

In particolare, questo connettore di tabella SAP supporta:

- Copia di dati da una tabella SAP in:

  - SAP ERP Central Component (SAP ECC) versione 7,01 o successiva (in uno stack del pacchetto di supporto SAP recente rilasciato dopo 2015).
  - SAP Business Warehouse (SAP BW) versione 7,01 o successiva (in uno stack di pacchetti di supporto SAP recente rilasciato dopo 2015).
  - SAP S/4HANA.
  - Altri prodotti in SAP Business Suite versione 7,01 o successiva (in uno stack del pacchetto di supporto SAP recente rilasciato dopo 2015).

- Copia di dati da una tabella di SAP trasparente, una tabella in pool, una tabella cluster e una vista.
- La copia dei dati tramite l'autenticazione di base o le comunicazioni di rete sicure (SNC), se è configurata la SNC.
- Connessione a un server applicazioni SAP o a un server di messaggi SAP.
- Recupero di dati tramite RFC predefinito o personalizzato.

La versione 7,01 o successiva si riferisce alla versione di SAP NetWeaver invece che alla versione SAP ECC. Ad esempio, SAP ECC 6,0 EHP 7 in generale presenta NetWeaver Version >= 7,4. Se non si è certi dell'ambiente, di seguito sono riportati i passaggi per confermare la versione dal sistema SAP:

1. Usare l'interfaccia utente grafica SAP per connettersi al sistema SAP. 
2. Passare a   ->  **stato** del sistema. 
3. Controllare la versione del SAP_BASIS, verificare che sia uguale o maggiore di 701.  
      ![Controlla SAP_BASIS](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore di tabella SAP, è necessario:

- Configurare un runtime di integrazione self-hosted (versione 3,17 o successiva). Per altre informazioni, vedere [creare e configurare un runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).

- Scaricare il [connettore SAP a 64 bit per Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) dal sito Web di SAP e installarlo nel computer del runtime di integrazione self-hosted. Durante l'installazione, assicurarsi di selezionare l'opzione **Installa assembly per GAC** nella finestra **passaggi di installazione facoltativi** .

  ![Installare SAP Connector per .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- L'utente SAP usato nel connettore Data Factory SAP TABLE deve avere le autorizzazioni seguenti:

  - Autorizzazione per l'utilizzo di destinazioni della chiamata di funzione remota (RFC).
  - Autorizzazioni per l'attività Execute dell'oggetto S_SDSAUTH autorizzazione. È possibile fare riferimento alla nota SAP 40089 sulla maggior parte degli oggetti di autorizzazione. Alcune RFC sono richieste dal connettore NCo sottostante, ad esempio RFC_FUNCTION_SEARCH. 

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire le entità Data Factory specifiche del connettore della tabella SAP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato SAP BW Hub aperto sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | La proprietà `type` deve essere impostata su `SapTable`. | Sì |
| `server` | Nome del server in cui si trova l'istanza di SAP.<br/>Usare per connettersi a un server applicazioni SAP. | No |
| `systemNumber` | Il numero di sistema del sistema SAP.<br/>Usare per connettersi a un server applicazioni SAP.<br/>Valore consentito: numero decimale A due cifre rappresentato come stringa. | No |
| `messageServer` | Nome host del server dei messaggi SAP.<br/>Usare per connettersi a un server di messaggi SAP. | No |
| `messageServerService` | Nome del servizio o numero di porta del server dei messaggi.<br/>Usare per connettersi a un server di messaggi SAP. | No |
| `systemId` | ID del sistema SAP in cui si trova la tabella.<br/>Usare per connettersi a un server di messaggi SAP. | No |
| `logonGroup` | Il gruppo di accesso per il sistema SAP.<br/>Usare per connettersi a un server di messaggi SAP. | No |
| `clientId` | ID del client nel sistema SAP.<br/>Valore consentito: numero decimale A tre cifre rappresentato come stringa. | Sì |
| `language` | Linguaggio utilizzato dal sistema SAP.<br/>Il valore predefinito è `EN`.| No |
| `userName` | Nome dell'utente che ha accesso al server SAP. | Sì |
| `password` | La password dell'utente. Contrassegnare questo campo con il `SecureString` tipo per archiviarlo in modo sicuro in data factory oppure [fare riferimento a un segreto archiviato nel Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| `sncMode` | Indicatore di attivazione di SNC per accedere al server SAP in cui si trova la tabella.<br/>Usare se si vuole usare SNC per connettersi al server SAP.<br/>I valori consentiti sono `0` (disattivato, valore predefinito) o `1` (on). | No |
| `sncMyName` | Nome della SNC dell'Initiator per accedere al server SAP in cui si trova la tabella.<br/>Si applica quando `sncMode` è on. | No |
| `sncPartnerName` | Nome della SNC del partner di comunicazione per accedere al server SAP in cui si trova la tabella.<br/>Si applica quando `sncMode` è on. | No |
| `sncLibraryPath` | Libreria del prodotto di sicurezza esterna per accedere al server SAP in cui si trova la tabella.<br/>Si applica quando `sncMode` è on. | No |
| `sncQop` | Qualità SNC del livello di protezione da applicare.<br/>Si applica quando `sncMode` è on. <br/>I valori consentiti sono `1` (Authentication), `2` (integrità), `3` (privacy), `8` (impostazione predefinita), `9` (Maximum). | No |
| `connectVia` | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Un runtime di integrazione self-hosted è obbligatorio, come indicato in precedenza in [prerequisiti](#prerequisites). |Sì |

### <a name="example-1-connect-to-an-sap-application-server"></a>Esempio 1: connettersi a un server applicazioni SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Esempio 2: connettersi a un server di messaggi SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Esempio 3: connettersi tramite SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà per la definizione dei set di impostazioni, vedere [DataSets](concepts-datasets-linked-services.md). La sezione seguente presenta un elenco delle proprietà supportate dal set di dati della tabella SAP.

Per copiare dati da e verso il SAP BW servizio collegato Hub aperto, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | La proprietà `type` deve essere impostata su `SapTableResource`. | Sì |
| `tableName` | Nome della tabella SAP da cui copiare i dati. | Sì |

### <a name="example"></a>Esempio

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà per la definizione delle attività, vedere [pipeline](concepts-pipelines-activities.md). Nella sezione seguente viene fornito un elenco delle proprietà supportate dall'origine della tabella SAP.

### <a name="sap-table-as-source"></a>Tabella SAP come origine

Per copiare dati da una tabella SAP, sono supportate le proprietà seguenti:

| Proprietà                         | Descrizione                                                  | Obbligatoria |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | La proprietà `type` deve essere impostata su `SapTableSource`.         | Sì      |
| `rowCount`                         | Numero di righe da recuperare.                              | No       |
| `rfcTableFields`                 | Campi (colonne) da copiare dalla tabella SAP. Ad esempio: `column0, column1`. | No       |
| `rfcTableOptions`                | Opzioni per filtrare le righe in una tabella SAP. Ad esempio: `COLUMN0 EQ 'SOMEVALUE'`. Vedere anche la tabella degli operatori di query SAP più avanti in questo articolo. | No       |
| `customRfcReadTableFunctionModule` | Un modulo della funzione RFC personalizzato che può essere usato per leggere i dati da una tabella SAP.<br>È possibile usare un modulo della funzione RFC personalizzato per definire il modo in cui i dati vengono recuperati dal sistema SAP e restituiti a Data Factory. Il modulo della funzione personalizzata deve disporre di un'interfaccia implementata (importazione, esportazione, tabelle) simile a `/SAPDS/RFC_READ_TABLE2` , ovvero l'interfaccia predefinita utilizzata da data factory.<br>Data Factory | No       |
| `partitionOption`                  | Meccanismo di partizione per la lettura da una tabella SAP. Le opzioni supportate includono: <ul><li>`None`</li><li>`PartitionOnInt` (valori integer o Integer normali con riempimento zero a sinistra, ad esempio `0000012345` )</li><li>`PartitionOnCalendarYear` (4 cifre nel formato "aaaa")</li><li>`PartitionOnCalendarMonth` (6 cifre nel formato "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 cifre nel formato "AAAAMMGG")</li><li>`PartitionOntime` (6 cifre nel formato "HHMMSS", ad esempio `235959` )</li></ul> | No       |
| `partitionColumnName`              | Nome della colonna utilizzata per partizionare i dati.                | No       |
| `partitionUpperBound`              | Valore massimo della colonna specificata in `partitionColumnName` che verrà utilizzato per continuare con il partizionamento. | No       |
| `partitionLowerBound`              | Valore minimo della colonna specificata in `partitionColumnName` che verrà utilizzato per continuare con il partizionamento. (Nota: `partitionLowerBound` non può essere "0" se l'opzione di partizione è `PartitionOnInt` ) | No       |
| `maxPartitionsNumber`              | Numero massimo di partizioni in cui dividere i dati.     | No       |
| `sapDataColumnDelimiter` | Singolo carattere utilizzato come delimitatore passato a SAP RFC per suddividere i dati di output. | No |

>[!TIP]
>Se la tabella SAP include un volume elevato di dati, ad esempio diversi miliardi di righe, usare `partitionOption` e `partitionSetting` per suddividere i dati in partizioni più piccole. In questo caso, i dati vengono letti per partizione e ogni partizione di dati viene recuperata dal server SAP tramite una singola chiamata RFC.<br/>
<br/>
>`partitionOption`Come `partitionOnInt` esempio, il numero di righe in ogni partizione viene calcolato con la formula seguente: (numero totale di righe comprese tra `partitionUpperBound` e `partitionLowerBound` )/ `maxPartitionsNumber` .<br/>
<br/>
>Per caricare le partizioni di dati in parallelo per velocizzare la copia, il grado di parallelismo è controllato dall' [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) impostazione dell'attività di copia. Se, ad esempio, si imposta `parallelCopies` su quattro, data factory genera ed esegue contemporaneamente quattro query in base all'opzione di partizione specificata e alle impostazioni e ogni query recupera una parte di dati dalla tabella SAP. Si consiglia vivamente di creare `maxPartitionsNumber` un multiplo del valore della `parallelCopies` Proprietà. Quando si copiano dati in un archivio dati basato su file, viene anche riordinato di scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.


>[!TIP]
> `BASXML`Per impostazione predefinita, è abilitato per questo connettore della tabella SAP sul lato Azure Data Factory.

In `rfcTableOptions` è possibile utilizzare gli operatori di query SAP comuni seguenti per filtrare le righe:

| Operatore | Descrizione |
| :------- | :------- |
| `EQ` | Uguale a |
| `NE` | Diverso da |
| `LT` | Minore di |
| `LE` | Minore o uguale a |
| `GT` | Maggiore di |
| `GE` | Maggiore o uguale a |
| `IN` | Come in `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | Come in `LIKE 'Emma%'` |

### <a name="example"></a>Esempio

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="join-sap-tables"></a>Aggiungere tabelle SAP

Il connettore tabella SAP supporta attualmente solo una singola tabella con il modulo funzione predefinito. Per ottenere i dati uniti in join di più tabelle, è possibile usare la proprietà [customRfcReadTableFunctionModule](#copy-activity-properties) in SAP Table Connector attenendosi alla procedura seguente:

- [Scrivere un modulo di funzione personalizzato](#create-custom-function-module), che può usare una query come opzioni e applicare la logica personalizzata per recuperare i dati.
- Per "Custom Function Module" immettere il nome del modulo della funzione personalizzata.
- Per le opzioni della tabella RFC, specificare l'istruzione Table join da inserire nel modulo della funzione come opzioni, ad esempio " `<TABLE1>` inner join `<TABLE2>` on COLUMN0".

Di seguito è riportato un esempio:

![Join di tabella SAP](./media/connector-sap-table/sap-table-join.png) 

>[!TIP]
>È anche possibile considerare che i dati uniti in join sono aggregati nella vista, supportata da SAP Table Connector.
>È anche possibile provare a estrarre le tabelle correlate per eseguire l'onboarding in Azure, ad esempio archiviazione di Azure, database SQL di Azure, quindi usare il flusso di dati per procedere con un ulteriore join o filtro.

## <a name="create-custom-function-module"></a>Crea modulo funzione personalizzata

Per la tabella SAP, attualmente è supportata la proprietà [customRfcReadTableFunctionModule](#copy-activity-properties) nell'origine della copia, che consente di sfruttare la logica ed elaborare i dati.

Come guida rapida, di seguito sono riportati alcuni requisiti per iniziare a usare il "modulo funzione personalizzata":

- Definizione:

    ![Definizione](./media/connector-sap-table/custom-function-module-definition.png) 

- Esportare i dati in una delle tabelle seguenti:

    ![Esporta tabella 1](./media/connector-sap-table/export-table-1.png) 

    ![Esporta tabella 2](./media/connector-sap-table/export-table-2.png)
 
Di seguito sono illustrate le modalità di funzionamento di SAP Table Connector con il modulo funzione personalizzata:

1. Creare una connessione con il server SAP tramite SAP NCO.

1. Richiamare "Custom Function Module" con i parametri impostati come indicato di seguito:

    - QUERY_TABLE: il nome della tabella impostato nel set di dati della tabella SAP di ADF. 
    - Delimitatore: il delimitatore impostato nell'origine della tabella SAP di ADF. 
    - RIGHE/opzioni/campi: i campi o l'opzione RowCount/aggregated impostati nell'origine della tabella di ADF.

1. Ottenere il risultato e analizzare i dati nei modi seguenti:

    1. Analizzare il valore nella tabella Fields per ottenere gli schemi.

        ![Analizzare i valori nei campi](./media/connector-sap-table/parse-values.png)

    1. Ottenere i valori della tabella di output per vedere quale tabella contiene questi valori.

        ![Ottenere i valori nella tabella di output](./media/connector-sap-table/get-values.png)

    1. Ottenere i valori nel OUT_TABLE, analizzare i dati e quindi scriverli nel sink.

## <a name="data-type-mappings-for-an-sap-table"></a>Mapping dei tipi di dati per una tabella SAP

Quando si copiano dati da una tabella SAP, vengono usati i mapping seguenti tra i tipi di dati della tabella SAP e i tipi di dati Azure Data Factory interim. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo SAP ABAP | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| `C` Stringa | `String` |
| `I` Intero | `Int32` |
| `F` Float | `Double` |
| `D` Data | `String` |
| `T` Tempo | `String` |
| `P` (BCD compresso, Currency, Decimal, Qty) | `Decimal` |
| `N` Numerico | `String` |
| `X` (Binario e non elaborato) | `String` |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
