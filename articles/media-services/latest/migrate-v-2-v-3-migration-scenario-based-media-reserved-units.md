---
title: Guida alla migrazione di media reserved Unit (MRU)
description: Questo articolo fornisce informazioni di base sugli scenari che consentono di eseguire la migrazione da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 00e1fa623fc625a568f6580998443758c3916be8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563073"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>Guida alla migrazione basata su scenari di media reserved Unit (MRU)

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-4.svg)

Questo articolo fornisce informazioni di base sugli scenari che consentono di eseguire la migrazione da servizi multimediali di Azure V2 a V3.

- Per i nuovi account V3 creati nel portale di Azure o con la versione 2020-05-01 dell'API V3, non è più necessario impostare Media reserved Unit (MRU). Il sistema verrà ora ridimensionato automaticamente in base al carico.
- Se si dispone di un account V3 o V2 creato prima della versione 2020-05-01 dell'API, è comunque possibile controllare la concorrenza e le prestazioni dei processi usando Media reserved unit. Per altre informazioni, vedere Panoramica del ridimensionamento dell'elaborazione multimediale. È possibile gestire MRU usando l'interfaccia della riga di comando 2,0 per Media Services V3 o usando il portale di Azure.  
- Se non viene visualizzata l'opzione per gestire MRU nel portale di Azure, viene eseguito un account creato con l'API 2020-05-01 o versione successiva.
- Se si ha familiarità con l'impostazione del tipo MRU su S3, le prestazioni miglioreranno o rimarranno invariate.
- Se si è un cliente V2 esistente, è necessario creare un nuovo account V2 per supportare l'applicazione esistente prima del completamento della migrazione. 
- Potrebbe essere necessario abilitare nuovamente l'indicizzatore V1 o altri processori di contenuti multimediali che non sono completamente deprecati. 

Per altre informazioni su MRU, vedere [media reserved Unit](concept-media-reserved-units.md) e [How to scale media reserved Unit](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Ultimi concetti, esercitazioni e procedure guidate

### <a name="concepts"></a>Concetti

[Media reserved unit](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Guide pratiche

[Come ridimensionare media reserved unit](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Esempi

È anche possibile [confrontare il codice V2 e V3 negli esempi di codice](migrate-v-2-v-3-migration-samples.md).
