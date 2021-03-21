---
title: Creare una funzione in go o Rust usando Visual Studio Code-funzioni di Azure
description: Informazioni su come creare una funzione Go come gestore personalizzato di funzioni di Azure e quindi pubblicare il progetto locale nell'hosting senza server in funzioni di Azure usando l'estensione funzioni di Azure in Visual Studio Code.
ms.topic: quickstart
ms.date: 12/4/2020
ms.openlocfilehash: 8b53031315cce3651a2de581d71da6ef6fe909c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470371"
---
# <a name="quickstart-create-a-go-or-rust-function-in-azure-using-visual-studio-code"></a>Guida introduttiva: creare una funzione Go o Rust in Azure usando Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

In questo articolo si usa Visual Studio Code per creare una funzione di [gestione personalizzata](functions-custom-handlers.md) che risponde alle richieste HTTP. Dopo aver testato il codice in locale, verrà distribuito nell'ambiente serverless di Funzioni di Azure.

I gestori personalizzati possono essere usati per creare funzioni in qualsiasi linguaggio o runtime eseguendo un processo server HTTP. Questo articolo supporta sia il [go](create-first-function-vs-code-other.md?tabs=go) che la [ruggine](create-first-function-vs-code-other.md?tabs=rust).

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

## <a name="configure-your-environment"></a>Configurare l'ambiente

Prima di iniziare, verificare che siano soddisfatti i requisiti seguenti:

# <a name="go"></a>[Go](#tab/go)

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code.

