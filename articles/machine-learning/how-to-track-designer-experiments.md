---
title: Metriche dei log nella finestra di progettazione
titleSuffix: Azure Machine Learning
description: Monitorare gli esperimenti della finestra di progettazione di Azure ML. Abilitare la registrazione usando il modulo Execute Python Script (Esegui script Python) e visualizzare i risultati registrati in studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: 13a3b86514428b0219aaf671260c07b4e197d2de
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817306"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Abilitare la registrazione nelle Azure Machine Learning della finestra di progettazione


Questo articolo illustra come aggiungere codice di registrazione alle pipeline di progettazione. Si apprenderà anche come visualizzare tali log usando il portale Studio di Azure Machine Learning Web.

Per altre informazioni sulla registrazione delle metriche usando l'esperienza di creazione dell'SDK, vedere Monitorare le esecuzioni e [le metriche dell'esperimento di Azure ML.](how-to-log-view-metrics.md)

## <a name="enable-logging-with-execute-python-script"></a>Abilitare la registrazione con Esegui script Python

Usare il [modulo Esegui script Python](./algorithm-module-reference/execute-python-script.md) per abilitare la registrazione nelle pipeline di progettazione. Anche se è possibile registrare qualsiasi valore con questo flusso di lavoro, è particolarmente utile registrare le metriche dal modulo __Evaluate Model__ (Valuta modello) per tenere traccia delle prestazioni del modello tra le esecuzioni.

L'esempio seguente illustra come registrare l'errore quadratino medio di due modelli con training usando i moduli Evaluate Model ed Execute Python Script .

1. Connettere un __modulo Execute Python Script (Esegui script Python)__ all'output del modulo Evaluate Model __(Valuta__ modello).

    ![Connettere un modulo Execute Python Script al modulo Evaluate Model](./media/how-to-log-view-metrics/designer-logging-pipeline.png)

1. Incollare il codice seguente nell'editor di codice Execute Python Script (Esegui script __Python)__ per registrare l'errore assoluto medio per il modello con training. È possibile usare un modello simile per registrare qualsiasi altro valore nella finestra di progettazione:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score Module to the` left port of Evaluate Model module.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
Questo codice usa il Azure Machine Learning Python SDK per registrare i valori. Usa Run.get_context() per ottenere il contesto dell'esecuzione corrente. Registra quindi i valori in tale contesto con il metodo run.parent.log(). Usa per `parent` registrare i valori nell'esecuzione della pipeline padre anziché nell'esecuzione del modulo.

Per altre informazioni su come usare Python SDK per registrare i valori, vedere Abilitare la registrazione nelle esecuzioni [di training di Azure ML.](how-to-log-view-metrics.md)

## <a name="view-logs"></a>Visualizzare i log

Al termine dell'esecuzione della pipeline, è possibile visualizzare il Mean_Absolute_Error *nella* pagina Esperimenti.

1. Passare alla **sezione Experiments (Esperimenti).**
1. Selezionare l'esperimento.
1. Selezionare l'esecuzione nell'esperimento che si vuole visualizzare.
1. Selezionare **Metriche**.

    ![Visualizzare le metriche di esecuzione in Studio](./media/how-to-log-view-metrics/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare i log nella finestra di progettazione. Per i passaggi successivi, vedere gli articoli correlati seguenti:


* Per informazioni su come risolvere i problemi relativi alle pipeline della finestra di progettazione, vedere [Eseguire il debug & risolvere i problemi delle pipeline di Machine Learning.](how-to-debug-pipelines.md#azure-machine-learning-designer)
* Per informazioni su come usare Python SDK per registrare le metriche nell'esperienza di creazione dell'SDK, vedere Abilitare la registrazione nelle esecuzioni di [training di Azure ML.](how-to-log-view-metrics.md)
* Informazioni su come usare [Esegui script Python](./algorithm-module-reference/execute-python-script.md) nella finestra di progettazione.
