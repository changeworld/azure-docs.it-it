---
title: Proteggere l'accesso ai dati nel cloud
titleSuffix: Azure Machine Learning
description: Informazioni su come connettersi in modo sicuro all'archiviazione dei dati in Azure con Azure Machine Learning archivi dati e set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 601be8409db22162a410d481e6609d378718a7b4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503590"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Proteggere l'accesso ai dati in Azure Machine Learning

Azure Machine Learning semplifica la connessione ai dati nel cloud. Fornisce un livello di astrazione sul servizio di archiviazione sottostante, quindi è possibile accedere in modo sicuro ai dati e utilizzarli senza dover scrivere codice specifico per il tipo di archiviazione. Azure Machine Learning fornisce anche le funzionalità dei dati seguenti:

*    Interoperabilità con Pandas e dataframe di Spark
*    Controllo delle versioni e rilevamento della derivazione dei dati
*    Etichettatura dei dati 
*    Monitoraggio della deriva dei dati
    
## <a name="data-workflow"></a>Flusso di lavoro dei dati

Quando si è pronti per usare i dati nella soluzione di archiviazione basata su cloud, si consiglia il seguente flusso di lavoro di distribuzione dei dati. Questo flusso di lavoro presuppone che si disponga di un [account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal) e di dati in un servizio di archiviazione basato su cloud in Azure. 

