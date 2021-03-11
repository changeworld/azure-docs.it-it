---
title: API per l'accesso ai dati di analisi del Marketplace commerciale
description: Usare queste API per accedere ai dati di analisi a livello di codice nel centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584152"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>API per l'accesso ai dati di analisi del Marketplace commerciale

Di seguito è riportato l'elenco delle API per accedere ai dati di analisi del Marketplace commerciale e alle relative funzionalità associate.

- [API pull del set di dati](#dataset-pull-apis)
- [API di gestione query](#query-management-apis)
- [API di gestione dei report](#report-management-apis)
- [API pull per l'esecuzione di report](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>API pull del set di dati

***Tabella 1: API pull del set di dati***

| **API** | **Funzionalità** |
| --- | --- |
| [Ottenere tutti i set di dati](analytics-api-get-all-datasets.md) | Ottiene tutti i set di impostazioni disponibili. Nei set di impostazioni sono elencate le tabelle, le colonne, le metriche e gli intervalli di tempo. |
|||

## <a name="query-management-apis"></a>API di gestione query

***Tabella 2: API di gestione query***

| **API** | **Funzionalità** |
| --- | --- |
| [Crea query report](analytics-programmatic-access.md#create-report-query-api) | Crea query personalizzate che definiscono il set di dati da cui devono essere esportate le colonne e le metriche. |
| [OTTENERE query del report](analytics-api-get-report-queries.md) | Ottiene tutte le query disponibili per l'utilizzo nei report. Ottiene tutte le query di sistema e definite dall'utente per impostazione predefinita. |
| [Elimina query report](analytics-api-delete-report-queries.md) | Elimina le query definite dall'utente. |
|||

## <a name="report-management-apis"></a>API di gestione dei report

***Tabella 3: API di gestione dei report***

| **API** | **Funzionalità** |
| --- | --- |
| [Creazione di report](analytics-programmatic-access.md#create-report-api) | Pianifica una query da eseguire a intervalli regolari. |
| [PROVA query report](analytics-api-try-report-queries.md) | Esegue un'istruzione di query del report. Restituisce solo 10 record che possono essere utilizzati da un partner per verificare se i dati sono quelli previsti. |
| [Genera report](analytics-api-get-report.md) | Ottenere tutti i report pianificati. |
| [Aggiorna report](analytics-api-update-report.md) | Modificare un parametro del report. |
| [Elimina report](analytics-api-delete-report.md) | Elimina tutti i record di report e di esecuzione del report. |
| [Sospendere le esecuzioni di report](analytics-api-pause-report-executions.md) | Sospende l'esecuzione pianificata dei report. |
| [Riprendere le esecuzioni di report](analytics-api-resume-report-executions.md) | Riprende l'esecuzione pianificata di un report sospeso. |
|||

## <a name="report-execution-pull-apis"></a>API pull per l'esecuzione di report

***Tabella 4: API pull per l'esecuzione di report***

| **API** | **Funzionalità** |
| --- | --- |
| [Ottenere le esecuzioni di report](analytics-programmatic-access.md#get-report-executions-api) | Ottenere tutte le esecuzioni che si sono verificate per un determinato report. |
|||

## <a name="next-steps"></a>Passaggi successivi

- È possibile provare le API tramite l' [URL dell'API spavalderia](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html).
