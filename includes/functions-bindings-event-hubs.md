---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623558"
---
## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Commenti 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure] è consigliata per l'uso con Visual Studio Code. |
| Script C# (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[Bundle di estensione]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Aggiornare le estensioni]: ../articles/azure-functions/functions-bindings-register.md
[Estensione degli strumenti di Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Estensione di hub eventi 5. x e versioni successive

Una nuova versione dell'estensione di binding di hub eventi è disponibile come [pacchetto NuGet di anteprima](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1). Questa versione di anteprima introduce la possibilità di [connettersi usando un'identità invece di un segreto](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection). Per le applicazioni .NET, vengono modificati anche i tipi a cui è possibile eseguire l'associazione, sostituendo i tipi da `Microsoft.Azure.EventHubs` con i tipi più recenti da [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs).

> [!NOTE]
> Il pacchetto di anteprima non è incluso in un bundle di estensione e deve essere installato manualmente. Per le applicazioni .NET, aggiungere un riferimento al pacchetto. Per tutti gli altri tipi di app, vedere [aggiornare le estensioni].

[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Aggiornare le estensioni]: ./functions-bindings-register.md
[Estensione degli strumenti di Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Le app di funzioni 1. x hanno automaticamente un riferimento al pacchetto NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2. x.

## <a name="hostjson-settings"></a>impostazioni host.json
<a name="host-json"></a>

Il file [host.json](../articles/azure-functions/functions-host-json.md#eventhub) contiene le impostazioni che controllano il comportamento del trigger per Hub eventi. La configurazione varia a seconda della versione di Funzioni di Azure.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]