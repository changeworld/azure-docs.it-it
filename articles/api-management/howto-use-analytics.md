---
title: Usare analisi API in gestione API di Azure | Microsoft Docs
description: Usare l'analisi in gestione API di Azure per comprendere e categorizzare l'uso delle API e delle prestazioni dell'API.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96841436"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Ottenere analisi API in gestione API di Azure

Gestione API di Azure offre analisi predefinite per le API. Analizzare l'utilizzo e le prestazioni delle API nell'istanza di gestione API in diverse dimensioni, tra cui:

* Tempo
* Area geografica
* API
* Operazioni API
* Prodotti
* Sottoscrizioni
* Utenti
* Requests

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Analisi della cronologia nel portale":::

Usare Analytics per il monitoraggio di alto livello e la risoluzione dei problemi delle API. Per altre funzionalità di monitoraggio, incluse le metriche quasi in tempo reale e i log delle risorse per la diagnostica e il controllo, vedere [esercitazione: monitorare le API pubblicate](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Analytics-portale

Usare il portale di Azure per esaminare i dati di analisi a colpo d'occhio per l'istanza di gestione API.

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API. 
1. Nel menu a sinistra selezionare **Analytics** in **monitoraggio**.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Selezionare Analytics per l'istanza di gestione API nel portale":::  
1. Selezionare un intervallo di tempo per i dati oppure immettere un intervallo di tempo personalizzato.
1. Selezionare una categoria di report per i dati di analisi, ad esempio **sequenza temporale**, **Geografia** e così via.
1. Facoltativamente, filtrare il report in base a una o più categorie aggiuntive.

## <a name="analytics---rest-api"></a>Analytics-API REST

Usare le operazioni di [report](/rest/api/apimanagement/2019-12-01/reports) nell'API REST di gestione API per recuperare e filtrare i dati di analisi per l'istanza di gestione API.

Le operazioni disponibili restituiscono record di report per API, geografia, operazioni API, prodotto, richiesta, sottoscrizione, ora o utente.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle funzionalità di monitoraggio di Azure in gestione API, vedere [esercitazione: monitorare le API pubblicate](api-management-howto-use-azure-monitor.md)
* Per informazioni dettagliate su registrazione e monitoraggio HTTP, vedere [monitorare le API con gestione API di Azure, Hub eventi e Moesif](api-management-log-to-eventhub-sample.md).
* Informazioni sull'integrazione di [gestione API di Azure con applicazione Azure Insights](api-management-howto-app-insights.md).