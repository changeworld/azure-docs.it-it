---
title: Localizzatori di streaming in Servizi multimediali di Azure
description: Questo articolo illustra le caratteristiche dei localizzatori di streaming e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: bcee8d0554b9c3349c7efc88c10e9eee8b185acb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773280"
---
# <a name="streaming-locators"></a>Localizzatori di streaming

Per rendere i video nell'asset di output disponibili per la riproduzione da parte dei client, è necessario creare un [localizzatore di streaming](/rest/api/media/streaminglocators) e quindi definire gli URL di streaming. Per creare un URL, è necessario concatenare il nome host dell'endpoint di streaming e il percorso del localizzatore di streaming. Per un esempio .NET, vedere [Ottenere un localizzatore di streaming](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Il processo di creazione di un **localizzatore di streaming** è detto pubblicazione. Per impostazione predefinita, **il localizzatore** di streaming è valido immediatamente dopo aver chiamato l'API e dura fino a quando non viene eliminato, a meno che non si configurano le ore di inizio e fine facoltative. 

Quando si crea **un localizzatore di streaming,** è necessario specificare un nome **di asset** e un nome di **criteri di** streaming. Per altre informazioni, vedere i seguenti argomenti:

* [Asset](assets-concept.md)
* [Criteri di streaming](stream-streaming-policy-concept.md)
* [Criteri di chiave simmetrica](drm-content-key-policy-concept.md)

È anche possibile specificare l'ora di inizio e di fine nel localizzatore di streaming, che consentirà all'utente di riprodurre il contenuto solo tra questi orari (ad esempio, dal 5/1/2019 al 5/5/2019).  

## <a name="considerations"></a>Considerazioni

* **I localizzatori di streaming** non sono aggiornabili. 
* Le proprietà dei **localizzatori di streaming** di tipo Datetime sono sempre in formato UTC.
* È necessario progettare un set limitato di criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming tutte le volte che si devono usare le stesse opzioni. Per altre informazioni, vedere [Quote e limiti.](limits-quotas-constraints-reference.md)

## <a name="create-streaming-locators"></a>Creare localizzatori di streaming  

### <a name="not-encrypted"></a>Non crittografato

Se si vuole trasmettere il file in chiaro (non crittografato), impostare i criteri di streaming non crittografati predefiniti: su "Predefined_ClearStreamingOnly" (in .NET è possibile usare l'enumerazione PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Crittografato 

Se è necessario crittografare il contenuto con la crittografia CENC, impostare i criteri su "Predefined_MultiDrmCencStreaming". La crittografia Widevine verrà applicata a un flusso DASH e PlayReady a Smooth. La chiave verrà recapitata a un client di riproduzione in base alle licenze DRM configurate.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Se si vuole anche crittografare il flusso HLS con CBCS (FairPlay), usare "Predefined_MultiDrmStreaming".

> [!NOTE]
> Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associare filtri ai localizzatori di streaming

Vedere [Filtri: associare ai localizzatori di streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Entità filter, order, page Streaming Locator

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](filter-order-page-entitites-how-to.md).

## <a name="list-streaming-locators-by-asset-name"></a>Elencare i localizzatori di streaming in base al nome dell'asset

Per ottenere localizzatori di streaming in base al nome dell'asset associato, usare le operazioni seguenti:

|Linguaggio|API|
|---|---|
|REST|[liststreaminglocators](/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](/cli/azure/ams/asset#az_ams_asset_list_streaming_locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Vedi anche

* [Asset](assets-concept.md)
* [Criteri di streaming](stream-streaming-policy-concept.md)
* [Criteri di chiave simmetrica](drm-content-key-policy-concept.md)
* [Esercitazione: Eseguire il caricamento, la codifica e lo streaming di video con .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Passaggi successivi

[Come creare un localizzatore di streaming e compilare URL](create-streaming-locator-build-url.md)
