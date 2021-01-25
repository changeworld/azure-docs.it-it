---
title: Hosting del piano a consumo di funzioni di Azure
description: Informazioni sul modo in cui l'hosting del piano a consumo di funzioni di Azure consente di eseguire il codice in un ambiente scalabile dinamicamente, ma si paga solo per le risorse usate durante l'esecuzione.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760537"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Hosting del piano a consumo di funzioni di Azure

Quando si usa il piano a consumo, le istanze dell'host di funzioni di Azure vengono aggiunte e rimosse in modo dinamico in base al numero di eventi in ingresso. Il piano a consumo è l'opzione di hosting completamente senza <em>Server</em> per funzioni di Azure.

## <a name="benefits"></a>Vantaggi

Il piano a consumo viene ridimensionato automaticamente, anche durante i periodi di carico elevato. Quando si eseguono funzioni in un piano a consumo, vengono addebitate le risorse di calcolo solo quando le funzioni sono in esecuzione. In un piano A consumo, il timeout dell'esecuzione di una funzione si verifica dopo un periodo di tempo configurabile.

Per un confronto tra il piano a consumo e gli altri tipi di piano e hosting, vedere [scalabilità delle funzioni e opzioni di hosting](functions-scale.md).

## <a name="billing"></a>Fatturazione

La fatturazione si basa sul numero di esecuzioni, il tempo di esecuzione e la memoria usata. L'utilizzo viene aggregato in tutte le funzioni all'interno di un'app per le funzioni. Per altre informazioni, vedere la [pagina relativa ai prezzi per Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/).

Per altre informazioni su come stimare i costi durante l'esecuzione in un piano a consumo, vedere [informazioni sui costi del piano a consumo](functions-consumption-costs.md).

## <a name="create-a-consumption-plan-function-app"></a>Creare un'app per le funzioni del piano a consumo

Quando si crea un'app per le funzioni nella portale di Azure, il piano a consumo è l'impostazione predefinita. Quando si usano le API per creare l'app per le funzioni, non è necessario creare prima un piano di servizio app come avviene con i piani Premium e dedicati.

Usare i collegamenti seguenti per informazioni su come creare un'app per le funzioni senza server in un piano a consumo, a livello di codice o nel portale di Azure:

+ [Interfaccia della riga di comando di Azure](./scripts/functions-cli-create-serverless.md)
+ [Azure portal](./functions-get-started.md)
+ [Modello di Azure Resource Manager](functions-create-first-function-resource-manager.md)

È anche possibile creare app per le funzioni in un piano a consumo quando si pubblica un progetto di funzioni da [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) o [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure).

## <a name="multiple-apps-in-the-same-plan"></a>Più app nello stesso piano

Le app per le funzioni nella stessa area possono essere assegnate allo stesso piano a consumo. Non ci sono svantaggi o conseguenze per l'esecuzione di più app nello stesso piano a consumo. L'assegnazione di più app allo stesso piano a consumo non ha alcun effetto sulla resilienza, la scalabilità o l'affidabilità di ogni app.

## <a name="next-steps"></a>Passaggi successivi

+ [Opzioni di hosting di funzioni di Azure](functions-scale.md)
+ [Scalabilità basata sugli eventi in funzioni di Azure](event-driven-scaling.md)
