---
title: Panoramica sul connettore di competenza
description: Questo articolo descrive i diversi archivi dati e le funzionalità supportate in ambito
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 88fb9c823df6ae5df345911ccce1c579009fba02
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555137"
---
# <a name="supported-data-stores"></a>Archivi dati supportati

La competenza supporta gli archivi dati seguenti. Fare clic su ogni archivio dati per ottenere informazioni sulle funzionalità supportate e sulle configurazioni corrispondenti.

## <a name="purview-data-sources"></a>Origini dati di competenza

|**Categoria**|  **Archivio dati**  |**Estrazione dei metadati**|**Analisi completa**|**Analisi incrementale**|**Analisi con ambito**|**Classificazione**|**Derivazione**|
|---|---|---|---|---|---|---|---|
| Azure | [Archiviazione BLOB di Azure](register-scan-azure-blob-storage-source.md)| Sì| Sì| Sì| Sì| Sì| Sì|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Sì| Sì| Sì| Sì| Sì| Sì|
||[Esplora dati di Azure](register-scan-azure-data-explorer.md)|Sì| Sì| Sì| Sì| Sì| Sì|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Sì| Sì| Sì| Sì| Sì| Sì|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Sì| Sì| Sì| Sì| Sì| Sì|
||[Database SQL di Azure](register-scan-azure-sql-database.md)|Sì| Sì| No| Sì| Sì| Sì|
||[Istanza gestita di database SQL di Azure](register-scan-azure-sql-database-managed-instance.md)|Sì| Sì| No| Sì| Sì| Sì|
||[Azure sinapsi Analytics (in precedenza SQL DW)](register-scan-azure-synapse-analytics.md)|Sì| Sì| No| Sì| Sì| Sì|
|Database|[SQL Server](register-scan-on-premises-sql-server.md)|Sì| Sì| No| Sì| Sì| Sì|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Sì| Sì| No| No| No| Sì|

## <a name="next-steps"></a>Passaggi successivi

- [Registrare e analizzare l'origine dell'archivio BLOB di Azure](register-scan-azure-blob-storage-source.md)