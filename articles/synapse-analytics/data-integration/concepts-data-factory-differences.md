---
title: Differenze rispetto ad Azure Data Factory
description: Informazioni sul modo in cui le funzionalità di integrazione dei dati di Azure sinapsi Analytics sono diverse da quelle di Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: a8fd0ef006b246e30c02cfb321c72b4e070f54de
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109148"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integrazione dei dati in Azure sinapsi Analytics rispetto a Azure Data Factory

In Azure sinapsi Analytics le funzionalità di integrazione dei dati, ad esempio i flussi di dati e le pipeline di sinapsi, sono basate su quelle di Azure Data Factory. Per ulteriori informazioni, vedere [che cos'è Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-azure-data-factory-and-azure-synapse-analytics"></a>Funzionalità disponibili in Azure Data Factory e Azure sinapsi Analytics

Per la disponibilità delle funzionalità, vedere la tabella seguente:

| Category                 | Funzionalità    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Uso di SSIS e SSIS Integration Runtime | ✓ | ✗ |
|                          | Supporto per Integration Runtime tra aree (flussi di dati) | ✓ | ✗ |
|                          | Condivisione Integration Runtime | ✓<br><small>*Può essere condiviso tra data factory diverse* | ✗ |
|                          | Durata (TTL) | ✓ | ✗ |
| **Attività pipeline** | Attività pacchetto SSIS | ✓ | ✗ |
|                          | Supporto per l'attività Power Query | ✓ | ✓ |
| **Raccolta di modelli e Knowledge Center** | Modelli di soluzioni | ✓<br><small>*Raccolta di modelli di Azure Data Factory* | ✓<br><small>*Centro informazioni area di lavoro sinapsi* |
| **Integrazione del repository GIT** | Integrazione con GIT | ✓ | ✓ |
| **Monitoring**           | Monitoraggio dei processi Spark per il flusso di dati | ✗ | ✓<br><small>*Sfruttare i pool di Spark sinapsi* |
|                          | Integrazione con monitoraggio di Azure | ✓ | ✗ |

> [!Note]
> Il **tempo di** esecuzione è un'impostazione Azure Integration Runtime che consente al cluster Spark di *rimanere* attivo per un periodo di tempo dopo un'esecuzione del flusso di dati.
>


## <a name="next-steps"></a>Passaggi successivi

Introduzione all'integrazione dei dati nell'area di lavoro sinapsi imparando a [inserire i dati in un account di Azure Data Lake storage Gen2](data-integration-data-lake.md).
