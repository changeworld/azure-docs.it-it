---
title: Componenti
description: Definizione di componenti nell'ambito del rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592195"
---
# <a name="components"></a>Componenti

Il rendering remoto di Azure usa il modello di [Sistema componente entità](https://en.wikipedia.org/wiki/Entity_component_system). Mentre le [entità](entities.md) rappresentano la posizione e la composizione gerarchica degli oggetti, i componenti sono responsabili dell'implementazione del comportamento.

I tipi di componenti usati più di frequente sono [:::no-loc text="mesh components":::](meshes.md) , che aggiungono mesh alla pipeline di rendering. Analogamente, vengono usati [componenti luminosi](../overview/features/lights.md) per aggiungere un'illuminazione e [componenti del piano di taglio](../overview/features/cut-planes.md), per tagliare le mesh aperte.

Tutti questi componenti usano come punto di riferimento la trasformazione (posizione, rotazione, scala) dell'entità a cui sono collegati.

## <a name="working-with-components"></a>Uso dei componenti

È possibile aggiungere, rimuovere e modificare facilmente i componenti a livello programmatico:

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

Un componente è associato a un'entità al momento della creazione e non può essere spostato successivamente in un'altra entità. I componenti vengono eliminati in modo esplicito con `Component.Destroy()` o automaticamente, quando l'entità proprietaria del componente viene distrutta.

È possibile aggiungere a un'entità una sola istanza di ogni tipo di componente alla volta.

## <a name="unity-specific"></a>Indicazioni specifiche per Unity

L'integrazione di Unity dispone di funzioni di estensione aggiuntive per l'interazione con i componenti. Vedere [Oggetti e componenti di gioco Unity](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>Documentazione dell'API

* [ComponentBase C#](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection. CreateComponent ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [Entity. FindComponentOfType C# ()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [ComponentBase C++](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection:: CreateComponent ()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [Entità C++:: FindComponentOfType ()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Passaggi successivi

* [Limiti degli oggetti](object-bounds.md)
* [Mesh](meshes.md)