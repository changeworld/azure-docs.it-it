---
title: Installare Visual Studio 2019
description: Installare Visual Studio e SQL Server Data Tools (SSDT) per Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.openlocfilehash: 6891176ff648e0f07b0f4921c3558df51f55388e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279749"
---
# <a name="getting-started-with-visual-studio-2019"></a>Introduzione a Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) è un singolo strumento che consente di eseguire le operazioni seguenti:

- Connettere, eseguire query e sviluppare applicazioni
- Usare Esplora oggetti per esplorare visivamente tutti gli oggetti del modello di dati, tra cui tabelle, visualizzazioni, stored procedure e così via.
- Generare script T-SQL DDL (Data Definition Language) per gli oggetti
- Sviluppare il data warehouse usando un approccio basato sullo stato con i progetti di database SSDT
- Integrare il progetto di database con sistemi di controllo del codice sorgente come Git con Azure Repos
- Configurare pipeline di integrazione continua e distribuzione continua con server di automazione come Azure DevOps

## <a name="install-visual-studio-2019"></a>Installare Visual Studio 2019

Vedere [Scaricare Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) per scaricare e installare Visual Studio **16.3 e versioni successive**. Durante l'installazione, selezionare il carico di lavoro di archiviazione ed elaborazione dei dati. L'installazione autonoma di SSDT non è più necessaria in Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Funzionalità non supportate in SSDT

In alcuni casi le versioni delle funzionalità per Synapse SQL possono non includere il supporto per SSDT. Le funzionalità seguenti non sono attualmente supportate:


- [Gestione dei carichi di lavoro](sql-data-warehouse-workload-management.md): gruppi e classificatori di carichi di lavoro
- [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (incluse le funzioni con valori di tabella)
  - Inviare un [ticket di supporto o votare ](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)per ottenere la funzionalità supportata.
  - Inviare un [ticket di supporto o votare ](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)per ottenere la funzionalità supportata.
- Alcune funzionalità T-SQL, ad esempio:
   - Clausola *within Group* nella funzione [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) String.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver installato la versione più recente di SSDT, è possibile [connettersi](sql-data-warehouse-query-visual-studio.md) al pool SQL.