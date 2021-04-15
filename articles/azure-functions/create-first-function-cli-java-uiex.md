---
title: Creare una funzione Java dalla riga di comando - Funzioni di Azure
description: Informazioni su come creare una funzione Java dalla riga di comando e quindi pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: b5bc453e2e0371ee0412824f01d99863b12d91e2
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375373"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Avvio rapido: Creare una funzione Java in Azure dalla riga di comando

> [!div class="op_single_selector" title1="Selezionare il linguaggio della funzione: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Usare gli strumenti da riga di comando per creare una funzione Java che risponde alle richieste HTTP. Testare il codice in locale, quindi distribuirlo nell'ambiente serverless di Funzioni di Azure.

Il completamento di questa guida introduttiva comporta un costo ridotto di pochi centesimi di USD <abbr title="Profilo che gestisce le informazioni di fatturazione per l'utilizzo di Azure.">Account Azure</abbr>.

Se si preferisce non usare Maven come strumento di sviluppo, vedere le esercitazioni simili per sviluppatori Java che usano [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) e [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

Per eseguire le procedure descritte è necessario:

+ Un account Azure con un account attivo <abbr title="Struttura organizzativa di base in cui si gestiscono le risorse in Azure, in genere associate a un singolo o reparto all'interno di un'organizzazione.">sottoscrizione</abbr>. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) versione 3.x.

+ L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.4 o successiva.

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versione 8 o 11. È necessario impostare la variabile di ambiente `JAVA_HOME` sul percorso di installazione della versione corretta di JDK.

+ [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

+ In un terminale o in una finestra di comando eseguire `func --version` per verificare che <abbr title="Set di strumenti da riga di comando per l'Funzioni di Azure nel computer locale.">Azure Functions Core Tools</abbr> sono la versione 3.x.

+ Eseguire `az --version` per verificare che la versione dell'interfaccia della riga di comando di Azure sia 2.4 o successiva.

+ Eseguire `az login` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. Creare un progetto di funzione locale

In Funzioni di Azure un progetto di funzione è un contenitore per una o più singole funzioni che rispondono a una specifica <abbr title="Tipo di evento che richiama il codice della funzione, ad esempio una richiesta HTTP, un messaggio in coda o un'ora specifica.">trigger</abbr>. Tutte le funzioni di un progetto condividono le stesse configurazioni locali e di hosting. In questa sezione viene creato un progetto di funzione che contiene una singola funzione.

1. In una cartella vuota eseguire il comando seguente per generare il progetto di Funzioni da un [archetipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```

    ---

    <br/>
    <details>
    <summary><strong>Per eseguire funzioni in Java 11</strong></summary>

    Usare `-DjavaVersion=11` per eseguire le funzioni in Java 11. Per altre informazioni, vedere [Versioni di Java](functions-reference-java.md#java-versions).
    </details>

1. Maven chiede i valori necessari per completare la generazione del progetto nella distribuzione.
    Quando richiesto, specificare i valori seguenti:

    | Prompt | Valore | Descrizione |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Un valore che identifica in modo univoco il progetto tra tutti gli altri, seguendo le [regole di denominazione dei pacchetti](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) per Java. |
    | **artifactId** | `fabrikam-functions` | Un valore che corrisponde al nome del jar, senza un numero di versione. |
    | **version** | `1.0-SNAPSHOT` | Scegliere il valore predefinito. |
    | **package** | `com.fabrikam` | Un valore che corrisponde al pacchetto Java per il codice della funzione generato. Usare il valore predefinito. |

1. Digitare `Y` o premere INVIO per confermare.

    Maven crea i file di progetto in una nuova cartella denominata _artifactId_, che in questo esempio è `fabrikam-functions`. 

1. Passare alla cartella del progetto:

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>Elementi creati nella cartella LocalFunctionProj</strong></summary>

Questa cartella contiene vari file per il progetto, ad esempio *Function.java,* *FunctionTest.java* e *pom.xml*. Sono inoltre disponibili file di configurazioni denominati [local.settings.jsin](functions-run-local.md#local-settings-file) e [host.jsin](functions-host-json.md). Poiché *local.settings.json* può contenere segreti scaricati da Azure, per impostazione predefinita il file viene escluso dal controllo del codice sorgente nel file con estensione *gitignore*.
</details>

<br/>
<details>
<summary><strong>Codice per Function.java</strong></summary>

*Function.java* contiene un metodo `run` che riceve i dati della richiesta nella variabile `request` come oggetto [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) decorato con l'annotazione [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger), che definisce il comportamento del trigger. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Il messaggio di risposta viene generato dall'API [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder).

L'archetipo genera anche uno unit test per la funzione. Quando si modifica la funzione per aggiungere i binding o si aggiungono nuove funzioni al progetto, sarà necessario modificare anche i test nel file *FunctionTest.java*.
</details>

<br/>
<details>
<summary><strong>Codice per pom.xml</strong></summary>

Le impostazioni per le risorse di Azure create  per ospitare l'app sono definite nell'elemento di configurazione del plug-in con **un groupId** di nel `com.microsoft.azure` filepom.xmlgenerato.  Ad esempio, l'elemento di configurazione seguente indica a una distribuzione basata su Maven di creare un'app per le funzioni nel gruppo `java-functions-group` di risorse in `westus` <abbr title="Riferimento geografico a un data center di Azure specifico in cui vengono allocate le risorse.">region</abbr>. L'app per le funzioni viene eseguita in Windows ospitata nel piano `java-functions-app-service-plan`, che per impostazione predefinita è un piano a consumo serverless.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

È possibile modificare queste impostazioni per controllare la modalità di creazione delle risorse in Azure, ad esempio cambiando `runtime.os` da `windows` a `linux` prima della distribuzione iniziale. Per un elenco completo delle impostazioni supportate dal plug-in Maven, vedere i [dettagli di configurazione](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Eseguire la funzione in locale

1. **Eseguire la funzione** avviando l'host Funzioni di Azure runtime locale dalla *cartella LocalFunctionProj:*

    ```console
    mvn clean package
    mvn azure-functions:run
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

    Se HttpExample non viene visualizzato come illustrato sopra, è probabile che l'host non sia stato avviato dalla cartella radice del progetto. In tal caso, usare <kbd>CTRL+C</kbd> per arrestare l'host, passare alla cartella radice del progetto ed eseguire di nuovo il comando precedente.

1. **Copiare l'URL** della funzione da questo output in un browser e aggiungere `HttpExample` la stringa di query , rendendo `?name=<YOUR_NAME>` l'URL completo, ad esempio `http://localhost:7071/api/HttpExample?name=Functions` . Nel browser dovrebbe essere visualizzato un messaggio simile a `Hello Functions`:

    ![Risultato della funzione eseguita in locale nel browser](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    Il terminale in cui è stato avviato il progetto mostra anche l'output del log quando si effettuano le richieste.

1. Al termine, usare <kbd>CTRL+C</kbd> e scegliere <kbd>y</kbd> per arrestare l'host di funzioni.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. Distribuire il progetto di funzione in Azure

La prima volta che si distribuisce il progetto di Funzioni, in Azure vengono create un'app per le funzioni e le risorse correlate. Le impostazioni per le risorse di Azure create per ospitare l'app sono definite nel file *pom.xml.* In questo articolo verranno accettate le impostazioni predefinite.

<br/>
<details>
<summary><strong>Per creare un'app per le funzioni in esecuzione in Linux</strong></summary>

Per creare un'app per le funzioni in esecuzione in Linux anziché in Windows, modificare l'elemento nel `runtime.os` file *pom.xml* da `windows` a `linux` . L'esecuzione di Linux in un piano a consumo è supportata in [queste aree](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Non è possibile avere nello stesso gruppo di risorse app eseguite in Linux e app eseguite in Windows.
</details>

1. Prima della distribuzione, accedere alla sottoscrizione di Azure usando l'interfaccia della riga di comando di Azure o Azure PowerShell. 

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Il comando [az login](/cli/azure/reference-index#az-login) consente di accedere all'account Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) consente di accedere all'account Azure.

    ---

1. Usare il comando seguente per distribuire il progetto in una nuova app per le funzioni.

    ```console
    mvn azure-functions:deploy
    ```

    La distribuzione inserisce i file di progetto in un pacchetto e li distribuisce nella nuova app per le funzioni tramite [ZipDeploy](functions-deployment-technologies.md#zip-deploy). Il codice viene eseguito dal pacchetto di distribuzione in Azure.

<br/>
<details>
<summary><strong>Elementi creati in Azure</strong></summary>

+ Gruppo di risorse. Il nome è _java-functions-group_.
+ Account di archiviazione. Richiesto da Funzioni. Il nome viene generato in modo casuale in base ai requisiti di denominazione degli account di archiviazione.
+ Piano di hosting. Hosting serverless per l'app per le funzioni nell'area _westus_. Il nome è _java-functions-app-service-plan_.
+ App per le funzioni. Un'app per le funzioni è l'unità di distribuzione ed esecuzione per le funzioni. Il nome viene generato in modo casuale in base al valore di _artifactId_, a cui viene aggiunto un numero generato in modo casuale.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. Richiamare la funzione in Azure

Poiché la funzione usa un trigger HTTP, viene richiamato effettuando una **richiesta HTTP** al relativo URL nel browser o con uno strumento come <abbr title="Uno strumento da riga di comando per la generazione di richieste HTTP a un URL. Vedere https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Browser](#tab/browser)

Copiare **l'URL di** richiamo completo visualizzato nell'output del comando in una barra degli indirizzi del `publish` browser, aggiungendo il parametro di query `&name=Functions` . Nel browser dovrebbe essere visualizzato un output simile a quello visualizzato quando è stata eseguita la funzione in locale.

![Output della funzione eseguita in Azure in un browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Eseguire [`curl`](https://curl.haxx.se/) con l'**URL di richiamo**, aggiungendo il parametro `&name=Functions`. L'output del comando dovrebbe essere il testo "Hello Functions".

![Output della funzione eseguita in Azure con curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Pulire le risorse

Se si continua con il [passaggio successivo e](#next-steps) si aggiunge un Archiviazione di Azure <abbr title="In Archiviazione di Azure, un mezzo per associare una funzione a una coda di archiviazione, in modo che possa creare messaggi nella coda.">associazione di output della coda</abbr>, mantenere tutte le risorse disponibili perché si baseranno su ciò che è già stato fatto.

In caso contrario, usare il comando seguente per eliminare il gruppo di risorse e tutte le relative risorse contenute per evitare di incorrere in costi aggiuntivi.

 # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

<br>
<hr/>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
