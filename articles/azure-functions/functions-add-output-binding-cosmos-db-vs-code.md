---
title: Connettere funzioni di Azure a Azure Cosmos DB usando Visual Studio Code
description: Informazioni su come connettere funzioni di Azure a un account Azure Cosmos DB aggiungendo un'associazione di output al progetto Visual Studio Code.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 91d27ce0d6f999ac5d13b079c877e49cdf3fcd61
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962871"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Connettere funzioni di Azure a Azure Cosmos DB usando Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Questo articolo illustra come usare Visual Studio Code per connettersi [Azure Cosmos DB](../cosmos-db/introduction.md) alla funzione creata nell'articolo della Guida introduttiva precedente. L'associazione di output aggiunta a questa funzione scrive i dati dalla richiesta HTTP a un documento JSON archiviato in un contenitore Azure Cosmos DB. 

::: zone pivot="programming-language-csharp"
Prima di iniziare, è necessario completare l'articolo [Avvio rapido: Creare un progetto di Funzioni di Azure dalla riga di comando](create-first-function-cli-csharp.md). Se è già stata eseguita la pulizia delle risorse alla fine di tale articolo, eseguire di nuovo i passaggi per ricreare l'app per le funzioni e le risorse correlate in Azure.
::: zone-end
::: zone pivot="programming-language-javascript"  
Prima di iniziare, è necessario completare l'articolo [Avvio rapido: Creare un progetto di Funzioni di Azure dalla riga di comando](create-first-function-cli-node.md). Se è già stata eseguita la pulizia delle risorse alla fine di tale articolo, eseguire di nuovo i passaggi per ricreare l'app per le funzioni e le risorse correlate in Azure.  
::: zone-end   

## <a name="configure-your-environment"></a>Configurare l'ambiente

Prima di iniziare, assicurarsi di installare l'estensione dei [database di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) per Visual Studio Code.

## <a name="create-your-azure-cosmos-db-account"></a>Creare l'account Azure Cosmos DB

> [!IMPORTANT]
> [Azure Cosmos DB senza server](../cosmos-db/serverless.md) è ora disponibile in anteprima. Questa modalità basata sul consumo rende Azure Cosmos DB un'opzione complessa per i carichi di lavoro senza server. Per usare Azure Cosmos DB in modalità senza server, scegliere senza **Server** come **modalità di capacità** durante la creazione dell'account.

1. In una nuova finestra del browser accedere al [portale di Azure](https://portal.azure.com/).

2. Fare clic su **Crea una risorsa** > **Database** > **Azure Cosmos DB**.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="Riquadro Database nel portale di Azure" border="true":::

3. Nella pagina **Crea account Azure Cosmos DB** immettere le impostazioni per il nuovo account di Azure Cosmos DB. 
 
    Impostazione|Valore|Descrizione
    ---|---|---
    Subscription|*Sottoscrizione in uso*|Scegliere la sottoscrizione di Azure in cui è stata creata la app per le funzioni nell' [articolo precedente](./create-first-function-vs-code-csharp.md).
    Gruppo di risorse|*Il gruppo di risorse*|Scegliere il gruppo di risorse in cui è stato creato il app per le funzioni nell' [articolo precedente](./create-first-function-vs-code-csharp.md).
    Nome account|*Immettere un nome univoco*|Immettere un nome univoco per identificare l'account Azure Cosmos DB.<br><br>Il nome dell'account può contenere solo lettere minuscole, numeri e trattini e deve avere una lunghezza compresa tra 3 e 31 caratteri.
    API|Core (SQL)|Selezionare **Core (SQL)** per creare un database di documenti su cui è possibile eseguire una query utilizzando una sintassi SQL. [Altre informazioni su Azure Cosmos DB API SQL](../cosmos-db/introduction.md).|
    Location|*Selezionare l'area geografica più vicina alla propria posizione*|Selezionare una posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare il percorso più vicino all'utente o agli utenti per ottenere l'accesso più rapido ai dati.
    Modalità di capacità|Velocità effettiva senza server o con provisioning|Selezionare **Serverless** per creare un account in modalità [Serverless](../cosmos-db/serverless.md). Selezionare **Provisioning velocità effettiva** per creare un account in modalità [Provisioning velocità effettiva](../cosmos-db/set-throughput.md).<br><br>Se si inizia a Azure Cosmos DB, scegliere senza **Server** .

4. Fare clic su **Rivedi e crea**. È possibile ignorare le sezioni **Rete** e **Tag**. 

5. Esaminare le informazioni di riepilogo e fare clic su **Crea**. 

6. Attendere la creazione del nuovo Azure Cosmos DB, quindi selezionare **Vai alla risorsa**.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Creazione dell'account di Azure Cosmos DB completata" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Creare un database e un contenitore Azure Cosmos DB

Dall'account Azure Cosmos DB selezionare **Esplora dati**, quindi **nuovo contenitore**. Creare un nuovo database denominato *My-database*, un nuovo contenitore denominato *My-container* e scegliere `/id` come [chiave di partizione](../cosmos-db/partitioning-overview.md).

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Creazione di un nuovo contenitore Azure Cosmos DB dalla portale di Azure" border="true":::

## <a name="update-your-function-app-settings"></a>Aggiornare le impostazioni dell'app per le funzioni

Nell' [articolo precedente della Guida introduttiva](./create-first-function-vs-code-csharp.md)è stata creata un'app per le funzioni in Azure. In questo articolo si aggiorna la app per le funzioni per scrivere documenti JSON nel contenitore Azure Cosmos DB creato in precedenza. Per connettersi all'account di Azure Cosmos DB, è necessario aggiungere la relativa stringa di connessione alle impostazioni dell'app. Si scarica quindi la nuova impostazione nel local.settings.jsfile in modo da potersi connettere all'account di Azure Cosmos DB durante l'esecuzione in locale.

1. In Visual Studio Code individuare l'account Azure Cosmos DB appena creato. Fare clic con il pulsante destro del mouse sul nome e scegliere **copia stringa di connessione**.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Copia della stringa di connessione Azure Cosmos DB" border="true":::

1. Premere <kbd>F1</kbd> per aprire il riquadro comandi, quindi cercare ed eseguire il comando `Azure Functions: Add New Setting...` .

1. Scegliere l'app per le funzioni creata nell'articolo precedente. Quando richiesto, immettere le informazioni seguenti:

    + **Immettere il nuovo nome dell'impostazione dell'app**: digitare `CosmosDbConnectionString` .

    + **Immettere il valore per "CosmosDbConnectionString"**: incollare la stringa di connessione dell'account di Azure Cosmos DB, come copiato in precedenza.

1. Premere di nuovo <kbd>F1</kbd> per aprire il riquadro comandi, quindi cercare ed eseguire il comando `Azure Functions: Download Remote Settings...` . 

1. Scegliere l'app per le funzioni creata nell'articolo precedente. Selezionare **Sì per tutti** per sovrascrivere le impostazioni locali esistenti. 

## <a name="register-binding-extensions"></a>Registrare le estensioni delle associazioni

Poiché si sta usando un'associazione di output Azure Cosmos DB, prima di eseguire il progetto è necessario che sia installata l'estensione di binding corrispondente. 

::: zone pivot="programming-language-csharp"

Ad eccezione dei trigger HTTP e timer, i binding vengono implementati come pacchetti di estensione. Eseguire il comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) seguente nella finestra del terminale per aggiungere il pacchetto di estensione di archiviazione nel progetto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

