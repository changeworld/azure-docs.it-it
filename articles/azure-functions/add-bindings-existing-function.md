---
title: Connetti funzioni ad altri servizi di Azure
description: Informazioni su come aggiungere binding che si connettono ad altri servizi di Azure a una funzione esistente nel progetto di funzioni di Azure.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258202"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>Connettere le funzioni ai servizi di Azure usando i binding

Quando si crea una funzione, il codice del trigger specifico della lingua viene aggiunto nel progetto da un set di modelli di trigger. Se si vuole connettere la funzione ad altri servizi usando binding di input o di output, è necessario aggiungere definizioni di binding specifiche nella funzione. Per altre informazioni sui binding, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md).

## <a name="local-development"></a>Sviluppo locale       

Quando si sviluppano funzioni localmente, è necessario aggiornare il codice della funzione per aggiungere associazioni. L'uso di Visual Studio Code può semplificare l'aggiunta di associazioni a una funzione.  

### <a name="visual-studio-code"></a>Visual Studio Code

Quando si usa Visual Studio Code per sviluppare la funzione e la funzione usa un function.jssu file, l'estensione di funzioni di Azure può aggiungere automaticamente un'associazione a un function.jsesistente sul file. Per altre informazioni, vedere [aggiungere binding di input e output](functions-develop-vs-code.md#add-input-and-output-bindings).   

### <a name="manually-add-bindings-based-on-examples"></a>Aggiungere manualmente associazioni basate su esempi

Quando si aggiunge un'associazione a una funzione esistente, è necessario aggiornare sia il codice della funzione sia il function.jsnel file di configurazione, se usato dal linguaggio. Sia la libreria di classi .NET che le funzioni Java usano gli attributi anziché function.json, quindi è necessario aggiornarla.

Usare la tabella seguente per trovare esempi di tipi di binding specifici che è possibile usare per l'aggiornamento di una funzione esistente. Per prima cosa, scegliere la scheda lingua corrispondente al progetto. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Portale di Azure

Quando si sviluppano le funzioni nella [portale di Azure](https://portal.azure.com), è possibile aggiungere associazioni di input e di output nella scheda **integrazione** per una determinata funzione. I nuovi binding vengono aggiunti all'function.jssu file o agli attributi del metodo, a seconda del linguaggio. Gli articoli seguenti illustrano alcuni esempi di come aggiungere binding a una funzione esistente nel portale:

+ [Associazione di output dell'archiviazione code](functions-integrate-storage-queue-output-binding.md)
+ [Binding di output Azure Cosmos DB](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Passaggi successivi

+ [Concetti relativi a trigger e associazioni in Funzioni di Azure](functions-triggers-bindings.md)
