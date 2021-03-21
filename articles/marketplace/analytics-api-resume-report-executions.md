---
title: Riprendere l'API per le esecuzioni di report
description: Usare questa API per riprendere l'esecuzione pianificata di un report di analisi del Marketplace commerciale sospeso.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4a11783b28352cb62c5a3c0d38e45dcdc47a8d86
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583936"
---
# <a name="resume-report-executions-api"></a>Riprendere l'API per le esecuzioni di report

Questa API, durante l'esecuzione, riprende l'esecuzione pianificata di un report di analisi del Marketplace commerciale sospeso.

**Sintassi della richiesta**

| Metodo | URI richiesta |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
|||

**Intestazione della richiesta**

| Intestazione | Tipo | Descrizione |
| ------------ | ------------- | ------------- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD) nel formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Parametro path**

nessuno

**Parametro di query**

| Nome parametro | Obbligatoria | Tipo | Descrizione |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sì | string | ID del report da modificare |
|||||

**Glossario**

Nessuno

**Risposta**

Il payload della risposta è strutturato nel formato JSON seguente:

Codice di risposta: 200, 400, 401, 403, 404, 500

Payload risposta:

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
| `ReportId` | UUID (Universally Unique Identifier) del report ripreso |
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
