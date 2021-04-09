---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 01e62685fa73a7f547ef5246b28fdfdf659e7afa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623483"
---
|Linguaggio                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3.1)<br/>[Anteprima (.NET 5,0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (nodo 6)|GA (Node 10 e 8)| GA (nodo 14, 12, & 10) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/D|GA (Java 8)| GA (Java 11 e 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/D|GA (PowerShell Core 6)| GA (PowerShell 7 e Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/D|GA (Python 3.7 e 3.6)| GA (Python 3.8, 3.7 e 3.6) <br/> Anteprima (Python 3,9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |N/D|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> le app della libreria di classi .NET destinate a Runtime versione 2. x possono ora essere eseguite in .net core 3,1 nella modalità di compatibilità di .NET Core 2. x. Per altre informazioni, vedere [considerazioni sulle funzioni V2. x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Supportato tramite compilazione da sorgente a sorgente di JavaScript.

Per altre informazioni sulle versioni supportate dei linguaggi, vedere l'articolo della guida per sviluppatori del linguaggio specifico.   
Per informazioni sulle modifiche previste per il supporto dei linguaggi di programmazione, vedere [Roadmap per Azure](https://azure.microsoft.com/roadmap/?tag=functions).
