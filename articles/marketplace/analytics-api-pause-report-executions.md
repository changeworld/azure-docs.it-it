---
title: Sospendere l'API per le esecuzioni di report
description: Usare questa API per sospendere l'esecuzione pianificata di un report di analisi del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584032"
---
# <a name="pause-report-executions-api"></a>Sospendere l'API per le esecuzioni di report

Questa API, in esecuzione, sospende l'esecuzione pianificata dei report.

**Sintassi della richiesta**

| Metodo | URI richiesta |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**Intestazione della richiesta**

| Intestazione | Tipo | Descrizione |
| ------------ | ------------- | ------------- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD) nel formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Parametro path**

nessuno

**Query parameter (Parametro di query)**

| Nome parametro | Obbligatoria | Tipo | Descrizione |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sì | string | ID del report da modificare |
|||||

**Glossario**

Nessuno

**Risposta**

Il payload della risposta è strutturato nel formato JSON seguente:

Codice di risposta: 200, 400, 401, 403, 404, payload di risposta 500:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      "RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glossario**

| Parametro | Descrizione |
| ------------ | ------------- |
| `ReportId` | Identificatore univoco universale (UUID) del report eliminato |
| `ReportName` | Nome assegnato al report durante la creazione |
| `Description` | Descrizione specificata durante la creazione del report |
| `QueryId` | ID query passato al momento della creazione del report |
| `Query` | Testo della query che verrà eseguito per il report |
| `User` | ID utente utilizzato per la creazione del report |
| `CreatedTime` | Ora di creazione del report. Il formato dell'ora è AAAA-MM-GGThh: mm: ssZ |
| `ModifiedTime` | Ora dell'Ultima modifica del report. Il formato dell'ora è AAAA-MM-GGThh: mm: ssZ |
| `StartTime` | Ora di inizio dell'esecuzione del report. Il formato dell'ora è AAAA-MM-GGThh: mm: ssZ |
| `ReportStatus` | Stato dell'esecuzione del report. I valori possibili sono Paused, Active e Inactive. |
| `RecurrenceInterval` | Intervallo di ricorrenza fornito durante la creazione del report |
| `RecurrenceCount` | Numero di ricorrenze fornito durante la creazione del report |
| `CallbackUrl` | URL callback specificato nella richiesta |
| `Format` | Formato dei file di report |
|||
