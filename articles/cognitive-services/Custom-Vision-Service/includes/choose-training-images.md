---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 9f8eadea198bbae3de2ffc1b3aaac48925719586
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106083"
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
> Per completare la formazione è necessario un set più ampio di immagini? Trove, un progetto Microsoft Garage, consente di raccogliere e acquistare set di immagini a scopo di training. Una volta raccolte le immagini, è possibile scaricarle e quindi importarle nel progetto di Visione personalizzata nel modo consueto. Per altre informazioni, visitare la [pagina di Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3).