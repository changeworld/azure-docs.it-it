---
title: Elimina API query report
description: Usare questa API per eliminare le query definite dall'utente per l'analisi del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584162"
---
# <a name="delete-report-queries-api"></a>Elimina API query report

Questa API Elimina le query definite dall'utente.

**Sintassi della richiesta**

| **Metodo** | **URI richiesta** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**Intestazione della richiesta**

| **Intestazione** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD) nel formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |

**Parametro path**

| **Nome parametro** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| `queryId` | string | Filtrare per ottenere i dettagli delle sole query con l'ID specificato in questo argomento |

**Parametro di query**

nessuno

**Payload della richiesta**

nessuno

**Glossario**

Nessuno

**Risposta**

Il payload della risposta è strutturato come segue in formato JSON.

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

Questa tabella elenca le definizioni chiave degli elementi nella risposta.

| **Parametro** | **Descrizione** |
| --- | --- |
| `QueryId` | UUID univoco della query eliminata. |
| `Name` | Nome della query eliminata |
| `Description` | Descrizione della query eliminata |
| `Query` | Stringa di query del report della query eliminata |
| `Type` | userDefined |
| `User` | ID utente che ha creato la query |
| `CreatedTime` | Ora di creazione della query |
| `ModifiedTime` | Null |
| `TotalCount` | Numero di set di impostazioni nella matrice di valori |
| `StatusCode` | Codice risultato. I valori possibili sono 200, 400, 401, 403, 500 |
