---
title: Ottenere tutti i set di impostazioni API
description: Usare questa API per ottenere tutti i set di dati disponibili per l'analisi del Marketplace commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583945"
---
# <a name="get-all-datasets-api"></a>Ottenere tutti i set di impostazioni API

L'API Get all DataSets ottiene tutti i set di impostazioni disponibili. Nei set di impostazioni sono elencate le tabelle, le colonne, le metriche e gli intervalli di tempo.

**Sintassi della richiesta**

| **Metodo** | **URI richiesta** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**Intestazione della richiesta**

| **Intestazione** | **Tipo** | **Descrizione** |
| --- | --- | --- |
| Autorizzazione | string | Obbligatorio. Il token di accesso Azure Active Directory (Azure AD) nel formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |

**Parametro path**

nessuno

**Query parameter (Parametro di query)**

| **Nome parametro** | **Tipo** | **Obbligatorio** | **Descrizione** |
| --- | --- | --- | --- |
| `datasetName` | stringa | No | Filtrare per ottenere i dettagli di un solo set di dati |

**Payload della richiesta**

nessuno

**Glossario**

Nessuno

**Risposta**

Il payload della risposta è strutturato nel modo seguente:

Codice di risposta: 200, 400, 401, 403, 404, 500

Esempio di payload di risposta:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Glossario**

Questa tabella definisce gli elementi chiave nella risposta:

| **Parametro** | **Descrizione** |
| --- | --- |
| `DatasetName` | Nome del set di dati definito da questo oggetto matrice |
| `SelectableColumns` | Colonne non elaborate che è possibile specificare nelle colonne SELECT |
| `AvailableMetrics` | Nomi delle colonne di aggregazione/metrica che è possibile specificare nelle colonne SELECT |
| `AvailableDateRanges` | Intervallo di date che è possibile utilizzare nelle query del report per il set di dati |
| `NextLink` | Collegamento alla pagina successiva se i dati sono impaginati |
| `TotalCount` | Numero di set di impostazioni nella matrice di valori |
| `StatusCode` | Codice risultato. I valori possibili sono 200, 400, 401, 403, 500 |
