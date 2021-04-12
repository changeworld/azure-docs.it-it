---
title: Criteri di streaming in servizi multimediali di Azure
description: Questo articolo illustra le caratteristiche dei criteri di streaming e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: 82f01b4216af8d924354ae9e9a204a8eaefb8213
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120844"
---
# <a name="streaming-policies"></a>Criteri di streaming

In Servizi multimediali di Azure v3, i [criteri di streaming](/rest/api/media/streamingpolicies) consentono di definire i protocolli di streaming e le opzioni di crittografia per [StreamingLocators](streaming-locators-concept.md). Media Services V3 fornisce alcuni criteri di streaming predefiniti che consentono di usarli direttamente per la valutazione o la produzione. 

Criteri di streaming predefiniti attualmente disponibili:<br/>
* ' Predefined_DownloadOnly '
* ' Predefined_ClearStreamingOnly '
* ' Predefined_DownloadAndClearStreaming '
* ' Predefined_ClearKey '
* ' Predefined_MultiDrmCencStreaming ' 
* ' Predefined_MultiDrmStreaming '

Il seguente "albero delle decisioni" consente di scegliere un criterio di streaming predefinito per lo scenario.

> [!IMPORTANT]
> * Le proprietà dei **criteri di streaming** di tipo Datetime sono sempre in formato UTC.
> * È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming tutte le volte che si devono usare le stesse opzioni. Per altre informazioni, vedere [quote e limiti](limits-quotas-constraints-reference.md).

## <a name="decision-tree"></a>Albero delle decisioni

Fare clic sull'immagine per visualizzarla a schermo intero.  

[![Diagramma che mostra un albero delle decisioni progettato per facilitare la scelta di un criterio di streaming predefinito per lo scenario.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

Se si crittografa il contenuto, è necessario creare un [criterio della chiave](drm-content-key-policy-concept.md)simmetrica. i **criteri della chiave** simmetrica non sono necessari per cancellare lo streaming o il download. 

Se si dispone di requisiti speciali (ad esempio, se si desidera specificare protocolli diversi, è necessario utilizzare un servizio di distribuzione delle chiavi personalizzato oppure è necessario utilizzare una traccia audio non crittografata), è possibile [creare](/rest/api/media/streamingpolicies/create) un criterio di flusso personalizzato. 

## <a name="get-a-streaming-policy-definition"></a>Ottenere una definizione di criteri di flusso  

Se si vuole visualizzare la definizione di un criterio di flusso, usare [Get](/rest/api/media/streamingpolicies/get) e specificare il nome del criterio. Ad esempio:

### <a name="rest"></a>REST

Richiesta:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Risposta:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](filter-order-page-entitites-how-to.md).

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
* [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi](drm-playready-license-template-concept.md)
* [Usare la crittografia dinamica DRM e il servizio di distribuzione delle licenze](drm-protect-with-drm-tutorial.md)
