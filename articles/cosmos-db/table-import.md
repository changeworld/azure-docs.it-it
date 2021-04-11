---
title: Eseguire la migrazione dei dati esistenti a un account API Tabella in Azure Cosmos DB
description: Informazioni su come eseguire la migrazione o importare dati locali o cloud in un account API Tabella di Azure Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443339"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Eseguire la migrazione dei dati a un account di API Tabella Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Questa esercitazione fornisce le istruzioni per importare i dati da usare con l'[API Tabelle](table-introduction.md) di Azure Cosmos DB. Se sono presenti dati archiviati nell'archiviazione tabelle di Azure, è possibile usare l'utilità di migrazione dati o AzCopy per importare i dati nel API Tabella di Azure Cosmos DB. 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Importazione di dati con l'utilità di migrazione dati
> * Importazione dei dati con AzCopy

## <a name="prerequisites"></a>Prerequisiti

* **Aumentare la velocità effettiva:** la durata della migrazione dei dati dipende dalla velocità effettiva configurata per un singolo contenitore o un set di contenitori. Assicurarsi di aumentare la velocità effettiva per le migrazioni dei dati di dimensioni più grandi. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi.

* **Creare risorse Azure Cosmos DB:** Prima di iniziare la migrazione dei dati, creare tutte le tabelle dal portale di Azure. Se si esegue la migrazione a un account Azure Cosmos DB con velocità effettiva a livello di database, assicurarsi di specificare una chiave di partizione durante la creazione delle tabelle Azure Cosmos DB.

## <a name="data-migration-tool"></a>Strumento di migrazione dei dati

È possibile usare lo strumento di migrazione dei dati da riga di comando (dt.exe) in Azure Cosmos DB per importare i dati di archiviazione tabelle di Azure esistenti in un account API Tabella. 

Per eseguire la migrazione dei dati della tabella:

1. Scaricare l'Utilità di migrazione dati da [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Eseguire `dt.exe` usando gli argomenti della riga di comando per lo scenario. `dt.exe` accetta un comando nel formato seguente:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Le opzioni supportate per questo comando sono:

* **/ErrorLog:** Facoltativa. Nome del file CSV per il reindirizzamento degli errori di trasferimento dei dati.
* **/OverwriteErrorLog:** Facoltativa. Sovrascrivere il file di log degli errori.
* **/ProgressUpdateInterval:** Facoltativo, il valore predefinito è `00:00:01` . Intervallo di tempo per l'aggiornamento dello stato di trasferimento dei dati sullo schermo.
* **/ErrorDetails:** Facoltativo, il valore predefinito è `None` . Specifica che devono essere visualizzate informazioni dettagliate sull'errore per gli errori seguenti: `None` , `Critical` o `All` .
* **/EnableCosmosTableLog:** Facoltativa. Indirizzare il log a un account Azure Cosmos DB tabella. Se impostata, per impostazione predefinita viene specificata la stringa di connessione dell'account di destinazione, a meno che non `/CosmosTableLogConnectionString` sia specificato anche. Questa operazione è utile se più istanze dello strumento vengono eseguite simultaneamente.
* **/CosmosTableLogConnectionString:** Facoltativa. Stringa di connessione per indirizzare il log a un account Azure Cosmos DB tabella remota.

### <a name="command-line-source-settings"></a>Impostazioni dell'origine della riga di comando

Usare le opzioni di origine seguenti quando si definisce l'archiviazione tabelle di Azure come origine della migrazione.

* **/s: AzureTable:** Legge i dati dall'archivio tabelle.
* **/s.ConnectionString:** Stringa di connessione per l'endpoint tabella. È possibile recuperarlo dalla portale di Azure.
* **/s.LocationMode:** Facoltativo, il valore predefinito è `PrimaryOnly` . Specifica la modalità di individuazione da utilizzare per la connessione all'archiviazione tabelle: `PrimaryOnly` ,, `PrimaryThenSecondary` `SecondaryOnly` , `SecondaryThenPrimary` .
* **/s.Table:** Nome della tabella di Azure.
* **/s.InternalFields:** Impostare su `All` per la migrazione della tabella, perché `RowKey` e `PartitionKey` sono necessari per l'importazione.
* **/s.Filter:** Facoltativa. Stringa di filtro da applicare.
* **/s.Projection:** Facoltativa. Elenco di colonne da selezionare,

Per recuperare la stringa di connessione di origine quando si importa dall'archivio tabelle, aprire il portale di Azure. Selezionare account di **archiviazione**  >    >  **chiavi di accesso** account e copiare la **stringa di connessione**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Screenshot che Mostra gli account di archiviazione > le opzioni di account > chiavi di accesso ed evidenzia l'icona di copia.":::

### <a name="command-line-target-settings"></a>Impostazioni della destinazione della riga di comando

Utilizzare le seguenti opzioni di destinazione quando si definisce il Azure Cosmos DB API Tabella come destinazione della migrazione.

* **/t: TableAPIBulk:** Carica i dati nel Azure Cosmos DB API Tabella in batch.
* **/t.ConnectionString:** Stringa di connessione per l'endpoint della tabella.
* **/t.TableName:** Specifica il nome della tabella in cui scrivere.
* **/t.overwrite:** Facoltativo, il valore predefinito è `false` . Specifica se i valori esistenti devono essere sovrascritti.
* **/t.MaxInputBufferSize:** Facoltativo, il valore predefinito è `1GB` . Stima approssimativa dei byte di input da memorizzare nel buffer prima di scaricare i dati nel sink.
* **/t.Throughput:** Facoltativa. Se non è specificata, vengono usate le impostazioni predefinite del servizio. Specifica la velocità effettiva da configurare per la tabella.
* **/t.MaxBatchSize:** Facoltativo, il valore predefinito è `2MB` . Specificare le dimensioni del batch in byte.

### <a name="sample-command-source-is-table-storage"></a>Comando di esempio: l'origine è l'archiviazione tabelle

Di seguito è riportato un esempio di riga di comando che illustra come importare dall'archivio tabelle al API Tabella:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Eseguire la migrazione dei dati mediante AzCopy

È inoltre possibile utilizzare l'utilità della riga di comando AzCopy per eseguire la migrazione dei dati dall'archiviazione tabelle al API Tabella Azure Cosmos DB. Per usare AzCopy, è necessario innanzitutto esportare i dati come descritto in [esportare dati da archiviazione tabelle](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage). Importare quindi i dati in Azure Cosmos DB come descritto in [Azure Cosmos DB API tabella](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Quando si esegue l'importazione in Azure Cosmos DB, vedere l'esempio seguente. Si noti che il `/Dest` valore USA `cosmosdb` , non `core` .

Comando di importazione di esempio:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire query sui dati usando il API Tabella Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Come eseguire query sui dati](../cosmos-db/tutorial-query-table.md)




