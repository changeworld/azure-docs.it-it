---
title: Novità della versione
titleSuffix: Azure Machine Learning
description: Informazioni sugli aggiornamenti più recenti per Azure Machine Learning e gli SDK Python di Machine Learning e di preparazione dei dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 02/18/2021
ms.openlocfilehash: 1de495253dacac5aeab7dcff95f74aeed11782a8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750735"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning sulla versione

Questo articolo illustra le versioni Azure Machine Learning versione.  Per il contenuto di riferimento completo dell'SDK, visitare la pagina di riferimento principale dell'SDK Azure Machine Learning per [**Python.**](/python/api/overview/azure/ml/intro)

__Feed RSS__: è possibile ricevere una notifica quando questa pagina viene aggiornata copiando e incollando l'URL seguente nel lettore di feed: `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes%22&locale=en-us`


## <a name="2021-04-19"></a>2021-04-19

### <a name="azure-machine-learning-sdk-for-python-v1270"></a>Azure Machine Learning SDK per Python v1.27.0
+ **Correzioni di bug e miglioramenti**
  + **azureml-core**
    + Aggiunta della possibilità di eseguire l'override del valore di timeout predefinito per il caricamento dell'artefatto tramite la variabile di ambiente "AZUREML_ARTIFACTS_DEFAULT_TIMEOUT".
    + Correzione di un bug per cui le impostazioni docker nell'oggetto Environment in ScriptRunConfig non vengono rispettate.
    + Consentire il partizionamento di un set di dati durante la copia in una destinazione.
    + Aggiunta di una modalità personalizzata a OutputDatasetConfig per abilitare il passaggio di set di dati creati nelle pipeline tramite una funzione di collegamento. Questi miglioramenti del supporto sono stati apportati per abilitare il partizionamento tabulare per prs.
    + Aggiunta di un nuovo tipo di calcolo KubernetesCompute ad azureml-core.
  + **azureml-pipeline-core**
    + Aggiunta di una modalità personalizzata a OutputDatasetConfig e abilitazione di un utente per passare i set di dati creati nelle pipeline tramite una funzione di collegamento. Le destinazioni dei percorsi file supportano i segnaposto. Questi supportano i miglioramenti apportati per abilitare il partizionamento tabulare per prs.
    + Aggiunta del nuovo tipo di calcolo KubernetesCompute ad azureml-core.
  + **azureml-pipeline-steps**
    + Aggiunta del nuovo tipo di calcolo KubernetesCompute ad azureml-core.
  + **azureml-synapse**
    + Aggiornare l'URL dell'interfaccia utente spark nel widget di azureml synapse
  + **azureml-train-automl-client**
    + Il featurizer STL per l'attività di previsione usa ora un rilevamento della stagionalità più affidabile in base alla frequenza della serie temporale.
  + **azureml-train-core**
    + Correzione del bug per cui le impostazioni di Docker nell'oggetto Environment non vengono rispettate.
    + Aggiunta del nuovo tipo di calcolo KubernetesCompute ad azureml-core.


## <a name="2021-04-05"></a>2021-04-05

### <a name="azure-machine-learning-sdk-for-python-v1260"></a>Azure Machine Learning SDK per Python v1.26.0
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Risolto un problema a causa del quale i modelli Naive sarebbero consigliati nelle esecuzioni AutoMLStep e si verificano errori con funzionalità di ritardo o finestra in sequenza. Questi modelli non saranno consigliati quando vengono impostati i ritardi di destinazione o le dimensioni della finestra in sequenza di destinazione.
    +  Modifica dell'output della console quando si invia un'esecuzione di AutoML per visualizzare un collegamento al portale per l'esecuzione.
  + **azureml-core**
    + Aggiunta della modalità HDFS nella documentazione.
    + Aggiunta del supporto per comprendere le partizioni del set di dati di file in base alla struttura GLOB.
    + Aggiunta del supporto per l'aggiornamento del registro contenitori associato all'area di lavoro di AzureML.
    + Gli attributi Environment deprecati in DockerSection: "enabled", "shared_volume" e "arguments" fanno ora parte di DockerConfiguration in RunConfiguration.
    + Documentazione aggiornata per i cloni dell'interfaccia della riga di comando della pipeline
    + Aggiornamento degli URI del portale per includere il tenant per l'autenticazione
    + Rimozione del nome dell'esperimento dagli URI di esecuzione per evitare reindirizzamenti 
    + Aggiornamento dell'esperimento URO per l'uso dell'ID esperimento.
    + Correzioni di bug per il collegamento del calcolo remoto con l'interfaccia della riga di comando di AzureML.
    + Aggiornamento degli URI del portale per includere il tenant per l'autenticazione.
    + Aggiornamento dell'URI dell'esperimento per l'uso dell'ID esperimento.
  + **azureml-interpret**
    + azureml-interpret aggiornato per l'uso di interpret-community 0.17.0
  + **azureml-opendatasets**
    + Convalida della data di inizio e della data di fine e indicazione dell'errore se non è di tipo datetime.
  + **azureml-parallel-run**
    + [Funzionalità sperimentale] Aggiungere `partition_keys` il parametro a ParallelRunConfig, se specificato, i set di dati di input verranno partizionati in mini batch dalle chiavi specificate. Richiede che tutti i set di dati di input siano partizionati.
  + **azureml-pipeline-steps**
    + Correzione di bug: supporto di path_on_compute durante il passaggio della configurazione del set di dati come download.
    + Depreca RScriptStep a favore dell'uso di CommandStep per l'esecuzione di script R nelle pipeline. 
    + Depreca EstimatorStep a favore dell'uso di CommandStep per l'esecuzione del training di Ml (incluso il training distribuito) nelle pipeline.
  + **azureml-sdk**
    + Aggiornare python_requires a < 3.9 per azureml-sdk
  + **azureml-train-automl-client**
    +  Modifica dell'output della console quando si invia un'esecuzione Di AutoML per visualizzare un collegamento al portale per l'esecuzione.
  + **azureml-train-core**
    + Attributi 'enabled', 'shared_volume' e 'arguments' di DockerSection deprecati a favore dell'uso di DockerConfiguration con ScriptRunConfig.
    +  Usare set di dati aperti di Azure per set di dati MNIST
    + I messaggi di errore di Hyperdrive sono stati aggiornati.


## <a name="2021-03-22"></a>2021-03-22

### <a name="azure-machine-learning-sdk-for-python-v1250"></a>Azure Machine Learning SDK per Python v1.25.0
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    +  Modifica dell'output della console quando si invia un'esecuzione Di AutoML per visualizzare un collegamento al portale per l'esecuzione.
  + **azureml-core**
    + Inizia a supportare l'aggiornamento del registro contenitori per l'area di lavoro nell'SDK e nell'interfaccia della riga di comando
    + Attributi 'enabled', 'shared_volume' e 'arguments' di DockerSection deprecati a favore dell'uso di DockerConfiguration con ScriptRunConfig.
    + Documentazione aggiornata del clone dell'interfaccia della riga di comando della pipeline
    + Aggiornamento degli URI del portale per includere il tenant per l'autenticazione
    + Rimozione del nome dell'esperimento dagli URI di esecuzione per evitare reindirizzamenti
    + Aggiornamento dell'esperimento URO per l'uso dell'ID esperimento.
    + Correzioni di bug per il collegamento del calcolo remoto tramite az cli
    + Aggiornamento degli URI del portale per includere il tenant per l'autenticazione.
    + Aggiunta del supporto per comprendere le partizioni del set di dati di file in base alla struttura GLOB.
  + **azureml-interpret**
    + azureml-interpret aggiornato per l'uso di interpret-community 0.17.0
  + **azureml-opendatasets**
    + Convalida del tipo di data di inizio e data di fine di input e indicazione dell'errore se non è di tipo datetime.
  + **azureml-pipeline-core**
    + Correzione di bug: supporto di path_on_compute durante il passaggio della configurazione del set di dati come download.
  + **azureml-pipeline-steps**
    + Correzione di bug: supporto di path_on_compute durante il passaggio della configurazione del set di dati come download.
    + Deprecazione di RScriptStep a favore dell'uso di CommandStep per l'esecuzione di script R nelle pipeline. 
    + Deprecazione di EstimatorStep a favore dell'uso di CommandStep per l'esecuzione del training di Machine Learning (incluso il training distribuito) nelle pipeline.
  + **azureml-train-automl-runtime**
    +  Modifica dell'output della console quando si invia un'esecuzione di AutoML per visualizzare un collegamento al portale per l'esecuzione.
  + **azureml-train-core**
    + Attributi 'enabled', 'shared_volume' e 'arguments' di DockerSection deprecati a favore dell'uso di DockerConfiguration con ScriptRunConfig.
    + Usare set di dati aperti di Azure per set di dati MNIST
    + I messaggi di errore di Hyperdrive sono stati aggiornati.


## <a name="2021-03-31"></a>2021-03-31
### <a name="azure-machine-learning-studio-notebooks-experience-march-update"></a>Azure Machine Learning notebook di Azure Machine Learning Studio (aggiornamento di marzo)
+ **Nuove funzionalità**
  + Eseguire il rendering di CSV/TSV. Gli utenti potranno eseguire il rendering e il file TSV/CSV in un formato griglia per semplificare l'analisi dei dati. 
  + Autenticazione SSO per l'istanza di calcolo. Gli utenti possono ora autenticare facilmente qualsiasi nuova istanza di calcolo direttamente nell'interfaccia utente di Notebook, semplificando l'autenticazione e l'uso degli SDK di Azure direttamente in AzureML. 
  + Metriche dell'istanza di calcolo. Gli utenti potranno visualizzare le metriche di calcolo come l'utilizzo della CPU e la memoria tramite terminale.
  + Dettagli file. Gli utenti possono ora visualizzare i dettagli del file, tra cui l'ora dell'ultima modifica e le dimensioni del file, facendo clic sui 3 punti accanto a un file.

+ **Correzioni di bug e miglioramenti**
  + Tempi di caricamento delle pagine migliorati.
  + Prestazioni migliorate.
  + Maggiore velocità e affidabilità del kernel.
  + Ottenere proprietà immobiliari verticali spostando in modo permanente il riquadro dei file notebook verso l'alto
  + I collegamenti sono ora selezionabili nel terminale
  + Prestazioni intellisense migliorate


## <a name="2021-03-08"></a>2021-03-08

