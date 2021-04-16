---
title: Usare le API e gli SDK di Gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Informazioni su come usare le API Gemelli digitali di Azure, anche tramite SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: efa5061a49978ed5e7766c0e7bf9b56a1e73cf5d
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389758"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Usare le API e gli SDK di Gemelli digitali di Azure

Gemelli digitali di Azure è dotato sia delle **API** del piano di controllo che delle **API** del piano dati per la gestione dell'istanza e dei relativi elementi. 
* Le API del piano di controllo [Azure Resource Manager API arm (ARM)](../azure-resource-manager/management/overview.md) e coprono le operazioni di gestione delle risorse, ad esempio la creazione e l'eliminazione dell'istanza. 
* Le API del piano dati sono Gemelli digitali di Azure API e vengono usate per operazioni di gestione dei dati, ad esempio la gestione di modelli, gemelli e il grafo.

Questo articolo offre una panoramica delle API disponibili e dei metodi per interagire con esse. È possibile usare le API REST direttamente con gli Swagger associati (tramite uno strumento come [Postman)](how-to-use-postman.md)o tramite un SDK.

## <a name="overview-control-plane-apis"></a>Panoramica: API del piano di controllo

Le API del piano [](../azure-resource-manager/management/overview.md) di controllo sono API ARM usate per gestire l'istanza di Gemelli digitali di Azure nel suo complesso, quindi coprono operazioni come la creazione o l'eliminazione dell'intera istanza. Verranno usate anche per creare ed eliminare endpoint.

La versione più recente dell'API del piano di controllo _**è 2020-12-01.**_

