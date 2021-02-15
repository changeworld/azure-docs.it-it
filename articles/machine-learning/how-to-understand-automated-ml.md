---
title: Valuta i risultati dell'esperimento AutoML
titleSuffix: Azure Machine Learning
description: Informazioni su come visualizzare e valutare i grafici e le metriche per ogni esecuzione dell'esperimento di Machine Learning automatizzato.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: c95a75ef48aa9e3db070c6c237f913fabbe893fa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388210"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Valuta i risultati dell'esperimento di Machine Learning automatizzato

Questo articolo illustra come valutare e confrontare i modelli di cui è stato eseguito il training automatico (Machine Learning). Nel corso di un esperimento di Machine Learning automatizzato, vengono create molte esecuzioni e ogni esecuzione crea un modello. Per ogni modello, la ML automatizzata genera metriche di valutazione e grafici che consentono di misurare le prestazioni del modello. 

Ad esempio, l'Machine Learning automatizzata genera i grafici seguenti in base al tipo di esperimento.

| Classificazione| Regressione/previsione |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Matrice di confusione](#confusion-matrix)                       | [Istogramma residui](#residuals)        |
| [Curva ROC (Receiver operating caratteristiche)](#roc-curve) | [Confronto tra stimato e true](#predicted-vs-true) |
| [Curva di richiamo di precisione (PR)](#precision-recall-curve)      |                                          |
| [Curva di accuratezza](#lift-curve)                                   |                                          |
| [Curva guadagni cumulativi](#cumulative-gains-curve)           |                                          |
| [Curva di calibrazione](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito prima di](https://aka.ms/AMLFree) iniziare.
- Un esperimento di Azure Machine Learning creato con uno dei seguenti:
  - [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) (nessun codice necessario)
  - [SDK Azure Machine Learning Python](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Visualizza risultati esecuzione

Al termine dell'esperimento di Machine Learning automatizzato, è possibile trovare una cronologia delle esecuzioni tramite:
  - Un browser con [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md)
  - Notebook di Jupyter con il [widget Jupyter di RunDetails](/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true)

I passaggi e i video seguenti illustrano come visualizzare la cronologia di esecuzione e le metriche e i grafici di valutazione del modello in studio:

1. [Accedere a Studio](https://ml.azure.com/) e passare all'area di lavoro.
1. Nel menu a sinistra selezionare **esperimenti**.
1. Selezionare l'esperimento dall'elenco degli esperimenti.
1. Nella tabella nella parte inferiore della pagina selezionare un'esecuzione automatizzata di ML.
1. Nella scheda **modelli** selezionare il nome dell' **algoritmo** per il modello che si desidera valutare.
1. Nella scheda **metriche** usare le caselle di controllo a sinistra per visualizzare le metriche e i grafici.

![Passaggi per visualizzare le metriche in studio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Metriche per la classificazione

Automatizzato ML calcola le metriche delle prestazioni per ogni modello di classificazione generato per l'esperimento. Queste metriche sono basate sull'implementazione di Scikit learn. 

Molte metriche di classificazione sono definite per la classificazione binaria in due classi e richiedono una media delle classi per produrre un punteggio per la classificazione multiclasse. Scikit-learn offre diversi metodi medi, tre dei quali esposte da Machine Learning automatizzati: **macro**, **micro** e **ponderati**.

- **Macro** : calcolare la metrica per ogni classe e prendere la media non ponderata
- **Micro** -calcolare la metrica a livello globale contando i veri positivi totali, i falsi negativi e i falsi positivi (indipendente dalle classi).
- **Ponderato** : calcolare la metrica per ogni classe e prendere la media ponderata in base al numero di campioni per classe.

Mentre ogni metodo di media ha i propri vantaggi, una considerazione comune quando si seleziona il metodo appropriato è lo squilibrio della classe. Se le classi hanno numeri di campioni diversi, potrebbe essere più istruttivo usare una media macro in cui alle classi minoritarie viene assegnato lo stesso peso alle classi di maggioranza. Altre informazioni sulle [metriche binarie e multiclasse in Machine Learning automatiche](#binary-vs-multiclass-classification-metrics). 

La tabella seguente riepiloga le metriche delle prestazioni del modello calcolate da Machine Learning Machine Learning per ogni modello di classificazione generato per l'esperimento. Per informazioni dettagliate, vedere la documentazione di Scikit-learn collegata nel campo **calcolo** di ogni metrica. 

|Metrica|Descrizione|Calcolo|
|--|--|---|
|Area sotto la curva | AUC è l'area sotto la [curva caratteristica operativa del ricevitore](#roc-curve).<br><br> **Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono: <li>`AUC_macro`, la media aritmetica dell'AUC per ogni classe.<li> `AUC_micro`, calcolato combinando i veri positivi e i falsi positivi da ogni classe. <li> `AUC_weighted`, media aritmetica del punteggio per ogni classe, ponderata in base al numero di istanze true in ogni classe.   |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|precisione| L'accuratezza è il rapporto tra le stime che corrispondono esattamente alle etichette di classe true. <br> <br>**Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [0, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|average_precision riepiloga una curva di precisione-recupero come media ponderata delle precisioni ottenute in corrispondenza di ogni soglia, usando come valore di ponderazione l'incremento nel recupero rispetto alla soglia precedente. <br><br> **Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono:<li>`average_precision_score_macro`, la media aritmetica del Punteggio di precisione media di ogni classe.<li> `average_precision_score_micro`, calcolato combinando i veri positivi e i falsi positivi a ogni cutoff.<li>`average_precision_score_weighted`, la media aritmetica del Punteggio di precisione media per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|balanced_accuracy è la media aritmetica del recupero per ogni classe.<br> <br>**Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [0, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|f1_score è la media armonica di precisione e recupero. Si tratta di una buona misura bilanciata di falsi positivi e falsi negativi. Tuttavia, non considera i veri negativi. <br> <br>**Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono:<li>  `f1_score_macro`: media aritmetica del Punteggio F1 per ogni classe. <li> `f1_score_micro`: calcolato contando il totale dei veri positivi, falsi negativi e falsi positivi. <li> `f1_score_weighted`: ponderato media per frequenza della classe del Punteggio F1 per ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Si tratta della funzione di perdita usata nella regressione logistica (Multinomial) e nelle estensioni di esso, ad esempio le reti neurali, definita come la probabilità di log negativa delle etichette vere, date le stime di un classificatore probabilistico. <br><br> **Obiettivo:** Più vicino a 0 migliore <br> **Intervallo:** [0, inf)|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Il richiamo delle macro normalizzate viene richiamata macro-mediata e normalizzata, in modo che le prestazioni casuali abbiano un punteggio pari a 0, mentre le prestazioni perfette hanno un punteggio pari a 1. <br> <br>**Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>dove `R` è il valore previsto di `recall_score_macro` per le stime casuali.<br><br>`R = 0.5`&nbsp;per &nbsp; la &nbsp; classificazione binaria. <br>`R = (1 / C)` per i problemi di classificazione di classe C.|
matthews_correlation | Il coefficiente di correlazione Matthews è una misura di accuratezza bilanciata, che può essere usata anche se una classe ha molti più campioni di un altro. Il coefficiente 1 indica una stima perfetta, una stima casuale 0 e una stima inversa di-1.<br><br> **Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [-1, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precisione|La precisione è la capacità di un modello di evitare l'assegnazione di etichette a campioni negativi come positivi. <br><br> **Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono: <li> `precision_score_macro`, la media aritmetica di precisione per ogni classe. <li> `precision_score_micro`, calcolato a livello globale contando i veri positivi totali e i falsi positivi. <li> `precision_score_weighted`, la media aritmetica di precisione per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
richiamo| Il richiamo è la capacità di un modello di rilevare tutti i campioni positivi. <br><br> **Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [0, 1]<br> <br>I nomi delle metriche supportati includono: <li>`recall_score_macro`: media aritmetica da richiamare per ogni classe. <li> `recall_score_micro`: calcolato a livello globale contando i veri positivi totali, falsi negativi e falsi positivi.<li> `recall_score_weighted`: media aritmetica di richiamo per ogni classe, ponderata in base al numero di istanze true in ogni classe.|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|L'accuratezza ponderata è l'accuratezza in cui ogni campione viene ponderato in base al numero totale di campioni appartenenti alla stessa classe. <br><br>**Obiettivo:** Più vicino a 1 migliore <br>**Intervallo:** [0, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Metriche di classificazione binaria e multiclasse

Il Machine Learning automatico non distingue le metriche binarie e multiclasse. Le stesse metriche di convalida vengono segnalate se un set di dati ha due classi o più di due classi. Tuttavia, alcune metriche sono destinate alla classificazione multiclasse. Quando vengono applicati a un set di dati binario, queste metriche non considerano alcuna classe come la `true` classe, come si può immaginare. Le metriche che sono chiaramente destinate per la multiclasse sono con suffisso `micro` , `macro` o `weighted` . Gli esempi sono `average_precision_score` ,, `f1_score` `precision_score` , `recall_score` e `AUC` .

Ad esempio, invece di calcolare il richiamo come `tp / (tp + fn)` , la media della multiclasse richiama ( `micro` , `macro` o `weighted` ) la media su entrambe le classi di un set di dati di classificazione binario. Questa operazione equivale a calcolare il richiamo per la `true` classe e la `false` classe separatamente e quindi a prendere la media dei due.

## <a name="confusion-matrix"></a>Matrice di confusione

Le matrici di confusione forniscono un oggetto visivo per il modo in cui un modello di apprendimento automatico sta creando errori sistematici nelle stime per i modelli di classificazione. La parola "confusione" nel nome deriva da un modello "confusionario" o da esempi di etichette erronee. Una cella alla riga `i` e alla colonna `j` in una matrice di confusione contiene il numero di campioni nel set di dati di valutazione che appartengono alla classe `C_i` e sono stati classificati dal modello come classe `C_j` .

In studio una cella più scura indica un numero maggiore di campioni. Selezionando la visualizzazione **normalizzata** nell'elenco a discesa si normalizza su ogni riga della matrice per mostrare la percentuale della classe `C_i` stimata come classe `C_j` . Il vantaggio della visualizzazione non **elaborata** predefinita è che è possibile verificare se lo squilibrio nella distribuzione delle classi effettive ha causato il mancato rispetto della classificazione degli esempi della classe minoritari, un problema comune nei set di dati sbilanciati.

La matrice di confusione di un modello valido avrà la maggior parte degli esempi lungo la diagonale.

### <a name="confusion-matrix-for-a-good-model"></a>Matrice di confusione per un modello valido 
![Matrice di confusione per un modello valido ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Matrice di confusione per un modello non valido
![Matrice di confusione per un modello non valido](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>Curva ROC

La curva ROC (Receiver operating caratteristica) traccia la relazione tra il valore true positive rate (TPR) e la percentuale di falsi positivi (ad) come variazioni della soglia di decisione. La curva ROC può essere meno informativa quando si esegue il training dei modelli nei set di DataSet con un elevato squilibrio della classe, poiché la classe di maggioranza può escludere i contributi dalle classi minoritarie.

L'area sotto la curva (AUC) può essere interpretata come la percentuale di campioni classificati correttamente. Più precisamente, l'AUC è la probabilità che il classificatore classifichi un campione positivo scelto in modo casuale superiore a un campione negativo scelto in modo casuale. La forma della curva fornisce un'intuizione per la relazione tra TPR e ad come funzione della soglia di classificazione o del limite della decisione.

Una curva che si avvicina all'angolo superiore sinistro del grafico si avvicina a un 100% TPR e 0% ad, il miglior modello possibile. Un modello casuale produrrebbe una curva ROC lungo la `y = x` linea dall'angolo inferiore sinistro alla parte superiore destra. Un modello più grave di quello casuale avrà una curva ROC che si immerge sotto la `y = x` linea.
> [!TIP]
> Per gli esperimenti di classificazione, ogni grafico a linee prodotto per i modelli di Machine Learning automatizzati può essere usato per valutare il modello per classe o per la media di tutte le classi. È possibile passare da una visualizzazione all'altra facendo clic sulle etichette delle classi nella legenda a destra del grafico.
### <a name="roc-curve-for-a-good-model"></a>Curva ROC per un modello corretto
![Curva ROC per un modello corretto](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>Curva ROC per un modello non valido
![Curva ROC per un modello non valido](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Precisione-curva di richiamo

La curva di richiamo della precisione traccia la relazione tra precisione e richiamo come la soglia di decisione viene modificata. Il richiamo è la capacità di un modello di rilevare tutti i campioni positivi e la precisione è la capacità di un modello di evitare l'assegnazione di etichette a campioni negativi come positivi. Alcuni problemi aziendali potrebbero richiedere un richiamo più elevato e una maggiore precisione a seconda dell'importanza relativa di evitare falsi negativi rispetto ai falsi positivi.
> [!TIP]
> Per gli esperimenti di classificazione, ogni grafico a linee prodotto per i modelli di Machine Learning automatizzati può essere usato per valutare il modello per classe o per la media di tutte le classi. È possibile passare da una visualizzazione all'altra facendo clic sulle etichette delle classi nella legenda a destra del grafico.
### <a name="precision-recall-curve-for-a-good-model"></a>Curva di richiamo di precisione per un modello corretto
![Curva di richiamo di precisione per un modello corretto](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Curva di richiamo di precisione per un modello non valido
![Curva di richiamo di precisione per un modello non valido](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Curva guadagni cumulativi

La curva guadagni cumulativi traccia la percentuale di campioni positivi correttamente classificati come funzione della percentuale di campioni considerati dove si considerano i campioni nell'ordine di probabilità stimata.

Per calcolare il guadagno, ordinare innanzitutto tutti i campioni dalla probabilità più alta alla più bassa stimata dal modello. Prendere quindi `x%` le stime con la massima confidenza. Dividere il numero di campioni positivi rilevati in quanto `x%` dal numero totale di campioni positivi per ottenere il miglioramento. Il guadagno cumulativo è la percentuale di campioni positivi rilevati quando si considera una percentuale dei dati più probabile che appartengano alla classe positiva.

Un modello perfetto consente di classificare tutti i campioni positivi sopra tutti i campioni negativi offrendo una curva di guadagno cumulativa costituita da due segmenti dritti. Il primo è una linea con pendenza `1 / x` da `(0, 0)` a `(x, 1)` dove `x` è la frazione di campioni che appartengono alla classe positiva ( `1 / num_classes` se le classi sono bilanciate). Il secondo è una linea orizzontale da `(x, 1)` a `(1, 1)` . Nel primo segmento tutti i campioni positivi sono classificati correttamente e il miglioramento cumulativo va all' `100%` interno del primo `x%` dei campioni considerati.

Il modello casuale di base avrà una curva di guadagno cumulativa dopo la `y = x` quale `x%` sono stati rilevati per di campioni considerati solo circa `x%` gli esempi positivi totali. Un modello perfetto avrà una micro curva media che tocca l'angolo superiore sinistro e una riga media della macro con pendenza `1 / num_classes` fino al 100% e quindi orizzontale fino a quando la percentuale di dati è 100.
> [!TIP]
> Per gli esperimenti di classificazione, ogni grafico a linee prodotto per i modelli di Machine Learning automatizzati può essere usato per valutare il modello per classe o per la media di tutte le classi. È possibile passare da una visualizzazione all'altra facendo clic sulle etichette delle classi nella legenda a destra del grafico.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Curva guadagni cumulativi per un modello corretto
![Curva guadagni cumulativi per un modello corretto](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Curva guadagni cumulativi per un modello non valido
![Curva guadagni cumulativi per un modello non valido](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Curva di accuratezza

La curva di accuratezza Mostra il numero di esecuzioni migliori rispetto a un modello casuale. Lift è definito come il rapporto del guadagno cumulativo al guadagno cumulativo di un modello casuale.

Queste prestazioni relative prendono in considerazione il fatto che la classificazione risulta più difficile quando si aumenta il numero di classi. Un modello casuale stima erroneamente una frazione più elevata di campioni da un set di dati con 10 classi rispetto a un set di dati con due classi.

La curva di accuratezza di base è la `y = 1` riga in cui le prestazioni del modello sono coerenti con quelle di un modello casuale. In generale, la curva di accuratezza per un modello appropriato sarà più alta su tale grafico e più lontana dall'asse x, mostrando che quando il modello è più sicuro nelle stime, viene eseguito più volte in modo migliore rispetto all'ipotesi casuale.

> [!TIP]
> Per gli esperimenti di classificazione, ogni grafico a linee prodotto per i modelli di Machine Learning automatizzati può essere usato per valutare il modello per classe o per la media di tutte le classi. È possibile passare da una visualizzazione all'altra facendo clic sulle etichette delle classi nella legenda a destra del grafico.
### <a name="lift-curve-for-a-good-model"></a>Curva di accuratezza per un modello corretto
![Curva di accuratezza per un modello corretto](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Curva di accuratezza per un modello non valido
![Curva di accuratezza per un modello non valido](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Curva di calibrazione

La curva di calibrazione traccia la confidenza di un modello nelle stime rispetto alla proporzione di campioni positivi a ogni livello di confidenza. In un modello ben calibrato verrà classificato correttamente il 100% delle stime a cui viene assegnata la confidenza del 100%, il 50% delle stime assegnate al 50% di confidenza, il 20% delle stime che assegna una confidenza del 20% e così via. Un modello perfettamente calibrato avrà una curva di calibrazione che segue la `y = x` riga in cui il modello stima perfettamente la probabilità che i campioni appartengano a ogni classe.

Un modello con un livello di attendibilità superiore stima le probabilità quasi pari a zero e uno, raramente incerto sulla classe di ogni campione e la curva di calibrazione sarà simile a "S" indietro. Un modello con un livello di attendibilità inferiore assegna una probabilità inferiore in media alla classe che stima e la curva di calibrazione associata sarà simile a una "S". La curva di calibrazione non rappresenta la capacità di un modello di classificazione corretta, ma la possibilità di assegnare correttamente la sicurezza alle stime. Un modello non valido può ancora avere una buona curva di calibrazione se il modello assegna correttamente una bassa confidenza e un'incertezza elevata.

> [!NOTE]
> La curva di calibrazione è sensibile al numero di campioni, pertanto un piccolo set di convalida può produrre risultati rumorosi che possono essere difficili da interpretare. Questo non significa necessariamente che il modello non sia ben calibrato.

### <a name="calibration-curve-for-a-good-model"></a>Curva di calibrazione per un modello corretto
![Curva di calibrazione per un modello corretto](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Curva di calibrazione per un modello non valido
![Curva di calibrazione per un modello non valido](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Metriche di regressione/previsione

Automatizzato ML calcola le stesse metriche delle prestazioni per ogni modello generato, indipendentemente dal fatto che si tratta di un esperimento di regressione o previsione. Queste metriche subiscono anche la normalizzazione per consentire il confronto tra i modelli sottoposti a training su dati con intervalli diversi. Per altre informazioni, vedere [normalizzazione metrica](#metric-normalization).  

La tabella seguente riepiloga le metriche delle prestazioni del modello generate per gli esperimenti di regressione e previsione. Analogamente alle metriche di classificazione, queste metriche sono basate anche sulle implementazioni di Scikit learn. La documentazione di Scikit learn appropriata è collegata di conseguenza nel campo **calcolo** .

|Metrica|Descrizione|Calcolo|
--|--|--|
explained_variance|Spiegazione della varianza misura la misura in cui un modello rappresenta la variazione nella variabile di destinazione. Si tratta della riduzione percentuale della varianza dei dati originali rispetto alla varianza degli errori. Quando la media degli errori è 0, è uguale al coefficiente di determinazione (vedere r2_score di seguito). <br> <br> **Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** (-inf, 1]|[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Media Absolute Error è il valore previsto del valore assoluto della differenza tra la destinazione e la stima.<br><br> **Obiettivo:** Più vicino a 0 migliore <br> **Intervallo:** [0, inf) <br><br> Tipi <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, il mean_absolute_error diviso per l'intervallo di dati. | [Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Errore percentuale assoluto medio (MAPE) è una misura della differenza media tra un valore stimato e il valore effettivo.<br><br> **Obiettivo:** Più vicino a 0 migliore <br> **Intervallo:** [0, inf) ||
median_absolute_error|median_absolute_error è il valore mediano di tutte le differenze assolute tra il target e la stima. Questa perdita è significativa per gli outlier.<br><br> **Obiettivo:** Più vicino a 0 migliore <br> **Intervallo:** [0, inf)<br><br>Tipi <br> `median_absolute_error`<br> `normalized_median_absolute_error`: la median_absolute_error divisa per l'intervallo di dati. |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R ^ 2 è il coefficiente di determinazione o la riduzione percentuale di errori quadratici rispetto a un modello di base che restituisce la media. <br> <br> **Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [-1, 1] <br><br> Nota: R ^ 2 ha spesso l'intervallo (-inf, 1], ma i valori negativi per i modelli non corretti vengono ritagliati da-1 in modo automatico.|[Calcolo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Radice errore quadratico medio (valori RMSE) è la radice quadrata della differenza quadrata prevista tra la destinazione e la stima. Per uno strumento di stima non distorta, valori RMSE è uguale alla deviazione standard.<br> <br> **Obiettivo:** Più vicino a 0 migliore <br> **Intervallo:** [0, inf)<br><br>Tipi<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: la root_mean_squared_error divisa per l'intervallo di dati. |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Radice errore quadratico medio di log è la radice quadrata dell'errore logaritmico quadrato previsto.<br><br>**Obiettivo:** Più vicino a 0 migliore <br> **Intervallo:** [0, inf) <br> <br>Tipi <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: la root_mean_squared_log_error divisa per l'intervallo di dati.  |[Calcolo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| La correlazione di Spearman è una misura non parametrica della monotonicità della relazione tra due set di dati. A differenza della correlazione di Pearson, quella di Spearman non presuppone che entrambi i set di dati siano normalmente distribuiti. Analogamente ad altri coefficienti di correlazione, il Lancieri può variare tra-1 e 1 con 0, per cui non ci sono correlazioni. Le correlazioni di-1 o 1 implicano una relazione monotona esatta. <br><br> Speara è una metrica per la correlazione degli ordini di rango che indica che le modifiche apportate ai valori stimati o effettivi non cambiano il risultato di Spearn se non modificano l'ordine di rango dei valori stimati o effettivi.<br> <br> **Obiettivo:** Più vicino a 1 migliore <br> **Intervallo:** [-1, 1]|[Calcolo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Normalizzazione metrica

Automatizzato ML normalizza le metriche di regressione e previsione che consente il confronto tra i modelli sottoposti a training su dati con intervalli diversi. Un modello sottoposto a training su dati con un intervallo più ampio presenta un errore maggiore rispetto allo stesso modello sottoposto a training sui dati con un intervallo inferiore, a meno che l'errore non venga normalizzato.

Sebbene non esista un metodo standard per normalizzare le metriche degli errori, Machine Learning utilizza l'approccio comune per dividere l'errore in base all'intervallo dei dati: `normalized_error = error / (y_max - y_min)`

Quando si valuta un modello di previsione sui dati delle serie temporali, l'operazione di Machine Learning machine learning richiede passaggi aggiuntivi per garantire che la normalizzazione venga eseguita in base all'ID della serie temporale, perché ogni serie temporale probabilmente presenta una distribuzione diversa dei valori di destinazione.
## <a name="residuals"></a>Residui

Il grafico residui è un istogramma degli errori di stima (residui) generati per gli esperimenti di regressione e previsione. I residui vengono calcolati come `y_predicted - y_true` per tutti i campioni e quindi visualizzati come istogramma per mostrare la distorsione del modello.

In questo esempio, si noti che entrambi i modelli sono leggermente parziali per prevedere un valore inferiore a quello effettivo. Questo non è insolito per un set di dati con una distribuzione asimmetrica delle destinazioni effettive, ma indica prestazioni del modello peggiori. Un modello valido disporrà di una distribuzione residua che picchi a zero con pochi residui al limite. Un modello peggiore disporrà di una distribuzione di residui residui con un minor numero di campioni circa zero.

### <a name="residuals-chart-for-a-good-model"></a>Grafico residui per un modello corretto
![Grafico residui per un modello corretto](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Grafico residui per un modello non valido
![Grafico residui per un modello non valido](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Confronto tra stimato e true

Per l'esperimento di regressione e previsione, il grafico stimato e quello reale tracciano la relazione tra la funzionalità di destinazione (valori true/effettivi) e le stime del modello. I valori true vengono suddivisi in contenitori lungo l'asse x e per ogni bin il valore medio stimato viene tracciato con le barre di errore. In questo modo, è possibile verificare se un modello è polarizzato per la stima di determinati valori. La riga Visualizza la stima media e l'area ombreggiata indica la varianza delle stime circa la media.

Spesso, il valore true più comune avrà le stime più accurate con la varianza più bassa. La distanza della linea di tendenza dalla linea ideale `y = x` in cui sono presenti pochi valori reali è una misura valida delle prestazioni del modello sugli outlier. È possibile utilizzare l'istogramma nella parte inferiore del grafico per motivare la distribuzione effettiva dei dati. L'inclusione di più esempi di dati in cui la distribuzione è sparse può migliorare le prestazioni del modello sui dati non visibili.

In questo esempio, si noti che il modello migliore dispone di una linea stimata rispetto a quella vera che è più vicina alla `y = x` linea ideale.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Grafico stimato rispetto al vero per un modello corretto
![Grafico stimato rispetto al vero per un modello corretto](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Grafico stimato e vero per un modello non valido
![Grafico stimato e vero per un modello non valido](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Spiegazioni del modello e importanza delle funzionalità

Sebbene le metriche e i grafici di valutazione del modello siano utili per misurare la qualità generale di un modello, controllare quale set di dati include un modello usato per eseguire le stime è essenziale quando si pratica l'intelligenza artificiale responsabile. Per questo motivo, Machine Learning Machine Learning fornisce un dashboard di interpretazione dei modelli per misurare e segnalare i contributi relativi delle funzionalità del set di dati.

Per visualizzare il dashboard di interpretazione in studio:
1. [Accedere a Studio](https://ml.azure.com/) e passare all'area di lavoro
2. Nel menu a sinistra selezionare **esperimenti** .
3. Selezionare l'esperimento dall'elenco degli esperimenti
4. Nella tabella nella parte inferiore della pagina selezionare un'esecuzione AutoML
5. Nella scheda **modelli** selezionare il nome dell' **algoritmo** per il modello che si vuole spiegare
6. Nella scheda **spiegazioni** è possibile che sia già stata creata una spiegazione se il modello è stato il migliore
7. Per creare una nuova spiegazione, selezionare **spiega modello** e selezionare il calcolo remoto con cui calcolare le spiegazioni

[Altre informazioni sulle spiegazioni del modello in Machine Learning automatiche](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> Il modello ForecastTCN non è attualmente supportato da spiegazioni automatiche di machine learning e altri modelli di previsione possono avere accesso limitato agli strumenti di interpretazione.

## <a name="next-steps"></a>Passaggi successivi
* Provare i [notebook di esempio per la spiegazione automatica del modello di Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
* Per domande specifiche su Machine Learning, rivolgersi a askautomatedml@microsoft.com .