### <a name="azure-machine-learning-sdk-for-python-v1240"></a>Azure Machine Learning SDK per Python v1.24.0
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Rimozione delle importazioni compatibili con le versioni precedenti da `azureml.automl.core.shared` . Gli errori di modulo non trovati nello spazio `azureml.automl.core.shared` dei nomi possono essere risolti importando da `azureml.automl.runtime.shared` .
  + **azureml-contrib-automl-dnn-vision**
    + Modello yolo di rilevamento oggetti esposto.
  + **azureml-contrib-dataset**
    + Aggiunta della funzionalità per filtrare i set di dati tabulari in base ai valori di colonna e ai set di dati file in base ai metadati.
  + **azureml-contrib-fairness**
    + Includere lo schema JSON nella ruota per `azureml-contrib-fairness`
  + **azureml-contrib-mir**
    + Se si imposta show_output su True durante la distribuzione dei modelli, la configurazione dell'inferenza e la configurazione della distribuzione verranno riprodotte prima di inviare la richiesta al server.
  + **azureml-core**
    + Aggiunta di funzionalità per filtrare i set di dati tabulari in base ai valori di colonna e ai set di dati dei file in base ai metadati.
    + In precedenza, era possibile che gli utenti creavano configurazioni di provisioning per computeTarget che non soddisfavano i requisiti di complessità della password per il campo (ad esempio, devono contenere almeno 3 dei seguenti elementi: 1 lettera minuscola, 1 lettera maiuscola, 1 cifra e 1 carattere speciale del `admin_user_password` set seguente: ``\`~!@#$%^&*()=+_[]{}|;:./'",<>?`` ). Se l'utente crea una configurazione con una password debole ed esegue un processo usando tale configurazione, il processo avrà esito negativo in fase di esecuzione. A questo punto, la chiamata `AmlCompute.provisioning_configuration` a genererà un'eccezione `ComputeTargetException` con un messaggio di errore associato che illustra i requisiti di complessità della password. 
    + Inoltre, in alcuni casi è stato anche possibile specificare una configurazione con un numero negativo di nodi massimi. Non è più possibile eseguire questa operazione. A questo `AmlCompute.provisioning_configuration` punto, genererà `ComputeTargetException` un'eccezione `max_nodes` se l'argomento è un numero intero negativo.
    + Se si imposta show_output su True quando si distribuiscono i modelli, verranno visualizzate la configurazione dell'inferenza e la configurazione della distribuzione.
    + Con l show_output su True quando si attende il completamento della distribuzione del modello, verrà visualizzato lo stato di avanzamento dell'operazione di distribuzione.
    + Consenti directory di configurazione dell'autenticazione AzureML specificata dal cliente tramite una variabile di ambiente: AZUREML_AUTH_CONFIG_DIR
    + In precedenza era possibile creare una configurazione di provisioning con il numero minimo di nodi inferiore al numero massimo di nodi. Il processo verrebbe eseguito ma avrà esito negativo in fase di esecuzione. Questo bug è stato corretto. Se ora si prova a creare una configurazione di provisioning con `min_nodes < max_nodes` l'SDK, verrà generato un evento `ComputeTargetException` .
  + **azureml-interpret**
    + correzione del dashboard di spiegazione che non mostra le priorità delle funzionalità di aggregazione per le spiegazioni progettate in modo sparse
    + utilizzo ottimizzato della memoria di ExplanationClient nel pacchetto azureml-interpret
  + **azureml-train-automl-client**
    +  Correzione show_output=False per restituire il controllo all'utente quando si esegue spark.

## <a name="2021-02-28"></a>2021-02-28
### <a name="azure-machine-learning-studio-notebooks-experience-february-update"></a>Azure Machine Learning'esperienza notebook di Studio (aggiornamento di febbraio)
+ **Nuove funzionalità**
  + [Terminale nativo (GA)](./how-to-access-terminal.md). Gli utenti avranno ora accesso a un terminale integrato e all'operazione Git tramite il terminale integrato.
  + Frammenti di notebook (anteprima). Gli estratti di codice comuni di Azure ML sono ora disponibili a portata di mano. Passare al pannello dei frammenti di codice, accessibile tramite la barra degli strumenti o attivare il menu dei frammenti di codice usando CTRL+BARRA SPAZIATRICE.  
  + [Tasti di scelta rapida](./how-to-run-jupyter-notebooks.md#useful-keyboard-shortcuts). Parità completa con i tasti di scelta rapida disponibili in Jupyter. 
  + Indicare i parametri della cella. Mostra agli utenti quali celle di un notebook sono celle di parametri e possono eseguire notebook con parametri tramite [Papermill](https://github.com/nteract/papermill) nell'istanza di calcolo.
  + Gestione sessioni del terminale e del kernel: gli utenti potranno gestire tutti i kernel e le sessioni del terminale in esecuzione nel calcolo.
  + Pulsante Di condivisione. Gli utenti possono ora condividere qualsiasi file in Esplora file notebook facendo clic con il pulsante destro del mouse sul file e usando il pulsante condividi.


+ **Correzioni di bug e miglioramenti**
  + Tempi di caricamento delle pagine migliorati
  + prestazioni migliorate 
  + Maggiore velocità e affidabilità del kernel
  + Aggiunta della ruota rotante per mostrare lo stato di avanzamento per tutte le operazioni [dell'istanza di calcolo in corso.](./how-to-run-jupyter-notebooks.md#status-indicators)
  + Fare clic con il pulsante destro del mouse Esplora file. Facendo clic con il pulsante destro del mouse su un file si apriranno le operazioni sui file. 


## <a name="2021-02-16"></a>2021-02-16

### <a name="azure-machine-learning-sdk-for-python-v1230"></a>Azure Machine Learning SDK per Python v1.23.0
+ **Correzioni di bug e miglioramenti**
  + **azureml-core**
    + [Funzionalità sperimentale] Aggiunta del supporto per collegare l'area di lavoro di Synapse ad AML come servizio collegato
    + [Funzionalità sperimentale] Aggiunta del supporto per collegare il pool di Synapse Spark ad AML come calcolo
    + [Funzionalità sperimentale] Aggiunta del supporto per l'accesso ai dati basato sull'identità. Gli utenti possono registrare l'archivio dati o i set di dati senza fornire credenziali. In tal caso, per l'autenticazione verrà usato il token AAD o l'identità gestita della destinazione di calcolo. Fare clic [qui](./how-to-identity-based-data-access.md) per altre informazioni.
  + **azureml-pipeline-steps**
    + [Funzionalità sperimentale] Aggiunta del supporto [per SynapseSparkStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.synapsesparkstep)
  + **azureml-synapse**
    + [Funzionalità sperimentale] Aggiunta del supporto di Spark Magic per eseguire una sessione interattiva nel pool di Synapse Spark.
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + In questo aggiornamento è stato aggiunto lo smoothing esponenziale holt alla casella degli strumenti di previsione di AutoML SDK. Data una serie temporale, il modello migliore viene selezionato da AICc (Criterio di informazioni corretto [di Akaike)](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors) e restituito.
    + AutoML genererà ora due file di log anziché uno. Le istruzioni di log verranno generate in una o nell'altra a seconda del processo in cui è stata generata l'istruzione di log.
    + Rimuovere le stime non necessarie nel campione durante il training del modello con convalide incrociate. Questo può ridurre il tempo di training del modello in alcuni casi, in particolare per i modelli di previsione di serie tempo reale.
  + **azureml-contrib-fairness**
    + Aggiungere uno schema JSON per il dashboard Upload del dizionario.
  + **azureml-contrib-interpret**
    + Il file README azureml-contrib-interpret viene aggiornato per riflettere che il pacchetto verrà rimosso nell'aggiornamento successivo dopo essere stato deprecato da ottobre. Usare invece il pacchetto azureml-interpret
  + **azureml-core**
    + In precedenza era possibile creare una configurazione di provisioning con il numero minimo di nodi inferiore al numero massimo di nodi. Questo problema è stato risolto. Se ora si tenta di creare una configurazione di provisioning con `min_nodes < max_nodes` l'SDK, verrà generato un oggetto `ComputeTargetException` .
    +  Correzione del bug in wait_for_completion in AmlCompute che ha causato la restituzione del flusso di controllo da parte della funzione prima del completamento dell'operazione
    + Run.fail() è ora deprecato, usa Run.tag() per contrassegnare l'esecuzione come non riuscita oppure usa Run.cancel() per contrassegnare l'esecuzione come annullata.
    + Mostra il messaggio di errore "Nome ambiente previsto str, {} trovato" quando il nome dell'ambiente specificato non è una stringa.
  + **azureml-train-automl-client**
    + Correzione di un bug che impediva l'annullamento degli esperimenti AutoML Azure Databricks cluster.


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Azure Machine Learning SDK per Python v1.22.0
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Correzione del bug in cui è stata aggiunta una dipendenza pip aggiuntiva al file conda yml per i modelli di visione.
  + **azureml-automl-runtime**
    + Risolto un bug per cui i modelli di previsione classici (ad esempio AutoArima) potevano ricevere dati di training in cui le righe con valori di destinazione imputati non erano presenti. Questo ha violato il contratto dati di questi modelli. * Correzione di vari bug con comportamento di ritardo per occorrenza nell'operatore di ritardo della serie temporale. In precedenza, l'operazione lag-by-occurrence non contrassegnava correttamente tutte le righe imputate e pertanto non generava sempre i valori di ritardo dell'occorrenza corretti. Sono stati risolti anche alcuni problemi di compatibilità tra l'operatore lag e l'operatore della finestra in sequenza con comportamento di ritardo per occorrenza. In precedenza, l'operatore della finestra in sequenza rilasciava alcune righe dai dati di training che avrebbe altrimenti utilizzato.
  + **azureml-core**
    + Aggiunta del supporto per l'autenticazione dei token da parte dei destinatari.
    + Aggiungere `process_count` a [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) per supportare processi PyTorch multi-processo a più nodi.
  + **azureml-pipeline-steps**
    + [CommandStep ora](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep) disponibile a lungo termine e non più sperimentale.
    + [ParallelRunConfig:](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig)aggiungere argomenti allowed_failed_count e allowed_failed_percent per controllare la soglia di errore a livello di mini batch. La soglia di errore ha ora 3 tipi:
       + error_threshold: numero di elementi mini batch non riusciti consentiti. 
       + allowed_failed_count: numero di mini batch non riusciti consentiti. 
       + allowed_failed_percent: percentuale di mini batch non riusciti consentiti. 
       
       Un processo verrà interrotta se ne supera uno qualsiasi. error_threshold necessario per mantenere la compatibilità con le versioni precedenti. Impostare il valore su -1 per ignorarlo.
    + Correzione della gestione degli spazi vuoti nel nome AutoMLStep.
    + ScriptRunConfig è ora supportato da HyperDriveStep
  + **azureml-train-core**
    + Le esecuzioni HyperDrive richiamate da scriptRun verranno ora considerate un'esecuzione figlio.
    + Aggiungere `process_count` a [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) per supportare processi PyTorch multi-processo a più nodi.
  + **azureml-widgets**
    + Aggiungere il widget ParallelRunStepDetails per visualizzare lo stato di un oggetto ParallelRunStep.
    + Consente agli utenti di hyperdrive di visualizzare un asse aggiuntivo nel grafico delle coordinate parallele che mostra il valore della metrica corrispondente a ogni set di iperparametrici per ogni esecuzione figlio.


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Azure Machine Learning'esperienza notebook di Studio (aggiornamento di gennaio)
+ **Nuove funzionalità**
  + Editor Markdown nativo in AzureML. Gli utenti possono ora eseguire il rendering e modificare i file markdown in modo nativo in AzureML Studio.
  + [Pulsante Esegui per script (.py, . R e sh)](./how-to-run-jupyter-notebooks.md#run-a-notebook-or-python-script). Gli utenti possono ora eseguire facilmente script Python, R e Bash in AzureML
  + [Esplora variabili](./how-to-run-jupyter-notebooks.md#explore-variables-in-the-notebook). Esplorare il contenuto di variabili e frame di dati in un pannello popup. Gli utenti possono controllare facilmente il tipo di dati, le dimensioni e il contenuto.
  + [Tabella di contenuto](./how-to-run-jupyter-notebooks.md#navigate-with-a-toc). Passare alle sezioni del notebook, indicate dalle intestazioni Markdown.
  + Esportare il notebook come Latex/HTML/Py. Creare file notebook facili da condividere esportando in LaTex, HTML o py
  + Intellicode. I risultati basati su ML forniscono [un'esperienza di completamento automatico intelligente avanzata.](/visualstudio/intellicode/overview)

+ **Correzioni di bug e miglioramenti**
  + Tempi di caricamento delle pagine migliorati
  + prestazioni migliorate 
  + Maggiore velocità e affidabilità del kernel
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK per Python v1.21.0
+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Correzione del testo della Guida dell'interfaccia della riga di comando quando si usa AmlCompute con UserAssigned Identity
  + **azureml-contrib-automl-dnn-vision**
    + I pulsanti di distribuzione e download diventano visibili per le esecuzioni di Visione di AutoML e i modelli possono essere distribuiti o scaricati in modo analogo ad altre esecuzioni di AutoML. Sono disponibili due nuovi file (scoring_file_v_1_0_0.py e conda_env_v_1_0_0.yml) che contengono uno script per eseguire l'inferenza e un file yml per ricreare l'ambiente Conda. Anche il file 'model.pth' è stato rinominato in modo da usare l'estensione '.pt'.
  + **azureml-core**
    + Supporto msi per azure-cli-ml
    + Supporto delle identità gestite assegnate dall'utente.
    + Con questa modifica, i clienti devono essere in grado di fornire un'identità assegnata dall'utente che può essere usata per recuperare la chiave dall'insieme di credenziali delle chiavi del cliente per la crittografia in pausa.
    +  fix row_count=0 per il profilo di file di dimensioni molto grandi: correzione dell'errore nella conversione doppia per i valori delimitati con spaziatura interna dello spazio vuoto
    + Rimuovere il flag sperimentale per il set di dati di output ga
    + Aggiornare la documentazione su come recuperare una versione specifica di un modello
    + Consenti l'aggiornamento dell'area di lavoro per l'accesso in modalità mista in caso di collegamento privato
    + Correzione per rimuovere la registrazione aggiuntiva nell'archivio dati per la funzionalità di ripresa dell'esecuzione
    + Aggiunta del supporto dell'interfaccia della riga di comando/SDK per l'aggiornamento dell'identità assegnata dall'utente primario dell'area di lavoro
  + **azureml-interpret**
    + aggiornamento di azureml-interpret per interpret-community 0.16.0
    + ottimizzazioni della memoria per il client spiegazione in azureml-interpret
  + **azureml-train-automl-runtime**
    + Streaming abilitato per le esecuzioni ADB
  + **azureml-train-core**
    + Correzione per rimuovere la registrazione aggiuntiva nell'archivio dati per la funzionalità di ripresa dell'esecuzione
  + **azureml-widgets**
    + I clienti non dovrebbero visualizzare le modifiche alla visualizzazione dei dati di esecuzione esistente usando il widget e ora avranno il supporto se usano facoltativamente iperparamerti condizionali.
    + Il widget di esecuzione dell'utente include ora una spiegazione dettagliata del motivo per cui un'esecuzione si trova nello stato in coda.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK per Python v1.20.0
+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + framework_version aggiunto in OptimizationConfig. Verrà usato quando il modello viene registrato con framework MULTI.
  + **azureml-contrib-optimization**
    + framework_version aggiunto in OptimizationConfig. Verrà usato quando il modello viene registrato con framework MULTI.
  + **azureml-pipeline-steps**
    + Introduzione di CommandStep che accetta il comando per l'elaborazione. Il comando può includere eseguibili, comandi della shell, script e così via.
  + **azureml-core**
    + La creazione dell'area di lavoro supporta ora l'identità assegnata dall'utente. Aggiunta del supporto uai da SDK/CLI
    + È stato risolto un problema relativo a service.reload() per raccogliere le modifiche score.py nella distribuzione locale.
    + `run.get_details()` ha un campo aggiuntivo denominato "submittedBy" che visualizza il nome dell'autore per l'esecuzione.
    + Documentazione del metodo Model.register modificata per indicare come registrare il modello dall'esecuzione diretta
    + Risolto IOT-Server problema di gestione delle modifiche dello stato della connessione.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning Notebook di Studio (aggiornamento di dicembre)
+ **Nuove funzionalità**
  + Ricerca del nome file dell'utente. Gli utenti possono ora cercare tutti i file salvati in un'area di lavoro.
  + Supporto markdown side-by-side per cella notebook. In una cella del notebook, gli utenti possono ora avere la possibilità di visualizzare la sintassi markdown e markdown sottoposta a rendering side-by-side.
  + Barra di stato della cella. La barra di stato indica lo stato in cui si trova una cella di codice, se l'esecuzione di una cella ha avuto esito positivo e il tempo necessario per l'esecuzione. 
   
+ **Correzioni di bug e miglioramenti**
  + Tempi di caricamento delle pagine migliorati
  + prestazioni migliorate 
  + Maggiore velocità e affidabilità del kernel

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK per Python v1.19.0
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Aggiunta del supporto sperimentale per i dati di test in AutoMLStep.
    + Aggiunta dell'implementazione principale iniziale della funzionalità di inserimento del set di test.
    + I riferimenti spostati a sklearn.externals.joblib dipendono direttamente da joblib.
    + introdurre un nuovo tipo di attività AutoML di "image-instance-segmentation".
  + **azureml-automl-runtime**
    + Aggiunta dell'implementazione principale iniziale della funzionalità di inserimento del set di test.
    + Quando tutte le stringhe in una colonna di testo hanno una lunghezza di esattamente 1 carattere, l'featurizer word-gram TfIdf non funzionerà perché il tokenizer ignora le stringhe con meno di 2 caratteri. La modifica del codice corrente consentirà a AutoML di gestire questo caso d'uso.
    + introdurre un nuovo tipo di attività AutoML di "image-instance-segmentation".
  + **azureml-contrib-automl-dnn-nlp**
    + Richiesta pull iniziale per il nuovo pacchetto dnn-nlp
  + **azureml-contrib-automl-dnn-vision**
    + introdurre un nuovo tipo di attività AutoML di "image-instance-segmentation".
  + **azureml-contrib-automl-pipeline-steps**
    + Questo nuovo pacchetto è responsabile della creazione dei passaggi necessari per molti modelli di training/inferenza. - Sposta anche il codice di training/inferenza nel pacchetto azureml.train.automl.runtime in modo che eventuali correzioni future siano automaticamente disponibili tramite versioni di ambiente curate.
  + **azureml-contrib-dataset**
    + introduce un nuovo tipo di attività AutoML di "image-instance-segmentation".
  + **azureml-core**
    + Aggiunta dell'implementazione principale iniziale della funzionalità di inserimento del set di test.
    + Correzione degli avvisi xref per la documentazione nel pacchetto azureml-core
    + Correzioni delle stringhe di documentazione per la funzionalità di supporto dei comandi in SDK
    + Aggiunta della proprietà del comando a RunConfiguration. La funzionalità consente agli utenti di eseguire un comando o file eseguibili effettivi nel calcolo tramite AzureML SDK.
    + Gli utenti possono eliminare un esperimento vuoto in base all'ID dell'esperimento.
  + **azureml-dataprep**
    + Aggiunta del supporto del set di dati per Spark compilato con Scala 2.12. Questo aggiunge al supporto 2.11 esistente.
  + **azureml-mlflow**
    + AzureML-MLflow aggiunge protezioni sicure negli script remoti per evitare la terminazione anticipata delle esecuzioni inviate.
  + **azureml-pipeline-core**
    + Correzione di un bug nell'impostazione di una pipeline predefinita per l'endpoint della pipeline creato tramite l'interfaccia utente
  + **azureml-pipeline-steps**
    + Aggiunta del supporto sperimentale per i dati di test in AutoMLStep.
  + **azureml-tensorboard**
    + Correzione degli avvisi xref per la documentazione nel pacchetto azureml-core
  + **azureml-train-automl-client**
    + Aggiunta del supporto sperimentale per i dati di test in AutoMLStep.
    + Aggiunta dell'implementazione principale iniziale della funzionalità di inserimento del set di test.
    + introduce un nuovo tipo di attività AutoML di "image-instance-segmentation".
  + **azureml-train-automl-runtime**
    + Aggiunta dell'implementazione principale iniziale della funzionalità di inserimento del set di test.
    + Correggere il calcolo delle spiegazioni non elaborati per il modello AutoML migliore se il training dei modelli AutoML viene validation_size impostazione.
    + Spostamento dei riferimenti a sklearn.externals.joblib in modo da dipendere direttamente da joblib.
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric() dovrebbe essere completato più velocemente
    + Miglioramento della gestione degli errori in HyperDrive SDK.
    +  Deprecate tutte le classi di stima a favore dell'uso di ScriptRunConfig per configurare le esecuzioni dell'esperimento. Le classi deprecate includono:
        + MMLBase
        + Strumento di stima
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + Deprecato l'uso di Nccl e Gloo come tipi di input validi per le classi Estimator a favore dell'uso di PyTorchConfiguration con ScriptRunConfig.
    + Deprecato l'uso di Mpi come tipo di input valido per le classi Estimator a favore dell'uso di MpiConfiguration con ScriptRunConfig.
    + Aggiunta della proprietà del comando a runconfiguration. La funzionalità consente agli utenti di eseguire un comando o file eseguibili effettivi nel calcolo tramite AzureML SDK.

    +  Deprecate tutte le classi di stima a favore dell'uso di ScriptRunConfig per configurare le esecuzioni dell'esperimento. Le classi deprecate includono: + MMLBaseEstimator + Estimator + PyTorch + TensorFlow + Chainer + SKLearn
    + Deprecato l'uso di Nccl e Gloo come tipo di input valido per le classi Estimator a favore dell'uso di PyTorchConfiguration con ScriptRunConfig. 
    + Deprecato l'uso di Mpi come tipo di input valido per le classi Estimator a favore dell'uso di MpiConfiguration con ScriptRunConfig.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning notebook di Azure Machine Learning Studio (aggiornamento di novembre)
+ **Nuove funzionalità**
   + Terminale nativo. Gli utenti avranno ora accesso a un terminale integrato e all'operazione Git tramite il [terminale integrato.](./how-to-access-terminal.md)
  + Duplica cartella 
  + Costing for Compute Drop Down 
  + Pylance di calcolo offline 

+ **Correzioni di bug e miglioramenti**
  + Tempi di caricamento delle pagine migliorati
  + prestazioni migliorate 
  + Maggiore velocità e affidabilità del kernel
  + Caricamento di file di grandi dimensioni. È ora possibile caricare il file >95 MB

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK per Python v1.18.0
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    +  Gestione migliorata della serie temporale breve consentendone il riempimento con rumore gaussiano.
  + **azureml-automl-runtime**
    + Generare ConfigException se una colonna DateTime ha un valore OutOfBoundsDatetime
    + Gestione migliorata della serie temporale breve consentendone il riempimento con rumore gaussiano.
    + Assicurarsi che ogni colonna di testo possa sfruttare la trasformazione di tipo char-gram con l'intervallo di n-gram in base alla lunghezza delle stringhe in tale colonna di testo
    + Spiegazione delle funzionalità non elaborate per la modalità migliore per gli esperimenti AutoML in esecuzione nel calcolo locale dell'utente
  + **azureml-core**
    + Aggiungere il pacchetto: pyjwt per evitare il pull di versioni di rilievo nelle versioni future.
    + La creazione di un esperimento restituirà l'esperimento attivo o l'ultimo esperimento archiviato con lo stesso nome se tale esperimento esiste o un nuovo esperimento.
    + La get_experiment in base al nome restituirà l'esperimento attivo o l'ultimo esperimento archiviato con il nome specificato.
    + Gli utenti non possono rinominare un esperimento durante la riattivazione.
    + Miglioramento del messaggio di errore per includere potenziali correzioni quando un set di dati viene passato in modo non corretto a un esperimento, ad esempio ScriptRunConfig. 
    + Documentazione migliorata per `OutputDatasetConfig.register_on_complete` per includere il comportamento di ciò che accade quando il nome esiste già.
    + Se si specificano i nomi di input e output del set di dati che potrebbero entrare in conflitto con variabili di ambiente comuni, verrà ora generato un avviso
    + Parametro reimpiego `grant_workspace_access` durante la registrazione degli archivi dati. Impostarlo su `True` per accedere ai dati dietro la rete virtuale da Machine Learning Studio.
      [Scopri di più](./how-to-enable-studio-virtual-network.md)
    + L'API del servizio collegato è stata affinata. Invece di fornire l'ID risorsa, nella configurazione sono stati definiti tre parametri sub_id, rg e name.
    + Per consentire ai clienti di risolvere autonomamente i problemi di danneggiamento dei token, abilitare la sincronizzazione dei token dell'area di lavoro come metodo pubblico.
    + Questa modifica consente di usare una stringa vuota come valore per un script_param
  + **azureml-train-automl-client**
    +  Gestione migliorata della serie temporale breve consentendone il riempimento con rumore gaussiano.
  + **azureml-train-automl-runtime**
    + Generare ConfigException se una colonna DateTime ha un valore OutOfBoundsDatetime
    + Aggiunta del supporto per fornire spiegazioni delle funzionalità non elaborati per il modello migliore per gli esperimenti AutoML in esecuzione nel calcolo locale dell'utente
    + Miglioramento della gestione delle serie temporità brevi consentendone il riempimento con rumore gaussiano.
  + **azureml-train-core**
    + Questa modifica consente di usare una stringa vuota come valore per un script_param
  + **azureml-train-restclients-hyperdrive**
    + Il file LEGGIMI è stato modificato per offrire più contesto
  + **azureml-widgets**
    + Aggiunta del supporto di stringhe alla libreria di grafici/coordinate parallele per il widget.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Etichettatura dei dati per la segmentazione dell'istanza di immagine (annotazione poligono) (anteprima)

Il tipo di progetto di segmentazione dell'istanza dell'immagine (annotazioni poligono) nell'etichettatura dei dati è ora disponibile, in modo che gli utenti possano disegnare e annotare con poligoni intorno al contorno degli oggetti nelle immagini. Gli utenti potranno assegnare una classe e un poligono a ogni oggetto di interesse all'interno di un'immagine.

Altre informazioni [sull'etichettatura della segmentazione dell'istanza dell'immagine.](how-to-label-images.md)



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK per Python v1.17.0
+ **nuovi esempi**
  + Un nuovo repository di esempi basato sulla community è disponibile all'indirizzo https://github.com/Azure/azureml-examples
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Risolto un problema a causa del quale get_output generare un errore XGBoostError.
  + **azureml-automl-runtime**
    + Le funzionalità basate su calendario/ora create da AutoML avranno ora il prefisso .
    + Correzione di un errore IndexError durante il training di StackEnsemble per i set di dati di classificazione con un numero elevato di classi e sottocampionamento abilitato.
    + È stato risolto un problema a causa del quale le stime votingRegressor potrebbero non essere accurate dopo la riassetto del modello.
  + **azureml-core**
    + Sono stati aggiunti dettagli aggiuntivi sulla relazione tra la configurazione della distribuzione del servizio Servizio Azure Kubernetes servizio Servizio Azure Kubernetes servizio.
    + Supporto delle etichette client dell'ambiente. L'utente può etichettare Ambienti e fare riferimento a essi in base all'etichetta.
  + **azureml-dataprep**
    + Messaggio di errore migliore quando si usa Spark attualmente non supportato con Scala 2.12.
  + **azureml-explain-model**
    + Il pacchetto azureml-explain-model è ufficialmente deprecato
  + **azureml-mlflow**
    + Risolto un bug in mlflow.projects.run nel back-end azureml in cui lo stato finalizzazione non è stato gestito correttamente.
  + **azureml-pipeline-core**
    + Aggiungere il supporto per creare, elencare e ottenere la pianificazione della pipeline in base a un endpoint della pipeline.
    +  È stata migliorata la documentazione di PipelineData.as_dataset con un esempio di utilizzo non valido: se si usa PipelineData.as_dataset in modo errato, verrà generata un'eccezione ValueException
    + È stato modificato il notebook delle pipeline HyperDriveStep per registrare il modello migliore all'interno di pipelineStep direttamente dopo l'esecuzione di HyperDriveStep.
  + **azureml-pipeline-steps**
    + È stato modificato il notebook delle pipeline HyperDriveStep per registrare il modello migliore all'interno di pipelineStep direttamente dopo l'esecuzione di HyperDriveStep.
  + **azureml-train-automl-client**
    + Risolto un problema a causa del quale get_output generare un errore XGBoostError.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning'esperienza notebook di Studio (aggiornamento di ottobre)
+ **Nuove funzionalità**
  + [Supporto completo della rete virtuale](./how-to-enable-studio-virtual-network.md)
  + [Modalità messa a fuoco](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Salvare i notebook CTRL+S
  + Numeri di riga

+ **Correzioni di bug e miglioramenti**
  + Miglioramento della velocità e dell'affidabilità del kernel
  + Aggiornamenti dell'interfaccia utente di Jupyter Widget

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK per Python v1.16.0
+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + AKSWebservice e AKSEndpoints supportano ora i limiti delle risorse di MEMORIA e CPU a livello di pod. Questi limiti facoltativi possono essere usati impostando e flag nelle chiamate dell'interfaccia `--cpu-cores-limit` della riga di comando `--memory-gb-limit` applicabili
  + **azureml-core**
    + Aggiungere versioni principali delle dipendenze dirette di azureml-core
    + AKSWebservice e AKSEndpoints supportano ora i limiti delle risorse di CPU e memoria a livello di pod. Altre informazioni su [risorse e limiti di Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + Aggiornamento run.log_table per consentire la registrazione di singole righe.
    + Aggiunta del metodo statico `Run.get(workspace, run_id)` per recuperare un'esecuzione solo usando un'area di lavoro 
    + Aggiunta del metodo di istanza `Workspace.get_run(run_id)` per recuperare un'esecuzione all'interno dell'area di lavoro
    + Introduzione alla proprietà del comando nella configurazione di esecuzione che consente agli utenti di inviare il comando anziché script & argomenti.
  + **azureml-interpret**
    + correzione del comportamento is_raw flag del client di spiegazione in azureml-interpret
  + **azureml-sdk**
    + `azureml-sdk` supportano ufficialmente Python 3.8.
  + **azureml-train-core**
    + Aggiunta dell'ambiente curato TensorFlow 2.3
    + Introduzione alla proprietà command nella configurazione di esecuzione che consente agli utenti di inviare il comando anziché script & argomenti.
  + **azureml-widgets**
    + Interfaccia riprogettata per il widget di esecuzione dello script.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK per Python v1.15.0
+ **Correzioni di bug e miglioramenti**
  + **azureml-contrib-interpret**
    + Lo spiegatore LIME è stato spostato da azureml-contrib-interpret a interpret-community package e image explainer rimosso dal pacchetto azureml-contrib-interpret
    + dashboard di visualizzazione rimosso dal pacchetto azureml-contrib-interpret, spiegazione client spostato nel pacchetto azureml-interpret e deprecato nel pacchetto azureml-contrib-interpret e notebook aggiornati per riflettere l'API migliorata
    + correzione delle descrizioni dei pacchetti pypi per azureml-interpret, azureml-explain-model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-contrib-notebook**
    + Aggiungere la dipendenza di nbcovert < 6 in modo che papermill 1.x continui a funzionare.
  + **azureml-core**
    + Aggiunta di parametri al costruttore TensorflowConfiguration e MpiConfiguration per consentire un'inizializzazione più semplificata degli attributi della classe senza richiedere all'utente di impostare ogni singolo attributo. Aggiunta di una classe PyTorchConfiguration per la configurazione di processi PyTorch distribuiti in ScriptRunConfig.
    + Aggiungere la versione di azure-mgmt-resource per correggere l'errore di autenticazione.
    + Supporto di Triton No Code Deploy
    + Le directory di output specificate in Run.start_logging() verranno ora rilevate quando si usa l'esecuzione in scenari interattivi. I file tracciati saranno visibili in ML Studio quando si chiama Run.complete()
    + La codifica dei file può ora essere specificata durante la creazione del set di `Dataset.Tabular.from_delimited_files` dati con e passando `Dataset.Tabular.from_json_lines_files` `encoding` l'argomento . Le codifiche supportate sono 'utf8', 'iso88591', 'latin1', 'ascii', utf16', 'utf32', 'utf8bom' e 'windows1252'.
    + Correzione di bug quando l'oggetto ambiente non viene passato al costruttore ScriptRunConfig.
    + Aggiornamento di Run.cancel() per consentire l'annullamento di un'esecuzione locale da un altro computer.
  + **azureml-dataprep**
    +  Sono stati risolti i problemi di timeout di montaggio del set di dati.
  + **azureml-explain-model**
    + correzione delle descrizioni dei pacchetti pypi per azureml-interpret, azureml-explain-model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-interpret**
    + Dashboard di visualizzazione rimosso dal pacchetto azureml-contrib-interpret, il client di spiegazione spostato nel pacchetto azureml-interpret e deprecato nel pacchetto azureml-contrib-interpret e nei notebook aggiornati per riflettere l'API migliorata
    + Pacchetto azureml-interpret aggiornato in modo da dipendere da interpret-community 0.15.0
    + correzione delle descrizioni dei pacchetti pypi per azureml-interpret, azureml-explain-model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-pipeline-core**
    +  Correzione del problema della pipeline per cui il sistema potrebbe smettere di rispondere quando viene chiamato con `OutputFileDatasetConfig` il parametro impostato su un nome di set di dati `register_on_complete` `name` preesiste.
  + **azureml-pipeline-steps**
    + Rimozione dei notebook di Databricks non obsoleti.
  + **azureml-tensorboard**
    + correzione delle descrizioni dei pacchetti pypi per azureml-interpret, azureml-explain-model, azureml-contrib-interpret e azureml-tensorboard
  + **azureml-train-automl-runtime**
    + Dashboard di visualizzazione rimosso dal pacchetto azureml-contrib-interpret, il client di spiegazione spostato nel pacchetto azureml-interpret e deprecato nel pacchetto azureml-contrib-interpret e nei notebook aggiornati per riflettere l'API migliorata
  + **azureml-widgets**
    + Dashboard di visualizzazione rimosso dal pacchetto azureml-contrib-interpret, il client di spiegazione spostato nel pacchetto azureml-interpret e deprecato nel pacchetto azureml-contrib-interpret e nei notebook aggiornati per riflettere l'API migliorata

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK per Python v1.14.0
+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + La profilatura griglia è stata rimossa dall'SDK e non è più supportata.
  + **azureml-accel-models**
    + Il pacchetto azureml-accel-models supporta ora Tensorflow 2.x
  + **azureml-automl-core**
    + Aggiunta della gestione degli errori get_output per i casi in cui le versioni locali di pandas/sklearn non corrispondono a quelle usate durante il training
  + **azureml-automl-runtime**
    + Correzione di un bug per cui le iterazioni di AutoArima hanno esito negativo con predictionException e il messaggio"Errore invisibile all'utente durante la stima".
  + **azureml-cli-common**
    + La profilatura griglia è stata rimossa dall'SDK e non è più supportata.
  + **azureml-contrib-server**
    + Aggiornare la descrizione del pacchetto per la pagina di panoramica di pypi.
  + **azureml-core**
    + La profilatura griglia è stata rimossa dall'SDK e non è più supportata.
    + Ridurre il numero di messaggi di errore quando il recupero dell'area di lavoro ha esito negativo.
    + Non visualizzare avvisi quando il recupero dei metadati ha esito negativo
    + Nuovo passaggio kusto e destinazione di calcolo Kusto.
    + Aggiornare il documento per il parametro sku. Rimuovere lo sku nella funzionalità di aggiornamento dell'area di lavoro nell'interfaccia della riga di comando e nell'SDK.
    + Aggiornare la descrizione del pacchetto per la pagina di panoramica pypi.
    + Documentazione aggiornata per gli ambienti AzureML.
    + Esporre le impostazioni delle risorse gestite del servizio per l'area di lavoro AML nell'SDK.
  + **azureml-dataprep**
    + Abilitare l'autorizzazione di esecuzione per i file per il montaggio del set di dati.
  + **azureml-mlflow**
    + Documentazione ed esempi di notebook di AzureML MLflow aggiornati 
    + Nuovo supporto per i progetti MLflow con back-end AzureML
    + Supporto del registro modelli MLflow
    + Aggiunta del supporto del controllo degli accessi in base al ruolo di Azure AzureML-MLflow operazioni 
    
  + **azureml-pipeline-core**
    + Migliorata la documentazione dei metodi PipelineOutputFileDataset.parse_*.
    + Nuovo passaggio kusto e destinazione di calcolo Kusto.
    + La proprietà Swaggerurl fornita per l'entità pipeline-endpoint tramite tale utente può visualizzare la definizione dello schema per l'endpoint della pipeline pubblicato.
  + **azureml-pipeline-steps**
    + Nuovo passaggio Kusto e destinazione di calcolo Kusto.
  + **azureml-telemetry**
    + Aggiornare la descrizione del pacchetto per la pagina di panoramica pypi.
  + **azureml-train**
    + Aggiornare la descrizione del pacchetto per la pagina di panoramica di pypi.
  + **azureml-train-automl-client**
    + Aggiunta della gestione degli errori get_output per i casi in cui le versioni locali di pandas/sklearn non corrispondono a quelle usate durante il training
  + **azureml-train-core**
    + Aggiornare la descrizione del pacchetto per la pagina di panoramica di pypi.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK per Python v1.13.0
+ **Funzionalità di anteprima**
  + **azureml-core** Con la nuova funzionalità dei set di dati di output, è possibile eseguire il write-back nell'archiviazione cloud, tra cui BLOB, ADLS Gen 1, ADLS Gen 2 e FileShare. È possibile configurare dove eseguire l'output dei dati, come eseguire l'output dei dati (tramite montaggio o caricamento), se registrare i dati di output per un riutilizzo futuro e condividere e passare facilmente i dati intermedi tra i passaggi della pipeline. Ciò consente la riproducibilità, la condivisione, impedisce la duplicazione dei dati e comporta un miglioramento dell'efficienza dei costi e della produttività. [Informazioni su come usare l'SDK](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)
    
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Aggiunta validated_ file di _requirements.txt{platform}per l'aggiunta di tutte le dipendenze pip per AutoML.
    + Questa versione supporta modelli superiori a 4 Gb.
    + Dipendenze AutoML aggiornate: `scikit-learn` (ora 0.22.1), `pandas` (ora 0.25.1), `numpy` (ora 1.18.2).
  + **azureml-automl-runtime**
    + Impostare horovod per la DNN di testo in modo da usare sempre la compressione fp16.
    + Questa versione supporta modelli superiori a 4 Gb.
    + Correzione del problema per cui AutoML non riesce con ImportError: impossibile importare il nome `RollingOriginValidator` .
    + Dipendenze AutoML aggiornate: `scikit-learn` (ora 0.22.1), `pandas` (ora 0.25.1), `numpy` (ora 1.18.2).
  + **azureml-contrib-automl-dnn-forecasting**
    + Dipendenze AutoML aggiornate: `scikit-learn` (ora 0.22.1), `pandas` (ora 0.25.1), `numpy` (ora 1.18.2).
  + **azureml-contrib-fairness**
    + Specificare una breve descrizione per azureml-contrib-fairness.
  + **azureml-contrib-pipeline-steps**
    + Aggiunta del messaggio che indica che il pacchetto è deprecato e che l'utente deve usare azureml-pipeline-steps.
  + **azureml-core**
    + Aggiunta del comando list key per l'area di lavoro.
    + Aggiungere il parametro tags in Workspace SDK e nell'interfaccia della riga di comando.
    + Correzione del bug per cui l'invio di un'esecuzione figlio con Dataset avrà esito negativo a causa di `TypeError: can't pickle _thread.RLock objects` .
    + Aggiunta page_count predefinita/documentazione per Model list().
    + Modificare l'SDK&dell'interfaccia della riga di comando per prendere il parametro adbworkspace e aggiungere l'area di lavoro adb lin/unlink runner.
    + Correzione del bug in Dataset.update che ha causato l'aggiornamento della versione più recente del set di dati e non la versione dell'aggiornamento del set di dati. 
    + Correzione di un bug Dataset.get_by_name che mostra i tag per la versione più recente del set di dati anche quando è stata recuperata una versione precedente specifica.
  + **azureml-interpret**
    + Sono stati aggiunti output di probabilità agli spiegatori di punteggio shap in azureml-interpret in base shap_values_output parametro dello explainer originale.
  + **azureml-pipeline-core**
    + Documentazione `PipelineOutputAbstractDataset.register` migliorata di .
  + **azureml-train-automl-client**
    + Dipendenze AutoML aggiornate: `scikit-learn` (ora 0.22.1), `pandas` (ora 0.25.1), `numpy` (ora 1.18.2).
  + **azureml-train-automl-runtime**
    + Dipendenze AutoML aggiornate: `scikit-learn` (ora 0.22.1), `pandas` (ora 0.25.1), `numpy` (ora 1.18.2).
  + **azureml-train-core**
    + Gli utenti devono ora fornire un valore hyperparameter_sampling arg durante la creazione di hyperdriveconfig. Inoltre, la documentazione per HyperDriveRunConfig è stata modificata per informare gli utenti della deprecazione di HyperDriveRunConfig.
    + Ripristino della versione predefinita di PyTorch alla versione 1.4.
    + Aggiunta di PyTorch 1.6 & immagini Tensorflow 2.2 e ambiente curato.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning'esperienza notebook di Studio (aggiornamento di agosto)
+ **Nuove funzionalità**
  + Nuova pagina di destinazione introduzione 
  
+ **Funzionalità di anteprima**
    + Funzionalità Raccolta nei notebook. Grazie alla [funzionalità Gather,](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) gli utenti possono ora pulire facilmente i notebook con , mentre Gather usa un'analisi automatizzata delle dipendenze del notebook, assicurandosi che il codice essenziale sia mantenuto, ma rimuovendo eventuali parti irrilevanti.

+ **Correzioni di bug e miglioramenti**
  + Miglioramento della velocità e dell'affidabilità
  + Bug in modalità scura risolti
  + Correzione dei bug di scorrimento dell'output
  + Ricerca di esempio ora esegue la ricerca in tutto il contenuto di tutti i file nel Azure Machine Learning di notebook di esempio
  + È ora possibile eseguire le celle R su più righe
  + "I trust contents of this file" (Considera attendibile il contenuto di questo file) viene ora controllato automaticamente dopo la prima volta
  + Finestra di dialogo per la risoluzione dei conflitti migliorata, con la nuova opzione "Crea una copia"
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK per Python v1.12.0

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Aggiungere image_name e image_label parametri a Model.package() per abilitare la ridenominazione dell'immagine del pacchetto compilato.
  + **azureml-automl-core**
    + AutoML genera un nuovo codice di errore da dataprep quando il contenuto viene modificato durante la lettura.
  + **azureml-automl-runtime**
    + Aggiunta di avvisi per l'utente quando i dati contengono valori mancanti ma la funzionalità è disattivata.
    + Correzione degli errori di esecuzione figlio quando i dati contengono nan e la funzione è disattivata.
    + AutoML genera un nuovo codice di errore da dataprep quando il contenuto viene modificato durante la lettura.
    + Normalizzazione aggiornata per la previsione delle metriche in base alla granularità.
    + Miglioramento del calcolo dei quantili di previsione quando le funzionalità di lookback sono disabilitate.
    + Correzione della gestione della matrice di tipo sparse bool durante il calcolo delle spiegazioni dopo AutoML.
  + **azureml-core**
    + Un nuovo metodo `run.get_detailed_status()` mostra ora la spiegazione dettagliata dello stato di esecuzione corrente. Attualmente viene visualizzata solo la spiegazione dello `Queued` stato.
    + Aggiungere image_name e image_label parametri a Model.package() per abilitare la ridenominazione dell'immagine del pacchetto compilato.
    + Nuovo metodo `set_pip_requirements()` per impostare l'intera sezione pip in [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) contemporaneamente.
    + Abilitare la registrazione dell'archivio ADLS Gen2 credenziali senza credenziali.
    + Miglioramento del messaggio di errore durante il tentativo di scaricare o montare un tipo di set di dati non corretto.
    + Aggiornare il notebook di esempio del filtro del set di dati time series con altri esempi di partition_timestamp che fornisce l'ottimizzazione dei filtri.
    + Modificare l'SDK e l'interfaccia della riga di comando per accettare subscriptionId, resourceGroup, workspaceName, peConnectionName come parametri anziché ArmResourceId quando si elimina la connessione dell'endpoint privato.
    + Decorator sperimentale mostra il nome della classe per semplificarne l'identificazione.
    + Le descrizioni per gli asset all'interno di Modelli non vengono più generate automaticamente in base a un'esecuzione.
  + **azureml-datadrift**
    + Contrassegnare create_from_model'API in DataDriftDetector come deprecata.
  + **azureml-dataprep**
    + Miglioramento del messaggio di errore durante il tentativo di scaricare o montare un tipo di set di dati non corretto.
  + **azureml-pipeline-core**
    + Correzione del bug durante la deserializzazione del grafo della pipeline che contiene set di dati registrati.
  + **azureml-pipeline-steps**
    + RScriptStep supporta RSection da azureml.core.environment.
    + È stato rimosso passthru_automl_config parametro `AutoMLStep` dall'API pubblica e convertito in un parametro solo interno.
  + **azureml-train-automl-client**
    + Rimozione dell'ambiente gestito asincrono locale da AutoML. Tutte le esecuzioni locali verranno eseguite nell'ambiente da cui è stata avviata l'esecuzione.
    + Sono stati risolti problemi di snapshot durante l'invio di esecuzioni AutoML senza script forniti dall'utente.
    + Correzione degli errori di esecuzione figlio quando i dati contengono nan e la funzione è disattivata.
  + **azureml-train-automl-runtime**
    + AutoML genera un nuovo codice di errore da dataprep quando il contenuto viene modificato durante la lettura.
    + Correzione dei problemi di snapshot durante l'invio di esecuzioni AutoML senza script forniti dall'utente.
    + Correzione degli errori di esecuzione figlio quando i dati contengono nan e la funzione è disattivata.
  + **azureml-train-core**
    + Aggiunta del supporto per specificare le opzioni pip (ad esempio --extra-index-url) nel file dei requisiti pip passato a un [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator) parametro `pip_requirements_file` through.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK per Python v1.11.0

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Correzione del framework del modello e del framework del modello non passati nell'oggetto run nel percorso di registrazione del modello dell'interfaccia della riga di comando
    + Correzione del comando amlcompute identity show dell'interfaccia della riga di comando per visualizzare l'ID tenant e l'ID entità 
  + **azureml-train-automl-client**
    + Aggiunta get_best_child () a AutoMLRun per recuperare la migliore esecuzione figlio per un'esecuzione AutoML senza scaricare il modello associato.
    + Aggiunta dell'oggetto ModelProxy che consente l'esecuzione della stima o della previsione in un ambiente di training remoto senza scaricare il modello in locale.
    + Le eccezioni non gestite in AutoML ora puntano a una pagina HTTP di problemi noti, in cui sono disponibili altre informazioni sugli errori.
  + **azureml-core**
    + I nomi dei modelli possono avere una lunghezza di 255 caratteri.
    + Environment.get_image_details() il tipo di oggetto restituito è stato modificato. `DockerImageDetails` la classe ha `dict` sostituito , i dettagli dell'immagine sono disponibili dalle nuove proprietà della classe. Le modifiche sono compatibili con le versioni precedenti.
    + Correzione del bug per Environment.from_pip_requirements() per mantenere la struttura delle dipendenze
    + Correzione di un bug a log_list non riesce se nello stesso elenco sono inclusi int e double.
    + Durante l'abilitazione del collegamento privato in un'area di lavoro esistente, si noti che se sono presenti destinazioni di calcolo associate all'area di lavoro, tali destinazioni non funzioneranno se non sono dietro la stessa rete virtuale dell'endpoint privato dell'area di lavoro.
    + Resa `as_named_input` facoltativa quando si usano i set di dati negli esperimenti e vengono aggiunti e a `as_mount` `as_download` `FileDataset` . Il nome di input verrà generato automaticamente se `as_mount` viene `as_download` chiamato o .
  + **azureml-automl-core**
    + Le eccezioni non gestite in AutoML ora puntano a una pagina HTTP di problemi noti, in cui sono disponibili altre informazioni sugli errori.
    + Aggiunta get_best_child () a AutoMLRun per recuperare la migliore esecuzione figlio per un'esecuzione AutoML senza scaricare il modello associato.
    + Aggiunta dell'oggetto ModelProxy che consente l'esecuzione di stime o previsioni in un ambiente di training remoto senza scaricare il modello in locale.
  + **azureml-pipeline-steps**
    + Aggiunta `enable_default_model_output` di flag e a `enable_default_metrics_output` `AutoMLStep` . Questi flag possono essere usati per abilitare o disabilitare gli output predefiniti.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK per Python v1.10.0

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Quando si usa AutoML, se un percorso viene passato all'oggetto AutoMLConfig e non esiste già, verrà creato automaticamente.
    + Gli utenti possono ora specificare una frequenza della serie temporale per le attività di previsione usando il `freq` parametro .
  + **azureml-automl-runtime**
    + Quando si usa AutoML, se un percorso viene passato all'oggetto AutoMLConfig e non esiste già, verrà creato automaticamente.
    + Gli utenti possono ora specificare una frequenza della serie temporale per le attività di previsione usando il `freq` parametro .
    + La previsione AutoML supporta ora la valutazione in sequenza, che si applica al caso d'uso in cui la lunghezza di un set di test o di convalida è più lunga dell'orizzonte di input e che il valore y_pred noto viene usato come contesto di previsione.
  + **azureml-core**
    + Se non sono stati scaricati file dall'archivio dati in un'esecuzione, verranno visualizzati messaggi di avviso.
    + Aggiunta della documentazione `skip_validation` per a `Datastore.register_azure_sql_database method` .
    + Gli utenti devono eseguire l'aggiornamento all'sdk v1.10.0 o versione precedente per creare un endpoint privato approvato automaticamente. Include la risorsa Notebook utilizzabile dietro la rete virtuale.
    + Esporre NotebookInfo nella risposta di ottenere l'area di lavoro.
    + Le modifiche apportate alle chiamate per elencare le destinazioni di calcolo e ottenere la destinazione di calcolo hanno esito positivo in un'esecuzione remota. Le funzioni sdk per ottenere la destinazione di calcolo e elencare le destinazioni di calcolo dell'area di lavoro funzioneranno ora nelle esecuzioni remote.
    + Aggiungere messaggi di deprecazione alle descrizioni delle classi per le classi azureml.core.image.
    + Generare un'eccezione e pulire l'area di lavoro e le risorse dipendenti se la creazione dell'endpoint privato dell'area di lavoro non riesce.
    + Supporto dell'aggiornamento dello SKU dell'area di lavoro nel metodo di aggiornamento dell'area di lavoro.
  + **azureml-datadrift**
    + Aggiornare matplotlib dalla versione 3.0.2 alla 3.2.1 per supportare Python 3.8.
  + **azureml-dataprep**
    + Aggiunta del supporto delle origini dati url Web con `Range` o `Head` request. 
    + Maggiore stabilità per il montaggio e il download dei set di dati dei file.
  + **azureml-train-automl-client**
    + Sono stati risolti i problemi relativi alla rimozione `RequirementParseError` di da setuptools.
    + Usare docker invece di conda per le esecuzioni locali inviate con "compute_target='local'"
    + La durata dell'iterazione stampata nella console è stata corretta. In precedenza, la durata dell'iterazione era talvolta stampata come ora di fine esecuzione meno l'ora di creazione dell'esecuzione. È stato corretto in modo da ottenere un'ora di fine esecuzione uguale meno l'ora di inizio dell'esecuzione.
    + Quando si usa AutoML, se un percorso viene passato all'oggetto AutoMLConfig e non esiste già, verrà creato automaticamente.
    + Gli utenti possono ora specificare una frequenza della serie temporale per le attività di previsione usando il `freq` parametro .
  + **azureml-train-automl-runtime**
    + Output della console migliorato quando le spiegazioni migliori del modello hanno esito negativo.
    + Ridenominazione del parametro di input in "blocked_models" per rimuovere un termine sensibile.
      + Ridenominazione del parametro di input in "allowed_models" per rimuovere un termine sensibile.
    + Gli utenti possono ora specificare una frequenza della serie temporale per le attività di previsione usando il `freq` parametro .

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK per Python v1.9.0

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Sostituito get_model_path() con AZUREML_MODEL_DIR di ambiente nello script di assegnazione dei punteggi generato automaticamente da AutoML. Sono stati aggiunti anche dati di telemetria per tenere traccia degli errori durante init().
    + Rimozione della possibilità di specificare `enable_cache` come parte di AutoMLConfig
    + Correzione di un bug per cui le esecuzioni potrebbero non riuscire con errori del servizio durante esecuzioni di previsione specifiche
    + Miglioramento della gestione degli errori in base a modelli specifici durante `get_output`
    + Correzione della chiamata a fitted_model.fit(X, y) per la classificazione con il trasformatore y
    + Abilitato l'imputer di riempimento in avanti personalizzato per le attività di previsione
    + Verrà usata una nuova classe ForecastingParameters anziché i parametri di previsione in un formato dettato
    + Rilevamento automatico del ritardo di destinazione migliorato
    + Aggiunta della disponibilità limitata di funzionalità distribuite multi-nodo e multi-gpu con BERT
  + **azureml-automl-runtime**
    + Il modello di stagionalità additivo viene ora modellato invece che moltiplicativo.
    + È stato risolto il problema quando i grani brevi, con frequenze diverse da quelle dei grani lunghi, comportano esecuzioni non riuscite.
  + **azureml-contrib-automl-dnn-vision**
    + Raccogliere statistiche di sistema/gpu e medie dei log per il training e l'assegnazione dei punteggi
  + **azureml-contrib-mir**
    + Aggiunta del supporto per il flag enable-app-insights in ManagedInferencing
  + **azureml-core**
    + Un parametro di convalida per queste API consentendo di ignorare la convalida quando l'origine dati non è accessibile dal calcolo corrente.
      + TabularDataset.time_before(end_time, include_boundary=True, validate=True)
      + TabularDataset.time_after(start_time, include_boundary=True, validate=True)
      + TabularDataset.time_recent(time_delta, include_boundary=True, validate=True)
      + TabularDataset.time_between(start_time, end_time, include_boundary=True, validate=True)
    + Aggiunta del supporto del filtro del framework per l'elenco dei modelli e aggiunta dell'esempio NCD AutoML nel notebook
    + Per Datastore.register_azure_blob_container e Datastore.register_azure_file_share (solo le opzioni che supportano il token di firma di accesso condiviso), sono state aggiornate le stringhe di documentazione per il campo in modo da includere i requisiti minimi delle autorizzazioni per i tipici scenari di lettura e `sas_token` scrittura.
    + Deprecazione _with_auth parametro in ws.get_mlflow_tracking_uri()
  + **azureml-mlflow**
    + Aggiunta del supporto per la distribuzione di modelli file:// locali con AzureML-MLflow
    + Deprecazione _with_auth parametro in ws.get_mlflow_tracking_uri()
  + **azureml-opendatasets**
    + I set di dati di rilevamento Covid-19 pubblicati di recente sono ora disponibili con l'SDK
  + **azureml-pipeline-core**
    + Avviso di disconnessione quando "azureml-defaults" non è incluso come parte di pip-dependency
    + Migliorare il rendering delle note.
    + Aggiunta del supporto per le interruzioni di riga tra virgolette durante l'analisi di file delimitati in PipelineOutputFileDataset.
    + La classe PipelineDataset è deprecata. Per altre informazioni, vedere https://aka.ms/dataset-deprecation. Per informazioni su come usare il set di dati con la pipeline, vedere https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Aggiornamenti della documentazione per azureml-pipeline-steps.
    +  Aggiunta del supporto in ParallelRunConfig per consentire agli utenti di definire ambienti inline con il resto della `load_yaml()` configurazione o in un file separato
  + **azureml-train-automl-client**.
    + Rimozione della possibilità di specificare `enable_cache` come parte di AutoMLConfig
  + **azureml-train-automl-runtime**
    + Aggiunta della disponibilità limitata di funzionalità distribuite multi-nodo e multi-gpu con BERT.
    + Aggiunta della gestione degli errori per i pacchetti incompatibili nelle esecuzioni di Machine Learning automatizzato basate su ADB.
  + **azureml-widgets**
    + Aggiornamenti della documentazione per azureml-widgets.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK per Python v1.8.0
  
  + **Funzionalità di anteprima**
    + **azureml-contrib-fairness** Il pacchetto fornisce l'integrazione tra la valutazione dell'equità open source e il pacchetto di mitigazione dell'ingiustizia `azureml-contrib-fairness` [Fairlearn](https://fairlearn.github.io) e studio di Azure Machine Learning. In particolare, il pacchetto consente ai dashboard di valutazione dell'equità del modello di essere caricati come parte di un'esecuzione di AzureML e visualizzati in studio di Azure Machine Learning

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Supporto per il recupero dei log del contenitore init.
    + Aggiunta di nuovi comandi dell'interfaccia della riga di comando per gestire ComputeInstance
  + **azureml-automl-core**
    + Gli utenti possono ora abilitare l'iterazione dell'insieme di stack per le attività della serie temporale con un avviso che potrebbe essere potenzialmente overfit.
    + Aggiunta di un nuovo tipo di eccezione utente che viene generata se il contenuto dell'archivio cache è stato manomesso
  + **azureml-automl-runtime**
    + Lo sweep di bilanciamento delle classi non sarà più abilitato se l'utente disabilita la funzionalità.  
  + **azureml-contrib-itp**
    + È supportato il tipo di calcolo CmAks. È possibile collegare un cluster del servizio AKS personalizzato all'area di lavoro per il processo di training.
  + **azureml-contrib-notebook**
    + Miglioramenti della documentazione per il pacchetto azureml-contrib-notebook.
  + **azureml-contrib-pipeline-steps**
    + Miglioramenti della documentazione per il pacchetto azureml-contrib--pipeline-steps.
  + **azureml-core**
    + Aggiungere set_connection, get_connection, list_connections, delete_connection per il cliente di operare sulla risorsa di connessione dell'area di lavoro
    + Aggiornamenti della documentazione al pacchetto azureml-coore/azureml.exceptions.
    + Aggiornamenti della documentazione per il pacchetto azureml-core.
    + Aggiornamenti della documentazione alla classe ComputeInstance.
    + Miglioramenti della documentazione al pacchetto azureml-core/azureml.core.compute.
    + Miglioramenti della documentazione per le classi correlate al servizio Web in azureml-core.
    + Supportare l'archivio dati selezionato dall'utente per archiviare i dati di profilatura
    + Aggiunta della proprietà expand e page_count per l'API dell'elenco di modelli
    + Correzione del bug per cui la rimozione della proprietà overwrite causa l'esito negativo dell'esecuzione inviata con un errore di deserializzazione.
    + Correzione della struttura di cartelle incoerente durante il download o il montaggio di un fileDataset che fa riferimento a un singolo file.
    + Il caricamento di un set di dati di file parquet to_spark_dataframe ora è più veloce e supporta tutti i tipi di dati SQL Parquet e Spark.
    + Supporto per il recupero dei log del contenitore init.
    + Le esecuzioni AutoML sono ora contrassegnate come esecuzione figlio del passaggio di esecuzione in parallelo.
  + **azureml-datadrift**
    + Miglioramenti della documentazione per il pacchetto azureml-contrib-notebook.
  + **azureml-dataprep**
    + Il caricamento di un set di dati di file parquet to_spark_dataframe ora è più veloce e supporta tutti i tipi di dati SQL Parquet e Spark.
    + Migliore gestione della memoria per il problema OutOfMemory per to_pandas_dataframe.
  + **azureml-interpret**
    + Aggiornamento di azureml-interpret per l'uso della versione interpret-community 0.12.*
  + **azureml-mlflow**
    + Miglioramenti della documentazione per azureml-mlflow.
    + Aggiunge il supporto per il registro dei modelli AML con MLFlow.
  + **azureml-opendatasets**
    + Aggiunta del supporto per Python 3.8
  + **azureml-pipeline-core**
    + Aggiornamento `PipelineDataset` della documentazione di per chiarire che si tratta di una classe interna.
    + ParallelRunStep viene aggiornato per accettare più valori per un solo argomento, ad esempio: "--group_column_names", "Col1", "Col2", "Col3"
    + Rimozione del passthru_automl_config per l'utilizzo intermedio dei dati con AutoMLStep nelle pipeline.
  + **azureml-pipeline-steps**
    + Miglioramenti della documentazione per il pacchetto azureml-pipeline-steps.
    + Rimozione del passthru_automl_config per l'utilizzo intermedio dei dati con AutoMLStep nelle pipeline.
  + **azureml-telemetry**
    + Miglioramenti della documentazione per azureml-telemetry.
  + **azureml-train-automl-client**
    + Correzione di un bug per `experiment.submit()` cui la chiamata a due volte su un oggetto `AutoMLConfig` causava un comportamento diverso.
    + Gli utenti sono ora in grado di abilitare l'iterazione dell'insieme di stack per le attività della serie temporale con un avviso che potrebbe essere potenzialmente overfit.
    + Modifica del comportamento di esecuzione di AutoML per generare UserErrorException se il servizio genera un errore utente
    + Correzione di un bug che ha causato la mancata generazione di log azureml_automl.log durante l'esecuzione di un esperimento AutoML in una destinazione di calcolo remota.
    + Per i set di dati di classificazione con classi sbilanciati, verrà applicato bilanciamento del peso, se lo sweep di funzionalità determina che per i dati sottocampionati, bilanciamento del peso migliora le prestazioni dell'attività di classificazione di una determinata soglia.
    + Le esecuzioni AutoML sono ora contrassegnate come esecuzione figlio del passaggio di esecuzione parallela.
  + **azureml-train-automl-runtime**
    + Modifica del comportamento di esecuzione di AutoML per generare UserErrorException se il servizio genera un errore utente
    + Le esecuzioni AutoML sono ora contrassegnate come esecuzione figlio del passaggio di esecuzione parallela.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK per Python v1.7.0

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Completata la rimozione della profilatura del modello da mir contrib pulendo i comandi dell'interfaccia della riga di comando e le dipendenze del pacchetto, la profilatura del modello è disponibile in core.
    + Aggiorna la versione minima dell'interfaccia della riga di comando di Azure alla versione 2.3.0
  + **azureml-automl-core**
    + Messaggio di eccezione migliore nel passaggio di fit_transform() a causa dei parametri del trasformatore personalizzato.
    + Aggiungere il supporto per più linguaggi per i modelli di trasformatore di Deep Learning, ad esempio BERT in Machine Learning automatizzato.
    + Rimuovere il parametro lag_length deprecato dalla documentazione.
    + La documentazione dei parametri di previsione è stata migliorata. Il lag_length è stato deprecato.
  + **azureml-automl-runtime**
    + Correzione dell'errore generato quando una delle colonne categoriche è vuota in tempo di previsione/test.
    + Correggere gli errori di esecuzione che si verificano quando le funzionalità di lookback sono abilitate e i dati contengono granularità brevi.
    + È stato risolto il problema relativo al messaggio di errore dell'indice temporale duplicato quando i ritardi o le finestre in sequenza erano impostati su "auto".
    + È stato risolto il problema relativo ai modelli Arima e Arima nei set di dati, contenenti le funzionalità di lookback.
    + Aggiunta del supporto per le date precedenti al 21/09/1677 o dopo il 2262-04-11 nelle colonne altre date time nelle attività di previsione. Messaggi di errore migliorati.
    + La documentazione dei parametri di previsione è stata migliorata. Il lag_length è stato deprecato.
    + Miglioramento del messaggio di eccezione al passaggio di fit_transform() a causa di parametri del trasformatore personalizzato.
    + Aggiunta del supporto per più linguaggi per i modelli di trasformatore di Deep Learning, ad esempio BERT nel Machine Learning automatizzato.
    + Le operazioni della cache che generano alcuni errori OS genereranno un errore dell'utente.
    + Aggiunta di controlli per garantire che i dati di training e convalida abbia lo stesso numero e lo stesso set di colonne
    + Correzione del problema con lo script di assegnazione dei punteggi AutoML generato automaticamente quando i dati contengono virgolette
    + Abilitazione delle spiegazioni per AutoML E i modelli complessi che contengono il modello Disastastamento automatico.
    + Un problema recente dei clienti ha rilevato un bug del sito live in cui si registrano i messaggi lungo class-balancing-sweeping anche quando la logica di bilanciamento delle classi non è abilitata correttamente. Rimozione di tali log/messaggi con questa richiesta pull.
  + **azureml-cli-common**
    + Completata la rimozione della profilatura del modello da mir contrib pulendo i comandi dell'interfaccia della riga di comando e le dipendenze dei pacchetti, la profilatura del modello è disponibile in core.
  + **azureml-contrib-reinforcementlearning**
    + Strumento di test di carico
  + **azureml-core**
    + Modifiche alla documentazione in Script_run_config.py
    + Correzione di un bug con la stampa dell'output dell'interfaccia della riga di comando submit-pipeline di esecuzione
    + Miglioramenti della documentazione per azureml-core/azureml.data
    + Correzione del problema di recupero dell'account di archiviazione con il comando hdfs getconf
    + Documentazione register_azure_blob_container e register_azure_file_share migliorata
  + **azureml-datadrift**
    + Miglioramento dell'implementazione per la disabilitazione e l'abilitazione dei monitoraggi della deriva dei set di dati
  + **azureml-interpret**
    + In spiegazione client, rimuovere NaN o Infs prima della serializzazione JSON al caricamento dagli artefatti
    + Eseguire l'aggiornamento alla versione più recente di interpret-community per migliorare gli errori di memoria insufficiente per spiegazioni globali con molte funzionalità e classi
    + Aggiungere true_ys parametro facoltativo per la spiegazione del caricamento per abilitare funzionalità aggiuntive nell'interfaccia utente di Studio
    + Migliorare le download_model_explanations() e list_model_explanations()
    + Piccole modifiche ai notebook, per facilitare il debug
  + **azureml-opendatasets**
    + azureml-opendatasets richiede azureml-dataprep versione 1.4.0 o successiva. Aggiunta di un avviso se viene rilevata una versione inferiore
  + **azureml-pipeline-core**
    + Questa modifica consente all'utente di fornire un runconfig facoltativo a moduleVersion quando chiama il modulo. Publish_python_script.
    + L'account di abilitazione del nodo può essere un parametro della pipeline in ParallelRunStep in azureml.pipeline.steps
  + **azureml-pipeline-steps**
    + Questa modifica consente all'utente di fornire un runconfig facoltativo a moduleVersion quando chiama il modulo. Publish_python_script.
  + **azureml-train-automl-client**
    + Aggiungere il supporto per più linguaggi per i modelli di trasformatore di Deep Learning, ad esempio BERT in Machine Learning automatizzato.
    + Rimuovere il parametro lag_length deprecato dalla documentazione.
    + La documentazione dei parametri di previsione è stata migliorata. Il lag_length è stato deprecato.
  + **azureml-train-automl-runtime**
    + Abilitazione di spiegazioni per AutoML Prophet e modelli complessi che contengono il modello Diamea.
    + Aggiornamenti della documentazione per i pacchetti azureml-train-automl-*.
  + **azureml-train-core**
    + Supporto di TensorFlow versione 2.1 in PyTorch Estimator
    + Miglioramenti al pacchetto azureml-train-core.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK per Python v1.6.0

+ **Nuove funzionalità**
  + **azureml-automl-runtime**
    + La previsione AutoML supporta ora le previsioni dei clienti oltre il max-horizon pre-specificato senza eseguire di nuovo il training del modello. Quando la destinazione della previsione è più lontano nel futuro rispetto all'orizzonte massimo specificato, la funzione forecast() esegue comunque stime dei punti fino alla data successiva usando una modalità di operazione ricorsiva. Per l'illustrazione della nuova funzionalità, vedere la sezione "Forecasting farther than the maximum horizon" del notebook "forecasting-forecast-function" nella [cartella](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)."
  
  + **azureml-pipeline-steps**
    + ParallelRunStep viene ora rilasciato e fa parte del **pacchetto azureml-pipeline-steps.** ParallelRunStep esistente nel **pacchetto azureml-contrib-pipeline-steps** è deprecato. Modifiche dalla versione di anteprima pubblica:
      + Aggiunta del parametro configurabile facoltativo per controllare il numero massimo di chiamate al metodo run per qualsiasi batch specificato. Il `run_max_try` valore predefinito è 3.
      + Nessun pipelineParameters viene più rigenerato automaticamente. I valori configurabili seguenti possono essere impostati come PipelineParameter in modo esplicito.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Il valore predefinito per process_count_per_node viene modificato in 1. L'utente deve ottimizzare questo valore per ottenere prestazioni migliori. La procedura consigliata consiste nell'impostare come numero di nodi GPU o CPU.
      + ParallelRunStep non inserisce alcun pacchetto. L'utente deve includere i pacchetti **azureml-core** e **azureml-dataprep[pandas, fuse]** nella definizione dell'ambiente. Se si usa un'immagine Docker personalizzata con user_managed_dependencies l'utente deve installare conda nell'immagine.
      
+ **Modifiche di rilievo**
  + **azureml-pipeline-steps**
    + Deprecato l'uso di azureml.dprep.Dataflow come tipo di input valido per AutoMLConfig
  + **azureml-train-automl-client**
    + Deprecato l'uso di azureml.dprep.Dataflow come tipo di input valido per AutoMLConfig

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Correzione del bug in cui può essere stampato un avviso durante il quale l'utente ha chiesto `get_output` di eseguire il downgrade del client.
    + Aggiornamento di Mac per affidarsi a cudatoolkit=9.0 perché non è ancora disponibile alla versione 10.
    + Rimozione delle restrizioni sui modelli diastomo e xgboost quando viene eserato il training nel calcolo remoto.
    + Registrazione migliorata in AutoML
    + La gestione degli errori per la funzionalità personalizzata nelle attività di previsione è stata migliorata.
    + Aggiunta della funzionalità per consentire agli utenti di includere funzionalità ritardate per generare previsioni.
    + Aggiornamenti al messaggio di errore per visualizzare correttamente l'errore dell'utente.
    + Supporto per cv_split_column_names da usare con training_data
    + Aggiornare la registrazione del messaggio di eccezione e del traceback.
  + **azureml-automl-runtime**
    + Abilitare guardrails per la previsione delle imputazioni di valori mancanti.
    + Registrazione migliorata in AutoML
    + Aggiunta della gestione granulare degli errori per le eccezioni di preparazione dei dati
    + Rimozione delle restrizioni sui modelli phrophet e xgboost quando viene addestrato nel calcolo remoto.
    + `azureml-train-automl-runtime` e `azureml-automl-runtime` hanno dipendenze aggiornate per `pytorch` , `scipy` e `cudatoolkit` . sono ora supportati `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` e `cudatoolkit==10.1.243` .
    + La gestione degli errori per la funzionalità personalizzata nelle attività di previsione è stata migliorata.
    + È stato migliorato il meccanismo di rilevamento della frequenza dei set di dati di previsione.
    + È stato risolto un problema con il training del modello a tema in alcuni set di dati.
    + Il rilevamento automatico dell'orizzonte massimo durante la previsione è stato migliorato.
    + Aggiunta di funzionalità per consentire agli utenti di includere funzionalità in ritardo per generare previsioni.
    +  Aggiunge funzionalità nella funzione di previsione per consentire di fornire previsioni oltre l'orizzonte con training senza eseguire di nuovo il training del modello di previsione.
    + Supporto per cv_split_column_names da usare con training_data
  + **azureml-contrib-automl-dnn-forecasting**
    + Registrazione migliorata in AutoML
  + **azureml-contrib-mir**
    + Aggiunta del supporto per i servizi Windows in ManagedInferencing
    + Rimuovere i flussi di lavoro MIR vecchi, ad esempio collegare il calcolo MIR, la classe SingleModelMirWebservice - Pulire la profilatura del modello inserita nel pacchetto contrib-mir
  + **azureml-contrib-pipeline-steps**
    + Correzione secondaria per il supporto YAML
    + ParallelRunStep viene rilasciato alla disponibilità generale: azureml.contrib.pipeline.steps ha un avviso di deprecazione e viene spostato in azureml.pipeline.steps
  + **azureml-contrib-reinforcementlearning**
    + Strumento di test di carico RL
    + Lo stimatore RL ha valori predefiniti intelligenti
  + **azureml-core**
    + Rimuovere i flussi di lavoro MIR vecchi, ad esempio collegare il calcolo MIR, la classe SingleModelMirWebservice- Pulire la profilatura del modello inserita nel pacchetto contrib-mir
    + Correzione delle informazioni fornite all'utente in caso di errore di profilatura: ID richiesta incluso e ridefinizione del messaggio in modo che sia più significativo. Aggiunta di un nuovo flusso di lavoro di profilatura ai runner di profilatura
    + Testo di errore migliorato in caso di errori di esecuzione del set di dati.
    + Aggiunta del supporto dell'interfaccia della riga di comando per il collegamento privato dell'area di lavoro.
    + Aggiunta di un parametro facoltativo a che consente di specificare come gestire le `invalid_lines` righe che contengono JSON non `Dataset.Tabular.from_json_lines_files` valido.
    + La creazione basata su esecuzione del calcolo verrà deprecata nella prossima versione. È consigliabile creare un cluster Amlcompute effettivo come destinazione di calcolo permanente e usare il nome del cluster come destinazione di calcolo nella configurazione di esecuzione. Vedere il notebook di esempio qui: aka.ms/amlcomputenb
    + Miglioramento dei messaggi di errore in caso di errori di esecuzione del set di dati.
  + **azureml-dataprep**
    + Avviso per l'aggiornamento della versione pyarrow più esplicito.
    + Miglioramento della gestione degli errori e del messaggio restituito in caso di errore nell'esecuzione del flusso di dati.
  + **azureml-interpret**
    + Aggiornamenti della documentazione al pacchetto azureml-interpret.
    + Correzione della compatibilità di pacchetti e notebook di interpretabilità con l'aggiornamento sklearn più recente
  + **azureml-opendatasets**
    + restituisce None quando non vengono restituiti dati.
    + Migliorare le prestazioni di to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Correzione rapida per ParallelRunStep in cui il caricamento da YAML è stato interrotto
    + ParallelRunStep viene rilasciato alla disponibilità generale: azureml.contrib.pipeline.steps ha un avviso di deprecazione e viene spostato in azureml.pipeline.steps. Le nuove funzionalità includono: 1. Set di dati come PipelineParameter 2. Nuovo parametro run_max_retry 3. Nome del file append_row di output configurabile
  + **azureml-pipeline-steps**
    + Deprecato azureml.dprep.Dataflow come tipo valido per i dati di input.
    + Correzione rapida per ParallelRunStep in cui il caricamento da YAML è stato interrotto
    + ParallelRunStep viene rilasciato alla disponibilità generale: azureml.contrib.pipeline.steps ha un avviso di deprecazione e viene spostato in azureml.pipeline.steps. Le nuove funzionalità includono:
      + Set di dati come PipelineParameter
      + Nuovo parametro run_max_retry
      + Nome del file append_row di output configurabile
  + **azureml-telemetry**
    + Aggiornare la registrazione del messaggio di eccezione e del traceback.
  + **azureml-train-automl-client**
    + Registrazione migliorata in AutoML
    + Aggiornamenti al messaggio di errore per visualizzare correttamente l'errore dell'utente.
    + Supporto per cv_split_column_names da usare con training_data
    + Deprecato azureml.dprep.Dataflow come tipo valido per i dati di input.
    + Aggiornamento di Mac per fare affidamento su cudatoolkit=9.0 perché non è ancora disponibile nella versione 10.
    + Rimozione delle restrizioni per i modelli phrophet e xgboost quando viene training in computer remoti.
    + `azureml-train-automl-runtime` e `azureml-automl-runtime` hanno dipendenze aggiornate per `pytorch` , `scipy` e `cudatoolkit` . sono ora supportati `pytorch==1.4.0` `scipy>=1.0.0,<=1.3.1` , e `cudatoolkit==10.1.243` .
    + Aggiunta di funzionalità per consentire agli utenti di includere funzionalità in ritardo per generare previsioni.
  + **azureml-train-automl-runtime**
    + Registrazione migliorata in AutoML
    + Aggiunta della gestione degli errori con granularità fine per le eccezioni di preparazione dei dati
    + Rimozione delle restrizioni per i modelli phrophet e xgboost quando viene training su risorse di calcolo remote.
    + `azureml-train-automl-runtime` e `azureml-automl-runtime` hanno dipendenze aggiornate per `pytorch` , `scipy` e `cudatoolkit` . sono ora supportati `pytorch==1.4.0` `scipy>=1.0.0,<=1.3.1` , e `cudatoolkit==10.1.243` .
    + Aggiornamenti al messaggio di errore per visualizzare correttamente l'errore dell'utente.
    + Supporto per cv_split_column_names da usare con training_data
  + **azureml-train-core**
    + Aggiunta di un nuovo set di eccezioni specifiche di HyperDrive. azureml.train.hyperdrive genererà ora eccezioni dettagliate.
  + **azureml-widgets**
    + I widget AzureML non vengono visualizzati in JupyterLab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK per Python v1.5.0

+ **Nuove funzionalità**
  + **Funzionalità di anteprima**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning il supporto dell'anteprima per l'apprendimento rinforzo usando il framework [Ray.](https://ray.io) consente il training di agenti di apprendimento di rinforzo tra destinazioni di calcolo GPU e `ReinforcementLearningEstimator` CPU in Azure Machine Learning.

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Corregge un log di avviso lasciato accidentalmente nella richiesta pull precedente. Il log è stato usato per il debug e accidentalmente è stato lasciato indietro.
    + Correzione di bug: informare i client sugli errori parziali durante la profilatura
  + **azureml-automl-core**
    + Velocizzare il modello Diamea/AutoArima nella previsione AutoML abilitando l'adattamento parallelo per la serie temporale quando i set di dati hanno più serie temporizzazione. Per trarre vantaggio da questa nuova funzionalità, è consigliabile impostare "max_cores_per_iteration = -1" (ovvero, usando tutti i core cpu disponibili) in AutoMLConfig.
    + Correzione di KeyError nelle protezioni di stampa nell'interfaccia della console
    + Correzione del messaggio di errore per experimentation_timeout_hours
    + Modelli Tensorflow deprecati per AutoML.
  + **azureml-automl-runtime**
    + Correzione del messaggio di errore per experimentation_timeout_hours
    + Correzione dell'eccezione non classificata durante il tentativo di deserializzazione dall'archivio cache
    + Velocizzare il modello Diamea/AutoArima nella previsione AutoML abilitando l'adattamento parallelo per la serie temporale quando i set di dati hanno più serie temporizzazione.
    + Correzione della previsione con la finestra in sequenza abilitata nei set di dati in cui il set di test/stima non contiene uno dei grani del set di training.
    + Gestione migliorata dei dati mancanti
    + Correzione di un problema relativo a intervalli di stima durante la previsione su set di dati contenenti serie tempormente non allineate nel tempo.
    + Aggiunta di una migliore convalida della forma dei dati per le attività di previsione.
    + Miglioramento del rilevamento della frequenza.
    + È stato creato un messaggio di errore migliore se non è possibile generare le sezioni di convalida incrociata per le attività di previsione.
    + Correzione dell'interfaccia della console per stampare correttamente la protezione dei valori mancanti.
    + Applicazione di controlli del tipo di dati cv_split_indices'input in AutoMLConfig.
  + **azureml-cli-common**
    + Correzione di bug: informazioni sui client in caso di errore parziale durante la profilatura
  + **azureml-contrib-mir**
    + Aggiunge una classe azureml.contrib.mir.RevisionStatus che inoltra informazioni sulla revisione MIR attualmente distribuita e sulla versione più recente specificata dall'utente. Questa classe è inclusa nell'oggetto MirWebservice nell'attributo "deployment_status".
    + Abilita l'aggiornamento nei servizi Web di tipo MirWebservice e nella relativa classe figlio SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Aggiunta del supporto per Ray 0.8.3
    + AmlWindowsCompute supporta solo File di Azure come archiviazione montata
    + Ridenominazione health_check_timeout in health_check_timeout_seconds
    + Correzione di alcune descrizioni di classi/metodi.
  + **azureml-core**
    + È stata abilitata la conversione di BLOB > WASB nei cloud Azure per enti pubblici e Cina.
    + Correzione del bug per consentire ai ruoli lettore di usare i comandi dell'interfaccia della riga di comando az ml run per ottenere informazioni sull'esecuzione
    + Rimozione della registrazione non necessaria durante le esecuzioni remote di Azure ML con set di dati di input.
    + RCranPackage supporta ora il parametro "version" per la versione del pacchetto CRAN.
    + Correzione di bug: informare i client sull'errore parziale durante la profilatura
    + Aggiunta della gestione float in stile europa per azureml-core.
    + Funzionalità di collegamento privato dell'area di lavoro abilitate in Azure Ml Sdk.
    + Quando si crea un oggetto TabularDataset usando , è possibile specificare se i valori vuoti devono essere caricati come Nessuno o come stringa vuota impostando `from_delimited_files` l'argomento booleano `empty_as_string` .
    + Aggiunta della gestione float di tipo Europa per i set di dati.
    + Sono stati migliorati i messaggi di errore relativi a errori di montaggio del set di dati.
  + **azureml-datadrift**
    + La query dei risultati della deriva dei dati dall'SDK ha rilevato un bug che non differenzia le metriche delle funzionalità minima, massima e media, con conseguente duplicazione dei valori. Questo bug è stato risolto antefisando target o baseline ai nomi delle metriche. Prima: min duplicato, max, mean. Dopo: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Migliorare la gestione degli ambienti Python con restrizioni di scrittura quando si assicurano le dipendenze .NET necessarie per il recapito dei dati.
    + Correzione della creazione del flusso di dati nel file con record vuoti iniziali.
    + Sono state aggiunte opzioni di gestione degli errori `to_partition_iterator` simili a `to_pandas_dataframe` .
  + **azureml-interpret**
    + Riduzione dei limiti di lunghezza del percorso di spiegazione per ridurre la probabilità di andare oltre il limite di Windows
    + Correzione di bug per le spiegazioni di tipo sparse create con lo strumento di spiegazione mimica usando un modello di surrogati lineare.
  + **azureml-opendatasets**
    + Correzione del problema delle colonne di MNIST analizzate come stringa, che deve essere int.
  + **azureml-pipeline-core**
    + L'opzione consente di regenerate_outputs quando si usa un modulo incorporato in ModuleStep.
  + **azureml-train-automl-client**
    + Modelli Tensorflow deprecati per AutoML.
    + Correggere gli utenti che consentono di elencare gli algoritmi non supportati in modalità locale
    + Correzioni della documentazione per AutoMLConfig.
    + Applicazione di controlli del tipo di dati cv_split_indices'input in AutoMLConfig.
    + Correzione del problema relativo all'esito negativo delle esecuzioni di AutoML show_output
  + **azureml-train-automl-runtime**
    + Correzione di un bug nelle iterazioni di Ensemble che impediva il corretto avvio del timeout di download del modello.
  + **azureml-train-core**
    + Correzione dell'errore di digitazione nella classe azureml.train.dnn.Nccl.
    + Supporto di PyTorch versione 1.5 in PyTorch Estimator
    + Correzione del problema per cui non è possibile recuperare l'immagine del framework in un'Azure per enti pubblici quando si usano gli strumenti di stima del framework di training

  
## <a name="2020-05-04"></a>2020-05-04
**Nuova esperienza dei notebook**

È ora possibile creare, modificare e condividere notebook e file di Machine Learning direttamente nell'esperienza Web di Studio Azure Machine Learning. È possibile usare tutte le classi e i metodi disponibili in [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) dall'interno di questi notebook Introduzione [qui](./how-to-run-jupyter-notebooks.md)

**Nuove funzionalità introdotte:**

+ Editor migliorato (editor Monaco) usato da VS Code 
+ Miglioramenti dell'interfaccia utente/dell'esperienza utente
+ Barra degli strumenti delle celle
+ Nuova barra degli strumenti del notebook e controlli di calcolo
+ Barra di stato del notebook 
+ Cambio di kernel inline
+ Supporto di R
+ Miglioramenti di accessibilità e localizzazione
+ Riquadro comandi
+ Tasti di scelta rapida aggiuntivi
+ Autosave
+ Prestazioni e affidabilità migliorate

Accedere ai seguenti strumenti di creazione basati sul Web da Studio:
    
| Strumento basato sul Web  |     Descrizione  |
|---|---|
| Notebook di Azure ML Studio   |     Prima creazione in classe per i file notebook e supporto di tutte le operazioni disponibili in Azure ML Python SDK. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK per Python v1.4.0

+ **Nuove funzionalità**
  + I cluster AmlCompute supportano ora la configurazione di un'identità gestita nel cluster al momento del provisioning. È sufficiente specificare se si vuole usare un'identità assegnata dal sistema o un'identità assegnata dall'utente e passare un identityId per quest'ultimo. È quindi possibile configurare le autorizzazioni per accedere a varie risorse, ad esempio Archiviazione o ACR, in modo che l'identità del calcolo venga usata per accedere in modo sicuro ai dati, invece di un approccio basato su token attualmente usato da AmlCompute. Per altre informazioni sui parametri, vedere le informazioni di riferimento sull'SDK.
  

+ **Modifiche di rilievo**
  + I cluster AmlCompute supportano una funzionalità di anteprima per la creazione basata su esecuzione, che si prevede di deprecazione in due settimane. È possibile continuare a creare destinazioni di calcolo persistenti come sempre usando la classe Amlcompute, ma l'approccio specifico di specificare l'identificatore "amlcompute" come destinazione di calcolo nella configurazione di esecuzione non sarà supportato nel prossimo futuro. 

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + Abilitare il supporto per il tipo non modificabile durante il calcolo del numero di valori univoci in una colonna.
  + **azureml-core**
    + Miglioramento della stabilità durante la lettura da Archiviazione BLOB di Azure'uso di un oggetto TabularDataset.
    + Documentazione migliorata per il `grant_workspace_msi` parametro per `Datastore.register_azure_blob_store` .
    + Correzione del bug con `datastore.upload` per supportare `src_dir` l'argomento che termina con `/` o `\` .
    + Aggiunta di un messaggio di errore utilizzabile quando si tenta di caricare in un archivio Archiviazione BLOB di Azure che non dispone di una chiave di accesso o di un token di firma di accesso condiviso.
  + **azureml-interpret**
    + Aggiunta del limite superiore alle dimensioni del file per i dati di visualizzazione nelle spiegazioni caricate.
  + **azureml-train-automl-client**
    + Verifica in modo esplicito label_column_name & weight_column_name i parametri autoMLConfig come di tipo stringa.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep supporta ora il set di dati come parametro della pipeline. L'utente può costruire una pipeline con un set di dati di esempio e può modificare il set di dati di input dello stesso tipo (file o tabulare) per la nuova esecuzione della pipeline.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK per Python v1.3.0

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Aggiunta di altri dati di telemetria per le operazioni di post-training.
    + Velocizza il training ARIMA automatico usando la somma condizionale del training dei quadrati (CSS) per serie di lunghezza superiore a 100. La lunghezza usata viene archiviata come costante ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/nella classe TimeSeriesInternal in /src/azureml-automl-core/azureml/automl/core/shared/constants.py
    + La registrazione utente delle esecuzioni di previsione è stata migliorata. Nel log verranno ora visualizzate altre informazioni sulla fase attualmente in esecuzione
    + Non consentito target_rolling_window_size essere impostato su valori minori di 2
  + **azureml-automl-runtime**
    + Miglioramento del messaggio di errore visualizzato quando vengono trovati timestamp duplicati.
    + Non è consentito target_rolling_window_size essere impostato su valori minori di 2.
    + Correzione dell'errore di imputazione del ritardo. Il problema è stato causato dal numero insufficiente di osservazioni necessarie per scomporre stagionalmente una serie. I dati "destaginati" vengono usati per calcolare una funzione di correzione automatica parziale (PACF) per determinare la lunghezza del ritardo.
    + È stata abilitata la personalizzazione della funzionalità scopo colonna per le attività di previsione in base alla configurazione di esetuazione delle funzionalità. Sono ora supportati valori numerici e categorici come scopo colonna per le attività di previsione.
    + È stata abilitata la personalizzazione dell'eliminazione delle funzionalità delle colonne per le attività di previsione in base alla configurazione di esetuazione delle funzionalità.
    + È stata abilitata la personalizzazione dell'imputazione per le attività di previsione in base alla configurazione delle funzionalità. Sono ora supportate l'imputazione del valore costante per la colonna di destinazione e l'imputazione mediana, most_frequent valore costante per i dati di training.
  + **azureml-contrib-pipeline-steps**
    + Accettare i nomi di calcolo delle stringhe da passare a ParallelRunConfig
  + **azureml-core**
    +  Aggiunta dell'API Environment.clone(new_name) per creare una copia dell'oggetto Environment
    +  Environment.docker.base_dockerfile accetta filepath. Se è possibile risolvere un file, il contenuto verrà letto nella proprietà base_dockerfile'ambiente
    + Reimpostare automaticamente i valori che si escludono a vicenda base_image e base_dockerfile quando l'utente imposta manualmente un valore in Environment.docker
    + Aggiunta user_managed flag in RSection che indica se l'ambiente è gestito dall'utente o da AzureML.
    + Set di dati: correzione dell'errore di download del set di dati se il percorso dati contiene caratteri Unicode.
    + Set di dati: miglioramento del meccanismo di memorizzazione nella cache del montaggio del set di dati per rispettare il requisito minimo di spazio su disco in Azure Machine Learning Compute, che evita di rendere inutilizzabile il nodo e causa l'annullamento del processo.
    + Set di dati: viene aggiunto un indice per la colonna time series quando si accede a un set di dati time series come frame di dati Pandas, che viene usato per velocizzare l'accesso ai dati basati su time series.  In precedenza, all'indice era assegnato lo stesso nome della colonna timestamp, confondendo gli utenti sulla colonna timestamp effettiva e sull'indice. Non viene ora specificato alcun nome specifico per l'indice perché non deve essere usato come colonna. 
    + Set di dati: risolto il problema di autenticazione del set di dati nel cloud sovrano.
    + Set di dati: correzione `Dataset.to_spark_dataframe` dell'errore per i set di dati creati dagli archivi dati di Azure PostgreSQL.
  + **azureml-interpret**
    + Sono stati aggiunti punteggi globali alla visualizzazione se i valori di importanza locale sono di tipo sparse
    + Aggiornamento di azureml-interpret per l'uso di interpret-community 0.9.*
    + È stato risolto un problema di download della spiegazione con dati di valutazione di tipo sparse
    + Aggiunta del supporto del formato sparse dell'oggetto spiegazione in AutoML
  + **azureml-pipeline-core**
    + Supportare ComputeInstance come destinazione di calcolo nelle pipeline
  + **azureml-train-automl-client**
    + Aggiunta di dati di telemetria aggiuntivi per le operazioni di post-training.
    + Correzione della regressione nell'arresto anticipato
    + Deprecato azureml.dprep.Dataflow come tipo valido per i dati di input.
    +  Modifica del timeout predefinito dell'esperimento AutoML a sei giorni.
  + **azureml-train-automl-runtime**
    + Aggiunta di altri dati di telemetria per le operazioni di post-training.
    + aggiunta del supporto end-to-end di AutoML di tipo sparse
  + **azureml-opendatasets**
    + Aggiunta di dati di telemetria aggiuntivi per monitoraggio del servizio.
    + Abilitare la front door per il BLOB per aumentare la stabilità 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK per Python v1.2.0

+ **Modifiche di rilievo**
  + Eliminazione del supporto per Python 2.7

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Aggiunge "--subscription-id" ai comandi `az ml model/computetarget/service` nell'interfaccia della riga di comando
    + Aggiunta del supporto per il passaggio di chiavi gestite dal cliente (CMK) vault_url, key_name e key_version per la distribuzione di ACI
  + **azureml-automl-core** 
    + È stata abilitata l'imputazione personalizzata con valore costante per le attività di previsione dei dati X e Y.
    + Correzione del problema in relativo alla visualizzazione dei messaggi di errore all'utente.    
  + **azureml-automl-runtime**
    + Correzione del problema relativo alla previsione nei set di dati, contenente granularità con una sola riga
    + Riduzione della quantità di memoria richiesta dalle attività di previsione.
    + Aggiunta di messaggi di errore migliori se il formato della colonna time non è corretto.
    + È stata abilitata l'imputazione personalizzata con valore costante per le attività di previsione dei dati X e Y.
  + **azureml-core**
    + Aggiunta del supporto per il caricamento di ServicePrincipal dalle variabili di ambiente: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID e AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + È stato introdotto un nuovo parametro in : per impostazione predefinita ( ), tutte le interruzioni di riga, incluse quelle nei valori di campo tra virgolette, verranno interpretate `support_multi_line` `Dataset.Tabular.from_delimited_files` come `support_multi_line=False` un'interruzione di record. La lettura dei dati in questo modo è più veloce e ottimizzata per l'esecuzione parallela in più core CPU. Tuttavia, può causare la generazione invisibile all'utente di più record con valori di campo non allineati. Deve essere impostato su quando `True` i file delimitati contengono interruzioni di riga tra virgolette.
    + Aggiunta della possibilità di registrare i ADLS Gen2 nell'interfaccia della Azure Machine Learning comando
    + Il parametro 'fine_grain_timestamp' è stato rinominato in 'timestamp' e il parametro 'coarse_grain_timestamp' in 'partition_timestamp' per il metodo with_timestamp_columns() in TabularDataset per riflettere meglio l'utilizzo dei parametri.
    + Aumento della lunghezza massima del nome dell'esperimento a 255.
  + **azureml-interpret**
    + Aggiornamento di azureml-interpret per interpret-community 0.7.*
  + **azureml-sdk**
    + Passaggio alle dipendenze con la versione compatibile Tilde per il supporto dell'applicazione di patch nelle versioni non definitiva e stabili.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK per Python v1.1.5

+ **Deprecazione delle funzionalità**
  + **Python 2.7**
    + Ultima versione per supportare Python 2.7

+ **Modifiche di rilievo**
  + **Versionamento Semantico 2.0.0**
    + A partire dalla versione 1.1 Azure ML Python SDK adotta il controllo delle versioni semantiche 2.0.0. [Per altre informazioni, vedere .](https://semver.org/) Tutte le versioni successive seguiranno il nuovo schema di numerazione e il contratto di controllo delle versioni semantico. 

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Modificare il nome del comando dell'interfaccia della riga di comando dell'endpoint da 'az ml endpoint aks' a 'az ml endpoint real time' per coerenza.
    + Aggiornare le istruzioni di installazione dell'interfaccia della riga di comando per l'interfaccia della riga di comando stabile e sperimentale
    + La profilatura a istanza singola è stata corretta per produrre una raccomandazione ed è stata resa disponibile in Core Sdk.
  + **azureml-automl-core**
    + È stata abilitata l'inferenza in modalità batch (che prende più righe una volta) per i modelli ONNX AutoML
    + Miglioramento del rilevamento della frequenza nei set di dati, mancanza di dati o presenza di punti dati irregolari
    + È stata aggiunta la possibilità di rimuovere i punti dati non conformi alla frequenza dominante.
    + Modifica dell'input del costruttore in modo da ottenere un elenco di opzioni per applicare le opzioni di imputazione per le colonne corrispondenti.
    + La registrazione degli errori è stata migliorata.
  + **azureml-automl-runtime**
    + Correzione del problema con l'errore generato se la granularità non era presente nel set di training nel set di test
    + Rimozione del requisito y_query durante l'assegnazione dei punteggi nel servizio di previsione
    + Correzione del problema relativo alla previsione quando il set di dati contiene granularità brevi con lunghi lacune di tempo.
    + È stato risolto il problema quando l'orizzonte massimo automatico è attivato e la colonna della data contiene date sotto forma di stringhe. Sono stati aggiunti messaggi di errore e conversione corretta per quando la conversione alla data non è possibile
    + Uso di NumPy e SciPy nativi per la serializzazione e la deserializzazione di dati intermedi per FileCacheStore (usato per le esecuzioni autoML locali)
    + Correzione di un bug per cui le esecuzioni figlio non riuscite potevano rimanere bloccate nello stato In esecuzione.
    + Maggiore velocità di esetuazione delle prestazioni.
    + Correzione del controllo della frequenza durante l'assegnazione dei punteggi, ora le attività di previsione non richiedono una rigida equivalenza di frequenza tra training e set di test.
    + Modifica dell'input del costruttore in modo da ottenere un elenco di opzioni per applicare le opzioni di imputazione per le colonne corrispondenti.
    + Correzione degli errori correlati alla selezione del tipo di ritardo.
    + Correzione dell'errore non classificato generato nei set di dati, con granularità con la singola riga
    + Correzione del problema relativo alla lentezza di rilevamento della frequenza.
    + Correzione di un bug nella gestione delle eccezioni AutoML che causava la sostituzione del motivo reale dell'errore di training con attributeError.
  + **azureml-cli-common**
    + La profilatura a istanza singola è stata corretta per produrre una raccomandazione ed è stata resa disponibile in Core Sdk.
  + **azureml-contrib-mir**
    + Aggiunge funzionalità nella classe MirWebservice per recuperare il token di accesso
    + Usare l'autenticazione del token per MirWebservice per impostazione predefinita durante la chiamata a MirWebservice.run(): aggiorna solo se la chiamata ha esito negativo
    + La distribuzione del servizio Web Mir richiede ora sku [Standard_DS2_v2, Standard_F16, Standard_A2_v2] anziché [Ds2v2, A2v2 e F16] rispettivamente.
  + **azureml-contrib-pipeline-steps**
    + Parametro facoltativo side_inputs aggiunto a ParallelRunStep. Questo parametro può essere usato per montare la cartella nel contenitore. I tipi attualmente supportati sono DataReference e PipelineData.
    + I parametri passati in ParallelRunConfig possono essere sovrascritti passando ora i parametri della pipeline. I nuovi parametri della pipeline supportati aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node fanno già parte della versione precedente).
  + **azureml-core**
    + I servizi Web AzureML distribuiti verranno ora abilitati per impostazione predefinita per la `INFO` registrazione. Questo può essere controllato impostando la `AZUREML_LOG_LEVEL` variabile di ambiente nel servizio distribuito.
    + Python Sdk usa il servizio di individuazione per usare l'endpoint 'api' anziché 'pipeline'.
    + Passare alle nuove route in tutte le chiamate SDK.
    + Modifica del routing delle chiamate a ModelManagementService in una nuova struttura unificata.
      + Metodo di aggiornamento dell'area di lavoro reso disponibile pubblicamente.
      + Aggiunta image_build_compute parametro nel metodo di aggiornamento dell'area di lavoro per consentire all'utente di aggiornare il calcolo per la compilazione dell'immagine.
    + Sono stati aggiunti messaggi di deprecazione al flusso di lavoro di profilatura precedente. Correzione dei limiti di cpu e memoria per la profilatura.
    + Aggiunta di RSection come parte dell'ambiente per eseguire i processi R.
    + Aggiunta della convalida a per generare un errore quando l'origine del set di dati non è `Dataset.mount` accessibile o non contiene dati.
    + Aggiunta di come parametro aggiuntivo per l'interfaccia della riga di comando dell'archivio dati per la registrazione del contenitore BLOB di Azure che consentirà di registrare il contenitore BLOB dietro `--grant-workspace-msi-access` una rete virtuale.
    + La profilatura a istanza singola è stata corretta per produrre una raccomandazione ed è stata resa disponibile in Core SDK.
    + Correzione del problema in aks.py _deploy.
    + Convalida l'integrità dei modelli caricati per evitare errori di archiviazione invisibile all'utente.
    + L'utente può ora specificare un valore per la chiave di autenticazione durante la rigenerazione delle chiavi per i servizi Web.
    + Correzione del bug per cui non è possibile usare lettere maiuscole come nome di input del set di dati.
  + **azureml-defaults**
    + `azureml-dataprep` verrà ora installato come parte di `azureml-defaults` . Non è più necessario installare manualmente la preparazione dei dati[fuse] nelle destinazioni di calcolo per montare i set di dati.
  + **azureml-interpret**
    + Aggiornamento di azureml-interpret a interpret-community 0.6.*
    + Aggiornamento di azureml-interpret in modo che dipersi da interpret-community 0.5.0
    + Aggiunta di eccezioni di tipo azureml ad azureml-interpret
    + Correzione della serializzazione DeepScoringExplainer per i modelli Keras
  + **azureml-mlflow**
    + Aggiunta del supporto per i cloud sovrani in azureml.mlflow
  + **azureml-pipeline-core**
    + Il notebook di assegnazione dei punteggi batch della pipeline ora usa ParallelRunStep
    + Correzione di un bug per cui i risultati di PythonScriptStep potevano essere riutilizzati in modo non corretto nonostante la modifica dell'elenco di argomenti
    + Aggiunta della possibilità di impostare il tipo di colonne quando si chiamano i metodi parse_* su `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Spostato `AutoMLStep` l'oggetto nel `azureml-pipeline-steps` pacchetto. Deprecato `AutoMLStep` all'interno di `azureml-train-automl-runtime` .
    + Aggiunta dell'esempio di documentazione per il set di dati come input PythonScriptStep
  + **azureml-tensorboard**
    + aggiornamento di azureml-tensorboard per supportare tensorflow 2.0
    + Visualizzare il numero di porta corretto quando si usa una porta Tensorboard personalizzata in un'istanza di calcolo
  + **azureml-train-automl-client**
    + È stato risolto un problema a causa del quale alcuni pacchetti possono essere installati in versioni non corrette nelle esecuzioni remote.
    + correzione del problema di override di FeaturizationConfig che filtra la configurazione di featurization personalizzata.
  + **azureml-train-automl-runtime**
    + È stato risolto il problema relativo al rilevamento della frequenza nelle esecuzioni remote
    + Spostato `AutoMLStep` l'oggetto nel `azureml-pipeline-steps` pacchetto. Deprecato `AutoMLStep` all'interno di `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Supporto di PyTorch versione 1.4 in PyTorch Estimator
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK per Python v1.1.2rc0 (versione non definitiva)

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + È stata abilitata l'inferenza in modalità Batch (che prende più righe una volta) per i modelli ONNX AutoML
    + Miglioramento del rilevamento della frequenza nei set di dati, senza dati o contenenti punti dati irregolari
    + Aggiunta della possibilità di rimuovere i punti dati non conformi alla frequenza dominante.
  + **azureml-automl-runtime**
    + È stato risolto il problema con l'errore generato se il granulo non era presente nel set di training nel set di test
    + Rimozione del requisito y_query durante l'assegnazione dei punteggi al servizio di previsione
  + **azureml-contrib-mir**
    + Aggiunge funzionalità nella classe MirWebservice per recuperare il token di accesso
  + **azureml-core**
    + I servizi Web AzureML distribuiti verranno ora abilitati per impostazione predefinita per la `INFO` registrazione. Questo può essere controllato impostando la `AZUREML_LOG_LEVEL` variabile di ambiente nel servizio distribuito.
    + Correzione dell'iterazione `Dataset.get_all` in per restituire tutti i set di dati registrati con l'area di lavoro.
    + Miglioramento del messaggio di errore quando un tipo non valido viene passato `path` all'argomento delle API di creazione del set di dati.
    + Python SDK usa il servizio di individuazione per usare l'endpoint 'api' invece di 'pipeline'.
    + Passare alle nuove route in tutte le chiamate SDK
    + Modifica il routing delle chiamate a ModelManagementService in una nuova struttura unificata
      + Metodo di aggiornamento dell'area di lavoro reso disponibile pubblicamente.
      + Aggiunta image_build_compute parametro nel metodo di aggiornamento dell'area di lavoro per consentire all'utente di aggiornare il calcolo per la compilazione dell'immagine
    +  Aggiunta di messaggi di deprecazione al flusso di lavoro di profilatura precedente. Correzione dei limiti di cpu e memoria per la profilatura
  + **azureml-interpret**
    + aggiornare azureml-interpret per interpret-community 0.6.*
  + **azureml-mlflow**
    + Aggiunta del supporto per i cloud sovrani in azureml.mlflow
  + **azureml-pipeline-steps**
    + Spostato `AutoMLStep` nell'oggetto `azureml-pipeline-steps package` . Deprecato `AutoMLStep` all'interno di `azureml-train-automl-runtime` .
  + **azureml-train-automl-client**
    + È stato risolto un problema a causa del quale alcuni pacchetti possono essere installati in versioni non corrette in esecuzioni remote.
  + **azureml-train-automl-runtime**
    + Correzione del problema relativo al rilevamento della frequenza nelle esecuzioni remote
    + Spostato `AutoMLStep` nell'oggetto `azureml-pipeline-steps package` . Deprecato `AutoMLStep` all'interno di `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Spostato `AutoMLStep` nell'oggetto `azureml-pipeline-steps package` . Deprecato `AutoMLStep` all'interno di `azureml-train-automl-runtime` .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK per Python v1.1.1rc0 (versione non definitiva)

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + La profilatura a istanza singola è stata corretta per produrre una raccomandazione ed è stata resa disponibile in Core Sdk.
  + **azureml-automl-core**
    + La registrazione degli errori è stata migliorata.
  + **azureml-automl-runtime**
    + È stato risolto il problema relativo alla previsione quando il set di dati contiene grani brevi con lunghi lacune di tempo.
    + È stato risolto il problema quando l'orizzonte massimo automatico è attivato e la colonna data contiene date sotto forma di stringhe. È stata aggiunta la conversione corretta e un errore ragionevole se la conversione alla data non è possibile
    + Uso di NumPy e SciPy nativi per la serializzazione e la deserializzazione dei dati intermedi per FileCacheStore (usato per le esecuzioni AutoML locali)
    + Correzione di un bug per cui le esecuzioni figlio non riuscite potrebbero rimanere bloccate nello stato In esecuzione.
  + **azureml-cli-common**
    + La profilatura a istanza singola è stata corretta per produrre una raccomandazione ed è stata resa disponibile in Core Sdk.
  + **azureml-core**
    + Aggiunta `--grant-workspace-msi-access` come parametro aggiuntivo per l'interfaccia della riga di comando dell'archivio dati per la registrazione del contenitore BLOB di Azure che consentirà di registrare il contenitore BLOB dietro una rete virtuale
    + La profilatura a istanza singola è stata corretta per produrre una raccomandazione ed è stata resa disponibile in Core Sdk.
    + È stato risolto il problema in aks.py _deploy
    + Convalida l'integrità dei modelli caricati per evitare errori di archiviazione invisibile all'utente.
  + **azureml-interpret**
    + aggiunta di eccezioni di tipo azureml ad azureml-interpret
    + correzione della serializzazione DeepScoringExplainer per i modelli keras
  + **azureml-pipeline-core**
    + Il notebook di assegnazione dei punteggi in batch della pipeline usa ora ParallelRunStep
  + **azureml-pipeline-steps**
    + Spostato `AutoMLStep` l'oggetto nel `azureml-pipeline-steps` pacchetto. Deprecato `AutoMLStep` all'interno di `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + Parametro facoltativo side_inputs aggiunto a ParallelRunStep. Questo parametro può essere usato per montare la cartella nel contenitore. I tipi attualmente supportati sono DataReference e PipelineData.
  + **azureml-tensorboard**
    + aggiornamento di azureml-tensorboard per supportare tensorflow 2.0
  + **azureml-train-automl-client**
    + correzione di un problema di override di FeaturizationConfig che filtra la configurazione di funzioni personalizzate.
  + **azureml-train-automl-runtime**
    + Spostato nel `AutoMLStep` `azureml-pipeline-steps` pacchetto. Deprecato `AutoMLStep` all'interno di `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Supporto di PyTorch versione 1.4 in PyTorch Estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK per Python v1.1.0rc0 (versione non definitiva)

+ **Modifiche di rilievo**
  + **Versionamento Semantico 2.0.0**
    + A partire dalla versione 1.1 Azure ML Python SDK adotta il controllo delle versioni semantico 2.0.0. [Per altre informazioni, vedere](https://semver.org/). Tutte le versioni successive seguiranno il nuovo schema di numerazione e il contratto di controllo delle versioni semantico. 
  
+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + Maggiore velocità di esetuazione delle prestazioni.
    + Correzione del controllo della frequenza durante l'assegnazione dei punteggi. Ora nelle attività di previsione non è necessaria l'equivalenza di frequenza tra training e set di test.
  + **azureml-core**
    + L'utente può ora specificare un valore per la chiave di autenticazione durante la rigenerazione delle chiavi per i servizi Web.
  + **azureml-interpret**
    + Aggiornamento di azureml-interpret in modo che dipersi da interpret-community 0.5.0
  + **azureml-pipeline-core**
    + Correzione di un bug per cui i risultati di PythonScriptStep potevano essere riutilizzati in modo non corretto nonostante la modifica dell'elenco di argomenti
  + **azureml-pipeline-steps**
    + Aggiunta dell'esempio di documentazione per il set di dati come input PythonScriptStep
  + **azureml-contrib-pipeline-steps**
    + I parametri passati in ParallelRunConfig possono essere sovrascritti passando ora i parametri della pipeline. I nuovi parametri della pipeline supportati aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node fanno già parte della versione precedente).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK per Python v1.0.85

+ **Nuove funzionalità**
  + **azureml-core**
    + Ottenere l'utilizzo principale corrente e la limitazione della quota per le risorse AmlCompute in una determinata area di lavoro e sottoscrizione
  
  + **azureml-contrib-pipeline-steps**
    + Consentire all'utente di passare il set di dati tabulare come risultato intermedio dal passaggio precedente a parallelrunstep

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + Rimozione del requisito di y_query colonna nella richiesta al servizio di previsione distribuito. 
    + La "y_query" è stata rimossa dalla sezione di richiesta del servizio Notebook Orange Juice di Dominick.
    + Correzione del bug che impedisce la previsione nei modelli distribuiti, operando su set di dati con colonne di data e ora.
    + Aggiunta del coefficiente di correlazione di Dinno come metrica di classificazione, sia per la classificazione binaria che per la classificazione multiclasse.
  + **azureml-contrib-interpret**
    + Gli spiegatori di testo rimossi da azureml-contrib-interpret come spiegazione del testo sono stati spostati nel repo interpret-text che verrà rilasciato a breve.
  + **azureml-core**
    + Set di dati: gli utilizzi per il set di dati di file non dipendono più da numpy e pandas da installare nell'ambiente python.
    + Modificato LocalWebservice.wait_for_deployment() per controllare lo stato del contenitore Docker locale prima di tentare di eseguire il ping dell'endpoint di integrità, riducendo notevolmente il tempo necessario per segnalare una distribuzione non riuscita.
    + Correzione dell'inizializzazione di una proprietà interna usata in LocalWebservice.reload() quando l'oggetto servizio viene creato da una distribuzione esistente usando il costruttore LocalWebservice().
    + Messaggio di errore modificato per chiarimenti.
    + Aggiunta di un nuovo metodo denominato get_access_token() ad AksWebservice che restituirà l'oggetto AksServiceAccessToken, che contiene il token di accesso, l'aggiornamento dopo il timestamp, la scadenza del timestamp e del tipo di token. 
    + Il metodo get_token() esistente deprecato in AksWebservice poiché il nuovo metodo restituisce tutte le informazioni restituite da questo metodo.
    + Output modificato del comando az ml service get-access-token. Il token è stato rinominato in accessToken e refreshBy in refreshAfter. Aggiunta delle proprietà expiryOn e tokenType.
    + Correzione del get_active_runs
  + **azureml-explain-model**
    + aggiornamento di shap alla versione 0.33.0 e di interpret-community alla versione 0.4.*
  + **azureml-interpret**
    + aggiornamento di shap alla versione 0.33.0 e di interpret-community alla versione 0.4.*
  + **azureml-train-automl-runtime**
    + Aggiunta del coefficiente di correlazione Disas come metrica di classificazione, sia per la classificazione binaria che multiclasse.
    + Deprecate preprocess flag from code and replaced with featurization -featurization is on by default (Deprecate preprocess flag from code and replaced with featurization -featurization is on by default) (Deprecate preprocess flag from code and replaced with featurization -featurization is on by

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK per Python v1.0.83

+ **Nuove funzionalità**
  + Set di dati: aggiungere due opzioni e per avere esito negativo quando i dati hanno valori `on_error` `out_of_range_datetime` di errore anziché `to_pandas_dataframe` compilarli con `None` .
  + Area di lavoro: è stato aggiunto il flag per le aree di lavoro con dati sensibili che abilita un'ulteriore crittografia e disabilita la diagnostica `hbi_workspace` avanzata nelle aree di lavoro. È stato anche aggiunto il supporto per portare le proprie chiavi per l'istanza di Cosmos DB associata, specificando i parametri e durante la creazione di un'area di lavoro, che crea un'istanza di Cosmos DB nella sottoscrizione durante il provisioning dell'area di `cmk_keyvault` `resource_cmk_uri` lavoro. [Per altre informazioni, leggi qui.](./concept-data-encryption.md#azure-cosmos-db)

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + Correzione di una regressione che causava la creazione di un errore di tipo durante l'esecuzione di AutoML nelle versioni di Python precedenti alla 3.5.4.
  + **azureml-core**
    + Correzione del bug in `datastore.upload_files` perché il percorso relativo che non iniziava con non era in grado di essere `./` usato.
    + Aggiunta di messaggi di deprecazione per tutti i percorsi del codice della classe Image
    + Correzione della Gestione modelli dell'URL per Azure China (21Vianet)area.
    + È stato risolto un problema a causa del quale source_dir non era possibile creare pacchetti per Funzioni di Azure.    
    + Aggiunta di un'opzione a [Environment.build_local()](/python/api/azureml-core/azureml.core.environment.environment) per eseguire il push di un'immagine nel registro contenitori dell'area di lavoro di AzureML
    + Aggiornamento dell'SDK per l'uso della nuova libreria di token in Azure Synapse in modo compatibile con le versioni precedenti.
  + **azureml-interpret**
    + Correzione del bug per cui non è stato restituito nessuno quando non sono disponibili spiegazioni per il download. Ora genera un'eccezione, che corrisponde al comportamento altrove.
  + **azureml-pipeline-steps**
    + Passaggio di non `DatasetConsumptionConfig` consentito al parametro di quando verrà utilizzato in un oggetto `Estimator` `inputs` `Estimator` `EstimatorStep` .
  + **azureml-sdk**
    + Aggiunta del client AutoML al pacchetto azureml-sdk, che consente l'invio di esecuzioni AutoML remote senza installare il pacchetto AutoML completo.
  + **azureml-train-automl-client**
    + Correzione dell'allineamento nell'output della console per le esecuzioni AutoML
    + Correzione di un bug per cui è possibile installare una versione non corretta di pandas in amlcompute remoto.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK per Python v1.0.81

+ **Correzioni di bug e miglioramenti**
  + **azureml-contrib-interpret**
    + defer shap dependency to interpret-community from azureml-interpret
  + **azureml-core**
    + La destinazione di calcolo può ora essere specificata come parametro per gli oggetti di configurazione della distribuzione corrispondenti. Si tratta in particolare del nome della destinazione di calcolo in cui eseguire la distribuzione, non dell'oggetto SDK.
    + Aggiunta delle informazioni CreatedBy agli oggetti Modello e Servizio. È possibile accedere through.created_by
    + Correzione di ContainerImage.run(), che non configurava correttamente la porta HTTP del contenitore Docker.
    + Rendere `azureml-dataprep` facoltativo il comando dell'interfaccia `az ml dataset register` della riga di comando
    + È stato risolto un bug in cui si verificava erroneamente `TabularDataset.to_pandas_dataframe` il fall back a un lettore alternativo e si stampava un avviso.
  + **azureml-explain-model**
    + defer shap dependency to interpret-community from azureml-interpret
  + **azureml-pipeline-core**
    + Aggiunta del nuovo passaggio della pipeline `NotebookRunnerStep` , per eseguire un notebook locale come passaggio nella pipeline.
    + Rimozione delle funzioni get_all deprecate per PublishedPipelines, Schedules e PipelineEndpoints
  + **azureml-train-automl-client**
    + È stata avviata la deprecazione data_script come input per AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK per Python v1.0.79

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Rimozione di featurizationConfig da registrato
      + Aggiornamento della registrazione per registrare solo "auto"/"off"/"customized".
  + **azureml-automl-runtime**
    + Aggiunta del supporto per pandas. Serie e pandas. Categoria per il rilevamento del tipo di dati della colonna. In precedenza era supportato solo numpy.ndarray
      + Aggiunta di modifiche al codice correlate per gestire correttamente il dtype categorico.
    + L'interfaccia della funzione di previsione è stata migliorata: y_pred parametro è stato reso facoltativo. -I docstring sono stati migliorati.
  + **azureml-contrib-dataset**
    + Correzione di un bug per cui non è possibile montare set di dati etichettati.
  + **azureml-core**
    + Correzione di bug per `Environment.from_existing_conda_environment(name, conda_environment_name)` . L'utente può creare un'istanza di Environment che rappresenta la replica esatta dell'ambiente locale
    + Per impostazione predefinita, i metodi dei set di dati relativi alle serie tempori sono `include_boundary=True` stati modificati in .
  + **azureml-train-automl-client**
    + È stato risolto un problema per cui i risultati della convalida non vengono stampati quando l'opzione Mostra output è impostata su false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK per Python v1.0.76

+ **Modifiche di rilievo**
  + Problemi di aggiornamento di Azureml-Train-AutoML
    + L'aggiornamento ad azureml-train-automl>=1.0.76 da azureml-train-automl<1.0.76 può causare installazioni parziali, causando l'esito negativo di alcune importazioni AutoML. Per risolvere questo problema, è possibile eseguire lo script di installazione disponibile in https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . In caso di uso diretto di pip, è possibile:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + oppure è possibile disinstallare la versione precedente prima dell'aggiornamento
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-runtime**
    + AutoML ora prenderà in considerazione le classi true e false durante il calcolo delle metriche scalari medie per le attività di classificazione binaria.
    + È stato spostato il codice di Machine Learning e training in AzureML-AutoML-Core in un nuovo pacchetto AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Quando si `to_pandas_dataframe` chiama su un set di dati con etichetta con l'opzione di download, è ora possibile specificare se sovrascrivere o meno i file esistenti.
    + Quando si chiama o si verifica l'ora in cui viene eliminata una serie temporale, un'etichetta o una colonna di immagine, verranno eliminate anche le funzionalità `keep_columns` `drop_columns` corrispondenti per il set di dati.
    + È stato risolto un problema con il caricatore pytorch per l'attività di rilevamento degli oggetti.
  + **azureml-contrib-interpret**
    + Rimozione del widget del dashboard spiegazione da azureml-contrib-interpret, pacchetto modificato per fare riferimento a quello nuovo in interpret_community
    + Versione aggiornata di interpret-community alla versione 0.2.0
  + **azureml-core**
    + Migliorare le prestazioni di `workspace.datasets` .
    + Aggiunta della possibilità di registrare database SQL di Azure Datastore usando l'autenticazione di nome utente e password
    + Correzione per il caricamento di RunConfigurations da percorsi relativi.
    + Quando si chiama o che comporta l'eliminare una colonna della serie temporale, verranno eliminate anche le funzionalità `keep_columns` `drop_columns` corrispondenti per il set di dati.
  + **azureml-interpret**
    + versione aggiornata di interpret-community alla versione 0.2.0
  + **azureml-pipeline-steps**
    + Documentati i valori supportati per i `runconfig_pipeline_params` passaggi della pipeline di Azure Machine Learning.
  + **azureml-pipeline-core**
    + Aggiunta dell'opzione dell'interfaccia della riga di comando per scaricare l'output in formato JSON per i comandi della pipeline.
  + **azureml-train-automl**
    + Suddividere AzureML-Train-AutoML in due pacchetti, un pacchetto client AzureML-Train-AutoML-Client e un pacchetto di training ml AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Aggiunta di un thin client per l'invio di esperimenti AutoML senza dover installare le dipendenze di Machine Learning in locale.
    + Correzione della registrazione dei ritardi rilevati automaticamente, delle dimensioni delle finestre in sequenza e degli orizzonti massimi nelle esecuzioni remote.
  + **azureml-train-automl-runtime**
    + È stato aggiunto un nuovo pacchetto AutoML per isolare i componenti di Machine Learning e runtime dal client.
  + **azureml-contrib-train-rl**
    + Aggiunta del supporto per l'apprendimento per rinforzo nell'SDK.
    + Aggiunta del supporto di AmlWindowsCompute in RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK per Python v1.0.74

  + **Funzionalità di anteprima**
    + **azureml-contrib-dataset**
      + Dopo aver importato azureml-contrib-dataset, è possibile chiamare `Dataset.Labeled.from_json_lines` anziché per creare un set di dati con `._Labeled` etichetta.
      + Quando si chiama su un set di dati con etichetta con l'opzione di download, è ora possibile specificare se `to_pandas_dataframe` sovrascrivere o meno i file esistenti.
      + Quando si chiama o che comporta l'eliminare una serie temporale, un'etichetta o una colonna immagine, verranno eliminate anche le funzionalità `keep_columns` `drop_columns` corrispondenti per il set di dati.
      + Sono stati risolti problemi con il caricatore PyTorch quando si chiama `dataset.to_torchvision()` .

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + Aggiunta di Profilatura del modello all'interfaccia della riga di comando di anteprima.
    + Corregge le modifiche di rilievo nel Archiviazione di Azure causando l'esito negativo dell'interfaccia della riga di comando di AzureML.
    + Aggiunta del Load Balancer di controllo di accesso a MLC per i tipi di AKS
  + **azureml-automl-core**
    + È stato risolto il problema relativo al rilevamento dell'orizzonte massimo sulle serie temporità, con valori mancanti e più grani.
    + È stato risolto il problema relativo a errori durante la generazione di divisioni di convalida incrociata.
    + Sostituire questa sezione con un messaggio in formato markdown da visualizzare nelle note sulla versione: -Migliorata la gestione delle granularità brevi nei set di dati di previsione.
    + È stato risolto il problema relativo alla maschera di alcune informazioni utente durante la registrazione. -Migliorata la registrazione degli errori durante le esecuzioni di previsione.
    + Aggiunta di psutil come dipendenza conda al file di distribuzione yml rigenerato automaticamente.
  + **azureml-contrib-mir**
    + Corregge le modifiche di rilievo nella Archiviazione di Azure causando l'esito negativo dell'interfaccia della riga di comando di AzureML.
  + **azureml-core**
    + Correzione di un bug che ha causato la distribuzione di modelli Funzioni di Azure per produrre 500.
    + È stato risolto un problema a causa del quale il file amlignore non è stato applicato agli snapshot.
    + Aggiunta di un nuovo amlcompute.get_active_runs API che restituisce un generatore per l'esecuzione e le esecuzioni in coda in un determinato amlcompute.
    + Aggiunta Load Balancer tipo di supporto a MLC per i tipi del centro di controllo di accesso.
    + Aggiunta append_prefix parametro bool per download_files in run.py e download_artifacts_from_prefix in artifacts_client. Questo flag viene usato per appiattire in modo selettivo il percorso del file di origine in modo che solo il nome del file o della cartella viene aggiunto al output_directory
    + Correzione del problema di deserializzazione per `run_config.yml` l'utilizzo del set di dati.
    + Quando si `keep_columns` chiama o che comporta l'eliminare una colonna time series, verranno eliminate anche le funzionalità corrispondenti per `drop_columns` il set di dati.
  + **azureml-interpret**
    + Aggiornamento della versione interpret-community alla versione 0.1.0.3
  + **azureml-train-automl**
    + Risolto un problema a causa del quale automl_step potrebbero non stampare problemi di convalida.
    + Correzione register_model esito positivo anche se nell'ambiente del modello mancano dipendenze in locale.
    + Risolto un problema per cui alcune esecuzioni remote non erano abilitate per Docker.
    + Aggiungere la registrazione dell'eccezione che causa un errore prematura di un'esecuzione locale.
  + **azureml-train-core**
    + Prendere in resume_from le esecuzioni nel calcolo dell'ottimizzazione automatica degli iperparamemi con le migliori esecuzioni figlio.
  + **azureml-pipeline-core**
    + Correzione della gestione dei parametri nella costruzione di argomenti della pipeline.
    + Aggiunta della descrizione della pipeline e del parametro yaml del tipo di passaggio.
    + Nuovo formato yaml per il passaggio pipeline e aggiunta dell'avviso di deprecazione per il formato precedente.



## <a name="2019-11-04"></a>4 novembre 2019

### <a name="web-experience"></a>Esperienza Web

La pagina di destinazione dell'area di lavoro collaborativa in è stata migliorata e [https://ml.azure.com](https://ml.azure.com) ridefinito come studio di Azure Machine Learning.

Da Studio è possibile eseguire il training, testare, distribuire e gestire asset Azure Machine Learning, ad esempio set di dati, pipeline, modelli, endpoint e altro ancora.

Accedere ai seguenti strumenti di creazione basati sul Web da Studio:

| Strumento basato sul Web | Descrizione | 
|-|-|-|
| Macchina virtuale notebook (anteprima) | Workstation basata sul cloud completamente gestita | 
| [Machine Learning automatizzato](tutorial-first-experiment-automated-ml.md) (anteprima) | Nessuna esperienza di codice per l'automazione dello sviluppo di modelli di Machine Learning | 
| [Progettazione](concept-designer.md) | Strumento di modellazione di Machine Learning con trascinamento della selezione noto in precedenza come interfaccia visiva grafica | 


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning miglioramenti della finestra di progettazione

+ Precedentemente nota come interfaccia visiva 
+    11 nuovi [moduli, tra](algorithm-module-reference/module-reference.md) cui consiglieri, classificatori e utilità di training, tra cui progettazione delle funzionalità, convalida incrociata e trasformazione dei dati.

### <a name="r-sdk"></a>R SDK 
 
Data scientist e sviluppatori di intelligenza artificiale usano [Azure Machine Learning SDK per R](https://github.com/Azure/azureml-sdk-for-r) per compilare ed eseguire flussi di lavoro di Machine Learning con Azure Machine Learning.

L Azure Machine Learning SDK per R usa il `reticulate` pacchetto per l'associazione a Python SDK. Associando direttamente a Python, l'SDK per R consente di accedere agli oggetti e ai metodi principali implementati in Python SDK da qualsiasi ambiente R scelto.

Le funzionalità principali dell'SDK includono:

+    Gestione delle risorse cloud per il monitoraggio, la registrazione e l'organizzazione degli esperimenti di machine learning.
+    Training dei modelli usando risorse cloud, incluso il training dei modelli accelerati con GPU.
+    Distribuire i modelli come servizi Web in Istanze di Azure Container (ACI) e servizio Azure Kubernetes (AKS).

Per la documentazione [completa, vedere](https://azure.github.io/azureml-sdk-for-r) il sito Web del pacchetto.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning'integrazione con Griglia di eventi 

Azure Machine Learning è ora un provider di risorse per Griglia di eventi, è possibile configurare gli eventi di Machine Learning tramite l'interfaccia portale di Azure o l'interfaccia della riga di comando di Azure. Gli utenti possono creare eventi per il completamento dell'esecuzione, la registrazione del modello, la distribuzione del modello e la deriva dei dati rilevata. Questi eventi possono essere indirizzati ai gestori eventi supportati da Griglia di eventi per l'utilizzo. Per altre informazioni, vedere [lo schema](../event-grid/event-schema-machine-learning.md) degli eventi di Machine Learning e [gli](how-to-use-event-grid.md) articoli dell'esercitazione.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK per Python v1.0.72

+ **Nuove funzionalità**
  + Sono stati aggiunti monitoraggi del set di dati tramite il pacchetto [**azureml-datadrift,**](/python/api/azureml-datadrift) consentendo il monitoraggio dei set di dati time series per la deriva dei dati o altre modifiche statistiche nel tempo. Gli avvisi e gli eventi possono essere attivati se viene rilevata una deriva o vengono soddisfatte altre condizioni sui dati. Per [informazioni dettagliate, vedere](how-to-monitor-datasets.md) la documentazione.
  + Announcing two new editions (also referred to as a SKU interchangeably) in Azure Machine Learning. Con questa versione è ora possibile creare un'area di lavoro di Azure Machine Learning Basic o Enterprise. Tutte le aree di lavoro esistenti verranno aggiornate per impostazione predefinita all'edizione Basic ed è possibile passare al portale di Azure o a Studio per aggiornare l'area di lavoro in qualsiasi momento. È possibile creare un'area di lavoro Basic o Enterprise dal portale di Azure. Per [altre informazioni,](./how-to-manage-workspace.md) leggere la documentazione. Dall'SDK, l'edizione dell'area di lavoro può essere determinata usando la proprietà "sku" dell'oggetto dell'area di lavoro.
  + Sono stati anche apportati miglioramenti all'ambiente di calcolo di Azure Machine Learning: è ora possibile visualizzare le metriche per i cluster (ad esempio il totale dei nodi, i nodi in esecuzione, la quota core totale) in Monitoraggio di Azure, oltre a visualizzare i log di diagnostica per il debug. Inoltre, è anche possibile visualizzare le esecuzioni attualmente in esecuzione o in coda nel cluster e i dettagli, ad esempio gli IP dei vari nodi del cluster. È possibile visualizzarli nel portale o usando le funzioni corrispondenti nell'SDK o nell'interfaccia della riga di comando.

  + **Funzionalità di anteprima**
    + Microsoft sta rilasciando il supporto in anteprima per la crittografia del disco dell'unità SSD locale in Azure Machine Learning calcolo. Generare un ticket di supporto tecnico per fare in modo che la sottoscrizione consenta di usare questa funzionalità.
    + Anteprima pubblica di Azure Machine Learning'inferenza batch. Azure Machine Learning'inferenza batch è destinata a processi di inferenza di grandi dimensioni che non sono sensibili al tempo. L'inferenza batch offre un ridimensionamento del calcolo di inferenza conveniente, con una velocità effettiva senza precedenti per le applicazioni asincrone. È ottimizzato per l'inferenza fire-and-forget ad alta velocità effettiva su raccolte di dati di grandi dimensioni.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + Funzionalità abilitate per il set di dati con etichetta
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + L'interfaccia della riga di comando supporta ora la creazione di pacchetti di modelli.
    + Aggiunta dell'interfaccia della riga di comando del set di dati. Per altre informazioni: `az ml dataset --help`
    + Aggiunta del supporto per la distribuzione e la creazione di pacchetti di modelli supportati (ONNX, scikit-learn e TensorFlow) senza un'istanza InferenceConfig.
    + Aggiunta del flag di sovrascrittura per la distribuzione del servizio (ACI e servizio AKS) nell'SDK e nell'interfaccia della riga di comando. Se specificato, sovrascriverà il servizio esistente se il servizio con nome esiste già. Se il servizio non esiste, creerà un nuovo servizio.
    + I modelli possono essere registrati con due nuovi framework, Onnx e Tensorflow. - La registrazione del modello accetta dati di input di esempio, dati di output di esempio e configurazione delle risorse per il modello.
  + **azureml-automl-core**
    + Il training di un'iterazione viene eseguito in un processo figlio solo quando vengono impostati vincoli di runtime.
    + Aggiunta di un guardrail per le attività di previsione, per verificare se un'max_horizon specificata causerà o meno un problema di memoria nel computer specificato. In caso contrario, verrà visualizzato un messaggio di guardrail.
    + Aggiunta del supporto per frequenze complesse come due anni e un mese. -Aggiunta del messaggio di errore comprensibile se non è possibile determinare la frequenza.
    + Aggiungere azureml-defaults a conda env generato automaticamente per risolvere l'errore di distribuzione del modello
    + Consente di convertire i dati intermedi Azure Machine Learning pipeline in un set di dati tabulare e di usare in `AutoMLStep` .
    + Implementazione dell'aggiornamento dello scopo della colonna per lo streaming.
    + Implementato aggiornamento dei parametri del trasformatore per Imputer e HashOneHotEncoder per lo streaming.
    + Sono state aggiunte le dimensioni dei dati correnti e le dimensioni minime necessarie ai messaggi di errore di convalida.
    + Aggiornamento delle dimensioni minime dei dati necessarie per la convalida incrociata per garantire un minimo di due campioni in ogni fold di convalida.
  + **azureml-cli-common**
    + L'interfaccia della riga di comando supporta ora la creazione di pacchetti di modelli.
    + I modelli possono essere registrati con due nuovi framework, Onnx e Tensorflow.
    + La registrazione del modello accetta dati di input di esempio, dati di output di esempio e configurazione delle risorse per il modello.
  + **azureml-contrib-gbdt**
    + correzione del canale di rilascio per il notebook
    + Aggiunta di un avviso per la destinazione di calcolo non AmlCompute che non è possibile supportare
    + Aggiunta dello strumento di stima LightGMB al pacchetto azureml-contrib-gbdt
  + [**azureml-core**](/python/api/azureml-core)
    + L'interfaccia della riga di comando supporta ora la creazione di pacchetti di modelli.
    + Aggiunta di un avviso di deprecazione per le API del set di dati deprecate. Vedere l'avviso di modifica dell'API del set di dati all'indirizzo https://aka.ms/tabular-dataset .
    + Modificare [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) per restituire il nome e la versione della registrazione se il set di dati è registrato.
    + Correzione di un bug per cui ScriptRunConfig con set di dati come argomento non può essere usato ripetutamente per inviare l'esecuzione dell'esperimento.
    + I set di dati recuperati durante un'esecuzione verranno monitorati e possono essere visualizzati nella pagina dei dettagli dell'esecuzione o chiamando al termine [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) dell'esecuzione.
    + Consentire ai dati intermedi nella Azure Machine Learning pipeline di essere convertiti in set di dati tabulari e usati in [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Aggiunta del supporto per la distribuzione e la creazione di pacchetti di modelli supportati (ONNX, scikit-learn e TensorFlow) senza un'istanza di InferenceConfig.
    + È stato aggiunto il flag di sovrascrittura per la distribuzione del servizio (ACI e servizio Microsoft Service Pack) nell'SDK e nell'interfaccia della riga di comando. Se specificato, sovrascriverà il servizio esistente se il servizio con il nome esiste già. Se il servizio non esiste, creerà un nuovo servizio.
    +  I modelli possono essere registrati con due nuovi framework, Onnx e Tensorflow. La registrazione del modello accetta dati di input di esempio, dati di output di esempio e configurazione delle risorse per il modello.
    + Aggiunta di un nuovo archivio dati per Database di Azure per MySQL. Aggiunta di un esempio per l'uso di Database di Azure per MySQL in DataTransferStep in Azure Machine Learning pipeline.
    + Aggiunta della funzionalità per aggiungere e rimuovere tag dagli esperimenti Funzionalità aggiunta per rimuovere i tag dalle esecuzioni
    + Aggiunta del flag di sovrascrittura per la distribuzione del servizio (ACI e servizio AKS) nell'SDK e nell'interfaccia della riga di comando. Se specificato, sovrascriverà il servizio esistente se il servizio con nome esiste già. Se il servizio non esiste, creerà un nuovo servizio.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Spostato da `azureml-contrib-datadrift` in `azureml-datadrift`
    + Aggiunta del supporto per il monitoraggio dei set di dati time series per la deviazione e altre misure statistiche
    + Nuovi metodi `create_from_model()` `create_from_dataset()` e alla classe [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) . Il `create()` metodo verrà deprecato.
    + Modifiche alle visualizzazioni in Python e nell'interfaccia utente nel studio di Azure Machine Learning.
    + Supporto della pianificazione dei monitoraggi settimanali e mensili, oltre che giornaliera per i monitoraggi dei set di dati.
    + Supporto del riempimento delle metriche di monitoraggio dei dati per analizzare i dati cronologici per i monitoraggi dei set di dati.
    + Varie correzioni di bug
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + azureml-dataprep non è più necessario per inviare un'Azure Machine Learning pipeline dal file della `yaml` pipeline.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Aggiungere azureml-defaults a conda env generato automaticamente per risolvere l'errore di distribuzione del modello
    + Il training remoto AutoML include ora azureml-defaults per consentire il riutilizzo dell'ambiente di training per l'inferenza.
  + **azureml-train-core**
    + Aggiunta del supporto di PyTorch 1.3 nella [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) stima

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interfaccia visiva (anteprima)

+ L Azure Machine Learning'interfaccia visiva (anteprima) è stata revisionata per l'esecuzione [Azure Machine Learning pipeline.](concept-ml-pipelines.md) Le pipeline (note in precedenza come esperimenti) creati nell'interfaccia visiva visiva sono ora completamente integrate con l'esperienza Azure Machine Learning base.
  + Esperienza di gestione unificata con gli asset SDK
  + Controllo delle versioni e rilevamento per modelli di interfaccia visiva visiva, pipeline ed endpoint
  + Interfaccia utente riprogettata
  + Aggiunta della distribuzione dell'inferenza batch
  + Aggiunta del servizio Azure Kubernetes per le destinazioni di calcolo di inferenza
  + Nuovo flusso di lavoro di creazione di pipeline in passaggi Python
  + Nuova [pagina di destinazione](https://ml.azure.com) per gli strumenti di creazione di oggetti visivi

+ **Nuovi moduli**
  + Applicare un'operazione matematica
  + Applicare la trasformazione SQL
  + Ritagliare i valori
  + Riepilogare i dati
  + Importare da database SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK per Python v1.0.69

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Limitare le spiegazioni del modello per un'esecuzione ottimale anziché calcolare le spiegazioni per ogni esecuzione. Modifica di questo comportamento per a livello locale, remoto e ADB.
    + Aggiunta del supporto per le spiegazioni del modello su richiesta per l'interfaccia utente
    + Aggiunta di psutil come dipendenza di `automl` e inclusa psutil come dipendenza conda in amlcompute.
    + È stato risolto il problema relativo ai ritardi euristici e alle dimensioni delle finestre in sequenza nei set di dati di previsione, alcune delle quali possono causare errori di algebra lineare
      + Aggiunta della stampa per i parametri determinati in modo euristico nelle esecuzioni di previsione.
  + **azureml-contrib-datadrift**
    + Aggiunta della protezione durante la creazione di metriche di output se la deriva a livello di set di dati non si trova nella prima sezione.
  + **azureml-contrib-interpret**
    + Il pacchetto azureml-contrib-explain-model è stato rinominato in azureml-contrib-interpret
  + **azureml-core**
    + Aggiunta dell'API per annullare la registrazione dei set di dati. `dataset.unregister_all_versions()`
    + Il pacchetto azureml-contrib-explain-model è stato rinominato in azureml-contrib-interpret.
  + **[azureml-core](/python/api/azureml-core)**
    + Aggiunta dell'API per annullare la registrazione dei set di dati. Dataset. [unregister_all_versions()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Aggiunta dell'API Set di dati per controllare l'ora di modifica dei dati. `dataset.data_changed_time`.
    + Possibilità di utilizzare `FileDataset` e come input per , e in Azure Machine Learning `TabularDataset` `PythonScriptStep` `EstimatorStep` `HyperDriveStep` Pipeline
    + Le prestazioni `FileDataset.mount` di sono state migliorate per le cartelle con un numero elevato di file
    + Possibilità di usare [FileDataset](/python/api/azureml-core/azureml.data.filedataset) e [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) come input per [PythonScriptStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)e [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) nella Azure Machine Learning pipeline.
    + Prestazioni di FileDataset. [mount()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) è stato migliorato per le cartelle con un numero elevato di file
    + Aggiunta dell'URL alle raccomandazioni di errore note nei dettagli di esecuzione.
    + Correzione di un bug in run.get_metrics in cui le richieste hanno esito negativo se un'esecuzione ha troppi elementi figlio
    + Correzione di un bug in [run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) in cui le richieste hanno esito negativo se un'esecuzione ha troppi elementi figlio
    + Aggiunta del supporto per l'autenticazione nel cluster Diadia.
    + La creazione di un oggetto Experiment ottiene o crea l'esperimento nell'area Azure Machine Learning per il rilevamento della cronologia di esecuzione. L'ID dell'esperimento e l'ora archiviata vengono popolati nell'oggetto Experiment al momento della creazione. Esempio: experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() e reactivate() sono funzioni che possono essere chiamate in un esperimento per nascondere e ripristinare la visualizzazione dell'esperimento nell'esperienza utente o restituita per impostazione predefinita in una chiamata per elencare gli esperimenti. Se viene creato un nuovo esperimento con lo stesso nome di un esperimento archiviato, è possibile rinominare l'esperimento archiviato durante la riattivazione passando un nuovo nome. Può essere presente un solo esperimento attivo con un nome specificato. Esempio: experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Creare un nuovo esperimento attivo con lo stesso nome dell'esperimento archiviato. experiment2. = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") The static method list() on Experiment can take a name filter and ViewType filter. I valori viewType sono "ACTIVE_ONLY", "ARCHIVED_ONLY" e "ALL" Esempio: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Supporto dell'uso dell'ambiente per la distribuzione del modello e dell'aggiornamento del servizio
  + **azureml-datadrift**
    + L'attributo show della classe DataDriftDector non supporta più l'argomento facoltativo 'with_details'. L'attributo show presenterà solo il coefficiente di deriva dei dati e il contributo della deriva dei dati delle colonne di funzionalità.
    + Modifiche del comportamento dell'attributo 'get_output' di DataDriftDetector:
      + Parametri di start_time, end_time facoltativi anziché obbligatori;
      + L'start_time di input e/o end_time con un run_id specifico nella stessa chiamata genera un'eccezione di errore di valore perché si escludono a vicenda
      + In base all'input start_time e/o end_time, verranno restituiti solo i risultati delle esecuzioni pianificate.
      + Il parametro 'daily_latest_only' è deprecato.
    + Supporto del recupero degli output di deriva dei dati basati su set di dati.
  + **azureml-explain-model**
    + Rinomina il pacchetto AzureML-explain-model in AzureML-interpret, mantenendo il pacchetto precedente per la compatibilità con le versioni precedenti per il momento
    + Correzione `automl` di un bug con spiegazioni non elaborate impostate sull'attività di classificazione anziché sulla regressione per impostazione predefinita durante il download da ExplanationClient
    + Aggiungere il supporto `ScoringExplainer` per da creare direttamente usando `MimicWrapper`
  + **azureml-pipeline-core**
    + Prestazioni migliorate per la creazione di pipeline di grandi dimensioni
  + **azureml-train-core**
    + Aggiunta del supporto di TensorFlow 2.0 in TensorFlow Estimator
  + **azureml-train-automl**
    + La creazione di [un oggetto Experiment](/python/api/azureml-core/azureml.core.experiment.experiment) ottiene o crea l'esperimento nell'area Azure Machine Learning per il rilevamento della cronologia di esecuzione. L'ID dell'esperimento e l'ora archiviata vengono popolati nell'oggetto Experiment al momento della creazione. Esempio:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) e [reactivate()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) sono funzioni che possono essere chiamate in un esperimento per nascondere e ripristinare l'esperimento dall'essere visualizzato nell'esperienza utente o restituito per impostazione predefinita in una chiamata agli esperimenti di elenco. Se viene creato un nuovo esperimento con lo stesso nome di un esperimento archiviato, è possibile rinominare l'esperimento archiviato durante la riattivazione passando un nuovo nome. Può essere presente un solo esperimento attivo con un nome specificato. Esempio:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Il metodo statico [list()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) nell'esperimento può usare un filtro nome e un filtro ViewType. I valori viewType sono "ACTIVE_ONLY", "ARCHIVED_ONLY" e "ALL". Esempio:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Supporto dell'uso dell'ambiente per la distribuzione del modello e dell'aggiornamento del servizio.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + L'attributo show [della classe DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) non supporta più l'argomento facoltativo 'with_details'. L'attributo show presenterà solo il coefficiente di deriva dei dati e il contributo alla deriva dei dati delle colonne della funzionalità.
    + Modifica del comportamento della funzione DataDriftDetector [get_output]python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-):
      + I parametri di start_time, end_time sono facoltativi anziché obbligatori;
      + L'input start_time e/o end_time con un run_id specifico nella stessa chiamata genera un'eccezione di errore di valore perché si escludono a vicenda;
      + In base all'input start_time e/o end_time, verranno restituiti solo i risultati delle esecuzioni pianificate.
      + Il parametro 'daily_latest_only' è deprecato.
    + Supporto del recupero degli output di deriva dei dati basati su set di dati.
  + **azureml-explain-model**
    + Aggiunta del supporto [per la creazione diretta di ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer) tramite MimicWrapper
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Miglioramento delle prestazioni per la creazione di pipeline di grandi dimensioni.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + Aggiunta del supporto di TensorFlow 2.0 in [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + L'esecuzione padre non avrà più esito negativo quando l'iterazione dell'installazione non è riuscita, perché l'orchestrazione ne esegue già la gestione.
    + Aggiunta del supporto local-docker e local-conda per gli esperimenti AutoML
    + Aggiunta del supporto local-docker e local-conda per gli esperimenti AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nuova esperienza Web (anteprima) per le aree Azure Machine Learning lavoro

La scheda Experiment (Esperimento) nel [nuovo portale dell'area](https://ml.azure.com) di lavoro è stata aggiornata in modo che i data scientist possano monitorare gli esperimenti in modo più efficace. È possibile esplorare le funzionalità seguenti:
+ Metadati dell'esperimento per filtrare e ordinare facilmente l'elenco di esperimenti
+ Pagine dei dettagli dell'esperimento semplificate ed performanti che consentono di visualizzare e confrontare le esecuzioni
+ Nuova progettazione per l'esecuzione di pagine di dettagli per comprendere e monitorare le esecuzioni di training

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK per Python v1.0.65

  + **Nuove funzionalità**
    + Aggiunta di ambienti curati. Questi ambienti sono stati preconfigurati con librerie per attività comuni di Machine Learning e sono stati pre-compilati e memorizzati nella cache come immagini Docker per un'esecuzione più rapida. Vengono visualizzati per impostazione predefinita nell'elenco dell'ambiente dell'area di lavoro, con il prefisso "AzureML".
    + Aggiunta di ambienti curati. Questi ambienti sono stati preconfigurati con librerie per attività comuni di Machine Learning e sono stati pre-compilati e memorizzati nella cache come immagini Docker per un'esecuzione più rapida. Vengono visualizzate per impostazione predefinita [nell'elenco](/python/api/azureml-core/azureml.core.workspace%28class%29)dell'ambiente dell'area di lavoro, con il prefisso "AzureML".

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Aggiunta del supporto della conversione ONNX per ADB e HDI

+ **Funzionalità di anteprima**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT e BiLSTM supportati come featurizer di testo (solo anteprima)
    + Personalizzazione delle funzionalità supportata per lo scopo della colonna e parametri del trasformatore (solo anteprima)
    + Spiegazioni non elaborate supportate quando l'utente abilita la spiegazione del modello durante il training (solo anteprima)
    + Aggiunta di Prophet `timeseries` per la previsione come pipeline di cui è possibile eseguire il training (solo anteprima)

  + **azureml-contrib-datadrift**
    + Pacchetti trasferiti da azureml-contrib-datadrift ad azureml-datadrift; il `contrib` pacchetto verrà rimosso in una versione futura

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Introduzione a FeaturizationConfig in AutoMLConfig e AutoMLBaseSettings
    + Introduzione di FeaturizationConfig [ad AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) e AutoMLBaseSettings
      + Eseguire l'override dello scopo della colonna per la funzionalità con la colonna e il tipo di funzionalità specificato
      + Eseguire l'override dei parametri del trasformatore
    + Aggiunta del messaggio di deprecazione per explain_model() e retrieve_model_explanations()
    + Aggiunta di Un'area di lavoro come pipeline di cui è possibile eseguire il training (solo anteprima)
    + Aggiunta del messaggio di deprecazione per explain_model() e retrieve_model_explanations().
    + Aggiunta di Un'area di lavoro come pipeline di cui è possibile eseguire il training (solo anteprima).
    + Aggiunta del supporto per il rilevamento automatico dei ritardi di destinazione, delle dimensioni della finestra in sequenza e dell'orizzonte massimo. Se una delle target_lags, target_rolling_window_size o max_horizon è impostata su "auto", l'euristica verrà applicata per stimare il valore del parametro corrispondente in base ai dati di training.
    + Correzione della previsione nel caso in cui il set di dati contenga una colonna con granularità, questo intervallo è di tipo numerico ed esiste un divario tra il set di training e quello di test
    + Correzione del messaggio di errore sull'indice duplicato nell'esecuzione remota nelle attività di previsione
    + Correzione della previsione nel caso in cui il set di dati contenga una colonna con granularità, questo intervallo è di tipo numerico ed esiste un divario tra il set di training e il set di test.
    + Correzione del messaggio di errore sull'indice duplicato nell'esecuzione remota nelle attività di previsione.
    + Aggiunta di una protezione per verificare se un set di dati è sbilanciato o meno. In caso contrario, nella console verrà scritto un messaggio di protezione.
  + **azureml-core**
    + Aggiunta della possibilità di recuperare l'URL di firma di accesso condiviso per il modello nell'archiviazione tramite l'oggetto modello. Ad esempio: model.get_sas_url()
    + Introduzione `run.get_details()['datasets']` a ottenere i set di dati associati all'esecuzione inviata
    + Aggiungere `Dataset.Tabular.from_json_lines_files` l'API per creare un oggetto TabularDataset dai file JSON Lines. Per informazioni su questi dati tabulari nei file di righe JSON in TabularDataset, vedere [questo articolo per](how-to-create-register-datasets.md) la documentazione.
    + Aggiunta di campi aggiuntivi per le dimensioni della macchina virtuale (disco del sistema operativo, numero di GPU) alla supported_vmsizes ()
    + Aggiunta di campi aggiuntivi alla list_nodes () per visualizzare l'esecuzione, l'indirizzo IP privato e pubblico, la porta e così via.
    + Possibilità di specificare un nuovo campo durante il provisioning del cluster --remotelogin_port_public_access che può essere impostato su abilitato o disabilitato a seconda che si desideri lasciare aperta o chiusa la porta SSH al momento della creazione del cluster. Se non viene specificato, il servizio aprirà o chiuderà in modo intelligente la porta a seconda che il cluster sia distribuito o meno all'interno di una rete virtuale.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Aggiunta della possibilità di recuperare l'URL di firma di accesso condiviso da modellare nell'archiviazione tramite l'oggetto modello. Ad esempio, model. [get_sas_url()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introduzione all'esecuzione. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] per ottenere i set di dati associati all'esecuzione inviata
    + Aggiungere l'API `Dataset.Tabular` .[ from_json_lines_files() per](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) creare un oggetto TabularDataset dai file Di righe JSON. Per informazioni su questi dati tabulari nei file JSON Lines in TabularDataset, vedere https://aka.ms/azureml-data la documentazione.
    + Sono stati aggiunti campi aggiuntivi per le dimensioni della macchina virtuale (disco del sistema operativo, numero di GPU) [alla funzione supported_vmsizes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Sono stati aggiunti campi aggiuntivi [alla funzione list_nodes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) per visualizzare l'esecuzione, l'indirizzo IP privato e pubblico, la porta e così via.
    + Possibilità di specificare un nuovo campo durante il [provisioning](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)  del cluster che può essere impostato su abilitato o disabilitato a seconda che si desideri lasciare aperta o chiusa la porta SSH al momento della creazione del cluster. Se non viene specificato, il servizio aprirà o chiuderà in modo intelligente la porta a seconda che il cluster sia distribuito o meno all'interno di una rete virtuale.
  + **azureml-explain-model**
    + Documentazione migliorata per gli output explanation nello scenario di classificazione.
    + Aggiunta della possibilità di caricare i valori y stimati nella spiegazione per gli esempi di valutazione. Sblocca visualizzazioni più utili.
    + Aggiunta della proprietà explainer a MimicWrapper per consentire il recupero dell'oggetto MimicExplainer sottostante.
  + **azureml-pipeline-core**
    + Aggiunta del notebook per descrivere Module, ModuleVersion e ModuleStep
  + **azureml-pipeline-steps**
    + Aggiunta di RScriptStep per supportare l'esecuzione di script R tramite la pipeline AML.
    + Correzione dell'analisi dei parametri dei metadati in AzureBatchStep che causava il messaggio di errore "L'assegnazione per il parametro SubscriptionId non è specificata".
  + **azureml-train-automl**
    + Supporto training_data, validation_data, label_column_name, weight_column_name come formato di input dei dati
    + Aggiunta del messaggio di deprecazione per explain_model() e retrieve_model_explanations()
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Aggiunta di [un notebook](https://aka.ms/pl-modulestep) per [descrivere Module](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion e [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + Aggiunta [di RScriptStep per](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) supportare l'esecuzione di script R tramite la pipeline AML.
    + Correzione dell'analisi dei parametri dei metadati in [AzureBatchStep che causava il messaggio di errore "Assegnazione per il parametro SubscriptionId non specificata".
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Supporto training_data, validation_data, label_column_name, weight_column_name come formato di input dei dati.
    + Aggiunta del messaggio di [deprecazione per explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) [e retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK per Python v1.0.62

+ **Nuove funzionalità**
  + È stato introdotto `timeseries`  il tratto in TabularDataset. Questo tratto consente di filtrare facilmente i timestamp sui dati di un oggetto TabularDataset, ad esempio prendendo tutti i dati tra un intervallo di tempo o i dati più recenti.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb per un notebook di esempio.
  + È stato abilitato il training con TabularDataset e FileDataset. 

  + **azureml-train-core**
      + Aggiunta `Nccl` e supporto nello `Gloo` estimator PyTorch

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + L'impostazione AutoML "lag_length" e LaggingTransformer sono state deprecate.
    + Correzione della convalida corretta dei dati di input se specificati in un formato flusso di dati
    + È stato modificato fit_pipeline.py per generare il file JSON del grafo e caricarlo in artefatti.
    + Rendering del grafo in `userrun` tramite `Cytoscape` .
  + **azureml-core**
    + È stata rivisitata la gestione delle eccezioni nel codice ADB e sono state apportate modifiche a in base alla nuova gestione degli errori
    + Aggiunta dell'autenticazione msi automatica per le macchine virtuali notebook.
    + Correzione del bug per cui è possibile caricare modelli danneggiati o vuoti a causa di tentativi non riusciti.
    + Correzione del bug in cui il nome cambia quando cambia la modalità , ad esempio quando `DataReference` si chiama , o `DataReference` `as_upload` `as_download` `as_mount` .
    + Rendere `mount_point` e `target_path` facoltativo per e `FileDataset.mount` `FileDataset.download` .
    + Eccezione per cui non è possibile trovare la colonna timestamp verrà generata se l'API correlata alla serie temporale viene chiamata senza una colonna timestamp fine assegnata o se le colonne timestamp assegnate vengono eliminate.
    + Le colonne seriali di ora devono essere assegnate con una colonna il cui tipo è Date. In caso contrario, è prevista un'eccezione
    + Le colonne seriali temporali che assegnano l'API 'with_timestamp_columns' possono usare il nome della colonna timestamp none value fine/coarse, in modo da cancellare le colonne timestamp assegnate in precedenza.
    + L'eccezione verrà generata quando viene eliminata una colonna con granularità grossolano o timestamp con granularità fine con indicazione per l'utente che l'eliminazione può essere eseguita dopo l'esclusione della colonna timestamp nell'elenco di rilascio o la chiamata with_time_stamp con il valore None per rilasciare le colonne timestamp
    + L'eccezione verrà generata quando la colonna timestamp grossolano o con granularità fine non è inclusa nell'elenco mantieni colonne con indicazione per l'utente che è possibile mantenere la colonna dopo aver incluso la colonna timestamp nell'elenco colonne keep o chiamare with_time_stamp con il valore None per rilasciare le colonne timestamp.
    + Aggiunta della registrazione per le dimensioni di un modello registrato.
  + **azureml-explain-model**
    + Correzione dell'avviso visualizzato nella console quando il pacchetto Python "packaging" non è installato: "Using older than supported version of lightgbm, please upgrade to version greater than 2.2.1"
    + Correzione della spiegazione del modello di download con partizionamento orizzontale per spiegazioni globali con molte funzionalità
    + Correzione degli esempi di inizializzazione mancanti del mimic explainer nella spiegazione dell'output
    + Correzione di un errore non modificabile nelle proprietà impostate durante il caricamento con il client spiegazione usando due tipi diversi di modelli
    + È stato aggiunto get_raw parametro di assegnazione dei punteggi a explainer.explain() in modo che uno spiegatore di assegnazione dei punteggi possa restituire sia valori elaborati che non elaborati.
  + **azureml-train-automl**
    + Sono state introdotte LE API pubbliche di AutoML per il supporto delle spiegazioni dell'SDK explain: un modo più recente per supportare le spiegazioni di AutoML disaccostando le funzionalità di AutoML e spiegando l'SDK - Supporto della spiegazione non elaborata integrato da azureml explain SDK per i `automl` modelli AutoML.
    + Rimozione di azureml-defaults da ambienti di training remoti.
    + Modifica del percorso predefinito dell'archivio cache da FileCacheStore basato su uno ad AzureFileCacheStore uno per AutoML Azure Databricks percorso del codice.
    + Correzione della convalida corretta dei dati di input se specificati in un formato flusso di dati
  + **azureml-train-core**
    + È stata source_directory_data_store deprecata.
    + Aggiunta della possibilità di eseguire l'override delle versioni del pacchetto installato azureml.
    + Aggiunta del supporto di dockerfile `environment_definition` nel parametro negli oggetti estimator.
    + Parametri di training distribuiti semplificati negli strumenti di stima.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nuova esperienza Web (anteprima) per le Azure Machine Learning lavoro
La nuova esperienza Web consente a data scientist e data engineer di completare il ciclo di vita di Machine Learning end-to-end, dalla preparazione e visualizzazione dei dati al training e alla distribuzione di modelli in un'unica posizione.

![Azure Machine Learning'interfaccia utente dell'area di lavoro (anteprima)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Funzionalità principali:**

Usando questa nuova Azure Machine Learning, è ora possibile:
+ Gestire i notebook o collegarsi a Jupyter
+ [Eseguire esperimenti di Machine Learning automatizzato](tutorial-first-experiment-automated-ml.md)
+ [Creare set di dati da file locali, archivi dati & file Web](how-to-create-register-datasets.md)
+ Esplorare & set di dati per la creazione del modello
+ Monitorare la deriva dei dati per i modelli
+ Visualizzare le risorse recenti da un dashboard

Al momento di questa versione sono supportati i browser seguenti: Chrome, Firefox, Safari e Microsoft Edge Preview.

**Problemi noti:**

1. Aggiornare il browser se viene visualizzato il messaggio "Si è verificato un errore. Errore durante il caricamento dei file di blocco" durante la distribuzione.

1. Non è possibile eliminare o rinominare file in Notebook e file. Durante l'anteprima pubblica, è possibile usare l'interfaccia utente di Jupyter o il terminale nella macchina virtuale notebook per eseguire operazioni di aggiornamento dei file. Poiché si tratta di una rete montata file system tutte le modifiche, le modifiche apportate alla macchina virtuale notebook vengono riflesse immediatamente nell'area di lavoro notebook.

1. Per eseguire SSH nella macchina virtuale Notebook:
   1. Trovare le chiavi SSH create durante l'installazione della macchina virtuale. In caso contrario, trovare le chiavi nell'area di lavoro Azure Machine Learning > aprire la scheda Calcolo > individuare La macchina virtuale notebook nell'elenco > aprire le relative proprietà: copiare le chiavi dalla finestra di dialogo.
   1. Importare le chiavi SSH pubbliche e private nel computer locale.
   1. Usarli per eseguire SSH nella macchina virtuale Notebook.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK per Python v1.0.60

+ **Nuove funzionalità**
  + È stato introdotto FileDataset, che fa riferimento a singoli o più file negli archivi dati o negli URL pubblici. I file possono essere di qualsiasi formato. FileDataset consente di scaricare o montare i file nel calcolo. 
  + Aggiunta del supporto Yaml della pipeline per il passaggio PythonScript, il passaggio di Adla, il passaggio di Databricks, DataTransferStep e il passaggio di AzureBatch

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + AutoArima è ora una pipeline suggerita solo per l'anteprima.
    + Miglioramento della segnalazione degli errori per la previsione.
    + Miglioramento della registrazione tramite eccezioni personalizzate anziché generiche nelle attività di previsione.
    + È stato rimosso il controllo max_concurrent_iterations deve essere inferiore al numero totale di iterazioni.
    + I modelli AutoML ora restituiscono AutoMLExceptions
    + Questa versione migliora le prestazioni di esecuzione delle esecuzioni locali di Machine Learning automatizzato.
  + **azureml-core**
    + Introduzione Dataset.get_all(area di lavoro), che restituisce un dizionario di oggetti `TabularDataset` e con chiave in base al nome di `FileDataset` registrazione.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Introdurre `parition_format` come argomento per e `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . Le informazioni sulla partizione di ogni percorso dati verranno estratte in colonne in base al formato specificato. '{column_name}' crea una colonna stringa e '{column_name:a/MM/gg/HH/mm/ss}' crea la colonna datetime, dove 'yyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' vengono usati per estrarre anno, mese, giorno, ora, minuto e secondo per il tipo datetime. Il partition_format deve iniziare dalla posizione della prima chiave di partizione fino alla fine del percorso del file. Ad esempio, dato il percorso '.. /USA/2019/01/01/data.csv' in cui la partizione è in base al paese e all'ora, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' crea la colonna stringa 'Country' con valore 'USA' e la colonna datetime 'PartitionDate' con valore '2019-01-01'.
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Introdurre `partition_format` come argomento per e `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . Le informazioni sulla partizione di ogni percorso dati verranno estratte in colonne in base al formato specificato. '{column_name}' crea una colonna stringa e '{column_name:a/MM/gg/HH/mm/ss}' crea la colonna datetime, dove 'yyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' vengono usati per estrarre anno, mese, giorno, ora, minuto e secondo per il tipo datetime. Il partition_format deve iniziare dalla posizione della prima chiave di partizione fino alla fine del percorso del file. Ad esempio, dato il percorso '.. /USA/2019/01/01/data.csv' in cui la partizione è in base al paese e all'ora, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' crea la colonna stringa 'Country' con valore 'USA' e la colonna datetime 'PartitionDate' con valore '2019-01-01'.
    + `to_csv_files` I `to_parquet_files` metodi e sono stati aggiunti a `TabularDataset` . Questi metodi consentono la conversione `TabularDataset` tra e `FileDataset` convertendo i dati in file con il formato specificato.
    + Accedere automaticamente al registro immagini di base quando si salva un Dockerfile generato da Model.package().
    + 'gpu_support' non è più necessario. AML ora rileva e usa automaticamente l'estensione docker nvidia quando è disponibile. Verrà rimosso in una versione futura.
    + Aggiunta del supporto per la creazione, l'aggiornamento e l'uso di PipelineDrafts.
    + Questa versione migliora le prestazioni di esecuzione delle esecuzioni locali di Machine Learning automatizzate.
    + Gli utenti possono eseguire query sulle metriche dalla cronologia di esecuzione in base al nome.
    + È stata migliorata la registrazione usando eccezioni personalizzate anziché generiche nelle attività di previsione.
  + **azureml-explain-model**
    + Aggiunta feature_maps parametro al nuovo MimicWrapper, consentendo agli utenti di ottenere spiegazioni delle funzionalità non elaborate.
    + I caricamenti di set di dati sono ora disattivati per impostazione predefinita per il caricamento della spiegazione e possono essere ri abilitati con upload_datasets=True
    + Aggiunta dei parametri is_law filtro "is_law" all'elenco di spiegazioni e alle funzioni di download.
    + Aggiunge il `get_raw_explanation(feature_maps)` metodo agli oggetti spiegazione globale e locale.
    + Aggiunta del controllo della versione a lightgbm con avviso stampato se la versione supportata è inferiore a
    + Utilizzo ottimizzato della memoria durante l'invio in batch di spiegazioni
    + I modelli AutoML ora restituiscono AutoMLExceptions
  + **azureml-pipeline-core**
    + Aggiunta del supporto per la creazione, l'aggiornamento e l'uso di PipelineDrafts: può essere usato per mantenere le definizioni di pipeline modificabili e usarle in modo interattivo per l'esecuzione
  + **azureml-train-automl**
    + Funzionalità creata per installare versioni specifiche di pytorch v1.1.0, :::no-loc text="cuda"::: toolkit 9.0, pytorch-transformers, necessarie per abilitare BERT/XLNet nell'ambiente di runtime Python remoto.
  + **azureml-train-core**
    + Errore anticipato di alcuni errori di definizione dello spazio degli iperparamersi direttamente nell'SDK anziché sul lato server.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Correzioni di bug e miglioramenti**
  + È stata abilitata la scrittura in ADLS/ADLSGen2 usando il percorso non elaborato e le credenziali.
  + Correzione di un bug che `include_path=True` causava il non funzionamento di per `read_parquet` .
  + Correzione `to_pandas_dataframe()` dell'errore causato dall'eccezione "Valore di proprietà non valido: hostSecret".
  + Correzione di un bug per cui non era possibile leggere i file in DBFS in modalità Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK per Python v1.0.57
+ **Nuove funzionalità**
  + Abilitato `TabularDataset` per essere utilizzato da AutomatedML. Per altre informazioni su `TabularDataset` , visitare https://aka.ms/azureml/howto/createdatasets .

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + È ora possibile aggiornare il certificato TLS/SSL per l'endpoint di assegnazione dei punteggi distribuito nel cluster del servizio Web Diaks sia per il certificato generato da Microsoft che per quello del cliente.
  + **azureml-automl-core**
    + Risolto un problema in AutoML per cui le righe con etichette mancanti non venivano rimosse correttamente.
    + Miglioramento della registrazione degli errori in AutoML; I messaggi di errore completi verranno ora sempre scritti nel file di log.
    + AutoML ha aggiornato il blocco del pacchetto per includere `azureml-defaults` `azureml-explain-model` , e `azureml-dataprep` . AutoML non avvisa più in caso di pacchetti non corrispondenti (ad eccezione del `azureml-train-automl` pacchetto).
    + Risolto un problema in a causa del quale le divisioni cv sono di dimensioni `timeseries`  non uguali causando l'esito negativo del calcolo del contenitore.
    + Quando si esegue l'iterazione dell'insieme per il tipo di training convalida incrociata, se si verificano problemi durante il download dei modelli di cui è stato eseguito il training sull'intero set di dati, si verifica un'incoerenza tra i pesi del modello e i modelli che venivano immessi nell'insieme di voto.
    + Correzione dell'errore, generato quando le etichette di training e/o di convalida (y e y_valid) vengono fornite sotto forma di frame di dati pandas, ma non come matrice numpy.
    + Correzione del problema relativo alle attività di previsione quando viene rilevata nessuna nelle colonne booleane delle tabelle di input.
    + Consentire agli utenti di AutoML di eliminare le serie di training che non sono sufficientemente lunghe durante la previsione. - Consente agli utenti di AutoML di eliminare i grani dal set di test che non esiste nel set di training durante la previsione.
  + **azureml-core**
    + È stato risolto un problema blob_cache_timeout ordinamento dei parametri.
    + Aggiunta dell'adattamento esterno e trasformazione dei tipi di eccezione agli errori di sistema.
    + Aggiunta del supporto per Key Vault per le esecuzioni remote. Aggiungere una classe azureml.core.keyvault.Keyvault per aggiungere, ottenere ed elencare i segreti dal keyvault associato all'area di lavoro. Le operazioni supportate sono:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(nome, valore)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(nome)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Metodi aggiuntivi per ottenere la chiave predefinita e ottenere i segreti durante l'esecuzione remota:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(nome)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Aggiunta di parametri di override aggiuntivi al comando dell'interfaccia della riga di comando submit-hyperdrive.
    + Migliorare l'affidabilità delle chiamate API espandendo i tentativi alle eccezioni della libreria delle richieste comuni.
    + Aggiungere il supporto per l'invio di esecuzioni da un'esecuzione inviata.
    + È stato risolto un problema di scadenza del token di firma di accesso condiviso in FileWatcher, che causava l'arresto del caricamento dei file dopo la scadenza del token iniziale.
    + È supportata l'importazione di file CSV/tsv HTTP nel set di dati Python SDK.
    + Il metodo Workspace.setup() è stato deprecato. Il messaggio di avviso visualizzato agli utenti suggerisce invece l'uso di create() o get()/from_config().
    + Aggiunta Environment.add_private_pip_wheel(), che consente di caricare pacchetti Python personalizzati privati nell'area di lavoro e di usarli in modo sicuro per `whl` compilare/materializzare l'ambiente.
    + È ora possibile aggiornare il certificato TLS/SSL per l'endpoint di assegnazione dei punteggi distribuito nel cluster del servizio Web Diaks sia per il certificato generato da Microsoft che per quello del cliente.
  + **azureml-explain-model**
    + Aggiunta del parametro per aggiungere un ID modello alle spiegazioni al caricamento.
    + Aggiunta `is_raw` di tag alle spiegazioni in memoria e caricamento.
    + Aggiunta del supporto di pytorch e dei test per il pacchetto azureml-explain-model.
  + **azureml-opendatasets**
    + Supporto del rilevamento e della registrazione dell'ambiente di test automatico.
    + Sono state aggiunte classi per ottenere la popolazione degli Stati Uniti per regione e capa.
  + **azureml-pipeline-core**
    + Aggiunta della proprietà label alle definizioni delle porte di input e output.
  + **azureml-telemetry**
    + Correzione di una configurazione di telemetria non corretta.
  + **azureml-train-automl**
    + Correzione del bug per cui in caso di errore di installazione, l'errore non viene registrato nel campo "errors" per l'esecuzione dell'installazione e quindi non viene archiviato nell'esecuzione padre "errors".
    + Risolto un problema in AutoML per cui le righe con etichette mancanti non venivano rimosse correttamente.
    + Consentire agli utenti di AutoML di eliminare le serie di training che non sono sufficientemente lunghe durante la previsione.
    + Consentire agli utenti di AutoML di eliminare i granulari dal set di test che non esiste nel set di training durante la previsione.
    + AutoMLStep passa ora la configurazione al back-end per evitare eventuali problemi relativi a modifiche o `automl` aggiunte di nuovi parametri di configurazione.
    + AutoML Data Guardrail è ora disponibile in anteprima pubblica. L'utente potrà visualizzare un report di Data Guardrail (per le attività di classificazione/regressione) dopo il training e sarà anche in grado di accedervi tramite l'API SDK.
  + **azureml-train-core**
    + Aggiunta del supporto torch 1.2 in PyTorch Estimator.
  + **azureml-widgets**
    + Grafici a matrice di confusione migliorati per il training della classificazione.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Nuove funzionalità**
  + Gli elenchi di stringhe possono ora essere passati come input ai `read_*` metodi.

+ **Correzioni di bug e miglioramenti**
  + Le prestazioni di `read_parquet` sono state migliorate durante l'esecuzione in Spark.
  + È stato risolto un problema a causa del quale l'operazione non è riuscita in caso di una singola `column_type_builder` colonna con formati di data ambigui.

### <a name="azure-portal"></a>Portale di Azure
+ **Funzionalità di anteprima**
  + Lo streaming dei file di log e di output è ora disponibile per le pagine dei dettagli di esecuzione. I file trasmettere gli aggiornamenti in tempo reale quando l'interruttore di anteprima è attivato.
  + La possibilità di impostare la quota a livello di area di lavoro viene rilasciata in anteprima. Le quote AmlCompute vengono allocate a livello di sottoscrizione, ma è ora possibile distribuire tale quota tra le aree di lavoro e allocarla per la condivisione equa e la governance. È sufficiente fare clic **sul pannello Usages+Quotas (Utilizzi+Quote)** nella barra di spostamento sinistra dell'area di lavoro e selezionare **la scheda Configure Quotas (Configura** quote). È necessario essere un amministratore della sottoscrizione per poter impostare quote a livello di area di lavoro perché si tratta di un'operazione tra aree di lavoro.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK per Python v1.0.55

+ **Nuove funzionalità**
  + L'autenticazione basata su token è ora supportata per le chiamate effettuate all'endpoint di assegnazione dei punteggi distribuito nel servizio AKS. Continueremo a supportare l'autenticazione basata sulla chiave corrente e gli utenti potranno usare uno di questi meccanismi di autenticazione alla volta.
  + Possibilità di registrare un archivio BLOB dietro la rete virtuale (VNet) come archivio dati.

+ **Correzioni di bug e miglioramenti**
  + **azureml-automl-core**
    + Correzione di un bug in cui le dimensioni di convalida per le divisioni cv sono ridotte e restituisce grafici stimati non corretti e veri per la regressione e la previsione.
    + È stata migliorata la registrazione delle attività di previsione nelle esecuzioni remote, ora all'utente viene fornito un messaggio di errore completo se l'esecuzione non è riuscita.
    + Correzione degli errori di `Timeseries` se il flag di pre-elaborazione è True.
    + Alcuni messaggi di errore di convalida dei dati di previsione sono stati resi più utilizzabili.
    + Riduzione del consumo di memoria delle esecuzioni autoML eliminando e/o caricando lazy dei set di dati, in particolare tra le operazioni di generazione del processo
  + **azureml-contrib-explain-model**
    + Aggiunta model_task flag agli spiegatori per consentire all'utente di eseguire l'override della logica di inferenza automatica predefinita per il tipo di modello
    + Modifiche del widget: viene installato automaticamente con , non più installato/abilitato: spiegazione del supporto con importanza delle `contrib` `nbextension` funzionalità globale (ad esempio, Permutative)
    + Modifiche del dashboard: - Box plot e tracciati di top-k oltre ai tracciati nella pagina di riepilogo- Molto più veloce il rerendering del tracciato sul dispositivo di scorrimento "Top -k" - messaggio utile che spiega come viene calcolato `beeswarm` top-k - Utili messaggi personalizzabili al posto dei grafici quando i dati non vengono forniti `beeswarm`
  + **azureml-core**
    + Aggiunta del metodo Model.package() per creare immagini Docker e Dockerfile che incapsulano i modelli e le relative dipendenze.
    + Aggiornamento dei servizi Web locali per accettare InferenceConfig contenenti oggetti Environment.
    + Correzione di Model.register() che produce modelli non validi quando '.' (per la directory corrente) viene passato come model_path parametro .
    + Aggiungere Run.submit_child, la funzionalità rispecchia Experiment.submit specificando l'esecuzione come padre dell'esecuzione figlio inviata.
    + Supporto delle opzioni di configurazione da Model.register in Run.register_model.
    + Possibilità di eseguire processi JAR in un cluster esistente.
    + Supporto di instance_pool_id e cluster_log_dbfs_path parametri.
    + Aggiunta del supporto per l'uso di un oggetto Environment durante la distribuzione di un modello in un servizio Web. L'oggetto Environment può ora essere fornito come parte dell'oggetto InferenceConfig.
    + Aggiungere il mapping appinsifht per le nuove aree - centralus - westus - northcentralus
    + Aggiunta della documentazione per tutti gli attributi in tutte le classi datastore.
    + Aggiunta blob_cache_timeout parametro a `Datastore.register_azure_blob_container` .
    + Aggiunta save_to_directory e load_from_directory metodi in azureml.core.environment.Environment.
    + Aggiunta dei comandi "az ml environment download" e "az ml environment register" all'interfaccia della riga di comando.
    + Aggiunta Environment.add_private_pip_wheel metodo .
  + **azureml-explain-model**
    + Aggiunta del rilevamento del set di dati a Explanations usando il servizio Set di dati (anteprima).
    + Riduzione delle dimensioni predefinite del batch durante lo streaming di spiegazioni globali da 10.00 a 100.
    + Aggiunta model_task flag agli explainer per consentire all'utente di eseguire l'override della logica di inferenza automatica predefinita per il tipo di modello.
  + **azureml-mlflow**
    + Correzione di un bug mlflow.azureml.build_image in cui le directory annidate vengono ignorate.
  + **azureml-pipeline-steps**
    + Aggiunta della possibilità di eseguire processi JAR in un cluster Azure Databricks esistente.
    + Aggiunta del supporto instance_pool_id e cluster_log_dbfs_path parametri per il passaggio DatabricksStep.
    + Aggiunta del supporto per i parametri della pipeline nel passaggio DatabricksStep.
  + **azureml-train-automl**
    + Aggiunta `docstrings` per i file correlati a Ensemble.
    + Aggiornamento della documentazione alla lingua più appropriata per `max_cores_per_iteration` e `max_concurrent_iterations`
    + La registrazione delle attività di previsione nelle esecuzioni remote è stata migliorata, ora all'utente viene fornito un messaggio di errore completo se l'esecuzione non è riuscita.
    + Rimozione get_data dal notebook della `automlstep` pipeline.
    + Supporto avviato `dataprep` in `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Nuove funzionalità**
  + È ora possibile richiedere di eseguire controlli specifici (ad esempio istogramma, grafico a dispersione e così via) in colonne specifiche.
  + Aggiunta di un argomento parallelize a `append_columns` . Se True, i dati verranno caricati in memoria, ma l'esecuzione verrà eseguita in parallelo. se False, l'esecuzione verrà in streaming ma a thread singolo.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK per Python v1.0.53

+ **Nuove funzionalità**
  + L'Machine Learning ora supporta il training di modelli ONNX nella destinazione di calcolo remota
  + Azure Machine Learning ora consente di riprendere il training da un'esecuzione precedente, un checkpoint o file di modello.
    + Informazioni su come [usare gli estimator per riprendere il training da un'esecuzione precedente](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Correzioni di bug e miglioramenti**
  + **azure-cli-ml**
    + I comandi dell'interfaccia della riga di comando "model deploy" e "service update" ora accettano parametri, file di configurazione o una combinazione dei due. I parametri hanno la precedenza rispetto agli attributi nei file.
    + La descrizione del modello può ora essere aggiornata dopo la registrazione
  + **azureml-automl-core**
    + Aggiornare la dipendenza NimbusML alla versione 1.2.0 (versione più recente corrente).
    + Aggiunta del supporto per gli oggetti estimator NimbusML & pipeline da usare all'interno degli oggetti estimator AutoML.
    + Correzione di un bug nella procedura di selezione dell'ensemble che aumentava inutilmente l'insieme risultante anche se i punteggi rimanevano costanti.
    + Abilitare il riutilizzo di alcune evolzioni di funzionalità tra divisioni CV per le attività di previsione. In questo modo si accelera il tempo di esecuzione dell'installazione di circa un fattore di n_cross_validations per le funzionalità costose, ad esempio ritardi e finestre in sequenza.
    + Risolvere un problema se l'ora non è compreso nell'intervallo di tempo supportato da pandas. Ora viene generata un'eccezione DataException se il tempo è inferiore a pd. Timestamp.min o maggiore di pd. Timestamp.max
    + La previsione ora consente frequenze diverse nei set di training e di test, se possono essere allineate. Ad esempio, è possibile allineare "trimestrale a partire da gennaio" e alle "trimestrali a partire da ottobre".
    + La proprietà "parameters" è stata aggiunta a TimeSeriesTransformer.
    + Rimuovere le classi di eccezione precedente.
    + Nelle attività di previsione, il parametro accetta ora un singolo valore `target_lags` integer o un elenco di numeri interi. Se è stato specificato il numero intero, verrà creato un solo ritardo. Se viene fornito un elenco, verranno utilizzati i valori univoci dei ritardi. target_lags=[1, 2, 2, 4] creerà ritardi di uno, due e quattro periodi.
    + Correzione del bug sulla perdita di tipi di colonne dopo la trasformazione (bug collegato);
    + In , y_query essere un tipo di oggetto contenente Nessuno `model.forecast(X, y_query)` all'inizio (#459519).
    + Aggiungere i valori previsti `automl` all'output
  + **azureml-contrib-datadrift**
    +  Miglioramenti al notebook di esempio che include il passaggio ad azureml-opendatasets anziché ai set di dati azureml-contrib-opendataset e miglioramenti delle prestazioni durante l'arricchimento dei dati
  + **azureml-contrib-explain-model**
    + Correzione dell'argomento transformations per lo strumento di spiegazione LIME per l'importanza della funzionalità non elaborata nel pacchetto azureml-contrib-explain-model
    + Sono state aggiunte segmentazioni alle spiegazioni delle immagini nello strumento di spiegazione delle immagini per il pacchetto AzureML-contrib-explain-model
    + Aggiungere il supporto sparse scipy per LimeExplainer
    + Aggiunta a mimic explainer quando , per lo streaming di spiegazioni globali in batch per migliorare il tempo di esecuzione `batch_size` `include_local=False` di DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Correzione per la chiamata set_featurizer_timeseries_params(): modifica del tipo di valore dict e controllo null - Aggiungere un notebook per `timeseries`  featurizer
    + Aggiornare la dipendenza NimbusML alla versione 1.2.0 (più recente).
  + **azureml-core**
    + Aggiunta della possibilità di collegare archivi dati DBFS nell'interfaccia della riga di comando di AzureML
    + Correzione del bug con il caricamento dell'archivio dati in cui viene creata una cartella vuota se `target_path` avviata con `/`
    + È `deepcopy` stato risolto un problema in ServicePrincipalAuthentication.
    + Aggiunta dei comandi "az ml environment show" e "az ml environment list" all'interfaccia della riga di comando.
    + Gli ambienti supportano ora la specifica di base_dockerfile come alternativa a un ambiente già base_image.
    + L'impostazione RunConfiguration inutilizzata auto_prepare_environment è stata contrassegnata come deprecata.
    + La descrizione del modello può ora essere aggiornata dopo la registrazione
    + Correzione di bug: l'eliminazione di modelli e immagini offre ora altre informazioni sul recupero di oggetti upstream che dipendono da essi se l'eliminazione non riesce a causa di una dipendenza upstream.
    + Correzione del bug che stampava una durata vuota per le distribuzioni che si verificavano durante la creazione di un'area di lavoro per alcuni ambienti.
    + Miglioramento delle eccezioni di errore per la creazione dell'area di lavoro. In questo modo gli utenti non visualizzano "Impossibile creare l'area di lavoro. Impossibile trovare..." come messaggio e visualizzano invece l'errore di creazione effettivo.
    + Aggiunta del supporto per l'autenticazione basata su token nei servizi Web del servizio Diaks.
    + Aggiungere `get_token()` il metodo agli oggetti `Webservice` .
    + Aggiunta del supporto dell'interfaccia della riga di comando per gestire i set di dati di Machine Learning.
    + `Datastore.register_azure_blob_container` ora accetta facoltativamente un valore (in secondi) che configura i parametri di montaggio di blobfuse per abilitare la scadenza `blob_cache_timeout` della cache per questo archivio dati. Il valore predefinito è nessun timeout, ad esempio quando un BLOB viene letto, rimarrà nella cache locale fino al termine del processo. La maggior parte dei processi preferisce questa impostazione, ma alcuni processi devono leggere più dati da un set di dati di grandi dimensioni rispetto a quelli che si adatteranno ai relativi nodi. Per questi processi, l'ottimizzazione di questo parametro consente di avere esito positivo. Quando si esegue l'ottimizzazione di questo parametro, l'impostazione di un valore troppo basso può comportare prestazioni scarse, in quanto i dati usati in un periodo possono scadere prima di essere usati di nuovo. Tutte le operazioni di lettura verranno eseguite dall'archiviazione BLOB o dalla rete anziché dalla cache locale, con un impatto negativo sui tempi di training.
    + La descrizione del modello può ora essere aggiornata correttamente dopo la registrazione
    + L'eliminazione di modelli e immagini offre ora altre informazioni sugli oggetti upstream che dipendono da essi, causando l'esito negativo dell'eliminazione
    + Migliorare l'utilizzo delle risorse delle esecuzioni remote usando azureml.mlflow.
  + **azureml-explain-model**
    + Correzione dell'argomento transformations per lo strumento di spiegazione LIME per l'importanza della funzionalità non elaborata nel pacchetto azureml-contrib-explain-model
    + aggiungere il supporto sparse scipy per LimeExplainer
    + aggiunta del wrapper di spiegazione lineare della forma, nonché di un altro livello di spiegazione tabulare per la spiegazione dei modelli lineari
    + per la spiegazione mimica nella libreria di modelli explain, correzione dell'errore quando include_local=False per l'input di dati di tipo sparse
    + aggiungere i valori previsti `automl` all'output
    + Fisso l'importanza della funzionalità di permutazione quando viene fornito l'argomento transformations per ottenere l'importanza della funzionalità non elaborata
    + aggiunta a mimic explainer quando , per lo streaming di spiegazioni globali in batch per migliorare il tempo di esecuzione `batch_size` `include_local=False` di DecisionTreeExplainableModel
    + per la libreria di spiegabilità del modello, fisso blackbox explainers in cui l'input del frame di dati pandas è necessario per la stima
    + Correzione di un bug in cui a volte `explanation.expected_values` restituisce un float anziché un elenco con un float al suo posto.
  + **azureml-mlflow**
    + Migliorare le prestazioni mlflow.set_experiment(experiment_name)
    + Correzione di un bug nell'uso di InteractiveLoginAuthentication per mlflow tracking_uri
    + Migliorare l'utilizzo delle risorse delle esecuzioni remote usando azureml.mlflow.
    + Migliorare la documentazione del pacchetto azureml-mlflow
    + Bug di patch in cui mlflow.log_artifacts("my_dir") salva gli elementi in "my_dir/<artifact-paths>" anziché "<artifact-paths>"
  + **azureml-opendatasets**
    + Aggiungere `pyarrow` a versioni precedenti `opendatasets` (<0.14.0) a causa di un problema di memoria appena introdotto.
    + Spostare azureml-contrib-opendatasets in azureml-opendatasets.
    + Consentire la registrazione delle classi di set di dati aperte Azure Machine Learning'area di lavoro e sfruttare facilmente le funzionalità del set di dati AML.
    + Migliorare notevolmente le prestazioni di NoaaIsdWeather nella versione non SPARK.
  + **azureml-pipeline-steps**
    + DbFS Datastore è ora supportato per input e output in DatabricksStep.
    + Aggiornamento della documentazione Azure Batch passaggio per quanto riguarda input/output.
    + In AzureBatchStep il valore *delete_batch_job_after_finish* predefinito è stato modificato in *true.*
  + **azureml-telemetry**
    +  Spostare azureml-contrib-opendatasets in azureml-opendatasets.
    + Consentire la registrazione delle classi di set di dati aperte nell Azure Machine Learning area di lavoro e sfruttare facilmente le funzionalità del set di dati di AML.
    + Migliorare in modo significativo le prestazioni noaaIsdWeather nella versione non SPARK.
  + **azureml-train-automl**
    + Aggiornamento della documentazione get_output per riflettere il tipo restituito effettivo e fornire note aggiuntive sul recupero delle proprietà chiave.
    + Aggiornare la dipendenza NimbusML alla versione 1.2.0 (versione più recente corrente).
    + aggiungere i valori previsti `automl` all'output
  + **azureml-train-core**
    + Le stringhe vengono ora accettate come destinazione di calcolo per l'ottimizzazione automatica degli iperparamemi
    + L'impostazione RunConfiguration inutilizzata auto_prepare_environment è stata contrassegnata come deprecata.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Nuove funzionalità**
  + Aggiunta del supporto per la lettura di un file direttamente da un URL HTTP o HTTPS.

+ **Correzioni di bug e miglioramenti**
  + Miglioramento del messaggio di errore durante il tentativo di leggere un set di dati Parquet da un'origine remota (attualmente non supportata).
  + Correzione di un bug durante la scrittura nel formato di file Parquet in ADLS Gen 2 e l'aggiornamento del nome del contenitore di ADLS Gen 2 nel percorso.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interfaccia visiva grafica
+ **Funzionalità di anteprima**
  + È stato aggiunto il modulo "Execute R script" (Esegui script R) nell'interfaccia visiva grafica.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK per Python v1.0.48

+ **Nuove funzionalità**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** è ora disponibile come **azureml-opendatasets**. Il pacchetto precedente può comunque funzionare, ma è consigliabile usare **azureml-opendataset per** migliorare le funzionalità e i miglioramenti.
    + Questo nuovo pacchetto consente di registrare i set di dati aperti come set di dati nell Azure Machine Learning area di lavoro e di sfruttare le funzionalità offerte dal set di dati.
    + Include anche funzionalità esistenti, ad esempio l'utilizzo di set di dati aperti come frame di dati Pandas/SPARK e join di posizione per alcuni set di dati, ad esempio meteo.

+ **Funzionalità di anteprima**
    + HyperDriveConfig può ora accettare l'oggetto pipeline come parametro per supportare l'ottimizzazione degli iperparametre tramite una pipeline.

+ **Correzioni di bug e miglioramenti**
  + **azureml-train-automl**
    + Correzione del bug sulla perdita di tipi di colonne dopo la trasformazione.
    + Correzione del bug per consentire y_query essere un tipo di oggetto contenente Nessuno/i all'inizio.
    + È stato risolto il problema nella procedura di selezione dell'insieme che aumentava inutilmente l'insieme risultante anche se i punteggi rimanevano costanti.
    + È stato risolto il problema relativo alle impostazioni list_models e blocca list_models in AutoMLStep.
    + È stato risolto il problema che impediva l'uso della pre-elaborazione quando AutoML sarebbe stato usato nel contesto delle pipeline di Azure ML.
  + **azureml-opendatasets**
    + Spostamento di azureml-contrib-opendatasets in azureml-opendatasets.
    + È stata consentita la registrazione delle classi di set di dati aperti Azure Machine Learning'area di lavoro e sfruttare facilmente le funzionalità del set di dati AML.
    + NoaaIsdWeather migliorato migliora notevolmente le prestazioni nella versione non SPARK.
  + **azureml-explain-model**
    + Aggiornamento della documentazione online per gli oggetti di interpretabilità.
    + Aggiunta a mimic explainer quando , per lo streaming di spiegazioni globali in batch per migliorare il tempo di esecuzione di `batch_size` `include_local=False` DecisionTreeExplainableModel per la libreria di spiegabilità del modello.
    + È stato risolto il problema a causa del quale a volte restituiva un tipo float anziché `explanation.expected_values` un elenco con un valore float.
    + Aggiunta dei valori previsti `automl` all'output per lo spiegazione mimica nella libreria del modello explain.
    + Correzione dell'importanza della funzionalità di permutazione quando viene fornito l'argomento transformations per ottenere l'importanza della funzionalità non elaborata.
  + **azureml-core**
    + Aggiunta della possibilità di collegare archivi dati DBFS nell'interfaccia della riga di comando di AzureML.
    + Correzione del problema relativo al caricamento dell'archivio dati per cui viene creata una cartella vuota `target_path` se avviata con `/` .
    + È stato abilitato il confronto di due set di dati.
    + Eliminazione di modelli e immagini offre ora altre informazioni sul recupero di oggetti upstream che dipendono da essi se l'eliminazione non riesce a causa di una dipendenza upstream.
    + L'impostazione RunConfiguration inutilizzata è stata deprecata in auto_prepare_environment.
  + **azureml-mlflow**
    + Miglioramento dell'utilizzo delle risorse delle esecuzioni remote che usano azureml.mlflow.
    + Miglioramento della documentazione del pacchetto azureml-mlflow.
    + Risolto il problema per cui mlflow.log_artifacts("my_dir") salvava gli artefatti in "my_dir/artifact-paths" anziché in "artifact-paths".
  + **azureml-pipeline-core**
    + I hash_paths per tutti i passaggi della pipeline sono deprecati e verranno rimossi in futuro. Per impostazione predefinita, viene eseguito l source_directory di hashing del contenuto del file , ad eccezione dei file elencati in `.amlignore` o `.gitignore` .
    + Miglioramento continuo di Module e ModuleStep per supportare moduli specifici del tipo di calcolo, per preparare l'integrazione di RunConfiguration e altre modifiche per sbloccare l'utilizzo di moduli specifici del tipo di calcolo nelle pipeline.
  + **azureml-pipeline-steps**
    + AzureBatchStep: documentazione migliorata per quanto riguarda input/output.
    + AzureBatchStep: modifica delete_batch_job_after_finish valore predefinito in true.
  + **azureml-train-core**
    + Le stringhe vengono ora accettate come destinazione di calcolo per l'ottimizzazione automatica degli iperparamemi.
    + L'impostazione RunConfiguration inutilizzata è stata deprecata in auto_prepare_environment.
    + Parametri deprecati `conda_dependencies_file_path` `pip_requirements_file_path` e rispettivamente a favore di `conda_dependencies_file` e `pip_requirements_file` .
  + **azureml-opendatasets**
    + Migliorare in modo significativo le prestazioni noaaIsdWeather nella versione non SPARK.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK per Python v1.0.33 rilasciato.

+ Azure ML Modelli con accelerazione hardware su [FPGA è](how-to-deploy-fpga-web-service.md) disponibile a livello generale.
  + È ora possibile [usare il pacchetto azureml-accel-models](how-to-deploy-fpga-web-service.md) per:
    + Eseguire il training dei pesi di una rete neurale profonda supportata (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Usare l'apprendimento per il trasferimento con la DNN supportata
    + Registrare il modello con Gestione modelli e creare un contenitore del modello
    + Distribuire il modello in una macchina virtuale di Azure con fpga in un cluster servizio Azure Kubernetes (AKS)
  + Distribuire il contenitore in un [dispositivo Azure Data Box server Edge](../databox-online/azure-stack-edge-overview.md)
  + Punteggio dei dati con l'endpoint gRPC con questo [esempio](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Machine Learning automatizzato

+ Sweep delle funzionalità per abilitare l'aggiunta dinamica per :::no-loc text="featurizers"::: l'ottimizzazione delle prestazioni. Nuovo: :::no-loc text="featurizers"::: incorporamenti di lavoro, peso dell'evidenza, codifiche di destinazione, codifica della destinazione del testo, distanza del cluster
+ Cv intelligente per gestire le divisioni di training/valide all'interno di Machine Learning automatizzato
+ Poche modifiche all'ottimizzazione della memoria e miglioramento delle prestazioni di runtime
+ Miglioramento delle prestazioni nella spiegazione del modello
+ Conversione del modello ONNX per l'esecuzione locale
+ Aggiunta del supporto per il sottocampionamento
+ Arresto intelligente quando non sono definiti criteri di uscita
+ Insiemi in pila

+ Previsione di serie temporali
  + Nuova funzione di previsione di stima
  + È ora possibile usare la convalida incrociata dell'origine in sequenza nei dati di serie tempor.
  + Aggiunta di una nuova funzionalità per configurare i ritardi delle serie tempo reale
  + Aggiunta di una nuova funzionalità per supportare le funzionalità di aggregazione delle finestre in sequenza
  + Nuove funzionalità e rilevamento delle festività quando il codice paese è definito nelle impostazioni dell'esperimento

+ Azure Databricks
  + Funzionalità di spiegazione/interpretabilità del modello e previsione di serie temporità abilitate
  + È ora possibile annullare e riprendere (continuare) gli esperimenti automatizzati di Machine Learning
  + Aggiunta del supporto per l'elaborazione multicore

### <a name="mlops"></a>MLOps
+ **Distribuzione locale & debug per l'assegnazione dei punteggi ai contenitori**<br/> È ora possibile distribuire un modello di Machine Learning in locale ed eseguire rapidamente l'iterazione sul file di assegnazione dei punteggi e sulle dipendenze per assicurarsi che si comportino come previsto.

+ **Introduzione di InferenceConfig & Model.deploy()**<br/> La distribuzione del modello supporta ora la specifica di una cartella di origine con uno script di immissione, uguale a un RunConfig.  Inoltre, la distribuzione del modello è stata semplificata in un unico comando.

+ **Rilevamento dei riferimenti Git**<br/> I clienti richiedono da tempo funzionalità di integrazione git di base perché consentono di mantenere una soluzione audit trail. È stato implementato il rilevamento tra le entità principali in Azure ML per i metadati correlati a Git (repository, commit, stato pulito). Queste informazioni verranno raccolte automaticamente dall'SDK e dall'interfaccia della riga di comando.

+ **Profilatura del modello & di convalida**<br/> I clienti spesso si lamentno della difficoltà di ridimensionare correttamente le risorse di calcolo associate al servizio di inferenza. Con il servizio di profilatura del modello, il cliente può fornire input di esempio ed eseguiremo la profilatura in 16 diverse configurazioni di CPU/memoria per determinare il dimensionamento ottimale per la distribuzione.

+ **Bring Your Own Image di base per l'inferenza**<br/> Un altro reclamo comune era la difficoltà di passare dalla sperimentazione all'inferenza, condividendo le dipendenze. Con la nuova funzionalità di condivisione delle immagini di base, è ora possibile riutilizzare le immagini di base della sperimentazione, le dipendenze e tutto, per l'inferenza. Questo dovrebbe velocizzare le distribuzioni e ridurre il gap dal ciclo interno al ciclo esterno.

+ **Miglioramento dell'esperienza di generazione dello schema Swagger**<br/> Il metodo di generazione precedente di swagger era ergasto e impossibile da automatizzare. È stato creato un nuovo modo in linea per generare schemi swagger da qualsiasi funzione Python tramite elementi Decorator. Questo codice è stato open source e il protocollo di generazione dello schema non è associato alla piattaforma Azure ML.

+ **interfaccia della riga di comando di Azure ML disponibile a livello generale**<br/> I modelli possono ora essere distribuiti con un singolo comando dell'interfaccia della riga di comando. È stato ricevuto un feedback comune dei clienti che nessuno distribuisce un modello di Machine Learning da un notebook di Jupyter. La [**documentazione di riferimento dell'interfaccia della riga**](./reference-azure-machine-learning-cli.md) di comando è stata aggiornata.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK per Python v1.0.30 rilasciato.

È [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint) stato introdotto per aggiungere una nuova versione di una pipeline pubblicata mantenendo lo stesso endpoint.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portale di Azure
  + È ora possibile inviare nuovamente un'esecuzione di script esistente in un cluster di calcolo remoto esistente.
  + È ora possibile eseguire una pipeline pubblicata con nuovi parametri nella scheda Pipeline.
  + I dettagli di esecuzione supportano ora un nuovo visualizzatore di file snapshot. È possibile visualizzare uno snapshot della directory quando è stata inviata un'esecuzione specifica. È anche possibile scaricare il notebook inviato per avviare l'esecuzione.
  + È ora possibile annullare le esecuzioni padre dal portale di Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK per Python v1.0.23

+ **Nuove funzionalità**
  + L Azure Machine Learning SDK supporta ora Python 3.7.
  + Azure Machine Learning estimator della DNN offrono ora il supporto integrato per più versioni. Ad esempio, estimator ora accetta un parametro e gli utenti possono specificare la versione `TensorFlow` `framework_version` '1.10' o '1.12'. Per un elenco delle versioni supportate dalla versione corrente dell'SDK, chiamare `get_supported_versions()` sulla classe del framework desiderata, ad esempio `TensorFlow.get_supported_versions()` .
  Per un elenco delle versioni supportate dalla versione più recente dell'SDK, vedere la documentazione dello strumento di stima [DNN.](/python/api/azureml-train-core/azureml.train.dnn)

## <a name="2019-03-25"></a>25/03/2019

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK per Python v1.0.21

+ **Nuove funzionalità**
  + Il *azureml.core.Run.create_children* consente la creazione a bassa latenza di più esecuzioni figlio con una singola chiamata.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK per Python v1.0.18

 + **Modifiche**
   + Il pacchetto azureml-tensorboard sostituisce azureml-contrib-tensorboard.
   + Con questa versione è possibile configurare un account utente nel cluster di calcolo gestito (amlcompute) durante la creazione. Questa operazione può essere eseguita passando queste proprietà nella configurazione del provisioning. Per altri dettagli, vedere la documentazione [di riferimento dell'SDK.](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Nuove funzionalità**
  + Supporta ora l'aggiunta di due colonne numeriche per generare una colonna risultante usando il linguaggio delle espressioni.

+ **Correzioni di bug e miglioramenti**
  + Miglioramento della documentazione e del controllo dei parametri per random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Correzione di bug**
  + È stato risolto un problema di autenticazione dell'entità servizio causato da una modifica dell'API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK per Python v1.0.17

+ **Nuove funzionalità**
  + Azure Machine Learning ora fornisce il supporto di prima classe per il chainer del framework DNN più diffuso. Gli [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer) utenti della classe possono eseguire facilmente il training e la distribuzione di modelli Chainer.
    + Informazioni su come [eseguire il training distribuito con ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Informazioni su come eseguire [l'ottimizzazione degli iperparamersi con Chainer con HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pipeline ha aggiunto la possibilità di attivare un'esecuzione della pipeline in base alle modifiche dell'archivio dati. Il notebook [della pianificazione della pipeline](https://aka.ms/pl-schedule) viene aggiornato per presentare questa funzionalità.

+ **Correzioni di bug e miglioramenti**
  + È stato aggiunto il supporto nelle pipeline Azure Machine Learning per l'impostazione della proprietà source_directory_data_store su un archivio dati desiderato ( ad esempio un archivio BLOB) in [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration) forniti a [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep). Per impostazione predefinita, Passaggi usa l'archivio file di Azure come archivio dati di backup, che può causare problemi di limitazione quando un numero elevato di passaggi viene eseguito contemporaneamente.

### <a name="azure-portal"></a>Portale di Azure

+ **Nuove funzionalità**
  + Nuova esperienza dell'editor di tabelle di trascinamento della selezione per i report. Gli utenti possono trascinare una colonna dall'area della tabella all'area della tabella in cui verrà visualizzata un'anteprima della tabella. Le colonne possono essere ridisporte.
  + Visualizzatore file nuovi log
  + Collegamenti a esecuzioni di esperimenti, calcolo, modelli, immagini e distribuzioni dalla scheda attività

## <a name="next-steps"></a>Passaggi successivi

Leggere la panoramica di [Azure Machine Learning](overview-what-is-azure-ml.md).
