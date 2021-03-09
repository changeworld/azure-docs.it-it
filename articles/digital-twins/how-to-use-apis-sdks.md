---
title: Usare le API e gli SDK di Gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Vedere come usare le API dei dispositivi gemelli digitali di Azure, incluso via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b9804e119f5b5cfbee1a61eabf217dad7dbf5500
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507229"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Usare le API e gli SDK di Gemelli digitali di Azure

I dispositivi gemelli digitali di Azure sono dotati di API del **piano di controllo** e **API del piano dati** per gestire l'istanza e i relativi elementi. 
* Le API del piano di controllo sono [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) e coprono le operazioni di gestione delle risorse come la creazione e l'eliminazione dell'istanza. 
* Le API del piano dati sono API per i dispositivi gemelli digitali di Azure e vengono usate per operazioni di gestione dati come la gestione di modelli, gemelli e Graph.

Questo articolo fornisce una panoramica delle API disponibili e i metodi per interagire con essi. È possibile usare le API REST direttamente con le loro spavalderia associate (tramite uno strumento come il [postazione](how-to-use-postman.md)) o tramite un SDK.

## <a name="overview-control-plane-apis"></a>Panoramica: API del piano di controllo

Le API del piano di controllo sono API [ARM](../azure-resource-manager/management/overview.md) usate per gestire l'istanza di dispositivi gemelli digitali di Azure nel suo complesso, in modo da coprire operazioni come la creazione o l'eliminazione dell'intera istanza. Questi vengono usati anche per creare ed eliminare gli endpoint.

La versione più recente dell'API del piano di controllo è _**2020-12-01**_.

