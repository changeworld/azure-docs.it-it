---
title: Elimina API report
description: Usare questa API per eliminare tutti i record di report e di esecuzione del report per i report di analisi del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7c39f8bc0db44f1d8aa885969ca09d90b0dcd332
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584112"
---
# <a name="delete-report-api"></a>Elimina API report

Durante l'esecuzione, questa API Elimina tutti i record del report e dell'esecuzione del report.

**Sintassi della richiesta**

| Metodo | URI richiesta |
| ------------ | ------------- |
| DOLETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Intestazione della richiesta**

| Intestazione | Tipo | Descrizione |
| ------------ | ------------- | ------------- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure AD nel formato `Bearer <token>` |
| Tipo di contenuto | string | `Application/JSON` |
||||

**Parametro path**

nessuno

**Parametro di query**

| Nome parametro | Necessario | string | Descrizione |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sì | string | ID del report che viene modificato |
|||||

**Glossario**

Nessuno

**Risposta**

Il payload della risposta è strutturato nel modo seguente:

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
| `ReportId` | UUID univoco del report eliminato |
| `ReportName` | Nome assegnato al report durante la creazione |
| `Description` | Descrizione specificata durante la creazione del report |
| `QueryId` | ID query passato al momento della creazione del report |
| `Query` | Testo della query che verrà eseguito per il report |
| `User` | ID utente utilizzato per creare il report |
| `CreatedTime` | Ora di creazione del report. Il formato dell'ora è AAAA-MM-GGThh: mm: ssZ |
| `ModifiedTime` | Ora dell'Ultima modifica del report. Il formato dell'ora è AAAA-MM-GGThh: mm: ssZ |
| `StartTime` | Ora di inizio dell'esecuzione del report. Il formato dell'ora è AAAA-MM-GGThh: mm: ssZ |
| `ReportStatus` | Stato dell'esecuzione del report. I valori possibili sono Paused, Active e Inactive. |
| `RecurrenceInterval` | Intervallo di ricorrenza fornito durante la creazione del report |
| `RecurrenceCount` | Numero di ricorrenze fornito durante la creazione del report |
| `CallbackUrl` | URL callback specificato nella richiesta |
| `Format` | Formato dei file di report |
|||
