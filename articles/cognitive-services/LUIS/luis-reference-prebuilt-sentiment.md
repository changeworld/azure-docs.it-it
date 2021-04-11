---
title: Analisi del sentiment - LUIS
titleSuffix: Azure Cognitive Services
description: Se l'analisi del sentiment è configurata, è inclusa nella risposta json LUIS.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/06/2021
ms.openlocfilehash: 7524644b34a6fd479c08b9ce6418c547c836add5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554026"
---
# <a name="sentiment-analysis"></a>Analisi del sentiment
Se l'analisi del sentiment è configurata, è inclusa nella risposta json LUIS. Per ulteriori informazioni sull'analisi del sentiment, vedere la documentazione [Analisi del testo](../text-analytics/index.yml).

LUIS USA Analisi del testo V2. 

Analisi del sentiment viene configurato quando si pubblica l'applicazione. Per altre informazioni [, vedere come pubblicare un'app](./luis-how-to-publish-app.md) .

## <a name="resolution-for-sentiment"></a>Risoluzione per i sentimenti

I dati sentiment sono un punteggio compreso tra 1 e 0 che indica il sentiment positivo (più vicino a 1) o negativo (più vicino a 0) dei dati.

#### <a name="english-language"></a>[Lingua inglese](#tab/english)

Quando le impostazioni cultura sono `en-us`, la risposta è:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Altri linguaggi](#tab/other-languages)

Per tutte le altre impostazioni cultura, la risposta è:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).
