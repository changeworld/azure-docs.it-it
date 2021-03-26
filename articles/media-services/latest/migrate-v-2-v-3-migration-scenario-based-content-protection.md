---
title: Guida alla migrazione della protezione del contenuto
description: Questo articolo fornisce indicazioni basate su scenari di protezione del contenuto che consentono di eseguire la migrazione minima da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 8853f5234cdbd5244d4624c91b7e3914948cf704
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561305"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Guida alla migrazione basata sullo scenario di protezione del contenuto

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-4.svg)

Questo articolo fornisce indicazioni basate su scenari di protezione del contenuto che consentono di eseguire la migrazione minima da servizi multimediali di Azure V2 a V3.

## <a name="protect-content-in-v3-api"></a>Proteggere il contenuto nell'API V3

Usare il supporto per le funzionalità [multichiave](design-multi-drm-system-with-access-control.md) nella nuova API V3.

Per i passaggi specifici, vedere Concetti relativi alla protezione del contenuto, esercitazioni e procedure guidate.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Concetti relativi alla protezione del contenuto, esercitazioni e guide

### <a name="concepts"></a>Concetti

- [Proteggi i tuoi contenuti con la crittografia dinamica di servizi multimediali](content-protection-overview.md)
- [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](design-multi-drm-system-with-access-control.md)
- [Modello di licenza PlayReady di servizi multimediali V3 con](playready-license-template-overview.md)
- [Panoramica del modello di licenza di servizi multimediali V3 con Widevine](widevine-license-template-overview.md)
- [Configurazione e requisiti della licenza Apple FairPlay](fairplay-license-overview.md)
- [Criteri di streaming](streaming-policy-concept.md)
- [Criteri chiave simmetrica](content-key-policy-concept.md)

### <a name="tutorials"></a>Esercitazioni

[Avvio rapido: Usare il portale per crittografare contenuto](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Guide pratiche

- [Ottenere una chiave di firma dai criteri esistenti](get-content-key-policy-dotnet-howto.md)
- [Streaming FairPlay offline per iOS con servizi multimediali V3](offline-fairplay-for-ios.md)
- [Streaming Widevine offline per Android con servizi multimediali V3](offline-widevine-for-android.md)
- [Streaming PlayReady offline per Windows 10 con servizi multimediali V3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Esempi

È anche possibile [confrontare il codice V2 e V3 negli esempi di codice](migrate-v-2-v-3-migration-samples.md).
