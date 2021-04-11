---
title: Guida alla migrazione della protezione del contenuto
description: Questo articolo fornisce indicazioni basate sullo scenario di protezione del contenuto che consentono di eseguire la migrazione da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490948"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Guida alla migrazione basata sullo scenario di protezione del contenuto

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-4.svg)

Questo articolo fornisce informazioni dettagliate e istruzioni sulla migrazione di casi d'uso della protezione del contenuto dall'API v2 alla nuova API di servizi multimediali di Azure V3.

## <a name="protect-content-in-v3-api"></a>Proteggere il contenuto nell'API V3

Usare il supporto per le funzionalità [multichiave](architecture-design-multi-drm-system.md) nella nuova API V3.

Per i passaggi specifici, vedere Concetti relativi alla protezione del contenuto, esercitazioni e procedure guidate alla fine di questo articolo.

> [!NOTE]
> Il resto di questo articolo illustra come è possibile eseguire la migrazione della protezione del contenuto V2 a V3 con .NET.  Se sono necessarie istruzioni o codice di esempio per una lingua o un metodo diverso, creare un problema di GitHub per questa pagina.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>visibilità V3 di asset, StreamingLocators e proprietà V2

Nell'API v2,, `Assets` `StreamingLocators` e `ContentKeys` sono stati usati per proteggere il contenuto del flusso. Quando si esegue la migrazione all'API V3, l'API v2 `Assets` , `StreamingLocators` e `ContentKeys` sono tutte esposte automaticamente nell'API V3 e tutti i dati in essi contenuti sono disponibili per l'accesso.

Tuttavia, non è possibile *aggiornare* le proprietà nelle entità v2 tramite l'API V3 che è stata creata nella versione V2.

Se è necessario aggiornare, modificare o modificare il contenuto archiviato in entità V2, aggiornarlo con l'API v2 o creare nuove entità dell'API V3 per eseguirne la migrazione.

## <a name="asset-identifier-differences"></a>Differenze tra gli identificatori di asset

Per eseguire la migrazione, è necessario accedere alle proprietà o alle chiavi del contenuto dalle risorse V2.  È importante comprendere che l'API v2 USA `AssetId` come chiave di identificazione primaria, ma la nuova API V3 usa il nome di *Gestione risorse di Azure* dell'entità come identificatore primario.  (La `Asset.Name` Proprietà V2 non viene utilizzata come identificatore univoco). Con l'API V3, il nome dell'asset v2 viene ora visualizzato come `Asset.Description` .

Ad esempio, se in precedenza era presente un asset V2 con l'ID `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` , l'identificatore si trova ora alla fine del GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8` . Questa operazione viene visualizzata quando si elencano le risorse v2 tramite l'API V3.

Gli asset creati e pubblicati usando l'API v2 avranno sia un che `ContentKeyPolicy` un `ContentKey` nell'API V3 anziché un criterio predefinito per la chiave simmetrica nel `StreamingPolicy` .

Per ulteriori informazioni, vedere la documentazione relativa ai [criteri della chiave](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) simmetrica e la documentazione sui [criteri di streaming](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept) .

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>Usare gli strumenti di Azure Media Services Explorer (AMSE) V2 e AMSE V3 affiancati

Usare lo [strumento di esplorazione di servizi multimediali di Azure V2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) insieme allo [strumento di esplorazione di servizi multimediali di Azure V3](https://github.com/Azure/Azure-Media-Services-Explorer) per confrontare i dati affiancati per un asset creato e pubblicato tramite le API v2. Le proprietà devono essere tutte visibili, ma in posizioni diverse.

## <a name="use-the-net-content-protection-migration-sample"></a>Usare l'esempio di migrazione della protezione del contenuto .NET

È possibile trovare un esempio di codice per confrontare le differenze negli identificatori di asset usando [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) in ContentProtection negli esempi di codice di servizi multimediali.

## <a name="list-the-streaming-locators"></a>Elencare i localizzatori di streaming

È possibile eseguire una query sull'oggetto `StreamingLocators` associato agli asset creati nell'API v2 usando il nuovo metodo V3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) nell'entità Asset.  Fare riferimento anche alla versione di [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true) per l'SDK client .NET

I risultati del `ListStreamingLocators` Metodo forniranno l'oggetto `Name` e `StreamingLocatorId` del localizzatore insieme a `StreamingPolicyName` .

## <a name="find-the-content-keys"></a>Trovare le chiavi simmetriche

Per trovare l'oggetto `ContentKeys` usato con il `StreamingLocators` , è possibile chiamare il metodo [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) .  

Per altre informazioni sulla protezione del contenuto nell'API V3, vedere l'articolo [proteggere i contenuti con la crittografia dinamica di servizi multimediali.](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>Modificare il ContentKeyPolicy V2 mantenendo lo stesso ContentKey

Per prima cosa, è necessario annullare la pubblicazione (rimuovere tutti i localizzatori di streaming) nell'asset tramite v2 SDK. Ecco come:

1. Eliminare il localizzatore.
1. Scollegare il `ContentKeyAuthorizationPolicy` .
1. Scollegare il `AssetDeliveryPolicy` .
1. Scollegare il `ContentKey` .
1. Eliminare `ContentKey` .
1. Creare un nuovo `StreamingLocator` in V3 usando un V3 `StreamingPolicy` e `ContentKeyPolicy` , specificando l'identificatore di chiave simmetrica e il valore della chiave specifici necessari.

> [!NOTE]
> È possibile eliminare il localizzatore V2 usando l'API V3, ma questa operazione non rimuoverà la chiave simmetrica o i criteri della chiave simmetrica se sono stati creati nell'API v2.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Concetti relativi alla protezione del contenuto, esercitazioni e guide

### <a name="concepts"></a>Concetti

- [Proteggi i tuoi contenuti con la crittografia dinamica di servizi multimediali](drm-content-protection-concept.md)
- [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](architecture-design-multi-drm-system.md)
- [Modello di licenza PlayReady di servizi multimediali V3 con](drm-playready-license-template-concept.md)
- [Panoramica del modello di licenza di servizi multimediali V3 con Widevine](drm-widevine-license-template-concept.md)
- [Configurazione e requisiti della licenza Apple FairPlay](drm-fairplay-license-overview.md)
- [Criteri di streaming](stream-streaming-policy-concept.md)
- [Criteri chiave simmetrica](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Esercitazioni

[Avvio rapido: Usare il portale per crittografare contenuto](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Guide pratiche

- [Ottenere una chiave di firma dai criteri esistenti](drm-get-content-key-policy-dotnet-how-to.md)
- [Streaming FairPlay offline per iOS con servizi multimediali V3](drm-offline-fairplay-for-ios-concept.md)
- [Streaming Widevine offline per Android con servizi multimediali V3](drm-offline-widevine-for-android.md)
- [Streaming PlayReady offline per Windows 10 con servizi multimediali V3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Esempi

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- È anche possibile [confrontare il codice V2 e V3 negli esempi di codice](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Strumenti

- [strumento di esplorazione di servizi multimediali di Azure V3](https://github.com/Azure/Azure-Media-Services-Explorer)
- [V2 strumento di esplorazione di servizi multimediali di Azure](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
