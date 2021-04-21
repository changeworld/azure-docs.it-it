---
title: Ottimizzazione di un modello da parte degli iperparameboli
titleSuffix: Azure Machine Learning
description: Automatizzare l'ottimizzazione degli iperparametra per i modelli di Deep Learning e Machine Learning usando Azure Machine Learning.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ec01561c5c44c850b32187629552b1bdb99537e7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819179"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Ottimizzazione di un modello con un iperparame Azure Machine Learning

Automatizzare l'ottimizzazione efficiente degli iperparame Azure Machine Learning [pacchetto HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive). Informazioni su come completare i passaggi necessari per ottimizzare gli iperparame aggiuntivi con [Azure Machine Learning SDK:](/python/api/overview/azure/ml/)

1. Definire lo spazio di ricerca dei parametri
1. Specificare una metrica primaria da ottimizzare  
1. Specificare i criteri di terminazione anticipata per le esecuzioni a prestazioni basse
1. Creare e assegnare risorse
1. Avviare un esperimento con la configurazione definita
1. Visualizzare le esecuzioni di training
1. Selezionare la configurazione migliore per il modello

## <a name="what-is-hyperparameter-tuning"></a>Che cos'è l'ottimizzazione degli iperparamermi?

**Gli iperparamerti** sono parametri regolabili che consentono di controllare il processo di training del modello. Ad esempio, con le reti neurali si decide il numero di livelli nascosti e il numero di nodi in ogni livello. Le prestazioni del modello dipendono in gran parte dagli iperparamersi.

 **L'ottimizzazione** degli **iperparameti,** detta anche ottimizzazione degli iperparameti, è il processo di individuazione della configurazione degli iperparameboli che offre prestazioni ottimali. Il processo è in genere costoso dal punto di vista del calcolo e manuale.

Azure Machine Learning consente di automatizzare l'ottimizzazione degli iperparamezzi ed eseguire esperimenti in parallelo per ottimizzare in modo efficiente gli iperparamezzi.


## <a name="define-the-search-space"></a>Definire lo spazio di ricerca

Ottimizzare gli iperparamezionari esplorando l'intervallo di valori definito per ogni iperparame stesso.

Gli iperparametre possono essere discreti o continui e hanno una distribuzione di valori descritti da [un'espressione di parametro](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions).

### <a name="discrete-hyperparameters"></a>Iperparametri discreti

Gli iperparametri discreti vengono specificati come una scelta (`choice`) tra valori discreti. `choice` può essere:

* Uno o più valori delimitati da virgole
* Un oggetto `range`
* Un oggetto `list` arbitrario


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

In questo caso, uno dei valori `batch_size` [16, 32, 64, 128] e accetta uno dei valori `number_of_hidden_layers` [1, 2, 3, 4].

È anche possibile specificare i seguenti iperparameboli discreti avanzati usando una distribuzione:

* `quniform(low, high, q)`: restituisce un valore come round(uniform(low, high) / q) * q
* `qloguniform(low, high, q)`: restituisce un valore come round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)`: restituisce un valore come round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)`: restituisce un valore come round(exp(normal(mu, sigma)) / q) * q

### <a name="continuous-hyperparameters"></a>Iperparametri continui 

Gli iperparamementi continui vengono specificati come distribuzione su un intervallo continuo di valori:

* `uniform(low, high)` : restituisce un valore distribuito in modo uniforme tra low e high
* `loguniform(low, high)` : restituisce un valore ricavato in base a exp(uniform(low, high)), in modo che il logaritmo del valore restituito sia distribuito uniformemente
* `normal(mu, sigma)` : restituisce un valore reale distribuito in modo normale con la media mu e la deviazione standard sigma
* `lognormal(mu, sigma)` : restituisce un valore ricavato in base a exp(normal(mu, sigma)), in modo che il logaritmo del valore restituito sia distribuito in modo normale

