---
title: Testing unità di Funzioni durevoli di Azure
description: Informazioni su come eseguire lo unit test di Funzioni durevoli.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 89b6419e95b3971b0d272490e19354f300204e1e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491045"
---
# <a name="durable-functions-unit-testing"></a>Testing unità di Funzioni durevoli

Il testing unità è una parte importante delle moderne procedure di sviluppo software. Gli unit test verificano il comportamento della logica di business e proteggono dall'introduzione futura di modifiche inosservate che causano un'interruzione. La complessità di Funzioni durevoli può facilmente aumentare e l'introduzione di unit test consentirà quindi di evitare le modifiche che causano un'interruzione. Nelle sezioni seguenti viene illustrato come unit test i tre tipi di funzione: client di orchestrazione, agente di orchestrazione e funzioni di attività.

> [!NOTE]
> Questo articolo fornisce informazioni aggiuntive per il testing unità per Durable Functions app destinate Durable Functions 2. x. Per ulteriori informazioni sulle differenze tra le versioni, vedere l'articolo relativo alle [versioni di Durable Functions](durable-functions-versions.md) .

## <a name="prerequisites"></a>Prerequisiti

Gli esempi di questo articolo richiedono la conoscenza dei concetti e dei framework seguenti:

* Unit test

* Funzioni permanenti

* [xUnit](https://github.com/xunit/xunit): framework di test

* [moq](https://github.com/moq/moq4): framework di comportamento fittizio

## <a name="base-classes-for-mocking"></a>Classi base per il comportamento fittizio

La simulazione è supportata tramite l'interfaccia seguente:

* [IDurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationClient), [IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.IDurableEntityClient) e [IDurableClient](/dotnet/api/microsoft.azure.webjobs.IDurableClient)

* [IDurableOrchestrationContext](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationContext)

* [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.IDurableActivityContext)
  
* [IDurableEntityContext](/dotnet/api/microsoft.azure.webjobs.IDurableEntityContext)

Queste interfacce possono essere utilizzate con i vari trigger e associazioni supportati da Durable Functions. Quando si eseguono le funzioni di Azure, il runtime di funzioni eseguirà il codice della funzione con un'implementazione concreta di queste interfacce. Per il testing unità, è possibile passare una versione fittizia di queste interfacce per testare la logica di business.

## <a name="unit-testing-trigger-functions"></a>Testing unità delle funzioni di trigger

In questa sezione lo unit test convaliderà la logica della funzione di trigger HTTP seguente per avviare nuove orchestrazioni.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

L'attività di unit test servirà a verificare il valore dell'intestazione `Retry-After` fornita nel payload della risposta. Quindi, il unit test simula alcuni `IDurableClient` metodi per garantire un comportamento prevedibile.

In primo luogo, si usa un Framework fittizio ([MOQ](https://github.com/moq/moq4) in questo caso) per simulare `IDurableClient` :

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> Sebbene sia possibile simulare interfacce implementando direttamente l'interfaccia come una classe, i Framework fittizi semplificano il processo in diversi modi. Ad esempio, se un nuovo metodo viene aggiunto all'interfaccia per le versioni secondarie, MOQ non richiede alcuna modifica del codice a differenza delle implementazioni concrete.

Viene quindi simulato il metodo `StartNewAsync` per restituire un ID istanza noto.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

Viene successivamente simulato `CreateCheckStatusResponse` per restituire sempre una risposta HTTP 200 vuota.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
    .Returns(new HttpResponseMessage
    {
        StatusCode = HttpStatusCode.OK,
        Content = new StringContent(string.Empty),
        Headers =
        {
            RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
        }
    });
```

Viene simulato anche `ILogger`:

```csharp
// Mock ILogger
var loggerMock = new Mock<ILogger>();
```  

Il metodo `Run` viene ora chiamato dallo unit test:

```csharp
// Call Orchestration trigger function
var result = await HttpStart.Run(
    new HttpRequestMessage()
    {
        Content = new StringContent("{}", Encoding.UTF8, "application/json"),
        RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
    },
    durableClientMock.Object,
    functionName,
    loggerMock.Object);
 ```

 L'ultimo passaggio è il confronto dell'output con il valore previsto:

```csharp
// Validate that output is not null
Assert.NotNull(result.Headers.RetryAfter);

// Validate output's Retry-After header value
Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Dopo avere combinato tutti i passaggi, lo unit test avrà il codice seguente:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Testing unità delle funzioni dell'agente di orchestrazione

Le funzioni dell'agente di orchestrazione sono ancora più interessanti per il testing unità perché in genere contengono molta più logica di business.

In questa sezione gli unit test convalideranno l'output della funzione dell'agente di orchestrazione `E1_HelloSequence`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Il codice degli unit test inizierà con la creazione di una simulazione:

```csharp
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
```

Verranno quindi simulate le chiamate al metodo dell'attività:

```csharp
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Lo unit test chiamerà successivamente il metodo `HelloSequence.Run`:

```csharp
var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Infine verrà convalidato l'output:

```csharp
Assert.Equal(3, result.Count);
Assert.Equal("Hello Tokyo!", result[0]);
Assert.Equal("Hello Seattle!", result[1]);
Assert.Equal("Hello London!", result[2]);
```

Dopo avere combinato tutti i passaggi, lo unit test avrà il codice seguente:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Testing unità delle funzioni dell'attività

È possibile eseguire lo unit test delle funzioni dell'attività nello stesso modo in cui si esegue quello delle funzioni non durevoli.

In questa sezione lo unit test convaliderà il comportamento della funzione dell'attività `E1_SayHello`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Lo unit test verificherà il formato dell'output. Gli unit test possono usare direttamente i tipi di parametro o la classe fittizia `IDurableActivityContext` :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Altre informazioni su moq](https://github.com/Moq/moq4/wiki/Quickstart)