Il progetto è stato configurato per l'uso di [bundle di estensione](functions-bindings-register.md#extension-bundles), che installano automaticamente un set predefinito di pacchetti di estensione. 

I bundle di estensione vengono abilitati nella radice del progetto in un file host.json come il seguente:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

A questo punto, è possibile aggiungere il Azure Cosmos DB binding di output al progetto.

## <a name="add-an-output-binding"></a>Aggiungere un binding di output

In Funzioni ogni tipo di binding richiede di definire `direction`, `type` e un valore univoco `name` nel file function.json. Il modo in cui si definiscono questi attributi dipende dal linguaggio dell'app per le funzioni.

::: zone pivot="programming-language-csharp"

In un progetto di libreria di classi C# i binding vengono definiti come attributi di binding nel metodo della funzione. Il *function.jssu* file richiesto dalle funzioni viene quindi generato automaticamente in base a questi attributi.

Aprire il file di progetto *HttpExample. cs* e aggiungere il parametro seguente alla `Run` definizione del metodo:

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

Il `documentsOut` parametro è un <T> tipo IAsyncCollector, che rappresenta una raccolta di documenti JSON che verranno scritti nel contenitore Azure Cosmos DB al termine della funzione. Attributi specifici specificano il nome del contenitore e il nome del relativo database padre. La stringa di connessione per l'account Azure Cosmos DB viene impostata da `ConnectionStringSettingAttribute` .

