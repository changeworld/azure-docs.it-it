---
title: Ottenere l'API per le query di report
description: Usare questa API per ottenere tutte le query disponibili per l'uso nei report di analisi del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584092"
---
# <a name="get-report-queries-api"></a>Ottenere l'API per le query di report

L'API Get report queries ottiene tutte le query disponibili per l'utilizzo nei report. Ottiene tutte le query di sistema e definite dall'utente per impostazione predefinita.

**Sintassi della richiesta**

| **Metodo** | **URI richiesta** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**Intestazione della richiesta**

| **Intestazione** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD) nel formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Parametro path**

nessuno

**Parametro di query**

| **Nome parametro** | **Tipo** | **Obbligatorio** | **Descrizione** |
| --- | --- | --- | --- |
| `queryId` | stringa | No | Filtrare per ottenere i dettagli delle sole query con l'ID specificato nell'argomento |
| `queryName` | stringa | No | Filtrare per ottenere i dettagli delle sole query con il nome specificato nell'argomento |
| `IncludeSystemQueries` | boolean | No | Includere query di sistema predefinite nella risposta |
| `IncludeOnlySystemQueries` | boolean | No | Includi solo query di sistema nella risposta |
|||||

**Payload della richiesta**

nessuno

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
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glossario**

In questa tabella vengono descritte le definizioni chiave degli elementi nella risposta.

| **Parametro** | **Descrizione** |
| --- | --- |
| `QueryId` | UUID univoco della query |
| `Name` | Nome assegnato alla query al momento della creazione della query |
| `Description` | Descrizione specificata durante la creazione della query |
| `Query` | Stringa di query del report |
| `Type` | Impostare su `userDefined` per le query create dall'utente e `system` per le query di sistema predefinite |
| `User` | ID utente che ha creato la query |
| `CreatedTime` | Data e ora della creazione della query |
| `TotalCount` | Numero di set di impostazioni nella matrice di valori |
| `StatusCode` | Codice risultato. I valori possibili sono 200, 400, 401, 403, 500 |
|||
