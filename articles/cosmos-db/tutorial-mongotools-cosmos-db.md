---
title: Migrare MongoDB offline all'API Azure Cosmos DB per MongoDB, usando gli strumenti nativi MongoDB
description: Informazioni sul modo in cui è possibile usare gli strumenti nativi MongoDB per eseguire la migrazione di piccoli set di impostazioni da istanze di MongoDB a Azure Cosmos DB
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2e9f3c877a5c4650d3e31fa414cac76837f4c9e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101655752"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Esercitazione: eseguire la migrazione di MongoDB all'API Azure Cosmos DB per MongoDB offline usando gli strumenti nativi MongoDB

È possibile usare gli strumenti nativi di MongoDB per eseguire una migrazione offline (una volta) di database da un'istanza locale o cloud di MongoDB all'API di Azure Cosmos DB per MongoDB.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Scegliere lo strumento nativo MongoDB appropriato per il caso d'uso
> * Eseguire la migrazione.
> * Monitorare la migrazione.
> * Verificare che la migrazione sia stata completata correttamente.

In questa esercitazione si esegue la migrazione di un set di dati in MongoDB ospitato in una macchina virtuale di Azure all'API Azure Cosmos DB per MongoDB usando gli strumenti nativi di MongoDB. Gli strumenti nativi MongoDB sono un set di file binari che facilitano la manipolazione dei dati in un'istanza di MongoDB esistente. Poiché Azure Cosmos DB espone un'API Mongo, gli strumenti nativi MongoDB sono in grado di inserire i dati in Azure Cosmos DB. L'obiettivo di questo documento è la migrazione dei dati da un'istanza di MongoDB usando *mongoexport/mongoimport* o *mongodump/mongorestore*. Poiché gli strumenti nativi si connettono a MongoDB usando le stringhe di connessione, è possibile eseguire gli strumenti ovunque, ma è consigliabile eseguire questi strumenti all'interno della stessa rete dell'istanza di MongoDB per evitare problemi del firewall. 

Gli strumenti nativi MongoDB possono spostare i dati solo con la stessa velocità con cui l'hardware dell'host lo consente; gli strumenti nativi possono essere la soluzione più semplice per i set di impostazioni di piccole dimensioni in cui il tempo totale di migrazione non costituisce un problema. Il [connettore Spark MongoDB](https://docs.mongodb.com/spark-connector/current/), il [servizio di migrazione dei dati di Azure (DMS)](../dms/tutorial-mongodb-cosmos-db.md)o [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) può essere una soluzione alternativa migliore se è necessaria una pipeline di migrazione scalabile.

Se non è già stata configurata un'origine MongoDB, vedere l'articolo [Installare e configurare MongoDB in una VM Windows in Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* [Completare la procedura di pre-migrazione](../cosmos-db/mongodb-pre-migration.md), ad esempio stimare la velocità effettiva, scegliere una chiave di partizione e i criteri di indicizzazione.
* [Creare un'API Azure Cosmos DB per l'account MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Accedere all'istanza di MongoDB
    * [Scaricare e installare gli strumenti nativi MongoDB da questo collegamento](https://www.mongodb.com/try/download/database-tools).
        * **Verificare che la versione degli strumenti nativi MongoDB corrisponda all'istanza di MongoDB esistente.**
        * Se l'istanza di MongoDB ha una versione diversa rispetto a Azure Cosmos DB l'API Mongo, **installare entrambe le versioni dello strumento MongoDB native e usare la versione dello strumento appropriata per MongoDB e Azure Cosmos DB l'API Mongo, rispettivamente.**
    * Aggiungere un utente con le `readWrite` autorizzazioni, a meno che non esista già uno. Più avanti in questa esercitazione, fornire questo nome utente/password agli strumenti *mongoexport* e *mongodump* .

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Configurare Azure Cosmos DB tentativi lato server

I clienti che eseguono la migrazione da MongoDB a Azure Cosmos DB traggono vantaggio dalle funzionalità di governance delle risorse, che garantiscono la possibilità di usare in modo completo le UR/sec di cui è stato effettuato il provisioning. Azure Cosmos DB possibile limitare una determinata richiesta nel corso della migrazione se tale richiesta supera le UR/sec di cui è stato effettuato il provisioning del contenitore; la richiesta deve quindi essere ritentata. Il tempo di round trip interessato dall'hop di rete tra lo strumento di migrazione e Azure Cosmos DB influisca sul tempo di risposta complessivo della richiesta; Inoltre, gli strumenti nativi MongoDB potrebbero non gestire i tentativi. La funzionalità di *ripetizione dei tentativi sul lato server* di Azure Cosmos DB consente al servizio di intercettare i codici di errore e riprovare con un tempo di round trip molto inferiore, migliorando in modo significativo i tempi di risposta della richiesta. Dal punto di vista degli strumenti nativi di MongoDB, la necessità di gestire i tentativi è ridotta a icona, che avrà un impatto positivo sulla propria esperienza durante la migrazione.

È possibile trovare la funzionalità di ripetizione dei tentativi sul lato server nel pannello *funzionalità* del portale di Azure Cosmos DB

![Screenshot della funzionalità SSR di MongoDB.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Se è *disabilitato*, è consigliabile abilitarlo come illustrato di seguito.

![Screenshot di MongoDB SSR Enable.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Scegliere lo strumento nativo MongoDB appropriato

![Diagramma della selezione dello strumento nativo MongoDB migliore.](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* è la coppia migliore di strumenti di migrazione per la migrazione di un subset del database MongoDB.
    * *mongoexport* Esporta i dati esistenti in un file JSON o CSV leggibile. *mongoexport* accetta un argomento che specifica il subset dei dati esistenti da esportare. 
    * *mongoimport* apre un file JSON o CSV e inserisce il contenuto nell'istanza del database di destinazione (Azure Cosmos DB in questo caso). 
    * Si noti che JSON e CSV non sono formati Compact; è possibile che si verifichino addebiti di rete in eccesso perché *mongoimport* invia dati a Azure Cosmos DB.
* *mongodump/mongorestore* è la coppia migliore di strumenti di migrazione per la migrazione dell'intero database MongoDB. Il formato Compact BSON renderà più efficiente l'uso delle risorse di rete quando i dati vengono inseriti in Azure Cosmos DB.
    * *mongodump* Esporta i dati esistenti come file BSON.
    * *mongorestore* importa il dump del file BSON in Azure Cosmos DB.
* A parte, se si dispone semplicemente di un file JSON di dimensioni ridotte che si vuole importare in Azure Cosmos DB l'API Mongo, lo strumento *mongoimport* è una soluzione rapida per l'inserimento dei dati.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Raccogli le credenziali dell'API Mongo Azure Cosmos DB

Azure Cosmos DB l'API Mongo fornisce credenziali di accesso compatibili che possono essere usate dagli strumenti nativi MongoDB. È necessario disporre di queste credenziali di accesso per eseguire la migrazione dei dati in Azure Cosmos DB l'API Mongo. Per trovare le credenziali seguenti:

1. Aprire il portale di Azure
1. Passa all'account di Azure Cosmos DB mongo API
1. Nel riquadro di spostamento a sinistra selezionare il pannello *stringa di connessione* . verrà visualizzata una schermata simile alla seguente:

    ![Screenshot delle credenziali Azure Cosmos DB.](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *Host* : l'endpoint Azure Cosmos DB funge da nome host MongoDB
    * *Porta* : quando gli strumenti nativi MongoDB si connettono a Azure Cosmos DB, è necessario specificare questa porta in modo esplicito
    * *Username* : il prefisso del nome di dominio dell'endpoint Azure Cosmos DB funziona come nome utente MongoDB
    * *Password* : la chiave master Azure Cosmos DB funge da password MongoDB
    * Si noti inoltre il campo *SSL* , ovvero lo `true` strumento MongoDB Native **deve** abilitare SSL durante la scrittura di dati in Azure Cosmos DB

## <a name="perform-the-migration"></a>Eseguire la migrazione

1. Scegliere i database e le raccolte di cui si vuole eseguire la migrazione. In questo esempio viene eseguita la migrazione della raccolta di *query* nel database *EDX* da MongoDB a Azure Cosmos DB.

Nella parte restante di questa sezione viene illustrato come usare la coppia di strumenti selezionati nella sezione precedente.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Per esportare i dati dall'istanza di MongoDB di origine, aprire un terminale nel computer dell'istanza di MongoDB. Se è un computer Linux, digitare

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    In Windows, il file eseguibile sarà `mongoexport.exe` . *Host*, *porta*, *nome utente* e *password* devono essere compilati in base alle proprietà dell'istanza del database MongoDB esistente. 
    
    È anche possibile scegliere di esportare solo un subset del set di dati MongoDB. Un modo per eseguire questa operazione consiste nell'aggiungere un argomento di filtro aggiuntivo:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Verranno esportati solo i documenti corrispondenti al filtro `{"field1":"value1"}` .

    Una volta eseguita la chiamata, si noterà che `edx.json` viene prodotto un file:

    ![Screenshot della chiamata mongoexport.](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. È possibile usare lo stesso terminale per importare `edx.json` in Azure Cosmos DB. Se si esegue `mongoimport` in un computer Linux, digitare

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    In Windows, il file eseguibile sarà `mongoimport.exe` . *Host*, *porta*, *nome utente* e *password* devono essere compilati in base alle credenziali Azure Cosmos DB raccolte in precedenza. 
1. **Monitorare** l'output del terminale da *mongoimport*. Si noterà che stampa righe di testo nel terminale contenente gli aggiornamenti sullo stato di migrazione:

    ![Screenshot della chiamata mongoimport.](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Infine, esaminare Azure Cosmos DB per **verificare** che la migrazione abbia avuto esito positivo. Aprire il portale di Azure Cosmos DB e passare a Esplora dati. Dovrebbe essere visualizzato (1) che è stato creato un database *EDX* con una raccolta *importedQuery* e (2) se è stato esportato solo un subset di dati, *importedQuery* deve contenere *solo* documenti corrispondenti al subset di dati desiderato. Nell'esempio seguente un solo documento corrisponde al filtro `{"field1":"value1"}` :

    ![Screenshot della verifica Cosmos DB dati.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. Per creare un dump dei dati BSON dell'istanza di MongoDB, aprire un terminale nel computer dell'istanza di MongoDB. Se è un computer Linux, digitare

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    *Host*, *porta*, *nome utente* e *password* devono essere compilati in base alle proprietà dell'istanza del database MongoDB esistente. Si noterà che `edx-dump` viene prodotta una directory e che la struttura di directory di `edx-dump` riproduce la gerarchia di risorse (struttura del database e della raccolta) dell'istanza di MongoDB di origine. Ogni raccolta è rappresentata da un file BSON:

    ![Screenshot della chiamata mongodump.](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. È possibile usare lo stesso terminale per ripristinare il contenuto di `edx-dump` in Azure Cosmos DB. Se si esegue `mongorestore` in un computer Linux, digitare

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    In Windows, il file eseguibile sarà `mongorestore.exe` . *Host*, *porta*, *nome utente* e *password* devono essere compilati in base alle credenziali Azure Cosmos DB raccolte in precedenza. 
1. **Monitorare** l'output del terminale da *mongorestore*. Si noterà che stampa le linee nell'aggiornamento del terminale nello stato di migrazione:

    ![Screenshot della chiamata mongorestore.](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Infine, esaminare Azure Cosmos DB per **verificare** che la migrazione abbia avuto esito positivo. Aprire il portale di Azure Cosmos DB e passare a Esplora dati. Dovrebbe essere visualizzato (1) che è stato creato un database *EDX* con una raccolta *importedQuery* e (2) *importedQuery* deve contenere l' *intero* set di dati della raccolta di origine:

    ![Screenshot della verifica Cosmos DB dati mongorestore.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Ottimizzazione della post-migrazione

Dopo aver eseguito la migrazione dei dati archiviati nel database MongoDB nell'API per MongoDB di Azure Cosmos DB, è possibile connettersi ad Azure Cosmos DB e gestire i dati. È anche possibile eseguire altre procedure di ottimizzazione della post-migrazione, ad esempio: ottimizzare i criteri di indicizzazione, aggiornare il livello di coerenza predefinito o configurare la distribuzione globale per l'account Azure Cosmos DB. Per altre informazioni, vedere l'articolo [Ottimizzazione della post-migrazione](../cosmos-db/mongodb-post-migration.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Informazioni sul servizio Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)
* [Documentazione degli strumenti di database MongoDB](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Passaggi successivi

* Esaminare le linee guida sulla migrazione nella [Guida alla migrazione di database](https://datamigration.microsoft.com/) di Microsoft per altri scenari.