La definizione del metodo Run dovrà ora essere come indicato di seguito:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Gli attributi di binding vengono definiti direttamente nel file function.json. A seconda del tipo di binding, potrebbero essere necessarie altre proprietà. La [configurazione di output Azure Cosmos DB](./functions-bindings-cosmosdb-v2-output.md#configuration) descrive i campi necessari per un'associazione di output di Azure Cosmos DB. L'estensione semplifica l'aggiunta di binding nel file function.json. 

Per creare un'associazione, fare clic con il pulsante destro del mouse (Ctrl + clic su macOS) `function.json` nel file nella cartella HttpTrigger e scegliere **Aggiungi binding.** Seguire le istruzioni per definire le seguenti proprietà di binding per la nuova associazione:

| Prompt | Valore | Descrizione |
| -------- | ----- | ----------- |
| **Selezionare la direzione di binding** | `out` | Il binding è un binding di output. |
| **Selezionare il binding con la direzione in uscita** | `Azure Cosmos DB` | L'associazione è un'associazione Azure Cosmos DB. |
| **Nome usato per identificare questa associazione nel codice** | `outputDocument` | Nome che identifica il parametro di binding a cui viene fatto riferimento nel codice. |
| **Il database Cosmos DB in cui verranno scritti i dati** | `my-database` | Nome del database Azure Cosmos DB contenente il contenitore di destinazione. |
| **Raccolta di database in cui verranno scritti i dati** | `my-container` | Nome del contenitore Azure Cosmos DB in cui verranno scritti i documenti JSON. |
| **Se true, crea il database e la raccolta di Cosmos DB** | `false` | Il database e il contenitore di destinazione esistono già. |
| **Selezionare l'impostazione da "local.setting.json"** | `CosmosDbConnectionString` | Nome di un'impostazione dell'applicazione che contiene la stringa di connessione per l'account Azure Cosmos DB. |
| **Chiave di partizione (facoltativa)** | *lasciare vuoto* | Obbligatorio solo quando l'associazione di output crea il contenitore. |
| **Velocità effettiva raccolta (facoltativa)** | *lasciare vuoto* | Obbligatorio solo quando l'associazione di output crea il contenitore. |

Viene aggiunta un'associazione alla `bindings` matrice nella function.jssu, che dovrebbe essere simile alla seguente:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Aggiungere il codice che usa l'associazione di output

::: zone pivot="programming-language-csharp"  

Aggiungere il codice che usa l' `documentsOut` oggetto di associazione di output per creare un documento JSON. Aggiungere questo codice prima dell'output restituito dal metodo.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

A questo punto, la funzione sarà come indicato di seguito:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

Aggiungere il codice che usa l' `outputDocument` oggetto di associazione `context.bindings` di output in per creare un documento JSON. Aggiungere questo codice prima dell'istruzione `context.res`.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

A questo punto, la funzione sarà come indicato di seguito:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

1. Come nell'articolo precedente, premere <kbd>F5</kbd> per avviare il progetto di app per le funzioni e gli strumenti di base. 

1. Con gli strumenti di base in esecuzione, passare all'area **funzioni di Azure:** . In **funzioni** espandere funzioni **del progetto locale**  >  . Fare clic con il pulsante destro del mouse sulla funzione (Ctrl + clic su Mac) `HttpExample` e scegliere **Esegui ora funzione...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Esegui ora la funzione da Visual Studio Code":::

1. In **immettere il corpo della richiesta** viene visualizzato il valore del corpo del messaggio di richiesta `{ "name": "Azure" }` . Premere INVIO per inviare il messaggio di richiesta alla funzione.  
 
1. Dopo la restituzione di una risposta, premere <kbd>CTRL + C</kbd> per arrestare gli strumenti di base.

### <a name="verify-that-a-json-document-has-been-created"></a>Verificare che sia stato creato un documento JSON

1. Nella portale di Azure tornare all'account Azure Cosmos DB e selezionare **Esplora dati**.

1. Espandere il database e il contenitore e selezionare **gli elementi** per elencare i documenti creati nel contenitore.

1. Verificare che sia stato creato un nuovo documento JSON dall'associazione di output.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Verifica per verificare se un nuovo documento è stato creato nel contenitore Azure Cosmos DB" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>Ridistribuire e verificare l'app aggiornata

1. In Visual Studio Code premere F1 per aprire il riquadro comandi. Nel riquadro comandi cercare e selezionare `Azure Functions: Deploy to function app...`.

1. Scegliere l'app per le funzioni creata nel primo articolo. Poiché il progetto viene ridistribuito nella stessa app, selezionare **Distribuisci** per ignorare l'avviso sulla sovrascrittura di file.

1. Al termine della distribuzione, è possibile usare di nuovo la funzionalità **Esegui funzione Now...** per attivare la funzione in Azure.

1. Controllare di nuovo [i documenti creati nel contenitore Azure Cosmos DB](#verify-that-a-json-document-has-been-created) per verificare che l'associazione di output generi nuovamente un nuovo documento JSON.

## <a name="clean-up-resources"></a>Pulire le risorse

In Azure il termine *risorse* si riferisce ad app per le funzioni, funzioni, account di archiviazione e così via. Le risorse sono raggruppate in *gruppi di risorse*, ed è possibile eliminare tutti gli elementi in un gruppo eliminando il gruppo.

Per completare queste guide introduttive sono state create risorse. Per tali risorse potrebbero venire addebitati costi, a seconda dello [stato dell'account](https://azure.microsoft.com/account/) e dei [prezzi dei servizi](https://azure.microsoft.com/pricing/). Se le risorse non sono più necessarie, ecco come eliminarle:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata aggiornata la funzione attivata tramite HTTP per scrivere documenti JSON in un contenitore Azure Cosmos DB. A questo punto, è possibile ottenere maggiori informazioni sullo sviluppo di funzioni con Visual Studio Code:

+ [Sviluppare Funzioni di Azure con Visual Studio Code](functions-develop-vs-code.md)

+ [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
::: zone pivot="programming-language-csharp"  
+ [Esempi di progetti di Funzioni completi in C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Esempi di progetti di Funzioni completi in JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Guida per gli sviluppatori JavaScript di Funzioni di Azure](functions-reference-node.md)  
::: zone-end  