---
title: Risorse per lo sviluppo Synapse SQL funzionalità
description: Concetti di sviluppo, decisioni di progettazione, raccomandazioni e tecniche di codifica per Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4d842414d3046692c982ca3203957a96f8a01b37
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377331"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Decisioni di progettazione e tecniche di codifica per Synapse SQL funzionalità in Azure Synapse Analytics
Questo articolo contiene un elenco di risorse per le funzioni dedicate del pool SQL e del pool SQL serverless di Synapse SQL. Gli articoli consigliati sono suddivisi in due sezioni: decisioni di progettazione chiave e tecniche di sviluppo e scrittura del codice.

L'obiettivo di questi articoli è aiutare a sviluppare l'approccio tecnico ottimale per i componenti Synapse SQL all'interno Azure Synapse Analytics.

## <a name="key-design-decisions"></a>Decisioni chiave nella progettazione
Gli articoli seguenti evidenziano i concetti e le decisioni di progettazione per Synapse SQL sviluppo:

| Articolo | pool SQL dedicato | Pool SQL serverless |
| ------- | -------- | ------------- |
| [Connessioni](connect-overview.md)                    | Sì | Sì |
| [Classi di risorse e concorrenza](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Sì    | No |
| [Transazioni](develop-transactions.md)              | Sì | No |
| [Schemi definiti dall'utente](develop-user-defined-schemas.md) | Sì | Sì |
| [Distribuzione di tabelle in SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Sì | No |
| [Indicizzazione di tabelle](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Sì | No |
| [Partizioni di tabella](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Sì | No |
| [Statistiche](develop-tables-statistics.md)            | Sì | Sì |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Sì | No |
| [Tabelle esterne](develop-tables-external-tables.md) | Sì | Sì |
| [CETAS](develop-tables-cetas.md)                     | Sì | Sì |


## <a name="recommendations"></a>Consigli

Di seguito sono riportati articoli essenziali che evidenziano tecniche di codifica specifiche, suggerimenti e consigli per lo sviluppo:

| Articolo | pool SQL dedicato | Pool SQL serverless |
| ------- | -------- | ------------- |
| [Stored procedure](develop-stored-procedures.md)  | Sì                | Sì                      |
| [Etichette](develop-label.md)                           | Sì                | No                      |
| [Visualizzazioni](develop-views.md)                             | Sì                | Sì                     |
| [Tabelle temporanee](develop-tables-temporary.md)       | Sì                | Sì                     |
| [SQL dinamica](develop-dynamic-sql.md)                 | Sì                | Sì                     |
| [Cicli](develop-loops.md)                         | Sì                | Sì                     |
| [Opzioni di raggruppamento](develop-group-by-options.md)       | Sì                | No                      |
| [Assegnazione di variabili](develop-variable-assignment.md) | Sì                | Sì                     |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni di riferimento, vedere [Istruzioni T-SQL del pool SQL.](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

