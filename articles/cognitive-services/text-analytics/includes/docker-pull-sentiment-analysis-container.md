---
title: Docker pull per il contenitore Analisi del sentiment contenitore
titleSuffix: Azure Cognitive Services
description: Comando docker pull per Analisi del sentiment contenitore
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564450"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker pull per il contenitore Analisi del sentiment v3

Il contenitore del contenitore di analisi del sentiment v3 è disponibile in diverse lingue. Per scaricare il contenitore per la lingua inglese, usare il comando seguente. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Per scaricare il contenitore per un'altra lingua, `en` sostituire con uno dei codici di lingua seguenti. 

| Analisi del testo contenitore | Codice lingua |
|--|--|
| Cinese semplificato    |   `zh-hans`   |
| Cinese tradizionale   |   `zh-hant`   |
| Olandese                 |     `nl`      |
| Inglese               |     `en`      |
| Francese                |     `fr`      |
| Tedesco                |     `de`      |
| Hindi                 |    `hi`       |
| Italiano               |     `it`      |
| Giapponese              |     `ja`      |
| Coreano                |     `ko`      |
| Norvegese (Bokmål)   |     `no`      |
| Portoghese (Brasile)   |    `pt-BR`    |
| Portoghese (Portogallo) |    `pt-PT`    |
| Spagnolo               |     `es`      |
| Turco               |     `tr`      |

Per una descrizione completa dei tag disponibili per i contenitori Analisi del testo, [vedere Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
