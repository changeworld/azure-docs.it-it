---
title: Linee guida basate sullo scenario di migrazione da servizi multimediali da V2 a V3 per lo streaming live
description: Questo articolo offre indicazioni basate sullo scenario di streaming live che ti aiuteranno a eseguire la migrazione minima da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 65b1c8906463002e144ee520855121ce18693e03
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898274"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Guida alla migrazione basata sullo scenario live streaming

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-4.svg)

Il portale di Azure supporta ora la configurazione e la gestione degli eventi live.  Si consiglia di provarlo durante il test della migrazione da V2 a V3.

## <a name="test-the-v3-live-event-workflow"></a>Testare il flusso di lavoro dell'evento Live V3

> [!NOTE]
> I canali e i programmi creati con V2 (mappati a eventi live e output in tempo reale in V3) verranno gestiti con l'API V3. Il materiale sussidiario prevede di passare agli eventi live V3 e agli output Live in un momento appropriato quando è possibile arrestare il canale V2 esistente. Attualmente non è disponibile alcun supporto per la migrazione senza interruzioni di un canale Live 24x7 con esecuzione continua nei nuovi eventi live V3. I tempi di inattività della manutenzione devono quindi essere coordinati con la migliore convenienza per i destinatari e le attività aziendali.

Testare la nuova modalità di distribuzione di eventi live con servizi multimediali prima di trasferire il contenuto dalla versione V2 alla versione V3. Di seguito sono riportate le funzionalità V3 da usare e considerate per la migrazione.

- Creare un nuovo [evento Live](live-events-outputs-concept.md#live-events) V3 per la codifica. È possibile abilitare i [set di impostazioni di codifica 1080p e 720p](live-event-types-comparison.md#system-presets).
- Usare l'entità di [output Live](live-events-outputs-concept.md#live-outputs) invece dei programmi
- Creare [localizzatori di streaming](streaming-locators-concept.md).
- Prendere in considerazione la necessità di eseguire lo streaming live [HLS e Dash](dynamic-packaging-overview.md) .
- Se è necessario avviare rapidamente eventi live, esplorare le nuove funzionalità della [modalità standby](live-events-outputs-concept.md#standby-mode) .
- Se si vuole trascrivere l'evento Live mentre è in corso, esplorare la nuova funzionalità di [trascrizione in tempo reale](live-transcription.md) .
- È possibile creare eventi live 24x7x365 in V3 se è necessaria una durata di streaming maggiore.
- USA [griglia di eventi](monitor-events-portal-how-to.md) per monitorare gli eventi live.

Per i passaggi specifici, vedere Concetti, esercitazioni e procedure dettagliate per gli eventi live.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Concetti, esercitazioni e guide sugli eventi Live

### <a name="concepts"></a>Concetti

- [Streaming live con Servizi multimediali di Azure v3](live-streaming-overview.md)
- [Eventi live e output live in Servizi multimediali](live-events-outputs-concept.md)
- [Codificatori di streaming live locali verificati](recommended-on-premises-live-encoders.md)
- [Usare gli output in tempo reale e di spostamento per creare la riproduzione video su richiesta](live-event-cloud-dvr.md)
- [Trascrizione in tempo reale (anteprima)](live-transcription.md)
- [Confronto tra tipi di eventi attivi](live-event-types-comparison.md)
- [Stati degli eventi live e fatturazione](live-event-states-billing.md)
- [Impostazioni di bassa latenza eventi Live](live-event-latency.md)
- [Codici di errore dell'evento live di servizi multimediali](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>Esercitazioni e guide introduttive

- [Esercitazione: flusso live con servizi multimediali](stream-live-tutorial-with-api.md)
- [Creare un evento di streaming live di Servizi multimediali di Azure con OBS](live-events-obs-quickstart.md)
- [Avvio rapido: Caricamento, codifica e streaming di contenuto con il portale](manage-assets-quickstart.md)
- [Guida introduttiva: creare un flusso live di servizi multimediali di Azure con Wirecast](live-events-wirecast-quickstart.md)

## <a name="samples"></a>Esempi

È anche possibile [confrontare il codice V2 e V3 negli esempi di codice](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
