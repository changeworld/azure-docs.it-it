---
title: 'ML Studio (classico): eseguire la migrazione al set di dati di ricompilazione Azure Machine Learning'
description: Ricompila i set di impostazioni di studio (classico) in Azure Machine Learning Designer
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 4c04dd5a2b41b3db54b20c9e514767453951cc35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565044"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Eseguire la migrazione di un set di dati di studio (classico) a Azure Machine Learning

Questo articolo illustra come eseguire la migrazione di un set di dati di studio (classico) a Azure Machine Learning. Per altre informazioni sulla migrazione da studio (classico), vedere [l'articolo Panoramica della migrazione](migrate-overview.md).

Sono disponibili tre opzioni per eseguire la migrazione di un set di dati a Azure Machine Learning. Leggere ogni sezione per determinare quale opzione è migliore per lo scenario in uso.


|Dove sono i dati? | Opzione di migrazione  |
|---------|---------|
|In studio (classico)     |  Opzione 1: [scaricare il set di dati da studio (classico) e caricarlo in Azure Machine Learning](#download-the-dataset-from-studio-classic).      |
|Archiviazione nel cloud     | Opzione 2: [registrare un set di dati da un'origine cloud](#import-data-from-cloud-sources). <br><br>  Opzione 3: [usare il modulo Import data per ottenere i dati da un'origine cloud](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning supporta anche [flussi di lavoro Code-First](../how-to-create-register-datasets.md) per la creazione e la gestione di DataSet. 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un'area di lavoro di Azure Machine Learning. [Creare un'area di lavoro di Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Set di dati di studio (classico) di cui eseguire la migrazione.


## <a name="download-the-dataset-from-studio-classic"></a>Scaricare il set di dati da studio (classico)

Il modo più semplice per eseguire la migrazione di un set di dati di studio (classico) a Azure Machine Learning consiste nel scaricare il set di dati e registrarlo nel Azure Machine Learning. In questo modo viene creata una nuova copia del set di dati che viene caricata in un archivio dati Azure Machine Learning.

È possibile scaricare direttamente i tipi di set di dati di studio (classico) seguenti.

* Testo normale (con estensione txt)
* Valori delimitati da virgole (CSV) con un'intestazione (con estensione csv) o senza intestazione (con estensione nh.csv)
* Valori separati da tabulazioni (TSV) con un'intestazione (con estensione tsv) o senza intestazione (con estensione nh.tsv)
* File di Excel
* File ZIP (con estensione zip)

Per scaricare i set di impostazioni direttamente:
1. Passare all'area di lavoro di studio (classica) ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Nella barra di spostamento a sinistra selezionare la scheda **set di impostazioni** .
1. Selezionare i set di dati che si desidera scaricare.
1. Nella barra delle azioni inferiore selezionare **Scarica**.

    ![Screenshot che illustra come scaricare un set di dati in studio (classico)](./media/migrate-register-dataset/download-dataset.png)

Per i tipi di dati seguenti, è necessario usare il modulo **Convert to CSV** per scaricare i set di dati.

* Dati SVMLight (. SVMLight) 
* Dati ARFF (attribute relation file Format) (. ARFF) 
* File dell’oggetto o dell'area di lavoro R (con estensione RData)
* Tipo di set di dati (. Data). Il tipo di set di dati è di tipo studio (classico) interno per l'output del modulo.

Per convertire il set di dati in un file CSV e scaricare i risultati:

1. Passare all'area di lavoro di studio (classica) ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Creare un nuovo esperimento.
1. Trascinare e rilasciare il set di dati che si desidera scaricare nell'area di disegno.
1. Aggiungere un modulo **Convert to CSV** .
1. Connettere la porta di input **Convert to CSV** alla porta di output del set di dati.
1. Eseguire l'esperimento.
1. Fare clic con il pulsante destro del mouse sul modulo **Convert to CSV** .
1. Selezionare **risultati download set di dati**  >  .

    ![Screenshot che illustra come configurare una pipeline Convert to CSV](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Caricare il set di dati in Azure Machine Learning

Dopo aver scaricato il file di dati, è possibile registrare il set di dati in Azure Machine Learning:

1. Passare a Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Nel riquadro di spostamento a sinistra selezionare la scheda **set di impostazioni** .
1. Selezionare **Crea set**  >  **di dati da file locali**.
    ![Screenshot che mostra la scheda set di impostazioni e il pulsante per la creazione di un file locale](./media/migrate-register-dataset/register-dataset.png)
1. Immettere un nome e una descrizione.
1. Per **tipo di set di dati** selezionare **tabulare**.

    > [!NOTE]
    > È anche possibile caricare i file ZIP come set di dati. Per caricare un file ZIP, selezionare **file** per **tipo di set di dati**.

1. In **archivio dati e selezione file** selezionare l'archivio dati in cui si vuole caricare il file del set di dati.

    Per impostazione predefinita, Azure Machine Learning archivia il set di dati nell'area di lavoro predefinita blobstore. Per altre informazioni sugli archivi dati, vedere [connettersi ai servizi di archiviazione](../how-to-access-data.md).

1. Impostare le impostazioni di analisi dei dati e lo schema per il set di dati. Confermare quindi le impostazioni.

## <a name="import-data-from-cloud-sources"></a>Importa dati da origini cloud

Se i dati sono già presenti in un servizio di archiviazione cloud e si desidera conservare i dati nel percorso nativo. È possibile utilizzare una delle opzioni seguenti:

|Metodo di inserimento|Descrizione|
|---| --- |
|Registrare un set di dati di Azure Machine Learning|Inserire dati da origini dati locali e online (BLOB, ADLS Gen1, ADLS Gen2, condivisione file, database SQL). <br><br>Crea un riferimento all'origine dati, che viene valutato in modo differito in fase di esecuzione. Utilizzare questa opzione se si accede ripetutamente a questo set di dati e si desidera abilitare funzionalità avanzate per i dati, ad esempio il controllo delle versioni dei dati
|Modulo Import Data|Inserire dati da origini dati online (BLOB, ADLS Gen1, ADLS Gen2, condivisione file, database SQL). <br><br> Il set di dati viene importato solo nell'esecuzione della pipeline della finestra di progettazione corrente.


>[!Note]
> Gli utenti di studio (classico) devono tenere presente che le origini cloud seguenti non sono supportate in modo nativo in Azure Machine Learning:
> - Hive Query
> - tabella di Azure
> - Azure Cosmos DB
> - Database SQL locale
>
> Si consiglia agli utenti di eseguire la migrazione dei dati in un servizio di archiviazione supportato usando Azure Data Factory.  

### <a name="register-an-azure-machine-learning-dataset"></a>Registrare un set di dati di Azure Machine Learning

Usare la procedura seguente per registrare un set di dati per Azure Machine Learning da un servizio cloud: 

1. [Creare un archivio dati](../how-to-connect-data-ui.md#create-datastores)che collega il servizio di archiviazione cloud all'area di lavoro Azure Machine Learning. 

1. [Registrare un set di dati](../how-to-connect-data-ui.md#create-datasets). Se si esegue la migrazione di un set di dati di studio (classico), selezionare l'impostazione del set di dati **tabulare** .

Dopo aver registrato un set di dati in Azure Machine Learning, è possibile utilizzarlo nella finestra di progettazione:
 
1. Crea una nuova bozza della pipeline di progettazione.
1. Nella tavolozza dei moduli a sinistra espandere la sezione **DataSets (set di impostazioni** ).
1. Trascinare il set di dati registrato nell'area di disegno. 

### <a name="use-the-import-data-module"></a>Usare il modulo Import Data

Usare la procedura seguente per importare i dati direttamente nella pipeline di progettazione:

1. [Creare un archivio dati](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores)che collega il servizio di archiviazione cloud all'area di lavoro Azure Machine Learning. 

Dopo aver creato l'archivio dati, è possibile usare il modulo [**Import Data (Importa dati**](../algorithm-module-reference/import-data.md) ) nella finestra di progettazione per inserire i dati:

1. Crea una nuova bozza della pipeline di progettazione.
1. Nella tavolozza dei moduli a sinistra trovare il modulo **Import Data (Importa dati** ) e trascinarlo nell'area di disegno.
1. Selezionare il modulo **Import Data** e usare le impostazioni nel pannello di destra per configurare l'origine dati.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire la migrazione di un set di dati di studio (classico) a Azure Machine Learning. Il passaggio successivo consiste nel [ricompilare una pipeline di training di studio (classica)](migrate-rebuild-experiment.md).


Vedere gli altri articoli della serie di migrazione Studio (classica):

1. [Panoramica sulla migrazione](migrate-overview.md).
1. **Eseguire la migrazione dei set di impostazioni**.
1. [Ricompilare una pipeline di training di studio (classica)](migrate-rebuild-experiment.md).
1. [Ricompilare un servizio Web di studio (classico)](migrate-rebuild-web-service.md).
1. [Integrare un servizio web Azure Machine Learning con le app client](migrate-rebuild-integrate-with-client-app.md).
1. [Migrare Execute R script](migrate-execute-r-script.md).
