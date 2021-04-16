---
title: Differenze rispetto ad Azure Data Factory
description: Informazioni sulle differenze tra le funzionalità di integrazione Azure Synapse Analytics dati e quelle di Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 144bdf5e94f753090dd73e5839b6c1fd25f11811
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567639"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integrazione dei dati in Azure Synapse Analytics rispetto Azure Data Factory

In Azure Synapse Analytics, le funzionalità di integrazione dei dati, ad esempio le pipeline di Synapse e i flussi di dati, si basano su quelle Azure Data Factory. Per altre informazioni, vedere [Che cos'è Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Funzionalità disponibili in AdF & Azure Synapse Analytics

Per la disponibilità delle funzionalità, vedere la tabella seguente:

| Categoria                 | Funzionalità    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Uso di SSIS e SSIS Integration Runtime | ✓ | ✗ |
|                          | Supporto per le aree Integration Runtime (flussi di dati) | ✓ | ✗ |
|                          | Integration Runtime condivisione | ✓<br><small>*Può essere condiviso tra data factory diverse* | ✗ |
|                          | Time-to-Live | ✓ | ✗ |
| **Attività delle pipeline** | Attività del pacchetto SSIS | ✓ | ✗ |
|                          | Supporto per l Power Query lavoro | ✓ | ✓ |
| **Raccolta modelli e Knowledge Center** | Modelli di soluzioni | ✓<br><small>*Azure Data Factory di modelli* | ✓<br><small>*Knowledge Center dell'area di lavoro di Synapse* |
| **Integrazione del repository GIT** | Integrazione GIT | ✓ | ✓ |
| **Monitoring**           | Monitoraggio dei processi Spark per Flusso di dati | ✗ | ✓<br><small>*Sfruttare i pool di Synapse Spark* |
|                          | Integrazione con Monitoraggio di Azure | ✓ | ✗ |
| **Derivazione** | Supporta la pubblicazione dei dati di derivazione della pipeline in Purview  | ✓ | ✗ |  

> [!Note]
> **Time to Live** è un'Azure Integration Runtime che consente  al cluster Spark di rimanere attivo per un periodo di tempo dopo un'esecuzione del flusso di dati.
>


## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare l'integrazione dei dati nell'area di lavoro synapse imparando a inserire dati in un account Azure Data Lake Storage [gen2.](data-integration-data-lake.md)