Per usare le API del piano di controllo:
* È possibile chiamare direttamente le API facendo riferimento alla cartella spavalderia più recente nel [repository di spavalderia del piano di controllo](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Questa cartella include anche una cartella di esempi che illustrano l'utilizzo.
* È attualmente possibile accedere ad SDK per le API di controllo in...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([riferimento [generato automaticamente]](/dotnet/api/overview/azure/digitaltwins/management)) ([origine](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([riferimento [generato automaticamente]](/java/api/overview/azure/digitaltwins)) ([origine](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([origine](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([origine](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) ([origine](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins))

È anche possibile esercitare le API del piano di controllo interagendo con i dispositivi gemelli digitali di Azure tramite il [portale di Azure](https://portal.azure.com) e l' [interfaccia](how-to-use-cli.md)della riga di comando.

## <a name="overview-data-plane-apis"></a>Panoramica: API del piano dati

Le API del piano dati sono le API di Azure Digital gemelli usate per gestire gli elementi all'interno dell'istanza di Azure Digital gemelli. Sono incluse operazioni come la creazione di route, il caricamento di modelli, la creazione di relazioni e la gestione dei dispositivi gemelli. Possono essere suddivise nelle categorie seguenti:
* **DigitalTwinModels** : la categoria DigitalTwinModels contiene le API per gestire i [modelli](concepts-models.md) in un'istanza di dispositivi gemelli digitali di Azure. Le attività di gestione includono il caricamento, la convalida, il recupero e l'eliminazione di modelli creati in DTDL.
* **DigitalTwins** : la categoria DigitalTwins contiene le API che consentono agli sviluppatori di creare, modificare ed eliminare i dispositivi [gemelli digitali](concepts-twins-graph.md) e le relative relazioni in un'istanza di dispositivi gemelli digitali di Azure.
* **Query** : la categoria query consente agli sviluppatori [di individuare set di gemelli digitali nel grafico gemello tra le](how-to-query-graph.md) relazioni.
* **Route di eventi** : la categoria di route degli eventi contiene le API per [indirizzare i dati](concepts-route-events.md), attraverso il sistema e i servizi downstream.

La versione più recente dell'API del piano dati è _**2020-10-31**_.

Per usare le API del piano dati:
* È possibile chiamare direttamente le API, da...
   - riferimento alla cartella spavalderia più recente nel [repository di spavalderia del piano dati](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Questa cartella include anche una cartella di esempi che illustrano l'utilizzo. 
   - visualizzazione della [documentazione di riferimento](/rest/api/azure-digitaltwins/)per le API.
* È possibile usare **.NET (C#) SDK**. Per utilizzare .NET SDK...
   - è possibile visualizzare e aggiungere il pacchetto da NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - è possibile visualizzare la [documentazione di riferimento dell'SDK](/dotnet/api/overview/azure/digitaltwins/client).
   - è possibile trovare l'origine SDK, inclusa una cartella di esempi, in GitHub: [libreria client di dispositivi digitali gemelli di Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - per visualizzare informazioni dettagliate ed esempi di utilizzo, continuare con la sezione [*.NET (C#) SDK (piano dati)*](#net-c-sdk-data-plane) di questo articolo.
* È possibile usare **Java SDK**. Per utilizzare Java SDK...
   - è possibile visualizzare e installare il pacchetto da Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - è possibile visualizzare la [documentazione di riferimento dell'SDK](/java/api/overview/azure/digitaltwins/client)
   - è possibile trovare l'origine SDK in GitHub: [libreria client di dispositivi digitali gemelli di Azure per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* È possibile usare **JavaScript SDK**. Per utilizzare JavaScript SDK...
   - è possibile visualizzare e installare il pacchetto da NPM: [libreria client dei componenti di base di Azure Digital gemelli per JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - è possibile visualizzare la [documentazione di riferimento dell'SDK](/javascript/api/@azure/digital-twins-core/).
   - è possibile trovare l'origine SDK in GitHub: [libreria client di Azure Digital Twins core per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* È possibile usare **Python SDK**. Per usare Python SDK...
   - è possibile visualizzare e installare il pacchetto da PyPi: [libreria client dei componenti di base di Azure Digital gemelli di Azure per Python](https://pypi.org/project/azure-digitaltwins-core/).
   - è possibile visualizzare la [documentazione di riferimento dell'SDK](/python/api/azure-digitaltwins-core/azure.digitaltwins.core).
   - è possibile trovare l'origine SDK in GitHub: [libreria client di Azure Digital Twins core per Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* È possibile generare un SDK per un altro linguaggio con autorest. Seguire le istruzioni in [*procedura: creare SDK personalizzati per i dispositivi gemelli digitali di Azure con autorest*](how-to-create-custom-sdks.md).

È anche possibile esercitare le API del piano dati interagendo con i dispositivi gemelli digitali di Azure tramite l' [interfaccia](how-to-use-cli.md)della riga di comando.

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (piano dati)

Azure Digital Twins .NET (C#) SDK è parte di Azure SDK per .NET. È open source ed è basato sulle API dei piani dati di Azure Digital gemelli.

> [!NOTE]
> Per altre informazioni sulla progettazione di SDK, vedere i [principi di progettazione generali per gli SDK di Azure](https://azure.github.io/azure-sdk/general_introduction.html) e le [linee guida di progettazione .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html)specifiche.

Per usare l'SDK, includere il pacchetto NuGet **Azure. DigitalTwins. Core** con il progetto. Sarà necessaria anche la versione più recente del pacchetto **Azure. Identity** . In Visual Studio è possibile aggiungere questi pacchetti usando Gestione pacchetti NuGet (a cui si accede tramite *strumenti > gestione pacchetti nuget > gestire i pacchetti NuGet per la soluzione*). In alternativa, è possibile usare lo strumento da riga di comando .NET con i comandi disponibili nei collegamenti al pacchetto NuGet seguenti per aggiungerli al progetto:
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Questo è il pacchetto per l'[SDK Gemelli digitali di Azure per .NET](/dotnet/api/overview/azure/digitaltwins/client). 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Questa libreria fornisce strumenti che consentono di eseguire l'autenticazione in Azure.

Per una procedura dettagliata sull'uso delle API, vedere l' [*esercitazione: scrivere codice per un'app client*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Esempi di utilizzo di .NET SDK

Di seguito sono riportati alcuni esempi di codice che illustrano l'uso di .NET SDK.

Eseguire l'autenticazione per il servizio:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Caricare un modello:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Elencare i modelli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Creazione di dispositivi gemelli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Eseguire query sui gemelli e scorrere in ciclo i risultati:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Vedere l' [*esercitazione: scrivere codice di un'app client*](tutorial-code.md) per una procedura dettagliata del codice dell'app di esempio. 

È anche possibile trovare esempi aggiuntivi nel [repository GitHub per .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Helper di serializzazione

Gli helper di serializzazione sono funzioni di supporto disponibili nell'SDK per creare o deserializzare rapidamente i dati gemelli per l'accesso alle informazioni di base. Poiché i metodi di Core SDK restituiscono i dati gemelli come JSON per impostazione predefinita, può essere utile usare queste classi helper per suddividere ulteriormente i dati gemelli.

Le classi helper disponibili sono:
* `BasicDigitalTwin`: Rappresenta i dati principali di un dispositivo gemello digitale
* `BasicRelationship`: Rappresenta i dati principali di una relazione
* `UpdateOperationUtility`: Rappresenta le informazioni sulla patch JSON usate nelle chiamate di aggiornamento
* `WriteableProperty`: Rappresenta i metadati della proprietà

##### <a name="deserialize-a-digital-twin"></a>Deserializzare un dispositivo gemello digitale

È sempre possibile deserializzare i dati gemelli usando la libreria JSON preferita, ad esempio `System.Test.Json` o `Newtonsoft.Json` . Per l'accesso di base a un dispositivo gemello, le classi helper lo rendono leggermente più pratico.

La `BasicDigitalTwin` classe helper consente inoltre di accedere alle proprietà definite nel dispositivo gemello, tramite un `Dictionary<string, object>` . Per elencare le proprietà del dispositivo gemello, è possibile usare:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

##### <a name="create-a-digital-twin"></a>Creare un dispositivo gemello digitale

Utilizzando la `BasicDigitalTwin` classe, è possibile preparare i dati per la creazione di un'istanza del dispositivo gemello:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Il codice precedente è equivalente alla variante "Manual" riportata di seguito:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Deserializzare una relazione

È sempre possibile deserializzare i dati della relazione in un tipo di propria scelta. Per l'accesso di base a una relazione, usare il tipo `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

La `BasicRelationship` classe helper consente inoltre di accedere alle proprietà definite nella relazione, tramite un oggetto `IDictionary<string, object>` . Per elencare le proprietà, è possibile usare:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Creare una relazione

Utilizzando la `BasicRelationship` classe, è inoltre possibile preparare i dati per la creazione di relazioni in un'istanza gemella:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Creare una patch per l'aggiornamento dei dispositivi gemelli

Le chiamate di aggiornamento per i dispositivi gemelli e le relazioni usano la struttura [patch JSON](http://jsonpatch.com/) . Per creare elenchi di operazioni patch JSON, è possibile usare `JsonPatchDocument` come illustrato di seguito.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Note generali sull'utilizzo di API/SDK

> [!NOTE]
> Si noti che i dispositivi gemelli digitali di Azure attualmente non supportano la **condivisione di risorse tra le origini (CORS)**. Per ulteriori informazioni sulle strategie di effetto e risoluzione, vedere la sezione relativa alla [*condivisione delle risorse tra le origini (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) di *concetti relativi alla sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*.

L'elenco seguente fornisce informazioni aggiuntive e linee guida generali per l'uso delle API e degli SDK.

* È possibile usare uno strumento di testing HTTP REST, ad esempio il post per effettuare chiamate dirette alle API dei dispositivi gemelli digitali di Azure. Per ulteriori informazioni su questo processo, vedere [*procedura: effettuare richieste con il post*](how-to-use-postman.md).
* Per usare l'SDK, creare un'istanza della `DigitalTwinsClient` classe. Il costruttore richiede credenziali che possono essere ottenute con diversi metodi di autenticazione nel `Azure.Identity` pacchetto. Per ulteriori informazioni `Azure.Identity` , vedere la relativa [documentazione dello spazio dei nomi](/dotnet/api/azure.identity). 
* Potrebbe risultare `InteractiveBrowserCredential` utile durante l'introduzione, ma sono disponibili diverse altre opzioni, incluse le credenziali per l' [identità gestita](/dotnet/api/azure.identity.interactivebrowsercredential), che è possibile usare per autenticare le [funzioni di Azure configurate con MSI](../app-service/overview-managed-identity.md?tabs=dotnet) nei dispositivi gemelli digitali di Azure. Per ulteriori informazioni su `InteractiveBrowserCredential` , vedere la relativa documentazione relativa alla [classe](/dotnet/api/azure.identity.interactivebrowsercredential).
* Tutte le chiamate all'API del servizio sono esposte come funzioni membro sulla `DigitalTwinsClient` classe.
* Tutte le funzioni del servizio sono disponibili in versioni sincrone e asincrone.
* Tutte le funzioni del servizio generano un'eccezione per qualsiasi stato di restituzione 400 o superiore. Assicurarsi di eseguire il wrapping delle chiamate in una `try` sezione e intercettare almeno `RequestFailedExceptions` . Per ulteriori informazioni su questo tipo di eccezione, vedere [qui](/dotnet/api/azure.requestfailedexception).
* La maggior parte dei metodi di servizio restituisce `Response<T>` o ( `Task<Response<T>>` per le chiamate asincrone), dove `T` è la classe dell'oggetto restituito per la chiamata al servizio. La [`Response`](/dotnet/api/azure.response-1) classe incapsula il risultato del servizio e Visualizza i valori restituiti nel `Value` campo.  
* I metodi del servizio con risultati di paging restituiscono `Pageable<T>` o `AsyncPageable<T>` come risultati. Per ulteriori informazioni sulla `Pageable<T>` classe, vedere [qui](/dotnet/api/azure.pageable-1). per ulteriori informazioni su `AsyncPageable<T>` , vedere [qui](/dotnet/api/azure.asyncpageable-1).
* È possibile scorrere i risultati di paging usando un `await foreach` ciclo. Per ulteriori informazioni su questo processo, vedere [qui](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* L'SDK sottostante è `Azure.Core` . Per informazioni di riferimento sull'infrastruttura e sui tipi SDK, vedere la [documentazione dello spazio dei nomi di Azure](/dotnet/api/azure) .

I metodi del servizio restituiscono oggetti fortemente tipizzati laddove possibile. Tuttavia, poiché i dispositivi gemelli digitali di Azure si basano sui modelli configurati in modo personalizzato dall'utente in fase di esecuzione (tramite i modelli DTDL caricati nel servizio), molte API del servizio accettano e restituiscono i dati gemelli in formato JSON.

## <a name="monitor-api-metrics"></a>Monitorare le metriche API

Le metriche API, ad esempio richieste, latenza e percentuale di errori, possono essere visualizzate nel [portale di Azure](https://portal.azure.com/). 

Dalla Home page del portale, cercare l'istanza di Azure Digital gemelli per recuperare i dettagli. Selezionare l'opzione **metrica** dal menu dell'istanza di Azure Digital gemelli per visualizzare la pagina *metrica* .

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot che mostra la pagina delle metriche per i dispositivi gemelli digitali di Azure":::

Da qui è possibile visualizzare le metriche per l'istanza e creare visualizzazioni personalizzate.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come inviare richieste dirette alle API, vedere l'articolo:
* [*Procedura: effettuare richieste con il post*](how-to-use-postman.md)

In alternativa, provare a usare .NET SDK creando un'app client con questa esercitazione:
* [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)