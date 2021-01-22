---
title: Limiti dei dati per l'API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Limitazioni dei dati per l'API Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 9ba9fe7ca73e874fb55c228e22b884a86de736cf
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661460"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Limiti dei dati e di velocità per l'API Analisi del testo
<a name="data-limits"></a>

Usare questo articolo per informazioni sui limiti di dimensioni e velocità per l'invio dei dati all'API Analisi del testo. Si noti che i prezzi non sono interessati dai limiti dei dati o dai limiti di velocità. I prezzi sono soggetti ai [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)della risorsa analisi del testo.

## <a name="data-limits"></a>Limiti dei dati

> [!NOTE]
> * Se è necessario analizzare documenti di dimensioni maggiori rispetto al limite consentito, è possibile suddividere il testo in blocchi di testo più piccoli prima di inviarli all'API. 
> * Un documento è costituito da una singola stringa di caratteri di testo.  

| Limite | valore |
|------------------------|---------------|
| Dimensioni massime di un singolo documento | 5\.120 caratteri misurati in base a [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Si applica anche ad Analisi del testo per la sanità. |
| Dimensioni massime di un singolo documento (endpoint `/analyze`)  | 125.000 caratteri misurati in base a [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Non si applica ad Analisi del testo per la sanità. |
| Dimensioni massime dell'intera richiesta | 1 MB. Si applica anche ad Analisi del testo per la sanità. |


Se un documento supera il limite di caratteri, l'API avrà un comportamento diverso a seconda dell'endpoint in uso:

* Endpoint `/analyze`:
  * L'API rifiuterà l'intera richiesta e restituirà un errore `400 bad request` se un documento al suo interno supera le dimensione massime.
* Tutti gli altri endpoint:  
  * L'API non elaborerà un documento che supera le dimensioni massime e restituirà un errore di documento non valido. Se una richiesta API include più documenti, l'API continuerà a elaborarli se rispettano il limite di caratteri.

Il numero massimo di documenti che è possibile inviare in una singola richiesta dipende dalla versione dell'API e dalla funzionalità in uso, come descritto nella tabella seguente.

#### <a name="version-3"></a>[Versione 3](#tab/version-3)

I limiti seguenti si applicano all'API v3 corrente. Il superamento dei limiti indicati di seguito provocherà un codice errore HTTP 400.


| Funzionalità | Numero massimo di documenti per richiesta | 
|----------|-----------|
| Rilevamento lingua | 1000 |
| Analisi del sentiment | 10 |
| Opinion mining | 10 |
| Estrazione frasi chiave | 10 |
| Riconoscimento di entità denominate | 5 |
| Collegamento delle entità | 5 |
| Analisi del testo per la sanità  | 10 per l'API basata sul Web, 1000 per il contenitore. |
| Endpoint di analisi | 25 per tutte le operazioni. |

#### <a name="version-2"></a>[Versione 2](#tab/version-2)

| Funzionalità | Numero massimo di documenti per richiesta | 
|----------|-----------|
| Rilevamento lingua | 1000 |
| Analisi del sentiment | 1000 |
| Estrazione frasi chiave | 1000 |
| Riconoscimento di entità denominate | 1000 |
| Collegamento delle entità | 1000 |

---

## <a name="rate-limits"></a>Limiti di velocità

Il limite di velocità varia in base al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) in uso. Questi limiti sono identici per entrambe le versioni dell'API. Questi limiti di velocità non si applicano al contenitore Analisi del testo per la sanità, che non prevede un limite di velocità impostato.

| Livello          | Richieste al secondo | Richieste al minuto |
|---------------|---------------------|---------------------|
| S/multiservizio | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Le percentuali di richieste vengono misurate per ogni funzionalità di Analisi del testo separatamente. È possibile inviare il numero massimo di richieste previsto per il piano tariffario per ogni funzionalità, contemporaneamente. Ad esempio, se con il livello `S` si inviano 1000 richieste contemporaneamente, non sarà possibile inviare un'altra richiesta per 59 secondi.


## <a name="see-also"></a>Vedere anche

* [Informazioni sull'API Analisi del testo](../overview.md)
* [Dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
