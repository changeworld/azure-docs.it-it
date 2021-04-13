---
title: Che cos'è l'API MultiVariante del rilevatore di anomalie?
titleSuffix: Azure Cognitive Services
description: Panoramica delle nuove API di anteprima pubblica del rilevatore di anomalie.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: rilevamento anomalie, Machine Learning, algoritmi
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315572"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Rilevamento di anomalie in serie temporali multivariate (anteprima pubblica)

La prima versione del rilevatore di anomalie dei servizi cognitivi di Azure permette di creare soluzioni di monitoraggio delle metriche usando le [API del rilevatore di anomalie della serie temporale](overview.md)di facile utilizzo. Consentendo l'analisi della serie temporale singolarmente, il rilevatore di anomalie univariato fornisce semplicità e scalabilità.

Le nuove API di **rilevamento delle anomalie multivariate** consentono inoltre agli sviluppatori di integrare in modo semplice l'intelligenza artificiale avanzata per il rilevamento di anomalie da gruppi di metriche, senza la necessità di informazioni di apprendimento automatico o dati con etichetta. Le dipendenze e le correlazioni intere tra un massimo di 300 segnali diversi vengono ora conteggiate automaticamente come fattori chiave. Questa nuova funzionalità consente di proteggere in modo proattivo i sistemi complessi, ad esempio applicazioni software, server, computer di fabbrica, veicoli spaziali o persino l'azienda, dagli errori.

Immaginate 20 sensori da un motore auto che genera 20 segnali diversi, ad esempio vibrazione, temperatura, pressione del carburante e così via. Le letture di questi segnali singolarmente potrebbero non indicare gran parte dei problemi a livello di sistema, ma insieme possono rappresentare l'integrità del motore. Quando l'interazione di questi segnali devia al di fuori dell'intervallo consueto, la funzionalità di rilevamento di anomalie a più variabili può rilevare l'anomalia come un esperto di esperti. I modelli di intelligenza artificiale sottostanti vengono sottoposti a training e personalizzati usando i dati in modo da comprendere le esigenze specifiche dell'azienda. Con le nuove API in rilevamento anomalie, gli sviluppatori possono ora integrare facilmente le funzionalità di rilevamento anomalie delle serie temporali multivariate in soluzioni di manutenzione predittiva, soluzioni di monitoraggio AIOps per software aziendale complesso o strumenti business intelligence.

## <a name="when-to-use-multivariate-versus-univariate"></a>Quando usare **MultiVariante** rispetto a **univariato**

Usare le API di rilevamento delle anomalie univariate, se l'obiettivo è quello di rilevare anomalie da un modello normale in ogni singola serie temporale esclusivamente in base ai dati cronologici. Esempi: si desidera rilevare le anomalie dei ricavi giornalieri in base ai dati di ricavi oppure si desidera rilevare un picco della CPU esclusivamente in base ai dati della CPU.
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![Grafico a linee della serie temporale con i valori fluttuanti di una singola variabile acquisiti da una linea blu con anomalie identificate da cerchi arancioni](./media/anomaly_detection2.png)

Usare le API di rilevamento delle anomalie multivariate di seguito, se l'obiettivo consiste nel rilevare le anomalie a livello di sistema da un gruppo di dati di serie temporali. In particolare, quando una singola serie temporale non comunica molto ed è necessario esaminare tutti i segnali (un gruppo di serie temporali) in modo olistico per determinare un problema a livello di sistema. Esempio: si dispone di un asset fisico costoso, ad esempio aereo, attrezzatura su un impianto di petrolio o satellite. Ognuno di questi asset dispone di decine o centinaia di tipi diversi di sensori. È necessario esaminare tutti questi segnali della serie temporale da tali sensori per decidere se è presente un problema a livello di sistema.

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![Più grafici a linee della serie temporale per le variabili: vibrazione, temperatura, pressione, velocità, velocità di rotazione con anomalie evidenziate in arancione](./media/multivariate-graph.png)

## <a name="region-support"></a>Supporto di area

L'anteprima pubblica del rilevatore di anomalie MultiVariante è attualmente disponibile in tre aree: West Uniti, East Uniti ed Europa occidentale.

## <a name="algorithms"></a>Algoritmi

- [Rilevamento di anomalie in serie temporali multivariate tramite la rete Graph attention](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Partecipare alla community di Rilevamento anomalie

- Partecipare al [gruppo Anomaly Detector Advisors su Microsoft Teams](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>Passaggi successivi

- [Guide introduttive](./quickstarts/client-libraries-multivariate.md).
- [Procedure](./concepts/best-practices-multivariate.md)consigliate: questo articolo descrive i modelli consigliati da usare con le API multivariate.
