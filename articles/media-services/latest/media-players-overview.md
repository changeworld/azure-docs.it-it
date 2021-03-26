---
title: Panoramica dei lettori multimediali per servizi multimediali
description: Quali lettori multimediali è possibile usare con servizi multimediali di Azure? Azure Media Player, Shaka e Video.js finora.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 47ef56a770ca9965cef3e50630be4b69342a038d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559860"
---
# <a name="media-players-for-media-services"></a>Lettori multimediali per servizi multimediali

Con servizi multimediali è possibile usare diversi Media Player.

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player è un lettore video per un'ampia gamma di browser e dispositivi. Azure Media Player usa standard di settore come HTML5, MSE (Media Source Extensions) ed EME (Encrypted Media Extensions) per fornire un'esperienza completa di streaming adattivo. Quando questi standard non sono disponibili in un dispositivo o in un browser, Azure Media Player usa flash e Silverlight come tecnologia di fallback. Indipendentemente dalla tecnologia di riproduzione usata, gli sviluppatori hanno un'interfaccia JavaScript unificata per accedere alle API. I contenuti serviti da servizi multimediali di Azure possono essere riprodotti in un'ampia gamma di dispositivi e browser senza sforzi aggiuntivi.

Vedere la [documentazione Azure Media Player](../azure-media-player/azure-media-player-overview.md).

## <a name="shaka"></a>Shaka

Shaka Player è una libreria JavaScript open source per un supporto adattivo. Riproduce formati multimediali adattivi, ad esempio DASH e HLS, in un browser, senza usare plug-in o Flash. Al contrario, il lettore Shaka usa le estensioni dei supporti Open Web Standards e i supporti crittografati.

Vedere [come usare il lettore Shaka con servizi multimediali di Azure](how-to-shaka-player.md).

## <a name="videojs"></a>Video.js

Video.js è un lettore video che riproduce formati multimediali adattivi, ad esempio DASH e HLS, in un browser. Video.js usa le estensioni MediaSource Open Web Standards e i supporti crittografati. Supporta la riproduzione video su desktop e dispositivi mobili. La documentazione ufficiale è reperibile all'indirizzo https://docs.videojs.com/ .

Vedere [come usare il lettore di Video.js con servizi multimediali di Azure](how-to-video-js-player.md).


## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](../azure-media-player/azure-media-player-quickstart.md)