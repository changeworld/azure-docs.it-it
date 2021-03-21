---
title: Adozione di ontologie standard del settore
titleSuffix: Azure Digital Twins
description: Informazioni sulle Ontologie del settore esistenti che è possibile adottare per i dispositivi gemelli digitali di Azure
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124228"
---
# <a name="adopting-an-industry-ontology"></a>Adozione di un'ontologia del settore

Poiché può essere più facile iniziare con una ontologia DTDL open source rispetto a una pagina vuota, Microsoft collabora con esperti di dominio per pubblicare le ontologie, che rappresentano le convenzioni di settore ampiamente accettate e supportano un'ampia gamma di casi d'uso dei clienti. 

Il risultato è un set di ontologie basate su DTDL open source, che imparano da, compilano, imparano da o usano direttamente gli standard del settore. Le ontologie sono progettate per soddisfare le esigenze degli sviluppatori downstream, con la possibilità di essere ampiamente adottate e/o estese dal settore.

Attualmente, Microsoft ha collaborato con i partner per sviluppare una ontologia per [Smart Build](#realestatecore-smart-building-ontology) e una ontologia per le [città intelligenti](#smart-cities-ontology), che forniscono un terreno comune per la modellazione in base agli standard in questi settori per evitare la riinvenzione. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologia di compilazione intelligente RealEstateCore

*Trovare l'ontologia qui: [**RealEstateCore ontologia basata sul linguaggio di definizione dei gemelli digitali per gli edifici intelligenti**](https://github.com/Azure/opendigitaltwins-building)*.

Microsoft ha collaborato con [RealEstateCore](https://www.realestatecore.io/), un consorzio svedese di proprietari immobiliari, fornitori di software e istituti di ricerca, per fornire questa ontologia DTDL open source per il settore immobiliare.

Questa ontologia degli edifici intelligenti fornisce un terreno comune per la modellazione di edifici intelligenti, usando standard di settore (ad esempio [Brick schema](https://brickschema.org/ontology/) o [W3C Building topologia ontologia](https://w3c-lbd-cg.github.io/bot/index.html)) per evitare la riinvenzione. L'ontologia viene inoltre fornita con le procedure consigliate per la modalità di utilizzo e di estensione corretta. 

Per altre informazioni sulla struttura e sulle convenzioni di modellazione di ontologia, su come usarla, su come estenderla e su come contribuire, visitare il repository di ontologia su GitHub: [Azure/opendigitaltwins-Building](https://github.com/Azure/opendigitaltwins-building). 

È possibile anche leggere altre informazioni sulla collaborazione con RealEstateCore e gli obiettivi di questa iniziativa in questo post di Blog e il video di accompagnamento: [RealEstateCore, una ontologia di compilazione intelligente per i dispositivi gemelli digitali, è ora disponibile](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Ontologia delle città intelligenti

*Trovare l'ontologia qui: [**ontologia Digital gemell Definition Language (DTDL) per le città intelligenti**](https://github.com/Azure/opendigitaltwins-smartcities)*.

Microsoft ha collaborato con [Open agile Smart Cities (OASC)](https://oascities.org/) e [Sirus](https://sirus.be/) per offrire un'ontologia basata su DTDL per le città intelligenti, a partire da [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim). Oltre ad ETSI NGSI-LD, abbiamo valutato anche Saref4City, CityGML, ISO e altri.

La versione corrente di ontologia è incentrata su un insieme iniziale di modelli. Gli autori di ontologia accolgono il contributo per estendere il set iniziale di casi d'uso e migliorare i modelli esistenti. 

Per altre informazioni sull'ontologia, su come usarla e su come contribuire, visitare il repository di ontologia su GitHub: [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Per altre informazioni sulle partnership e sull'approccio per le città intelligenti, vedere questo post di Blog e il video correlato: [ontologia intelligente di città per i dispositivi gemelli digitali](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'estensione delle ontologie standard del settore per soddisfare le specifiche: [*concetti: estensione delle ontologie del settore*](concepts-ontologies-extend.md).

* In alternativa, continuare con il percorso per lo sviluppo di modelli basati su ontologie: [*uso delle strategie di ontologia in un percorso di sviluppo del modello*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).