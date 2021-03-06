---
title: Usare Java e Gradle per pubblicare una funzione in Azure
description: Creare e pubblicare una funzione attivata tramite HTTP in Azure con Java e Gradle.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: d7f8aa990f5a5e64d2d5c59b52457149187acddd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773982"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Usare Java e Gradle per creare e pubblicare una funzione in Azure

Questo articolo illustra come compilare e pubblicare un progetto di funzione Java Funzioni di Azure con lo strumento da riga di comando Gradle. Al termine, il codice della funzione viene eseguito in Azure in un [piano di hosting serverless](consumption-plan.md) e viene attivato da una richiesta HTTP. 

> [!NOTE]
> Se Gradle non è lo strumento di sviluppo preferito, vedere le esercitazioni simili per gli sviluppatori Java che usano [Maven](./create-first-function-cli-java.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) [e VS Code](./create-first-function-vs-code-java.md).

## <a name="prerequisites"></a>Prerequisiti

Per sviluppare funzioni con Java, è necessario che siano installati gli elementi seguenti:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versione 8
- [Interfaccia della riga di comando di Azure]
- [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.6.666 o successive
- [Gradle](https://gradle.org/), versione 4.10 e successive

È anche necessaria una sottoscrizione di Azure attiva. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Per completare questa guida di avvio rapido, è necessario impostare la variabile di ambiente JAVA_HOME sul percorso di installazione di JDK.

## <a name="prepare-a-functions-project"></a>Preparare un progetto di Funzioni

Usare il comando seguente per clonare il progetto di esempio:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Aprire e modificare nella sezione seguente con un nome univoco per evitare conflitti tra nomi di `build.gradle` dominio durante la distribuzione in `appName` Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Aprire il nuovo file Function.java dal percorso *src/main/java* in un editor di testo e rivedere il codice generato. Questo codice è una funzione [attivata da HTTP](functions-bindings-http-webhook.md) che restituisce il corpo della richiesta. 

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Eseguire il comando seguente per compilare e quindi eseguire il progetto di funzione:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Quando si esegue il progetto in locale, viene visualizzato un output di Azure Functions Core Tools simile al seguente:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Attivare la funzione dalla riga di comando usando il comando cURL seguente in una nuova finestra del terminale:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

L'output previsto è il seguente:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Se si imposta authLevel su o , la chiave della funzione non è necessaria quando viene `FUNCTION` `ADMIN` eseguita in locale. [](functions-bindings-http-webhook-trigger.md#authorization-keys)  

Usare `Ctrl+C` nel terminal per interrompere il codice funzione.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Distribuire la funzione in Azure

La prima volta che si distribuisce la nuova app per le funzioni, in Azure vengono create un'app per le funzioni e le risorse correlate. Prima di procedere alla distribuzione, è necessario usare il comando [az login](/cli/azure/authenticate-azure-cli) dell'interfaccia della riga di comando di Azure per accedere alla sottoscrizione di Azure. 

```azurecli
az login
```

> [!TIP]
> Se l'account può accedere a più sottoscrizioni, usare [az account set](/cli/azure/account#az_account_set) per impostare quella predefinita per questa sessione. 

Usare il comando seguente per distribuire il progetto in una nuova app per le funzioni. 

```bash
gradle azureFunctionsDeploy
```

In questo modo vengono create le risorse seguenti in Azure, in base ai valori nel file build.gradle:

+ Gruppo di risorse. Con il nome _resourceGroup_ specificato.
+ Account di archiviazione. Richiesto da Funzioni. Il nome viene generato in modo casuale in base ai requisiti di denominazione degli account di archiviazione.
+ Piano di servizio app. Hosting del piano a consumo serverless per l'app per le funzioni _nell'area appRegion specificata._ Il nome viene generato in modo casuale.
+ App per le funzioni. Un'app per le funzioni è l'unità di distribuzione ed esecuzione per le funzioni. Il nome è _appName_, con l'aggiunta finale di un numero generato in modo casuale. 

La distribuzione inserisce inoltre i file di progetto in un pacchetto e li distribuisce nella nuova app per le funzioni tramite [ZipDeploy](functions-deployment-technologies.md#zip-deploy), con la modalità run-from-package abilitata.

authLevel per il trigger HTTP nel progetto di esempio è `ANONYMOUS` , che ignora l'autenticazione. Tuttavia, se si usa un altro authLevel come o , è necessario ottenere la chiave della funzione per `FUNCTION` chiamare l'endpoint della funzione su `ADMIN` HTTP. Il modo più semplice per farlo è tramite il [portale di Azure].

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>Ottenere l'URL del trigger HTTP

È possibile ottenere l'URL necessario per attivare la funzione, con la chiave della funzione, nel portale di Azure. 

1. Passare al [portale di Azure], accedere, digitare il nome _appName_ dell'app per le funzioni nella casella **Cerca** nella parte superiore della pagina, quindi premere INVIO.
 
1. Nell'app per le funzioni selezionare **Funzioni,** scegliere la funzione, quindi fare clic su **</> Get Function Url** (Ottieni URL funzione) in alto a destra. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Creare l'URL della funzione dal portale di Azure":::

1. Scegliere il **valore predefinito (chiave della funzione)** e selezionare **Copia**. 

È ora possibile usare l'URL copiato per accedere alla funzione.

## <a name="verify-the-function-in-azure"></a>Verificare la funzione in Azure

Per verificare l'app per le funzioni in esecuzione in Azure tramite `cURL`, sostituire l'URL dell'esempio seguente con l'URL copiato dal portale.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Viene inviata una richiesta POST all'endpoint della funzione con `AzureFunctions` nel corpo. Verrà visualizzata la risposta seguente.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Passaggi successivi

È stato creato un progetto di funzioni Java con una funzione attivata tramite HTTP. Il progetto è stato quindi eseguito in un computer locale e distribuito in Azure. A questo punto, estendere la funzione effettuando l'operazione seguente.

> [!div class="nextstepaction"]
> [Aggiungere un'associazione di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-java.md)


[Interfaccia della riga di comando di Azure]: /cli/azure
[Azure portal]: https://portal.azure.com