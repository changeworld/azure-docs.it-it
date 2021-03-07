---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d09d69db33cadee1b5a214e9cb2f09a508f37242
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431848"
---
|Linguaggio                                 |1.x         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3.1)<br/>[Anteprima (.NET 5,0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (nodo 6)|GA (Node 10 e 8)| GA (Node 12 e 10)<br />Anteprima (Node 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4.7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3.1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|N/D|GA (Java 8)| GA (Java 11 e 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |N/D|GA (PowerShell Core 6)| GA (PowerShell 7 e Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|N/D|GA (Python 3.7 e 3.6)| GA (Python 3.8, 3.7 e 3.6) <br/> Anteprima (Python 3,9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |N/D|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> le app della libreria di classi .NET destinate a Runtime versione 2. x possono ora essere eseguite in .net core 3,1 nella modalità di compatibilità di .NET Core 2. x. Per altre informazioni, vedere [considerazioni sulle funzioni V2. x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> Supportato tramite compilazione da sorgente a sorgente di JavaScript.

Per altre informazioni sulle versioni supportate dei linguaggi, vedere l'articolo della guida per sviluppatori del linguaggio specifico.   
Per informazioni sulle modifiche previste per il supporto dei linguaggi di programmazione, vedere [Roadmap per Azure](https://azure.microsoft.com/roadmap/?tag=functions).