Esempio di definizione per lo spazio dei parametri:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Questo codice definisce uno spazio di ricerca con due parametri: `learning_rate` e `keep_probability`. `learning_rate` ha una distribuzione normale con un valore medio di 10 e una deviazione standard pari a 3. `keep_probability` ha una distribuzione uniforme con un valore minimo di 0,05 e un valore massimo di 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Campionamento dello spazio degli iperparametri

Specificare il metodo di campionamento dei parametri da usare per lo spazio degli iperparametre. Azure Machine Learning supporta i metodi seguenti:

* Campionamento casuale
* Campionamento a griglia
* Campionamento bayesiano

#### <a name="random-sampling"></a>Campionamento casuale

[Il campionamento](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) casuale supporta iperparamementi discreti e continui. Supporta la terminazione anticipata di esecuzioni a prestazioni basse. Alcuni utenti esercitino una ricerca iniziale con campionamento casuale e quindi perfezionano lo spazio di ricerca per migliorare i risultati.

Nel campionamento casuale i valori degli iperparametri vengono selezionati in modo casuale dallo spazio di ricerca definito. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Campionamento a griglia

[Il campionamento](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) a griglia supporta iperparamementi discreti. Usare il campionamento a griglia se è possibile effettuare una ricerca esaustiva nello spazio di ricerca. Supporta la terminazione anticipata di esecuzioni a prestazioni basse.

Il campionamento a griglia esegue una semplice ricerca a griglia su tutti i valori possibili. Il campionamento a griglia può essere usato solo `choice` con iperparameboli. Ad esempio, lo spazio seguente include sei campioni:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Campionamento bayesiano

[Il campionamento bayesiano](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) è basato sull'algoritmo di ottimizzazione bayesiano. Seleziona gli esempi in base al modo in cui sono stati emersi in precedenza, in modo che i nuovi campioni migliorino la metrica primaria.

Il campionamento bayesiano è consigliato se il budget disponibile è sufficiente per esplorare lo spazio degli iperparametri. Per ottenere risultati ottimali, è consigliabile un numero massimo di esecuzioni maggiore o uguale a 20 volte il numero di iperparamementi ottimizzati. 

Il numero di esecuzioni simultanee influisce sull'efficacia del processo di ottimizzazione. Un numero minore di esecuzioni simultanee può comportare una migliore convergenza del campionamento, poiché il grado più ridotto di parallelismo aumenta il numero di esecuzioni che traggono vantaggio dalle esecuzioni completate in precedenza.

Il campionamento bayesiano supporta `choice` solo `uniform` le distribuzioni , e nello spazio di `quniform` ricerca.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Specificare la metrica primaria

Specificare la [metrica primaria da](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) ottimizzare per l'ottimizzazione degli iperparametrici. Ogni esecuzione di training viene valutata in base a questa metrica primaria I criteri di terminazione anticipata utilizzano la metrica primaria per identificare le esecuzioni a prestazioni basse.

Specificare gli attributi seguenti per la metrica primaria:

* `primary_metric_name`: il nome della metrica primaria deve corrispondere esattamente al nome della metrica registrata dallo script di training
* `primary_metric_goal`: può essere `PrimaryMetricGoal.MAXIMIZE` o `PrimaryMetricGoal.MINIMIZE` e determina se la metrica primaria verrà aumentata o ridotta durante la valutazione dell'esecuzione. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Questo esempio ottimizza la "precisione".

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Registrare le metriche per l'ottimizzazione degli iperparametri

Lo script di training per il modello **deve** registrare la metrica primaria durante il training del modello in modo che HyperDrive possa accedervi per l'ottimizzazione degli iperparametrici.

Registrare la metrica primaria nello script di training con il frammento di esempio seguente:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Lo script di training calcola l'oggetto e lo registra come `val_accuracy` "accuratezza" della metrica primaria. Ogni volta che la metrica viene registrata, viene ricevuta dal servizio di ottimizzazione degli iperparametrici. È necessario determinare la frequenza di creazione di report.

Per altre informazioni sui valori di registrazione nelle esecuzioni di training del modello, vedere [Abilitare la registrazione nelle esecuzioni di training di Azure ML.](how-to-log-view-metrics.md)

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Specificare i criteri di terminazione anticipata

