---
title: Accesso a livello di codice alle domande comuni sui dati di analisi
description: Domande frequenti sull'accesso a livello di codice ai dati di analisi nel centro per i partner per le offerte del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583972"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Accesso a livello di codice alle domande comuni sui dati di analisi

Questo articolo illustra le domande frequenti su come accedere ai dati di analisi a livello di codice nel centro partner per le offerte del Marketplace commerciale.

## <a name="api-responses"></a>Risposte API

Quali sono i diversi scenari in cui è possibile ricevere una risposta API diversa da 200 (esito positivo)?

Questa tabella descrive le risposte API e le operazioni da eseguire se vengono ricevute.

| Descrizione errore | Codice di errore | Risolvere problemi |
| ------------ | ------------- | ------------- |
| Non autorizzata | 401 | Si tratta di un'eccezione di autenticazione. Verificare la correttezza del token Azure Active Directory (Azure AD). Il token Azure AD è valido per 60 minuti, dopo il quale è necessario rigenerare il token di Azure AD. |
| Nome di tabella non valido | 400 | Il nome del set di dati non è corretto. Controllare nuovamente il nome del set di dati chiamando l'API "Get all DataSets". |
| Nome di colonna non corretto | 400| Il nome della colonna nella query non è corretto. Controllare nuovamente il nome della colonna chiamando l'API "Get all DataSets" o fare riferimento ai nomi di colonna nelle tabelle seguenti:<br><ul><li>[Tabella Dettagli ordini](orders-dashboard.md#orders-details-table)</li><li>[Tabella dettagli utilizzo](usage-dashboard.md#usage-details-table)</li><li>[Tabella Dettagli cliente](customer-dashboard.md#customer-details-table)</li><li>[Tabella dettagli di Marketplace Insights](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Valore null o mancante | 400 | È possibile che siano presenti parametri obbligatori mancanti come parte del payload della richiesta dell'API. |
| Parametri del report non validi | 400 | Verificare che i parametri del report siano corretti. Ad esempio, è possibile assegnare un valore minore di 4 per il `RecurrenceInterval` parametro. |
| L'intervallo di ricorrenza deve essere compreso tra 4 e 90 | 400 | Verificare che il valore del `RecurrenceInterval` parametro request sia compreso tra 4 e 90. |
| QueryId non valido | 400 | Controllare nuovamente l'oggetto generato `QueryId` . |
| Parametri di report non validi per la creazione: l'ora di inizio del report deve essere almeno 4 ore dall'ora UTC corrente- | 400 | Il parametro dell'ora di inizio come parte del payload della richiesta non deve essere nel passato. L'ora di inizio del report deve essere di almeno 4 ore rispetto all'ora UTC corrente. |
| Il valore richiesto ' String ' non è stato trovato | 400 | Controllare se sono stati aggiornati i parametri della richiesta `callbackurl` o `format` . |
| Non sono stati trovati elementi con i filtri specificati. | 404 | Verificare il `reportID` parametro usato nell'API Get report executions. |
| Non sono state eseguite esecuzioni per le condizioni di filtro specificate. Verificare di nuovo il ID report o executionId e riprovare l'API dopo l'ora di esecuzione pianificata del report | 404 | Verificare che `reportId` sia corretto. Ripetere l'API dopo l'ora di esecuzione pianificata del report, come specificato nel payload della richiesta. |
| Si è verificato un errore interno durante la creazione del report. ID di correlazione <> | 500 | Verificare che il formato della data per i campi "StartTime", "QueryStartTime" e "QueryEndTime" siano corretti. |
| Servizio non disponibile | 500 | Se si riceve continuamente un servizio non disponibile (errore 5xx), creare un [ticket di supporto](support.md). |
||||

## <a name="no-records"></a>Nessun record

Si riceve la risposta API 200 quando si Scarica il report dal percorso protetto. Perché non ricevo alcun record?

Verificare che la stringa nella query includa uno dei valori consentiti per l'intestazione di colonna. Ad esempio, questa query restituirà zero risultati:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

In questo esempio, i valori consentiti per SKUBillingType sono paid o free. Per tutti i valori possibili per le varie colonne, vedere le tabelle seguenti:

- [Tabella Dettagli ordini](orders-dashboard.md#orders-details-table)
- [Tabella dettagli utilizzo](usage-dashboard.md#usage-details-table)
- [Tabella Dettagli cliente](customer-dashboard.md#customer-details-table)
- [Tabella dettagli di Marketplace Insights](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Passaggi successivi

- [API per l'accesso ai dati di analisi del Marketplace commerciale](analytics-available-apis.md)
