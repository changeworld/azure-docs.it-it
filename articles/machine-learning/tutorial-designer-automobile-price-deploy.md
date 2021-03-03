---
title: 'Esercitazione: Distribuire modelli di ML con la finestra di progettazione'
titleSuffix: Azure Machine Learning
description: Creare una soluzione di analisi predittiva nella finestra di progettazione di Azure Machine Learning. Eseguire il training, assegnare punteggi e distribuire un modello di Machine Learning usando moduli con trascinamento della selezione.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 01/15/2021
ms.custom: designer
ms.openlocfilehash: ec563371ab505113117707f56c31f506f7fdf377
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659526"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Esercitazione: Distribuire un modello di Machine Learning con la finestra di progettazione


È possibile distribuire il modello predittivo sviluppato nella [prima parte dell'esercitazione](tutorial-designer-automobile-price-train-score.md) per offrire ad altri la possibilità di usarlo. Nella prima parte è stato eseguito il training del modello. A questo punto è possibile generare stime basate sull'input dell'utente. In questa parte dell'esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una pipeline di inferenza in tempo reale.
> * Creare un cluster di inferenza.
> * Distribuire l'endpoint in tempo reale.
> * Testare l'endpoint in tempo reale.

## <a name="prerequisites"></a>Prerequisiti

Completare [la prima parte dell'esercitazione](tutorial-designer-automobile-price-train-score.md) per apprendere come eseguire il training e assegnare un punteggio a un modello di Machine Learning nella finestra di progettazione.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Creare una pipeline di inferenza in tempo reale

Per distribuire la pipeline, è necessario prima convertire la pipeline di training in una pipeline di inferenza in tempo reale. Questo processo rimuove i moduli di training e aggiunge input e output del servizio Web per gestire le richieste.

### <a name="create-a-real-time-inference-pipeline"></a>Creare una pipeline di inferenza in tempo reale

1. Sopra il canvas della pipeline selezionare **Create inference pipeline** > **Real-time inference pipeline** (Crea pipeline di inferenza > Pipeline di inferenza in tempo reale).

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png" alt-text="Screenshot che mostra dove trovare il pulsante per creare la pipeline":::

    La pipeline dovrebbe ora avere un aspetto simile al seguente: 

   ![Screenshot della configurazione prevista della pipeline dopo la preparazione per la distribuzione](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Quando si seleziona **Create inference pipeline** accadono diverse cose:
    
    * Il modello sottoposto a training viene archiviato come modulo **Dataset** (Set di dati) nel riquadro dei moduli. È possibile trovarlo in **My Datasets** (Set di dati personali).
    * I moduli di training, come **Train Model** (Training modello) e **Split Data** (Divisione dati), vengono rimossi.
    * Il modello con training salvato viene aggiunto nuovamente alla pipeline.
    * Vengono aggiunti i moduli **Web Service Input** (Input servizio Web) e **Web Service Output** (Output servizio Web). Questi moduli mostrano il punto in cui i dati dell'utente vengono immessi nella pipeline e il punto in cui vengono restituiti.

    > [!NOTE]
    > Per impostazione predefinita, **Input servizio Web** prevede lo stesso schema di dati dei dati di training usati per creare la pipeline predittiva. In questo scenario il prezzo è incluso nello schema. Tuttavia, non viene usato come fattore durante la previsione.
    >

1. Selezionare **Submit** (Invia) e usare la stessa destinazione di calcolo e lo stesso esperimento usati nella prima parte.

    La prima volta, l'esecuzione della pipeline potrebbe impiegare fino a 20 minuti. Le impostazioni di calcolo predefinite prevedono una dimensione minima del nodo pari a 0, il che significa che la finestra di progettazione deve allocare risorse dopo l'inattività. Le esecuzioni ripetute della pipeline richiederanno meno tempo, perché le risorse di calcolo sono già allocate. Inoltre, la finestra di progettazione usa i risultati memorizzati nella cache per ogni modulo per migliorare ulteriormente l'efficienza.

1. Selezionare **Distribuisci**.

## <a name="create-an-inferencing-cluster"></a>Creare un cluster di inferenza

Nella finestra di dialogo visualizzata è possibile selezionare uno dei cluster del servizio Azure Kubernetes esistenti in cui distribuire il modello. Se non si ha un cluster del servizio Azure Kubernetes, seguire questa procedura per crearne uno.

1. Selezionare **Compute** (Calcolo) nella finestra di dialogo visualizzata per passare alla pagina **Compute**.

1. Sulla barra multifunzione di spostamento selezionare **Inference Clusters** >  **+ New** (Cluster di inferenza > Nuovo).

    ![Screenshot che mostra come passare al riquadro del nuovo cluster di inferenza](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. Nel riquadro del cluster di inferenza configurare un nuovo servizio Kubernetes.

1. Immettere *aks-compute* in **Compute name** (Nome del calcolo).
    
1. Selezionare un'area vicina disponibile per **Region** (Area).

1. Selezionare **Crea**.

    > [!NOTE]
    > La creazione di un nuovo servizio Azure Kubernetes richiede circa 15 minuti. È possibile controllare lo stato del provisioning nella pagina **Inference Clusters** (Cluster di inferenza)
    >

## <a name="deploy-the-real-time-endpoint"></a>Distribuire l'endpoint in tempo reale

Al termine del provisioning del servizio Azure Kubernetes, tornare alla pipeline di inferenza in tempo reale per completare la distribuzione.

1. Selezionare **Deploy** (Distribuisci) sopra il canvas.

1. Selezionare **Deploy new real-time endpoint** (Distribuisci nuovo endpoint in tempo reale). 

1. Selezionare il cluster del servizio Azure Kubernetes creato.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png" alt-text="Screenshot che mostra come configurare un nuovo endpoint in tempo reale":::

    È anche possibile modificare l'impostazione **avanzata** per l'endpoint in tempo reale.
    
    |Impostazione avanzata|Descrizione|
    |---|---|
    |Abilitare la diagnostica Application Insights e la raccolta dati| Indica se abilitare applicazione Azure Insights per la raccolta di dati dagli endpoint distribuiti. </br> Per impostazione predefinita: false |
    |Timeout Punteggio| Timeout in millisecondi da applicare per la valutazione delle chiamate al servizio Web.</br>Per impostazione predefinita: 60000|
    |Scalabilità automatica abilitata|   Indica se abilitare o meno la scalabilità automatica per il servizio Web.</br>Per impostazione predefinita: true|
    |Repliche minime| Il numero minimo di contenitori da usare per la scalabilità automatica di questo servizio Web.</br>Per impostazione predefinita: 1|
    |Numero massimo di repliche| Il numero massimo di contenitori da usare per la scalabilità automatica di questo servizio Web.</br> Per impostazione predefinita: 10|
    |Utilizzo di destinazione|L'utilizzo di destinazione (in percentuale su 100) che è necessario provare a mantenere per la scalabilità automatica di questo servizio Web.</br> Per impostazione predefinita: 70|
    |Periodo di aggiornamento|Frequenza (in secondi) con cui il ridimensionamento automatico tenta di ridimensionare questo servizio Web.</br> Per impostazione predefinita: 1|
    |Capacità di riserva della CPU|Il numero di core di CPU da allocare per questo servizio Web.</br> Per impostazione predefinita: 0,1|
    |Capacità di riserva memoria|La quantità di memoria (in GB) da allocare per questo servizio Web.</br> Per impostazione predefinita: 0,5|
        

1. Selezionare **Distribuisci**. 

    Al termine della distribuzione verrà visualizzata una notifica di esito positivo sopra il canvas. L'operazione potrebbe richiedere qualche minuto.

> [!TIP]
> È anche possibile eseguire la distribuzione in un' **istanza di contenitore di Azure** (ACI) se si seleziona istanza di contenitore di **Azure** per **tipo di calcolo** nella casella Impostazioni endpoint in tempo reale.
> L'istanza di contenitore di Azure viene usata per test o sviluppo. Usare ACI per carichi di lavoro basati su CPU su scala ridotta che richiedono meno di 48 GB di RAM.

## <a name="test-the-real-time-endpoint"></a>Testare l'endpoint in tempo reale

Una volta completata la distribuzione, è possibile visualizzare l'endpoint in tempo reale passando alla pagina **Endpoints**.

1. Nella pagina **Endpoints** selezionare l'endpoint appena distribuito.

    Nella scheda **Dettagli** è possibile visualizzare altre informazioni, ad esempio l'URI Rest, la definizione di spavalderia, lo stato e i tag.

    Nella scheda **utilizzo** è possibile trovare codice consumo di esempio, chiavi di sicurezza e impostare metodi di autenticazione.

    Nella scheda **Log di distribuzione** sono disponibili i log di distribuzione dettagliati dell'endpoint in tempo reale.

1. Per testare l'endpoint, passare alla scheda **test** . Da qui è possibile immettere i dati di test e selezionare **test** Verify the output of the endpoint.

Per altre informazioni sull'utilizzo del servizio Web, vedere [Usare un modello di Azure Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)

## <a name="limitations"></a>Limitazioni

Se si apportano modifiche alla pipeline di training, è necessario inviare di nuovo la pipeline di training, **aggiornare** la pipeline di inferenza ed eseguire nuovamente la pipeline di inferenza.

Si noti che solo i modelli sottoposti a training verranno aggiornati nella pipeline di inferenza, mentre la trasformazione dei dati non verrà aggiornata.

Per usare la trasformazione aggiornata nella pipeline di inferenza, è necessario registrare l'output della trasformazione del modulo di trasformazione come DataSet.

![Screenshot che illustra come registrare un set di dati di trasformazione](./media/tutorial-designer-automobile-price-deploy/register-transformation-dataset.png)

Sostituire quindi manualmente il modulo **TD-** Module nella pipeline di inferenza con il set di dati registrato.

![Screenshot che illustra come sostituire il modulo di trasformazione](./media/tutorial-designer-automobile-price-deploy/replace-td-module.png)

È quindi possibile inviare la pipeline di inferenza con il modello e la trasformazione aggiornati e distribuire.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati i principali passaggi per creare, distribuire e usare un modello di Machine Learning nella finestra di progettazione. Per informazioni su come usare la finestra di progettazione, vedere i collegamenti seguenti:

+ [Esempi della finestra di progettazione](samples-designer.md): informazioni su come usare la finestra di progettazione per risolvere altri tipi di problemi.
+ [Usare Azure Machine Learning in una rete virtuale di Azure](how-to-enable-studio-virtual-network.md).
