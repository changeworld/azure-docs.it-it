---
title: Trigger e associazioni di archiviazione BLOB di Azure per funzioni di Azure
description: Informazioni su come usare il trigger e le associazioni di archiviazione BLOB di Azure in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381529"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Panoramica dei binding dell'archiviazione BLOB di Azure per funzioni di Azure

Funzioni di Azure si integra con [archiviazione di Azure](../storage/index.yml) tramite [trigger e associazioni](./functions-triggers-bindings.md). L'integrazione con archiviazione BLOB consente di compilare funzioni che reagiscono alle modifiche nei dati BLOB, nonché i valori di lettura e scrittura.

| Azione | Tipo |
|---------|---------|
| Eseguire una funzione come modifiche ai dati di archiviazione BLOB | [Trigger](./functions-bindings-storage-blob-trigger.md) |
| Leggere dati di archiviazione BLOB in una funzione | [Binding di input](./functions-bindings-storage-blob-input.md) |
| Consentire a una funzione di scrivere dati di archiviazione BLOB |[Binding di output](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Commenti 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) è consigliata per l'uso con Visual Studio Code. |
| Script C# (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

#### <a name="storage-extension-5x-and-higher"></a>Estensione di archiviazione 5. x e versioni successive

Una nuova versione dell'estensione delle associazioni di archiviazione è disponibile come [pacchetto NuGet di anteprima](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Questa versione di anteprima introduce la possibilità di [connettersi usando un'identità invece di un segreto](./functions-reference.md#configure-an-identity-based-connection). Per le applicazioni .NET, vengono modificati anche i tipi a cui è possibile eseguire l'associazione, sostituendo i tipi da `WindowsAzure.Storage` e `Microsoft.Azure.Storage` con i tipi più recenti da [Azure. storage. Blobs](/dotnet/api/azure.storage.blobs).

> [!NOTE]
> Il pacchetto di anteprima non è incluso in un bundle di estensione e deve essere installato manualmente. Per le applicazioni .NET, aggiungere un riferimento al pacchetto. Per tutti gli altri tipi di app, vedere [aggiornare le estensioni].

[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aggiornare le estensioni]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Le app di funzioni 1. x hanno automaticamente un riferimento al pacchetto NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>impostazioni host.json

> [!NOTE]
> Questa sezione non si applica quando si usano versioni di estensione precedenti a 5.0.0. Per tali versioni non sono disponibili impostazioni di configurazione globali per i BLOB.

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione quando si usa la [versione dell'estensione 5.0.0 e versioni successive](#storage-extension-5x-and-higher). Il *host.jsdi esempio nel* file seguente contiene solo le impostazioni versione 2. x + per questa associazione. Per altre informazioni sulle impostazioni di configurazione globali nelle funzioni versione 2. x e successive, vedere [host.jsdi riferimento per funzioni di Azure](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (numero di core disponibili)|Numero intero di chiamate simultanee consentite per ogni funzione attivata da BLOB. Il valore minimo consentito è 1.|

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione quando i dati dell'archivio BLOB cambiano](./functions-bindings-storage-blob-trigger.md)
- [Leggere i dati di archiviazione BLOB quando viene eseguita una funzione](./functions-bindings-storage-blob-input.md)
- [Scrivere dati di archiviazione BLOB da una funzione](./functions-bindings-storage-blob-output.md)
