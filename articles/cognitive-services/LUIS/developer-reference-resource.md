---
title: Risorse per sviluppatori - Language Understanding
description: SDK, API REST e interfaccia della riga di comando consentono di sviluppare app Language Understanding (LUIS) nel linguaggio di programmazione. Gestire le risorse di Azure e le stime LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 04c7d4a7e725d99c7dba94779d365312f8b960af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787108"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Risorse per sviluppatori SDK, REST e CLI per Language Understanding (LUIS)

SDK, API REST e interfaccia della riga di comando consentono di sviluppare app Language Understanding (LUIS) nel linguaggio di programmazione. Gestire le risorse di Azure e le stime LUIS.

## <a name="azure-resource-management"></a>Gestione delle risorse di Azure

Usare il Servizi cognitivi di Azure Management per creare, modificare, elencare ed eliminare la risorsa Language Understanding o servizio cognitivo.

Trovare la documentazione di riferimento basata sullo strumento:

* [Interfaccia della riga di comando di Azure](/cli/azure/cognitiveservices#az_cognitiveservices_list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding di creazione e stima

Il Language Understanding è accessibile da una risorsa di Azure che è necessario creare. Sono disponibili due risorse:

* Usare la **risorsa di creazione** per il training per creare, modificare, eseguire il training e pubblicare.
* Usare la **stima per** il runtime per inviare il testo dell'utente e ricevere una stima.

Informazioni [sull'endpoint di stima V3.](luis-migration-api-v3.md)

Usare [il codice di esempio di](https://github.com/Azure-Samples/cognitive-services-quickstart-code) Servizi cognitivi per apprendere e usare le attività più comuni.

### <a name="rest-specifications"></a>Specifiche REST

Le [specifiche REST di LUIS,](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)insieme a tutte le specifiche REST di [Azure,](https://github.com/Azure/azure-rest-api-specs)sono disponibili pubblicamente su GitHub.

### <a name="rest-apis"></a>API REST

Sia le API degli endpoint di creazione che di stima sono disponibili dalle API REST:

|Type|Versione|
|--|--|
|Creazione|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[anteprima V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Stima|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Endpoint REST

LUIS dispone attualmente di 2 tipi di endpoint:

* **creazione nell'endpoint** di training
* stima **delle query** nell'endpoint di runtime.

|Scopo|URL|
|--|--|
|Creazione V2 nell'endpoint di training|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Creazione di V3 nell'endpoint di training|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|Stima V2: tutte le stime sull'endpoint di runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Stima V3 : stima delle versioni nell'endpoint di runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Stima V3 : stima dello slot nell'endpoint di runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

La tabella seguente illustra i parametri indicati con parentesi graffe `{}` nella tabella precedente.

|Parametro|Scopo|
|--|--|
|`your-resource-name`|Nome della risorsa di Azure|
|`q` o `query`|testo dell'espressione inviato dall'applicazione client, ad esempio chatbot|
|`version`|Nome della versione di 10 caratteri|
|`slot`| `production` o `staging`|

### <a name="rest-query-string-parameters"></a>Parametri della stringa di query REST

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Schema dell'app

Lo [schema dell'app](app-schema-definition.md) viene importato ed esportato in `.json` un formato o `.lu` .

### <a name="language-based-sdks"></a>SDK basati sul linguaggio

|Linguaggio |Documentazione di riferimento|Pacchetto|Avvi rapidi|
|--|--|--|--|
|C#|[Creazione](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Stima](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[Creazione di NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Stima NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Creazione](./client-libraries-rest-api.md?pivots=rest-api)<br>[Stima delle query](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Creazione e stima](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Creazione e stima](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Creazione di Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Stima di Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Creazione](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Stima](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[Creazione di NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Stima NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Creazione](./client-libraries-rest-api.md?pivots=rest-api)<br>[Stima](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Creazione e stima](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Creazione](./client-libraries-rest-api.md?pivots=rest-api)<br>[Stima](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Contenitori

Language Understanding (LUIS) fornisce [un](luis-container-howto.md) contenitore per fornire versioni locali e indipendenti dell'app.

### <a name="export-and-import-formats"></a>Esportare e importare formati

Language Understanding offre la possibilità di gestire l'app e i relativi modelli in formato JSON, il formato ( LUDown ) e un pacchetto compresso per il contenitore `.LU` Language Understanding contenitore.[](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)

L'importazione e l'esportazione di questi formati sono disponibili dalle API e dal portale LUIS. Il portale fornisce l'importazione e l'esportazione come parte dell'elenco App e dell'elenco Versioni.

## <a name="workshops"></a>Workshop

* GitHub: (Workshop) Conversational-AI: NLU using LUIS (GitHub: (Workshop) [Conversational-AI : NLU using LUIS (GitHub: (Workshop) Conversational-AI : NLU using LUIS](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Strumenti di integrazione continua

* GitHub: (anteprima) [Sviluppo di un'app LUIS con procedure DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps: strumenti](https://github.com/microsoft/NLU.DevOps) che supportano l'integrazione e la distribuzione continue per i servizi NLU.

## <a name="bot-framework-tools"></a>Bot Framework strumenti

Il framework bot è disponibile [come SDK](https://github.com/Microsoft/botframework) in un'ampia gamma di linguaggi e come servizio [usando servizio Azure Bot](https://dev.botframework.com/).

Bot Framework offre [diversi strumenti per](https://github.com/microsoft/botbuilder-tools) facilitare l'Language Understanding, tra cui:
* [Bot Framework emulatore:](https://github.com/Microsoft/BotFramework-Emulator/releases) un'applicazione desktop che consente agli sviluppatori di bot di testare ed eseguire il debug di bot compilati con Bot Framework SDK
* [Bot Framework Composer:](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) uno strumento di sviluppo integrato per sviluppatori e team multidisciplinari per creare bot ed esperienze di conversazione con il Microsoft Bot Framework
* [Bot Framework seguenti:](https://github.com/microsoft/botbuilder-samples) in #C, JavaScript, TypeScript e Python

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui codici di [errore HTTP comuni](luis-reference-response-codes.md)
* [Documentazione di](../../index.yml) riferimento per tutte le API e gli SDK
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) e [servizio Azure Bot](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Contenitori cognitivi](../cognitive-services-container-support.md)
