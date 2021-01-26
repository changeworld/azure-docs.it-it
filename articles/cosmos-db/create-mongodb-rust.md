---
title: Connettere un'applicazione Rust all'API Azure Cosmos DB per MongoDB
description: Questa Guida introduttiva illustra come creare un'applicazione Rust supportata dall'API di Azure Cosmos DB per MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 4b7e7258664aed3b171166bb392406cd5d826b3f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792552"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Guida introduttiva: connettere un'applicazione Rust all'API Azure Cosmos DB per MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale. L'esempio presentato in questo articolo è una semplice applicazione basata su riga di comando che usa il [driver Rust per MongoDB](https://github.com/mongodb/mongo-rust-driver). Poiché l'API di Azure Cosmos DB per MongoDB è [compatibile con il protocollo wire di MongoDB](./mongodb-introduction.md#wire-protocol-compatibility), è possibile che qualsiasi driver client MongoDB si connetta a tale protocollo.

Si apprenderà come usare il driver di MongoDB Rust per interagire con l'API Azure Cosmos DB per MongoDB esplorando le operazioni CRUD (creazione, lettura, aggiornamento, eliminazione) implementate nel codice di esempio. Infine, è possibile eseguire l'applicazione localmente per verificarne l'azione.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free). In alternativa, è possibile [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure. È anche possibile usare l'[Emulatore di Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) con la stringa di connessione `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.
- [Rust](https://www.rust-lang.org/tools/install) (versione 1,39 o successiva)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Configurazione di Azure Cosmos DB

Per configurare un account di Azure Cosmos DB, seguire le [istruzioni](create-mongodb-dotnet.md)riportate qui. Per l'applicazione sarà necessaria la stringa di connessione MongoDB che è possibile recuperare utilizzando il portale di Azure. Per informazioni dettagliate, vedere [ottenere la stringa di connessione MongoDB da personalizzare](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Eseguire l'applicazione

### <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Eseguire i comandi seguenti per clonare il repository di esempio.

1. Aprire un prompt dei comandi, creare una nuova cartella denominata `git-samples` e quindi chiudere il prompt dei comandi.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una nuova cartella in cui installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

1. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Compilare l'applicazione

Per compilare il file binario:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Configurare l'applicazione 

Esportare la stringa di connessione, il database MongoDB e i nomi di raccolta come variabili di ambiente. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> L'opzione `ssl=true` è importante in considerazione dei requisiti di Cosmos DB. Per altre informazioni, vedere [Requisiti della stringa di connessione](connect-mongodb-account.md#connection-string-requirements).
>

Per la variabile di ambiente `MONGODB_URL`, sostituire i segnaposto per `<COSMOSDB_ACCOUNT_NAME>` e `<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: nome dell'account Azure Cosmos DB creato
- `<COSMOSDB_PASSWORD>`: chiave di database estratta nel passaggio precedente

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

È possibile scegliere i valori desiderati per `MONGODB_DATABASE` e `MONGODB_COLLECTION` o lasciarli invariati.

Per eseguire l'applicazione, passare alla cartella corretta, in cui è presente il file binario dell'applicazione:

```bash
cd target/release
```

Per creare un elemento `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

In caso di esito positivo, verrà visualizzato un output con l'elemento `_id` MongoDB del documento appena creato:

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Creare un altro elemento `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Elencare tutti gli elementi `todo`

```bash
./todo list all
```

Verranno visualizzati quelli appena aggiunti:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Per aggiornare lo stato di un `todo` (ad esempio, modificarlo in `completed` stato), usare l' `todo` ID come segue:

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Elencare solo gli elementi `todo` completati

```bash
./todo list completed
```

Viene visualizzato solo l'elemento appena aggiornato

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

Eliminare un elemento `todo` tramite il relativo ID

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Elencare gli elementi `todo` da confermare

```bash
./todo list all
```

Il `todo` appena eliminato non dovrebbe essere presente.

