---
title: Usare la cache di Azure per Redis con Go
description: Questa guida di avvio rapido descrive come creare un'app Go che usa la cache di Azure per Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 04b582b5ef31e61039c5513ea2a4aa60f1c638e7
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121338"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Avvio rapido: Usare la cache di Azure per Redis con Go

Questo articolo descrive come creare un'API REST in Go in cui archiviare e recuperare informazioni utente con il supporto di una struttura dei dati [HASH](https://redis.io/topics/data-types-intro#redis-hashes) nella [cache di Azure per Redis](./cache-overview.md). 

## <a name="skip-to-the-code-on-github"></a>Passa al codice su GitHub

Per passare direttamente al codice, vedere la [Guida introduttiva](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/) su GitHub.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install) (preferibilmente versione 1.13 o successiva)
- [Git](https://git-scm.com/downloads)
- Un client HTTP, ad esempio [cURL](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Creare un'istanza di Azure Cache per Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Esaminare il codice (facoltativo)

Se si è interessati a comprendere il funzionamento del codice, è possibile esaminare i frammenti seguenti. In caso contrario, passare direttamente a [Eseguire l'applicazione](#run-the-application).

La libreria open source [go-redis](https://github.com/go-redis/redis) viene usata per interagire con la cache di Azure per Redis.

La funzione `main` inizia con la lettura del nome host e della password (chiave di accesso) per l'istanza della cache di Azure per Redis.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Viene quindi stabilita una connessione con la cache di Azure per Redis. Si noti che viene usato [tls.Config](https://golang.org/pkg/crypto/tls/#Config): la cache di Azure per Redis accetta solo connessioni sicure con [TLS 1.2 come versione minima obbligatoria](cache-remove-tls-10-11.md).

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

Se la connessione riesce, vengono configurati i [gestori HTTP](https://golang.org/pkg/net/http/#HandleFunc) per gestire le operazioni `POST` e `GET` e viene avviato il server HTTP. 

> [!NOTE] 
> La [libreria gorilla mux](https://github.com/gorilla/mux) viene usata per il routing, anche se non è strettamente necessaria e per questa applicazione di esempio si potrebbe tranquillamente usare la libreria standard.
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

Lo struct `userHandler` incapsula un oggetto [redis.Client](https://pkg.go.dev/github.com/go-redis/redis/v8#Client), che viene usato dai metodi `createUser`, `getUser`. Il codice di questi metodi non è stato incluso per brevità. 

- `createUser`: accetta un payload JSON (contenente le informazioni utente) e lo salva come `HASH` nella cache di Azure per Redis.
- `getUser`: recupera le informazioni utente da `HASH` o restituisce una risposta HTTP `404` se non vengono trovate.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
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
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

L'applicazione accetta le informazioni su connettività e credenziali sotto forma di variabili di ambiente. 

1. Recuperare i valori di **Nome host** e **Chiavi di accesso** (disponibili tramite Chiavi di accesso) per l'istanza della cache di Azure per Redis nel [portale di Azure](https://portal.azure.com/)

1. Impostarli sulle rispettive variabili di ambiente:

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Nella finestra del terminale passare alla cartella corretta. Ad esempio:

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. Nel terminale eseguire il comando seguente per avviare l'applicazione.

    ```shell
    go run main.go
    ```

Il server HTTP verrà avviato sulla porta `8080`.

## <a name="test-the-application"></a>Testare l'applicazione

1. Creare alcune voci utente. L'esempio seguente usa cURL:

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Recuperare un utente esistente con il relativo `id`:

    ```bash
    curl -i localhost:8080/users/1
    ```

    Si riceverà una risposta JSON come questa:
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Se si prova a recuperare un utente che non esiste, si riceverà un errore HTTP `404`. Ad esempio:

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
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

In questo argomento di avvio rapido si è appreso come usare Go con la cache di Azure per Redis. È stata configurata ed eseguita una semplice applicazione basata su API REST per creare e ottenere informazioni utente con il supporto di una struttura dei dati `HASH` Redis.

> [!div class="nextstepaction"]
> [Creare una semplice app Web ASP.NET che usa Cache Redis di Azure.](./cache-web-app-howto.md)
