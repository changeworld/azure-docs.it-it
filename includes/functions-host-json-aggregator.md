---
title: includere file
description: includere file
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92167654"
---
Specifica il numero di chiamate di funzione che vengono aggregate quando [si esegue il calcolo della metrica per Application Insights](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Proprietà |Predefinito  | Descrizione |
|---------|---------|---------| 
|batchSize|1000|Numero massimo di richieste da aggregare.| 
|flushTimeout|00:00:30|Intervallo massimo da aggregare.| 

Le chiamate di funzione vengono aggregate quando il primo dei due limiti viene raggiunto.
