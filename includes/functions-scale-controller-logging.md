---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081096"
---
| Proprietà | Descrizione |
|--|--|
|**`<DESTINATION>`**| Destinazione a cui vengono inviati i log. I valori validi sono `AppInsights` e `Blob`.<br/>Quando si usa `AppInsights` , assicurarsi che [Application Insights sia abilitato nell'app per le funzioni](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>Quando si imposta la destinazione su `Blob` , i log vengono creati in un contenitore BLOB denominato `azure-functions-scale-controller` nell'account di archiviazione predefinito impostato nell' `AzureWebJobsStorage` impostazione dell'applicazione. |
|**`<VERBOSITY>`** | Specifica il livello di registrazione. I valori supportati sono `None`, `Warning` e `Verbose`.<br/>Quando è impostato su `Verbose` , il controller di scalabilità registra un motivo per ogni modifica nel conteggio dei thread di lavoro, nonché informazioni sui trigger che determinano tali decisioni. I log dettagliati includono gli avvisi dei trigger e gli hash usati dai trigger prima e dopo l'esecuzione del controller di scalabilità. |

> [!TIP]
> Tenere presente che, mentre si lascia abilitata la registrazione del controller di scalabilità, questo influisca sui [potenziali costi di monitoraggio dell'app per le funzioni](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Prendere in considerazione l'abilitazione della registrazione fino a quando non sono stati raccolti dati sufficienti per comprendere il comportamento del controller di scalabilità e quindi la relativa disabilitazione.
