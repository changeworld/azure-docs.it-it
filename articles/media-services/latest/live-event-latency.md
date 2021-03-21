---
title: Impostazioni Live a bassa latenza in servizi multimediali di Azure
description: Questo argomento offre una panoramica delle impostazioni di Live bassa latenza e Mostra come impostare una bassa latenza.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: f9f4476e932df9a5f4c093968b2e7c4840e7ff39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122902"
---
# <a name="live-event-low-latency-settings"></a>Impostazioni di bassa latenza eventi Live

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo illustra come impostare una bassa latenza in un [evento live](/rest/api/media/liveevents). Esamina anche alcuni risultati tipici visualizzati quando si usano le impostazioni di bassa latenza in diversi lettori. I risultati variano a seconda della rete CDN e della latenza di rete.

Per usare la nuova funzionalità **LowLatency** impostare il flag **StreamOptionsFlag** su **LowLatency** nel **LiveEvent**. Quando si crea [LiveOutput](/rest/api/media/liveoutputs) per la riproduzione HLS, impostare [LiveOutput.Hls.fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) su 1. Quando il flusso è in esecuzione, è possibile usare la [Azure Media Player](https://ampdemo.azureedge.net/) (pagina demo amp) e impostare le opzioni di riproduzione per usare il "profilo euristica a bassa latenza".

> [!NOTE]
> Attualmente, il LowLatency HeuristicProfile in Azure Media Player è progettato per la riproduzione di flussi nel protocollo MPEG-DASH, con formato CSF o CMAF (ad esempio, `format=mdp-time-csf` o `format=mdp-time-cmaf` ). 

L'esempio di .NET seguente mostra come impostare **LowLatency** nel **LiveEvent**:

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#NewLiveEvent)]

Vedere l'esempio completo: [evento live con DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs).

## <a name="live-events-latency"></a>Latenza di eventi live

La tabella seguente mostra i risultati tipici della latenza (quando è attivato il flag LowLatency) in Servizi multimediali, misurati dal momento in cui il feed di contributo raggiunge il servizio al momento in cui un utente vede la riproduzione sul lettore. Per un uso ottimale della bassa latenza, è consigliabile ridurre le impostazioni del codificatore fino alla durata di un 1 secondo GOP (Group Of Pictures). Quando si usa una durata GOP maggiore, ridurre al minimo il consumo di larghezza di banda e ridurre la velocità in bit nella stessa frequenza dei fotogrammi. È particolarmente utile nei video con meno movimento.

### <a name="pass-through"></a>Pass-through 

||Bassa latenza (GOP 2 secondi) abilitata|Bassa latenza (GOP 1 secondo) abilitata|
|---|---|---|
|**DASH in AMP**|10 secondi|8 secondi|
|**HLS nel lettore iOS nativo**|14 secondi|10 secondi|

### <a name="live-encoding"></a>Codifica live

||Bassa latenza (GOP 2 secondi) abilitata|Bassa latenza (GOP 1 secondo) abilitata|
|---|---|---|
|**DASH in AMP**|14 secondi|10 secondi|
|**HLS nel lettore iOS nativo**|18s|13s|

> [!NOTE]
> La latenza end-to-end può variare in base alle condizioni della rete locale o con l'introduzione di un livello di memorizzazione nella cache della rete CDN. È consigliabile testare l'esatta configurazione in uso.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
