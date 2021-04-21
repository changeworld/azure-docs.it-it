---
title: Valutare i risultati dell'esperimento AutoML
titleSuffix: Azure Machine Learning
description: Informazioni su come visualizzare e valutare grafici e metriche per ogni esecuzione dell'esperimento di Machine Learning automatizzato.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: 2bed95385823a167c7a31eed11d752894984ea38
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791878"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Valutare i risultati dell'esperimento di Machine Learning automatizzato

Questo articolo illustra come valutare e confrontare i modelli di cui è stato training l'esperimento di Machine Learning automatizzato (Machine Learning automatizzato). Nel corso di un esperimento di Machine Learning automatizzato, vengono create molte esecuzioni e ogni esecuzione crea un modello. Per ogni modello, machine learning automatizzato genera metriche di valutazione e grafici che consentono di misurare le prestazioni del modello. 

Ad esempio, machine learning automatizzato genera i grafici seguenti in base al tipo di esperimento.

| Classificazione| Regressione/previsione |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Matrice di confusione](#confusion-matrix)                       | [Istogramma residui](#residuals)        |
| [Curva ROC (Receiver Operating Characteristic)](#roc-curve) | [Stimata e vera](#predicted-vs-true) |
| [Curva di richiamo di precisione (PR)](#precision-recall-curve)      |                                          |
| [Curva di accuratezza](#lift-curve)                                   |                                          |
| [Curva dei profitti cumulativi](#cumulative-gains-curve)           |                                          |
| [Curva di calibrazione](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://aka.ms/AMLFree) prima di iniziare.
- Un Azure Machine Learning creato con:
  - Il [studio di Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md) (non è necessario codice)
  - Sdk [Azure Machine Learning Python](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Visualizzare i risultati dell'esecuzione

Al termine dell'esperimento automatizzato di Machine Learning, è possibile trovare una cronologia delle esecuzioni tramite:
  - Un browser con [studio di Azure Machine Learning](overview-what-is-machine-learning-studio.md)
  - Un notebook di Jupyter che usa il [widget Jupyter RunDetails](/python/api/azureml-widgets/azureml.widgets.rundetails)

I passaggi e il video seguenti illustrano come visualizzare la cronologia di esecuzione e le metriche di valutazione del modello e i grafici in studio:

1. [Accedere a Studio e](https://ml.azure.com/) passare all'area di lavoro.
1. Nel menu a sinistra selezionare **Esperimenti**.
1. Selezionare l'esperimento nell'elenco degli esperimenti.
1. Nella tabella nella parte inferiore della pagina selezionare un'esecuzione automatica di Machine Learning.
1. Nella scheda **Modelli** selezionare il nome **dell'algoritmo** per il modello da valutare.
1. Nella scheda **Metriche** usare le caselle di controllo a sinistra per visualizzare metriche e grafici.

![Passaggi per visualizzare le metriche in Studio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Metriche per la classificazione

Machine Learning automatizzato calcola le metriche delle prestazioni per ogni modello di classificazione generato per l'esperimento. Queste metriche si basano sull'implementazione scikit learn. 

Molte metriche di classificazione sono definite per la classificazione binaria in due classi e richiedono una media rispetto alle classi per produrre un punteggio per la classificazione multiclasse. Scikit-learn offre diversi metodi di media, tre dei quali esposti da Machine Learning automatizzato: **macro,** **micro** e **ponderati.**

- **Macro:** calcolare la metrica per ogni classe e prendere la media non ponderata
- **Micro:** calcola la metrica a livello globale contando i veri positivi totali, i falsi negativi e i falsi positivi (indipendentemente dalle classi).
- **Ponderato:** calcolare la metrica per ogni classe e prendere la media ponderata in base al numero di campioni per classe.

Anche se ogni metodo di media ha i suoi vantaggi, una considerazione comune quando si seleziona il metodo appropriato è lo sbilanciamento delle classi. Se le classi hanno un numero diverso di esempi, potrebbe essere più informativo usare una media macro in cui alle classi di maggioranza viene assegnato lo stesso peso alle classi di maggioranza. Altre informazioni sulle [metriche binarie e multiclasse sono disponibili in Machine Learning automatizzato.](#binary-vs-multiclass-classification-metrics) 

La tabella seguente riepiloga le metriche delle prestazioni del modello calcolate da Machine Learning automatizzato per ogni modello di classificazione generato per l'esperimento. Per altri dettagli, vedere la documentazione di scikit-learn collegata nel **campo Calcolo** di ogni metrica. 

|Metrica|Descrizione|Calcolo|
|--|--|---|
|Area sotto la curva | AUC è l'area sotto la [curva delle caratteristiche operative del ricevitore.](#roc-curve)<br><br> **Obiettivo:** Più vicino a 1 è meglio <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono: <li>`AUC_macro`, la media aritmetica dell'AUC per ogni classe.<li> `AUC_micro`, calcolato combinando i veri positivi e i falsi positivi di ogni classe. <li> `AUC_weighted`, media aritmetica del punteggio per ogni classe, ponderata in base al numero di istanze true in ogni classe.<br><br>Nota: i valori AUC segnalati dal Machine Learning automatizzato potrebbero non corrispondere al grafico ROC se sono presenti solo due classi. Per la classificazione binaria, l'implementazione scikit-learn sottostante di AUC non applica effettivamente la media macro/micro/ponderata. Viene invece restituita l'AUC della classe positiva più probabile. Il grafico ROC continua ad applicare la media delle classi per la classificazione binaria come per la classificazione multiclasse.  |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|precisione| L'accuratezza è il rapporto di stime che corrispondono esattamente alle etichette di classe vere. <br> <br>**Obiettivo:** Più vicino a 1 è meglio <br> **Intervallo:** [0, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. <br><br> **Obiettivo:** Più vicino a 1 è meglio <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono:<li>`average_precision_score_macro`, la media aritmetica del punteggio di precisione medio di ogni classe.<li> `average_precision_score_micro`, calcolato combinando i veri positivi e i falsi positivi a ogni cutoff.<li>`average_precision_score_weighted`, media aritmetica del punteggio di precisione medio per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|balanced_accuracy è la media aritmetica del recupero per ogni classe.<br> <br>**Obiettivo:** Più vicino a 1, meglio è <br> **Intervallo:** [0, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|f1_score è la media armonica di precisione e recupero. Si tratta di una buona misura bilanciata di falsi positivi e falsi negativi. Tuttavia, non prende in considerazione i veri negativi. <br> <br>**Obiettivo:** Più vicino a 1, meglio è <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono,<li>  `f1_score_macro`: media aritmetica del punteggio F1 per ogni classe. <li> `f1_score_micro`: calcolato contando i veri positivi totali, i falsi negativi e i falsi positivi. <li> `f1_score_weighted`: media ponderata in base alla frequenza di classe del punteggio F1 per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Si tratta della funzione di perdita usata nella regressione logistica (multinomiale) e nelle estensioni di esso, ad esempio le reti neurali, definite come probabilità di log negativa delle etichette vere date le stime di un classificatore probabilistico. <br><br> **Obiettivo:** Più vicino a 0, meglio è <br> **Intervallo:** [0, inf)|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Il richiamo delle macro normalizzate è il richiamo con media macro e normalizzato, in modo che le prestazioni casuali hanno un punteggio pari a 0 e le prestazioni perfette hanno un punteggio pari a 1. <br> <br>**Obiettivo:** Più vicino a 1, meglio è <br> **Intervallo:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>dove, `R` è il valore previsto di per le stime `recall_score_macro` casuali.<br><br>`R = 0.5`&nbsp;per la &nbsp; classificazione &nbsp; binaria. <br>`R = (1 / C)` per problemi di classificazione di classe C.|
matthews_correlation | Il coefficiente di correlazione dei campioni è una misura bilanciata di accuratezza, che può essere usata anche se una classe ha molti più campioni rispetto a un'altra. Un coefficiente di 1 indica una stima perfetta, una stima casuale 0 e una stima inversa di -1.<br><br> **Obiettivo:** Più vicino a 1 è meglio <br> **Intervallo:** [-1, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precisione|La precisione è la capacità di un modello di evitare di etichettare campioni negativi come positivi. <br><br> **Obiettivo:** Più vicino a 1 è meglio <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono: <li> `precision_score_macro`, la media aritmetica di precisione per ogni classe. <li> `precision_score_micro`, calcolato a livello globale contando i veri positivi e i falsi positivi totali. <li> `precision_score_weighted`, la media aritmetica di precisione per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
richiamo| Il richiamo è la capacità di un modello di rilevare tutti i campioni positivi. <br><br> **Obiettivo:** Più vicino a 1 è meglio <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono: <li>`recall_score_macro`: media aritmetica di richiamo per ogni classe. <li> `recall_score_micro`: calcolato a livello globale contando i veri positivi totali, i falsi negativi e i falsi positivi.<li> `recall_score_weighted`: media aritmetica di richiamo per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|L'accuratezza ponderata è l'accuratezza in cui ogni campione è ponderato in base al numero totale di campioni appartenenti alla stessa classe. <br><br>**Obiettivo:** Più vicino a 1, meglio è <br>**Intervallo:** [0, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Metriche di classificazione binarie e multiclassi

Machine Learning automatizzato non distingue tra metriche binarie e multiclasse. Le stesse metriche di convalida vengono segnalate se un set di dati ha due classi o più di due classi. Tuttavia, alcune metriche sono destinate alla classificazione multiclasse. Se applicate a un set di dati binario, queste metriche non trattano alcuna classe come `true` classe, come ci si potrebbe aspettare. Le metriche chiaramente destinate a multiclasse sono con suffisso `micro` , `macro` o `weighted` . Ad `average_precision_score` esempio, `f1_score` , , , e `precision_score` `recall_score` `AUC` .

Ad esempio, invece di calcolare il richiamo come , il richiamo medio multiclasse ( , o ) viene calcolato su entrambe le classi di un set di dati `tp / (tp + fn)` `micro` di classificazione `macro` `weighted` binaria. Equivale a calcolare il richiamo per la classe e la classe separatamente e quindi a prendere `true` `false` la media dei due.

Machine Learning automatizzato non calcola le metriche binarie, che sono metriche per i set di dati di classificazione binaria. Tuttavia, queste metriche possono essere calcolate manualmente usando la [matrice di confusione](#confusion-matrix) generata da Machine Learning automatizzato per quella particolare esecuzione. Ad esempio, è possibile calcolare la precisione, , con i valori veri positivi e falsi positivi visualizzati in un grafico a matrice di confusione `tp / (tp + fp)` 2x2.

## <a name="confusion-matrix"></a>Matrice di confusione

Le matrici di confusione forniscono un oggetto visivo per il modo in cui un modello di Machine Learning crea errori sistematici nelle stime per i modelli di classificazione. La parola "confusione" nel nome deriva da un modello che "confonde" o etichetta gli esempi in modo erto. Una cella in corrispondenza di riga e colonna in una matrice di confusione contiene il numero di campioni nel set di dati di valutazione appartenenti alla classe e classificati dal modello `i` `j` come classe `C_i` `C_j` .

In Studio una cella più scura indica un numero maggiore di campioni. La **selezione della visualizzazione** Normalizzata nell'elenco a discesa normalizza ogni riga della matrice per mostrare la percentuale della classe stimata come classe `C_i` `C_j` . Il vantaggio della  visualizzazione Raw predefinita è che è possibile vedere se lo sbilanciamento nella distribuzione delle classi effettive ha causato la classificazione errata dei campioni dalla classe di minoranza, un problema comune nei set di dati sbilanciati.

La matrice di confusione di un modello di buon tipo avrà la maggior parte dei campioni lungo la diagonale.

### <a name="confusion-matrix-for-a-good-model"></a>Matrice di confusione per un modello di qualità 
![Matrice di confusione per un modello di qualità ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Matrice di confusione per un modello non erato
![Matrice di confusione per un modello non erato](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>Curva ROC

La curva ROC (Receiver Operating Characteristic) traccia la relazione tra il tasso di vero positivo (TPR) e il tasso di falsi positivi (FPR) quando cambia la soglia decisionale. La curva ROC può essere meno informativa quando si esegue il training di modelli su set di dati con sbilanciamento di classe elevato, in quanto la classe di maggioranza può ottenere contributi dalle classi di minoranza.

L'area sotto la curva (AUC) può essere interpretata come la proporzione di campioni classificati correttamente. Più precisamente, l'AUC è la probabilità che il classificatore classifica un campione positivo scelto casualmente più in alto rispetto a un campione negativo scelto in modo casuale. La forma della curva fornisce un'avariazione per la relazione tra TPR e FPR come funzione della soglia di classificazione o del limite decisionale.

Una curva che si avvicina all'angolo superiore sinistro del grafico si avvicina a una TPR al 100% e a un FPR 0%, il modello migliore possibile. Un modello casuale produrrebbe una curva ROC lungo la `y = x` linea dall'angolo inferiore sinistro all'angolo superiore destro. Un modello peggiore di quello casuale avrebbe una curva ROC che si disassella sotto la `y = x` linea.
> [!TIP]
> Per gli esperimenti di classificazione, ogni grafico a linee prodotto per i modelli di Machine Learning automatizzato può essere usato per valutare il modello per classe o per ottenere una media su tutte le classi. È possibile passare da una visualizzazione all'altra facendo clic sulle etichette di classe nella legenda a destra del grafico.

### <a name="roc-curve-for-a-good-model"></a>Curva ROC per un modello di qualità
![Curva ROC per un buon modello](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>Curva ROC per un modello non erto
![Curva ROC per un modello non erto](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Curva di richiamo di precisione

La curva di richiamo di precisione traccia la relazione tra precisione e richiamo quando cambia la soglia della decisione. Il richiamo è la capacità di un modello di rilevare tutti i campioni positivi e la precisione è la capacità di un modello di evitare di etichettare i campioni negativi come positivi. Alcuni problemi aziendali potrebbero richiedere un richiamo più elevato e una precisione maggiore a seconda dell'importanza relativa di evitare falsi negativi o falsi positivi.
> [!TIP]
> Per gli esperimenti di classificazione, ognuno dei grafici a linee prodotti per i modelli di Machine Learning automatizzati può essere usato per valutare il modello per classe o per una media su tutte le classi. È possibile passare da una visualizzazione all'altra facendo clic sulle etichette di classe nella legenda a destra del grafico.
### <a name="precision-recall-curve-for-a-good-model"></a>Curva di richiamo di precisione per un modello di qualità
![Curva di richiamo di precisione per un modello di qualità](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Curva di richiamo di precisione per un modello non valida
![Curva di richiamo di precisione per un modello non valida](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Curva dei profitti cumulativi

La curva dei profitti cumulativi traccia la percentuale di campioni positivi classificati correttamente come funzione della percentuale di campioni considerati in cui si considerano i campioni nell'ordine di probabilità stimata.

Per calcolare il guadagno, ordinare prima tutti i campioni dalla probabilità più alta alla più bassa stimata dal modello. Eseguire quindi `x%` le stime di attendibilità più elevata. Dividere il numero di campioni positivi rilevati in per `x%` il numero totale di campioni positivi per ottenere il guadagno. Il guadagno cumulativo è la percentuale di campioni positivi rilevati quando si prende in considerazione una percentuale dei dati che probabilmente appartengono alla classe positiva.

Un modello perfetto classifica tutti i campioni positivi sopra tutti i campioni negativi, con una curva di guadagno cumulativa costituito da due segmenti rette. Il primo è una linea con inclinazione da a dove è la frazione di campioni che appartengono alla classe positiva `1 / x` ( se le classi sono `(0, 0)` `(x, 1)` `x` `1 / num_classes` bilanciate). Il secondo è una linea orizzontale da `(x, 1)` a `(1, 1)` . Nel primo segmento tutti i campioni positivi vengono classificati correttamente e il guadagno cumulativo va a `100%` all'interno del primo `x%` dei campioni considerati.

Il modello casuale di base avrà una curva di guadagno cumulativa dopo la quale per i campioni considerati solo circa `y = x` circa i campioni positivi totali sono stati `x%` `x%` rilevati. Un modello perfetto avrà una curva micro-media che tocca l'angolo superiore sinistro e una retta media macro con inclinazione fino a un guadagno cumulativo del 100% e quindi orizzontale fino a quando la percentuale di dati non è `1 / num_classes` 100.
> [!TIP]
> Per gli esperimenti di classificazione, ogni grafico a linee prodotto per i modelli di Machine Learning automatizzato può essere usato per valutare il modello per classe o per ottenere una media su tutte le classi. È possibile passare da una visualizzazione all'altra facendo clic sulle etichette di classe nella legenda a destra del grafico.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Curva dei profitti cumulativi per un modello di buon tipo
![Curva dei profitti cumulativi per un modello di buon tipo](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Curva dei miglioramenti cumulativi per un modello non utile
![Curva dei miglioramenti cumulativi per un modello non utile](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Curva di accuratezza

La curva di elevamento mostra il numero di prestazioni migliori di un modello rispetto a un modello casuale. L'elevazione è definita come rapporto tra il guadagno cumulativo e il guadagno cumulativo di un modello casuale.

Queste prestazioni relative tiene conto del fatto che la classificazione diventa più difficile quando si aumenta il numero di classi. Un modello casuale stima erroneamente una frazione più elevata di campioni da un set di dati con 10 classi rispetto a un set di dati con due classi.

La curva di lifting di base `y = 1` è la linea in cui le prestazioni del modello sono coerenti con quella di un modello casuale. In generale, la curva di accurateità per un modello di buon livello sarà più alta su tale grafico e più lontano dall'asse x, dimostrando che quando il modello è più sicuro nelle stime, le prestazioni sono molte volte migliori rispetto all'ipotesi casuale.

> [!TIP]
> Per gli esperimenti di classificazione, ognuno dei grafici a linee prodotti per i modelli di Machine Learning automatizzati può essere usato per valutare il modello per classe o per una media su tutte le classi. È possibile passare da una visualizzazione all'altra facendo clic sulle etichette di classe nella legenda a destra del grafico.
### <a name="lift-curve-for-a-good-model"></a>Curva di sollevamento per un modello di qualità
![Curva di sollevamento per un modello di qualità](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Curva di sollevamento per un modello non erto
![Curva di sollevamento per un modello non erto](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Curva di calibrazione

La curva di calibrazione traccia l'attendibilità di un modello nelle stime rispetto alla percentuale di campioni positivi a ogni livello di attendibilità. Un modello ben calibrato classifica correttamente il 100% delle stime a cui assegna il 100% di attendibilità, il 50% delle stime assegna il 50% di attendibilità, il 20% delle stime a cui assegna una confidenza del 20% e così via. Un modello perfettamente calibrato avrà una curva di calibrazione seguendo la linea in cui il modello stima perfettamente la probabilità che i campioni `y = x` appartengano a ogni classe.

Un modello con maggiore sicurezza stima le probabilità vicine a zero e una, raramente incerte sulla classe di ogni campione e la curva di calibrazione avrà un aspetto simile a "S" all'indietro. Un modello con minore probabilità assegna mediamente una probabilità inferiore alla classe stimata e la curva di calibrazione associata sarà simile a una "S". La curva di calibrazione non illustra la capacità di un modello di classificare correttamente, ma piuttosto la capacità di assegnare correttamente l'attendibilità alle stime. Un modello non corretto può comunque avere una curva di calibrazione corretta se il modello assegna correttamente una bassa attendibilità e un'elevata incertezza.

> [!NOTE]
> La curva di calibrazione è sensibile al numero di campioni, quindi un piccolo set di convalida può produrre risultati rumorosi che possono essere difficili da interpretare. Questo non significa necessariamente che il modello non sia ben calibrato.

### <a name="calibration-curve-for-a-good-model"></a>Curva di calibrazione per un modello di qualità
![Curva di calibrazione per un modello di qualità](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Curva di calibrazione per un modello non erudito
![Curva di calibrazione per un modello non erudito](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Metriche di regressione/previsione

Machine Learning automatizzato calcola le stesse metriche delle prestazioni per ogni modello generato, indipendentemente dal fatto che si tratta di un esperimento di regressione o previsione. Queste metriche vengono anche sottoposte a normalizzazione per consentire il confronto tra modelli sottoposti a training su dati con intervalli diversi. Per altre informazioni, vedere [Normalizzazione delle metriche.](#metric-normalization)  

La tabella seguente riepiloga le metriche delle prestazioni del modello generate per gli esperimenti di regressione e previsione. Analogamente alle metriche di classificazione, queste metriche si basano anche sulle implementazioni scikit learn. La documentazione di scikit learn appropriata è collegata di conseguenza, nel **campo** Calcolo.

|Metrica|Descrizione|Calcolo|
--|--|--|
explained_variance|La varianza spiegata misura la misura in cui un modello rappresenta la variazione nella variabile di destinazione. Si tratta della riduzione percentuale della varianza dei dati originali rispetto alla varianza degli errori. Quando la media degli errori è 0, è uguale al coefficiente di determinazione (vedere r2_score di seguito). <br> <br> **Obiettivo:** Più vicino a 1 è meglio <br> **Intervallo:** (-inf, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Errore assoluto medio è il valore previsto del valore assoluto della differenza tra la destinazione e la stima.<br><br> **Obiettivo:** Più vicino a 0, meglio è <br> **Intervallo:** [0, inf) <br><br> Tipi: <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, il mean_absolute_error diviso per l'intervallo dei dati. | [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|L'errore percentuale assoluto medio (MAPE) è una misura della differenza media tra un valore previsto e il valore effettivo.<br><br> **Obiettivo:** Più vicino a 0, meglio è <br> **Intervallo:** [0, inf) ||
median_absolute_error|median_absolute_error è il valore mediano di tutte le differenze assolute tra il target e la stima. Questa perdita è significativa per gli outlier.<br><br> **Obiettivo:** Più vicino a 0 è migliore è <br> **Intervallo:** [0, inf)<br><br>Tipi: <br> `median_absolute_error`<br> `normalized_median_absolute_error`: il median_absolute_error diviso per l'intervallo dei dati. |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (coefficiente di determinazione) misura la riduzione proporzionale dell'errore quadrativo medio (MSE) rispetto alla varianza totale dei dati osservati. <br> <br> **Obiettivo:** Più vicino a 1, meglio è <br> **Intervallo:** [-1, 1]<br><br>Nota: R<sup>2</sup> include spesso l'intervallo (-inf, 1). Il valore MSE può essere maggiore della varianza osservata, quindi R<sup>2</sup> può avere valori negativi arbitrariamente grandi, a seconda dei dati e delle stime del modello. I clip automatizzati di Machine Learning hanno segnalato punteggi R<sup>2</sup> a -1, quindi un valore pari a -1 per R<sup>2</sup> indica probabilmente che il punteggio R<sup>2</sup> vero è minore di -1. Si considerino gli altri valori delle metriche e le proprietà dei dati quando si interpreta un punteggio R<sup>2</sup> negativo.|[Calcolo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |L'errore quadratica media radice (RMSE) è la radice quadrata della differenza quadratica prevista tra la destinazione e la stima. Per una stima imparziale, RMSE è uguale alla deviazione standard.<br> <br> **Obiettivo:** Più vicino a 0 è migliore è <br> **Intervallo:** [0, inf)<br><br>Tipi:<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: il root_mean_squared_error diviso per l'intervallo dei dati. |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|L'errore di log quadratico medio radice è la radice quadrata dell'errore logaritmico quadratico previsto.<br><br>**Obiettivo:** Più vicino a 0, meglio è <br> **Intervallo:** [0, inf) <br> <br>Tipi: <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: il root_mean_squared_log_error diviso per l'intervallo dei dati.  |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| La correlazione di Spearman è una misura non parametrica della monotonicità della relazione tra due set di dati. A differenza della correlazione di Pearson, quella di Spearman non presuppone che entrambi i set di dati siano normalmente distribuiti. Come altri coefficienti di correlazione, Spearman varia tra -1 e 1 e 0 implica l'assenza di correlazione. Le correlazioni di -1 o 1 implicano una relazione monotona esatta. <br><br> Spearman è una metrica di correlazione dell'ordine di classificazione che indica che le modifiche ai valori stimati o effettivi non modificheranno il risultato di Spearman se non modificano l'ordine di classificazione dei valori stimati o effettivi.<br> <br> **Obiettivo:** Più vicino a 1 è meglio <br> **Intervallo:** [-1, 1]|[Calcolo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Normalizzazione delle metriche

Machine Learning automatizzato normalizza le metriche di regressione e previsione che consentono il confronto tra modelli di cui è stato eseguito il training su dati con intervalli diversi. Un modello con training su dati con un intervallo più grande ha un errore maggiore rispetto allo stesso modello con training sui dati con un intervallo più piccolo, a meno che tale errore non venga normalizzato.

Anche se non esiste un metodo standard per normalizzare le metriche di errore, l'apprendimento automatico adotta l'approccio comune di dividere l'errore per l'intervallo dei dati: `normalized_error = error / (y_max - y_min)`

Quando si valuta un modello di previsione sui dati delle serie temporizzazioni, il Machine Learning automatizzato esegue passaggi aggiuntivi per garantire che la normalizzazione avvenga in base all'ID serie temporale (granularità), perché ogni serie temporale ha probabilmente una distribuzione diversa dei valori di destinazione.
## <a name="residuals"></a>Residui

Il grafico dei residui è un istogramma degli errori di stima (residui) generati per gli esperimenti di regressione e previsione. I residui vengono calcolati come `y_predicted - y_true` per tutti i campioni e quindi visualizzati come istogramma per mostrare la distorsione del modello.

In questo esempio, si noti che entrambi i modelli sono leggermente distorti per stimare un valore inferiore al valore effettivo. Ciò non è insolito per un set di dati con una distribuzione a inclinazione delle destinazioni effettive, ma indica prestazioni peggiori del modello. Un buon modello avrà una distribuzione dei residui che raggiunge il picco a zero con pochi residui agli estremi. Un modello peggiore avrà una distribuzione dei residui ripartito con un minor numero di campioni intorno allo zero.

### <a name="residuals-chart-for-a-good-model"></a>Grafico dei residui per un modello di qualità
![Grafico dei residui per un modello di qualità](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Grafico dei residui per un modello non erto
![Grafico dei residui per un modello non erto](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Stimata rispetto a true

Per l'esperimento di regressione e previsione, il grafico stimato e vero traccia la relazione tra la funzionalità di destinazione (valori true/effettivi) e le stime del modello. I valori true vengono binati lungo l'asse x e per ogni contenitore il valore medio previsto viene tracciato con barre di errore. In questo modo è possibile verificare se un modello è orientato verso la stima di determinati valori. La riga visualizza la stima media e l'area ombreggiata indica la varianza delle stime intorno a tale media.

Spesso, il valore reale più comune avrà le stime più accurate con la varianza più bassa. La distanza della linea di tendenza dalla linea ideale in cui sono presenti pochi valori veri è una buona misura delle prestazioni del modello `y = x` sugli outlier. È possibile usare l'istogramma nella parte inferiore del grafico per descrizione della distribuzione effettiva dei dati. L'inclusione di più esempi di dati in cui la distribuzione è di tipo sparse può migliorare le prestazioni del modello sui dati nonvisibile.

In questo esempio, si noti che il modello migliore ha una linea stimata e vera più vicina alla linea `y = x` ideale.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Grafico previsto e grafico vero per un modello positivo
![Grafico previsto e grafico vero per un modello positivo](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Grafico previsto e vero per un modello non erto
![Grafico previsto e vero per un modello non erto](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Spiegazioni del modello e priorità delle funzionalità

Anche se i grafici e le metriche di valutazione del modello sono utili per misurare la qualità generale di un modello, controllare quali funzionalità del set di dati vengono usate da un modello per eseguire le stime è essenziale quando si pratica l'intelligenza artificiale responsabile. Ecco perché Machine Learning automatizzato fornisce un dashboard di spiegazioni del modello per misurare e segnalare i contributi relativi delle funzionalità del set di dati. Vedere come [visualizzare il dashboard delle spiegazioni nella studio di Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview).

Per un'esperienza Code First, vedere come configurare le spiegazioni del modello per gli esperimenti di Machine Learning automatizzato [con Azure Machine Learning Python SDK.](how-to-machine-learning-interpretability-automl.md)

> [!NOTE]
> Il modello ForecastTCN non è attualmente supportato dalle spiegazioni automatizzate di Machine Learning e altri modelli di previsione possono avere accesso limitato agli strumenti di interpretabilità.

## <a name="next-steps"></a>Passaggi successivi
* Provare i notebook di esempio di spiegazione del modello di Machine [Learning automatizzato](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
* Per domande specifiche sul Machine Learning automatizzato, contattare askautomatedml@microsoft.com .