### <a name="view-data-in-data-explorer"></a>Visualizzare i dati in Esplora dati

I dati archiviati in Azure Cosmos DB sono disponibili per la visualizzazione e l'esecuzione di query nel portale di Azure.

Per visualizzare e usare i dati utente creati nel passaggio precedente, nonché eseguire query su di essi, accedere al [portale di Azure](https://portal.azure.com) nel Web browser.

Nella casella di ricerca nella parte superiore immettere **Azure Cosmos DB**. Quando viene aperto il pannello dell'account Cosmos, selezionare l'account Cosmos. Nel riquadro di spostamento a sinistra selezionare **Esplora dati**. Espandere la raccolta nel riquadro Raccolte. Sarà quindi possibile visualizzare i documenti nella raccolta, eseguire query sui dati e anche creare ed eseguire stored procedure, trigger e funzioni definite dall'utente.

## <a name="review-the-code-optional"></a>Esaminare il codice (facoltativo)

Se si è interessati a comprendere il funzionamento dell'applicazione, è possibile esaminare i frammenti di codice in questa sezione. I frammenti di codice seguenti sono tratti dal `src/main.rs` file.

La `main` funzione è il punto di ingresso per l' `todo` applicazione. Prevede che l'URL di connessione per l'API di Azure Cosmos DB per MongoDB venga fornito dalla `MONGODB_URL` variabile di ambiente. Viene creata una nuova istanza di `TodoManager` , seguita da un' [ `match` espressione](https://doc.rust-lang.org/book/ch06-02-match.html) che delega al metodo appropriato `TodoManager` in base all'operazione scelta dall'utente,,, `create` `update` `list` o `delete` .

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` è un oggetto `struct` che incapsula un oggetto [MongoDB:: Sync:: Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html). Quando si tenta di creare un'istanza di `TodoManager` usando la `new` funzione, viene avviata una connessione all'API Azure Cosmos DB per MongoDB.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

In particolare, `TodoManager` dispone di metodi per semplificare la gestione di `todo` . Ora le sezioni verranno esaminate una a una.

Il `add_todo` metodo accetta una `todo` Descrizione fornita dall'utente e crea un'istanza di `Todo` struct, che ha un aspetto simile al seguente. Il Framework [serde](https://github.com/serde-rs/serde) viene usato per eseguire il mapping dei dati di BSON (serializzazione/deserializzazione) in istanze di `Todo` struct. Si noti come `serde` vengono usati gli attributi di campo per personalizzare il processo di serializzazione/deserialzationzione. Ad esempio, `todo_id` il campo nel todo `struct` è un `ObjectId` e viene archiviato in MongoDB come `_id` .

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) accetta un [documento](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) che rappresenta i `todo` dettagli da aggiungere. Si noti che la conversione da `Todo` a a `Document` è un processo in due passaggi, eseguita utilizzando una combinazione di [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) e [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document).

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Collection. Find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) viene usato per recuperare *tutti* gli oggetti `todo` o filtrarli in base allo stato fornito dall'utente ( `pending` o `completed` ). Si noti come nel `while` ciclo, ognuno `Document` ottenuto come risultato della ricerca viene convertito in uno `Todo` struct usando [BSON:: from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html). Questo è il contrario di quanto è stato fatto nel `add_todo` metodo.

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

`todo`È possibile aggiornare lo stato (da `pending` a `completed` o viceversa) utilizzando. `todo`Viene convertito in [BSON:: OID:: ObjectID](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) , che viene quindi utilizzato dal metodo[Collection.Update_One](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) per individuare il documento che deve essere aggiornato.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

L'eliminazione di un oggetto `todo` è semplice usando il metodo [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) .


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come creare un account dell'API MongoDB Azure Cosmos DB usando il Azure Cloud Shell e creare ed eseguire un'app della riga di comando Rust per gestire `todo` i. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importare i dati di MongoDB in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
