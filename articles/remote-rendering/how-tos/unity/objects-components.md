---
title: Oggetti e componenti di gioco Unity
description: Vengono descritti i metodi specifici di Unity per l'utilizzo di entità e componenti per il rendering remoto.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594147"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagire con gli oggetti e i componenti di gioco Unity

Azure Remote rendering (ARR) è ottimizzato per un numero elevato di oggetti (vedere [limitazioni](../../reference/limits.md)). Sebbene sia possibile gestire gerarchie di grandi dimensioni e complesse nell'host, la replica di tutte le unità in Unity su dispositivi a basso consumo non è fattibile.

Pertanto, quando un modello viene caricato nell'host, il rendering remoto di Azure rispecchia le informazioni sulla struttura del modello nel dispositivo client, che comporterà il traffico di rete, ma non replica gli oggetti e i componenti in Unity. Si prevede invece di richiedere manualmente gli oggetti e i componenti del gioco Unity necessari, in modo che sia possibile limitare l'overhead a ciò che è effettivamente necessario. In questo modo si ha maggiore controllo sulle prestazioni lato client.

Di conseguenza, l'integrazione di Unity del rendering remoto di Azure viene fornita con funzionalità aggiuntive per replicare la struttura di rendering remota su richiesta.

## <a name="load-a-model-in-unity"></a>Caricare un modello in Unity

Quando si carica un modello, si ottiene un riferimento all'oggetto radice del modello caricato. Questo riferimento non è un oggetto gioco Unity, ma è possibile convertirlo in uno usando il metodo di estensione `Entity.GetOrCreateGameObject()` . Questa funzione prevede un argomento di tipo `UnityCreationMode` . Se si passa `CreateUnityComponents` , l'oggetto gioco Unity appena creato verrà popolato con i componenti proxy per tutti i componenti di rendering remoti presenti nell'host. Si consiglia, tuttavia, di preferire `DoNotCreateUnityComponents` , per ridurre il sovraccarico minimo.

### <a name="load-model-with-unity-coroutines"></a>Caricare il modello con coroutine di Unity

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>Modello di caricamento con modello await

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Negli esempi di codice precedenti è stato usato il percorso di caricamento del modello tramite SAS perché il modello incorporato è caricato. L'indirizzamento del modello tramite contenitori BLOB (tramite `LoadModelAsync` e) funziona in modo completo in modo `LoadModelOptions` analogo.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

La creazione di un oggetto gioco Unity aggiunge implicitamente un `RemoteEntitySyncObject` componente all'oggetto Game. Questo componente viene usato per sincronizzare la trasformazione dell'entità con il server. Per impostazione predefinita, è `RemoteEntitySyncObject` necessario che l'utente chiami in modo esplicito `SyncToRemote()` per sincronizzare lo stato di Unity locale con il server. Con `SyncEveryFrame` l'abilitazione, l'oggetto verrà sincronizzato automaticamente.

Per gli oggetti con un `RemoteEntitySyncObject` è possibile creare l'istanza di elementi figlio remoti e visualizzarli nell'editor di Unity tramite il **:::no-loc text="Show children":::** pulsante.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Componenti wrapper

I [componenti](../../concepts/components.md) collegati alle entità di rendering remoto sono esposti a Unity tramite proxy `MonoBehavior` . Questi proxy rappresentano il componente remoto in Unity e inoltrano tutte le modifiche all'host.

Per creare componenti per il rendering remoto del proxy, usare il metodo di estensione `GetOrCreateArrComponent` :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Durate associate

Il ciclo di vita di un' [entità](../../concepts/entities.md) remota e di un oggetto gioco Unity è associato mentre sono associati tramite `RemoteEntitySyncObject` . Se si chiama `UnityEngine.Object.Destroy(...)` con un oggetto gioco di questo tipo, verrà rimossa anche l'entità remota.

Per eliminare definitivamente l'oggetto gioco Unity, senza influire sull'entità remota, è innanzitutto necessario chiamare `Unbind()` su `RemoteEntitySyncObject` .

Lo stesso vale per tutti i componenti proxy. Per eliminare solo la rappresentazione sul lato client, è prima necessario chiamare `Unbind()` sul componente proxy:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Configurare Rendering remoto per Unity](unity-setup.md)
* [Esercitazione: modificare entità remote in Unity](../../tutorials/unity/manipulate-models/manipulate-models.md)
