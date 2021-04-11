---
title: Eseguire la migrazione dei dati a un account dell'API Cassandra in Azure Cosmos DB - Esercitazione
description: Questa esercitazione illustra come copiare dati da Apache Cassandra a un account API Cassandra in Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449291"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Esercitazione: eseguire la migrazione dei dati a un account API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Gli sviluppatori potrebbero avere carichi di lavoro Cassandra esistenti in esecuzione in locale o nel cloud e volerne eseguire la migrazione in Azure. È possibile eseguire la migrazione di questi carichi di lavoro in un account dell'API Cassandra in Azure Cosmos DB. Questa esercitazione fornisce istruzioni sulle diverse opzioni disponibili per eseguire la migrazione dei dati Apache Cassandra nell'account dell'API Cassandra di Azure Cosmos DB.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Pianificare la migrazione
> * Prerequisiti per la migrazione
> * Eseguire la migrazione dei dati tramite il `cqlsh` `COPY` comando
> * Eseguire la migrazione dei dati con Spark

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites-for-migration"></a>Prerequisiti per la migrazione

* **Elaborare una stima delle esigenze di velocità effettiva:** prima di eseguire la migrazione dei dati nell'account dell'API Cassandra in Azure Cosmos DB, è necessario elaborare una stima delle esigenze di velocità effettiva del carico di lavoro. In generale, iniziare con la velocità effettiva media necessaria per le operazioni CRUD, quindi includere la velocità effettiva aggiuntiva necessaria per le operazioni di caricamento della trasformazione Estrazione o spiky. Per pianificare la migrazione, sono necessari i dettagli seguenti: 

  * **Dimensioni dei dati esistenti o stimate:** definisce le dimensioni minime del database e i requisiti di velocità effettiva. Per stimare le dimensioni dei dati per una nuova applicazione, è possibile presupporre che i dati siano distribuiti in modo uniforme tra le righe e stimare il valore moltiplicando per la dimensione dei dati. 

  * **Velocità effettiva necessaria:** Velocità effettiva approssimativa delle operazioni di lettura (query/Get) e di scrittura (aggiornamento/eliminazione/inserimento). Questo valore è necessario per calcolare le unità richiesta richieste, insieme alle dimensioni dei dati di stato stazionario.  

  * **Schema:** Connettersi al cluster Cassandra esistente tramite `cqlsh` ed esportare lo schema da Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Dopo aver identificato i requisiti del carico di lavoro esistente, creare un account Azure Cosmos DB, un database e i contenitori in base ai requisiti di velocità effettiva raccolti.  

  * **Determinare gli addebiti di UR per un'operazione:** è possibile determinare le UR usando uno degli SDK supportati dall'API Cassandra. Questo esempio illustra la versione .NET per ottenere gli addebiti delle unità richiesta.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Allocare la velocità effettiva richiesta:** Azure Cosmos DB può ridimensionare automaticamente l'archiviazione e la velocità effettiva man mano che aumentano i requisiti. È possibile stimare le esigenze di velocità effettiva usando il [calcolatore di unità di richieste di Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Creare tabelle nell'account API Cassandra:** Prima di iniziare la migrazione dei dati, creare prima di tutto le tabelle dal portale di Azure o da `cqlsh` . Se si esegue la migrazione a un account Azure Cosmos DB con velocità effettiva a livello di database, assicurarsi di specificare una chiave di partizione durante la creazione dei contenitori.

* **Aumentare la velocità effettiva:** la durata della migrazione dei dati dipende dalla velocità effettiva di cui è stato eseguito il provisioning per le tabelle in Azure Cosmos DB. Aumentare la velocità effettiva per la durata della migrazione. Con una velocità effettiva più elevata, è possibile evitare la limitazione di velocità e completare più rapidamente la migrazione. Dopo avere completato la migrazione, diminuire la velocità effettiva per ridurre i costi. Si consiglia inoltre di avere l'account Azure Cosmos DB nella stessa area del database di origine. 

* **Abilitare TLS:** Azure Cosmos DB presenta standard e requisiti di sicurezza restrittivi. Assicurarsi di abilitare TLS quando si interagisce con l'account. Quando si usa CQL con SSH, è possibile fornire le informazioni TLS.

## <a name="options-to-migrate-data"></a>Opzioni per la migrazione dei dati

È possibile spostare i dati da carichi di lavoro Cassandra esistenti a Azure Cosmos DB usando il `cqlsh` `COPY` comando o Spark. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Eseguire la migrazione dei dati tramite il comando cqlsh COPY

Usare il [comando copy di CQL](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) per copiare i dati locali nell'account API Cassandra in Azure Cosmos DB.

1. Ottenere informazioni sulla stringa di connessione dell'account dell'API Cassandra:

   * Accedere al [portale di Azure](https://portal.azure.com)e passare all'account Azure Cosmos DB.

   * Aprire il riquadro **Stringa di connessione**. Qui vengono visualizzate tutte le informazioni necessarie per connettersi all'account di API Cassandra da `cqlsh` .

1. Accedere a `cqlsh` usando le informazioni di connessione del portale.

1. Usare il `CQL` `COPY` comando per copiare i dati locali nell'account API Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Eseguire la migrazione dei dati con Spark 

Usare questa procedura per eseguire la migrazione dei dati nell'account dell'API Cassandra con Spark:

1. Effettuare il provisioning di un [cluster Azure Databricks](cassandra-spark-databricks.md) o di un [cluster HDInsight di Azure](cassandra-spark-hdinsight.md). 

1. Spostare i dati nell'endpoint API Cassandra di destinazione usando l' [operazione di copia della tabella](cassandra-spark-table-copy-ops.md). 

La migrazione dei dati usando i processi Spark è un'opzione consigliata se i dati risiedono in un cluster esistente nelle macchine virtuali di Azure o in qualsiasi altro cloud. A tale scopo, è necessario configurare Spark come intermediario per l'inserimento monouso o regolare. Per accelerare questa migrazione, è possibile usare la connettività ExpressRoute di Azure tra l'ambiente locale e Azure. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile eliminare il gruppo di risorse, l'account Azure Cosmos DB e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare **Elimina** e quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come eseguire la migrazione dei dati a un account API Cassandra in Azure Cosmos DB. È ora possibile ottenere informazioni sugli altri concetti in Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB](../cosmos-db/consistency-levels.md)




