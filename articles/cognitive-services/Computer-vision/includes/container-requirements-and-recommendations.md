---
title: Indicazioni e requisiti per i contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 5cbf07ab97206ae3509701a6d6b0f71e961b0bf8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308778"
---
> [!NOTE]
> I requisiti e le indicazioni sono basati su benchmark con una singola richiesta al secondo, usando un'immagine di 8 MB di una lettera commerciale digitalizzata che contiene 29 righe e un totale di 803 caratteri.

La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore OCR di lettura.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Leggi 2,0-Anteprima | 1 core, 8 GB di memoria |    8 core, 16 GB di memoria |
| Leggi 3,2 | 8 core, 16 GB di memoria | 8 core, 24 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.
