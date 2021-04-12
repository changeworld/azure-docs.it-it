---
title: Usare i parametri della pipeline nella finestra di progettazione per creare pipeline versatili
titleSuffix: Azure Machine Learning
description: Come utilizzare i parametri della pipeline in Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 30ae737a170c337fe6be51521aeb358cdcebd44b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107338"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>Usare i parametri della pipeline nella finestra di progettazione per creare pipeline versatili

Usare i parametri della pipeline per creare pipeline flessibili nella finestra di progettazione. I parametri della pipeline consentono di impostare dinamicamente i valori in fase di esecuzione per incapsulare la logica della pipeline e riutilizzare gli asset

I parametri della pipeline sono particolarmente utili per il reinvio di un'esecuzione della pipeline, la ripetizione del [training dei modelli](how-to-retrain-designer.md)o l' [esecuzione di stime batch](how-to-run-batch-predictions-designer.md).

In questo articolo si apprenderà come eseguire le operazioni seguenti:

> [!div class="checklist"]
> * Creare parametri della pipeline
> * Eliminare e gestire i parametri della pipeline
> * Esecuzione della pipeline di trigger durante la regolazione dei parametri della pipeline

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

* Per un'introduzione guidata alla finestra di progettazione, completare l' [esercitazione di progettazione](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Crea parametro pipeline

Sono disponibili tre modi per creare un parametro della pipeline nella finestra di progettazione:
- Creare un parametro della pipeline nel pannello impostazioni e associarlo a un modulo.
- Innalzare di livello un parametro del modulo a un parametro della pipeline.
- Innalzamento di livello di un set di dati a un parametro

> [!NOTE]
> I parametri della pipeline supportano solo i tipi di dati di base `int` , ad esempio, `float` e `string` .

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>Opzione 1: creare un parametro della pipeline nel pannello impostazioni

In questa sezione viene creato un parametro della pipeline nel pannello impostazioni.

In questo esempio viene creato un parametro della pipeline che definisce il modo in cui una pipeline inserisce i dati mancanti usando il modulo **Clean Missing data** .

1. Accanto al nome della bozza della pipeline selezionare l'icona a forma di **ingranaggio** per aprire il pannello **Impostazioni** .

1. Nella sezione **parametri pipeline** selezionare l' **+** icona.

1.  Immettere un nome per il parametro e un valore predefinito. 

    Ad esempio, immettere `replace-missing-value` come nome del parametro e `0` come valore predefinito.

   ![Screenshot che illustra come creare un parametro della pipeline](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


Dopo aver creato un parametro della pipeline, è necessario [collegarlo al parametro del modulo](#attach-module-parameter-to-pipeline-parameter) che si desidera impostare in modo dinamico.

### <a name="option-2-promote-a-module-parameter"></a>Opzione 2: alzare di livello un parametro del modulo

Il modo più semplice per creare un parametro della pipeline per un valore di modulo è promuovere un parametro del modulo. Usare la procedura seguente per innalzare di livello un parametro del modulo a un parametro della pipeline:

1. Selezionare il modulo a cui si vuole aggiungere un parametro della pipeline.
1. Nel riquadro dei dettagli del modulo, eseguire il passaggio del mouse sul parametro che si desidera specificare.
1. Selezionare i puntini di sospensione (**...**) visualizzati.
1. Selezionare **Add to pipeline parameter** (Aggiungi a parametro pipeline).

    ![Screenshot che illustra come innalzare di livello il parametro del modulo alla pipeline parametro1](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Immettere un nome di parametro e un valore predefinito.
1. Selezionare **Salva**

È ora possibile specificare nuovi valori per questo parametro quando si invia questa pipeline.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>Opzione 3: innalzare di livello un set di dati a un parametro della pipeline

Se si vuole inviare la pipeline con set di dati delle variabili, è necessario alzare di livello il set di dati a un parametro della pipeline:

1. Selezionare il set di dati che si desidera convertire in un parametro della pipeline.

1. Nel pannello dei dettagli del set di dati selezionare **Imposta come parametro della pipeline**.

   ![Screenshot che illustra come impostare il set di dati come parametro della pipeline](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

È ora possibile specificare un set di dati diverso usando il parametro pipeline alla successiva esecuzione della pipeline.

## <a name="attach-and-detach-module-parameter-to-pipeline-parameter"></a>Collegamento e scollegamento del parametro del modulo al parametro della pipeline 

In questa sezione verrà illustrato come connettere e scollegare il parametro module nel parametro pipeline.

### <a name="attach-module-parameter-to-pipeline-parameter"></a>Connetti parametro modulo al parametro della pipeline

È possibile alleghi gli stessi parametri di modulo dei moduli duplicati allo stesso parametro della pipeline se si vuole modificare il valore in una sola volta quando si attiva l'esecuzione della pipeline.

Nell'esempio seguente è stato duplicato il modulo **Clean Missing data** . Per ogni modulo **Clean Missing data** , aggiungere il **valore di sostituzione** al parametro della pipeline **replace-missing-value**:

1. Selezionare il modulo **Clean Missing Data** (Pulisci dati mancanti).

1. Nel riquadro dei dettagli del modulo, a destra dell'area di disegno, impostare la **modalità di pulizia** su "Custom Substitution value".

1. MouseOver il campo del **valore di sostituzione** .

1. Selezionare i puntini di sospensione (**...**) visualizzati.

1. Selezionare il parametro pipeline `replace-missing-value` .

   ![Screenshot che illustra come aggiungere un parametro della pipeline](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

Il campo del valore di **sostituzione** è stato collegato al parametro della pipeline. 


### <a name="detach-module-parameter-to-pipeline-parameter"></a>Scollega il parametro del modulo nel parametro della pipeline

Dopo aver collegato il **valore di sostituzione** al parametro della pipeline, non è possibile eseguire questa operazione.

È possibile scollegare il parametro del modulo nel parametro della pipeline facendo clic sui puntini di sospensione (**...**) accanto al parametro module, quindi selezionare **Disconnetti dal parametro della pipeline**.

 ![Screenshot che mostra la mancata azione dopo la connessione al parametro della pipeline](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>Aggiornare ed eliminare i parametri della pipeline

In questa sezione viene illustrato come aggiornare ed eliminare i parametri della pipeline.

### <a name="update-pipeline-parameters"></a>Aggiornare i parametri della pipeline

Usare la procedura seguente per aggiornare un parametro della pipeline del modulo:

1. Nella parte superiore dell'area di disegno selezionare l'icona a forma di ingranaggio.
1. Nella sezione **parametri pipeline** è possibile visualizzare e aggiornare il nome e il valore predefinito per tutti i parametri della pipeline.

### <a name="delete-a-dataset-pipeline-parameter"></a>Eliminare un parametro della pipeline del set di dati

Per eliminare un parametro della pipeline del set di dati, attenersi alla procedura seguente:

1. Selezionare il modulo del set di dati.
1. Deselezionare l'opzione **Imposta come parametro della pipeline**.


### <a name="delete-module-pipeline-parameters"></a>Eliminare i parametri della pipeline del modulo

Per eliminare un parametro della pipeline del modulo, attenersi alla procedura seguente:

1. Nella parte superiore dell'area di disegno selezionare l'icona a forma di ingranaggio.

1. Selezionare i puntini di sospensione (**..**.) accanto al parametro della pipeline.

    Questa visualizzazione Mostra i moduli a cui è associato il parametro della pipeline.

    ![Screenshot che mostra il parametro della pipeline corrente applicato a un modulo](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. Selezionare **Delete Parameter** per eliminare il parametro pipeline.

    > [!NOTE]
    > Se si elimina un parametro della pipeline, tutti i parametri del modulo collegati verranno scollegati e il valore dei parametri del modulo scollegato manterrà il valore del parametro della pipeline corrente.     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Attivare un'esecuzione della pipeline con i parametri della pipeline 

In questa sezione viene illustrato come inviare un'esecuzione di pipeline durante l'impostazione dei parametri della pipeline.

### <a name="resubmit-a-pipeline-run"></a>Inviare nuovamente un'esecuzione della pipeline

Dopo aver inviato una pipeline con i parametri della pipeline, è possibile inviare nuovamente un'esecuzione della pipeline con parametri diversi:

1. Vai alla pagina di dettaglio della pipeline. Nella finestra **Panoramica dell'esecuzione della pipeline** è possibile controllare i parametri e i valori della pipeline correnti.

1. Selezionare **Invia** di più.
1. Nell' **esecuzione della pipeline di installazione** specificare i nuovi parametri della pipeline. 

![Screenshot che mostra la pipeline di reinvio con i parametri della pipeline](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Usare le pipeline pubblicate

È anche possibile pubblicare una pipeline per usare i parametri della pipeline. Una **pipeline pubblicata** è una pipeline che è stata distribuita in una risorsa di calcolo, che le applicazioni client possono richiamare tramite un endpoint REST.

Gli endpoint pubblicati sono particolarmente utili per la ripetizione del training e scenari di stima in batch. Per ulteriori informazioni, vedere [come ripetere il training dei modelli nella finestra di progettazione](how-to-retrain-designer.md) o [eseguire stime batch nella finestra di progettazione](how-to-run-batch-predictions-designer.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare i parametri della pipeline nella finestra di progettazione. Vedere quindi come usare i parametri della pipeline per ripetere il [training dei modelli](how-to-retrain-designer.md) o eseguire [stime batch](how-to-run-batch-predictions-designer.md).

È anche possibile imparare a [usare le pipeline a livello di codice con l'SDK](how-to-deploy-pipelines.md).
