---
title: Aggiornare l'API report
description: Usare questa API per segnalare il parametro per i report di analisi del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583795"
---
# <a name="update-report-api"></a>Aggiornare l'API report

Questa API consente di modificare un parametro del report.

**Sintassi della richiesta**

| Metodo | URI richiesta |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
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

**Payload della richiesta**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Glossario**

Questa tabella elenca le definizioni chiave degli elementi nel payload della richiesta.

| Parametro | Obbligatoria | Descrizione | Valori consentiti |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Sì | Nome da assegnare al report | stringa |
| `Description` | No | Descrizione del report creato | string |
| `StartTime` | Sì | Timestamp dopo il quale inizierà la generazione del report | stringa |
| `RecurrenceInterval` | No | Frequenza con cui il report deve essere generato in ore. Il valore minimo è 4 | numero intero |
| `RecurrenceCount` | No | Numero di report da generare. Il valore predefinito è indefinito | numero intero |
| `Format` | Sì | Formato del file esportato. Il valore predefinito è CSV. | CSV/TSV |
| `CallbackUrl` | Sì | URL callback HTTPS da chiamare durante la generazione del report | string |
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
| `ReportId` | Identificatore univoco universale (UUID) del report in fase di aggiornamento |
| `ReportName` | Nome assegnato al report nel payload della richiesta |
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
