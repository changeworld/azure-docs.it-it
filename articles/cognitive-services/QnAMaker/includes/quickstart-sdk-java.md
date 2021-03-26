---
title: 'Avvio rapido: Libreria client di QnA Maker per Java'
description: Questo argomento di avvio rapido illustra come usare la libreria client di QnA Maker per Java.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 755e4fcb9d5b9f1711bc615a29489037ea377d43
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609428"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Usare la libreria client di QnA Maker per Java per:

* Creare una knowledge base
* Aggiornare una knowledge base
* Pubblicare una knowledge base
* Ottenere la chiave dell'endpoint di runtime di previsione
* Attendere un'attività a esecuzione prolungata
* Scaricare una knowledge base
* Ottenere una risposta da una knowledge base
* Eliminare una knowledge base

[Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [Pacchetto](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.1) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/quickstart.java)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Usare la libreria client di QnA Maker per Java per:

* Creare una knowledge base
* Aggiornare una knowledge base
* Pubblicare una knowledge base
* Attendere un'attività a esecuzione prolungata
* Scaricare una knowledge base
* Ottenere una risposta da una knowledge base
* Eliminare una knowledge base

[Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker) | [Pacchetto](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker/1.0.0-beta.2) | [Esempi](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/qnamaker/sdk/preview-sdk/quickstart.java)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Prerequisiti

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Dopo aver creato la sottoscrizione di Azure, creare una [risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) nel portale di Azure per ottenere la chiave di creazione e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API QnA Maker. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* [JDK](https://www.oracle.com/java/technologies/javase-downloads.html)
* Dopo aver creato la sottoscrizione di Azure, creare una [risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) nel portale di Azure per ottenere la chiave di creazione e l'endpoint.
    * NOTA:  assicurarsi di selezionare la casella di controllo **Gestita**.
    * Dopo la distribuzione della risorsa QnA Maker, fare clic su **Vai alla risorsa**. La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API QnA Maker. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

---

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-libraries"></a>Installare le librerie client

Dopo aver installato Java, è possibile installare le librerie client usando [Maven](https://maven.apache.org/) da [repository MVN](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-qnamaker).

### <a name="create-a-new-java-application"></a>Creare una nuova applicazione Java

Creare un nuovo file denominato `quickstart.java` e importare le librerie seguenti.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-java[Dependencies](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=dependencies)]

---

Creare le variabili per l'endpoint e la chiave di Azure della risorsa.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

- Si usano la chiave di sottoscrizione e la chiave di creazione e modifica. Per ulteriori informazioni sulla creazione di una chiave, seguire le [chiavi in QnA Maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- Il formato del valore di QNA_MAKER_ENDPOINT è `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Passare alla portale di Azure e trovare la risorsa QnA Maker creata nei prerequisiti. Fare clic su **chiavi ed endpoint** pagina, in **Gestione risorse** per individuare la chiave di creazione (sottoscrizione) e QnA Maker endpoint.

 ![QnA Maker endpoint di creazione](../media/keys-endpoint.png)

- Il formato del valore di QNA_MAKER_RUNTIME_ENDPOINT è `https://YOUR-RESOURCE-NAME.azurewebsites.net`. Passare alla portale di Azure e trovare la risorsa QnA Maker creata nei prerequisiti. Fare clic sulla pagina **Esporta modello** in **automazione** per individuare l'endpoint di Runtime.

 ![Endpoint di runtime QnA Maker](../media/runtime-endpoint.png)
   
- Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Ad esempio, [Azure Key Vault](../../../key-vault/general/overview.md) fornisce una risorsa di archiviazione sicura per le chiavi.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=resourceKeys)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

- Si usano la chiave di sottoscrizione e la chiave di creazione e modifica. Per ulteriori informazioni sulla creazione di una chiave, seguire le [chiavi in QnA Maker](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- Il formato del valore di QNA_MAKER_ENDPOINT è `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`. Passare alla portale di Azure e trovare la risorsa QnA Maker creata nei prerequisiti. Fare clic su **chiavi ed endpoint** pagina, in **Gestione risorse** per individuare la chiave di creazione (sottoscrizione) e QnA Maker endpoint.

 ![QnA Maker endpoint di creazione](../media/keys-endpoint.png)
 
- Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Ad esempio, [Azure Key Vault](../../../key-vault/general/overview.md) fornisce una risorsa di archiviazione sicura per le chiavi.

[!code-java[Resource variables](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=resourceKeys)]

---

## <a name="object-models"></a>Modelli a oggetti

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

QnA Maker usa due modelli a oggetti diversi:
* **[QnAMakerClient](#qnamakerclient-object-model)** è l'oggetto per creare, gestire, pubblicare e scaricare la knowledge base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** è l'oggetto per eseguire una query sulla knowledge base con l'API GenerateAnswer e inviare nuove domande suggerite usando l'API Train (come parte dell'[apprendimento attivo](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

QnA Maker usa il modello a oggetti seguente:
* **[QnAMakerClient](#qnamakerclient-object-model)** è l'oggetto per creare, gestire, pubblicare, scaricare ed eseguire query sulla knowledge base.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>Modello a oggetti QnAMakerClient

Il client di creazione di QnA Maker è un oggetto [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java) che esegue l'autenticazione con Azure tramite MsRest::ServiceClientCredentials, che contiene la chiave.

Dopo la creazione del client, usare i metodi della proprietà [Knowledgebases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java) del client per creare, gestire e pubblicare la knowledge base.

Per le operazioni immediate, un metodo restituisce in genere il risultato, se disponibile. Per le operazioni a esecuzione prolungata, la risposta è un oggetto [Operation](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java). Chiamare il metodo [getDetails](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Operations.java#L32) con il valore `operation.operationId` per determinare lo [stato della richiesta](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/OperationStateType.java).

### <a name="qnamakerruntimeclient-object-model"></a>Modello a oggetti QnAMakerRuntimeClient

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Il client di runtime di QnA Maker è un oggetto [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java).

Dopo aver pubblicato la knowledge base con il client di creazione, usare il metodo [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36) del client di runtime per ottenere una risposta dalla knowledge base.

Per creare un client di runtime, chiamare [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) e passare una chiave dell'endpoint di runtime. Per ottenere la chiave dell'endpoint di runtime, usare il client di creazione per chiamare [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Con una risorsa gestita di QnA Maker non è necessario usare l'oggetto QnAMakerRuntimeClient. Chiamare invece direttamente [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308) sull'oggetto [QnAMakerClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerClient.java).

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autenticare il client per la creazione della knowledge base

Creare un'istanza di un client con l'endpoint di creazione e la chiave di sottoscrizione.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=authenticate)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-java[Authenticate](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=authenticate)]

---

## <a name="create-a-knowledge-base"></a>Creare una knowledge base

In una knowledge base vengono archiviate le coppie di domande e risposte per l'oggetto [CreateKbDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/CreateKbDTO.java) da tre origini:

* Per **contenuto editoriale**, usare l'oggetto [QnADTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADTO.java).
    * Per usare i metadati e le richieste di completamento, usare il contesto editoriale, perché questi dati vengono aggiunti a livello di singola coppia di domande e risposte.
* Per i **file**, usare l' oggetto [FileDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/FileDTO.java). FileDTO include il nome file e l'URL pubblico per raggiungere il file.
* Per **URL**, usare un elenco di stringhe per rappresentare gli URL disponibili pubblicamente.

Chiamare il metodo [create](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L173), quindi passare la proprietà `operationId` dell'operazione restituita al metodo [getDetails](#get-status-of-an-operation) per eseguire il polling dello stato.

La riga finale del codice seguente restituisce l'ID knowledge base.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=createKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-java[Create knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=createKb)]

---

## <a name="update-a-knowledge-base"></a>Aggiornare una knowledge base

Per aggiornare una knowledge base, chiamare [update](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L150) e passare l'ID Knowledge base e un oggetto [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTO.java). Tale oggetto può a sua volta contenere:
- [add](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOAdd.java)
- [update](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTOUpdate.java)
- [delete](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/UpdateKbOperationDTODelete.java)

Passare la proprietà `operationId` dell'operazione restituita al metodo [getDetails](#get-status-of-an-operation) per eseguire il polling dello stato.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=updateKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-java[Update knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=updateKb)]

---

## <a name="download-a-knowledge-base"></a>Scaricare una knowledge base

Usare il metodo [download](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196) per scaricare il database come elenco di [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QnADocumentsDTO.java). Questa operazione _non_ equivale all'esportazione dalla pagina **Settings** (Impostazioni) del portale di QnA Maker perché il risultato di questo metodo non è un file TSV.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=downloadKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-java[Download knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=downloadKb)]

---

## <a name="publish-a-knowledge-base"></a>Pubblicare una knowledge base

Pubblicare la knowledge base tramite il metodo [publish](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L196). In questo modo, il modello corrente salvato e sottoposto a training a cui fa riferimento l'ID knowledge base viene pubblicato in un endpoint.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=publishKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-java[Publish knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=publishKb)]

---

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generare una risposta dalla knowledge base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Dopo aver pubblicato una knowledge base, per eseguire query è necessario avere la chiave dell'endpoint di runtime, che non corrisponde alla chiave di sottoscrizione usata per creare il client di creazione.

Usare il metodo [getKeys](https://github.com/Azure/azure-sdk-for-java/blob/b637366e32edefb0fe63962983715a02c1ad2631/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/EndpointKeys.java#L30) per ottenere un oggetto [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/EndpointKeysDTO.java).

Per creare un client di runtime, chiamare [QnAMakerRuntimeManager.authenticate](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/QnAMakerRuntimeManager.java#L29) e passare una chiave dell'endpoint di runtime dell'oggetto EndpointKeysDTO.

Generare una risposta da una knowledge base pubblicata usando il metodo [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Runtimes.java#L36). Questo metodo accetta l'ID knowledge base e un oggetto [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java).

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=queryKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Generare una risposta da una knowledge base pubblicata usando il metodo [generateAnswer](https://github.com/Azure/azure-sdk-for-java/blob/657e9a47e4b4c7e7e7eee4100273c09468a30c63/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L308). Questo metodo accetta l'ID knowledge base e un oggetto [QueryDTO](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/QueryDTO.java).

[!code-java[Query knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=queryKb)]

---

Questo è un semplice esempio di esecuzione di query su una knowledge base. Per informazioni sugli scenari di query avanzati, vedere [altri esempi di query](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Eliminare una knowledge base

Eliminare la knowledge base tramite il metodo [delete](https://github.com/Azure/azure-sdk-for-java/blob/b455a61f4c6daece13590a0f4136bab3c4f30546/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/Knowledgebases.java#L81) con un parametro dell'ID knowledge base.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=deleteKb)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-java[Delete knowledgebase](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=deleteKb)]

---

## <a name="get-status-of-an-operation"></a>Ottenere lo stato di un'operazione

Alcuni metodi, ad esempio per la creazione e l'aggiornamento, possono richiedere una quantità di tempo tale che invece di attendere il completamento del processo viene restituita un'[operazione](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cognitiveservices/ms-azure-cs-qnamaker/src/main/java/com/microsoft/azure/cognitiveservices/knowledge/qnamaker/models/Operation.java). Usare l'ID dell'operazione per eseguire il polling (con logica di ripetizione dei tentativi) in modo da determinare lo stato del metodo originale.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=waitForOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-java[Wait for operation](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=waitForOperation)]

---

## <a name="run-the-application"></a>Eseguire l'applicazione

Ecco il metodo main per l'applicazione.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/quickstart.java?name=main)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

[!code-java[Main method](~/cognitive-services-quickstart-code/java/qnamaker/sdk/preview-sdk/quickstart.java?name=main)]

---

Eseguire l'applicazione come indicato di seguito. Si presuppone che il nome della classe sia `Quickstart` e che le dipendenze si trovino in una sottocartella denominata `lib` sotto la cartella corrente.

```console
javac Quickstart.java -cp .;lib\*
java -cp .;lib\* Quickstart
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/version-1)

Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/quickstart.java).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/version-2)

Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/qnamaker/sdk/preview-sdk/quickstart.java).

---
