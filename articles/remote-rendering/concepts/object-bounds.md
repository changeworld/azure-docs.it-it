---
title: Limiti degli oggetti
description: Illustra il modo in cui è possibile eseguire query sui limiti spaziali degli oggetti
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594485"
---
# <a name="object-bounds"></a>Limiti degli oggetti

I limiti degli oggetti rappresentano il volume occupato da un'[entità](entities.md) e dai relativi elementi figlio. In Rendering remoto di Azure i limiti degli oggetti sono sempre assegnati come *rettangoli delimitatori allineati all'asse* (AABB, Axis Aligned Bounding Box). I limiti degli oggetti possono trovarsi nello *spazio locale* o nello *spazio globale*. In entrambi i casi sono sempre allineati all'asse. Questo significa che le estensioni e il volume della rappresentazione nello spazio locale possono essere diversi da quelli della rappresentazione nello spazio globale.

## <a name="querying-object-bounds"></a>Esecuzione di query sui limiti degli oggetti

Il rettangolo di delimitazione dell'asse locale di una [mesh](meshes.md) può essere sottoposto a query direttamente dalla risorsa mesh. Questi limiti possono essere trasformati nello spazio locale o nello spazio globale di un'entità usando la trasformazione dell'entità.

Con questo metodo è possibile calcolare i limiti di un'intera gerarchia di oggetti, ma è necessario attraversare la gerarchia, eseguire query sui limiti di ogni mesh e unire tali limiti manualmente. È un'operazione noiosa e poco efficiente.

Un metodo più efficiente consiste nel chiamare `QueryLocalBoundsAsync` o `QueryWorldBoundsAsync` su un'entità. Il calcolo viene quindi scaricato nel server e restituito in tempi brevi.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>Documentazione dell'API

* [Entity. QueryLocalBoundsAsync C#](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Entità C++:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Passaggi successivi

* [Query spaziali](../overview/features/spatial-queries.md)