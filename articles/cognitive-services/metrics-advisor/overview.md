---
title: Che cos'è il servizio Advisor metriche?
titleSuffix: Azure Cognitive Services
description: Che cos'è Advisor metriche?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 901d86b5569be61f89178dac460b8750bce9ea73
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605536"
---
# <a name="what-is-metrics-advisor-preview"></a>Che cos'è Advisor metriche (anteprima)? 

Metrica Advisor fa parte dei servizi cognitivi di Azure che usa l'intelligenza artificiale per eseguire il monitoraggio dei dati e il rilevamento delle anomalie nei dati delle serie temporali. Il servizio automatizza il processo di applicazione dei modelli ai dati e fornisce un set di API e un'area di lavoro basata sul Web per l'inserimento dei dati, il rilevamento delle anomalie e la diagnostica, senza la necessità di conoscere l'apprendimento automatico. Gli sviluppatori possono creare applicazioni di AIOps, predicativo e di monitoraggio business sul servizio. Usare Advisor metriche per:

* Analizzare dati multidimensionali da più origini dati
* Identificare e correlare le anomalie
* Configurare e ottimizzare il modello di rilevamento anomalie usato sui dati
* Diagnosticare le anomalie e semplificare l'analisi delle cause principali

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Panoramica di Advisor metriche":::

## <a name="connect-to-a-variety-of-data-sources"></a>Connettersi a una varietà di origini dati

Advisor metriche può connettersi e [inserire dati di metriche multidimensionali](how-tos/onboard-your-data.md) da numerosi archivi dati, tra cui: SQL Server, Archiviazione BLOB di Azure, MongoDB e altri.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Rilevamento anomalie intuitivo e personalizzabile

* Advisor metriche seleziona automaticamente il modello migliore per i dati, senza che sia necessario conoscere le nozioni di Machine Learning.
* Monitorare automaticamente ogni serie temporale all'interno di [metriche multidimensionali](glossary.md#multi-dimensional-metric).
* Usare l'[ottimizzazione dei parametri](how-tos/configure-metrics.md) e il [feedback interattivo](how-tos/anomaly-feedback.md) per personalizzare il modello applicato ai dati e i risultati del rilevamento delle anomalie future.

## <a name="real-time-alerts-through-multiple-channels"></a>Avvisi in tempo reale tramite più canali

Ogni volta che vengono rilevate anomalie, Advisor metrica è in grado di [inviare avvisi in tempo reale](how-tos/alerts.md) tramite più canali usando Hook, ad esempio: hook di posta elettronica, webhook e hook di Azure DevOps. Le regole di avviso flessibili consentono di personalizzare gli avvisi inviati e la relativa destinazione.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Informazioni di diagnostica intelligenti mediante l'analisi delle anomalie

È possibile analizzare le anomalie rilevate nelle metriche multidimensionali e generare [informazioni di diagnostica intelligenti](how-tos/diagnose-incident.md) che includono la causa radice più probabile, gli alberi di diagnostica, l'esplorazione delle metriche e altro ancora. Configurando il [grafico delle metriche](how-tos/metrics-graph.md), è possibile abilitare l'analisi delle metriche incrociate per facilitare la visualizzazione degli eventi imprevisti.


## <a name="typical-workflow"></a>Flusso di lavoro tipico

Il flusso di lavoro è semplice: dopo l'onboarding dei dati, è possibile ottimizzare il rilevamento anomalie e creare configurazioni in base alla situazione.

1. [Creare una risorsa di Azure](https://go.microsoft.com/fwlink/?linkid=2142156) per Advisor metriche. 
2. Configurare il primo monitoraggio con il portale Web.
    1. Eseguire l'onboarding dei dati
    2. Ottimizzare il rilevamento anomalie
    3. Sottoscrivere gli avvisi
    4. Visualizzare le informazioni di diagnostica
3. Usare l'API REST per personalizzare l'istanza.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare una guida di avvio rapido: [Monitorare la prima metrica nel Web](quickstarts/web-portal.md).
* Esplorare una guida di avvio rapido: [Usare le API REST per personalizzare la soluzione](./quickstarts/rest-api-and-client-library.md).
