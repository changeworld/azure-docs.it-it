---
title: Gestire gli endpoint di streaming
description: Questo articolo illustra come gestire gli endpoint di streaming con Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2b442edc537ec64b12df215a18ab017ee47becff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791734"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Gestire gli endpoint di streaming con Servizi multimediali v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Quando viene creato l'account di Servizi **multimediali,** viene aggiunto un endpoint di [streaming](stream-streaming-endpoint-concept.md) predefinito all'account nello **stato Arrestato.** Per avviare lo streaming del [](encode-dynamic-packaging-concept.md) contenuto e sfruttare la creazione dinamica dei pacchetti e la crittografia [dinamica,](drm-content-protection-concept.md)l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello **stato In** esecuzione.

Questo articolo illustra come eseguire il comando [start](/rest/api/media/streamingendpoints/start) nell'endpoint di streaming usando tecnologie diverse. 
 
> [!NOTE]
> Il costo verrà addebitato solo quando StreamingEndpoint è in stato di esecuzione.
    
## <a name="prerequisites"></a>Prerequisiti

Vedere: 

* [Concetti relativi ai Servizi multimediali](concepts-overview.md)
* [Concetto di endpoint di streaming](stream-streaming-endpoint-concept.md)
* [Creazione dinamica dei pacchetti](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>Usare REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Per altre informazioni, vedere: 

* Avvio [di una documentazione di riferimento di StreamingEndpoint.](/rest/api/media/streamingendpoints/start)
* L'avvio di un endpoint di streaming è un'operazione asincrona. 

    Per informazioni su come monitorare le operazioni a esecuzione lunga, vedere [Operazioni a esecuzione lunga](media-services-apis-overview.md).
* Questa [raccolta Postman contiene](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) esempi di più operazioni REST, tra cui l'avvio di un endpoint di streaming.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure 
 
1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Passare all'account Servizi multimediali di Azure locale.
1. Nel riquadro sinistro selezionare  **Endpoint di streaming**.
1. Selezionare l'endpoint di streaming da avviare e quindi **selezionare Avvia**.

## <a name="use-the-azure-cli"></a>Usare l'interfaccia della riga di comando di Azure

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Per altre informazioni, vedere [az ams streaming-endpoint start](/cli/azure/ams/streaming-endpoint#az_ams_streaming_endpointstart).

## <a name="use-sdks"></a>Usare SDK

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Vedere l'esempio [di codice Java completo.](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Vedere l'esempio [di codice .NET completo.](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112)

---

## <a name="next-steps"></a>Passaggi successivi

* [Specifica OpenAPI di Servizi multimediali v3 (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Operazioni degli endpoint di streaming](/rest/api/media/streamingendpoints)
