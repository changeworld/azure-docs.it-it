---
title: Linguaggi supportati in Funzioni di Azure
description: Informazioni sulle lingue supportate (GA) e sulle quali sono disponibili in anteprima e sui modi per estendere lo sviluppo di funzioni ad altre lingue.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: da3100521faae61e9803aaa82dbf91045a7ecbc9
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108552"
---
# <a name="supported-languages-in-azure-functions"></a>Linguaggi supportati in Funzioni di Azure

Questo articolo illustra i livelli di supporto offerti per i linguaggi disponibili in Funzioni di Azure. Vengono inoltre descritte le strategie per la creazione di funzioni utilizzando linguaggi non supportati in modo nativo.

## <a name="levels-of-support"></a>Livelli di supporto

Sono disponibili due livelli di supporto:

* **Disponibile a livello generale** - Il linguaggio è completamente supportato e approvato per l'uso in produzione.
* **Anteprima** - Il linguaggio non è ancora supportato ma si prevede che in futuro diventi disponibile a livello generale.

## <a name="languages-by-runtime-version"></a>Linguaggi per versione runtime 

Sono disponibili [tre versioni del runtime di funzioni di Azure](functions-versions.md) . Nella tabella seguente sono indicati i linguaggi supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers"></a>Gestori personalizzati

I gestori personalizzati sono server Web leggeri che ricevono eventi dall'host di funzioni di Azure. Qualsiasi linguaggio che supporta le primitive HTTP può implementare un gestore personalizzato. Ciò significa che i gestori personalizzati possono essere usati per creare funzioni in linguaggi che non sono ufficialmente supportati. Per altre informazioni, vedere [gestori personalizzati di funzioni di Azure](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Estendibilità di un linguaggio

A partire dalla versione 2. x, il runtime è progettato per offrire l' [estendibilità del linguaggio](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). I linguaggi JavaScript e Java nel runtime 2.x sono costruiti con questa estendibilità.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sullo sviluppo di funzioni nelle lingue supportate, vedere le risorse seguenti:

+ [Riferimenti per sviluppatori della libreria di classi C#](functions-dotnet-class-library.md)
+ [Riferimenti per sviluppatori di script C#](functions-reference-csharp.md)
+ [Riferimenti per sviluppatori Java](functions-reference-java.md)
+ [Riferimenti per sviluppatori JavaScript](functions-reference-node.md)
+ [Guida di riferimento per gli sviluppatori di PowerShell](functions-reference-powershell.md)
+ [Guida di riferimento per sviluppatori Python](functions-reference-python.md)
+ [Informazioni di riferimento per gli sviluppatori TypeScript](functions-reference-node.md#typescript)
