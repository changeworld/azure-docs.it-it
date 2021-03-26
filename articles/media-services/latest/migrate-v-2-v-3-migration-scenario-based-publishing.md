---
title: Linee guida per la migrazione di pacchetti e recapito
description: Questo articolo fornisce indicazioni basate sullo scenario per la creazione di pacchetti e la distribuzione che consentono di eseguire la migrazione da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 3a09e3f2bf29c09066e9414f9aa02a7879375425
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563532"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Guida alla migrazione basata sullo scenario per la creazione di pacchetti e la distribuzione

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-4.svg)

Questo articolo fornisce linee guida basate sullo scenario per la creazione di pacchetti e la distribuzione che aiuteranno a eseguire la migrazione da servizi multimediali di Azure V2 a V3.

Modifiche sostanziali alla modalità di pubblicazione del contenuto nell'API V3. Il nuovo modello di pubblicazione è semplificato e usa un numero inferiore di entità per creare un localizzatore di streaming. L'API ha ridotto fino a due entità e le quattro entità richieste in precedenza. I criteri chiave simmetrica e i localizzatori di streaming ora sostituiscono la necessità di `ContentKeyAuthoriationPolicy` ,, `AssetDeliveyPolicy` `ContentKey` e `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Creazione di pacchetti e recapito in V3

1. Creare [criteri per chiavi](content-key-policy-concept.md)simmetriche.
1. Creare [localizzatori di streaming](streaming-locators-concept.md).
1. Ottenere i [percorsi di streaming](create-streaming-locator-build-url.md) 
    1. Configurarlo per un [Dash](dynamic-packaging-overview.md#mpeg-dash-protocol) o un lettore [HLS](dynamic-packaging-overview.md#hls-protocol) .

Per i passaggi specifici, vedere pubblicazione di concetti, esercitazioni e procedure guidate di seguito.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Pubblicazione di concetti, esercitazioni e guide

### <a name="concepts"></a>Concetti

- [Creazione dinamica dei pacchetti in Servizi multimediali versione 3](dynamic-packaging-overview.md)
- [Filtri](filters-concept.md)
- [Filtrare i manifesti usando Dynamic Packager](filters-dynamic-manifest-overview.md)
- [Endpoint di streaming (Origin) in servizi multimediali di Azure](streaming-endpoint-concept.md)
- [Flusso di contenuto con l'integrazione della rete CDN](scale-streaming-cdn.md)
- [Localizzatori di streaming](streaming-locators-concept.md)

### <a name="how-to-guides"></a>Guide pratiche

- [Gestire gli endpoint di streaming con servizi multimediali V3](manage-streaming-endpoints-howto.md)
- [Esempio dell'interfaccia della riga di comando: Pubblicare un asset](cli-publish-asset.md)
- [Creare un localizzatore di streaming e gli URL di compilazione](create-streaming-locator-build-url.md)
- [Scaricare i risultati di un processo](download-results-howto.md)
- [Segnala tracce audio descrittive](signal-descriptive-audio-howto.md)
- [Azure Media Player installazione completa](../azure-media-player/azure-media-player-full-setup.md)
- [Come usare il lettore di Video.js con servizi multimediali di Azure](how-to-video-js-player.md)
- [Come usare il lettore Shaka con servizi multimediali di Azure](how-to-shaka-player.md)

## <a name="samples"></a>Esempi

È anche possibile [confrontare il codice V2 e V3 negli esempi di codice](migrate-v-2-v-3-migration-samples.md).
