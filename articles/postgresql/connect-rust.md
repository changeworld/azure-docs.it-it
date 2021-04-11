---
title: 'Guida introduttiva: connettersi con Rust-database di Azure per PostgreSQL-server singolo'
description: Questa Guida introduttiva fornisce esempi di codice ruggine che è possibile usare per connettersi ai dati ed eseguire query da database di Azure per PostgreSQL-server singolo.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505853"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Guida introduttiva: usare Rust per connettersi ed eseguire query sui dati nel database di Azure per PostgreSQL-server singolo

In questo articolo si apprenderà come usare il [driver PostgreSQL per la ruggine](https://github.com/sfackler/rust-postgres) per interagire con database di Azure per PostgreSQL esplorando le operazioni CRUD (creazione, lettura, aggiornamento, eliminazione) implementate nel codice di esempio. Infine, è possibile eseguire l'applicazione localmente per verificarne l'azione.

## <a name="prerequisites"></a>Prerequisiti
Per questa guida di avvio rapido, è necessario:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free).
- È installata una versione recente di [Rust](https://www.rust-lang.org/tools/install) .
- Un server singolo di database di Azure per PostgreSQL-crearne uno usando [portale di Azure](./quickstart-create-server-database-portal.md) <br/> o [interfaccia](./quickstart-create-server-database-azure-cli.md)della riga di comando di Azure.
- A seconda che si usi l'accesso pubblico o privato, completare **UNA** delle azioni seguenti per abilitare la connettività.

  |Action| Metodo di connettività|Guida pratica|
  |:--------- |:--------- |:--------- |
  | **Configurare le regole del firewall** | Pubblico | [Portale](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Configurare l'endpoint di servizio** | Pubblico | [Portale](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Configurare il collegamento privato** | Privato | [Portale](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads) installato.

## <a name="get-database-connection-information"></a>Recuperare le informazioni di connessione al database SQL
Per la connessione a Database di Azure per PostgreSQL, sono necessari il nome completo del server e le credenziali di accesso. È possibile ottenere queste informazioni nel portale di Azure.

1. Nel [portale di Azure](https://portal.azure.com/), cercare il nome del server di Database di Azure per PostgreSQL e selezionarlo.
1. Nella pagina **Panoramica** del server, copiare il valore dei campi **Nome server** (completo) e **Nome utente amministratore**. Il valore completo di **Nome server** è sempre nel formato *\<my-server-name>.postgres.database.azure.com* e il valore di **Nome utente amministratore** è sempre nel formato *\<my-admin-username>@\<my-server-name>* .

## <a name="review-the-code-optional"></a>Esaminare il codice (facoltativo)

Se si è interessati a comprendere il funzionamento del codice, è possibile esaminare i frammenti seguenti. In caso contrario, passare direttamente a [Eseguire l'applicazione](#run-the-application).

### <a name="connect"></a>Connessione

La `main` funzione inizia con la connessione a database di Azure per PostgreSQL e dipende dalle variabili di ambiente seguenti per le informazioni di connettività `POSTGRES_HOST` , `POSTGRES_USER` `POSTGRES_PASSWORD` e `POSTGRES_DBNAME` . Per impostazione predefinita, il servizio database PostgreSQL è configurato per richiedere la `TLS` connessione. È possibile scegliere di disabilitare la richiesta `TLS` se l'applicazione client non supporta la `TLS` connettività. Per informazioni dettagliate, vedere [configurare la connettività TLS nel database di Azure per PostgreSQL-server singolo](./concepts-ssl-connection-security.md).

L'applicazione di esempio in questo articolo usa TLS con il [Crate Postgres-OpenSSL](https://crates.io/crates/postgres-openssl/). la funzione [Postgres:: client:: Connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) viene usata per avviare la connessione e il programma viene chiuso nel caso in cui l'operazione abbia esito negativo.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Elimina e crea tabella

Nell'applicazione di esempio viene utilizzata una semplice `inventory` tabella per illustrare le operazioni CRUD (creazione, lettura, aggiornamento, eliminazione).

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

La `drop_create_table` funzione tenta inizialmente di `DROP` `inventory` creare una nuova tabella prima di crearne una nuova. Questo rende più semplice l'apprendimento e la sperimentazione, perché si inizia sempre con uno stato noto (pulito). Il metodo [Execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) viene utilizzato per le operazioni di creazione ed eliminazione. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Inserire i dati

`insert_data` aggiunge voci alla `inventory` tabella. Viene creata un' [istruzione preparata](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) con la funzione [Prepare](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) . 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Si noti inoltre l'utilizzo del metodo [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) , che consente di specificare in modo esplicito i tipi di parametri di query.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Infine, `for` viene utilizzato un ciclo per aggiungere `item-3` `item-4` e, `item-5` con una quantità generata in modo casuale per ogni.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Eseguire query sui dati

`query_data` la funzione illustra come recuperare i dati dalla `inventory` tabella. Il metodo [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) viene usato per ottenere un elemento in base a `id` . 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

Tutte le righe della tabella di inventario vengono recuperate usando una `select * from` query con il metodo di [query](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) . Viene eseguita l'iterazione delle righe restituite per estrarre il valore per ogni colonna utilizzando [Get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get). 

> [!TIP]
> Si noti `get` in che modo è possibile specificare la colonna in base al relativo indice numerico nella riga o in base al nome della colonna.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Aggiornare i dati

La `update_date` funzione Aggiorna in modo casuale la quantità per tutti gli elementi. Poiché la `insert_data` funzione ha aggiunto `5` righe, lo stesso viene preso in considerazione nel `for` ciclo- `for n in 1..=5`

> [!TIP]
> Si noti che `query` viene usato anziché `execute` , perché si prevede di ottenere l'oggetto `id` e il nuovo oggetto generato `quantity` (usando la [clausola RETURNING](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Eliminare i dati

Infine, la `delete` funzione illustra come rimuovere un elemento dalla `inventory` tabella in base al relativo `id` . `id`Viene scelto in modo casuale. si tratta di un numero intero casuale compreso tra `1` a `5` ( `5` inclusi) perché la `insert_data` funzione ha aggiunto `5` righe da cui iniziare. 

> [!TIP]
> Si noti che `query` viene usato anziché `execute` perché si prevede di ottenere le informazioni sull'elemento appena eliminato (usando la [clausola RETURNING](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

1. Per iniziare, eseguire il comando seguente per clonare il repository di esempio:

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Impostare le variabili di ambiente necessarie con i valori copiati dal portale di Azure:

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Per eseguire l'applicazione, passare alla directory in cui è stata clonata ed eseguire `cargo run` :

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    L'output dovrebbe essere simile al seguente:

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. Per confermare, è anche possibile connettersi a database di Azure per PostgreSQL [usando PSQL](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) ed eseguire query sul database, ad esempio:

    ```sql
    select * from inventory;
    ```

[Problemi? Segnalarli](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire tutte le risorse usate in questo argomento di avvio rapido, eliminare il gruppo di risorse con il comando seguente:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Gestire il database di Azure per il server PostgreSQL usando il portale](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gestire il server di database di Azure per PostgreSQL tramite l'interfaccia della riga](./how-to-manage-server-cli.md)<br/>

[Ci sono problemi a trovare le informazioni giuste? Segnalarli](https://aka.ms/postgres-doc-feedback).
