---
title: Creare SDK in linguaggio personalizzato con AutoRest
titleSuffix: Azure Digital Twins
description: Informazioni su come usare AutoRest per generare SDK in linguaggio personalizzato, per scrivere codice Gemelli digitali di Azure in altri linguaggi che non hanno pubblicato SDK.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 4e91bf5acc5290229afa8dc7a849e8953257bcfd
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751113"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Creare SDK in linguaggio personalizzato per Gemelli digitali di Azure usando AutoRest

Se è necessario usare Gemelli digitali di Azure usando un linguaggio che non dispone di un [SDK di Gemelli digitali di Azure](how-to-use-apis-sdks.md)pubblicato, questo articolo illustra come usare AutoRest per generare il proprio SDK nel linguaggio preferito. 

Gli esempi in questo articolo illustrano la creazione di un [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)del piano dati, ma questo processo funzionerà anche per la generazione di un  [SDK](how-to-use-apis-sdks.md#overview-control-plane-apis) del piano di controllo.

## <a name="prerequisites"></a>Prerequisiti

Per generare un SDK, è prima necessario completare la configurazione seguente nel computer locale:
* Installare [**AutoRest**](https://github.com/Azure/autorest), versione 2.0.4413 (la versione 3 non è attualmente supportata)
* Installare [**Node.js**](https://nodejs.org), che è un requisito preliminare per l'uso di AutoRest
* Installare [ **Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* Scaricare la versione Gemelli digitali di Azure file **Swagger** (OpenAPI) del piano dati dalla cartella [Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)del piano dati, insieme alla relativa cartella di esempi. Il file Swagger è quello denominato *digitaltwins.jsin*.

>[!TIP]
> Per creare invece un **SDK** del piano di controllo, completare i passaggi descritti in questo articolo usando il file **Swagger** (OpenAPI) del piano di controllo più recente dalla cartella [Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) del piano di controllo invece del piano dati uno.

Quando il computer è dotato di tutti gli elementi dell'elenco precedente, è possibile usare AutoRest per creare un SDK.

## <a name="create-the-sdk-using-autorest"></a>Creare l'SDK usando AutoRest 

Dopo aver installato Node.js, è possibile eseguire questo comando per assicurarsi che sia installata la versione richiesta di AutoRest:
```cmd/sh
npm install -g autorest@2.0.4413
```

Per eseguire AutoRest nel file Gemelli digitali di Azure Swagger, seguire questa procedura:
1. Copiare Gemelli digitali di Azure file Swagger e la relativa cartella di esempi in una directory di lavoro.
2. Usare una finestra del prompt dei comandi per passare alla directory di lavoro.
3. Eseguire AutoRest con il comando seguente. Sostituire il `<language>` segnaposto con la lingua scelta: `python` , , e così `java` `go` via. È possibile trovare l'elenco completo delle opzioni nel [file LEGGIMI di AutoRest.](https://github.com/Azure/autorest)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Di conseguenza, nella directory di lavoro verrà visualizzata una nuova cartella denominata *DigitalTwinsApi.* I file SDK generati avranno lo spazio dei *nomi DigitalTwinsApi*. Si continuerà a usare tale spazio dei nomi tramite il resto degli esempi di utilizzo in questo articolo.

AutoRest supporta un'ampia gamma di generatori di codice del linguaggio.

## <a name="make-the-sdk-into-a-class-library"></a>Rendere l'SDK in una libreria di classi

È possibile includere i file generati da AutoRest direttamente in una soluzione .NET. Tuttavia, è probabile che si voglia includere l'SDK di Gemelli digitali di Azure in diversi progetti separati (app client, app Funzioni di Azure e altro ancora). Per questo motivo, può essere utile compilare un progetto separato (una libreria di classi .NET) dai file generati. È quindi possibile includere questo progetto di libreria di classi in diverse soluzioni come riferimento al progetto.

In questa sezione vengono fornite istruzioni su come compilare l'SDK come libreria di classi, che è il proprio progetto e può essere incluso in altri progetti. Questi passaggi si basano **Visual Studio**.

Di seguito sono riportati i passaggi necessari:

1. Creare una nuova soluzione Visual Studio per una libreria di classi
2. Usare *DigitalTwinsApi* come nome del progetto
3. In Esplora soluzioni fare clic con il pulsante destro del mouse sul *progetto DigitalTwinsApi* della soluzione generata e scegliere Aggiungi > *elemento esistente...*
4. Trovare la cartella in cui è stato generato l'SDK e selezionare i file a livello di radice
5. Premere "OK"
6. Aggiungere una cartella al progetto (fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Aggiungi > *nuova cartella*)
7. Assegnare alla cartella il nome *Models*
8. Fare clic con il pulsante *destro del mouse* sulla cartella Esplora soluzioni e scegliere > Elemento *esistente...*
9. Selezionare i file nella *cartella Models dell'SDK* generato e premere "OK"

Per compilare correttamente l'SDK, il progetto avrà bisogno di questi riferimenti:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Per aggiungerli, aprire *Strumenti > NuGet Gestione pacchetti > Gestisci pacchetti NuGet per la soluzione...*.

1. Nel pannello verificare che la scheda *Sfoglia* sia selezionata
2. Cercare *Microsoft.Rest*
3. Selezionare i `ClientRuntime` `ClientRuntime.Azure` pacchetti e e aggiungerli alla soluzione

È ora possibile compilare il progetto e includerlo come riferimento al progetto in qualsiasi applicazione Gemelli digitali di Azure scrittura.

## <a name="tips-for-using-the-sdk"></a>Suggerimenti per l'uso dell'SDK

Questa sezione contiene informazioni generali e linee guida per l'uso dell'SDK generato.

### <a name="synchronous-and-asynchronous-calls"></a>Chiamate sincrone e asincrone

Tutte le funzioni SDK sono disponibili in versioni sincrone e asincrone.

### <a name="typed-and-untyped-data"></a>Dati tipiati e non tipi

Le chiamate API REST in genere restituiscono oggetti fortemente tipizzato. Tuttavia, poiché Gemelli digitali di Azure consente agli utenti di definire i propri tipi personalizzati per i gemelli, non è possibile pre-definire i dati restituiti statici per molte Gemelli digitali di Azure chiamate. Al contrario, le API restituiscono tipi wrapper fortemente tipizzato, se applicabile, e i dati del dispositivo gemello personalizzato si trova in oggetti Json.NET (usati ovunque il tipo di dati "object" venga visualizzato nelle firme dell'API). È possibile eseguire il cast di questi oggetti in modo appropriato nel codice.

### <a name="error-handling"></a>Gestione degli errori

Ogni volta che si verifica un errore nell'SDK (inclusi gli errori HTTP, ad esempio 404), l'SDK genererà un'eccezione. Per questo motivo, è importante incapsulare tutte le chiamate API all'interno di blocchi try/catch.

Ecco un frammento di codice che tenta di aggiungere un gemello e rileva eventuali errori in questo processo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Paging

AutoRest genera due tipi di modelli di paging per l'SDK:
* Una per tutte le API tranne l'API Query
* Uno per l'API Query

Nel modello di paging non di query, ecco un metodo di esempio che mostra come recuperare un elenco di pagine di relazioni in uscita da Gemelli digitali di Azure:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Il secondo modello viene generato solo per l'API Query. Usa un oggetto in `continuationToken` modo esplicito.

>[!TIP]
> Un motivo principale per ottenere le pagine è calcolare gli addebiti per [l'unità query](concepts-query-units.md) per una chiamata api query.

Ecco un esempio con questo modello:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Passaggi successivi

Seguire i passaggi per creare un'app client in cui è possibile usare l'SDK:
* [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)
