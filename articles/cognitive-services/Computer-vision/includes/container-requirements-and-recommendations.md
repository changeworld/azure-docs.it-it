---
title: Indicazioni e requisiti per i contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 2a399b683dc9596d3514ce7d3be1fa2444449e2a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284942"
---
> [!NOTE]
> I requisiti e le indicazioni sono basati su benchmark con una singola richiesta al secondo, usando un'immagine di 8 MB di una lettera commerciale digitalizzata che contiene 29 righe e un totale di 803 caratteri.

La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore OCR di lettura.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Leggi 2,0-Anteprima | 1 core, 8 GB di memoria |    8 core, 16 GB di memoria |
| Read 3.2-preview | 8 core, 16 GB di memoria | 8 core, 24 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.
