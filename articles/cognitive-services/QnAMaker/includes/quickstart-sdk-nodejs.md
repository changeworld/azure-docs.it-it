---
title: 'Avvio rapido: Libreria client di QnA Maker per Node.js'
description: Questo argomento di avvio rapido illustra come usare la libreria client di QnA Maker per Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: fba4354fb1aae19833790e166474008dc994cd79
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792366"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Usare la libreria client di QnA Maker per Node.js per eseguire queste operazioni:

* Creare una knowledge base
* Aggiornare una knowledge base
* Pubblicare una knowledge base
* Ottenere la chiave dell'endpoint di runtime di previsione
* Attendere un'attività a esecuzione prolungata
* Scaricare una knowledge base
* Ottenere una risposta da una knowledge base
* Eliminare una knowledge base

[Documentazione di riferimento](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Pacchetto (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Esempi Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Usare la libreria client di QnA Maker per Node.js per eseguire queste operazioni:

* Creare una knowledge base
* Aggiornare una knowledge base
* Pubblicare una knowledge base
* Attendere un'attività a esecuzione prolungata
* Scaricare una knowledge base
* Ottenere una risposta da una knowledge base
* Eliminare una knowledge base

[Documentazione di riferimento](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [Pacchetto (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Esempi Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Versione corrente di [Node.js](https://nodejs.org).
* Dopo aver creato la sottoscrizione di Azure, creare una [risorsa di QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) nel portale di Azure per ottenere la chiave di creazione e la risorsa. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e il nome della risorsa creata sono necessari per connettere l'applicazione all'API QnA Maker. La chiave e il nome della risorsa verranno incollati nel codice seguente più avanti in questo argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Versione corrente di [Node.js](https://nodejs.org).
* Dopo aver creato la sottoscrizione di Azure, creare una [risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) nel portale di Azure per ottenere la chiave di creazione e l'endpoint.
    * NOTA:  assicurarsi di selezionare la casella di controllo **Gestita**.
    * Dopo la distribuzione della risorsa QnA Maker, fare clic su **Vai alla risorsa**. La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API QnA Maker. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

---

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Eseguire il comando `npm init -y` per creare un'applicazione Node con un file `package.json`.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Installare la libreria client

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Installare i pacchetti NPM seguenti:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Installare i pacchetti NPM seguenti:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/ms-rest-js
```

---

Il file `package.json` dell'app viene aggiornato con le dipendenze.

Creare un file denominato index.js, aprirlo e importare le librerie seguenti:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Dependencies)]

---

Creare una variabile per la chiave di Azure della risorsa e il nome della risorsa.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

> [!IMPORTANT]
> Passare al portale di Azure e individuare la chiave e l'endpoint per la risorsa QnA Maker creata nei prerequisiti. Si trovano nella pagina **Chiave ed endpoint** della risorsa, in **Gestione risorse**.

- Creare variabili di ambiente denominate QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT e QNA_MAKER_RUNTIME_ENDPOINT per archiviare questi valori.
- Il formato del valore di QNA_MAKER_ENDPOINT è `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Il formato del valore di QNA_MAKER_RUNTIME_ENDPOINT è `https://YOUR-RESOURCE-NAME.azurewebsites.net`.
- Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Ad esempio, [Azure Key Vault](../../../key-vault/general/overview.md) fornisce una risorsa di archiviazione sicura per le chiavi.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

> [!IMPORTANT]
> Passare al portale di Azure e individuare la chiave e l'endpoint per la risorsa QnA Maker creata nei prerequisiti. Si trovano nella pagina **Chiave ed endpoint** della risorsa, in **Gestione risorse**.

- Creare variabili di ambiente denominate QNA_MAKER_SUBSCRIPTION_KEY e QNA_MAKER_ENDPOINT per archiviare questi valori.
- Il formato del valore di QNA_MAKER_ENDPOINT è `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Ad esempio, [Azure Key Vault](../../../key-vault/general/overview.md) fornisce una risorsa di archiviazione sicura per le chiavi.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Resourcevariables)]

---

## <a name="object-models"></a>Modelli a oggetti

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) usa due modelli a oggetti diversi:
* **[QnAMakerClient](#qnamakerclient-object-model)** è l'oggetto per creare, gestire, pubblicare e scaricare la knowledge base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** è l'oggetto per eseguire una query sulla knowledge base con l'API GenerateAnswer e inviare nuove domande suggerite usando l'API Train (come parte dell'[apprendimento attivo](../concepts/active-learning-suggestions.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[QnA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) usa il modello a oggetti seguente:
* **[QnAMakerClient](#qnamakerclient-object-model)** è l'oggetto per creare, gestire, pubblicare, scaricare ed eseguire query sulla knowledge base.

---

### <a name="qnamakerclient-object-model"></a>Modello a oggetti QnAMakerClient

Il client di creazione di QnA Maker è un oggetto [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) che esegue l'autenticazione con Azure usando le proprie credenziali, che contengono la chiave.

Dopo aver creato il client, usare [knowledgebase](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) per creare, gestire e pubblicare la knowledge base.

Gestire la knowledge base inviando un oggetto JSON. Per le operazioni immediate, un metodo restituisce in genere un oggetto JSON che indica lo stato. Per le operazioni a esecuzione prolungata, la risposta è l'ID operazione. Chiamare il metodo [client.operations.getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-) con l'ID operazione per determinare lo [stato della richiesta](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest).

### <a name="qnamakerruntimeclient-object-model"></a>Modello a oggetti QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Il client di previsione di QnA Maker è un oggetto QnAMakerRuntimeClient che esegue l'autenticazione con Azure tramite Microsoft.Rest.ServiceClientCredentials, che contiene la chiave di runtime di previsione, restituita dalla chiamata del client di creazione[client.EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-) dopo la pubblicazione della knowledge base.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Con una risorsa gestita di QnA Maker non è necessario usare l'oggetto QnAMakerRuntimeClient. Chiamare invece direttamente [generateAnswer](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#generateAnswer_string__QueryDTO__msRest_RequestOptionsBase_) sull'oggetto [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest).

---

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di QnA Maker per .NET:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

* [Autenticare il client di creazione](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Creare una knowledge base](#create-a-knowledge-base)
* [Aggiornare una knowledge base](#update-a-knowledge-base)
* [Scaricare una knowledge base](#download-a-knowledge-base)
* [Pubblicare una knowledge base](#publish-a-knowledge-base)
* [Eliminare una knowledge base](#delete-a-knowledge-base)
* [Ottenere una chiave di runtime di query](#get-query-runtime-key)
* [Ottenere lo stato di un'operazione](#get-status-of-an-operation)
* [Autenticare il client di runtime di query](#authenticate-the-runtime-for-generating-an-answer)
* [Generare una risposta dalla knowledge base](#generate-an-answer-from-the-knowledge-base)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

* [Autenticare il client di creazione](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Creare una knowledge base](#create-a-knowledge-base)
* [Aggiornare una knowledge base](#update-a-knowledge-base)
* [Scaricare una knowledge base](#download-a-knowledge-base)
* [Pubblicare una knowledge base](#publish-a-knowledge-base)
* [Eliminare una knowledge base](#delete-a-knowledge-base)
* [Ottenere lo stato di un'operazione](#get-status-of-an-operation)
* [Generare una risposta dalla knowledge base](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticare il client per la creazione della knowledge base

Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto ServiceClientCredentials con la chiave e usarlo con l'endpoint per creare un oggetto [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest).

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Creare una knowledge base

In una knowledge base vengono archiviate le coppie di domande e risposte per l'oggetto [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) da tre origini:

* Per **contenuto editoriale**, usare l'oggetto [QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest).
    * Per usare i metadati e le richieste di completamento, usare il contesto editoriale, perché questi dati vengono aggiunti a livello di singola coppia di domande e risposte.
* Per i **file**, usare l' oggetto [FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest). FileDTO include il nome file e l'URL pubblico per raggiungere il file.
* Per **URL**, usare un elenco di stringhe per rappresentare gli URL disponibili pubblicamente.

Il passaggio di creazione include anche le proprietà della knowledge base:
* `defaultAnswerUsedForExtraction`: il risultato restituito quando non si trova nessuna risposta
* `enableHierarchicalExtraction`: crea automaticamente relazioni di richieste tra coppie di domande e risposte estratte
* `language`: quando si crea la prima knowledge base di una risorsa, imposta la lingua da usare nell'indice di Ricerca di Azure.

Chiamare il metodo [create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) con le informazioni della knowledge base. Le informazioni della knowledge base sono essenzialmente un oggetto JSON.

Quando il metodo create viene completato, passare l'ID operazione restituito al metodo [wait_for_operation](#get-status-of-an-operation) per eseguire il polling dello stato. Il metodo wait_for_operation viene completato al termine dell'operazione. Analizzare il valore dell'intestazione `resourceLocation` dell'operazione restituita per ottenere il nuovo ID della knowledge base.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=CreateKBMethod)]

---

Per creare correttamente una knowledge base, assicurarsi di includere la funzione [`wait_for_operation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente.

## <a name="update-a-knowledge-base"></a>Aggiornare una knowledge base

È possibile aggiornare una knowledge base passando l'ID knowledge base e un oggetto [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) contenente gli oggetti DTO [add](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [update](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) e l'[delete](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) al metodo [update](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-). Anche gli oggetti DTO sono essenzialmente oggetti JSON. Usare il metodo [wait_for_operation](#get-status-of-an-operation) per determinare se l'aggiornamento è riuscito.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=UpdateKBMethod)]

---

Per aggiornare correttamente una knowledge base, assicurarsi di includere la funzione [`wait_for_operation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente.

## <a name="download-a-knowledge-base"></a>Scaricare una knowledge base

Usare il metodo [download](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-) per scaricare il database come elenco di [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest). Questa operazione _non_ equivale all'esportazione dalla pagina **Settings** (Impostazioni) del portale di QnA Maker perché il risultato di questo metodo non è un file TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

Pubblicare la knowledge base tramite il metodo [publish](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-). In questo modo, il modello corrente salvato e sottoposto a training a cui fa riferimento l'ID knowledge base viene pubblicato in un endpoint. Controllare il codice di risposta HTTP per verificare che la pubblicazione sia riuscita.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Eseguire query su una knowledge base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Ottenere una chiave di runtime di query

Dopo la pubblicazione di una knowledge base, è necessaria la chiave di runtime di query per eseguire una query sul runtime. Non è la stessa chiave usata per creare l'oggetto client originale.

Usare il metodo [EndpointKeys.getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest) per ottenere la classe [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest).

Usare una delle proprietà chiave restituite nell'oggetto per eseguire una query sulla knowledge base.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticare il runtime per la generazione di una risposta

Creare un oggetto QnAMakerRuntimeClient per eseguire una query sulla knowledge base e generare una risposta o eseguire il training da apprendimento attivo.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Usare QnAMakerRuntimeClient per ottenere una risposta dalla knowledge base o per inviare nuove domande suggerite alla knowledge base per l'[apprendimento attivo](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Generare una risposta dalla knowledge base

Generare una risposta da una knowledge base pubblicata usando il metodo RuntimeClient.runtime.generateAnswer. Questo metodo accetta l'ID knowledge base e [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Accedere ad altre proprietà di QueryDTO, ad esempio Top e Context da usare nel chatbot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Generare una risposta dalla knowledge base

Generare una risposta da una knowledge base pubblicata con il metodo QnAMakerClient.knowledgebase.generateAnswer. Questo metodo accetta l'ID knowledge base e [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Accedere ad altre proprietà di QueryDTO, ad esempio Top e Context da usare nel chatbot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=GenerateAnswer)]

---

Si tratta di un semplice esempio di esecuzione di query sulla knowledge base. Per informazioni sugli scenari di query avanzati, vedere [altri esempi di query](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Eliminare una knowledge base

Eliminare la knowledge base tramite il metodo [delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) con un parametro dell'ID knowledge base.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Ottenere lo stato di un'operazione

Alcuni metodi, ad esempio per la creazione e l'aggiornamento, possono richiedere una quantità di tempo tale che invece di attendere il completamento del processo viene restituita un'[operazione](/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest). Usare il relativo [ID operazione](/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) per eseguire il polling (con logica di ripetizione dei tentativi) e determinare lo stato del metodo originale.

La chiamata _delayTimer_ nel blocco di codice seguente viene usata per simulare la logica di ripetizione dei tentativi. Sostituirla con la propria logica di ripetizione dei tentativi.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node index.js` dalla directory dell'applicazione.

```console
node index.js
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js).

---