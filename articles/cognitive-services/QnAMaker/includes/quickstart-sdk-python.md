---
title: 'Avvio rapido: Libreria client di QnA Maker per Python'
description: Questa guida di avvio rapido illustra come usare la libreria client di QnA Maker per Python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 70d55bd6fba209119efa0d3828cad91c2de09f82
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792365"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Usare la libreria client di QnA Maker per Python per eseguire queste operazioni:

* Creare una knowledge base
* Aggiornare una knowledge base
* Pubblicare una knowledge base
* Ottenere la chiave dell'endpoint di runtime di previsione
* Attendere un'attività a esecuzione prolungata
* Scaricare una knowledge base
* Ottenere una risposta da una knowledge base
* Eliminare una knowledge base

[Documentazione di riferimento](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Pacchetto (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/0.2.0/) | [Esempi Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Usare la libreria client di QnA Maker per Python per eseguire queste operazioni:

* Creare una knowledge base
* Aggiornare una knowledge base
* Pubblicare una knowledge base
* Attendere un'attività a esecuzione prolungata
* Scaricare una knowledge base
* Ottenere una risposta da una knowledge base
* Eliminare una knowledge base

[Documentazione di riferimento](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [Pacchetto (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Esempi Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Dopo aver creato la sottoscrizione di Azure, creare una [risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) nel portale di Azure per ottenere la chiave di creazione e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API QnA Maker. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Dopo aver creato la sottoscrizione di Azure, creare una [risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) nel portale di Azure per ottenere la chiave di creazione e l'endpoint.
    * NOTA:  assicurarsi di selezionare la casella di controllo **Gestita**.
    * Dopo la distribuzione della risorsa QnA Maker, fare clic su **Vai alla risorsa**. La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API QnA Maker. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

---

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Dopo l'installazione di Python, è possibile installare la libreria client con:

```console
pip install azure-cognitiveservices-knowledge-qnamaker==0.2.0
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Dopo l'installazione di Python, è possibile installare la libreria client con:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

---

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo file Python denominato `quickstart-file.py` e importare le librerie seguenti.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Dependencies)]

---

Creare le variabili per l'endpoint e la chiave di Azure della risorsa.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

> [!IMPORTANT]
> Passare al portale di Azure e individuare la chiave e l'endpoint per la risorsa QnA Maker creata nei prerequisiti. Si trovano nella pagina **Chiave ed endpoint** della risorsa, in **Gestione risorse**.

- Creare variabili di ambiente denominate QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT e QNA_MAKER_RUNTIME_ENDPOINT per archiviare questi valori.
- Il formato del valore di QNA_MAKER_ENDPOINT è `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Il formato del valore di QNA_MAKER_RUNTIME_ENDPOINT è `https://YOUR-RESOURCE-NAME.azurewebsites.net`.
- Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Ad esempio, [Azure Key Vault](../../../key-vault/general/overview.md) fornisce una risorsa di archiviazione sicura per le chiavi.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

> [!IMPORTANT]
> Passare al portale di Azure e individuare la chiave e l'endpoint per la risorsa QnA Maker creata nei prerequisiti. Si trovano nella pagina **Chiave ed endpoint** della risorsa, in **Gestione risorse**.

- Creare variabili di ambiente denominate QNA_MAKER_SUBSCRIPTION_KEY e QNA_MAKER_ENDPOINT per archiviare questi valori.
- Il formato del valore di QNA_MAKER_ENDPOINT è `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. 
- Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Ad esempio, [Azure Key Vault](../../../key-vault/general/overview.md) fornisce una risorsa di archiviazione sicura per le chiavi.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=Resourcevariables)]

---

## <a name="object-models"></a>Modelli a oggetti

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) usa due modelli a oggetti diversi:
* **[QnAMakerClient](#qnamakerclient-object-model)** è l'oggetto per creare, gestire, pubblicare e scaricare la knowledge base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** è l'oggetto per eseguire una query sulla knowledge base con l'API GenerateAnswer e inviare nuove domande suggerite usando l'API Train (come parte dell'[apprendimento attivo](../concepts/active-learning-suggestions.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[QnA Maker](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) usa il modello a oggetti seguente:
* **[QnAMakerClient](#qnamakerclient-object-model)** è l'oggetto per creare, gestire, pubblicare, scaricare ed eseguire query sulla knowledge base.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modello a oggetti QnAMakerClient

Il client di creazione di QnA Maker è un oggetto [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python) che esegue l'autenticazione ad Azure con Microsoft.Rest.ServiceClientCredentials, che contiene la chiave.

Dopo la creazione del client, usare la proprietà [Knowledgebase](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python) per creare, gestire e pubblicare la knowledge base.

Gestire la knowledge base inviando un oggetto JSON. Per le operazioni immediate, un metodo restituisce in genere un oggetto JSON che indica lo stato. Per le operazioni a esecuzione prolungata, la risposta è l'ID operazione. Chiamare il metodo [operations.get_details](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#get-details-kb-id--custom-headers-none--raw-false----operation-config-) con l'ID operazione per determinare lo [stato della richiesta](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python).

### <a name="qnamakerruntimeclient-object-model"></a>Modello a oggetti QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Il client di previsione di QnA Maker è un oggetto `QnAMakerRuntimeClient` che esegue l'autenticazione con Azure tramite Microsoft.Rest.ServiceClientCredentials, che contiene la chiave di runtime di previsione, restituita dalla chiamata del client di creazione [client.EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpoint_keys_operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) dopo la pubblicazione della knowledge base.

Usare il metodo `generate_answer` per ottenere una risposta dal runtime di query.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Con una risorsa gestita di QnA Maker non è necessario usare l'oggetto QnAMakerRuntimeClient. Chiamare invece direttamente [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) sull'oggetto [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python).

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticare il client per la creazione della knowledge base

Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto CognitiveServicesCredentials con la chiave e usarlo con l'endpoint per creare un oggetto [QnAMakerClient](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qn_amaker_client.qnamakerclient?view=azure-python).

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Creare una knowledge base

Usare l'oggetto client per ottenere un oggetto [KnowledgeBaseOperations](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python).

In una knowledge base vengono archiviate le coppie di domande e risposte per l'oggetto [CreateKbDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) da tre origini:

* Per **contenuto editoriale**, usare l'oggetto [QnADTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python).
    * Per usare i metadati e le richieste di completamento, usare il contesto editoriale, perché questi dati vengono aggiunti a livello di singola coppia di domande e risposte.
* Per i **file**, usare l' oggetto [FileDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python). FileDTO include il nome file e l'URL pubblico per raggiungere il file.
* Per **URL**, usare un elenco di stringhe per rappresentare gli URL disponibili pubblicamente.

Chiamare il metodo [create](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) e quindi passare l'ID operazione restituito al metodo [Operations.getDetails](#get-status-of-an-operation) per eseguire il polling dello stato.

La riga finale del codice seguente restituisce l'ID knowledge base dalla risposta di MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=CreateKBMethod)]

---

Per creare correttamente una knowledge base, assicurarsi di includere la funzione [`_monitor_operation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente.

## <a name="update-a-knowledge-base"></a>Aggiornare una knowledge base

È possibile aggiornare una knowledge base passando l'ID knowledge base e un oggetto [UpdateKbOperationDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) contenente gli oggetti DTO [add](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python), [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python) e l'[delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) al metodo [update](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python). Usare il metodo [Operation.getDetail](#get-status-of-an-operation) per determinare se l'aggiornamento è riuscito.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=UpdateKBMethod)]

---

Per aggiornare correttamente una knowledge base, assicurarsi di includere la funzione [`_monitor_operation`](#get-status-of-an-operation), a cui si fa riferimento nel codice precedente.

## <a name="download-a-knowledge-base"></a>Scaricare una knowledge base

Usare il metodo [download](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) per scaricare il database come elenco di [QnADocumentsDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). Questa operazione _non_ equivale all'esportazione dalla pagina **Settings** (Impostazioni) del portale di QnA Maker perché il risultato di questo metodo non è un file TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

Pubblicare la knowledge base tramite il metodo [publish](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-). In questo modo, il modello corrente salvato e sottoposto a training a cui fa riferimento l'ID knowledge base viene pubblicato in un endpoint.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Eseguire query su una knowledge base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Ottenere una chiave di runtime di query

Dopo la pubblicazione di una knowledge base, è necessaria la chiave di runtime di query per eseguire una query sul runtime. Non è la stessa chiave usata per creare l'oggetto client originale.

Usare il metodo [EndpointKeysOperations.get_keys](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) per ottenere la classe [EndpointKeysDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-python).

Usare una delle proprietà chiave restituite nell'oggetto per eseguire una query sulla knowledge base.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autenticare il runtime per la generazione di una risposta

Creare un oggetto [QnAMakerRuntimeClient](/javascript/api/@azure/cognitiveservices-qnamaker-runtime/qnamakerruntimeclient?view=azure-node-latest) per eseguire una query sulla knowledge base e generare una risposta o eseguire il training da apprendimento attivo.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Usare QnAMakerRuntimeClient per ottenere una risposta dalla knowledge base o per inviare nuove domande suggerite alla knowledge base per l'[apprendimento attivo](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Generare una risposta dalla knowledge base

Generare una risposta da una knowledge base pubblicata usando il metodo QnAMakerRuntimeClient.runtime.generate_answer. Questo metodo accetta l'ID knowledge base e [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-python). Accedere ad altre proprietà di QueryDTO, ad esempio Top e Context da usare nel chatbot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Generare una risposta dalla knowledge base

Generare una risposta da una knowledge base pubblicata usando il metodo [generate_answer](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-). Questo metodo accetta l'ID knowledge base e [QueryDTO](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-python). Accedere ad altre proprietà di QueryDTO, ad esempio Top e Context da usare nel chatbot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=GenerateAnswer)]

---

Si tratta di un semplice esempio di esecuzione di query sulla knowledge base. Per informazioni sugli scenari di query avanzati, vedere [altri esempi di query](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Eliminare una knowledge base

Eliminare la knowledge base tramite il metodo [delete](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) con un parametro dell'ID knowledge base.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Ottenere lo stato di un'operazione

Alcuni metodi, ad esempio per la creazione e l'aggiornamento, possono richiedere una quantità di tempo tale che invece di attendere il completamento del processo viene restituita un'[operazione](/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation(class)?view=azure-python). Usare l'ID dell'operazione per eseguire il polling (con logica di ripetizione dei tentativi) in modo da determinare lo stato del metodo originale.

La chiamata _setTimeout_ nel blocco di codice seguente viene usata per simulare il codice asincrono. Sostituire questo codice con logica di ripetizione dei tentativi.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/preview-sdk/quickstart.py?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando python nel file quickstart.

```console
python quickstart-file.py
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/preview-sdk/quickstart.py).

---