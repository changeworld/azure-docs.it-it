---
title: 'ML Studio (versione classica): eseguire la migrazione a endpoint della pipeline con Azure Machine Learning utilizzo'
description: Integrare gli endpoint della pipeline con le applicazioni client in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fc7e222d400c2ded602e3d26d504896302fff014
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311788"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Utilizzare gli endpoint della pipeline dalle applicazioni client

In questo articolo si apprenderà come integrare applicazioni client con Azure Machine Learning endpoint. Per altre informazioni sulla scrittura del codice dell'applicazione, vedere usare [un endpoint Azure Machine Learning](../how-to-consume-web-service.md).

Questo articolo fa parte di studio (classico) per Azure Machine Learning serie di migrazione. Per altre informazioni sulla migrazione ad Azure Machine Learning, vedere [l'articolo Panoramica della migrazione](migrate-overview.md).

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un'area di lavoro di Azure Machine Learning. [Creare un'area di lavoro di Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Un endpoint Azure Machine Learning o un endpoint della [pipeline in tempo reale](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Utilizzare un endpoint in tempo reale 

Se il modello è stato distribuito come **endpoint in tempo reale**, è possibile trovare l'endpoint REST e il codice a consumo pregenerato in C#, Python e R:

1. Passare a Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Passare alla scheda **endpoint** .
1. Selezionare l'endpoint in tempo reale.
1. Selezionare **consume**.

> [!NOTE]
> È anche possibile trovare la specifica di spavalderia per l'endpoint nella scheda **Dettagli** . Usare la definizione di spavalderia per comprendere lo schema dell'endpoint. Per ulteriori informazioni sulla definizione di spavalderia, vedere la [documentazione ufficiale di spavalderia](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Utilizzo di un endpoint della pipeline

Esistono due modi per utilizzare un endpoint della pipeline:

- Chiamate API REST
- Integrazione con Azure Data Factory

### <a name="use-rest-api-calls"></a>Usare le chiamate API REST

Chiamare l'endpoint REST dall'applicazione client. È possibile usare la specifica di spavalderia per l'endpoint per comprenderne lo schema:

1. Passare a Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Passare alla scheda **endpoint** .
1. Selezionare **Pipeline endpoints** (Endpoint pipeline).
1. Selezionare l'endpoint della pipeline.
1. Nel riquadro **Panoramica Endpoint pipeline** selezionare il collegamento nella documentazione dell' **endpoint REST**.

### <a name="use-azure-data-factory"></a>Usare Azure Data Factory

È possibile chiamare la pipeline Azure Machine Learning come passaggio in una pipeline di Azure Data Factory. Per altre informazioni, vedere [eseguire pipeline Azure Machine Learning in Azure Data Factory](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come trovare lo schema e il codice di esempio per gli endpoint della pipeline. Per ulteriori informazioni sull'utilizzo di endpoint dall'applicazione client, vedere Utilizzo di [un endpoint Azure Machine Learning](../how-to-consume-web-service.md).

Vedere il resto degli articoli della serie di migrazione Azure Machine Learning: 
1. [Panoramica sulla migrazione](migrate-overview.md).
1. [Eseguire la migrazione del set di dati](migrate-register-dataset.md).
1. [Ricompilare una pipeline di training di studio (classica)](migrate-rebuild-experiment.md).
1. [Ricompilare un servizio Web di studio (classico)](migrate-rebuild-web-service.md).
1. **Integrare un servizio web Azure Machine Learning con le app client**.
1. [Migrare Execute R script](migrate-execute-r-script.md).