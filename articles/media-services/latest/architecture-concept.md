---
title: Architetture di Servizi multimediali
description: Questo articolo descrive le architetture per servizi multimediali.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: 4d14ab8b72e3e120e8ee8cc2be4ae29f20c32e87
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964173"
---
# <a name="media-services-architectures"></a>Architetture di Servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>Servizi multimediali digitali in streaming live

Una soluzione di streaming live ti permette di acquisire video in tempo reale e di trasmetterli ai consumatori in tempo reale, ad esempio interviste di streaming, conferenze e eventi sportivi online. In questa soluzione, i video vengono acquisiti da una videocamera e inviati a un endpoint di input del canale. Il canale riceve il flusso di input Live e lo rende disponibile per lo streaming tramite un endpoint di streaming a un Web browser o a un'app per dispositivi mobili. Il canale fornisce anche un endpoint di monitoraggio dell'anteprima per visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e recapito. Il canale può anche registrare e archiviare il contenuto inserito per poter essere trasmesso in un secondo momento (video on demand).

Questa soluzione si basa sui servizi gestiti di Azure: Servizi multimediali e rete per la distribuzione di contenuti. Questi servizi vengono eseguiti in un ambiente a disponibilità elevata, con patch e supportati, consentendo di concentrarsi sulla soluzione anziché sull'ambiente in cui vengono eseguiti.

Vedere [Live streaming Digital Media](/azure/architecture/solution-ideas/articles/digital-media-live-stream) nel centro architetture di Azure.

## <a name="video-on-demand-digital-media"></a>Servizi multimediali digitali di video su richiesta

Una soluzione video on demand di base che ti offre la possibilità di trasmettere contenuti video registrati, come filmati, notizie, clip, segmenti sportivi, video di formazione ed esercitazioni sul supporto tecnico per qualsiasi dispositivo endpoint con supporto per video, applicazione per dispositivi mobili o browser desktop. I file video vengono caricati nell'archiviazione BLOB di Azure, codificati in un formato standard a più velocità in bit e quindi distribuiti tramite tutti i principali protocolli di streaming con velocità in bit adattiva (HLS, MPEG-DASH, Smooth) al client di Azure Media Player.

Questa soluzione si basa sui servizi gestiti di Azure: archiviazione BLOB, rete per la distribuzione di contenuti e Azure Media Player. Questi servizi vengono eseguiti in un ambiente a disponibilità elevata, con patch e supportati, consentendo di concentrarsi sulla soluzione anziché sull'ambiente in cui vengono eseguiti.

Vedere i [supporti digitali video on demand](/azure/architecture/solution-ideas/articles/digital-media-video) nel centro architetture di Azure.

## <a name="gridwich-media-processing-system"></a>Sistema di elaborazione multimediale Gridwich

Il sistema Gridwich illustra le procedure consigliate per l'elaborazione e la distribuzione di asset multimediali in Azure. Sebbene il sistema Gridwich sia specifico del supporto, il Framework di elaborazione dei messaggi e di eventi può essere applicato a qualsiasi flusso di lavoro di elaborazione di eventi senza stato.

Vedere [Gridwich Media Processing System](/azure/architecture/reference-architectures/media-services/gridwich-architecture) nel centro architetture di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [Panoramica di Servizi multimediali di Azure](media-services-overview.md)