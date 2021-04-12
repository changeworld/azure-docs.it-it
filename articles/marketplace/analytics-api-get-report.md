---
title: Ottenere l'API del report
description: Usare questa API per ottenere i report di analisi pianificati nel centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584049"
---
# <a name="get-report-api"></a>Ottenere l'API del report

Questa API ottiene tutti i report pianificati.

**Sintassi della richiesta**

| **Metodo** | **URI richiesta** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**Intestazione della richiesta**

| **Intestazione** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD) nel formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |

**Parametro path**

nessuno

**Parametro di query**

| **Nome parametro** | **Obbligatorio** | **Tipo** | **Descrizione** |
| --- | --- | --- | --- |
| `reportId` | No | string | Filtrare per ottenere i dettagli solo dei report con l'oggetto `reportId` specificato in questo argomento |
| `reportName` | No | string | Filtrare per ottenere i dettagli solo dei report con l'oggetto `reportName` specificato in questo argomento |
| `queryId` | No | boolean | Includere query di sistema predefinite nella risposta |

**Glossario**

Nessuno

**Risposta**

Il payload della risposta è strutturato in formato JSON come segue:

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
      " RecurrenceCount": 0,
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

Questa tabella elenca le definizioni chiave degli elementi nella risposta.

| **Parametro** | **Descrizione** |
| --- | --- |
| `ReportId` | UUID univoco del report creato |
| `ReportName` | Nome assegnato al report nel payload della richiesta |
| `Description` | Descrizione specificata al momento della creazione del report |
| `QueryId` | ID query passato al momento della creazione del report |
| `Query` | Testo della query che verrà eseguito per il report |
| `User` | ID utente utilizzato per creare il report |
| `CreatedTime` | Ora di creazione del report. Il formato dell'ora è AAAA-MM-GGThh: mm: ssZ |
| `ModifiedTime` | Ora dell'Ultima modifica del report. Il formato dell'ora è AAAA-MM-GGThh: mm: ssZ |
| `StartTime` | L'esecuzione dell'ora inizierà. Il formato dell'ora è AAAA-MM-GGThh: mm: ssZ |
| `ReportStatus` | Stato dell'esecuzione del report. I valori possibili sono Paused, Active e Inactive. |
| `RecurrenceInterval` | Intervallo di ricorrenza fornito durante la creazione del report |
| `RecurrenceCount` | Numero di ricorrenze fornito durante la creazione del report |
| `CallbackUrl` | URL callback specificato nella richiesta |
| `Format` | Formato dei file di report |
