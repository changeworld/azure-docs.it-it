---
title: Rilevamento di oggetti - Visione artificiale
titleSuffix: Azure Cognitive Services
description: "Informazioni sui concetti relativi alla funzionalità di rilevamento oggetti dell'API Visione artificiale: utilizzo e limiti."
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a705a4134ec22d1cb14406cab4491f2af9177b48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767998"
---
# <a name="detect-common-objects-in-images"></a>Rilevare oggetti comuni nelle immagini

Il rilevamento di oggetti è simile all'[assegnazione di tag](concept-tagging-images.md), ma l'API restituisce le coordinate del rettangolo di selezione (in pixel) per ogni oggetto trovato. Se, ad esempio, un'immagine contiene un cane, gatto e una persona, l'operazione di rilevamento elencherà tali oggetti insieme alle relative coordinate nell'immagine. È possibile usare questa funzionalità per elaborare le relazioni tra gli oggetti in un'immagine. Consente inoltre di determinare se sono presenti più istanze dello stesso tag in un'immagine.

L'API di rilevamento applica tag basati sugli oggetti o sugli esseri viventi identificati nell'immagine. Attualmente non esiste alcuna relazione formale tra la tassonomia di assegnazione di tag e la tassonomia di rilevamento degli oggetti. A livello concettuale, l'API Di rilevamento trova solo oggetti e esseri viventi, mentre l'API Tag può anche includere termini contestuali come "interni", che non possono essere localizzati con relimiti.

## <a name="object-detection-example"></a>Esempio di rilevamento di oggetti

La risposta JSON seguente illustra le informazioni restituite da Visione artificiale quando viene eseguito il rilevamento degli oggetti nell'immagine di esempio.

![Donna con un dispositivo Microsoft Surface in una cucina](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>Limitazioni

È importante notare le limitazioni del rilevamento degli oggetti in modo da evitare o attenuare gli effetti di falsi negativi (oggetti persi) e dettagli limitati.

* Gli oggetti in genere non vengono rilevati se sono di piccole dimensioni (meno del 5% dell'immagine).
* Gli oggetti in genere non vengono rilevati se sono disposti strettamente tra loro (ad esempio, una pila di targhe).
* Gli oggetti non vengono differenziati in base alla marca o ai nomi di prodotto (ad esempio, diversi tipi di bibite su uno scaffale di un supermercato). È tuttavia possibile ottenere informazioni sul marchio presente in un'immagine usando la funzionalità per il [rilevamento del marchio](concept-brand-detection.md).

## <a name="use-the-api"></a>Usare l'API

La funzionalità per il rilevamento di oggetti fa parte dell'API di [analisi delle immagini](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b). È possibile chiamare questa API tramite un SDK nativo o con chiamate REST. Includere `Objects` nel parametro di query **visualFeatures.** Quindi, quando si ottiene la risposta JSON completa, è sufficiente analizzare la stringa per il contenuto della `"objects"` sezione .

* [Avvio rapido: API REST o librerie client di Visione artificiale](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
