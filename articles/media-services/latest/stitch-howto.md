---
title: Come unire due o più file video con .NET | Microsoft Docs
description: Questo articolo illustra come unire due o più file video.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111454"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Come unire due o più file video con .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Unire due o più file video

Nell'esempio seguente viene illustrato come è possibile generare un set di impostazioni per unire due o più file video. Lo scenario più comune è l'aggiunta di un'intestazione o una sequenza finale al video principale.

> [!NOTE]
> I file video modificati insieme devono condividere proprietà (risoluzione video, frequenza dei fotogrammi, conteggio tracce audio e così via). Prestare attenzione a non combinare video con frequenze dei fotogrammi diverse o con un numero diverso di tracce audio.

## <a name="prerequisites"></a>Prerequisiti

Clonare o scaricare gli [esempi .NET di servizi multimediali](https://github.com/Azure-Samples/media-services-v3-dotnet/).  Il codice a cui si fa riferimento di seguito si trova nella [cartella EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)

### <a name="net-code"></a>Codice .NET

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
