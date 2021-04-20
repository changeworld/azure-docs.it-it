---
author: baanders
description: file di inclusione per Gemelli digitali di Azure limiti
ms.service: digital-twins
ms.topic: include
ms.date: 4/8/2021
ms.author: baanders
ms.openlocfilehash: 34fec713c3764987f07bc7fb89ecb0a0d770a840
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728024"
---
### <a name="functional-limits"></a>Limiti funzionali

Nella tabella seguente sono elencati i limiti funzionali di Gemelli digitali di Azure. 

> [!TIP]
> Per indicazioni sulla modellazione per operare entro questi limiti funzionali, vedere [Procedure consigliate per la progettazione di modelli](../articles/digital-twins/concepts-models.md#best-practices-for-designing-models).

| Ad aree | Funzionalità | Limite predefinito | Regolabile? |
| --- | --- | --- | --- |
| Risorsa di Azure | Numero di istanze Gemelli digitali di Azure in un'area, per sottoscrizione | 10 | Sì |
| Gemelli digitali | Numero di gemelli in un'istanza Gemelli digitali di Azure | 200.000 | Sì |
| Gemelli digitali | Numero di relazioni in ingresso con un singolo gemello | 5\.000 | No |
| Gemelli digitali | Numero di relazioni in uscita da un singolo gemello | 5\.000 | No |
| Gemelli digitali | Dimensioni massime (del corpo JSON in una richiesta PUT o PATCH) di un singolo gemello | 32 KB | No |
| Gemelli digitali | Dimensioni massime del payload della richiesta | 32 KB | No | 
| Routing | Numero di endpoint per una singola Gemelli digitali di Azure istanza | 6 | No |
| Routing | Numero di route per una singola Gemelli digitali di Azure istanza | 6 | Sì |
| Modelli | Numero di modelli all'interno di una singola Gemelli digitali di Azure istanza | 10,000 | Sì |
| Modelli | Numero di modelli che possono essere caricati in una singola chiamata API | 250 | No |
| Modelli | Dimensioni massime (del corpo JSON in una richiesta PUT o PATCH) di un singolo modello | 1 MB | No |
| Modelli | Numero di elementi restituiti in una singola pagina | 100 | No |
| Query | Numero di elementi restituiti in una singola pagina | 100 | Sì |
| Query | Numero di `AND`  /  `OR` espressioni in una query | 50 | Sì |
| Query | Numero di elementi di matrice in una `IN`  /  `NOT IN` clausola | 50 | Sì |
| Query | Numero di caratteri in una query | 8\.000 | Sì |
| Query | Numero di `JOINS` in una query | 5 | Sì |

### <a name="rate-limits"></a>Limiti di velocità

La tabella seguente riflette i limiti di frequenza delle diverse API.

| API | Funzionalità | Limite predefinito | Regolabile? |
| --- | --- | --- | --- |
| Models API | Numero di richieste al secondo | 100 | Sì |
| API Gemelli digitali | Numero di richieste di lettura al secondo | 1\.000 | Sì |
| API Gemelli digitali | Numero di richieste di patch al secondo | 1\.000 | Sì |
| API Gemelli digitali | Numero di operazioni di creazione/eliminazione al secondo in **tutti i gemelli e le relazioni** | 50 | Sì |
| API Gemelli digitali | Numero di operazioni di creazione, aggiornamento/eliminazione al secondo in un **singolo gemello** o nelle relative relazioni | 10 | No |
| API di query | Numero di richieste al secondo | 500 | Sì |
| API di query | [Unità di query](../articles/digital-twins/concepts-query-units.md) al secondo | 4.000 | Sì |
| API Route di eventi | Numero di richieste al secondo | 100 | Sì |

### <a name="other-limits"></a>Altri limiti

I limiti relativi ai tipi di dati e ai campi all'interno di documenti DTDL per i modelli Gemelli digitali di Azure sono disponibili nella relativa documentazione specifica in GitHub: [*DTDL (Digital Twins Definition Language) - versione 2.*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)
 
I dettagli sulla latenza delle query e altre limitazioni delle query sono disponibili in [*Procedura: Eseguire query sul grafo gemello.*](../articles/digital-twins/how-to-query-graph.md)
