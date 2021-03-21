---
title: Usare cache di Azure per Redis con Rust
description: Questa Guida introduttiva illustra come interagire con cache di Azure per Redis usando Rust.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 17f38d79b75179d7a54ca5ed1d20dff18d0a0363
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121100"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Guida introduttiva: usare cache di Azure per Redis con Rust

In questo articolo si apprenderà come usare il [linguaggio di programmazione Rust](https://www.rust-lang.org/) per interagire con [cache di Azure per Redis](./cache-overview.md). Vengono illustrati esempi di strutture di dati Redis comunemente utilizzate, ad esempio [stringa](https://redis.io/topics/data-types-intro#redis-strings), [hash](https://redis.io/topics/data-types-intro#redis-hashes), [elenco](https://redis.io/topics/data-types-intro#redis-lists) e così via. uso della libreria [Redis-RS](https://github.com/mitsuhiko/redis-rs) per Redis. Questo client espone sia le API di alto livello che quelle di basso livello. questi stili vengono visualizzati in azione con l'aiuto del codice di esempio presentato in questo articolo.

## <a name="skip-to-the-code-on-github"></a>Passa al codice su GitHub

Per passare direttamente al codice, vedere la [Guida introduttiva di Rust](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/) su GitHub.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install) (versione 1,39 o successiva)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Creare un'istanza di Azure Cache per Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Esaminare il codice (facoltativo)

Se si è interessati a comprendere il funzionamento del codice, è possibile esaminare i frammenti seguenti. In caso contrario, passare direttamente a [Eseguire l'applicazione](#run-the-application).

La `connect` funzione viene usata per stabilire una connessione alla cache di Azure per Redis. Si prevede che il nome host e la password (chiave di accesso) vengano passati rispettivamente tramite le variabili di ambiente `REDIS_HOSTNAME` e `REDIS_PASSWORD` . Il formato dell'URL di connessione è `rediss://<username>:<password>@<hostname>` -cache di Azure per Redis accetta solo connessioni sicure con [TLS 1,2 come versione minima richiesta](cache-remove-tls-10-11.md).

La chiamata a [redis:: client:: Open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) esegue la convalida di base mentre [get_Connection ()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) avvia effettivamente la connessione: il programma si interrompe se la connettività non riesce a causa di un motivo come una password non corretta.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

La `basics` funzione copre i comandi [set](https://redis.io/commands/set), [Get](https://redis.io/commands/get)e [incr](https://redis.io/commands/incr) . L'API di basso livello viene utilizzata per `SET` e `GET` , che imposta e recupera il valore per una chiave denominata `foo` . Il `INCRBY` comando viene eseguito usando un'API di alto livello, ad esempio [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) incrementa il valore di una chiave (denominata `counter` ) da `2` seguito da una chiamata a [Get](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) per recuperarla.

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

Il frammento di codice seguente illustra la funzionalità di una `HASH` struttura di dati Redis. [HSET](https://redis.io/commands/hset) viene richiamato usando l'API di basso livello per archiviare le informazioni ( `name` , `version` , `repo` ) sui driver Redis (client). Ad esempio, i dettagli per il driver della ruggine (uno usato in questo codice di esempio!) vengono acquisiti sotto forma di [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) e quindi passati all'API di basso livello. Viene quindi recuperato usando [HGETALL](https://redis.io/commands/hgetall).

`HSET` può essere eseguito anche usando un'API di alto livello usando [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) che accetta una matrice di Tuple. [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) viene quindi eseguito per recuperare il valore per un singolo attributo ( `repo` in questo caso).

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

Nella funzione seguente è possibile vedere come usare una `LIST` struttura di dati. [LPUSH](https://redis.io/commands/lpush) viene eseguito con l'API di basso livello per aggiungere una voce all'elenco e il metodo [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) di alto livello viene usato per recuperarlo dall'elenco. Viene quindi usato il metodo [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) per aggiungere un paio di voci all'elenco, che vengono quindi recuperate usando il metodo [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) di basso livello.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

Qui è possibile visualizzare alcune delle `SET` operazioni. Il metodo [Sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (API di alto livello) viene usato per aggiungere due voci a un oggetto `SET` denominato `users` . [SISMEMBER](https://redis.io/commands/hset) viene quindi eseguito (API di basso livello) per verificare se `user1` esiste. Infine, [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) viene usato per recuperare ed eseguire l'iterazione su tutte le voci impostate sotto forma di vettore[( <String> vec](https://doc.rust-lang.org/std/vec/struct.Vec.html)).

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

`sorted_set` la funzione seguente illustra la struttura dei dati del set ordinato. [ZADD](https://redis.io/commands/zadd) viene richiamato (con l'API di basso livello) per aggiungere un punteggio integer casuale per un lettore ( `player-1` ). Successivamente, il metodo [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) (API di alto livello) viene usato per aggiungere altri giocatori ( `player-2` a `player-5` ) e i rispettivi punteggi (generati casualmente). Il numero di voci nel set ordinato viene calcolato usando [ZCARD](https://redis.io/commands/zcard) e viene usato come limite per il comando [ZRANGE](https://redis.io/commands/zrange) (richiamato con l'API di basso livello) per elencare i giocatori con i punteggi in ordine crescente.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Per iniziare, clonare l'applicazione da GitHub.

1. Aprire un prompt dei comandi e creare una cartella denominata `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

1. Aprire una finestra del terminale Git, ad esempio Git Bash. Usare il comando `cd` per passare alla nuova cartella in cui clonare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

1. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

L'applicazione accetta le informazioni su connettività e credenziali sotto forma di variabili di ambiente. 

1. Recuperare il **nome host** e le **chiavi di accesso** (disponibili tramite chiavi di accesso) per la cache di Azure per l'istanza di redis nella [portale di Azure](https://portal.azure.com/).

1. Impostarli sulle rispettive variabili di ambiente:

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Nella finestra del terminale passare alla cartella corretta. Ad esempio:

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. Nel terminale eseguire il comando seguente per avviare l'applicazione.

    ```shell
    cargo run
    ```
    
    Viene visualizzato un output simile al seguente:
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Se si desidera eseguire una funzione specifica, impostare come commento altre funzioni nella `main` funzione:
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Se l'uso delle risorse e del gruppo di risorse di Azure creati nella presente Guida introduttiva è terminato, è possibile eliminarli per evitare addebiti.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse. Se l'istanza della cache di Azure per Redis è stata creata in un gruppo di risorse esistenti che si vuole conservare, è possibile eliminare solo la cache selezionando **Elimina** nella pagina **Panoramica** della cache. 

Per eliminare il gruppo di risorse e la relativa istanza della cache di Azure per Redis:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.
1. Nella casella di testo **Filtra per nome** immettere il nome del gruppo di risorse che contiene l'istanza della cache, quindi selezionarlo nei risultati della ricerca. 
1. Nella pagina del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Digitare il nome del gruppo di risorse e quindi selezionare **Elimina**.
   
   ![Eliminare il gruppo di risorse per l'istanza della cache di Azure per Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato illustrato come usare il driver Rust per Redis per connettersi ed eseguire operazioni in cache di Azure per Redis.

> [!div class="nextstepaction"]
> [Creare una semplice app Web ASP.NET che usa Cache Redis di Azure.](./cache-web-app-howto.md)
