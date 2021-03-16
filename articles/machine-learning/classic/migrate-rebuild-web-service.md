---
title: 'ML Studio (classico): eseguire la migrazione al servizio Web di ricompilazione Azure Machine Learning'
description: Ricompilare i servizi Web di studio (classico) come endpoint della pipeline in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565249"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Ricompilare un servizio Web di studio (classico) in Azure Machine Learning

Questo articolo illustra come ricompilare un servizio Web di studio (classico) come **endpoint** in Azure Machine Learning.

Usare Azure Machine Learning endpoint della pipeline per eseguire stime, ripetere il training dei modelli o eseguire qualsiasi pipeline generica. L'endpoint REST consente di eseguire pipeline da qualsiasi piattaforma. 

Questo articolo fa parte di studio (classico) per Azure Machine Learning serie di migrazione. Per altre informazioni sulla migrazione ad Azure Machine Learning, vedere l' [articolo Panoramica della migrazione](migrate-overview.md).

> [!NOTE]
> Questa serie di migrazione è incentrata sulla finestra di progettazione con trascinamento della selezione. Per altre informazioni sulla distribuzione di modelli a livello di codice, vedere [distribuire modelli di Machine Learning in Azure](../how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un'area di lavoro di Azure Machine Learning. [Creare un'area di lavoro di Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Una pipeline di training Azure Machine Learning. Per altre informazioni, vedere [ricompilare un esperimento di studio (classico) in Azure Machine Learning](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Endpoint della pipeline di confronto tra endpoint in tempo reale

I servizi Web di studio (classico) sono stati sostituiti da **endpoint** in Azure Machine Learning. Usare la tabella seguente per scegliere il tipo di endpoint da usare:

|Servizio Web Studio (classico)| Sostituzione Azure Machine Learning
|---|---|
|Servizio Web Request/Rispondi (stima in tempo reale)|Endpoint in tempo reale|
|Servizio Web batch (stima batch)|Endpoint della pipeline|
|Ripetizione del training del servizio Web (ripetizione del training)|Endpoint della pipeline| 


## <a name="deploy-a-real-time-endpoint"></a>Distribuire un endpoint in tempo reale

In studio (classico) è stato usato un **servizio Web Request/Rispondi** per distribuire un modello per le stime in tempo reale. In Azure Machine Learning si usa un **endpoint in tempo reale**.

Esistono diversi modi per distribuire un modello in Azure Machine Learning. Uno dei modi più semplici consiste nell'usare la finestra di progettazione per automatizzare il processo di distribuzione. Per distribuire un modello come endpoint in tempo reale, attenersi alla procedura seguente:

1. Eseguire la pipeline di training completata almeno una volta.
1. Al termine dell'esecuzione, nella parte superiore dell'area di disegno selezionare Crea pipeline di **inferenza pipeline** in  >  **tempo reale**.

    ![crea pipeline inferenza in tempo reale](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    La pipeline di training viene convertita dalla finestra di progettazione in una pipeline di inferenza in tempo reale. Una conversione simile si verifica anche in studio (classico).

    Nella finestra di progettazione, il passaggio [di conversione registra anche il modello sottoposto a training nell'area di lavoro Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Selezionare **Submit (Invia** ) per eseguire la pipeline di inferenza in tempo reale e verificare che venga eseguita correttamente.

1. Dopo aver verificato la pipeline di inferenza, selezionare **Distribuisci**.

1. Immettere un nome per l'endpoint e un tipo di calcolo.

    Nella tabella seguente vengono descritte le opzioni di calcolo della distribuzione nella finestra di progettazione:

    | Destinazione del calcolo | Utilizzo | Descrizione | Creazione |
    | ----- |  ----- | ----- | -----  |
    |[Servizio Azure Kubernetes](../how-to-deploy-azure-kubernetes-service.md) |Inferenza in tempo reale|Distribuzioni di produzione su larga scala. Tempi di risposta rapidi e scalabilità automatica del servizio.| Creato dall'utente. Per altre informazioni, vedere [creare destinazioni di calcolo](../how-to-create-attach-compute-studio.md#inference-clusters). |
    |[Istanze di contenitore di Azure ](../how-to-deploy-azure-container-instance.md)|Test o sviluppo | Carichi di lavoro su scala ridotta basati su CPU che richiedono meno di 48 GB di RAM.| Creato automaticamente da Azure Machine Learning.

### <a name="test-the-real-time-endpoint"></a>Testare l'endpoint in tempo reale

Al termine della distribuzione, è possibile visualizzare altri dettagli e testare l'endpoint:

1. Passare alla scheda **endpoint** .
1. Selezionare l'endpoint.
1. Selezionare la scheda **Test**.
    
    ![Screenshot che mostra la scheda endpoint con il pulsante test endpoint](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Pubblicare un endpoint della pipeline per la stima o la ripetizione del training in batch

È anche possibile usare la pipeline di training per creare un **endpoint della pipeline** anziché un endpoint in tempo reale. Usare gli **endpoint della pipeline** per eseguire la stima in batch o la ripetizione del training.

Gli endpoint della pipeline sostituiscono gli **endpoint di esecuzione batch**  di studio (classico) e i **servizi Web** di ripetizione del training.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Pubblicare un endpoint della pipeline per la stima in batch

La pubblicazione di un endpoint di stima batch è simile all'endpoint in tempo reale.

Per pubblicare un endpoint della pipeline per la stima batch, attenersi alla procedura seguente:

1. Eseguire la pipeline di training completata almeno una volta.

1. Al termine dell'esecuzione, nella parte superiore dell'area di disegno selezionare Crea pipeline di inferenza batch per la **pipeline**  >  .

    ![Screenshot che illustra il pulsante crea pipeline di inferenza in una pipeline di training](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    La pipeline di training viene convertita dalla finestra di progettazione in una pipeline di inferenza batch. Una conversione simile si verifica anche in studio (classico).

    Nella finestra di progettazione, questo passaggio [registra anche il modello sottoposto a training nell'area di lavoro Azure Machine Learning](../how-to-deploy-and-where.md#registermodel).

1. Selezionare **Submit (Invia** ) per eseguire la pipeline di inferenza batch e verificare che venga completata correttamente.

1. Dopo aver verificato la pipeline di inferenza, selezionare **pubblica**.
 
1. Creare un nuovo endpoint della pipeline o selezionarne uno esistente.
    
    Un nuovo endpoint della pipeline crea un nuovo endpoint REST per la pipeline. 

    Se si seleziona un endpoint della pipeline esistente, non sovrascrivere la pipeline esistente. Al contrario, Azure Machine Learning versioni di ogni pipeline nell'endpoint. È possibile specificare la versione da eseguire nella chiamata REST. È anche necessario impostare una pipeline predefinita se la chiamata REST non specifica una versione.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Pubblicare un endpoint della pipeline per la ripetizione del training

Per pubblicare un endpoint della pipeline per la ripetizione del training, è necessario disporre già di una bozza della pipeline che esegue il training di un modello. Per altre informazioni sulla creazione di una pipeline di training, vedere [ricompilare un esperimento di studio (classico)](migrate-rebuild-experiment.md).

Per riutilizzare l'endpoint della pipeline per la ripetizione del training, è necessario creare un **parametro della pipeline** per il set di dati di input. Ciò consente di impostare dinamicamente il set di dati di training, in modo da poter ripetere il training del modello.

Per pubblicare l'endpoint della pipeline di ripetizione del training, attenersi alla procedura seguente:

1. Eseguire la pipeline di training almeno una volta.
1. Al termine dell'esecuzione, selezionare il modulo del set di dati.
1. Nel riquadro Dettagli modulo selezionare **Imposta come parametro pipeline**.
1. Fornire un nome descrittivo come "InputDataset".    

    ![Screenshot che evidenzia come creare un parametro della pipeline](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Viene creato un parametro della pipeline per il set di dati di input. Quando si chiama l'endpoint della pipeline per il training, è possibile specificare un nuovo set di dati per ripetere il training del modello.

1. Selezionare **Pubblica**.

    ![Screenshot che evidenzia il pulsante pubblica in una pipeline di training](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Chiamare l'endpoint della pipeline da studio

Dopo aver creato l'inferenza batch o l'endpoint della pipeline di ripetizione del training, è possibile chiamare l'endpoint direttamente dal browser.

1. Passare alla scheda **pipeline** e selezionare **endpoint della pipeline**.
1. Selezionare l'endpoint della pipeline che si desidera eseguire.
1. Selezionare **Submit** (Invia).

    Dopo aver selezionato **Invia**, è possibile specificare qualsiasi parametro della pipeline.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come ricompilare un servizio Web di studio (classico) in Azure Machine Learning. Il passaggio successivo consiste nell' [integrare il servizio Web con le app client](migrate-rebuild-integrate-with-client-app.md).


Vedere gli altri articoli della serie di migrazione Studio (classica):

1. [Panoramica sulla migrazione](migrate-overview.md).
1. [Eseguire la migrazione del set di dati](migrate-register-dataset.md).
1. [Ricompilare una pipeline di training di studio (classica)](migrate-rebuild-experiment.md).
1. **Ricompilare un servizio Web di studio (classico)**.
1. [Integrare un servizio web Azure Machine Learning con le app client](migrate-rebuild-integrate-with-client-app.md).
1. [Migrare Execute R script](migrate-execute-r-script.md).
