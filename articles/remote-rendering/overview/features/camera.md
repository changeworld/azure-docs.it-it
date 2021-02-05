---
title: Fotocamera
description: Descrive le impostazioni della fotocamera e i casi d'uso
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594130"
---
# <a name="camera"></a>Fotocamera

Per assicurarsi che il contenuto sottoposto a rendering in locale e in remoto possa essere composto senza interruzioni, diverse proprietà della fotocamera devono rimanere sincronizzate tra server e client. In particolare la trasformazione e la proiezione della fotocamera. Ad esempio, il contenuto sottoposto a rendering in locale deve usare la stessa trasformazione della fotocamera e la stessa proiezione con cui è stato eseguito il rendering dell'ultimo frame remoto.

![Fotocamera locale e remota](./media/camera.png)

Nell'immagine precedente la fotocamera locale è stata spostata rispetto al frame remoto inviato dal server. Di conseguenza, è necessario eseguire il rendering del contenuto locale dalla stessa prospettiva del frame remoto e infine l'immagine risultante viene riproiettata nello spazio locale della fotocamera, descritta in dettaglio nella [riproiezione della fase finale](late-stage-reprojection.md).

Il ritardo tra il rendering remoto e quello locale significa che qualsiasi modifica di queste impostazioni viene applicata con un ritardo. Pertanto, qualsiasi nuovo valore non può essere utilizzato immediatamente nello stesso frame.

Nelle impostazioni della fotocamera è possibile impostare le distanze del piano vicino e lontano. In HoloLens 2, i dati di trasformazione e proiezione rimanenti vengono definiti dall'hardware. Quando si usa la [simulazione desktop](../../concepts/graphics-bindings.md), questi vengono impostati tramite l'input della relativa `Update` funzione.

I valori modificati possono essere usati localmente non appena viene eseguito il primo frame remoto di cui è stato eseguito il rendering. In HoloLens 2 i dati da usare per il rendering vengono forniti da *HolographicFrame* proprio come in qualsiasi altra applicazione olografica. Nella [simulazione del desktop](../../concepts/graphics-bindings.md)vengono recuperate tramite l'output della `Update` funzione.

## <a name="camera-settings"></a>Impostazioni della fotocamera

Le proprietà seguenti possono essere modificate nelle impostazioni della fotocamera:

**Piano vicino e lontano:**

Per assicurarsi che non sia possibile impostare intervalli non validi, le proprietà **NearPlane** e **FarPlane** sono di sola lettura e per modificare l'intervallo esiste una funzione separata **SetNearAndFarPlane** . Questi dati verranno inviati al server al termine del frame. Quando si impostano questi valori, **NearPlane** deve essere minore di **FarPlane**. In caso contrario, si verificherà un errore.

> [!IMPORTANT]
> In Unity, questo viene gestito automaticamente quando si modifica la videocamera principale vicino a un piano e un altro.

**EnableDepth**:

A volte è utile disabilitare la scrittura del buffer di profondità dell'immagine remota a scopo di debug. La disabilitazione della profondità impedirà inoltre al server di inviare dati di profondità, riducendo così la larghezza di banda.

> [!TIP]
> In Unity è disponibile un componente di debug denominato **EnableDepthComponent** che può essere usato per abilitare o disabilitare questa funzionalità nell'interfaccia utente dell'editor.

**InverseDepth**:

> [!NOTE]
> Questa impostazione è importante solo se `EnableDepth` è impostato su `true` . In caso contrario, questa impostazione non ha alcun effetto.

I buffer di profondità registrano in genere i valori z in un intervallo a virgola mobile di [0; 1], con 0 che indica la profondità del piano più vicino e 1 che indica la profondità del piano lontano. È anche possibile invertire questo intervallo e i valori di profondità record nell'intervallo [1; 0], ovvero la profondità del piano più vicino diventa 1 e la profondità del piano lontano diventa 0. In genere, il secondo miglioramento della distribuzione della precisione a virgola mobile nell'intervallo z non lineare.

> [!WARNING]
> Un approccio comune consiste nell'invertire i valori dei piani vicini e lontani sugli oggetti della fotocamera. Questa operazione avrà esito negativo per il rendering remoto di Azure con un errore durante il tentativo di eseguire questa operazione su `CameraSettings` .

L'API per il rendering remoto di Azure deve conoscere la convenzione del buffer di profondità del renderer locale per comporre correttamente la profondità remota nel buffer di profondità locale. Se l'intervallo del buffer di profondità è [0; 1] lasciare questo flag come `false` . Se si usa un buffer di profondità invertito con un intervallo [1; 0], impostare il `InverseDepth` flag su `true` .

> [!NOTE]
> Per Unity, l'impostazione corretta è già applicata da, `RenderingConnection` quindi non è necessario l'intervento manuale.

La modifica delle impostazioni della fotocamera può essere eseguita come indicato di seguito:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>Documentazione dell'API

* [CameraSettings C#](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [CameraSettings C++](/cpp/api/remote-rendering/camerasettings)
* [Funzione C# GraphicsBindingSimD3d11. Update](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [Funzione C++ GraphicsBindingSimD3d11:: Update](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Passaggi successivi

* [Binding di grafica](../../concepts/graphics-bindings.md)
* [Riproiezione con ritardo della fase](late-stage-reprojection.md)