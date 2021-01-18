---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: d07a5da3b9013700694f6c20102ef2e8c5066087
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256335"
---
Come minimo, è consigliabile usare almeno 30 immagini per ogni tag nel set di training iniziale. È anche opportuno raccogliere alcune immagini aggiuntive per testare il modello dopo il training.

Per eseguire il training del modello in modo efficace, usare le immagini con diversi oggetti visivi. Selezionare immagini diverse per:
* angolazione
* illuminazione
* background
* stile visivo
* soggetti singoli/raggruppati
* size
* type

Assicurarsi anche che tutte le immagini di training soddisfino i criteri seguenti:
* formato JPG, PNG, BMP o GIF
* dimensioni massime pari a 6 MB (4 MB per le immagini per la previsione)
* almeno 256 pixel sul bordo più corto. Le immagini più piccole verranno automaticamente ingrandite dal Servizio visione artificiale personalizzato

> [!NOTE]
> Trove, un progetto Microsoft Garage, consente di raccogliere e acquistare set di immagini a scopo di training. Una volta raccolte le immagini, è possibile scaricarle e quindi importarle nel progetto di Visione personalizzata nel modo consueto. Per altre informazioni, visitare la [pagina di Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3).