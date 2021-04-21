---
title: Creare una funzione Python dalla riga di comando per Funzioni di Azure
description: Informazioni su come creare una funzione Python dalla riga di comando e pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 626cff867a336880689373c289087e2332a816ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787450"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Avvio rapido: Creare una funzione Python in Azure dalla riga di comando

> [!div class="op_single_selector" title1="Selezionare il linguaggio della funzione: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

In questo articolo vengono usati gli strumenti da riga di comando per creare una funzione Python che risponde alle richieste HTTP. Dopo aver eseguito il test del codice in locale, è possibile distribuirlo in <abbr title="Ambiente di elaborazione di runtime in cui tutti i dettagli del server sono trasparenti per gli sviluppatori di applicazioni, semplificando il processo di distribuzione e gestione del codice.">senza server</abbr> ambiente di <abbr title="Un servizio di Azure che offre un ambiente di elaborazione serverless a basso costo per le applicazioni.">Funzioni di Azure</abbr>.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

È inoltre disponibile una [versione basata su Visual Studio Code](create-first-function-vs-code-python.md) di questo articolo.

## <a name="1-configure-your-environment"></a>1. Configurare l'ambiente

Per eseguire le procedure descritte è necessario:

+ Un'istanza di Azure <abbr title="Profilo che gestisce le informazioni di fatturazione per l'utilizzo di Azure.">account</abbr> con un oggetto attivo <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo o reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) versione 3.x. 
  
+ In entrambi i <abbr title="Set di strumenti da riga di comando multipiattaforma per l'uso delle risorse di Azure dal computer di sviluppo locale, in alternativa all'uso del portale di Azure.">Interfaccia della riga di comando di Azure</abbr> oppure <abbr title="Modulo di PowerShell che fornisce comandi per l'uso delle risorse di Azure dal computer di sviluppo locale, in alternativa all'uso del portale di Azure.">Azure PowerShell</abbr> per la creazione di risorse di Azure:

    + [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.4 o successiva.

    + [Azure PowerShell](/powershell/azure/install-az-ps) versione 5.0 o successiva.

+ [Python 3.8 (64 bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bit)](https://www.python.org/downloads/release/python-368/), supportati tutti dalla versione 3.x di Funzioni di Azure.

### <a name="11-prerequisite-check"></a>1.1 Controllo dei prerequisiti

Verificare i prerequisiti, a seconda che si utilizzi l'interfaccia della riga di comando di Azure o Azure PowerShell per la creazione di risorse di Azure:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

+ In un terminale o in una finestra di comando eseguire `func --version` per verificare che <abbr title="Set di strumenti da riga di comando per l'Funzioni di Azure nel computer locale.">Azure Functions Core Tools</abbr> sono la versione 3.x.

+ Eseguire `az --version` per verificare che la versione dell'interfaccia della riga di comando di Azure sia 2.4 o successiva.

+ Eseguire `az login` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.

+ Eseguire `python --version` (Linux/macOS) o `py --version` (Windows) per verificare che la versione di Python sia 3.8.x, 3.7.x o 3.6.x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ In un terminale o in una finestra di comando eseguire `func --version` per verificare che <abbr title="Set di strumenti da riga di comando per l'uso Funzioni di Azure nel computer locale.">Azure Functions Core Tools</abbr> sono la versione 3.x.

+ Eseguire `(Get-Module -ListAvailable Az).Version` per verificare che la versione sia 5.0 o successiva. 

+ Eseguire `Connect-AzAccount` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.

+ Eseguire `python --version` (Linux/macOS) o `py --version` (Windows) per verificare che la versione di Python sia 3.8.x, 3.7.x o 3.6.x.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> Creare e attivare un ambiente virtuale

In una cartella appropriata eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.venv`. Assicurarsi di usare Python 3.8, 3.7 o 3.6, che sono supportati da Funzioni di Azure.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Se Python non ha installato il pacchetto venv nella distribuzione Linux, eseguire il comando seguente:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

È possibile eseguire tutti i comandi successivi in questo ambiente virtuale attivato. 

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. Creare un progetto di funzione locale

In questa sezione viene creato un oggetto locale <abbr title="Contenitore logico per una o più singole funzioni che possono essere distribuite e gestite insieme.">Funzioni di Azure progetto</abbr> in Python. Ogni funzione nel progetto risponde a una specifica <abbr title="Tipo di evento che richiama il codice della funzione, ad esempio una richiesta HTTP, un messaggio in coda o un'ora specifica.">trigger</abbr>.

1. Eseguire il `func init` comando per creare un progetto di funzioni in una cartella denominata *LocalFunctionProj* con il runtime specificato:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Passare alla cartella del progetto:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>Elementi creati nella cartella LocalFunctionProj</strong></summary>
    
    Questa cartella contiene vari file per il progetto, inclusi i file di configurazione denominati [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Poiché *local.settings.json* può contenere segreti scaricati da Azure, per impostazione predefinita il file viene escluso dal controllo del codice sorgente nel file con estensione *gitignore*.
    </details>

1. Aggiungere una funzione al progetto usando il comando seguente:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    `--name`L'argomento è il nome univoco della funzione (HttpExample).

    `--template`L'argomento specifica il trigger della funzione (HTTP).
    
    `func new`crea una sottocartella corrispondente al nome della funzione che contiene un file *\_ \_ init \_ \_ con* estensione py con il codice della funzione e un file di configurazione denominato *function.jsin*.

    <br/>    
    <details>
    <summary><strong>Codice per __init__.py</strong></summary>
    
    *\_\_init\_\_.py* contiene una funzione Python `main()` attivata in base alla configurazione presente in *function.json*.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    Per un trigger HTTP, la funzione riceve i dati della richiesta nella variabile `req` come definito in *function.json*. `req` è un'istanza della [classe azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). L'oggetto restituito, definito come `$return` in *function.json*, è un'istanza della [classe azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Per altre informazioni, vedere [Trigger e associazioni HTTP di Funzioni di Azure](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>Codice per function.jssu</strong></summary>

    *function.jsè* un file di configurazione che definisce <abbr title="Connessioni dichiarative tra una funzione e altre risorse. Un'associazione di input fornisce dati alla funzione. Un'associazione di output fornisce dati dalla funzione ad altre risorse.">associazioni di input e output</abbr> per la funzione, incluso il tipo di trigger.
    
    Se si vuole, è possibile modificare `scriptFile` per richiamare un file Python diverso.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Ogni binding richiede una direzione, un tipo e un nome univoco. Il trigger HTTP dispone di un'associazione di input di tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e di un'associazione di output di tipo [`http`](functions-bindings-http-webhook-output.md).    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Eseguire la funzione in locale

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
    <summary><strong>HttpExample non viene visualizzato nell'output</strong></summary>

    Se HttpExample non viene visualizzato, è probabile che l'host sia stato avviato dall'esterno della cartella radice del progetto. In tal caso, usare <kbd>CTRL+C</kbd> per arrestare l'host, passare alla cartella radice del progetto ed eseguire di nuovo il comando precedente.
    </details>

1. Copiare l'URL della funzione **HttpExample** da questo output in un browser e aggiungere la stringa di query **?name=<YOUR_NAME>**, rendendo l'URL completo, ad esempio **http://localhost:7071/api/HttpExample?name=Functions** . Il browser dovrebbe visualizzare un messaggio simile a **Hello Functions:**

    ![Risultato della funzione eseguita in locale nel browser](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Il terminale in cui è stato avviato il progetto mostra anche l'output del log quando si effettuano le richieste.

1. Al termine, usare <kbd>CTRL+C</kbd> e scegliere <kbd>y</kbd> per arrestare l'host di funzioni.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Creare risorse di Azure di supporto per la funzione

Prima di poter distribuire il codice della funzione in Azure, è necessario creare un <abbr title="Un contenitore logico per le risorse di Azure correlate che è possibile gestire come unità.">gruppo di risorse</abbr>Un <abbr title="Un account che contiene tutti gli oggetti dati di archiviazione di Azure. L'account di archiviazione fornisce uno spazio dei nomi univoco per i dati di archiviazione.">archiviazione di Azure</abbr>e <abbr title="Risorsa cloud che ospita funzioni serverless in Azure, che fornisce l'ambiente di calcolo sottostante in cui vengono eseguite le funzioni.">app per le funzioni</abbr> usando i comandi seguenti:

1. Se non è già stato fatto, accedere ad Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Il comando [az login](/cli/azure/reference-index#az_login) consente di accedere all'account Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) consente di accedere all'account Azure.

    ---

1. Creare un gruppo di risorse denominato `AzureFunctionsQuickstart-rg` nell'area `westeurope`. 

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Il comando [az group create](/cli/azure/group#az_group_create) crea un gruppo di risorse. In genere si creano il gruppo di risorse e le risorse in un <abbr title="Riferimento geografico a un data center di Azure specifico in cui vengono allocate le risorse.">region</abbr> nelle vicinanze, usando un'area disponibile restituita dal `az account list-locations` comando .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) crea un gruppo di risorse. In genere, il gruppo di risorse e le risorse vengono creati in un'area vicina alla propria, usando un'area disponibile restituita dal cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

    Non è possibile ospitare app Windows e Linux nello stesso gruppo di risorse. Se si dispone d un gruppo di risorse esistente denominato `AzureFunctionsQuickstart-rg` con un'app per le funzioni Windows o un'app Web, sarà necessario usare un gruppo di risorse diverso.

1. Creare un account di Archiviazione di Azure generico nel gruppo di risorse e nell'area:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) crea l'account di archiviazione. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Il cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) crea l'account di archiviazione.

    ---

    Sostituire `<STORAGE_NAME>` con un nome appropriato per l'utente e <abbr title="Il nome deve essere univoco in tutti gli account di archiviazione usati a livello globale da tutti i clienti di Azure. Ad esempio, è possibile usare una combinazione del nome personale o della società, del nome dell'applicazione e di un identificatore numerico, come in contosobizappstorage20.">univoco in Archiviazione di Azure</abbr>. I nomi devono contenere da tre a 24 caratteri costituiti esclusivamente da numeri e lettere in minuscolo. `Standard_LRS` specifica un account per utilizzo generico, che è [supportato da Funzioni](storage-considerations.md#storage-account-requirements).
    
    Per questo argomento di avvio rapido, il costo dell'account di archiviazione è solo di pochi centesimi di dollaro USA.

1. Creare l'app per le funzioni in Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Il comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) crea l'app per le funzioni in Azure. Se si usa Python 3.7 o 3.6, sostituire `--runtime-version` rispettivamente con `3.7` o `3.6`.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    Il cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) crea l'app per le funzioni in Azure. Se si usa Python 3.7 o 3.6, sostituire `-RuntimeVersion` rispettivamente con `3.7` o `3.6`.

    ---
    
    Sostituire `<STORAGE_NAME>` con il nome dell'account usato nel passaggio precedente.

    Sostituire `<APP_NAME>` con un <abbr title="Nome che deve essere univoco per tutti i clienti di Azure a livello globale. Ad esempio, è possibile usare una combinazione del nome personale o dell'organizzazione, del nome dell'applicazione e di un identificatore numerico, come in contoso-bizapp-func-20.">nome univoco globale appropriato</abbr>. `<APP_NAME>` è anche il dominio DNS predefinito per l'app per le funzioni. 
    
    <br/>
    <details>
    <summary><strong>Qual è il costo delle risorse di cui è stato effettuato il provisioning in Azure?</strong></summary>

    Questo comando crea un'app per le funzioni che esegue il runtime del linguaggio specificato nel [Piano a consumo di Funzioni di Azure](functions-scale.md#overview-of-plans), che è gratuito per la quantità di utilizzo prevista in questo argomento. Il comando effettua anche il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse, con cui è possibile monitorare l'app per le funzioni e visualizzare i log. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md). L'istanza non comporta costi finché non viene attivata.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Distribuire il progetto di funzione in Azure

Dopo aver creato correttamente l'app per le funzioni  in Azure, è ora possibile distribuire il progetto di funzioni locali usando il comando [func azure functionapp publish.](functions-run-local.md#project-file-deployment)  

Nell'esempio seguente sostituire `<APP_NAME>` con il nome dell'app.

```console
func azure functionapp publish <APP_NAME>
```

Il `publish` comando mostra risultati simili all'output seguente (troncati per semplicità):

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

## <a name="7-invoke-the-function-on-azure"></a>7. Richiamare la funzione in Azure

Poiché la funzione usa un trigger HTTP, è possibile richiamarlo effettuando una richiesta HTTP al relativo URL nel browser o con uno strumento come <abbr title="Uno strumento da riga di comando per la generazione di richieste HTTP a un URL. Vedere https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Browser](#tab/browser)

Copiare **l'URL** di richiamo completo visualizzato nell'output del comando in una barra degli indirizzi del browser, aggiungendo il parametro di `publish` query&**name=Functions**. Nel browser dovrebbe essere visualizzato un output simile a quello visualizzato quando è stata eseguita la funzione in locale.

![Output della funzione eseguita in Azure in un browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Eseguire [`curl`](https://curl.haxx.se/) con **l'URL invoke**, aggiungendo il parametro&**name=Functions**. L'output del comando dovrebbe essere il testo "Hello Functions".

![Output della funzione eseguita in Azure con curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 Visualizzare i log in streaming in tempo reale

Eseguire il comando seguente per visualizzare i [log in streaming](functions-run-local.md#enable-streaming-logs) near real-time in Application Insights nel portale di Azure:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Sostituire `<APP_NAME>` con il nome dell'app per le funzioni.

In una finestra del terminale separata o nel browser chiamare di nuovo la funzione remota. Nel terminale viene visualizzato un log dettagliato dell'esecuzione della funzione in Azure. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Pulire le risorse

Se si continua con il [passaggio successivo e si](#next-steps) aggiunge un <abbr title="Mezzo per associare una funzione a una coda di archiviazione, in modo che possa creare messaggi nella coda. "> Associazione di output della coda di Archiviazione di Azure</abbr>, mantenere tutte le risorse disponibili perché si baseranno su ciò che è già stato fatto.

In caso contrario, usare il comando seguente per eliminare il gruppo di risorse e tutte le relative risorse contenute per evitare di incorrere in costi aggiuntivi.

 # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Problemi? Segnalarli](https://aka.ms/python-functions-qs-survey).
