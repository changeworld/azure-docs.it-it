---
title: Migrazione ai contenitori Read V3. x
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la migrazione in contenitori OCR di Read v3.x
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/29/2021
ms.author: aahi
ms.openlocfilehash: 1cc17306265e6e8ba2e7fb3f570d0017b006b84f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284686"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Eseguire la migrazione in contenitori OCR di Read v3.x

Se si usa la versione 2 del contenitore OCR Read di Visione artificiale, leggere questo articolo per informazioni su come aggiornare l'applicazione per l'uso della versione 3.x del contenitore. 


## <a name="configuration-changes"></a>Modifiche di configurazione

* `ReadEngineConfig:ResultExpirationPeriod` non è più supportato. Il contenitore OCR Read include un processo cron compilato che rimuove i risultati e i metadati associati a una richiesta dopo 48 ore.
* `Cache:Redis:Configuration` non è più supportato. Nei contenitori v3.x la cache non viene usata, quindi non è necessario impostarla.

## <a name="api-changes"></a>Modifiche all'API

Il contenitore Read v3.2 usa la versione 3 dell'API Visione artificiale e ha gli endpoint seguenti:

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

Per informazioni dettagliate sull'aggiornamento delle applicazioni per l'uso della versione 3 dell'API Read basata sul cloud, vedere la [guida alla migrazione dell'API REST Visione artificiale v3](./upgrade-api-versions.md). Queste informazioni sono valide anche per il contenitore. Tenere presente che le operazioni di sincronizzazione sono supportate solo nei contenitori.

## <a name="memory-requirements"></a>Requisiti di memoria

I requisiti e le indicazioni sono basati su benchmark con una singola richiesta al secondo, usando un'immagine di 8 MB di una lettera commerciale digitalizzata che contiene 29 righe e un totale di 803 caratteri. La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore OCR di lettura.

|Contenitore  |Minima | Consigliato  |
|---------|---------|------|
|Read 3.2-preview | 8 core, 16 GB di memoria         | 8 core, 24 GB di memoria |

Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando run di Docker.

## <a name="storage-implementations"></a>Implementazioni dell'archiviazione

>[!NOTE]
> MongoDB non è più supportato nelle versioni 3.x del contenitore. I contenitori supportano invece Archiviazione di Azure e i file system offline.

| Implementazione |    Argomenti di runtime obbligatori |
|---------|---------|
|Livello file (predefinito)    | Non sono richiesti argomenti di runtime. Verrà usata la directory `/share`. |
|BLOB Azure    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Implementazioni della coda

Nel contenitore v3.x la coda RabbitMQ non è attualmente supportata. Le implementazioni supportate sono:

| Implementazione | Argomenti di runtime | Uso previsto |
|---------|---------|-------|
| In memoria (predefinito) | Non sono richiesti argomenti di runtime. | Sviluppo e test |
| Code di Azure | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Produzione |
| RabbitMQ    | Non disponibile | Produzione |

Per una maggiore ridondanza, il contenitore Read v3.x usa un timer di visibilità per garantire che le richieste possano essere elaborate correttamente in caso di arresto anomalo del sistema, durante l'esecuzione in una configurazione a più contenitori. 

Impostare il timer con `Queue:Azure:QueueVisibilityTimeoutInMilliseconds`, che imposta l'ora in cui un messaggio deve essere invisibile quando un altro ruolo di lavoro lo sta elaborando. Per evitare un'elaborazione ridondante delle pagine, è consigliabile impostare il periodo di timeout su 120 secondi. Il valore predefinito è 30 secondi.

| Valore predefinito | Valore consigliato |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](computer-vision-resource-container-config.md) per informazioni sulle impostazioni di configurazione.
* Per altre informazioni su come riconoscere il testo stampato e scritto a mano, vedere [Cenni preliminari sull'OCR](overview-ocr.md)
* Vedere l' [API Read](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) per informazioni dettagliate sui metodi supportati dal contenitore.
* Fare riferimento alle [domande frequenti](FAQ.md) per risolvere i problemi correlati alle funzionalità di Analisi del testo.
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)