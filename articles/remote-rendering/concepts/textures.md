---
title: Trame
description: Flusso di lavoro delle risorse trama
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: e01ddf0690f11d41021e0a5ae5958c7c80646743
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594418"
---
# <a name="textures"></a>Trame

Le trame sono una [risorsa condivisa](../concepts/lifetime.md) non modificabile. È possibile caricare trame da una [risorsa di archiviazione BLOB](../how-tos/conversion/blob-storage.md) e applicarle direttamente ai modelli, come illustrato nell'esercitazione [: Modificare l'ambiente e i materiali](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md). In genere, tuttavia, le trame faranno parte di un [modello convertito](../how-tos/conversion/model-conversion.md), in cui si fa riferimento ad esse con i [materiali](materials.md).

## <a name="texture-types"></a>Tipi di trama

Tipi di trama diversi hanno casi d'uso diversi:

* Le **trame 2D** vengono usate principalmente nei [materiali](materials.md).
* Le **mappe cubi** possono essere usate per il [cielo](../overview/features/sky.md).

## <a name="supported-texture-formats"></a>Formati di trama supportati

Tutte le trame fornite ad ARR devono essere in formato [DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface). Preferibilmente con mipmap e la compressione della trama.

## <a name="loading-textures"></a>Caricamento delle trame

Quando si carica una trama, è necessario specificarne il tipo previsto. Se il tipo non corrisponde, il caricamento della trama non riesce.
Il caricamento di una trama con lo stesso URI due volte restituirà lo stesso oggetto trama, perché si tratta di una [risorsa condivisa](../concepts/lifetime.md).

Analogamente al caricamento dei modelli, sono disponibili due varianti di indirizzamento di una risorsa trama nella risorsa di archiviazione BLOB di origine:

* La trama può essere inviata direttamente tramite parametri della risorsa di archiviazione BLOB, nel caso in cui la [risorsa di archiviazione BLOB sia collegata all'account](../how-tos/create-an-account.md#link-storage-accounts). La funzione di caricamento pertinente in questo caso è `LoadTextureAsync` con parametro `LoadTextureOptions`.
* La risorsa trama può essere inviata tramite il relativo URI SAS. La funzione di caricamento pertinente è `LoadTextureFromSasAsync` con parametro `LoadTextureFromSasOptions`. È consigliabile usare questa variante anche quando si caricano [trame predefinite](../overview/features/sky.md#built-in-environment-maps).

Il codice di esempio seguente mostra come caricare una trama:

```cs
async void LoadMyTexture(RenderingSession session, string storageContainer, string blobName, string assetPath)
{
    try
    {
        LoadTextureOptions options = new LoadTextureOptions(storageContainer, blobName, assetPath, TextureType.Texture2D);
        Texture texture = await session.Connection.LoadTextureAsync(options);
    
        // use texture...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void LoadMyTexture(ApiHandle<RenderingSession> session, std::string storageContainer, std::string blobName, std::string assetPath)
{
    LoadTextureOptions params;
    params.TextureType = TextureType::Texture2D;
    params.Blob.StorageAccountName = std::move(storageContainer);
    params.Blob.BlobContainerName = std::move(blobName);
    params.Blob.AssetPath = std::move(assetPath);
    session->Connection()->LoadTextureAsync(params, [](Status status, ApiHandle<Texture> texture)
    {
        // use texture...
    });
}
```

Si noti che in caso di utilizzo della relativa variante SAS solo la funzione o il parametro di caricamento è diverso.

A seconda del tipo di trama da usare, è possibile che siano presenti restrizioni per il tipo di trama e il contenuto. La mappa di rugosità di un [materiale PBR](../overview/features/pbr-materials.md), ad esempio, deve essere in scala di grigi.

## <a name="api-documentation"></a>Documentazione dell'API

* [Classe trama C#](/dotnet/api/microsoft.azure.remoterendering.texture)
* [C# RenderingConnection. LoadTextureAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtextureasync)
* [C# RenderingConnection. LoadTextureFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadtexturefromsasasync)
* [Classe trama C++](/cpp/api/remote-rendering/texture)
* [C++ RenderingConnection:: LoadTextureAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtextureasync)
* [C++ RenderingConnection:: LoadTextureFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadtexturefromsasasync)

## <a name="next-steps"></a>Passaggi successivi

* [Materiali](materials.md)
* [Cielo](../overview/features/sky.md)