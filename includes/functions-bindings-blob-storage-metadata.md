---
title: includere file
description: includere file
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455916"
---
Il trigger del BLOB contiene diverse proprietà di metadati. Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Questi valori hanno la stessa semantica del tipo [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob).

|Proprietà  |Type  |Descrizione  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Percorso del BLOB trigger.|
|`Uri`|`System.Uri`|L'URI del BLOB per la posizione primaria.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Le proprietà di sistema del BLOB. |
|`Metadata` |`IDictionary<string,string>`|I metadati definiti dall'utente per il BLOB.|

Ad esempio, gli esempi seguenti di script C# e JavaScript registrano il percorso nel BLOB di attivazione, incluso il contenitore:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```