Per usare le API del piano di controllo:
* È possibile chiamare direttamente le API facendo riferimento alla cartella Swagger più recente nel piano di controllo [Swagger repo](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Questa cartella include anche una cartella di esempi che illustrano l'utilizzo.
* È attualmente possibile accedere agli SDK per le API di controllo in ...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([riferimento [generato automaticamente]](/dotnet/api/overview/azure/digitaltwins/management)) ([source](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([riferimento [generato automaticamente]](/java/api/overview/azure/digitaltwins)) ([source](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([source](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([source](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([source](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

È anche possibile usare le API del piano di controllo interagendo con Gemelli digitali di Azure tramite l'interfaccia portale di Azure [e](https://portal.azure.com) l'interfaccia della riga [di comando.](how-to-use-cli.md)

## <a name="overview-data-plane-apis"></a>Panoramica: API del piano dati

Le API del piano dati sono le API Gemelli digitali di Azure usate per gestire gli elementi all'interno dell'istanza Gemelli digitali di Azure dati. Includono operazioni come la creazione di route, il caricamento di modelli, la creazione di relazioni e la gestione dei gemelli. Possono essere suddivisi in modo ampio nelle categorie seguenti:
* **DigitalTwinModels:** la categoria DigitalTwinModels contiene le API per gestire i modelli [in](concepts-models.md) un'Gemelli digitali di Azure istanza. Le attività di gestione includono caricamento, convalida, recupero ed eliminazione dei modelli creati in DTDL.
* **DigitalTwins:** la categoria DigitalTwins contiene le API che consentono [](concepts-twins-graph.md) agli sviluppatori di creare, modificare ed eliminare gemelli digitali e le relative relazioni in un Gemelli digitali di Azure istanza.
* **Query:** la categoria Query consente agli [sviluppatori di trovare set di gemelli digitali nel grafo dei gemelli](how-to-query-graph.md) tra le relazioni.
* **Route di** eventi: la categoria Route eventi contiene LE API per [instradare](concepts-route-events.md)i dati , attraverso il sistema e verso i servizi downstream.

La versione più recente dell'API del piano dati _**è 2020-10-31.**_

Per usare le API del piano dati:
* È possibile chiamare le API direttamente, tramite...
   - che fa riferimento alla cartella Swagger più recente nel [repo Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)del piano dati. Questa cartella include anche una cartella di esempi che illustrano l'utilizzo. 
   - visualizzazione della documentazione [di riferimento dell'API](/rest/api/azure-digitaltwins/).
* È possibile usare **.NET (C#) SDK.** Per usare .NET SDK...
   - È possibile visualizzare e aggiungere il pacchetto da NuGet: [Azure.DigitalTwins.Core.](https://www.nuget.org/packages/Azure.DigitalTwins.Core) 
   - È possibile visualizzare la documentazione [di riferimento dell'SDK](/dotnet/api/overview/azure/digitaltwins/client).
   - È possibile trovare l'origine SDK, inclusa una cartella di esempi, in GitHub: Azure IoT client di Gemelli digitali [per .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) 
   - È possibile visualizzare informazioni dettagliate ed esempi di utilizzo continuando con la sezione [*.NET (C#) SDK (piano dati)*](#net-c-sdk-data-plane) di questo articolo.
* È possibile usare **Java SDK**. Per usare Java SDK...
   - È possibile visualizzare e installare il pacchetto da Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - è possibile visualizzare la documentazione [di riferimento dell'SDK](/java/api/overview/azure/digitaltwins/client)
   - È possibile trovare l'origine SDK in GitHub: [Azure IoT libreria client di Gemelli digitali per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* È possibile usare **JavaScript SDK**. Per usare JavaScript SDK...
   - è possibile visualizzare e installare il pacchetto da npm: [libreria client di Azure Gemelli digitali di Azure Core per JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - è possibile visualizzare la documentazione [di riferimento dell'SDK](/javascript/api/@azure/digital-twins-core/).
   - È possibile trovare l'origine SDK in GitHub: [Libreria client di Azure Gemelli digitali di Azure Core per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* È possibile usare **Python SDK**. Per usare Python SDK...
   - è possibile visualizzare e installare il pacchetto da PyPi: [Libreria client di Azure Gemelli digitali di Azure Core per Python](https://pypi.org/project/azure-digitaltwins-core/).
   - è possibile visualizzare la documentazione [di riferimento dell'SDK](/python/api/azure-digitaltwins-core/azure.digitaltwins.core).
   - È possibile trovare l'origine SDK in GitHub: [Libreria client di Azure Gemelli digitali di Azure Core per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* È possibile generare un SDK per un altro linguaggio usando AutoRest. Seguire le istruzioni in Procedura: Creare SDK personalizzati per Gemelli digitali di Azure [*con AutoRest*](how-to-create-custom-sdks.md).

È anche possibile usare le API del piano data interagendo con Gemelli digitali di Azure tramite l'interfaccia della [riga di comando.](how-to-use-cli.md)

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (piano dati)

L Gemelli digitali di Azure SDK .NET (C#) fa parte di Azure SDK per .NET. È open source e si basa sulle API Gemelli digitali di Azure del piano dati.

> [!NOTE]
> Per altre informazioni sulla progettazione dell'SDK, vedere i principi generali di progettazione per gli SDK di [Azure](https://azure.github.io/azure-sdk/general_introduction.html) e le linee guida di progettazione [.NET specifiche.](https://azure.github.io/azure-sdk/dotnet_introduction.html)

Per usare l'SDK, includere il pacchetto NuGet **Azure.DigitalTwins.Core** con il progetto. Sarà anche necessaria la versione più recente del **pacchetto Azure.Identity.** In Visual Studio, è possibile aggiungere questi pacchetti usando il Gestione pacchetti NuGet (accessibile tramite Strumenti *> NuGet Gestione pacchetti > Gestire pacchetti NuGet per la soluzione*). In alternativa, è possibile usare lo strumento da riga di comando .NET con i comandi disponibili nei collegamenti al pacchetto NuGet seguenti per aggiungerli al progetto:
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Questo è il pacchetto per l'[SDK Gemelli digitali di Azure per .NET](/dotnet/api/overview/azure/digitaltwins/client). 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Questa libreria fornisce strumenti che consentono di eseguire l'autenticazione in Azure.

Per una procedura dettagliata sull'uso delle API nella pratica, vedere [*Esercitazione: Codificare un'app client*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Esempi di utilizzo di .NET SDK

Ecco alcuni esempi di codice che illustrano l'uso di .NET SDK.

Eseguire l'autenticazione nel servizio:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Caricare un modello:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Elencare i modelli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Creare gemelli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Eseguire query sui gemelli e scorrere i risultati:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Vedere [*Esercitazione: Codificare un'app client*](tutorial-code.md) per una procedura dettagliata di questo codice dell'app di esempio. 

È anche possibile trovare altri esempi nel [repository GitHub per .NET (C#) SDK.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)

#### <a name="serialization-helpers"></a>Helper di serializzazione

Gli helper di serializzazione sono funzioni helper disponibili all'interno dell'SDK per la creazione o la deserializzazione rapida dei dati gemelli per l'accesso alle informazioni di base. Poiché i metodi SDK principali restituiscono dati gemelli come JSON per impostazione predefinita, può essere utile usare queste classi helper per suddividere ulteriormente i dati gemelli.

Le classi helper disponibili sono:
* `BasicDigitalTwin`: rappresenta in modo generico i dati di base di un gemello digitale
* `BasicDigitalTwinComponent`: rappresenta in modo generico un componente nelle `Contents` proprietà di un oggetto `BasicDigitalTwin`
* `BasicRelationship`: rappresenta in modo generico i dati principali di una relazione
* `DigitalTwinsJsonPropertyName`: contiene le costanti stringa da usare nella serializzazione e deserializzazione JSON per i tipi di gemelli digitali personalizzati

##### <a name="deserialize-a-digital-twin"></a>Deserializzare un gemello digitale

È sempre possibile deserializzare i dati gemelli usando la libreria JSON di propria scelta, ad `System.Text.Json` esempio o `Newtonsoft.Json` . Per l'accesso di base a un gemello, le classi helper possono rendere questa operazione più pratica.

La `BasicDigitalTwin` classe helper consente anche di accedere alle proprietà definite nel dispositivo gemello, tramite un oggetto `Dictionary<string, object>` . Per elencare le proprietà del gemello, è possibile usare:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` usa `System.Text.Json` attributi. Per usare con `BasicDigitalTwin` [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true), è necessario inizializzare il client con il costruttore predefinito oppure, se si vuole personalizzare l'opzione serializer, usare [JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true).

##### <a name="create-a-digital-twin"></a>Creare un gemello digitale

Usando la `BasicDigitalTwin` classe , è possibile preparare i dati per la creazione di un'istanza del dispositivo gemello:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Il codice precedente equivale alla variante "manuale" seguente:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Deserializzare una relazione

È sempre possibile deserializzare i dati della relazione in un tipo di propria scelta. Per l'accesso di base a una relazione, usare il tipo `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

La `BasicRelationship` classe helper consente anche di accedere alle proprietà definite nella relazione, tramite un oggetto `IDictionary<string, object>` . Per elencare le proprietà, è possibile usare:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Creare una relazione

Usando la `BasicRelationship` classe , è anche possibile preparare i dati per la creazione di relazioni in un'istanza gemella:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Creare una patch per l'aggiornamento gemello

Le chiamate di aggiornamento per i gemelli e le relazioni usano [la struttura della patch JSON.](http://jsonpatch.com/) Per creare elenchi di operazioni di patch JSON, è possibile usare come `JsonPatchDocument` illustrato di seguito.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Note generali sull'utilizzo di API/SDK

> [!NOTE]
> Si noti che Gemelli digitali di Azure attualmente non supporta la condivisione di risorse tra le origini **(CORS).** Per altre informazioni sulle strategie di impatto e risoluzione, vedere la sezione [*Cross-Origin Resource Sharing (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) di *Concepts: Security for Gemelli digitali di Azure solutions*.

L'elenco seguente fornisce informazioni dettagliate aggiuntive e linee guida generali per l'uso delle API e degli SDK.

* È possibile usare uno strumento di test REST HTTP come Postman per effettuare chiamate dirette Gemelli digitali di Azure API. Per altre informazioni su questo processo, vedere [*Procedura: Effettuare richieste con Postman*](how-to-use-postman.md).
* Per usare l'SDK, creare un'istanza della `DigitalTwinsClient` classe . Il costruttore richiede credenziali che possono essere ottenute con un'ampia gamma di metodi di autenticazione nel `Azure.Identity` pacchetto. Per altre informazioni su `Azure.Identity` , vedere la relativa documentazione sullo spazio dei [nomi](/dotnet/api/azure.identity). 
* È possibile trovare utile durante l'introduzione, ma esistono diverse altre opzioni, tra cui le credenziali per l'identità gestita, che probabilmente verranno usate per autenticare le funzioni di Azure impostate con l'identità del servizio gestito in `InteractiveBrowserCredential` Gemelli digitali di Azure. [](/dotnet/api/azure.identity.interactivebrowsercredential) [](../app-service/overview-managed-identity.md?tabs=dotnet) Per altre informazioni su `InteractiveBrowserCredential` , vedere la documentazione della [classe](/dotnet/api/azure.identity.interactivebrowsercredential).
* Le richieste alle API Gemelli digitali di Azure richiedono un utente o un'entità servizio che fa parte dello stesso tenant [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) in cui risiede l'istanza Gemelli digitali di Azure. Per impedire agli attori non autorizzati di analizzare gli URL per individuare la posizione in cui si trovano le istanze di Gemelli digitali di Azure, alle richieste con token di accesso dall'esterno del tenant di origine verrà restituito un messaggio di errore "404 Sub-Domain non trovato". Questo errore verrà  restituito anche se all'utente o all'entità servizio è stato assegnato un ruolo Gemelli digitali di Azure Data Owner o Gemelli digitali di Azure Data Reader tramite Azure AD [Collaborazione B2B.](../active-directory/external-identities/what-is-b2b.md)
* Tutte le chiamate API del servizio vengono esposte come funzioni membro nella `DigitalTwinsClient` classe .
* Tutte le funzioni del servizio esistono in versioni sincrone e asincrone.
* Tutte le funzioni del servizio generano un'eccezione per qualsiasi stato restituito di 400 o superiore. Assicurarsi di eseguire il wrapping delle chiamate in `try` una sezione e intercettare almeno `RequestFailedExceptions` . Per altre informazioni su questo tipo di eccezione, vedere [qui](/dotnet/api/azure.requestfailedexception).
* La maggior parte dei metodi `Response<T>` di servizio restituisce o ( per le chiamate `Task<Response<T>>` asincrone), dove `T` è la classe dell'oggetto restituito per la chiamata al servizio. La [`Response`](/dotnet/api/azure.response-1) classe incapsula la restituzione del servizio e presenta i valori restituiti nel `Value` relativo campo .  
* I metodi del servizio con risultati di cui è stato paginato `Pageable<T>` restituiscono `AsyncPageable<T>` o come risultati. Per altre informazioni sulla `Pageable<T>` classe , vedere [qui](/dotnet/api/azure.pageable-1). Per altre informazioni su , `AsyncPageable<T>` vedere [qui](/dotnet/api/azure.asyncpageable-1).
* È possibile eseguire l'iterazione dei risultati di paginazione usando un `await foreach` ciclo . Per altre informazioni su questo processo, vedere [qui](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* L'SDK sottostante è `Azure.Core` . Per informazioni di [riferimento sull'infrastruttura](/dotnet/api/azure) e sui tipi dell'SDK, vedere la documentazione dello spazio dei nomi di Azure.


I metodi del servizio restituiscono oggetti fortemente tipizzato laddove possibile. Tuttavia, poiché Gemelli digitali di Azure è basato su modelli configurati dall'utente in fase di esecuzione (tramite modelli DTDL caricati nel servizio), molte API del servizio accettano e restituiscono dati gemelli in formato JSON.

## <a name="monitor-api-metrics"></a>Monitorare le metriche dell'API

Le metriche delle API, ad esempio le richieste, la latenza e la frequenza degli errori, possono essere visualizzate nel [portale di Azure](https://portal.azure.com/). 

Dalla home page del portale cercare l'istanza Gemelli digitali di Azure per visualizzarne i dettagli. Selezionare **l'opzione** Metriche dal menu Gemelli digitali di Azure'istanza per visualizzare la *pagina* Metriche.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot che mostra la pagina delle metriche per Gemelli digitali di Azure":::

Da qui è possibile visualizzare le metriche per l'istanza e creare visualizzazioni personalizzate.

## <a name="next-steps"></a>Passaggi successivi

Vedere come effettuare richieste dirette alle API usando Postman:
* [*Procedura: Effettuare richieste con Postman*](how-to-use-postman.md)

In caso contrario, è consigliabile usare .NET SDK creando un'app client con questa esercitazione:
* [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)
