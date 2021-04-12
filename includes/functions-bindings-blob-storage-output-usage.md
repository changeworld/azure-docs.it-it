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
ms.openlocfilehash: e375a12be73c280f2778e6e28efb709b9116a4cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100381656"
---
### <a name="default"></a>Predefinito

Per scrivere i BLOB, è possibile associare i tipi seguenti:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> richiede l'associazione "in" `direction` in *function.json* o `FileAccess.Read` in una libreria di classi C#. Tuttavia, è possibile usare l'oggetto contenitore che il runtime fornisce per scrivere operazioni, come ad esempio il caricamento di BLOB nel contenitore.

<sup>2</sup> richiede l'associazione "inout" `direction` in *function.json* o `FileAccess.ReadWrite` in una libreria di classi C#.

Se si prova a eseguire l'associazione a uno dei tipi di Storage SDK e si riceve un messaggio di errore, assicurarsi di fare riferimento alla [versione corretta di Storage SDK](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

L'associazione a `string` o `Byte[]` è consigliabile solo se le dimensioni del BLOB sono ridotte, in quanto l'intero contenuto del BLOB viene caricato in memoria. In genere, è preferibile usare un tipo `Stream` o `CloudBlockBlob`. Per altre informazioni, vedere [Concorrenza e utilizzo della memoria](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) più indietro in questo articolo.

### <a name="additional-types"></a>Tipi aggiuntivi

Anche le app che usano la [versione 5.0.0 o successiva dell'estensione di archiviazione](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) possono usare i tipi di [Azure SDK per .NET](/dotnet/api/overview/azure/storage.blobs-readme). Questa versione Elimina il supporto per i `CloudBlobContainer` tipi legacy,,,, `CloudBlobDirectory` `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` e `CloudAppendBlob` a favore dei tipi seguenti:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> richiede l'associazione "in" `direction` in *function.json* o `FileAccess.Read` in una libreria di classi C#. Tuttavia, è possibile usare l'oggetto contenitore che il runtime fornisce per scrivere operazioni, come ad esempio il caricamento di BLOB nel contenitore.

<sup>2</sup> richiede l'associazione "inout" `direction` in *function.json* o `FileAccess.ReadWrite` in una libreria di classi C#.

Per esempi relativi all'uso di questi tipi, vedere [il repository GitHub per l'estensione](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).
