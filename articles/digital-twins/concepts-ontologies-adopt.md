---
title: Adozione di ontologie standard del settore
titleSuffix: Azure Digital Twins
description: Informazioni sulle Ontologie del settore esistenti che è possibile adottare per i dispositivi gemelli digitali di Azure
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561661"
---
# <a name="adopting-an-industry-ontology"></a>Adozione di un'ontologia del settore

Poiché può essere più facile iniziare con una ontologia DTDL open source rispetto a una pagina vuota, Microsoft collabora con esperti di dominio per pubblicare le ontologie, che rappresentano le convenzioni di settore ampiamente accettate e supportano un'ampia gamma di casi d'uso dei clienti. 

Il risultato è un set di ontologie basate su DTDL open source, che imparano da, compilano, imparano da o usano direttamente gli standard del settore. Le ontologie sono progettate per soddisfare le esigenze degli sviluppatori downstream, con la possibilità di essere ampiamente adottate e/o estese dal settore.

Attualmente Microsoft ha collaborato con i partner immobiliari per sviluppare una ontologia per Smart Build, che fornisce un terreno comune per la modellazione di edifici intelligenti basati sugli standard del settore per evitare la riinvenzione. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologia di compilazione intelligente RealEstateCore

Microsoft ha collaborato con [RealEstateCore](https://www.realestatecore.io/), un consorzio svedese di proprietari immobiliari, fornitori di software e istituti di ricerca, per offrire un'ontologia DTDL open source per il settore immobiliare, ovvero l' [**ontologia RealEstateCore basata su DTDL per gli edifici intelligenti**](https://github.com/Azure/opendigitaltwins-building).

Questa ontologia degli edifici intelligenti fornisce un terreno comune per la modellazione di edifici intelligenti, usando standard di settore (ad esempio [Brick schema](https://brickschema.org/ontology/) o [W3C Building topologia ontologia](https://w3c-lbd-cg.github.io/bot/index.html)) per evitare la riinvenzione. L'ontologia viene inoltre fornita con le procedure consigliate per la modalità di utilizzo e di estensione corretta. 

Per altre informazioni sulla struttura e sulle convenzioni di modellazione di ontologia, su come usarla, su come estenderla e su come contribuire, visitare il repository di ontologia [su GitHub](https://github.com/Azure/opendigitaltwins-building). 

È possibile anche leggere altre informazioni sulla collaborazione con RealEstateCore e gli obiettivi di questa iniziativa in questo post di Blog e il video di accompagnamento: [*RealEstateCore, una ontologia di compilazione intelligente per i dispositivi gemelli digitali, è ora disponibile*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'estensione delle ontologie standard del settore per soddisfare le specifiche: [*concetti: estensione delle ontologie del settore*](concepts-ontologies-extend.md).

* In alternativa, continuare con il percorso per lo sviluppo di modelli basati su ontologie: [*uso delle strategie di ontologia in un percorso di sviluppo del modello*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).