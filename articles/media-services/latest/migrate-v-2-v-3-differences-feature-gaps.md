---
title: Gap delle funzionalità tra servizi multimediali di Azure V2 e V3
description: Questo articolo descrive i gap tra le funzionalità di servizi multimediali di Azure da V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 07e43bdb0969c26120231013cc0c0354e6968ceb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567951"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Gap delle funzionalità tra servizi multimediali di Azure V2 e V3

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-2.svg)

Questa parte della Guida alla migrazione fornisce informazioni dettagliate sulle differenze tra le API v2 e V3.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>Gap delle funzionalità tra le API v2 e V3

L'API V3 presenta i gap di funzionalità seguenti con l'API v2. Alcune delle funzionalità avanzate del Media Encoder Standard nelle API v2 non sono attualmente disponibili in V3:

- Inserimento di una traccia audio invisibile all'utente quando l'input non ha audio, perché non è più necessario con l'Azure Media Player.

- Inserimento di una traccia video quando l'input non ha video.

- Gli eventi live con transcodifica non supportano attualmente l'inserimento dello Slate a metà flusso e l'inserimento di marcatori ad tramite chiamata API.

- Il codificatore Azure Media Premium non sarà più supportato nella versione V2. Se lo si usa per la codifica HEVC a 8 bit, usare il nuovo supporto di HEVC nel codificatore standard. 
    - È stato aggiunto il supporto per il mapping del canale audio al codificatore standard.  Vedere [audio nella documentazione di spavalderia per la codifica di servizi multimediali](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).
    - Se si usano funzionalità avanzate o formati di output del prodotto concesso in licenza di terze parti, ad esempio MXF o ProRes, usare la soluzione Azure partner da Telestream, che sarà transazionale entro il tempo del ritiro V2. In alternativa, è possibile usare Imagine Communications o [Bitmovin](http://bitmovin.com).

- La proprietà "set di disponibilità" nell'endpoint di streaming in V2 non è più supportata. Vedere il progetto di esempio e le linee guida per il recapito [VOD a disponibilità elevata](./media-services-high-availability-encoding.md) nell'API V3.

- In servizi multimediali V3 non è possibile specificare FairPlay IV. Anche se non ha alcun effetto sui clienti che usano servizi multimediali per la creazione di pacchetti e la distribuzione di licenze, può trattarsi di un problema quando si usa un sistema DRM di terze parti per distribuire le licenze FairPlay (modalità ibrida).

- La crittografia di archiviazione lato client per la protezione degli asset inattivi è stata rimossa nell'API V3 e sostituita dalla crittografia del servizio di archiviazione per i dati inattivi. Le API V3 continuano a funzionare con asset crittografati di archiviazione esistenti, ma non ne consentono la creazione di nuove.

## <a name="terminology-and-entity-changes"></a>Terminologia e modifiche alle entità

Per altre modifiche all'API, vedere [terminologia e](migrate-v-2-v-3-differences-terminology.md) modifiche alle entità.