+ [Azure Functions Core Tools](./functions-run-local.md#v2) versione 3.x. Usare il `func --version` comando per verificare che sia installato correttamente.

+ [Go](https://golang.org/doc/install), versione più recente consigliata. Usare il comando `go version` per controllare la versione in uso.

# <a name="rust"></a>[Rust](#tab/rust)

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code.

+ [Azure Functions Core Tools](./functions-run-local.md#v2) versione 3.x. Usare il `func --version` comando per verificare che sia installato correttamente.

+ [Rustup](https://www.rust-lang.org/tools/install). Usare il comando `rustc --version` per controllare la versione in uso.

---

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Creare il progetto locale

In questa sezione si usa Visual Studio Code per creare un progetto di gestori personalizzati di funzioni di Azure locali. Più avanti in questo articolo verrà pubblicato il codice della funzione in Azure.

1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** selezionare l'icona **Crea nuovo progetto...** .

    ![Scegliere Crea nuovo progetto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Scegliere una posizione della directory per l'area di lavoro del progetto e quindi scegliere **Seleziona**.

    > [!NOTE]
    > Questa procedura è stata progettata per il completamento all'esterno di un'area di lavoro. In questo caso, non selezionare una cartella di progetto inclusa in un'area di lavoro.

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare un linguaggio per il progetto di funzione**: Scegliere `Custom`.

    + **Selezionare un modello per la prima funzione del progetto**: Scegliere `HTTP trigger`.

    + **Specificare un nome di funzione**: Digitare `HttpExample`.

    + **Livello di autorizzazione**: Scegliere `Anonymous`, che consente a chiunque di chiamare l'endpoint della funzione. Per informazioni sul livello di autorizzazione, vedere [Chiavi di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selezionare come si vuole aprire il progetto**: Scegliere `Add to workspace`.

1. Usando queste informazioni, Visual Studio Code genera un progetto di funzioni di Azure con una funzione trigger HTTP. È possibile visualizzare i file di progetto locali in Explorer. Per altre informazioni sui file che vengono creati, vedere [File di progetto generati](functions-develop-vs-code.md#generated-project-files). 

## <a name="create-and-build-your-function"></a>Creare e compilare la funzione

Il *function.js* nel file nella cartella *HttpExample* dichiara una funzione trigger http. Per completare la funzione, aggiungere un gestore e compilarlo in un file eseguibile.

# <a name="go"></a>[Go](#tab/go)

1. Premere <kbd>CTRL + n</kbd> (<kbd>cmd + n</kbd> su MacOS) per creare un nuovo file. Salvarlo come *gestore. passare* alla radice dell'app per le funzioni (nella stessa cartella *host.js*).

1. In *handler. go* aggiungere il codice seguente e salvare il file. Questo è il gestore personalizzato go.

    ```go
    package main
    
    import (
        "fmt"
        "log"
        "net/http"
        "os"
    )
    
    func helloHandler(w http.ResponseWriter, r *http.Request) {
        message := "This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.\n"
        name := r.URL.Query().Get("name")
        if name != "" {
            message = fmt.Sprintf("Hello, %s. This HTTP triggered function executed successfully.\n", name)
        }
        fmt.Fprint(w, message)
    }
    
    func main() {
        listenAddr := ":8080"
        if val, ok := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT"); ok {
            listenAddr = ":" + val
        }
        http.HandleFunc("/api/HttpExample", helloHandler)
        log.Printf("About to listen on %s. Go to https://127.0.0.1%s/", listenAddr, listenAddr)
        log.Fatal(http.ListenAndServe(listenAddr, nil))
    }
    ```

1. Premere <kbd>CTRL + MAIUSC +</kbd> o selezionare *nuovo terminale* dal menu *terminale* per aprire un nuovo terminale integrato in vs code.

1. Compilare il gestore personalizzato usando il comando seguente. Un file eseguibile denominato `handler` ( `handler.exe` in Windows) viene restituito nella cartella radice dell'app per le funzioni.

    ```bash
    go build handler.go
    ```

    ![Gestore personalizzato VS Code Build go](./media/functions-create-first-function-vs-code/functions-vscode-go.png)

# <a name="rust"></a>[Rust](#tab/rust)

1. Premere <kbd>CTRL + MAIUSC +</kbd> o selezionare *nuovo terminale* dal menu *terminale* per aprire un nuovo terminale integrato in vs code.

1. Nella radice dell'app per le funzioni (la stessa cartella *host.js*) inizializzare un progetto rust denominato `handler` .

    ```bash
    cargo init --name handler
    ```

1. In *Cargo. toml* aggiungere le dipendenze seguenti necessarie per completare questa Guida introduttiva. Nell'esempio viene usato il Framework del server Web [Warp](https://docs.rs/warp/) .

    ```toml
    [dependencies]
    warp = "0.3"
    tokio = { version = "1", features = ["rt", "macros", "rt-multi-thread"] }
    ```

1. In *src/main. RS* aggiungere il codice seguente e salvare il file. Si tratta del gestore personalizzato Rust.

    ```rust
    use std::collections::HashMap;
    use std::env;
    use std::net::Ipv4Addr;
    use warp::{http::Response, Filter};

    #[tokio::main]
    async fn main() {
        let example1 = warp::get()
            .and(warp::path("api"))
            .and(warp::path("HttpExample"))
            .and(warp::query::<HashMap<String, String>>())
            .map(|p: HashMap<String, String>| match p.get("name") {
                Some(name) => Response::builder().body(format!("Hello, {}. This HTTP triggered function executed successfully.", name)),
                None => Response::builder().body(String::from("This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.")),
            });

        let port_key = "FUNCTIONS_CUSTOMHANDLER_PORT";
        let port: u16 = match env::var(port_key) {
            Ok(val) => val.parse().expect("Custom Handler port is not a number!"),
            Err(_) => 3000,
        };

        warp::serve(example1).run((Ipv4Addr::UNSPECIFIED, port)).await
    }
    ```

1. Compilare un file binario per il gestore personalizzato. Un file eseguibile denominato `handler` ( `handler.exe` in Windows) viene restituito nella cartella radice dell'app per le funzioni.

    ```bash
    cargo build --release
    cp target/release/handler .
    ```

    ![VS Code-crea gestore personalizzato Rust](./media/functions-create-first-function-vs-code/functions-vscode-rust.png)

---

## <a name="configure-your-function-app"></a>Configurare l'app per le funzioni

L'host della funzione deve essere configurato per l'esecuzione del file binario del gestore personalizzato all'avvio.

1. Aprire *host.js*.

1. Nella `customHandler.description` sezione, impostare il valore di `defaultExecutablePath` `handler` su (in Windows, impostarlo su `handler.exe` ).

1. Nella `customHandler` sezione aggiungere una proprietà denominata `enableForwardingHttpRequest` e impostarne il valore su `true` . Per le funzioni costituite solo da un trigger HTTP, questa impostazione semplifica la programmazione consentendo di usare una tipica richiesta HTTP anziché il [payload della richiesta](functions-custom-handlers.md#request-payload)del gestore personalizzato.

1. Verificare che la sezione abbia un `customHandler` aspetto simile a questo esempio. Salvare il file.

    ```
    "customHandler": {
      "description": {
        "defaultExecutablePath": "handler",
        "workingDirectory": "",
        "arguments": []
      },
      "enableForwardingHttpRequest": true
    }
    ```

L'app per le funzioni è configurata per avviare il file eseguibile del gestore personalizzato.

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

È possibile eseguire questo progetto nel computer di sviluppo locale prima di pubblicare in Azure.

1. Nel terminale integrato avviare l'app per le funzioni usando Azure Functions Core Tools.

    ```bash
    func start
    ```

1. Con Core Tools in esecuzione, passare all'URL seguente per eseguire una richiesta GET, che include la stringa di query `?name=Functions`.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Viene restituita una risposta simile alla seguente in un browser:

    ![Browser - Output di esempio di localhost](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Le informazioni sulla richiesta sono visualizzate nel pannello **Terminale**.

    ![Avvio dell'host attività - Output del terminale di VS Code](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Premere <kbd>CTRL + C</kbd> per arrestare gli strumenti di base.

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile usare Visual Studio Code per pubblicare il progetto direttamente in Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="compile-the-custom-handler-for-azure"></a>Compilare il gestore personalizzato per Azure

In questa sezione si pubblica il progetto in Azure in un'app per le funzioni che esegue Linux. Nella maggior parte dei casi, è necessario ricompilare il file binario e modificare la configurazione in modo che corrisponda alla piattaforma di destinazione prima di pubblicarla in Azure.

# <a name="go"></a>[Go](#tab/go)

1. Nel terminale integrato compilare il gestore in Linux/x64. Viene creato un file binario denominato `handler` nella radice dell'app per le funzioni.

    # <a name="macos"></a>[macOS](#tab/macos)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="windows"></a>[Windows](#tab/windows)
    ```cmd
    set GOOS=linux
    set GOARCH=amd64
    go build handler.go
    ```

    Modificare il `defaultExecutablePath` *host.js* da `handler.exe` a `handler` . Indica all'app per le funzioni di eseguire il file binario di Linux.
    
    ---

# <a name="rust"></a>[Rust](#tab/rust)

1. Creare un file in *. Cargo/config*. Aggiungere il contenuto seguente e salvare il file.

    ```
    [target.x86_64-unknown-linux-musl]
    linker = "rust-lld"
    ```

1. Nel terminale integrato compilare il gestore in Linux/x64. Viene creato un file binario denominato `handler` . Copiarlo nella radice dell'app per le funzioni.

    ```bash
    rustup target add x86_64-unknown-linux-musl
    cargo build --release --target=x86_64-unknown-linux-musl
    cp target/x86_64-unknown-linux-musl/release/handler .
    ```

1. Se si usa Windows, modificare il `defaultExecutablePath` *host.js* da `handler.exe` a `handler` . Indica all'app per le funzioni di eseguire il file binario di Linux.

1. Aggiungere la riga seguente al file con *estensione funcignore* :

    ```
    target
    ```

    In questo modo si impedisce la pubblicazione del contenuto della cartella di *destinazione* .

---

## <a name="publish-the-project-to-azure"></a>Pubblicare il progetto in Azure

In questa sezione verrà creata un'app per le funzioni con le risorse correlate nella sottoscrizione di Azure e quindi verrà distribuito il codice. 

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure. 


1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** scegliere il pulsante **Deploy to function app...** (Distribuisci nell'app per le funzioni...).

    ![Pubblicare il progetto in Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare la cartella**: scegliere una cartella dall'area di lavoro o selezionarne una che contenga l'app per le funzioni. Questa opzione non verrà visualizzata se è già stata aperta un'app per le funzioni valida.

    + **Selezionare la sottoscrizione**: scegliere la sottoscrizione da usare. Questa opzione non è visibile se è disponibile una sola sottoscrizione.

    + **Selezionare l'app per le funzioni in Azure**: Scegliere `+ Create new Function App (advanced)`. 
    
        > [!IMPORTANT]
        > L' `advanced` opzione consente di scegliere il sistema operativo specifico in cui viene eseguita l'app per le funzioni in Azure, che in questo caso è Linux.

        ![VS Code-selezionare Crea nuova app per le funzioni avanzata](./media/functions-create-first-function-vs-code/functions-vscode-create-azure-advanced.png)

    + **Immettere un nome univoco a livello globale per l'app per le funzioni**: digitare un nome valido in un percorso URL. Il nome digitato viene convalidato per assicurarsi che sia univoco in Funzioni di Azure.

    + **Selezionare uno stack di runtime**: scegliere `Custom Handler` .

    + **Selezionare un sistema operativo**: scegliere `Linux` .

    + **Selezionare un piano di hosting**: scegliere `Consumption` .

    + **Selezionare un gruppo di risorse**: scegliere `+ Create new resource group` . Immettere un nome per il gruppo di risorse. Questo nome deve essere univoco all'interno della sottoscrizione di Azure. È possibile utilizzare il nome suggerito nella richiesta.

    + **Selezionare un account di archiviazione**: scegliere `+ Create new storage account` . Questo nome deve essere univoco a livello globale in Azure. È possibile utilizzare il nome suggerito nella richiesta.

    + **Selezionare una risorsa Application Insights**: scegliere `+ Create Application Insights resource` . Questo nome deve essere univoco a livello globale in Azure. È possibile utilizzare il nome suggerito nella richiesta.

    + **Selezionare un percorso per le nuove risorse**: per ottenere prestazioni migliori, scegliere un' [area](https://azure.microsoft.com/regions/) nelle vicinanze. L'estensione Mostra lo stato delle singole risorse man mano che vengono create in Azure nell'area di notifica.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notifica della creazione di risorse di Azure":::

1. Al termine, nella sottoscrizione vengono create le risorse di Azure seguenti:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. 

4. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create. Se non si riesce a visualizzare la notifica, selezionare l'icona della campana nell'angolo in basso a destra per visualizzarla di nuovo.

    ![Notifica di creazione completata](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sui gestori personalizzati di funzioni di Azure](functions-custom-handlers.md)
