---
title: Trigger e associazioni in Funzioni di Azure
description: Informazioni su come usare trigger e associazioni per connettere la funzione di Azure agli eventi online e ai servizi basati sul cloud.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 4cafe9af1eb5a765ab86bafb63cc9ab7d0889dc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627600"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concetti di Trigger e associazioni di Funzioni di Azure

In questo articolo vengono illustrati i concetti di alto livello che circondano i trigger e le associazioni di funzioni.

I trigger sono la cosa che determina l'esecuzione di una funzione. Un trigger definisce il modo in cui viene richiamata una funzione e una funzione deve avere esattamente un trigger. Ai trigger sono associati dati, che spesso vengono forniti come payload della funzione. 

Il binding a una funzione è un modo per connettere in modo dichiarativo un'altra risorsa alla funzione; i binding possono essere connessi come *binding di input*, *associazioni di output* o entrambi. I dati dei binding vengono forniti alla funzione come parametri.

È possibile combinare binding diversi in base alle esigenze. I binding sono facoltativi e una funzione potrebbe avere uno o più binding di input e/o di output.

Trigger e associazioni consentono di evitare l'accesso hardcoded ad altri servizi. La funzione riceve i dati, ad esempio il contenuto di un messaggio della coda, nei parametri della funzione. I dati vengono inviati, ad esempio per creare un messaggio della coda, usando il valore restituito della funzione. 

Si considerino gli esempi seguenti di come implementare funzioni diverse.

| Scenario di esempio | Trigger | Binding di input | Binding di output |
|-------------|---------|---------------|----------------|
| Arriva un nuovo messaggio di coda che esegue una funzione per scrivere in un'altra coda. | Coda<sup>*</sup> | *Nessuno* | Coda<sup>*</sup> |
|Un processo pianificato legge il contenuto dell'archiviazione BLOB e crea un nuovo documento Cosmos DB. | Timer | Archiviazione BLOB | Cosmos DB |
|La griglia di eventi viene usata per leggere un'immagine dall'archiviazione BLOB e un documento da Cosmos DB per inviare un messaggio di posta elettronica. | Griglia di eventi | Archiviazione BLOB e Cosmos DB | SendGrid |
| Un webhook che usa Microsoft Graph per aggiornare un foglio di Excel. | HTTP | *Nessuno* | Microsoft Graph |

<sup>\*</sup> Rappresenta le code diverse

Questi esempi non sono destinati a essere esaustivi, ma vengono forniti per illustrare come è possibile usare i trigger e le associazioni.

###  <a name="trigger-and-binding-definitions"></a>Trigger e definizioni di binding

I trigger e le associazioni sono definiti in modo diverso a seconda del linguaggio di sviluppo.

| Linguaggio | Trigger e associazioni sono configurati da... |
|-------------|--------------------------------------------|
| Libreria di classi C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorazione di metodi e parametri con attributi C# |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorazione di metodi e parametri con annotazioni Java  | 
| JavaScript/PowerShell/Python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aggiornamento [ difunction.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

Per le lingue che si basano su function.json, il portale fornisce un'interfaccia utente per l'aggiunta di binding nella scheda **integrazione** . È anche possibile modificare il file direttamente nel portale nella scheda **codice + test** della funzione. Visual Studio Code consente di [aggiungere facilmente un'associazione a una function.jssul file](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project) seguendo una semplice serie di richieste. 

In .NET e Java il tipo di parametro definisce il tipo di dati per i dati di input. Ad esempio, usare `string` per eseguire l'associazione al testo di un trigger della coda, una matrice di byte da leggere come binario e un tipo personalizzato da deserializzare in un oggetto. Poiché le funzioni della libreria di classi .NET e le funzioni Java non si basano su *function.json* per le definizioni di binding, non possono essere create e modificate nel portale. La modifica del portale c# è basata sullo script C#, che usa *function.jsin* anziché negli attributi.

Per altre informazioni su come aggiungere binding alle funzioni esistenti, vedere Connettere le [funzioni ai servizi di Azure usando le associazioni](add-bindings-existing-function.md).

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
- Alcune associazioni supportano una direzione speciale `inout`. Se si usa `inout` , solo l' **Editor avanzato** è disponibile tramite la scheda **integrazione** nel portale.

Quando si usano gli [attributi in una libreria di classi](functions-dotnet-class-library.md) per configurare i trigger e le associazioni, la direzione viene specificata in un costruttore di attributo o dedotta dal tipo di parametro.

## <a name="add-bindings-to-a-function"></a>Aggiungere binding a una funzione

È possibile connettere la funzione ad altri servizi usando binding di input o di output. Aggiungere un'associazione aggiungendo le definizioni specifiche alla funzione. Per informazioni, vedere [aggiungere binding a una funzione esistente in funzioni di Azure](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Binding supportati

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Per informazioni sulle associazioni in anteprima o approvate per l'uso in ambiente di produzione, vedere [Linguaggi supportati ](supported-languages.md).

## <a name="bindings-code-examples"></a>Esempi di codice delle associazioni

Usare la tabella seguente per trovare esempi di tipi di binding specifici che mostrano come usare le associazioni nelle funzioni. Per prima cosa, scegliere la scheda lingua corrispondente al progetto. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Binding personalizzati

È possibile creare binding di input e di output personalizzati. I binding devono essere creati in .NET, ma possono essere utilizzati da qualsiasi linguaggio supportato. Per ulteriori informazioni sulla creazione di associazioni personalizzate, vedere [creazione di binding di input e output personalizzati](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

## <a name="resources"></a>Risorse
- [Modelli ed espressioni di associazione](./functions-bindings-expressions-patterns.md)
- [Uso del valore restituito della funzione di Azure](./functions-bindings-return-value.md)
- [Come registrare un'espressione di associazione](./functions-bindings-register.md)
- Testing:
  - [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md)
  - [Eseguire manualmente una funzione non attivata da HTTP](functions-manually-run-non-http.md)
- [Gestione degli errori di associazione](./functions-bindings-errors.md)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Registrare le estensioni di binding di funzioni di Azure](./functions-bindings-register.md)
