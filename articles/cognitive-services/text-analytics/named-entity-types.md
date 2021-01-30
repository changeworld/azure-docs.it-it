---
title: Categorie supportate per il riconoscimento delle entità denominate
titleSuffix: Azure Cognitive Services
description: Informazioni sulle categorie di entità supportate nel API Analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 883c5a20612f4dab44c0d06776ee287b27174ab9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097271"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Categorie di entità supportate nella API Analisi del testo V3

Usare questo articolo per trovare le categorie di entità che possono essere restituite dal [riconoscimento delle entità denominate](how-tos/text-analytics-how-to-entity-linking.md) (ner). NER esegue un modello predittivo per identificare e classificare le entità denominate da un documento di input.

È disponibile anche un'anteprima di NER v 3.1, che include la possibilità di rilevare informazioni personali ( `PII` ) e di integrità ( `PHI` ). Inoltre, fare clic sulla scheda **integrità** per visualizzare un elenco di categorie supportate in analisi del testo per l'integrità. 

È possibile trovare un elenco di tipi restituiti dalla versione 2,1 nella [Guida alla migrazione](migration-guide.md?tabs=named-entity-recognition)

## <a name="entity-categories"></a>Categorie di entità

#### <a name="general"></a>[Generale](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Salute](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

**_

## <a name="next-steps"></a>Passaggi successivi

_ [Come usare il riconoscimento delle entità denominate in analisi del testo](how-tos/text-analytics-how-to-entity-linking.md)
