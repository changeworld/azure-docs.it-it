---
title: Sviluppare Funzioni di Azure con Visual Studio Code
description: Informazioni su come sviluppare e testare Funzioni di Azure usando l'estensione Funzioni di Azure per Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c2869b2b30722495523a9f0dfb2d70a17a205854
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871274"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Sviluppare Funzioni di Azure con Visual Studio Code

[L'Funzioni di Azure per Visual Studio Code] consente di sviluppare funzioni in locale e distribuirle in Azure. Se questa è la prima esperienza con Funzioni di Azure, è consigliabile leggere altre informazioni in [Introduzione a Funzioni di Azure](functions-overview.md).

L Funzioni di Azure'estensione offre i vantaggi seguenti:

* Modifica, compilazione ed esecuzione di funzioni nel computer di sviluppo locale.
* Pubblicazione del proprio progetto di Funzioni di Azure direttamente in Azure.
* Scrivere le funzioni in vari linguaggi sfruttando al tempo stesso i vantaggi di Visual Studio Code.

L'estensione può essere usata con i linguaggi seguenti, supportati dal runtime di Funzioni di Azure a partire dalla versione 2.x:

* [C# compilato](functions-dotnet-class-library.md)
* [Script C#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Richiede [l'impostazione dello script C# come linguaggio di progetto predefinito.](#c-script-projects)

In questo articolo gli esempi sono attualmente disponibili solo per le funzioni della libreria di classi JavaScript (Node.js) e C#.  

Questo articolo fornisce informazioni dettagliate su come usare l'estensione Funzioni di Azure per sviluppare funzioni e pubblicarle in Azure. Prima di leggere questo articolo, è [necessario creare la prima funzione usando Visual Studio Code](./create-first-function-vs-code-csharp.md).

> [!IMPORTANT]
> Non combinare lo sviluppo locale e lo sviluppo del portale per una singola app per le funzioni. Quando si pubblica da un progetto locale a un'app per le funzioni, il processo di distribuzione sovrascrive le funzioni sviluppate nel portale.

## <a name="prerequisites"></a>Prerequisiti

Prima di installare ed eseguire [l'estensione Funzioni di Azure Funzioni di Azure][per Visual Studio Code], è necessario soddisfare i requisiti seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) installato in una delle [piattaforme supportate.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Le altre risorse necessarie, ad esempio un account di archiviazione di Azure, vengono create nella sottoscrizione quando si esegue la pubblicazione [usando Visual Studio Code](#publish-to-azure). 

### <a name="run-local-requirements"></a>Eseguire i requisiti locali

Questi prerequisiti sono necessari solo per eseguire [le funzioni ed eseguirne il debug in locale.](#run-functions-locally) Non sono necessari per creare o pubblicare progetti in Funzioni di Azure.

# <a name="c"></a>[C\#](#tab/csharp)

+ Il [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versione 2.x o successiva. Il pacchetto Core Tools viene scaricato e installato automaticamente quando si avvia il progetto in locale. Core Tools include l'intero Funzioni di Azure runtime, quindi il download e l'installazione potrebbero richiedere del tempo.

+ [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) per Visual Studio Code. 

+ [interfaccia della riga di comando di .NET Core strumenti .](/dotnet/core/tools/?tabs=netcore2x)  

# <a name="java"></a>[Java](#tab/java)

+ La [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versione 2.x o successiva. Il pacchetto Core Tools viene scaricato e installato automaticamente quando si avvia il progetto in locale. Core Tools include l'intero Funzioni di Azure runtime, quindi il download e l'installazione potrebbero richiedere del tempo.

+ [Debugger per l'estensione Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ [Java 8 consigliato.](/azure/developer/java/fundamentals/java-jdk-long-term-support) Per altre versioni supportate, vedere [Versioni java](functions-reference-java.md#java-versions).

+ [Maven 3 o versione successiva](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ La [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versione 2.x o successiva. Il pacchetto Core Tools viene scaricato e installato automaticamente quando si avvia il progetto in locale. Core Tools include l'intero Funzioni di Azure runtime, quindi il download e l'installazione potrebbero richiedere del tempo.

+ [Node.js](https://nodejs.org/), versioni Active LTS e Maintenance LTS (10.14.1 consigliata). Usare il comando `node --version` per controllare la versione in uso. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ La [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versione 2.x o successiva. Il pacchetto Core Tools viene scaricato e installato automaticamente quando si avvia il progetto in locale. Core Tools include l'intero Funzioni di Azure runtime, quindi il download e l'installazione potrebbero richiedere del tempo.

+ [È consigliabile utilizzare PowerShell 7.](/powershell/scripting/install/installing-powershell-core-on-windows) Per informazioni sulla versione, vedere [Versioni di PowerShell.](functions-reference-powershell.md#powershell-versions)

+ Sia il [runtime di .NET Core 3.1](https://dotnet.microsoft.com/download) che il [runtime di .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ [Estensione PowerShell per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

# <a name="python"></a>[Python](#tab/python)

+ La [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) versione 2.x o successiva. Il pacchetto Core Tools viene scaricato e installato automaticamente quando si avvia il progetto in locale. Core Tools include l'intero Funzioni di Azure runtime, quindi il download e l'installazione potrebbero richiedere del tempo.

+ [Python 3.x](https://www.python.org/downloads/). Per informazioni sulla versione, vedere [Versioni di Python](functions-reference-python.md#python-version) da parte Funzioni di Azure runtime.

+ [Estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) per Visual Studio Code.

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Creare un progetto di Funzioni di Azure

L'estensione Funzioni consente di creare un progetto di app per le funzioni, insieme alla prima funzione. La procedura seguente illustra come creare una funzione attivata tramite HTTP in un nuovo progetto di Funzioni. [Il trigger HTTP](functions-bindings-http-webhook.md) è il modello di trigger di funzione più semplice da illustrare.

1. In **Azure: Funzioni** selezionare l'icona **Crea** funzione:

    ![Creare una funzione](./media/functions-develop-vs-code/create-function.png)

1. Selezionare la cartella per il progetto di app per le funzioni e quindi **Selezionare un linguaggio per il progetto di funzione.**

1. Selezionare il **modello di funzione trigger HTTP** oppure è possibile selezionare Ignora per il **momento** per creare un progetto senza una funzione. È sempre possibile [aggiungere una funzione al progetto in un secondo](#add-a-function-to-your-project) momento.

    ![Scegliere il modello Trigger HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Digitare **HttpExample come** nome della funzione, premere INVIO e quindi selezionare **Autorizzazione funzione.** Questo livello di autorizzazione richiede di fornire una chiave [di funzione](functions-bindings-http-webhook-trigger.md#authorization-keys) quando si chiama l'endpoint della funzione.

    ![Selezionare l'autorizzazione per le funzioni](./media/functions-develop-vs-code/create-function-auth.png)

    Viene creata una funzione nel linguaggio scelto e nel modello per una funzione attivata tramite HTTP.

    ![Modello di funzione attivato da HTTP in Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>File di progetto generati

Il modello di progetto crea un progetto nel linguaggio scelto e installa le dipendenze necessarie. Per qualsiasi linguaggio, il nuovo progetto include i file seguenti:

* **host.json**: consente di configurare l'host di Funzioni. Queste impostazioni si applicano quando si eseguono funzioni in locale e quando vengono eseguite in Azure. Per altre informazioni, vedere il [riferimento su host.json](functions-host-json.md).

* **local.settings.jsin**: mantiene le impostazioni usate quando si eseguono le funzioni in locale. Queste impostazioni vengono usate solo quando si eseguono funzioni in locale. Per altre informazioni, vedere [File di impostazioni locali](#local-settings-file).

    >[!IMPORTANT]
    >Poiché il local.settings.jsfile può contenere segreti, è necessario escluderlo dal controllo del codice sorgente del progetto.

A seconda della lingua, vengono creati gli altri file seguenti:

# <a name="c"></a>[C\#](#tab/csharp)

* [File della libreria di classi HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project) che implementa la funzione .

# <a name="java"></a>[Java](#tab/java)

+ Un pom.xml file nella cartella radice che definisce i parametri di progetto e distribuzione, incluse le dipendenze del progetto e la [versione java](functions-reference-java.md#java-versions). Il pom.xml contiene anche informazioni sulle risorse di Azure create durante una distribuzione.   

+ Un [file Functions.java](functions-reference-java.md#triggers-and-annotations) nel percorso src che implementa la funzione .

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Un package.jssul file nella cartella radice.

* Una cartella HttpExample che contiene ilfunction.jsnel [ file](functions-reference-node.md#folder-structure) di definizione e il [ fileindex.js](functions-reference-node.md#exporting-a-function), un file Node.js che contiene il codice della funzione.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* Cartella HttpExample che contiene ilfunction.js[ nel file](functions-reference-powershell.md#folder-structure) di definizione e il file run.ps1, che contiene il codice della funzione.
 
# <a name="python"></a>[Python](#tab/python)
    
* Un file di requirements.txt di progetto che elenca i pacchetti richiesti da Funzioni.
    
* Cartella HttpExample che contiene ilfunction.js[nel file](functions-reference-python.md#folder-structure) di definizione e il file init con estensione \_ \_ \_ \_ py, che contiene il codice della funzione.

---

A questo punto, è possibile [aggiungere associazioni di input e output](#add-input-and-output-bindings) alla funzione. È anche possibile [aggiungere una nuova funzione al progetto](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installare le estensioni di binding

Ad eccezione dei trigger HTTP e timer, le associazioni vengono implementate nei pacchetti di estensione. È necessario installare i pacchetti di estensione per i trigger e le associazioni che ne hanno bisogno. Il processo di installazione delle estensioni di associazione dipende dal linguaggio del progetto.

# <a name="c"></a>[C\#](#tab/csharp)

Eseguire il [comando dotnet add package](/dotnet/core/tools/dotnet-add-package) nella finestra Terminale per installare i pacchetti di estensione necessari nel progetto. Il comando seguente installa l'estensione Archiviazione di Azure, che implementa le associazioni per l'archiviazione BLOB, code e tabelle.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Aggiungere una funzione al progetto

È possibile aggiungere una nuova funzione a un progetto esistente usando uno dei modelli di trigger di Funzioni predefiniti. Per aggiungere un nuovo trigger di funzione, premere F1 per aprire il riquadro comandi, quindi cercare ed eseguire il **comando Funzioni di Azure: Crea funzione**. Seguire le istruzioni per scegliere il tipo di trigger e definire gli attributi obbligatori del trigger. Se il trigger richiede una chiave di accesso o una stringa di connessione per connettersi a un servizio, prepararlo prima di creare il trigger della funzione.

I risultati di questa azione dipendono dal linguaggio del progetto:

# <a name="c"></a>[C\#](#tab/csharp)

Un nuovo file della libreria di classi C# (con estensione cs) viene aggiunto al progetto.

# <a name="java"></a>[Java](#tab/java)

Un nuovo file Java (.java) viene aggiunto al progetto.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Nel progetto viene creata una nuova cartella. La cartella contiene un nuovo function.jsfile e il nuovo file di codice JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Nel progetto viene creata una nuova cartella. La cartella contiene un nuovo function.jsfile e il nuovo file di codice di PowerShell.

# <a name="python"></a>[Python](#tab/python)

Nel progetto viene creata una nuova cartella. La cartella contiene un nuovo function.jsfile e il nuovo file di codice Python.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Connettersi ai servizi

È possibile connettere la funzione ad altri servizi di Azure aggiungendo associazioni di input e output. Le associazioni connettono la funzione ad altri servizi senza che sia necessario scrivere il codice di connessione. Il processo per l'aggiunta di associazioni dipende dal linguaggio del progetto. Per altre informazioni sui binding, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md).

Gli esempi seguenti si connettono a una coda di archiviazione denominata , in cui la stringa di connessione per l'account di archiviazione è impostata nell'impostazione dell'applicazione `outqueue` `MyStorageConnection` local.settings.jsattivata.

# <a name="c"></a>[C\#](#tab/csharp)

Aggiornare il metodo della funzione per aggiungere il parametro seguente alla definizione `Run` del metodo:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Il parametro `msg` è un tipo `ICollector<T>`, che rappresenta una raccolta di messaggi scritti in un binding di output al completamento della funzione. Il codice seguente aggiunge un messaggio alla raccolta :

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 I messaggi vengono inviati alla coda al completamento della funzione.

Per altre informazioni, vedere la documentazione di riferimento [sull'associazione di output dell'archiviazione](functions-bindings-storage-queue-output.md?tabs=csharp) code. Per altre informazioni generali sulle associazioni che è possibile aggiungere a una funzione, vedere Aggiungere associazioni a una funzione esistente [in Funzioni di Azure](add-bindings-existing-function.md?tabs=csharp). 

# <a name="java"></a>[Java](#tab/java)

Aggiornare il metodo della funzione per aggiungere il parametro seguente alla definizione `Run` del metodo:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

Il parametro è un tipo , dove è una stringa scritta in `msg` `OutputBinding<T>` `T` un'associazione di output al completamento della funzione. Il codice seguente imposta il messaggio nell'associazione di output:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Questo messaggio viene inviato alla coda al termine della funzione.

Per altre informazioni, vedere la documentazione di riferimento [sull'associazione di output dell'archiviazione code.](functions-bindings-storage-queue-output.md?tabs=java) Per altre informazioni generali sulle associazioni che è possibile aggiungere a una funzione, vedere Aggiungere associazioni a una funzione esistente [in Funzioni di Azure](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Nel codice della funzione `msg` l'associazione è accessibile da `context` , come nell'esempio seguente:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Questo messaggio viene inviato alla coda al termine della funzione.

Per altre informazioni, vedere la documentazione di riferimento [sull'associazione di output dell'archiviazione code.](functions-bindings-storage-queue-output.md?tabs=javascript) Per altre informazioni generali sulle associazioni che è possibile aggiungere a una funzione, vedere Aggiungere associazioni a una funzione esistente [in Funzioni di Azure](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Questo messaggio viene inviato alla coda al termine della funzione.

Per altre informazioni, vedere la documentazione di riferimento [sull'associazione di output dell'archiviazione code.](functions-bindings-storage-queue-output.md?tabs=powershell) Per altre informazioni generali sulle associazioni che è possibile aggiungere a una funzione, vedere Aggiungere associazioni a una funzione esistente [in Funzioni di Azure](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Aggiornare la `Main` definizione per aggiungere un parametro di output in modo che la definizione sia simile `msg: func.Out[func.QueueMessage]` all'esempio seguente:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Il codice seguente aggiunge i dati stringa dalla richiesta alla coda di output:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Questo messaggio viene inviato alla coda al termine della funzione.

Per altre informazioni, vedere la documentazione di riferimento [sull'associazione di output dell'archiviazione code.](functions-bindings-storage-queue-output.md?tabs=python) Per altre informazioni generali sulle associazioni che è possibile aggiungere a una funzione, vedere Aggiungere associazioni a una funzione esistente [in Funzioni di Azure](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Eseguire la pubblicazione in Azure

Visual Studio Code consente di pubblicare il progetto Funzioni direttamente in Azure. Durante il processo vengono create un'app per le funzioni e le risorse correlate nella sottoscrizione di Azure. L'app per le funzioni fornisce un contesto di esecuzione per le funzioni. Il progetto viene inserito in un pacchetto e distribuito nella nuova app per le funzioni nella sottoscrizione di Azure.

Quando si pubblica da Visual Studio Code in una nuova app per le funzioni in Azure, è possibile scegliere un percorso di creazione rapida dell'app per le funzioni usando le impostazioni predefinite o un percorso avanzato, in cui si ha maggiore controllo sulle risorse remote create. 

Quando si pubblica da Visual Studio Code, si sfrutta la tecnologia di distribuzione [Zip.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Creazione rapida di app per le funzioni

Quando si sceglie + Crea nuova app per le funzioni **in Azure...**, l'estensione genera automaticamente i valori per le risorse di Azure necessarie per l'app per le funzioni. Questi valori sono basati sul nome dell'app per le funzioni scelto. Per un esempio dell'uso delle impostazioni predefinite per pubblicare il progetto in una nuova app per le funzioni in Azure, vedere l'articolo Visual Studio Code [di avvio rapido.](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)

Per specificare nomi espliciti per le risorse create, è necessario scegliere il percorso di creazione avanzato.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Pubblicare un progetto in una nuova app per le funzioni in Azure usando le opzioni avanzate

La procedura seguente pubblica il progetto in una nuova app per le funzioni creata con opzioni di creazione avanzate:

1. Nel palette dei comandi immettere **Funzioni di Azure: Deploy to function app (Distribuisci nell'app per le funzioni).**

1. Se non è stato effettuato l'accesso, viene richiesto di **accedere ad Azure**. È anche possibile **creare un account Azure gratuito.** Dopo aver effettuato l'accesso dal browser, tornare a Visual Studio Code.

1. Se si hanno più sottoscrizioni, **selezionare una sottoscrizione per l'app** per le funzioni e quindi selezionare + Crea nuova app per le funzioni in **Azure... _Advanced_**. Questa _opzione_ Avanzata offre un maggiore controllo sulle risorse create in Azure. 

1. Dopo le istruzioni, specificare queste informazioni:

    | Prompt | Valore | Descrizione |
    | ------ | ----- | ----------- |
    | Selezionare l'app per le funzioni in Azure | Creare una nuova app per le funzioni in Azure | Al prompt successivo digitare un nome univoco globale che identifichi la nuova app per le funzioni e quindi premere INVIO. I caratteri validi per un nome di app per le funzioni sono `a-z`, `0-9` e `-`. |
    | Selezionare un sistema operativo | Windows | L'app per le funzioni viene eseguita in Windows. |
    | Selezionare un piano di hosting | Piano a consumo | Viene usato un hosting del [piano a consumo serverless.](consumption-plan.md) |
    | Selezionare un runtime per la nuova app | Linguaggio del progetto | Il runtime deve corrispondere al progetto che si sta pubblicando. |
    | Selezionare un gruppo di risorse per le nuove risorse | Creare un nuovo gruppo di risorse | Al prompt successivo digitare il nome di un gruppo di risorse, ad esempio `myResourceGroup` , e quindi premere INVIO. È anche possibile selezionare un gruppo di risorse esistente. |
    | Select a storage account (Selezionare un account di archiviazione) | Creare un nuovo account di archiviazione | Al prompt successivo digitare un nome univoco globale per il nuovo account di archiviazione usato dall'app per le funzioni e quindi premere INVIO. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È anche possibile selezionare un account esistente. |
    | Selezionare una località per le nuove risorse | region | Selezionare una posizione in [un'area nelle](https://azure.microsoft.com/regions/) vicinanze o vicino ad altri servizi a cui accedono le funzioni. |

    Dopo la creazione dell'app per le funzioni e l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>Ottenere l'URL di una funzione attivata tramite HTTP in Azure

Per chiamare una funzione attivata da HTTP da un client, è necessario l'URL della funzione quando viene distribuita nell'app per le funzioni. Questo URL include tutte le chiavi di funzione necessarie. È possibile usare l'estensione per ottenere questi URL per le funzioni distribuite. Se si vuole solo eseguire la funzione remota in Azure, [usare la funzionalità Esegui](#run-functions-in-azure) ora dell'estensione.

1. Selezionare F1 per aprire il riquadro comandi e quindi cercare ed eseguire il comando **Funzioni di Azure: Copia URL funzione**.

1. Seguire le istruzioni per selezionare l'app per le funzioni in Azure e quindi il trigger HTTP specifico che si vuole richiamare.

L'URL della funzione viene copiato negli Appunti, insieme alle chiavi necessarie passate dal parametro `code` di query . Usare uno strumento HTTP per inviare richieste POST o un browser per le richieste GET alla funzione remota.  

Quando si recupera l'URL delle funzioni in Azure, l'estensione usa l'account Azure per recuperare automaticamente le chiavi necessarie per avviare la funzione. [Altre informazioni sui tasti di scelta della funzione.](security-concepts.md#function-access-keys) L'avvio di funzioni non attivate da HTTP richiede l'uso della chiave amministratore.

## <a name="republish-project-files"></a>Ripubblicare i file di progetto

Quando si configura la [distribuzione continua,](functions-continuous-deployment.md)l'app per le funzioni in Azure viene aggiornata quando si aggiornano i file di origine nel percorso di origine connesso. È consigliabile eseguire la distribuzione continua, ma è anche possibile ripubblicare gli aggiornamenti dei file di progetto Visual Studio Code.

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Eseguire funzioni

L Funzioni di Azure'estensione consente di eseguire singole funzioni, nel progetto nel computer di sviluppo locale o nella sottoscrizione di Azure. 

Per le funzioni trigger HTTP, l'estensione chiama l'endpoint HTTP. Per altri tipi di trigger, chiama le API dell'amministratore per avviare la funzione. Il corpo del messaggio della richiesta inviata alla funzione dipende dal tipo di trigger. Quando un trigger richiede dati di test, viene richiesto di immettere i dati in un formato JSON specifico.

### <a name="run-functions-in-azure"></a>Eseguire funzioni in Azure

Per eseguire una funzione in Azure da Visual Studio Code. 

1. Nel palette dei comandi immettere **Funzioni di Azure: Esegui funzione adesso** e scegliere la sottoscrizione di Azure. 

1. Scegliere l'app per le funzioni in Azure dall'elenco. Se l'app per le funzioni non viene visualizzata, assicurarsi di aver eseguito l'accesso alla sottoscrizione corretta. 

1. Scegliere la funzione da eseguire nell'elenco e digitare il corpo del messaggio della richiesta in **Immettere il corpo della richiesta.** Premere INVIO per inviare questo messaggio di richiesta alla funzione. Il testo predefinito in **Enter request body (Immettere** il corpo della richiesta) deve indicare il formato del corpo. Se l'app per le funzioni non ha funzioni, viene visualizzato un errore di notifica con questo errore. 

1. Quando la funzione viene eseguita in Azure e restituisce una risposta, viene generata una notifica Visual Studio Code.
 
È anche possibile eseguire la funzione dall'area **Azure:** Funzioni facendo clic con il pulsante destro del mouse (CTRL+clic su Mac) sulla funzione che si vuole eseguire dall'app per le funzioni nella sottoscrizione di Azure e scegliendo Esegui funzione **ora...**.

Quando si eseguono funzioni in Azure, l'estensione usa l'account Azure per recuperare automaticamente le chiavi necessarie per avviare la funzione. [Altre informazioni sulle chiavi di accesso alle funzioni.](security-concepts.md#function-access-keys) L'avvio di funzioni attivate non HTTP richiede l'uso della chiave amministratore.

### <a name="run-functions-locally"></a>Eseguire funzioni localmente

Il runtime locale è lo stesso runtime che ospita l'app per le funzioni in Azure. Le impostazioni locali vengono lette dal [local.settings.jsnel file](#local-settings-file). Per eseguire il progetto Funzioni in locale, è necessario soddisfare [requisiti aggiuntivi.](#run-local-requirements)

#### <a name="configure-the-project-to-run-locally"></a>Configurare il progetto per l'esecuzione in locale

Il runtime di Funzioni usa un account Archiviazione di Azure internamente per tutti i tipi di trigger diversi da HTTP e webhook. È quindi necessario impostare la chiave **Values.AzureWebJobsStorage** su una stringa di connessione Archiviazione di Azure account.

Questa sezione usa [l'estensione Archiviazione di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) con [Azure Storage Explorer](https://storageexplorer.com/) per connettersi e recuperare la stringa di connessione di archiviazione.

Per impostare la stringa di connessione dell'account di archiviazione:

1. In Visual Studio aprire **Cloud Explorer,** espandere Account di archiviazione Account di archiviazione e quindi selezionare Proprietà e copiare il  >  valore Stringa di **connessione** primaria. 

2. Nel progetto aprire il file local.settings.json e impostare il valore della chiave **AzureWebJobsStorage** sulla stringa di connessione copiata.

3. Ripetere il passaggio precedente per aggiungere chiavi univoche alla matrice di **Valori** per tutte le altre connessioni richieste dalle funzioni.

Per altre informazioni, vedere [File di impostazioni locali](#local-settings-file).

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Eseguire il debug delle funzioni in locale  

Per eseguire il debug delle funzioni, selezionare F5. Se Core [Tools]non è ancora stato[scaricato Azure Functions Core Tools], viene richiesto di farlo. Quando Core Tools è installato e in esecuzione, l'output viene visualizzato nel terminale. Questa operazione è identica all'esecuzione del comando Core Tools dal terminale, ma con attività di compilazione aggiuntive `func host start` e un debugger collegato.  

Quando il progetto è in esecuzione, è possibile usare la funzionalità Esegui funzione **ora...** dell'estensione per attivare le funzioni come si farebbe quando il progetto viene distribuito in Azure. Con il progetto in esecuzione in modalità di debug, i punti di interruzione vengono Visual Studio Code come previsto. 

1. Nel gruppo di comandi immettere **Funzioni di Azure: Esegui funzione ora** e scegliere **Progetto locale.** 

1. Scegliere la funzione da eseguire nel progetto e digitare il corpo del messaggio della richiesta in **Immettere il corpo della richiesta.** Premere INVIO per inviare questo messaggio di richiesta alla funzione. Il testo predefinito in **Enter request body (Immettere** il corpo della richiesta) deve indicare il formato del corpo. Se l'app per le funzioni non ha funzioni, viene visualizzato un errore di notifica con questo errore. 

1. Quando la funzione viene eseguita in locale e dopo la ricezione della risposta, viene generata una notifica in Visual Studio Code. Le informazioni sull'esecuzione della funzione sono visualizzate nel **pannello Terminale.**

L'esecuzione di funzioni in locale non richiede l'uso di chiavi. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Per impostazione predefinita, non viene eseguita la migrazione automatica di queste impostazioni quando il progetto viene pubblicato in Azure. Al termine della pubblicazione, viene data la possibilità di pubblicare le impostazioni local.settings.jsnell'app per le funzioni in Azure. Per altre informazioni, vedere [Pubblicare le impostazioni dell'applicazione.](#publish-application-settings)

I valori in **ConnectionStrings** non vengono mai pubblicati.

I valori delle impostazioni dell'applicazione della funzione possono anche essere letti nel codice come variabili di ambiente. Per altre informazioni, vedere le sezioni Variabili di ambiente di questi articoli di riferimento specifici del linguaggio:

* [C# precompilato](functions-dotnet-class-library.md#environment-variables)
* [Script C# (file con estensione csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Impostazioni dell'applicazione in Azure

Le impostazioni nel local.settings.jsfile nel progetto devono essere le stesse delle impostazioni dell'applicazione nell'app per le funzioni in Azure. Tutte le impostazioni che si aggiungono local.settings.jsin è necessario aggiungere anche all'app per le funzioni in Azure. Queste impostazioni non vengono caricate automaticamente quando si pubblica il progetto. Analogamente, tutte le impostazioni create nell'app per le funzioni [nel portale](functions-how-to-use-azure-function-app-settings.md#settings) devono essere scaricate nel progetto locale.

### <a name="publish-application-settings"></a>Pubblicare le impostazioni dell'applicazione

Il modo più semplice per pubblicare le impostazioni necessarie nell'app per le funzioni in Azure è usare il collegamento Carica **impostazioni** visualizzato dopo la pubblicazione del progetto:

![Caricare le impostazioni dell'applicazione](./media/functions-develop-vs-code/upload-app-settings.png)

È anche possibile pubblicare le impostazioni usando **il comando Funzioni di Azure: Carica** impostazioni locali nel riquadro comandi. È possibile aggiungere singole impostazioni alle impostazioni dell'applicazione in Azure usando il **comando Funzioni di Azure: Aggiungi nuova** impostazione.

> [!TIP]
> Assicurarsi di salvare il local.settings.jsfile prima di pubblicarlo.

Se il file locale è crittografato, viene decrittografato, pubblicato e crittografato di nuovo. Se sono presenti impostazioni con valori in conflitto nelle due posizioni, viene chiesto di scegliere come procedere.

Visualizzare le impostazioni dell'app esistenti nell'area **Azure:** Funzioni espandendo la sottoscrizione, l'app per le funzioni e **le impostazioni dell'applicazione.**

![Visualizzare le impostazioni dell'app per le funzioni in Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Scaricare le impostazioni da Azure

Se sono state create impostazioni dell'applicazione in Azure, è possibile scaricarle nel local.settings.jssu file usando il comando **Funzioni di Azure: Scarica impostazioni** remote.

Come per il caricamento, se il file locale è crittografato, viene decrittografato, aggiornato e crittografato di nuovo. Se sono presenti impostazioni con valori in conflitto nelle due posizioni, viene chiesto di scegliere come procedere.

## <a name="monitoring-functions"></a>Monitoraggio delle funzioni

Quando si [eseguono funzioni in locale,](#run-functions-locally)i dati di log vengono trasmessi alla console terminale. È anche possibile ottenere dati di log quando il progetto Funzioni è in esecuzione in un'app per le funzioni in Azure. È possibile connettersi ai log di streaming in Azure per visualizzare i dati di log quasi in tempo reale oppure abilitare Application Insights per una comprensione più completa del comportamento dell'app per le funzioni.

### <a name="streaming-logs"></a>Streaming dei log

Quando si sviluppa un'applicazione, è spesso utile visualizzare le informazioni di registrazione quasi in tempo reale. È possibile visualizzare un flusso di file di log generati dalle funzioni. Questo output è un esempio di log di streaming per una richiesta a una funzione attivata tramite HTTP:

![Output dei log di streaming per il trigger HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Per altre informazioni, vedere [Streaming dei log.](functions-monitoring.md#streaming-logs)

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> I log di streaming supportano solo una singola istanza dell'host di Funzioni. Quando la funzione viene ridimensionata in più istanze, i dati di altre istanze non vengono visualizzati nel flusso di log. [Live Metrics Stream](../azure-monitor/app/live-stream.md) in Application Insights supporta più istanze. Anche in tempo quasi reale, l'analisi di flusso si basa [su dati campionati.](configure-monitoring.md#configure-sampling)

### <a name="application-insights"></a>Application Insights

È consigliabile monitorare l'esecuzione delle funzioni integrando l'app per le funzioni con Application Insights. Quando si crea un'app per le funzioni nel portale di Azure, questa integrazione viene eseguita per impostazione predefinita. Quando si crea l'app per le funzioni durante Visual Studio pubblicazione, è necessario integrare Application Insights manualmente. Per altre informazioni, vedere [Abilitare l Application Insights integra.](configure-monitoring.md#enable-application-insights-integration)

Per altre informazioni sul monitoraggio con Application Insights, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

## <a name="c-script-projects"></a>Progetti script C \#

Per impostazione predefinita, tutti i progetti C# vengono creati come [progetti di libreria di classi compilati in C#.](functions-dotnet-class-library.md) Se invece si preferisce usare i progetti di script C#, è necessario selezionare Script C# come linguaggio predefinito nelle impostazioni Funzioni di Azure'estensione:

1. Selezionare **File**  >  **Preferences**  >  Settings (Impostazioni preferenze **file).**

1. Passare a **User Settings**  >  **Extensions (Estensioni impostazioni**  >  **utente) Funzioni di Azure**.

1. Selezionare **C#Script** da **Funzione di Azure: Linguaggio del progetto.**

Dopo aver completato questi passaggi, le chiamate effettuate agli strumenti core sottostanti includono l'opzione , che genera e pubblica file di progetto di `--csx` script C# (con estensione csx). Quando è stato specificato questo linguaggio predefinito, tutti i progetti creati vengono predefiniti per i progetti di script C#. Non viene richiesto di scegliere un linguaggio del progetto quando è impostato un valore predefinito. Per creare progetti in altre lingue, è necessario modificare questa impostazione o rimuoverla dall'settings.jsnel file. Dopo aver rimosso questa impostazione, viene nuovamente richiesto di scegliere la lingua quando si crea un progetto.

## <a name="command-palette-reference"></a>Informazioni di riferimento sul riquadro comandi

L Funzioni di Azure'estensione offre un'interfaccia grafica utile nell'area per l'interazione con le app per le funzioni in Azure. La stessa funzionalità è disponibile anche come comandi nel riquadro comandi (F1). Sono Funzioni di Azure comandi seguenti:

|Funzioni di Azure comando  | Descrizione  |
|---------|---------|
|**Aggiungere nuove impostazioni**  |  Crea una nuova impostazione dell'applicazione in Azure. Per altre informazioni, vedere [Pubblicare le impostazioni dell'applicazione.](#publish-application-settings) Potrebbe anche essere necessario [scaricare questa impostazione nelle impostazioni locali.](#download-settings-from-azure) |
| **Configurare l'origine della distribuzione** | Connette l'app per le funzioni in Azure a un repository Git locale. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md). |
| **Connettersi al repository GitHub** | Connette l'app per le funzioni a un repository GitHub. |
| **Copiare l'URL della funzione** | Ottiene l'URL remoto di una funzione attivata da HTTP in esecuzione in Azure. Per altre informazioni, vedere [Ottenere l'URL della funzione distribuita.](#get-the-url-of-the-deployed-function) |
| **Creare un'app per le funzioni in Azure** | Crea una nuova app per le funzioni nella sottoscrizione in Azure. Per altre informazioni, vedere la sezione su come pubblicare [in una nuova app per le funzioni in Azure.](#publish-to-azure)        |
| **Decrittografa impostazioni** | [Decrittografa le](#local-settings-file) impostazioni locali crittografate da **Funzioni di Azure: Crittografa impostazioni**.  |
| **Eliminare l'app per le funzioni** | Rimuove un'app per le funzioni dalla sottoscrizione in Azure. Quando non sono presenti altre app nel piano di servizio app, è possibile eliminarla. Altre risorse, ad esempio gli account di archiviazione e i gruppi di risorse, non vengono eliminate. Per rimuovere tutte le risorse, è invece necessario [eliminare il gruppo di risorse](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Il progetto locale non è interessato. |
|**Funzione Delete**  | Rimuove una funzione esistente da un'app per le funzioni in Azure. Poiché questa eliminazione non influisce sul progetto locale, è consigliabile rimuovere la funzione in locale e quindi ripubblicare [il progetto](#republish-project-files). |
| **Eliminare il proxy** | Rimuove un proxy Funzioni di Azure dall'app per le funzioni in Azure. Per altre informazioni sui proxy, vedere [Usare Proxy di Funzioni di Azure](functions-proxies.md). |
| **Elimina impostazione** | Elimina un'impostazione dell'app per le funzioni in Azure. Questa eliminazione non influisce sulle impostazioni nel local.settings.jsfile. |
| **Disconnettersi dal repo**  | Rimuove la [connessione di distribuzione](functions-continuous-deployment.md) continua tra un'app per le funzioni in Azure e un repository del controllo del codice sorgente. |
| **Scaricare le impostazioni remote** | Scarica le impostazioni dall'app per le funzioni scelta in Azure nel local.settings.jsfile. Se il file locale è crittografato, viene decrittografato, aggiornato e crittografato di nuovo. Se sono presenti impostazioni con valori in conflitto nelle due posizioni, viene chiesto di scegliere come procedere. Assicurarsi di salvare le modifiche apportate al local.settings.jsfile prima di eseguire questo comando. |
| **Modificare le impostazioni** | Modifica il valore di un'impostazione dell'app per le funzioni esistente in Azure. Questo comando non influisce sulle impostazioni nel local.settings.jsfile.  |
| **Crittografare le impostazioni** | Crittografa singoli elementi `Values` nella matrice nelle impostazioni [locali.](#local-settings-file) In questo file è anche impostato su , che specifica che il runtime locale decrittograferà le `IsEncrypted` `true` impostazioni prima di usarle. Crittografare le impostazioni locali per ridurre il rischio di perdita di informazioni preziose. In Azure le impostazioni dell'applicazione vengono sempre archiviate crittografate. |
| **Esegui funzione ora** | Avvia manualmente una funzione usando le API di amministrazione. Questo comando viene usato per i test, sia in locale durante il debug che nelle funzioni in esecuzione in Azure. Quando si attiva una funzione in Azure, l'estensione ottiene prima automaticamente una chiave di amministrazione, che viene utilizzata per chiamare le API di amministrazione remota che avviano le funzioni in Azure. Il corpo del messaggio inviato all'API dipende dal tipo di trigger. I trigger timer non richiedono il passaggio di dati. |
| **Inizializzare il progetto per l'uso con VS Code** | Aggiunge i file di Visual Studio Code necessari a un progetto di Funzioni esistente. Usare questo comando per lavorare con un progetto creato con Core Tools. |
| **Installare o aggiornare Azure Functions Core Tools** | Installa o aggiorna [Azure Functions Core Tools], che viene usato per eseguire le funzioni in locale. |
| **Ripetere la distribuzione**  | Consente di ridistribuire i file di progetto da un repository Git connesso a una distribuzione specifica in Azure. Per ripubblicare gli aggiornamenti locali Visual Studio Code, [ripubblicare il progetto](#republish-project-files). |
| **Rinomina impostazioni** | Modifica il nome della chiave di un'impostazione dell'app per le funzioni esistente in Azure. Questo comando non influisce sulle impostazioni nel local.settings.jsfile. Dopo aver rinominato le impostazioni in Azure, è necessario [scaricare le modifiche nel progetto locale](#download-settings-from-azure). |
| **Riavvia** | Riavvia l'app per le funzioni in Azure. La distribuzione degli aggiornamenti riavvia anche l'app per le funzioni. |
| **Impostare AzureWebJobsStorage**| Imposta il valore `AzureWebJobsStorage` dell'impostazione dell'applicazione. Questa impostazione è necessaria per Funzioni di Azure. Viene impostato quando viene creata un'app per le funzioni in Azure. |
| **Inizia** | Avvia un'app per le funzioni arrestata in Azure. |
| **Avvia streaming dei log** | Avvia i log di streaming per l'app per le funzioni in Azure. Usare i log di streaming durante la risoluzione dei problemi remoti in Azure se è necessario visualizzare le informazioni di registrazione quasi in tempo reale. Per altre informazioni, vedere [Log di streaming](#streaming-logs). |
| **Stop** | Arresta un'app per le funzioni in esecuzione in Azure. |
| **Arrestare i log di streaming** | Arresta i log di streaming per l'app per le funzioni in Azure. |
| **Attiva/Disattiva come impostazione slot** | Se abilitata, garantisce che un'impostazione dell'applicazione sia persistente per un determinato slot di distribuzione. |
| **Disinstallare Azure Functions Core Tools** | Rimuove Azure Functions Core Tools, richiesto dall'estensione. |
| **Caricare le impostazioni locali** | Carica le impostazioni dal local.settings.jsnel file nell'app per le funzioni scelta in Azure. Se il file locale è crittografato, viene decrittografato, caricato e crittografato di nuovo. Se sono presenti impostazioni con valori in conflitto nelle due posizioni, viene chiesto di scegliere come procedere. Assicurarsi di salvare le modifiche apportate al local.settings.jsfile prima di eseguire questo comando. |
| **Visualizzare il commit in GitHub** | Mostra il commit più recente in una distribuzione specifica quando l'app per le funzioni è connessa a un repository. |
| **Visualizzare i log di distribuzione** | Mostra i log per una distribuzione specifica nell'app per le funzioni in Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui Azure Functions Core Tools, vedere [Usare Azure Functions Core Tools](functions-run-local.md).

Per altre informazioni sullo sviluppo di funzioni come librerie di classi .NET, vedere [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md). Questo articolo fornisce anche collegamenti ad esempi su come usare gli attributi per dichiarare i vari tipi di associazioni supportate da Funzioni di Azure.

[Estensione Funzioni di Azure per Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md