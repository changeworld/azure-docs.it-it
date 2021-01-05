---
title: Binding di Azure RabbitMQ per funzioni di Azure
description: Informazioni su come inviare trigger e associazioni di Azure RabbitMQ in funzioni di Azure.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 2a480f25821f5022295b18ca24abfd2c0fb8a50c
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746525"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Cenni preliminari sulle associazioni RabbitMQ per funzioni di Azure

> [!NOTE]
> Le associazioni RabbitMQ sono completamente supportate solo su piani **Premium e dedicati** . Il consumo non è supportato.

Funzioni di Azure si integra con [RabbitMQ](https://www.rabbitmq.com/) tramite [trigger e associazioni](./functions-triggers-bindings.md). L'estensione RabbitMQ di funzioni di Azure consente di inviare e ricevere messaggi usando l'API RabbitMQ con le funzioni.

| Azione | Type |
|---------|---------|
| Esegui una funzione quando un messaggio RabbitMQ passa attraverso la coda | [Trigger](./functions-bindings-rabbitmq-trigger.md) |
| Invia messaggi RabbitMQ |[Binding di output](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

Per iniziare a sviluppare con questa estensione, assicurarsi di [configurare prima un endpoint RabbitMQ](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Per altre informazioni su RabbitMQ, vedere la [pagina](https://www.rabbitmq.com/getstarted.html)introduttiva.

### <a name="functions-3x-and-higher"></a>Funzioni 3. x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Osservazioni
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 4. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure] è consigliata per l'uso con Visual Studio Code. |
| Script C# (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Aggiornare le estensioni]: ./functions-bindings-register.md
[Estensione degli strumenti di Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Funzioni 1. x e 2. x

Le estensioni di binding RabbitMQ non sono supportate per le funzioni 1. x e 2. x. Usare le funzioni 3. x e versioni successive.

## <a name="next-steps"></a>Passaggi successivi

- [Esegui una funzione quando viene creato un messaggio RabbitMQ (trigger)](./functions-bindings-rabbitmq-trigger.md)
- [Inviare messaggi RabbitMQ da funzioni di Azure (associazione di output)](./functions-bindings-rabbitmq-output.md)