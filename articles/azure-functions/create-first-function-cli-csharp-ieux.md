---
title: Creare una funzione C# dalla riga di comando - Funzioni di Azure
description: Informazioni su come creare una funzione C# dalla riga di comando e quindi pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a78abea5bcc5925cb2e137d918c7217ae92b118e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044324"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Avvio rapido: Creare una funzione C# in Azure dalla riga di comando

> [!div class="op_single_selector" title1="Selezionare il linguaggio della funzione: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

In questo articolo vengono usati gli strumenti da riga di comando per creare una funzione basata sulla libreria di classi C# che risponde alle richieste HTTP. Dopo aver testato il codice localmente, è necessario distribuirlo nel <abbr title="Ambiente di elaborazione del runtime in cui tutti i dettagli del server sono trasparenti per gli sviluppatori di applicazioni, semplificando il processo di distribuzione e gestione del codice.">senza server</abbr> ambiente di <abbr title="Servizio di Azure che offre un ambiente di elaborazione senza server a basso costo per le applicazioni.">Funzioni di Azure</abbr>.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

È inoltre disponibile una [versione basata su Visual Studio Code](create-first-function-vs-code-csharp.md) di questo articolo.

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

+ Ottenere un'Azure <abbr title="Profilo che mantiene le informazioni di fatturazione per l'utilizzo di Azure.">account</abbr> con un oggetto attivo <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo utente o a un reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Installare [.NET Core SDK 3,1](https://www.microsoft.com/net/download)

+ Installare [Azure Functions Core Tools](functions-run-local.md#v2) versione 3. x.

+ Il valore di <abbr title="Un set di strumenti da riga di comando multipiattaforma per l'uso delle risorse di Azure dal computer di sviluppo locale, in alternativa all'uso del portale di Azure.">Interfaccia della riga di comando di Azure</abbr> oppure <abbr title="Un modulo di PowerShell che fornisce comandi per l'utilizzo delle risorse di Azure dal computer di sviluppo locale, in alternativa all'utilizzo del portale di Azure.">Azure PowerShell</abbr> per la creazione di risorse di Azure:

    + [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.4 o successiva.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versione 5.0 o successiva.

---

### <a name="2-verify-prerequisites"></a>2. verificare i prerequisiti

Verificare i prerequisiti, che variano a seconda che si stia usando l'interfaccia della riga di comando di Azure o Azure PowerShell per la creazione di risorse di Azure:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

+ In un terminale o una finestra di comando eseguire `func --version` per verificare che il <abbr title="Set di strumenti da riga di comando per l'uso di funzioni di Azure nel computer locale.">Azure Functions Core Tools</abbr> sono la versione 3. x.

+ **Esegui** `az --version` per verificare che la versione dell'interfaccia della riga di comando di Azure sia 2,4 o successiva.

+ **Esegui** `az login` per accedere ad Azure e verificare una sottoscrizione attiva.

+ **Esegui** `dotnet --list-sdks` per verificare che sia installata la versione 3.1. x di .NET Core SDK

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Esegui** `func --version` per verificare che il Azure Functions Core Tools sia la versione 3. x.

+ **Esegui** `(Get-Module -ListAvailable Az).Version` e verificare la versione 5,0 o successiva. 

+ **Esegui** `Connect-AzAccount` per accedere ad Azure e verificare una sottoscrizione attiva.

+ **Esegui** `dotnet --list-sdks` per verificare che sia installata la versione 3.1. x di .NET Core SDK

---

## <a name="3-create-a-local-function-project"></a>3. creare un progetto di funzione locale

In questa sezione viene creato un locale <abbr title="Un contenitore logico per una o più funzioni singole che possono essere distribuite e gestite insieme.">Progetto di funzioni di Azure</abbr> in C#. Ogni funzione nel progetto risponde a una specifica <abbr title="Evento che richiama il codice della funzione, ad esempio una richiesta HTTP, un messaggio della coda o un orario specifico.">trigger</abbr>.

1. Eseguire il `func init` comando per creare un progetto di funzioni in una cartella denominata *LocalFunctionProj* con il runtime specificato:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Esegui** ' CD LocalFunctionProj ' per passare al <abbr title="Questa cartella contiene vari file per il progetto, inclusi i file di configurazione denominati local.settings.json e host.json. Poiché local.settings.json può contenere segreti scaricati da Azure, per impostazione predefinita il file viene escluso dal controllo del codice sorgente nel file con estensione gitignore.">cartella di progetto</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Aggiungere una funzione al progetto usando il comando seguente:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    L' `--name` argomento è il nome univoco della funzione (HttpExample).

    L' `--template` argomento specifica il trigger della funzione (http).

    
    <br/>   
    <details>  
    <summary><strong>Facoltativo: codice per HttpExample.cs</strong></summary>  
    
    *HttpExample.cs* contiene un metodo `Run` che riceve i dati della richiesta nella variabile `req` come oggetto [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) decorato con **HttpTriggerAttribute**, che definisce il comportamento del trigger.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    L'oggetto restituito è [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult), che restituisce un messaggio di risposta come [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) o [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Per altre informazioni, vedere [Trigger e associazioni HTTP di Funzioni di Azure](./functions-bindings-http-webhook.md?tabs=csharp).  
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. eseguire la funzione localmente

1. Eseguire la funzione avviando l'host di runtime locale di Funzioni di Azure nella cartella *LocalFunctionProj*:

    ```
    func start
    ```

    Verso la fine dell'output, verranno visualizzate le righe seguenti: 

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...

    </pre>

    <br/>
    <details>
    <summary><strong>Non è possibile visualizzare HttpExample nell'output</strong></summary>

    Se HttpExample non viene visualizzato, è probabile che l'host sia stato avviato dall'esterno della cartella radice del progetto. In tal caso, usare <kbd>CTRL + C</kbd> per arrestare l'host, passare alla cartella radice del progetto ed eseguire di nuovo il comando precedente.
    </details>

1. Copiare l'URL della funzione **HttpExample** da questo output in un browser e aggiungere la stringa di query **? name =<YOUR_NAME>**, rendendo l'URL completo, ad esempio **http://localhost:7071/api/HttpExample?name=Functions** . Nel browser dovrebbe essere visualizzato un messaggio come **Hello Functions**:

    ![Risultato della funzione eseguita in locale nel browser](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)


1. Premere  <kbd>CTRL + C</kbd> e scegliere <kbd>y</kbd> per arrestare l'host di funzioni.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. creare risorse di supporto di Azure per la funzione

Prima di poter distribuire il codice della funzione in Azure, è necessario creare un <abbr title="Un contenitore logico per le risorse di Azure correlate che è possibile gestire come unità.">gruppo di risorse</abbr>, <abbr title="Un account che contiene tutti gli oggetti dati di archiviazione di Azure. L'account di archiviazione fornisce uno spazio dei nomi univoco per i dati di archiviazione.">archiviazione di Azure</abbr>e <abbr title="Risorsa cloud che ospita funzioni senza server in Azure, che fornisce l'ambiente di calcolo sottostante in cui vengono eseguite le funzioni.">app per le funzioni</abbr> utilizzando i comandi seguenti:

1. Se non è già stato fatto, accedere ad Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


    ---

1. Creare un gruppo di risorse denominato `AzureFunctionsQuickstart-rg` nell'area `westeurope`. 

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```

    Il comando [az group create](/cli/azure/group#az-group-create) crea un gruppo di risorse. In genere si creano il gruppo di risorse e le risorse in un <abbr title="Riferimento geografico a un Data Center di Azure specifico in cui vengono allocate le risorse.">region</abbr> nelle vicinanze, usando un'area disponibile restituita dal `az account list-locations` comando.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```


    ---

    Non è possibile ospitare app Windows e Linux nello stesso gruppo di risorse. Se si dispone d un gruppo di risorse esistente denominato `AzureFunctionsQuickstart-rg` con un'app per le funzioni Windows o un'app Web, sarà necessario usare un gruppo di risorse diverso.
    
1. Creare un account di archiviazione di Azure per utilizzo generico nel gruppo di risorse e nell'area geografica:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Sostituire `<STORAGE_NAME>` con un nome appropriato e <abbr title="Il nome deve essere univoco in tutti gli account di archiviazione usati da tutti i clienti di Azure a livello globale. Ad esempio, è possibile usare una combinazione del nome personale o della società, il nome dell'applicazione e un identificatore numerico, come in contosobizappstorage20">univoco in archiviazione di Azure</abbr>. I nomi devono contenere da tre a 24 caratteri costituiti esclusivamente da numeri e lettere in minuscolo. `Standard_LRS` specifica un account per utilizzo generico, che è [supportato da Funzioni](storage-considerations.md#storage-account-requirements).


1. Creare l'app per le funzioni in Azure.
**Sostituisci** ' <STORAGE_NAME> * * con il nome nel passaggio precedente.
**Sostituisci** ' <APP_NAME>' con un nome univoco globale.

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    Sostituire `<STORAGE_NAME>` con il nome dell'account usato nel passaggio precedente.

    Sostituisci `<APP_NAME>` con <abbr title="Nome che deve essere univoco in tutti i clienti di Azure A livello globale. Ad esempio, è possibile usare una combinazione del nome personale o dell'organizzazione, il nome dell'applicazione e un identificatore numerico, come in Contoso-bizapp-Func-20.">nome univoco</abbr>. `<APP_NAME>` è anche il dominio DNS predefinito per l'app per le funzioni. 

    <br/>
    <details>
    <summary><strong>Qual è il costo delle risorse di cui è stato effettuato il provisioning in Azure?</strong></summary>

    Questo comando crea un'app per le funzioni in esecuzione nel runtime del linguaggio specificato sotto il [piano a consumo di funzioni di Azure](consumption-plan.md), che è gratuito per la quantità di utilizzo che si influisce qui. Il comando effettua anche il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse, con cui è possibile monitorare l'app per le funzioni e visualizzare i log. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md). L'istanza non comporta costi finché non viene attivata.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. distribuire il progetto di funzione in Azure


**Copia** di ' Func Azure funtionapp Publish <APP_NAME> nel terminale **sostituire** `<APP_NAME>` con il nome dell'app.
**Esegui**

```console
func azure functionapp publish <APP_NAME>
```

Il `publish` comando Mostra risultati simili all'output seguente (troncato per semplicità):

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. richiamare la funzione in Azure

Copiare l' **URL di richiamo** completo visualizzato nell'output del `publish` comando in una barra degli indirizzi del browser. **Aggiungere** il parametro di query **&name = functions**. 

![Output della funzione eseguita in Azure in un browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Pulire le risorse

Se si continua con il [passaggio successivo](#next-steps) e si aggiunge un output della coda di archiviazione di Azure <abbr title="Connessione dichiarativa tra una funzione e altre risorse. Un'associazione di input fornisce dati alla funzione. un'associazione di output fornisce dati dalla funzione ad altre risorse.">binding</abbr>, Mantieni tutte le risorse disponibili in base a quanto già fatto.

In caso contrario, usare il comando seguente per eliminare il gruppo di risorse e tutte le relative risorse contenute per evitare di incorrere in costi aggiuntivi.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
