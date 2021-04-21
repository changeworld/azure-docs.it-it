---
title: Rilevare la deriva dei dati nei set di dati (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare il rilevamento della deriva dei dati in Azure Learning. Creare i monitoraggi dei set di dati (anteprima), monitorare la deriva dei dati e configurare gli avvisi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 95fb2dfeea98b988eaeaea43efc4ea44fd6e33fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770310"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Rilevare la deriva dei dati (anteprima) nei set di dati

Informazioni su come monitorare la deriva dei dati e impostare avvisi quando la deriva è elevata.  

Con Azure Machine Learning di set di dati (anteprima), è possibile:
* **Analizzare la deriva nei dati** per comprendere come cambiano nel tempo.
* **Monitorare i dati del modello** per le differenze tra i set di dati di training e di gestione.  Iniziare [raccogliendo i dati del modello dai modelli distribuiti.](how-to-enable-data-collection.md)
* **Monitorare i nuovi dati per** le differenze tra qualsiasi set di dati di base e di destinazione.
* **Profilare le funzionalità nei dati per** tenere traccia del modo in cui le proprietà statistiche cambiano nel tempo.
* **Configurare avvisi sulla deriva dei dati per** avvisi anticipati su potenziali problemi. 
* **[Creare una nuova versione del set di](how-to-version-track-datasets.md)** dati quando si determina che i dati sono deviati troppo.

Per creare il monitoraggio viene usato un set di dati di [Azure Machine Learning.](how-to-create-register-datasets.md) Il set di dati deve includere una colonna timestamp.

È possibile visualizzare le metriche di deriva dei dati con Python SDK o in studio di Azure Machine Learning.  Altre metriche e informazioni dettagliate sono disponibili tramite la [risorsa applicazione Azure Insights](../azure-monitor/app/app-insights-overview.md) associata all'area Azure Machine Learning lavoro.

> [!IMPORTANT]
> Il rilevamento della deriva dei dati per i set di dati è attualmente disponibile in anteprima pubblica.
> La versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i monitoraggi dei set di dati, è necessario:
* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).
* [Un'Azure Machine Learning di lavoro.](how-to-manage-workspace.md)
* [L Azure Machine Learning SDK per Python installato,](/python/api/overview/azure/ml/install)che include il pacchetto azureml-datasets.
* Dati strutturati (tabulari) con un timestamp specificato nel percorso del file, nel nome file o nella colonna nei dati.

## <a name="what-is-data-drift"></a>Che cos'è la deriva dei dati?

La deriva dei dati è uno dei motivi principali per cui l'accuratezza del modello diminuisce nel tempo.  Per i modelli di Machine Learning, la deriva dei dati è la modifica dei dati di input del modello che determina una riduzione delle prestazioni del modello.  Il monitoraggio della deriva dei dati consente di rilevare questi problemi di prestazioni del modello.

Le cause della deriva dei dati includono:

- Modifiche del processo upstream, ad esempio un sensore sostituito che modifica le unità di misura da pollici a centimetri. 
- Problemi di qualità dei dati, ad esempio un sensore guasto che legge sempre 0.
- Deriva naturale nei dati, ad esempio la temperatura media che cambia con le stagioni.
- Modifica della relazione tra funzionalità o spostamento covariate. 

Azure Machine Learning semplifica il rilevamento della deriva calcolando una singola metrica che astrae la complessità dei set di dati confrontati.  Questi set di dati possono avere centinaia di funzionalità e decine di migliaia di righe. Dopo aver rilevato la deriva, è possibile eseguire il drill-down delle funzionalità che causano la deriva.  Esaminare quindi le metriche a livello di funzionalità per eseguire il debug e isolare la causa radice della deriva.

Questo approccio dall'alto verso il basso semplifica il monitoraggio dei dati anziché le tecniche tradizionali basate su regole. Le tecniche basate su regole, ad esempio l'intervallo di dati consentito o i valori univoci consentiti, possono richiedere molto tempo e possono essere erre di errore.

In Azure Machine Learning si usano i monitoraggi del set di dati per rilevare e avvisare la deriva dei dati.
  
### <a name="dataset-monitors"></a>Monitoraggi set di dati 

Con un monitoraggio del set di dati è possibile:

* Rilevare e avvisare della deriva dei dati sui nuovi dati in un set di dati.
* Analizzare i dati cronologici per la deriva.
* Profilare i nuovi dati nel tempo.

L'algoritmo di deriva dei dati fornisce una misura complessiva delle modifiche nei dati e indica quali funzionalità sono responsabili di ulteriori indagini. I monitoraggi dei set di dati producono una serie di altre metriche profilando nuovi dati nel set di `timeseries` dati. 

Gli avvisi personalizzati possono essere impostati su tutte le metriche generate dal monitoraggio [tramite applicazione Azure Insights.](../azure-monitor/app/app-insights-overview.md) I monitoraggi dei set di dati possono essere usati per rilevare rapidamente i problemi relativi ai dati e ridurre il tempo necessario per eseguire il debug del problema identificando le cause probabili.  

Concettualmente, esistono tre scenari principali per la configurazione dei monitoraggi dei set di dati in Azure Machine Learning.

Scenario | Descrizione
---|---
Monitorare i dati di gestione di un modello per la deriva dai dati di training | I risultati di questo scenario possono essere interpretati come il monitoraggio di un proxy per l'accuratezza del modello, perché l'accuratezza del modello si riduce quando i dati di gestione si allontanano dai dati di training.
Monitorare un set di dati della serie temporale per la deriva da un periodo di tempo precedente. | Questo scenario è più generale e può essere usato per monitorare i set di dati coinvolti nella compilazione del modello a monte o a valle.  Il set di dati di destinazione deve avere una colonna timestamp. Il set di dati di base può essere qualsiasi set di dati tabulare con caratteristiche in comune con il set di dati di destinazione.
Eseguire l'analisi sui dati passati. | Questo scenario può essere usato per comprendere i dati cronologici e informare le decisioni nelle impostazioni per i monitoraggi dei set di dati.

I monitoraggi dei set di dati dipendono dai servizi di Azure seguenti.

|Servizio di Azure  |Descrizione  |
|---------|---------|
| *Set di dati* | Drift usa Machine Learning set di dati per recuperare i dati di training e confrontare i dati per il training del modello.  La generazione del profilo dei dati viene usata per generare alcune delle metriche segnalate, ad esempio min, max, distinct values e distinct values count. |
| *Pipeline e calcolo di Azureml* | Il processo di calcolo della deriva è ospitato nella pipeline azureml.  Il processo viene attivato su richiesta o in base alla pianificazione per l'esecuzione in un calcolo configurato al momento della creazione del monitoraggio della deriva.
| *Application Insights*| Drift emette metriche per Application Insights appartenenti all'area di lavoro di Machine Learning.
| *Archiviazione BLOB di Azure*| Drift genera metriche in formato JSON per l'archiviazione BLOB di Azure.

### <a name="baseline-and-target-datasets"></a>Set di dati di base e di destinazione 

È possibile monitorare i [set di dati di Azure Machine Learning](how-to-create-register-datasets.md) per la deriva dei dati. Quando si crea un monitoraggio del set di dati, si farà riferimento a:
* Set di dati di base: in genere il set di dati di training per un modello.
* Il set di dati di destinazione, in genere i dati di input del modello, viene confrontato nel tempo con il set di dati di base. Questo confronto indica che per il set di dati di destinazione deve essere specificata una colonna timestamp.

Il monitoraggio confrontierà i set di dati di base e di destinazione.

## <a name="create-target-dataset"></a>Creare un set di dati di destinazione

Il set di dati di destinazione richiede il set di tratti specificando la colonna timestamp da una colonna nei dati o da una colonna virtuale derivata dal modello di percorso `timeseries` dei file. Creare il set di dati con un timestamp tramite [Python SDK](#sdk-dataset) [o studio di Azure Machine Learning](#studio-dataset). Per aggiungere un tratto al set di dati, è necessario specificare una colonna che rappresenta un `timeseries` "timestamp". Se i dati sono partizionati in una struttura di cartelle con informazioni sull'ora, ad esempio "{yyyy/MM/dd}", creare una colonna virtuale tramite l'impostazione del modello di percorso e impostarla come "timestamp della partizione" per migliorare l'importanza delle funzionalità delle serie temporali.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

Il [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) metodo della classe definisce la colonna timestamp per il set di [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  dati.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

> [!TIP]
> Per un esempio completo dell'uso del tratto dei set di dati, vedere il notebook di `timeseries` [esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) o la documentazione dell'SDK dei set [di dati](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

# <a name="studio"></a>[Studio](#tab/azure-studio)

<a name="studio-dataset"></a>

Se si crea il set di dati usando studio di Azure Machine Learning, assicurarsi che il percorso dei dati contenga informazioni sul timestamp, includere tutte le sottocartelle con i dati e impostare il formato della partizione.

Nell'esempio seguente vengono presi tutti i dati nella *sottocartella NoaaIsdFlorida/2019* e il formato di partizione specifica anno, mese e giorno del timestamp.

[![Formato partizione](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Nelle impostazioni **dello schema** specificare la colonna timestamp da una colonna virtuale o reale nel set di dati specificato:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Impostare il timestamp":::

Se i dati sono partizionati in base alla data, come nel caso di questo caso, è anche possibile specificare il partition_timestamp.  Ciò consente un'elaborazione più efficiente delle date.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Timestamp della partizione":::

---

## <a name="create-dataset-monitor"></a>Creare il monitoraggio del set di dati

Creare un monitoraggio del set di dati per rilevare e avvisare della deriva dei dati in un nuovo set di dati.  Usare Python [SDK o](#sdk-monitor) [studio di Azure Machine Learning](#studio-monitor).

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> Per informazioni [dettagliate, vedere la documentazione di riferimento](/python/api/azureml-datadrift/azureml.datadrift) di Python SDK sulla deriva dei dati. 

L'esempio seguente illustra come creare un monitoraggio del set di dati usando Python SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

> [!TIP]
> Per un esempio completo di configurazione di un set di dati e del `timeseries` rilevatore della deriva dei dati, vedere il [notebook di esempio](https://aka.ms/datadrift-notebook).


# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Passare alla [home page di Studio.](https://ml.azure.com)
1. Selezionare la **scheda Set di** dati a sinistra. 
1. Selezionare **Monitoraggi set di dati**.
   ![Elenco di monitoraggio](./media/how-to-monitor-datasets/monitor-list.png)

1. Fare clic sul **pulsante +Crea monitoraggio** e continuare la procedura guidata facendo clic su **Avanti.**  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Creazione guidata monitoraggio":::

* **Selezionare il set di dati di destinazione**.  Il set di dati di destinazione è un set di dati tabulare con la colonna timestamp specificata che verrà analizzata per la deriva dei dati. Il set di dati di destinazione deve avere funzionalità in comune con il set di dati di base e deve essere un set di dati a cui vengono aggiunti `timeseries` nuovi dati. I dati cronologici nel set di dati di destinazione possono essere analizzati oppure è possibile monitorare nuovi dati.

* **Selezionare il set di dati di base.**  Selezionare il set di dati tabulare da usare come baseline per il confronto del set di dati di destinazione nel tempo.  Il set di dati di base deve avere funzionalità in comune con il set di dati di destinazione.  Selezionare un intervallo di tempo per usare una sezione del set di dati di destinazione o specificare un set di dati separato da usare come baseline.

* **Monitorare le impostazioni**.  Queste impostazioni sono relative alla pipeline di monitoraggio del set di dati pianificata che verrà creata. 

    | Impostazione | Descrizione | Suggerimenti | Modificabile | 
    | ------- | ----------- | ---- | ------- |
    | Nome | Nome del monitoraggio del set di dati. | | No |
    | Funzionalità | Elenco di funzionalità che verranno analizzate per la deriva dei dati nel tempo. | Impostare su una o più funzionalità di output di un modello per misurare la deriva del concetto. Non includere funzionalità che si spostano naturalmente nel tempo (mese, anno, indice e così via). È possibile eseguire il backfill e il monitoraggio della deriva dei dati esistente dopo aver regolato l'elenco di funzionalità. | Sì | 
    | Destinazione del calcolo | Azure Machine Learning destinazione di calcolo per eseguire i processi di monitoraggio del set di dati. | | Sì | 
    | Abilita | Abilitare o disabilitare la pianificazione nella pipeline di monitoraggio del set di dati | Disabilitare la pianificazione per analizzare i dati cronologici con l'impostazione backfill. Può essere abilitato dopo la creazione del monitoraggio del set di dati. | Sì | 
    | Frequenza | Frequenza che verrà usata per pianificare il processo della pipeline e analizzare i dati cronologici se si esegue un backfill. Le opzioni includono giornaliero, settimanale o mensile. | Ogni esecuzione confronta i dati nel set di dati di destinazione in base alla frequenza: <li>Giornaliero: confrontare il giorno completo più recente nel set di dati di destinazione con la baseline <li>Settimanale: confronta la settimana completa più recente (da lunedì a domenica) nel set di dati di destinazione con la baseline <li>Mensile: confrontare il mese completo più recente nel set di dati di destinazione con la baseline | No | 
    | Latenza | Tempo, in ore, necessario per l'arrivo dei dati nel set di dati. Ad esempio, se sono necessari tre giorni per l'arrivo dei dati nel database SQL incapsulato dal set di dati, impostare la latenza su 72. | Non può essere modificato dopo la creazione del monitoraggio del set di dati | No | 
    | Indirizzi di posta elettronica | Indirizzi di posta elettronica per gli avvisi in base alla violazione della soglia percentuale di deriva dei dati. | I messaggi di posta elettronica vengono inviati Monitoraggio di Azure. | Sì | 
    | Soglia | Soglia percentuale di deriva dei dati per gli avvisi tramite posta elettronica. | Altri avvisi ed eventi possono essere impostati su molte altre metriche nella risorsa Application Insights dell'area di lavoro. | Sì |

Al termine della procedura guidata, il monitoraggio del set di dati risultante verrà visualizzato nell'elenco. Selezionarlo per passare alla pagina dei dettagli del monitoraggio.

---

## <a name="understand-data-drift-results"></a>Comprendere i risultati della deriva dei dati

Questa sezione illustra i risultati del monitoraggio di un set di dati, disponibili nella **pagina** Monitoraggi set di dati in  /   Azure Studio.  In questa pagina è possibile aggiornare le impostazioni e analizzare i dati esistenti per un periodo di tempo specifico.  

Iniziare con le informazioni dettagliate di primo livello sulla grandezza della deriva dei dati e un'evidenziazione delle funzionalità da approfondire.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Panoramica della deriva":::


| Metrica | Descrizione | 
| ------ | ----------- | 
| Grandezza della deriva dei dati | Percentuale di differenza nel tempo tra il set di dati di base e quello di destinazione. Compreso tra 0 e 100, 0 indica set di dati identici e 100 indica che il modello di deriva dei dati Azure Machine Learning è in grado di distinguerli completamente dai due set di dati. Il rumore nella percentuale precisa misurata è previsto a causa delle tecniche di Machine Learning usate per generare questa grandezza. | 
| Principali funzionalità di drifting | Mostra le funzionalità del set di dati che sono state più deviate e contribuiscono maggiormente alla metrica Di magnitudine della deriva. A causa di uno spostamento covariato, la distribuzione sottostante di una funzionalità non deve necessariamente cambiare per avere un'importanza delle funzionalità relativamente elevata. |
| Soglia | Data Drift magnitude beyond the set threshold will trigger alerts(Data Drift magnitude beyond the set threshold will trigger alerts). Questa impostazione può essere configurata nelle impostazioni del monitoraggio. | 

### <a name="drift-magnitude-trend"></a>Tendenza di grandezza della deriva

Vedere le differenze tra il set di dati e il set di dati di destinazione nel periodo di tempo specificato.  Più vicini al 100%, più i due set di dati differiscono.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Tendenza di grandezza della deriva":::

### <a name="drift-magnitude-by-features"></a>Grandezza della deriva in base alle funzionalità

Questa sezione contiene informazioni dettagliate a livello di funzionalità sulla modifica della distribuzione della funzionalità selezionata, nonché altre statistiche, nel tempo.

Anche il set di dati di destinazione viene profilato nel tempo. La distanza statistica tra la distribuzione di base di ogni funzionalità viene confrontata con quella del set di dati di destinazione nel tempo.  Concettualmente, questo è simile alla grandezza della deriva dei dati.  Tuttavia, questa distanza statistica è per una singola funzionalità anziché per tutte le funzionalità. Sono disponibili anche min, max e mean.

Nel riquadro studio di Azure Machine Learning fare clic su una barra nel grafico per visualizzare i dettagli del livello di funzionalità per tale data. Per impostazione predefinita, vengono visualizzati la distribuzione del set di dati di base e la distribuzione dell'esecuzione più recente della stessa funzionalità.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Grandezza della deriva in base alle funzionalità":::

Queste metriche possono essere recuperate anche in Python SDK tramite `get_metrics()` il metodo su un oggetto `DataDriftDetector` .

### <a name="feature-details"></a>Dettagli delle funzionalità

Infine, scorrere verso il basso per visualizzare i dettagli per ogni singola funzionalità.  Usare gli elenchi a discesa sopra il grafico per selezionare la funzionalità e selezionare anche la metrica che si vuole visualizzare.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Grafico delle caratteristiche numeriche e confronto":::

Le metriche nel grafico dipendono dal tipo di funzionalità.

* Caratteristiche numeriche

    | Metrica | Descrizione |  
    | ------ | ----------- |  
    | Distanza di Wasserban | Quantità minima di lavoro per trasformare la distribuzione di base nella distribuzione di destinazione. |
    | Valore medio | Valore medio della funzionalità. |
    | Valore minimo | Valore minimo della funzionalità. |
    | Valore massimo | Valore massimo della funzionalità. |

* Funzionalità categoriche
    
    | Metrica | Descrizione |  
    | ------ | ----------- |  
    | Distanza euclidiana     |  Calcolato per le colonne categoriche. La distanza euclidea viene calcolata su due vettori, generati dalla distribuzione empirica della stessa colonna categorica da due set di dati. 0 indica che non esiste alcuna differenza nelle distribuzioni empiriche.  Più devia da 0, più questa colonna è deviata. Le tendenze possono essere osservate da un tracciato time series di questa metrica e possono essere utili per individuare una funzionalità di deriva.  |
    | Valori univoci | Numero di valori univoci (cardinalità) della funzionalità. |

In questo grafico selezionare una singola data per confrontare la distribuzione delle funzionalità tra la destinazione e questa data per la funzionalità visualizzata. Per le funzionalità numeriche, mostra due distribuzioni di probabilità.  Se la funzionalità è numerica, viene visualizzato un grafico a barre.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Selezionare una data da confrontare con la destinazione":::

## <a name="metrics-alerts-and-events"></a>Metriche, avvisi ed eventi

È possibile eseguire query sulle metriche nella risorsa applicazione Azure [Insights](../azure-monitor/app/app-insights-overview.md) associata all'area di lavoro di Machine Learning. È possibile accedere a tutte le funzionalità di Application Insights incluse le impostazioni per le regole di avviso personalizzate e i gruppi di azioni per attivare un'azione, ad esempio email/SMS/push/voice o funzione di Azure. Per informazioni dettagliate, vedere la documentazione Application Insights completa. 

Per iniziare, passare al portale di Azure [e](https://portal.azure.com) selezionare la pagina Panoramica dell'area **di** lavoro.  La risorsa Application Insights è all'estrema destra:

[![Panoramica del portale di Azure](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selezionare Log (Analisi) in Monitoraggio nel riquadro sinistro:

![Panoramica di Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

Le metriche di monitoraggio del set di dati vengono archiviate come `customMetrics` . È possibile scrivere ed eseguire una query dopo aver impostato un monitoraggio del set di dati per visualizzarle:

[![Query di Log Analytics](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Dopo aver identificato le metriche per configurare le regole di avviso, creare una nuova regola di avviso:

![Nuova regola di avviso](./media/how-to-monitor-datasets/alert-rule.png)

È possibile usare un gruppo di azioni esistente o crearne uno nuovo per definire l'azione da eseguire quando vengono soddisfatte le condizioni del set:

![Nuovo gruppo di azioni](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>Risoluzione dei problemi

Limitazioni e problemi noti per i monitoraggi della deriva dei dati:

* L'intervallo di tempo durante l'analisi dei dati cronologici è limitato a 31 intervalli dell'impostazione di frequenza del monitoraggio. 
* Limitazione di 200 funzionalità, a meno che non venga specificato un elenco di funzionalità (tutte le funzionalità usate).
* Le dimensioni di calcolo devono essere sufficienti per gestire i dati.
* Assicurarsi che il set di dati abbia dati entro la data di inizio e di fine per una determinata esecuzione del monitoraggio.
* I monitoraggi dei set di dati funzionano solo sui set di dati che contengono 50 o più righe.
* Le colonne, o caratteristiche, nel set di dati vengono classificate come categoriche o numeriche in base alle condizioni nella tabella seguente. Se la funzionalità non soddisfa queste condizioni, ad esempio una colonna di tipo stringa con >100 valori univoci, la funzionalità viene eliminata dall'algoritmo di deriva dei dati, ma viene comunque profilata. 

    | Tipo di funzionalità | Tipo di dati | Condizione | Limitazioni | 
    | ------------ | --------- | --------- | ----------- |
    | Categorical | string, bool, int, float | Il numero di valori univoci nella funzionalità è minore di 100 e minore del 5% del numero di righe. | Null viene considerato come la propria categoria. | 
    | Numerico | int, float | I valori nella funzionalità sono di un tipo di dati numerici e non soddisfano la condizione per una funzionalità di categoria. | Funzionalità eliminata se >il 15% dei valori è Null. | 

* Dopo aver creato un monitoraggio della deriva  dei dati, ma non è possibile visualizzare i dati nella pagina Monitoraggi set di studio di Azure Machine Learning, provare a eseguire le operazioni seguenti.

    1. Controllare se è stato selezionato l'intervallo di date giusto nella parte superiore della pagina.  
    1. Nella scheda **Monitoraggi set di dati** selezionare il collegamento dell'esperimento per controllare lo stato di esecuzione.  Questo collegamento si trova all'estrema destra della tabella.
    1. Se l'esecuzione è stata completata correttamente, controllare i log dei driver per vedere quante metriche sono state generate o se sono presenti messaggi di avviso.  Trovare i log dei driver nella **scheda Output e log** dopo aver fatto clic su un esperimento.

* Se la funzione SDK `backfill()` non genera l'output previsto, potrebbe essere dovuto a un problema di autenticazione.  Quando si crea il calcolo da passare a questa funzione, non usare `Run.get_context().experiment.workspace.compute_targets` .  Usare invece [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) come illustrato di seguito per creare il calcolo passato a tale `backfill()` funzione: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* L'agente di raccolta dati del modello può richiedere fino a 10 minuti per l'arrivo dei dati nell'account di archiviazione BLOB, ma in genere meno di 10 minuti. In uno script o notebook attendere 10 minuti per assicurarsi che le celle seguenti siano eseguite.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un [monitoraggio del set di dati, andare](https://ml.azure.com) al studio di Azure Machine Learning o al notebook [Python.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)
* Vedere come configurare la deriva dei dati nei [modelli distribuiti in servizio Azure Kubernetes](./how-to-enable-data-collection.md).
* Configurare i monitoraggi di deriva dei set di dati con [griglia di eventi](how-to-use-event-grid.md).
