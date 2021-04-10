---
title: Esempi di Servizi multimediali v3
description: Questo articolo contiene un elenco di tutti gli esempi disponibili per Media Services V3 organizzati per metodo e SDK.  Gli esempi includono .NET, Node.JS, Python e Java, oltre a REST con postazione.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: bfe39020da0be245f47d0c954de7598914d6534d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105051343"
---
# <a name="media-services-v3-samples"></a>Esempi di Servizi multimediali v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Questo articolo contiene un elenco di tutti gli esempi disponibili per servizi multimediali organizzati per metodo e SDK.  Gli esempi includono .NET, Node.JS, Python e Java, oltre a REST con postazione.

## <a name="rest-postman-collection"></a>Raccolta di messaggi REST

Gli esempi relativi ai [messaggi Rest](https://github.com/Azure-Samples/media-services-v3-rest-postman) includono un ambiente e una raccolta di post che è possibile importare nel client di posta.

## <a name="samples-by-sdk"></a>Esempi per SDK

Sono disponibili descrizioni e collegamenti agli esempi che è possibile cercare in ognuna delle schede.

## <a name="net"></a>[.NET](#tab/net/)

| Cartella | Descrizione |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|Come inviare un processo usando un set di impostazioni predefinito e un input URL HTTP, pubblicare l'asset di output per lo streaming e scaricare i risultati per la verifica.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|Come inviare un processo usando un set di impostazioni di codifica H. 264 personalizzato e un input URL HTTP, pubblicare asset di output per lo streaming e scaricare i risultati per la verifica.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|Come inviare un processo usando un set di impostazioni di codifica HEVC personalizzato e un input URL HTTP, pubblicare asset di output per lo streaming e scaricare i risultati per la verifica.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|Come inviare un processo usando JobInputSequence per unire due o più risorse che possono essere ritagliate in base all'ora di inizio o di fine. Il file codificato risultante è un singolo video con tutti gli asset Uniti.  L'esempio pubblicherà anche l'asset di output per lo streaming e il download dei risultati per la verifica.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|Come inviare un processo usando un set di impostazioni personalizzato con uno sprite di anteprima e un input URL HTTP, pubblicare asset di output per lo streaming e scaricare i risultati per la verifica.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|Come creare una Live con un archivio completo di un massimo di 25 ore e un filtro sull'asset con una finestra DVR di 5 minuti. Come usare un filtro per creare un localizzatore per lo streaming.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|Come creare una trasformazione dell'analizzatore video, caricare un file video in un asset di input, inviare un processo con la trasformazione e scaricare i risultati per la verifica.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|Come creare una trasformazione dell'analizzatore audio, caricare un file multimediale in un asset di input, inviare un processo con la trasformazione e scaricare i risultati per la verifica.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|Come creare una trasformazione con il set di impostazioni predefinito AdaptiveStreaming, inviare un processo, creare un ContentKeyPolicy usando una chiave privata, associare ContentKeyPolicy a StreamingLocator, ottenere un token e stampare un URL per la riproduzione in Azure Media Player. Quando un flusso viene richiesto da un lettore, servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti con AES-128 e Azure Media Player usa il token per la decrittografia.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|Come creare una trasformazione con il set di impostazioni predefinito AdaptiveStreaming, inviare un processo, creare un ContentKeyPolicy con la configurazione Widevine usando una chiave privata, associare il ContentKeyPolicy a StreamingLocator, ottenere un token e stampare un URL per la riproduzione in un lettore Widevine. Quando un utente richiede contenuto protetto da Widevine, l'applicazione lettore richiede una licenza dal servizio licenze di servizi multimediali. Se l'applicazione lettore viene autorizzata, il servizio licenze di Servizi multimediali rilascia una licenza per il lettore. Una licenza Widevine contiene la chiave di decrittografia che può essere usata dal lettore client per decrittografare e trasmettere il contenuto.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|Come creare una trasformazione con il set di impostazioni predefinito AdaptiveStreaming, inviare un processo, creare un ContentKeyPolicy con la configurazione PlayReady usando una chiave privata, associare il ContentKeyPolicy a StreamingLocator, ottenere un token e stampare un URL per la riproduzione in un Azure Media Player. Quando un utente richiede contenuto protetto da PlayReady, l'applicazione lettore richiede una licenza dal servizio licenze di servizi multimediali. Se l'applicazione lettore viene autorizzata, il servizio licenze di Servizi multimediali rilascia una licenza per il lettore. Una licenza PlayReady contiene la chiave di decrittografia che può essere usata dal lettore client per decrittografare e trasmettere in flusso i contenuti.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|Come crittografare dinamicamente i contenuti con PlayReady e Widevine DRM e riprodurre i contenuti senza richiedere una licenza al servizio licenze. Viene illustrato come creare una trasformazione con il set di impostazioni predefinito AdaptiveStreaming, inviare un processo, creare un ContentKeyPolicy con la restrizione Open e la configurazione persistente PlayReady/Widevine, associare ContentKeyPolicy a un StreamingLocator e stampare un URL per la riproduzione.|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|Come creare una trasformazione con il set di impostazioni predefinito AdaptiveStreaming, inviare un processo, creare un filtro asset e un filtro account, associare i filtri ai localizzatori di streaming e agli URL di stampa per la riproduzione.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|Come creare una trasformazione con il set di impostazioni predefinito AdaptiveStreaming, inviare un processo, pubblicare un asset di output per il flusso HLS e DASH.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | Linee guida e procedure consigliate per un sistema di produzione con la codifica o l'analisi su richiesta. I lettori devono iniziare con la [disponibilità elevata degli articoli con servizi multimediali e VOD](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding). È disponibile un file di soluzione separato per l'esempio [HighAvailabilityEncodingStreaming](/HighAvailabilityEncodingStreaming/Readme.md) . |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Cartella|Descrizione|
|---|---|
|[HelloWorld-ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |Come connettersi ed elencare gli asset |
|[Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| Come eseguire lo streaming live di base. Assicurarsi di verificare che tutte le risorse siano state pulite e che non vengano più fatturate nel portale quando si usa Live.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| Come caricare un file o una codifica locale da un URL di origine, come usare Storage SDK per scaricare il contenuto e come eseguire lo streaming a un lettore. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Come codificare e trasmettere in streaming con Widevine e PlayReady DRM |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| Come usare i set di impostazioni video e Audio Analyzer per generare metadati e informazioni dettagliate da un file video o audio. |

## <a name="python"></a>[Python](#tab/python)

Attualmente, è disponibile un esempio Python, ovvero [la codifica di base con Python](https://github.com/Azure-Samples/media-services-v3-python).

## <a name="java"></a>[Java](#tab/java)

|Cartella|Descrizione|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|Come analizzare l'audio in un file multimediale. |
|Protezione del contenuto|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|Come crittografare dinamicamente i contenuti con AES-128.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|Come crittografare dinamicamente i contenuti con PlayReady DRM.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Come crittografare dinamicamente i contenuti con Widevine DRM.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|Come crittografare dinamicamente i contenuti con FairPlay DRM e riprodurre i contenuti senza richiedere una licenza al servizio licenze.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|Come crittografare dinamicamente i contenuti con PlayReady e Widevine DRM e riprodurre i contenuti senza richiedere una licenza al servizio licenze.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|Come filtrare il contenuto usando i filtri asset e account.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|Come comprimere dinamicamente il contenuto VOD in HLS/DASH per lo streaming.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|Come creare un evento live con un archivio completo di un massimo di 25 ore e un filtro sull'asset con una finestra DVR di 5 minuti e come creare un localizzatore per lo streaming e usare il filtro.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|Come creare un evento live con un archivio completo di un massimo di 25 ore e un filtro sull'asset con una finestra DVR di 5 minuti e come creare un localizzatore per lo streaming e usare il filtro.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|Come creare una trasformazione di codifica personalizzata usando le impostazioni di StandardEncoderPreset.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|Come inviare un processo usando un set di impostazioni predefinito e un input URL HTTP, pubblicare l'asset di output per lo streaming e scaricare i risultati per la verifica.|

---