Termina automaticamente le esecuzioni con prestazioni scarse con criteri di terminazione anticipata. La terminazione anticipata migliora l'efficienza di calcolo.

È possibile configurare i parametri seguenti che controllano quando viene applicato un criterio:

* `evaluation_interval`: frequenza di applicazione dei criteri. Ogni volta che lo script di training registra la metrica primaria viene conteggiata come un intervallo. Un `evaluation_interval` valore di 1 applica i criteri ogni volta che lo script di training segnala la metrica primaria. Un `evaluation_interval` valore di 2 applica i criteri ogni volta. Se non specificato, `evaluation_interval` è impostato su 1 per impostazione predefinita.
* `delay_evaluation`: ritarda la prima valutazione dei criteri per un numero di intervalli specificato. Si tratta di un parametro facoltativo che evita la terminazione prematura delle esecuzioni di training consentendo l'esecuzione di tutte le configurazioni per un numero minimo di intervalli. Se specificato, i criteri vengono applicati ogni multiplo di evaluation_interval che è maggiore o uguale a delay_evaluation.

Azure Machine Learning supporta i criteri di terminazione anticipata seguenti:
* [Criteri Bandit](#bandit-policy)
* [Criteri di arresto con valore mediano](#median-stopping-policy)
* [Criteri di selezione con troncamento](#truncation-selection-policy)
* [Criteri senza terminazione](#no-termination-policy-default)


### <a name="bandit-policy"></a>Criteri Bandit

[I criteri Disas](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) si basano sull'intervallo di valutazione e sul fattore di flessibilità. Termina quando la metrica primaria non rientra nel fattore/margine di flessibilità specificato dell'esecuzione con esito positivo.

> [!NOTE]
> Il campionamento bayesiano non supporta la terminazione anticipata. Quando si usa il campionamento bayesiano, impostare `early_termination_policy = None` .

Specificare i parametri di configurazione seguenti:

* `slack_factor` o `slack_amount`: margine di flessibilità consentito rispetto all'esecuzione di training con le migliori prestazioni. `slack_factor` specifica il margine di flessibilità consentito come rapporto. `slack_amount` specifica il margine di flessibilità consentito come valore assoluto, anziché come rapporto.

    Si consideri, ad esempio, un criterio Disas applicato all'intervallo 10. Si supponga che l'esecuzione con le prestazioni migliori all'intervallo 10 segnalata da una metrica primaria sia 0,8 con l'obiettivo di ottimizzare la metrica primaria. Se il criterio specifica un valore di 0,2, tutte le esecuzioni di training la cui metrica migliore all'intervallo 10 è minore di `slack_factor` 0,66 (0,8/(1+ )) verranno `slack_factor` terminate.
* `evaluation_interval`: (facoltativo) frequenza per l'applicazione dei criteri
* `delay_evaluation`: (facoltativo) ritarda la prima valutazione dei criteri per un numero specificato di intervalli


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

In questo esempio, i criteri di terminazione anticipata vengono applicati a ogni intervallo in cui vengono segnalate le metriche, a partire dall'intervallo di valutazione 5. Verrà terminata qualsiasi esecuzione la cui metrica migliore è minore di (1/(1+0,1) o del 91% rispetto all'esecuzione con le migliori prestazioni.

### <a name="median-stopping-policy"></a>Criteri di arresto con valore mediano

[L'arresto mediano](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) è un criterio di terminazione anticipata basato sulle medie in esecuzione delle metriche primarie segnalate dalle esecuzioni. Questo criterio calcola le medie in esecuzione in tutte le esecuzioni di training e arresta le esecuzioni il cui valore della metrica primaria è peggiore della mediana delle medie.

I parametri di configurazione accettati da questi criteri sono i seguenti:
* `evaluation_interval`: frequenza per l'applicazione del criterio (parametro facoltativo).
* `delay_evaluation`: ritarda la prima valutazione dei criteri per un numero di intervalli specificato (parametro facoltativo).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In questo esempio, i criteri di terminazione anticipata vengono applicati a ogni intervallo, a partire dall'intervallo di valutazione 5. Un'esecuzione viene arrestata all'intervallo 5 se la metrica primaria migliore è peggiore della mediana delle medie in esecuzione negli intervalli 1:5 in tutte le esecuzioni di training.

### <a name="truncation-selection-policy"></a>Criteri di selezione con troncamento

[La selezione del troncamento](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) annulla una percentuale di esecuzioni con le prestazioni più basse a ogni intervallo di valutazione. Le esecuzioni vengono confrontate usando la metrica primaria. 

I parametri di configurazione accettati da questi criteri sono i seguenti:

* `truncation_percentage`: percentuale delle esecuzioni con le prestazioni più basse da terminare in ogni intervallo di valutazione. Valore intero compreso tra 1 e 99.
* `evaluation_interval`: (facoltativo) frequenza per l'applicazione dei criteri
* `delay_evaluation`: (facoltativo) ritarda la prima valutazione dei criteri per un numero specificato di intervalli


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

In questo esempio, i criteri di terminazione anticipata vengono applicati a ogni intervallo, a partire dall'intervallo di valutazione 5. Un'esecuzione termina all'intervallo 5 se le prestazioni all'intervallo 5 si trova nel 20% più basso delle prestazioni di tutte le esecuzioni all'intervallo 5.

### <a name="no-termination-policy-default"></a>Nessun criterio di terminazione (impostazione predefinita)

Se non viene specificato alcun criterio, il servizio di ottimizzazione degli iperparamersi consente l'esecuzione fino al completamento di tutte le esecuzioni di training.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Selezione di un criterio di terminazione anticipata

* Per un criterio conservativo che offre risparmi senza terminare i processi promettenti, prendere in considerazione un criterio di arresto mediano `evaluation_interval` con 1 e `delay_evaluation` 5. Queste sono impostazioni conservative, che possono garantire circa il 25-35% di risparmio senza alcuna perdita sulla metrica primaria (in base ai dati di valutazione).
* Per un risparmio più aggressivo, usare i criteri Disaggregante con un margine di flessibilità consentito minore o Criteri di selezione troncamento con una percentuale di troncamento maggiore.

## <a name="create-and-assign-resources"></a>Creare e assegnare risorse

Controllare il budget delle risorse specificando il numero massimo di esecuzioni di training.

* `max_total_runs`: numero massimo di esecuzioni di training. Deve essere un numero intero compreso tra 1 e 1000.
* `max_duration_minutes`: (facoltativo) Durata massima, in minuti, dell'esperimento di ottimizzazione degli iperparameti. Le esecuzioni dopo questa durata vengono annullate.

>[!NOTE] 
>Se si specificano sia `max_total_runs` che `max_duration_minutes`, l'esperimento di ottimizzazione degli iperparametri viene terminato quando viene raggiunta la prima di queste due soglie.

Specificare inoltre il numero massimo di esecuzioni di training da eseguire simultaneamente durante la ricerca per l'ottimizzazione degli iperparametri.

* `max_concurrent_runs`: (facoltativo) Numero massimo di esecuzioni che possono essere eseguite contemporaneamente. Se non specificato, tutte le esecuzioni vengono avviate in parallelo. Se specificato, deve essere un numero intero compreso tra 1 e 100.

>[!NOTE] 
>Il numero di esecuzioni simultanee è limitato dalle risorse disponibili nella destinazione di calcolo specificata. Assicurarsi che la destinazione di calcolo abbia le risorse disponibili per la concorrenza desiderata.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Questo codice configura l'esperimento di ottimizzazione dell'iperparametro in modo da usare un massimo di 20 esecuzioni totali, eseguendo quattro configurazioni alla volta.

## <a name="configure-hyperparameter-tuning-experiment"></a>Configurare l'esperimento di ottimizzazione degli iperparameti

Per [configurare l'esperimento di ottimizzazione degli iperparameti,](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig) specificare quanto segue:
* Spazio di ricerca degli iperparamersi definito
* Criteri di terminazione anticipata
* Metrica primaria
* Impostazioni di allocazione delle risorse
* ScriptRunConfig `script_run_config`

ScriptRunConfig è lo script di training che verrà eseguito con gli iperparameboli campionati. Definisce le risorse per processo (singolo o a più nodi) e la destinazione di calcolo da usare.

> [!NOTE]
>La destinazione di calcolo usata in `script_run_config` deve avere risorse sufficienti per soddisfare il livello di concorrenza. Per altre informazioni su ScriptRunConfig, vedere Configurare [le esecuzioni di training.](how-to-set-up-training-targets.md)

Configurare l'esperimento di ottimizzazione degli iperparametri:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
from azureml.train.hyperdrive import RandomParameterSampling, BanditPolicy, uniform, PrimaryMetricGoal

param_sampling = RandomParameterSampling( {
        'learning_rate': uniform(0.0005, 0.005),
        'momentum': uniform(0.9, 0.99)
    }
)

early_termination_policy = BanditPolicy(slack_factor=0.15, evaluation_interval=1, delay_evaluation=10)

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

imposta `HyperDriveConfig` i parametri passati all'oggetto `ScriptRunConfig script_run_config` . , `script_run_config` a sua volta, passa i parametri allo script di training. Il frammento di codice precedente è tratto dal notebook di esempio [Train, hyperparameter tune e deploy with PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch). In questo esempio verranno `learning_rate` `momentum` ottimizzati i parametri e . L'arresto anticipato delle esecuzioni verrà determinato da un oggetto , che arresta un'esecuzione la cui metrica primaria non rientra in `BanditPolicy` `slack_factor` .Vedere le informazioni di [riferimento sulla classePolicy.](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy) 

Il codice seguente dell'esempio mostra come i valori ottimizzati vengono ricevuti, analizzati e passati alla funzione dello script di `fine_tune_model` training:

```python
# from pytorch_train.py
def main():
    print("Torch version:", torch.__version__)

    # get command-line arguments
    parser = argparse.ArgumentParser()
    parser.add_argument('--num_epochs', type=int, default=25,
                        help='number of epochs to train')
    parser.add_argument('--output_dir', type=str, help='output directory')
    parser.add_argument('--learning_rate', type=float,
                        default=0.001, help='learning rate')
    parser.add_argument('--momentum', type=float, default=0.9, help='momentum')
    args = parser.parse_args()

    data_dir = download_data()
    print("data directory is: " + data_dir)
    model = fine_tune_model(args.num_epochs, data_dir,
                            args.learning_rate, args.momentum)
    os.makedirs(args.output_dir, exist_ok=True)
    torch.save(model, os.path.join(args.output_dir, 'model.pt'))
```

> [!Important]
> Ogni esecuzione di iperparametrici riavvia il training da zero, inclusa la ricompilazione del modello e _di tutti i caricatori di dati_. È possibile ridurre al minimo questo costo usando una pipeline di Azure Machine Learning o un processo manuale per eseguire la preparazione dei dati il più possibile prima delle esecuzioni di training. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Inviare l'esperimento di ottimizzazione degli iperparameboli

Dopo aver definito la configurazione di ottimizzazione degli iperparamerti, [inviare l'esperimento](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Ottimizzazione degli iperparameboli ad avvio a caldo (facoltativo)

Trovare i valori degli iperparamerati migliori per il modello può essere un processo iterativo. È possibile riutilizzare le informazioni delle cinque esecuzioni precedenti per accelerare l'ottimizzazione degli iperparamerti.

L'avvio a caldo viene gestito in modo diverso a seconda del metodo di campionamento:
- **Campionamento bayesiano:** le versioni di valutazione dell'esecuzione precedente vengono usate come conoscenze precedenti per selezionare nuovi campioni e migliorare la metrica primaria.
- **Campionamento casuale** **o campionamento griglia:** la terminazione anticipata usa le informazioni delle esecuzioni precedenti per determinare le esecuzioni con prestazioni non ottimali. 

Specificare l'elenco di esecuzioni padre da cui si vuole iniziare a caldo.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Se un esperimento di ottimizzazione degli iperparamerti viene annullato, è possibile riprendere le esecuzioni di training dall'ultimo checkpoint. Tuttavia, lo script di training deve gestire la logica del checkpoint.

L'esecuzione del training deve usare la stessa configurazione degli iperparameti e montare le cartelle di output. Lo script di training deve accettare l'argomento , che contiene il checkpoint o i file di modello `resume-from` da cui riprendere l'esecuzione del training. È possibile riprendere singole esecuzioni di training usando il frammento di codice seguente:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

È possibile configurare l'esperimento di ottimizzazione degli iperparamerti per l'avvio a caldo da un esperimento precedente o riprendere singole esecuzioni di training usando i parametri facoltativi `resume_from` e `resume_child_runs` nella configurazione:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Visualizzare le esecuzioni di ottimizzazione degli iperparamezzi

È possibile visualizzare le esecuzioni di ottimizzazione degli iperparamezzi nel studio di Azure Machine Learning oppure usare un widget del notebook.

### <a name="studio"></a>Studio

È possibile visualizzare tutte le esecuzioni di ottimizzazione degli iperparame studio di Azure Machine Learning [.](https://ml.azure.com) Per altre informazioni su come visualizzare un esperimento nel portale, vedere [Visualizzare i record di esecuzione in studio.](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal)

- **Grafico delle metriche:** questa visualizzazione tiene traccia delle metriche registrate per ogni esecuzione figlio di hyperdrive per la durata dell'ottimizzazione degli iperparamersi. Ogni riga rappresenta un'esecuzione figlio e ogni punto misura il valore della metrica primaria in corrispondenza dell'iterazione del runtime.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Grafico delle metriche di ottimizzazione degli iperparameti":::

- **Grafico delle coordinate parallele:** questa visualizzazione mostra la correlazione tra le prestazioni della metrica primaria e i singoli valori degli iperparametrici. Il grafico è interattivo tramite il movimento degli assi (clic e trascinamento dell'etichetta dell'asse) e evidenziando i valori su un singolo asse (fare clic e trascinare verticalmente lungo un singolo asse per evidenziare un intervallo di valori desiderati).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Grafico delle coordinate parallele per l'ottimizzazione degli iperparameboli":::

- **Grafico a dispersione bidimensionale:** questa visualizzazione mostra la correlazione tra due singoli iperparametrici qualsiasi insieme al valore della metrica primaria associato.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="Grafico a dispersione bidimensionale per l'ottimizzazione degli iparametrici":::

- **Grafico a dispersione tridimensionale:** questa visualizzazione è uguale a 2D, ma consente tre dimensioni degli iperparametrici di correlazione con il valore della metrica primaria. È anche possibile fare clic e trascinare per riorientare il grafico per visualizzare correlazioni diverse nello spazio 3D.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="Grafico a dispersione tridimensionale per l'ottimizzazione degli iparametrici":::

### <a name="notebook-widget"></a>Widget del notebook

Usare il [widget Notebook](/python/api/azureml-widgets/azureml.widgets.rundetails) per visualizzare lo stato di avanzamento delle esecuzioni di training. Il frammento seguente mostra tutte le esecuzioni di ottimizzazione degli iperparametri in un Jupyter Notebook:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Il codice mostra una tabella con informazioni dettagliate sulle esecuzioni di training per ciascuna configurazione degli iperparametri.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Tabella di ottimizzazione degli iperparameboli":::

È anche possibile visualizzare le prestazioni di ognuna delle esecuzioni nel corso del training.

## <a name="find-the-best-model"></a>Individuare il modello migliore

Una volta completate tutte le esecuzioni di ottimizzazione degli iperparameboli, identificare i valori di configurazione e iperparameboli con le prestazioni migliori:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Notebook di esempio

Fare riferimento ai notebook train-hyperparameter-* in questa cartella:
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Tenere traccia di un esperimento](how-to-log-view-metrics.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
