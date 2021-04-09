---
title: Introduzione alla migrazione da Media Services V2 a V3
description: Questo articolo è un'introduzione alla migrazione da Media Services V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 32e502ff175a9222faa0eb79fb53f2cc3f76bfb7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559741"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Introduzione alla migrazione da Media Services V2 a V3

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

La guida alla migrazione di servizi multimediali consente di eseguire la migrazione dalle API di servizi multimediali V2 alle API V3 basate su una migrazione che sfrutta i vantaggi delle nuove funzionalità e funzioni ora disponibili. È consigliabile usare il giudizio migliore e determinare il modo più adatto per lo scenario.

## <a name="how-to-use-this-guide"></a>Come usare questa guida

### <a name="navigating"></a>Esplorazione

In tutta la guida, viene visualizzato il grafico seguente.

![passaggi della migrazione](./media/migration-guide/steps.svg)<br/>

Il passaggio in cui ci si trova verrà indicato da una modifica del colore nel numero del passaggio, come segue:

![passaggi della migrazione 2](./media/migration-guide/steps-2.svg)<br/>

Alla fine di ogni pagina, vengono visualizzati i collegamenti al resto dei documenti di migrazione che è possibile leggere sotto l'intestazione **passaggi successivi** .

### <a name="guidance"></a>Indicazioni

Le linee guida fornite di seguito sono *generali*. Include contenuto per migliorare la conoscenza di ciò che è ora disponibile in V3, oltre a ciò che è stato modificato nei flussi di lavoro di servizi multimediali.

Per istruzioni più dettagliate, tra cui schermate e grafica concettuale, sono disponibili collegamenti a concetti, esercitazioni, guide introduttive, esempi e riferimenti alle API in ogni argomento basato su scenari. Sono stati anche elencati esempi che consentono di confrontare l'API v2 con l'API V3.

## <a name="migration-guidance-overview"></a>Panoramica delle linee guida sulla migrazione

Sono disponibili quattro passaggi generali da seguire durante la migrazione:

## <a name="step-1-benefits"></a>Vantaggi del passaggio 1

<hr color="#5ea0ef" size="10">

[Informazioni sui vantaggi](migrate-v-2-v-3-migration-benefits.md) della migrazione a Media Services API V3.

## <a name="step-2-differences"></a>Differenze del passaggio 2

<hr color="#5ea0ef" size="10">

Informazioni sulle differenze tra l'API di servizi multimediali V2 e l'API V3.

- [Accesso all'API](migrate-v-2-v-3-differences-api-access.md)
- [Gap delle funzionalità](migrate-v-2-v-3-differences-feature-gaps.md)
- [Terminologia e modifiche alle entità](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Installazione di Step 3 SDK

<hr color="#5ea0ef" size="10">

Informazioni sulle differenze dell'SDK e sulla [configurazione per eseguire la migrazione all'API REST V3 o all'SDK client](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>Istruzioni basate sullo scenario Step 4

<hr color="#5ea0ef" size="10">

L'applicazione di servizi multimediali V2 potrebbe essere univoca. Per questo motivo, sono state fornite indicazioni basate sullo scenario basate su come è *possibile* usare servizi multimediali in passato e i passaggi per ogni funzionalità del servizio, ad esempio:

- [Encoding](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Streaming Live](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Creazione di pacchetti e distribuzione](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Protezione del contenuto](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Media reserved Unit (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)