1. Creare un [archivio dati Azure Machine Learning](#datastores) per archiviare le informazioni di connessione nell'archiviazione di Azure.

2. Da tale archivio dati, creare un [set di dati Azure Machine Learning](#datasets) in modo che punti a uno o più file specifici nell'archivio sottostante. 

3. Per usare tale set di dati nell'esperimento di Machine Learning, è possibile
    1. Montarlo nella destinazione di calcolo dell'esperimento per il training del modello.

        **OR** 

    1. È sufficiente utilizzarlo direttamente in soluzioni Azure Machine Learning come, esecuzioni di esperimenti automatizzati di Machine Learning (automatico ML), pipeline di Machine Learning o [progettazione Azure Machine Learning](concept-designer.md).

4. Creare [monitoraggi del set](#drift) di dati per il set di dati di output del modello per rilevare la tendenza dei dati. 

5. Se viene rilevata la deriva dei dati, aggiornare il set di dati di input e ripetere il training del modello di conseguenza.

Il diagramma seguente fornisce una dimostrazione visiva di questo flusso di lavoro consigliato.

![Il diagramma mostra il servizio di archiviazione di Azure che scorre in un archivio dati, che scorre in un set di dati. Il set di dati passa al training del modello, che passa alla deviazione dei dati, che esegue il flusso al set di dati.](./media/concept-data/data-concept-diagram.svg)

<a name="datastores"></a>
## <a name="connect-to-storage-with-datastores"></a>Connettersi all'archiviazione con archivi dati

Azure Machine Learning archivi dati conservano in modo sicuro le informazioni di connessione all'archivio dati in Azure, quindi non è necessario codificarlo negli script. [Registrare e creare un archivio](how-to-access-data.md) dati per connettersi facilmente all'account di archiviazione e accedere ai dati nel servizio di archiviazione sottostante. 

Servizi di archiviazione basati su cloud supportati in Azure che possono essere registrati come archivi dati:

+ Contenitore BLOB di Azure
+ Condivisione file di Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Database SQL di Azure
+ Database di Azure per PostgreSQL
+ File system di Databricks
+ Database di Azure per MySQL

>[!TIP]
> La funzionalità disponibile a livello generale per la creazione di archivi dati richiede l'autenticazione basata su credenziali per l'accesso ai servizi di archiviazione, ad esempio un'entità servizio o un token di firma di accesso condiviso (SAS). È possibile accedere a queste credenziali da utenti che dispongono dell'accesso in *lettura* all'area di lavoro. <br><br>Se si tratta di un problema,  [creare un archivio dati che usa l'accesso ai dati basato su identità ai servizi di archiviazione (anteprima)](how-to-identity-based-data-access.md). Questa funzionalità è una funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/#stable-vs-experimental) e può cambiare in qualsiasi momento.

<a name="datasets"></a>
## <a name="reference-data-in-storage-with-datasets"></a>Riferimenti ai dati nell'archiviazione con DataSet

Azure Machine Learning set di dati non sono copie dei dati. Creando un set di dati, si crea un riferimento ai dati nel relativo servizio di archiviazione, insieme a una copia dei relativi metadati. 

Poiché i set di dati vengono valutati in modo differito e i dati rimangono nella posizione esistente,

* Non sostenere costi di archiviazione aggiuntivi.
* Non è rischioso modificare involontariamente le origini dati originali.
* Migliorare le velocità di prestazioni del flusso di lavoro ML.

Per interagire con i dati nell'archiviazione, [creare un set](how-to-create-register-datasets.md) di dati per creare un pacchetto dei dati in un oggetto utilizzabile per le attività di machine learning. Registrare il set di dati nell'area di lavoro per condividerlo e riutilizzarlo in diversi esperimenti senza complessi di inserimento di dati.

I set di dati possono essere creati da file locali, URL pubblici, set di dati [aperti di Azure](https://azure.microsoft.com/services/open-datasets/)o servizi di archiviazione di Azure tramite archivi dati. 

Sono disponibili 2 tipi di set di impostazioni: 

+ Un [filedataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) fa riferimento a uno o più file negli archivi dati o negli URL pubblici. Se i dati sono già puliti e pronti per l'uso negli esperimenti di training, è possibile [scaricare o montare i file](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) a cui fa riferimento filedatasets nella destinazione di calcolo.

+ Un [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) rappresenta i dati in formato tabulare analizzando il file o l'elenco di file fornito. È possibile caricare un TabularDataset in un dataframe Pandas o Spark per eseguire ulteriori operazioni di manipolazione e pulizia. Per un elenco completo dei formati di dati da cui è possibile creare TabularDatasets, vedere la [classe TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory).

Le funzionalità di set di impostazioni aggiuntive sono disponibili nella documentazione seguente:

+ Derivazione del set [di dati Version e Track](how-to-version-track-datasets.md) .
+ Consente di [monitorare il set](how-to-monitor-datasets.md) di dati per semplificare il rilevamento della tendenza.    

## <a name="work-with-your-data"></a>Usare i dati

Con i set di elementi, è possibile eseguire numerose attività di machine learning grazie a una perfetta integrazione con Azure Machine Learning funzionalità. 

+ Creare un [progetto di assegnazione di etichette dati](#label).
+ Eseguire il training di modelli di Machine Learning:
     + [esperimenti di Machine Learning automatici](how-to-use-automated-ml-for-ml-models.md)
     + [finestra di progettazione](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Notebooks](how-to-train-with-datasets.md)
     + [Pipeline di Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)
+ Accedere ai set di impostazioni per l'assegnazione dei punteggi con l' [inferenza batch](./tutorial-pipeline-batch-scoring-classification.md) nelle [pipeline di Machine Learning](./how-to-create-machine-learning-pipelines.md).
+ Configurare un monitoraggio del set di [dati](#drift) per il rilevamento della deriva.

<a name="label"></a>

## <a name="label-data-with-data-labeling-projects"></a>Etichettare i dati con i progetti di etichettatura dei dati

L'etichettatura di grandi quantità di dati nei progetti di Machine Learning è spesso stata considerata un'attività molto complessa. Quelli con un componente visione artificiale, ad esempio la classificazione delle immagini o il rilevamento di oggetti, richiedono in genere migliaia di immagini e etichette corrispondenti.

Azure Machine Learning offre una posizione centrale per creare, gestire e monitorare i progetti di etichettatura. I progetti di etichettatura consentono di coordinare i dati, le etichette e i membri del team, per una gestione più efficiente delle attività di etichettatura. Le attività attualmente supportate sono la classificazione delle immagini, multi-etichetta o multi-classe, e l'identificazione di oggetti tramite i riquadri di selezione.

Creare un [progetto di assegnazione di etichette dei dati](how-to-create-labeling-projects.md)e restituire un set di dati da usare negli esperimenti di machine learning.

<a name="drift"></a>

## <a name="monitor-model-performance-with-data-drift"></a>Monitorare le prestazioni del modello con la tendenza dei dati

Nel contesto di Machine Learning, la deriva dei dati è la modifica dei dati di input del modello che comporta un peggioramento delle prestazioni del modello. Si tratta di uno dei motivi principali per cui l'accuratezza del modello diminuisce nel tempo, di conseguenza il monitoraggio della deriva dei dati consente di rilevare problemi di prestazioni del modello.

Vedere l'articolo [creare un monitor del set](how-to-monitor-datasets.md) di dati per altre informazioni su come rilevare e inviare avvisi ai dati in un set di dati.

## <a name="next-steps"></a>Passaggi successivi 

+ Creare un set di dati in Azure Machine Learning Studio o con Python SDK [seguendo questa procedura.](how-to-create-register-datasets.md)
+ Provare gli esempi di training del set di dati con i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).