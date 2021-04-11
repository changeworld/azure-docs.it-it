---
title: Guida alla migrazione della protezione del contenuto
description: Questo articolo fornisce indicazioni basate sullo scenario di protezione del contenuto che consentono di eseguire la migrazione da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 74f15fc302a8499e41a1413dd8915e6442d4bbe7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064495"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Guida alla migrazione basata sullo scenario di protezione del contenuto

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-4.svg)

Questo articolo fornisce informazioni dettagliate e istruzioni sulla migrazione di casi d'uso della protezione del contenuto dall'API v2 alla nuova API di servizi multimediali di Azure V3.

## <a name="protect-content-in-v3-api"></a>Proteggere il contenuto nell'API V3

Usare il supporto per le funzionalità [multichiave](architecture-design-multi-drm-system.md) nella nuova API V3.

Per i passaggi specifici, vedere Concetti relativi alla protezione del contenuto, esercitazioni e procedure guidate.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>Visibilità degli asset, StreamingLocators e delle proprietà v2 nell'API V3 per gli scenari di protezione del contenuto

Durante la migrazione all'API V3, si noterà che è necessario accedere ad alcune proprietà o chiavi simmetriche dalle risorse V2. Una differenza fondamentale è che l'API v2 USA **AssetID** come chiave di identificazione primaria e la nuova API V3 usa il nome di gestione risorse di Azure dell'entità come identificatore primario.  La proprietà **asset.Name** V2 non viene in genere usata come identificatore univoco, pertanto quando si esegue la migrazione a V3 si noterà che i nomi degli asset V2 sono ora visualizzati nel campo **asset. Description** .

Se, ad esempio, in precedenza era presente un asset V2 con ID **"NB: CID: UUID: 8cb39104-122C-496E-9ac5-7f9e2c2547b8"**, si troverà quando si elencano le risorse V2 precedenti tramite l'API V3, il nome sarà ora la parte del GUID alla fine (in questo caso, **"8cb39104-122C-496E-9ac5-7f9e2c2547b8"**).

È possibile eseguire una query su **StreamingLocators** associato agli asset creati nell'API v2 usando il nuovo metodo V3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) nell'entità Asset.  Fare riferimento anche alla versione di [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true) per l'SDK client .NET

I risultati del metodo **ListStreamingLocators** forniranno il **nome** e il **StreamingLocatorId** del localizzatore insieme a **StreamingPolicyName**.

Per trovare il **entità ContentKey mediante** usato in **StreamingLocators** per la protezione del contenuto, è possibile chiamare il metodo [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) .  

Per gli **Asset** creati e pubblicati con l'API v2 sono presenti sia un [criterio della chiave](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) simmetrica sia una chiave simmetrica definita nell'API V3, anziché usare un criterio predefinito per la chiave simmetrica nei criteri di [streaming](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept).

Per altre informazioni sulla protezione del contenuto nell'API V3, vedere l'articolo [proteggere i contenuti con la crittografia dinamica di servizi multimediali.](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>Come elencare le impostazioni di protezione del contenuto e degli asset V2 usando l'API V3

Nell'API v2 si usano in genere **Asset**, **StreamingLocators** e **entità ContentKey mediante** per proteggere il contenuto di streaming.
Quando si esegue la migrazione all'API V3, le risorse dell'API v2, StreamingLocators e entità ContentKey mediante vengono tutte esposte automaticamente nell'API V3 e tutti i dati in essi contenuti sono disponibili per l'accesso.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>È possibile aggiornare le proprietà V2 usando l'API V3?

No, non è possibile aggiornare le proprietà nelle entità v2 tramite l'API V3 creata usando StreamingLocators, StreamingPolicies, i criteri chiave simmetrica e le chiavi simmetriche nella versione 2.
Se è necessario aggiornare, modificare o modificare il contenuto archiviato in entità V2, sarà necessario aggiornarlo tramite l'API v2 o creare nuove entità dell'API V3 per eseguirne la migrazione.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>Ricerca per categorie modificare il ContentKeyPolicy usato per un asset V2 pubblicato e che mantiene la stessa chiave simmetrica?

In questa situazione, è necessario prima annullare la pubblicazione (rimuovere tutti i localizzatori di streaming) nell'asset tramite v2 SDK (eliminare il localizzatore, scollegare i criteri di autorizzazione della chiave simmetrica, scollegare i criteri di distribuzione degli asset, scollegare la chiave simmetrica, eliminare la chiave simmetrica), quindi creare un nuovo **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** in V3 usando v3 [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) e [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept).

È necessario specificare l'identificatore della chiave simmetrica e il valore della chiave specifici necessari durante la creazione di **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)**.

Si noti che è possibile eliminare il localizzatore V2 usando l'API V3, ma questa operazione non rimuoverà la chiave simmetrica o i criteri della chiave simmetrica usati se sono stati creati nell'API v2.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>Uso di AMSE V2 e AMSE V3 side-by-side

Quando si esegue la migrazione del contenuto da V2 a V3, è consigliabile installare lo [strumento di esplorazione di servizi multimediali di Azure V2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) insieme allo [strumento di esplorazione di servizi multimediali di Azure V3](https://github.com/Azure/Azure-Media-Services-Explorer) per confrontare i dati visualizzati affiancati per un asset creato e pubblicato tramite le API v2. Le proprietà devono essere tutte visibili, ma in posizioni leggermente diverse.  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Concetti relativi alla protezione del contenuto, esercitazioni e guide

### <a name="concepts"></a>Concetti

- [Proteggi i tuoi contenuti con la crittografia dinamica di servizi multimediali](drm-content-protection-concept.md)
- [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](architecture-design-multi-drm-system.md)
- [Modello di licenza PlayReady di servizi multimediali V3 con](drm-playready-license-template-concept.md)
- [Panoramica del modello di licenza di servizi multimediali V3 con Widevine](drm-widevine-license-template-concept.md)
- [Configurazione e requisiti della licenza Apple FairPlay](drm-fairplay-license-overview.md)
- [Criteri di streaming](streaming-policy-concept.md)
- [Criteri chiave simmetrica](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Esercitazioni

[Avvio rapido: Usare il portale per crittografare contenuto](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Guide pratiche

- [Ottenere una chiave di firma dai criteri esistenti](drm-get-content-key-policy-dotnet-how-to.md)
- [Streaming FairPlay offline per iOS con servizi multimediali V3](drm-offline-fairplay-for-ios-concept.md)
- [Streaming Widevine offline per Android con servizi multimediali V3](drm-offline-widevine-for-android.md)
- [Streaming PlayReady offline per Windows 10 con servizi multimediali V3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Esempi

È anche possibile [confrontare il codice V2 e V3 negli esempi di codice](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Strumenti

- [strumento di esplorazione di servizi multimediali di Azure V3](https://github.com/Azure/Azure-Media-Services-Explorer)
- [V2 strumento di esplorazione di servizi multimediali di Azure](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
