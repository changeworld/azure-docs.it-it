---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563191"
---
Nella tabella seguente sono riepilogate alcune delle differenze principali tra ML Studio (versione classica) e Azure Machine Learning.

| Feature | ML Studio (versione classica) | Azure Machine Learning |
|---| --- | --- |
| Interfaccia per il trascinamento della selezione | Esperienza classica | Esperienza aggiornata - [Finestra di progettazione di Azure Machine Learning](../articles/machine-learning/concept-designer.md)| 
| SDK di codice | Non supportato | Completamente integrato con gli SDK di [Azure Machine Learning per Python](/python/api/overview/azure/ml/) e [R](https://github.com/Azure/azureml-sdk-for-r) |
| Esperimento | Scalabile (limite dei dati di training di 10 GB) | Ridimensionamento con destinazione di calcolo |
| Destinazioni di calcolo del training | Destinazione di calcolo proprietaria, solo supporto di CPU | Ampia gamma di [destinazioni di calcolo per il training](../articles/machine-learning/concept-compute-target.md#train) personalizzabili. Include il supporto di GPU e CPU | 
| Destinazioni di calcolo della distribuzione | Formato di servizio Web proprietario, non personalizzabile | Ampia gamma di [destinazioni di calcolo per la distribuzione](../articles/machine-learning/concept-compute-target.md#deploy) personalizzabili. Include il supporto di GPU e CPU |
| Pipeline di Machine Learning | Non supportate | Creazione di [pipeline](../articles/machine-learning/concept-ml-pipelines.md) flessibili e modulari per automatizzare i flussi di lavoro |
| MLOps | Gestione e distribuzione dei modelli di base; distribuzioni solo CPU | Controllo delle versioni delle entità (modello, dati, flussi di lavoro), automazione dei flussi di lavoro, integrazione con strumenti CICD, distribuzioni CPU e GPU [e altro ancora](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Formato dei modelli | Formato proprietario, solo Studio (versione classica) | Più formati supportati a seconda del tipo di processo di training |
| Training automatizzato dei modelli e ottimizzazione degli iperparametri |  Non supportate | [Supportata](../articles/machine-learning/concept-automated-ml.md). Opzioni code-first e senza codice. | 
| Rilevamento deriva dei dati | Non supportate | [Supporto](../articles/machine-learning/how-to-monitor-datasets.md) |
| Progetti di etichettatura dei dati | Non supportato | [Supporto](../articles/machine-learning/how-to-create-labeling-projects.md) |
| Controllo degli accessi in base al ruolo | Solo ruolo Collaboratore e proprietario | [Definizione di ruolo flessibile e controllo RBAC](../articles/machine-learning/how-to-assign-roles.md) |
| Raccolta AI | Supportato ( [https://gallery.azure.ai/](https://gallery.azure.ai/) ) | Non supportato <br><br> Informazioni sui [notebook di esempio di Python SDK](https://github.com/Azure/MachineLearningNotebooks). |