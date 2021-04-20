---
title: Trigger e associazioni in Funzioni di Azure
description: Informazioni su come usare trigger e associazioni per connettere la funzione di Azure a eventi online e servizi basati sul cloud.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8648a52c58929983070b9a89c8fe946b89d37385
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739405"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure

Questo articolo illustra i concetti generali relativi ai trigger e alle associazioni delle funzioni.

I trigger sono la causa dell'esecuzione di una funzione. Un trigger definisce come viene richiamata una funzione e una funzione deve avere esattamente un trigger. Ai trigger sono associati dati, che spesso vengono forniti come payload della funzione. 

L'associazione a una funzione è un modo per connettere in modo dichiarativo un'altra risorsa alla funzione. Le associazioni possono essere connesse come *associazioni di input,* *associazioni di output* o entrambe. I dati dei binding vengono forniti alla funzione come parametri.

È possibile combinare binding diversi in base alle esigenze. I binding sono facoltativi e una funzione potrebbe avere uno o più binding di input e/o di output.

I trigger e le associazioni consentono di evitare l'accesso hardcoding ad altri servizi. La funzione riceve i dati, ad esempio il contenuto di un messaggio della coda, nei parametri della funzione. I dati vengono inviati, ad esempio per creare un messaggio della coda, usando il valore restituito della funzione. 

Si considerino gli esempi seguenti di come è possibile implementare funzioni diverse.

| Scenario di esempio | Trigger | Associazione di input | Associazione di output |
|-------------|---------|---------------|----------------|
| Arriva un nuovo messaggio della coda che esegue una funzione per scrivere in un'altra coda. | Coda<sup>*</sup> | *Nessuna* | Coda<sup>*</sup> |
|Un processo pianificato legge il contenuto dell'archivio BLOB e crea un nuovo Cosmos DB documento. | Timer | Archiviazione BLOB | Cosmos DB |
|Griglia di eventi viene usato per leggere un'immagine dall'archivio BLOB e un documento Cosmos DB inviare un messaggio di posta elettronica. | Griglia di eventi | Archiviazione BLOB e Cosmos DB | SendGrid |
| Webhook che usa Microsoft Graph per aggiornare un foglio di Excel. | HTTP | *Nessuna* | Microsoft Graph |

<sup>\*</sup> Rappresenta code diverse

Questi esempi non sono concepiti per essere esaustivi, ma vengono forniti per illustrare come è possibile usare trigger e associazioni insieme.

###  <a name="trigger-and-binding-definitions"></a>Definizioni di trigger e associazioni

I trigger e le associazioni vengono definiti in modo diverso a seconda del linguaggio di sviluppo.

| Linguaggio | I trigger e le associazioni sono configurati da ... |
|-------------|--------------------------------------------|
| Libreria di classi C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorazione di metodi e parametri con attributi C# |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorazione di metodi e parametri con annotazioni Java  | 
| JavaScript/PowerShell/Python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aggiornamento [function.jssu](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

Per le lingue che si basano function.js, il portale fornisce un'interfaccia utente per l'aggiunta di associazioni nella **scheda** Integrazione. È anche possibile modificare il file direttamente nel portale nella **scheda Codice e test** della funzione. Visual Studio Code consente di [aggiungere facilmente un'associazione a](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project) un function.jsnel file seguendo un pratico set di richieste. 

In .NET e Java il tipo di parametro definisce il tipo di dati per i dati di input. Ad esempio, usare per eseguire l'associazione al testo di un trigger della coda, una matrice di byte da leggere come binaria e un tipo personalizzato da `string` de-serializzare in un oggetto . Poiché le funzioni della libreria di classi  .NET e le funzioni Java non si basano sufunction.jsper le definizioni di associazione, non possono essere create e modificate nel portale. La modifica del portale C# è basata su script C#, che usafunction.js *su anziché* sugli attributi.

Per altre informazioni su come aggiungere associazioni a funzioni esistenti, vedere Connettere funzioni [ai servizi di Azure usando le associazioni](add-bindings-existing-function.md).

Per i linguaggi tipizzati in modo dinamico, ad esempio JavaScript, usare la proprietà `dataType` nel file *function.json*. Ad esempio, per eseguire la lettura del contenuto di una richiesta HTTP in formato binario, impostare `dataType` su `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Altre opzioni per `dataType` sono `stream` e `string`.

## <a name="binding-direction"></a>Direzione dell'associazione

Tutti i trigger e le associazioni hanno una proprietà `direction` nel file [function.json](./functions-reference.md):

- Per i trigger, la direzione è sempre `in`
- Le associazioni di input e di output usano `in` e `out`
- Alcune associazioni supportano una direzione speciale `inout`. Se si usa `inout` , solo **l'editor avanzato** è disponibile tramite la scheda **Integrazione** nel portale.

Quando si usano gli [attributi in una libreria di classi](functions-dotnet-class-library.md) per configurare i trigger e le associazioni, la direzione viene specificata in un costruttore di attributo o dedotta dal tipo di parametro.

## <a name="add-bindings-to-a-function"></a>Aggiungere associazioni a una funzione

È possibile connettere la funzione ad altri servizi usando associazioni di input o di output. Aggiungere un'associazione aggiungendo le relative definizioni specifiche alla funzione. Per informazioni su come, vedere [Aggiungere associazioni a una funzione esistente in Funzioni di Azure](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="bindings-code-examples"></a>Esempi di codice di associazioni

Usare la tabella seguente per trovare esempi di tipi di associazione specifici che illustrano come usare le associazioni nelle funzioni. Per prima cosa, scegliere la scheda lingua corrispondente al progetto. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Binding personalizzati

È possibile creare associazioni di input e output personalizzate. I binding devono essere creati in .NET, ma possono essere utilizzati da qualsiasi linguaggio supportato. Per altre informazioni sulla creazione di associazioni personalizzate, vedere [Creazione di associazioni di input e output personalizzate.](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)

## <a name="resources"></a>Risorse
- [Modelli ed espressioni di associazione](./functions-bindings-expressions-patterns.md)
- [Uso del valore restituito di Funzione di Azure](./functions-bindings-return-value.md)
- [Come registrare un'espressione di associazione](./functions-bindings-register.md)
- Testing:
  - [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md)
  - [Eseguire manualmente una funzione non attivata da HTTP](functions-manually-run-non-http.md)
- [Gestione degli errori di associazione](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Registrare Funzioni di Azure binding di estensioni](./functions-bindings-register.md)
