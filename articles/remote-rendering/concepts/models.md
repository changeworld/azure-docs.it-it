---
title: Modelli
description: Descrive l'aspetto di un modello in Rendering remoto di Azure
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593875"
---
# <a name="models"></a>Modelli

In Rendering remoto di Azure, un *modello* fa riferimento a una rappresentazione completa dell'oggetto, costituita da [entità](entities.md) e [componenti](components.md). Costituisce lo strumento principale con cui è possibile trasmettere dati personalizzati al servizio di rendering remoto.

## <a name="model-structure"></a>Struttura del modello

Un modello ha esattamente un'[entità](entities.md) come nodo radice, al di sotto della quale può avere una gerarchia arbitraria di entità figlio. Quando si carica un modello, all'entità radice viene restituito un riferimento.

A ogni entità possono essere associati alcuni [componenti](components.md) che, nel caso più comune, sono costituiti da *MeshComponents*, che fanno riferimento a [risorse mesh](meshes.md).

## <a name="creating-models"></a>Creazione di modelli

Per creare modelli per il runtime è necessario [convertire modelli di input](../how-tos/conversion/model-conversion.md) da file di tipo FBX e GLTF. Il processo di conversione estrae tutte le risorse, quali trame, materiali e mesh, e le converte in formati di runtime ottimizzati. Estrae anche le informazioni strutturali e le converte nella struttura del grafo entità/componente di Rendering remoto di Azure.

> [!IMPORTANT]
> La [conversione di un modello](../how-tos/conversion/model-conversion.md) è l'unico modo per creare [mesh](meshes.md). Sebbene in fase di runtime sia possibile condividere le mesh tra più entità, non esiste altro modo per inserire una mesh nel runtime se non caricando un modello.

## <a name="loading-models"></a>Caricamento di modelli

Dopo essere stato convertito, un modello può essere caricato da Archiviazione BLOB di Azure nel runtime.

Sono disponibili due funzioni di caricamento, che si differenziano per il modo in cui l'asset viene inviato nell'archiviazione BLOB:

* Il modello può essere inviato direttamente tramite parametri di archiviazione BLOB, nel caso in cui l'[archiviazione BLOB sia collegata all'account](../how-tos/create-an-account.md#link-storage-accounts). La funzione di caricamento pertinente in questo caso è `LoadModelAsync` con parametro `LoadModelOptions`.
* Il modello può essere inviato tramite il relativo URI SAS. La funzione di caricamento pertinente è `LoadModelFromSasAsync` con parametro `LoadModelFromSasOptions`. È consigliabile usare questa variante anche quando si caricano [modelli predefiniti](../samples/sample-model.md).

I frammenti di codice seguenti illustrano come caricare i modelli con le due funzioni. Per caricare un modello usando i relativi parametri di archiviazione BLOB, usare un codice simile a quello riportato di seguito:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Se si vuole caricare un modello usando un token di firma di accesso condiviso, usare codice simile al frammento seguente:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Successivamente, è possibile attraversare la gerarchia delle entità e modificare le entità e i componenti. Caricando più volte lo stesso modello vengono create più istanze, ognuna con una copia specifica della struttura entità/componente. Poiché, tuttavia, le mesh, i materiali e le trame sono [risorse condivise](../concepts/lifetime.md), questi dati non vengono caricati di nuovo. Quando si crea più volte l'istanza di un modello, quindi, si verifica un sovraccarico di memoria relativamente ridotto.

## <a name="api-documentation"></a>Documentazione dell'API

* [C# RenderingConnection. LoadModelAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection. LoadModelFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection:: LoadModelAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection:: LoadModelFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Passaggi successivi

* [Entità](entities.md)
* [Mesh](meshes.md)