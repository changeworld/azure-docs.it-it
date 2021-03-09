---
title: includere file
description: File di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: 081eea7842652884363603bc5ba742ba48539f85
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503616"
---
**Le destinazioni di calcolo possono essere riutilizzate per più processi di training**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi. Per le pipeline di Machine Learning, usare il [passaggio della pipeline](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) appropriato per ogni destinazione di calcolo.

Per la maggior parte dei processi, è possibile usare qualsiasi risorsa seguente come destinazione di calcolo. Non tutte le risorse possono essere usate per Machine Learning automatizzato, pipeline di Machine Learning o progettazione.

|Destinazioni di&nbsp;training|[Machine Learning automatizzato](../articles/machine-learning/concept-automated-ml.md) | [Pipeline di apprendimento automatico](../articles/machine-learning/concept-ml-pipelines.md) | [Finestra di progettazione di Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Sì | &nbsp; | &nbsp; |
|[Cluster di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Sì | Sì | Sì |
|[Istanza di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Sì (tramite SDK)  | Sì |  |
|[Macchina virtuale remota](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Sì  | Sì | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Sì (solo modalità locale SDK) | Sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Sì | &nbsp; |
