---
title: 'Train Model: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo **Train Model** in Azure Machine Learning per eseguire il training di un modello di classificazione o di regressione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2021
ms.openlocfilehash: 41b327e793c6b35a2b2aeae825c493a484d84fb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562767"
---
# <a name="train-model-module"></a>Modulo Train Model

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per eseguire il training di un modello di classificazione o di regressione. Il training viene eseguito dopo aver definito un modello e avere impostato i relativi parametri e richiede i dati con tag. È inoltre possibile utilizzare **Train Model** per ripetere il training di un modello esistente con nuovi dati. 

## <a name="how-the-training-process-works"></a>Funzionamento del processo di training

In Azure Machine Learning la creazione e l'uso di un modello di apprendimento automatico è in genere un processo in tre passaggi. 

1. È possibile configurare un modello, scegliendo un particolare tipo di algoritmo e definendone i parametri o gli iperparametri. Scegliere uno dei tipi di modello seguenti: 

    + Modelli di **classificazione** , basati su reti neurali, alberi delle decisioni e foreste delle decisioni e altri algoritmi.
    + Modelli di **regressione** , che possono includere la regressione lineare standard o che usano altri algoritmi, incluse le reti neurali e la regressione Bayes.  

2. Fornire un set di dati con etichetta e con i dati compatibili con l'algoritmo. Connettere sia i dati che il modello per eseguire il **training del modello**.

    Il training generato è un formato binario specifico, iLearner, che incapsula i modelli statistici appresi dai dati. Non è possibile modificare o leggere direttamente questo formato; Tuttavia, altri moduli possono usare questo modello sottoposto a training. 
    
    È inoltre possibile visualizzare le proprietà del modello. Per ulteriori informazioni, vedere la sezione risultati.

3. Al termine del training, usare il modello con training con uno dei [moduli di Punteggio](./score-model.md)per eseguire stime sui nuovi dati.

## <a name="how-to-use-train-model"></a>Come usare Train Model 
    
1. Aggiungere il modulo **Train Model** alla pipeline.  Questo modulo è reperibile nella categoria **Machine Learning** . Espandere **Train**, quindi trascinare il modulo **Train Model** nella pipeline.
  
1.  Nell'input di sinistra allineare la modalità non trainata. Allineare il set di dati di training all'input di destra del **modello Train**.

    Il set di dati di training deve contenere una colonna di etichetta. Tutte le righe senza etichette vengono ignorate.
  
1.  Per **colonna etichetta**, fare clic su **modifica colonna** nel pannello destro del modulo e scegliere una singola colonna contenente i risultati che il modello può usare per il training.
  
    - Per i problemi di classificazione, la colonna Label deve contenere valori **categorici** o valori **discreti** . Alcuni esempi potrebbero essere una classificazione Sì/No, un nome o un codice di classificazione di malattia oppure un gruppo di reddito.  Se si seleziona una colonna non categorica, il modulo restituirà un errore durante il training.
  
    -   Per i problemi di regressione, la colonna Label deve contenere dati **numerici** che rappresentano la variabile di risposta. Idealmente, i dati numerici rappresentano una scala continua. 
    
    Esempi possono essere un punteggio di rischio di credito, il tempo previsto per l'errore per un disco rigido o il numero previsto di chiamate a un Call Center in un giorno o in un momento specifico.  Se non si sceglie una colonna numerica, è possibile che venga ricevuto un errore.
  
    -   Se non si specifica la colonna di etichetta da usare, Azure Machine Learning tenterà di dedurre la colonna di etichetta appropriata usando i metadati del set di dati. Se seleziona la colonna errata, usare il selettore di colonna per correggerla.
  
    > [!TIP] 
    > In caso di problemi con il selettore di colonna, vedere l'articolo [selezionare le colonne nel set di dati](./select-columns-in-dataset.md) per i suggerimenti. Vengono descritti alcuni scenari comuni e suggerimenti per l'utilizzo delle opzioni **with Rules** e **by Name** .
  
1.  Inviare la pipeline. Se si dispone di una grande quantità di dati, l'esecuzione può richiedere alcuni minuti.

    > [!IMPORTANT] 
    > Se si dispone di una colonna ID che corrisponde all'ID di ogni riga o a una colonna di testo che contiene troppi valori univoci, **Train Model** può comportare un errore, ad esempio "numero di valori univoci nella colonna:" {column_name} "è maggiore di quello consentito.
    >
    > Questo è dovuto al fatto che la colonna ha raggiunto la soglia di valori univoci e potrebbe causare una memoria insufficiente. È possibile utilizzare [Modifica metadati](edit-metadata.md) per contrassegnare la colonna come **funzionalità non crittografata** e non verrà utilizzata nel training oppure [estrarre le funzionalità di N-Gram dal modulo di testo](extract-n-gram-features-from-text.md) per la pre-elaborazione della colonna di testo. Per ulteriori informazioni sull'errore, vedere il [codice di errore della finestra di progettazione](././designer-error-codes.md) .

## <a name="model-interpretability"></a>Interpretazione dei modelli

L'interpretazione dei modelli consente di comprendere il modello ML e di presentare la base sottostante per il processo decisionale in modo comprensibile per gli utenti.

Il modulo **Train Model** attualmente supporta l' [uso del pacchetto di interpretazione per spiegare i modelli ml](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs). Sono supportati gli algoritmi predefiniti seguenti:

- Linear Regression
- Regressione rete neurale
- Regressione logistica a due classi
- Two-Class Support Vector Machine
- Foresta delle decisioni multiclasse

Per generare spiegazioni del modello, è possibile selezionare **true** nell'elenco a discesa di **spiegazione del modello** in Train Model Module. Per impostazione predefinita, è impostato su false nel modulo **Train Model** . Si noti che la spiegazione di generazione richiede costi di calcolo aggiuntivi.

![Screenshot che mostra la casella di controllo spiegazione modello](./media/module/train-model-explanation-checkbox.png)

Al termine dell'esecuzione della pipeline, è possibile visitare la scheda **spiegazioni** nel riquadro destro del modulo **Train Model** ed esplorare le prestazioni del modello, il set di dati e l'importanza della funzionalità.

![Screenshot che illustra i grafici di spiegazione del modello](./media/module/train-model-explanations-tab.gif)

Per altre informazioni sull'uso di spiegazioni del modello in Azure Machine Learning, vedere l'articolo sulle procedure per [interpretare i modelli ml](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs).

## <a name="results"></a>Risultati

Dopo il training del modello:


+ Per usare il modello in altre pipeline, selezionare il modulo e selezionare l'icona **registra set di dati** nella scheda **output** nel pannello di destra. È possibile accedere ai modelli salvati nella tavolozza dei moduli in **set di impostazioni**.

+ Per usare il modello per stimare nuovi valori, connetterlo al modulo [Score Model](./score-model.md) , insieme ai nuovi dati di input.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning.