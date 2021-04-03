---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 906413d0a6702e6146779f79d628b5cebf383af1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92165767"
---
| | |
|--|--|
|**`<DESTINATION>`**| Destinazione a cui vengono inviati i log. I valori validi sono `AppInsights` e `Blob`.<br/>Quando si usa `AppInsights` , assicurarsi che [Application Insights sia abilitato nell'app per le funzioni](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>Quando si imposta la destinazione su `Blob` , i log vengono creati in un contenitore BLOB denominato `azure-functions-scale-controller` nell'account di archiviazione predefinito impostato nell' `AzureWebJobsStorage` impostazione dell'applicazione. |
|**`<VERBOSITY>`** | Specifica il livello di registrazione. I valori supportati sono `None`, `Warning` e `Verbose`.<br/>Quando è impostato su `Verbose` , il controller di scalabilità registra un motivo per ogni modifica nel conteggio dei thread di lavoro, nonché informazioni sui trigger che determinano tali decisioni. I log dettagliati includono gli avvisi dei trigger e gli hash usati dai trigger prima e dopo l'esecuzione del controller di scalabilità. |

> [!TIP]
> Tenere presente che, mentre si lascia abilitata la registrazione del controller di scalabilità, questo influisca sui [potenziali costi di monitoraggio dell'app per le funzioni](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Prendere in considerazione l'abilitazione della registrazione fino a quando non sono stati raccolti dati sufficienti per comprendere il comportamento del controller di scalabilità e quindi la relativa disabilitazione.