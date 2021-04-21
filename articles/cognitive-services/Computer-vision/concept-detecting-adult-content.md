---
title: Contenuto per adulti, inciso e Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti correlati al rilevamento di contenuto per adulti nelle immagini usando l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceef604fe07a11be89376e26c6fecc49298ebacf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778860"
---
# <a name="detect-adult-content"></a>Rilevare contenuto per adulti

Visione artificiale possibile rilevare materiale per adulti nelle immagini in modo che gli sviluppatori possano limitare la visualizzazione di queste immagini nel proprio software. I flag di contenuto vengono applicati con un punteggio compreso tra zero e uno in modo che gli sviluppatori possano interpretare i risultati in base alle proprie preferenze.

> [!NOTE]
> Gran parte di questa funzionalità è offerta dal servizio [azure Content Moderator.](../content-moderator/overview.md) Vedere questa alternativa per soluzioni per scenari più rigorosi di moderazione del contenuto, ad esempio per flussi di lavoro di moderazione del testo e di revisione umana.

## <a name="content-flag-definitions"></a>Definizioni dei flag di contenuto

La classificazione "adulto" contiene diverse categorie:

- **Le immagini** per adulti sono di natura esplicitamente di natura sessuale e spesso mostrano atti di sesso e di sesso.
- **Le immagini** incise sono di natura sessualmente allusiva e spesso contengono contenuto meno esplicito rispetto alle immagini contrassegnate come **adulte.**
- **Le immagini gory** mostrano il sangue/gore.

## <a name="use-the-api"></a>Usare l'API

È possibile rilevare contenuto per adulti con l'API [Analizza immagine.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) Quando si aggiunge il valore di al parametro `Adult` di query **visualFeatures,** l'API restituisce tre proprietà booleane &mdash; `isAdultContent` , e nella `isRacyContent` risposta `isGoryContent` &mdash; JSON. Il metodo restituisce anche le proprietà corrispondenti , e che rappresentano i punteggi di attendibilità compresi tra zero e &mdash; `adultScore` uno per ogni `racyScore` rispettiva `goreScore` &mdash; categoria.

- [Avvio rapido: API REST o librerie client di Visione artificiale](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
