---
title: 'Esercitazione: Estrarre i dati di moduli in blocco con Azure Data Factory - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: Configurare le attività di Azure Data Factory per attivare il training e l'esecuzione di modelli di Riconoscimento modulo per digitalizzare un backlog di documenti di grandi dimensioni.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 1780aebc113fa68a9a89cfce9fd67c9b5911fc58
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606671"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>Esercitazione: Estrarre i dati di moduli in blocco con Azure Data Factory

In questa esercitazione verrà illustrato come usare Servizi di Azure per inserire un batch di moduli di grandi dimensioni in contenuto multimediale. Questa esercitazione illustra come automatizzare l'inserimento dati da un data lake di documenti di Azure Data Lake in un database SQL di Azure. Saranno sufficienti pochi clic per eseguire rapidamente il training dei modelli ed elaborare nuovi documenti.

## <a name="business-need"></a>Esigenza aziendale

La maggior parte delle organizzazioni è ora consapevole dell'importanza dei dati presenti in formati diversi (PDF, immagini, video). È quindi alla ricerca delle procedure consigliate e delle soluzioni più convenienti per digitalizzare tali asset.

I clienti si trovano inoltre a gestire spesso diversi tipi di moduli provenienti dai clienti. Diversamente dalle guide di [avvio rapido](./quickstarts/client-library.md), in questa esercitazione viene illustrato come eseguire automaticamente il training di un modello con tipi nuovi e diversi di moduli usando un approccio basato sui metadati. Se non esiste già un modello per il tipo di modulo specificato, il sistema ne creerà uno e fornirà l'ID del modello. 

Estraendo i dati dai moduli e combinandoli ai sistemi e i data warehouse esistenti, le aziende possono ottenere dati analitici e produrre valore per i propri clienti e utenti aziendali.

Con il servizio di Riconoscimento modulo di Azure le organizzazioni possono sfruttare i propri dati, automatizzare i processi (fatturazione, elaborazione fiscale e così via), risparmiare denaro e tempo e ottenere dati ancora più accurati.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare Azure Data Lake per l'archiviazione dei moduli
> * Usare un database di Azure per creare una tabella di parametrizzazione
> * Usare Azure Key Vault per archiviare le credenziali sensibili
> * Eseguire il training di un modello di Riconoscimento modulo in un notebook di Databricks
> * Estrarre i dati del modulo usando un notebook di Databricks
> * Automatizzare il training del modulo e l'estrazione con Azure Data Factory

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="creare una risorsa di Riconoscimento modulo"  target="_blank">creare una risorsa di Riconoscimento modulo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Riconoscimento modulo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* Un set di almeno cinque moduli dello stesso tipo. Se possibile, questo flusso di lavoro dovrebbe supportare set di documenti di grandi dimensioni. Per suggerimenti e informazioni sulle opzioni per la creazione di un set di dati di training, vedere [Creare un set di dati di training](./build-training-data-set.md). Per questa esercitazione, è possibile usare i file inclusi nella cartella **Train** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2128080).

## <a name="project-architecture"></a>Architettura del progetto 

Questo progetto definisce un set di pipeline di Azure Data Factory per attivare notebook Python che consentono di eseguire il training, l'analisi e l'estrazione di dati da documenti in un account di archiviazione di Azure Data Lake.

Per l'API REST di Riconoscimento modulo è richiesta l'immissione di alcuni parametri. Per motivi di sicurezza, alcuni di questi parametri verranno archiviati in un Azure Key Vault, mentre altri parametri meno sensibili, come il nome della cartella del BLOB di archiviazione, verranno archiviati in una tabella di parametrizzazione in un database SQL di Azure.

Per ogni tipo di modulo da analizzare gli ingegneri dei dati o i data scientist compileranno una riga della tabella dei parametri. Useranno quindi Azure Data Factory per eseguire l'iterazione dell'elenco dei tipi di modulo rilevati e passare i parametri pertinenti a un notebook di Databricks per eseguire il training o ripetere il training dei modelli di Riconoscimento modulo. In questo punto è anche possibile usare una funzione di Azure.

Il notebook di Azure Databricks usa quindi i modelli sottoposti a training per estrarre i dati del modulo ed esporta tali dati in un database SQL di Azure.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="architettura del progetto":::


## <a name="set-up-azure-data-lake"></a>Configurare Azure Data Lake

Il backlog dei moduli può trovarsi nell'ambiente locale o in un server FTP. Per questa esercitazione si usano i moduli in un account di archiviazione di Azure Data Lake Gen 2. È possibile trasferire i file con Azure Data Factory, Azure Storage Explorer o AzCopy. I set di dati di training e assegnazione dei punteggi possono trovarsi in contenitori diversi, ma i set di dati di training per tutti i tipi di modulo devono trovarsi nello stesso contenitore, anche se in cartelle diverse.

Per creare un nuovo data lake, seguire le istruzioni in [Creare un account di archiviazione da usare con Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/create-data-lake-storage-account).

## <a name="create-a-parameterization-table"></a>Creare una tabella di parametrizzazione

Verrà ora creata una tabella di metadati in un database SQL di Azure. Questa tabella conterrà i dati non sensibili richiesti dall'API REST di Riconoscimento modulo. Ogni volta che nel set di dati è presente un nuovo tipo di modulo, verrà inserito un nuovo record in questa tabella e verrà attivata la pipeline di training e di assegnazione dei punteggi (che verrà implementata in un secondo momento).

Nella tabella verranno usati i campi seguenti:

* **form_description**: questo campo non è obbligatorio per il training. Fornisce una descrizione del tipo di moduli per il quale si sta eseguendo il training del modello, ad esempio "moduli cliente A", "modulo albergo B".
* **training_container_name**: questo campo corrisponde al nome del contenitore dell'account di archiviazione in cui è stato archiviato il set di dati di training. Può essere lo stesso contenitore specificato per **scoring_container_name**.
* **training_blob_root_folder**: indica la cartella nell'account di archiviazione in cui verranno archiviati i file per il training del modello.
* **scoring_container_name**: questo campo corrisponde al nome del contenitore dell'account di archiviazione in cui sono stati archiviati i file da cui si vogliono estrarre le coppie chiave-valore. Può essere lo stesso contenitore specificato per **training_container_name**.
* **scoring_input_blob_folder**: indica la cartella nell'account di archiviazione in cui verranno archiviati i file da cui estrarre i dati.
* **model_id**: indica l'ID del modello di cui si vuole ripetere il training. Per la prima esecuzione, il valore deve essere impostato su -1, in modo che il notebook di training crei un nuovo modello personalizzato di cui eseguire il training. Il notebook di training restituirà l'ID modello appena creato all'istanza di Azure Data Factory e, usando un'attività di stored procedure, questo valore verrà aggiornato nel database SQL di Azure.

  Quando si vuole inserire un nuovo tipo di modulo, è necessario reimpostare manualmente l'ID modello su -1 prima di eseguire il training del modello.

* **file_type**: i tipi di modulo supportati sono `application/pdf`, `image/jpeg`, `image/png` e `image/tif`.

  Se si hanno moduli con tipi di file diversi, sarà necessario modificare questo valore e quello di **model_id** quando si esegue il training di un nuovo tipo di modulo.
* **form_batch_group_id**: nel corso del tempo potrebbero esistere più tipi di modulo di cui si esegue il training per lo stesso modello. **form_batch_group_id** consente di specificare tutti i tipi di modulo di cui è stato eseguito il training usando un modello specifico.

### <a name="create-the-table"></a>Creare la tabella

[Creare un database SQL di Azure](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase) e quindi eseguire lo script SQL seguente nell'[editor di query](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal) per creare la tabella necessaria.

```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Eseguire lo script seguente per creare la routine per l'aggiornamento automatico di **model_id** dopo che ne è stato eseguito il training.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Usare Azure Key Vault per archiviare le credenziali sensibili

Per motivi di sicurezza, si preferisce non archiviare determinate informazioni sensibili nella tabella di parametrizzazione del database SQL di Azure. I parametri sensibili verranno quindi archiviati come segreti di Azure Key Vault.

### <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault

[Creare una risorsa Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Passare quindi alla risorsa Key Vault dopo che è stata creata e nella sezione **impostazioni** selezionare **segreti** per aggiungere i parametri.

Verrà visualizzata una nuova finestra. Selezionare **Genera/Importa**. Immettere il nome del parametro e il relativo valore e quindi fare clic su Crea. Eseguire questa operazione per i parametri seguenti:

* **CognitiveServiceEndpoint**: URL dell'endpoint dell'API di Riconoscimento modulo.
* **CognitiveServiceSubscriptionKey**: chiave di accesso del servizio Riconoscimento modulo. 
* **StorageAccountName**: account di archiviazione in cui sono archiviati i moduli e i set di dati di training da cui si vogliono estrarre le coppie chiave-valore. Se si trovano in account diversi, immettere ognuno dei nomi di account come segreti distinti. Tenere presente che i set di dati di training devono trovarsi nello stesso contenitore per tutti i tipi di modulo, anche se in cartelle diverse.
* **StorageAccountSasKey**: firma di accesso condiviso dell'account di archiviazione. Per recuperare l'URL di firma di accesso condiviso, passare alla risorsa di archiviazione e selezionare la scheda **Storage Explorer**. Passare al contenitore, fare clic con il pulsante destro del mouse e scegliere **Ottieni firma di accesso condiviso**. È importante ottenere la firma di accesso condiviso per il contenitore, non per l'account di archiviazione. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Eseguire il training di un modello di Riconoscimento modulo in un notebook di Databricks

Si userà Azure Databricks per archiviare ed eseguire il codice Python che interagisce con il servizio Riconoscimento modulo.

### <a name="create-a-notebook-in-databricks"></a>Creare un notebook in Databricks

[Creare una risorsa di Azure Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) nel portale di Azure. Passare alla risorsa dopo che è stata creata e avviare l'area di lavoro.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Creare un ambito dei segreti con supporto di Azure Key Vault

Per fare riferimento ai segreti nell'istanza di Azure Key Vault creata in precedenza, è necessario creare un ambito dei segreti in Databricks. Seguire la procedura descritta in [Creare un ambito dei segreti supportato da Azure Key Vault](https://docs.microsoft.com/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Creare un cluster di Databricks

Un cluster è una raccolta di risorse di calcolo di Databricks. Per creare un cluster:

1. Nella barra laterale fare clic sul pulsante **Cluster**.
1. Nella pagina **Cluster** fare clic su **Crea cluster**.
1. Nella pagina **Crea cluster** specificare un nome di cluster e selezionare **7.2 (Scala 2.12, Spark 3.0.0)** nell'elenco a discesa per la versione del runtime di Databricks.
1. Fare clic su **Crea cluster**.

### <a name="write-a-settings-notebook"></a>Scrivere un notebook di impostazioni

A questo punto è possibile aggiungere i notebook Python. Per prima cosa, creare un notebook denominato **Settings**. Questo notebook assegnerà i valori della tabella di parametrizzazione alle variabili presenti nello script. I valori verranno passati come parametri in un secondo momento da Azure Data Factory. Verranno inoltre eseguita l'assegnazione dei valori dei segreti nel Key Vault alle variabili. 

1. Per creare il notebook **Settings**, fare clic sul pulsante **area di lavoro**, nella nuova scheda fare clic sull'elenco a discesa e selezionare **crea** e quindi **notebook**.
1. Nella finestra popup immettere il nome che si vuole assegnare al notebook e selezionare  **Python** come linguaggio predefinito. Selezionare il cluster Databricks e quindi **Crea**.
1. Nella prima cella del notebook verranno recuperati i parametri passati da Azure Data Factory.

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. Nella seconda cella i segreti verranno recuperati da Key Vault e quindi assegnati alle variabili.

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Scrivere un notebook di training

Dopo aver completato il notebook **Settings**, è possibile creare un notebook per eseguire il training del modello. Come indicato in precedenza, verranno usati i file archiviati in una cartella in un account di archiviazione di Azure Data Lake Gen 2 (**training_blob_root_folder**). Il nome della cartella è stato passato come variabile. Ogni set di tipi di modulo sarà disponibile nella stessa cartella e, quando si eseguirà il ciclo della tabella dei parametri, verrà eseguito il training del modello usando tutti i tipi di modulo. 

1. Creare un nuovo notebook denominato **TrainFormRecognizer**. 
1. Nella prima cella eseguire il notebook di impostazioni:

    ```python
    %run "./Settings"
    ```

1. Nella cella successiva assegnare le variabili dal file **Settings** e quindi eseguire il training dinamico del modello per ogni tipo di modulo, applicando il codice nell'[avvio rapido REST](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. Nel passaggio finale del processo di training si otterrà il risultato del training in formato JSON.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-using-a-notebook"></a>Estrarre i dati del moduli con un notebook

### <a name="mount-the-azure-data-lake-storage"></a>Montare l'account di archiviazione di Azure Data Lake

Il passaggio successivo consiste nell'assegnare un punteggio ai diversi moduli disponibili con il modello sottoposto a training. L'account di archiviazione di Azure Data Lake verrà montato Databricks e fare riferimento al montaggio durante il processo di inserimento.

Proprio come nella fase di training, si userà Azure Data Factory per richiamare l'estrazione delle coppie chiave-valore dai moduli. Verrà eseguito il ciclo sui moduli presenti nelle cartelle specificate nella tabella dei parametri.

1. A questo punto verrà creato il notebook per montare l'account di archiviazione in Databricks. A tale notebook verrà assegnato il nome **MountDataLake**. 
1. È prima necessario chiamare il notebook **Settings**:

    ```python
    %run "./Settings"
    ```

1. Nella seconda cella verranno definite le variabili per i parametri sensibili, che verranno recuperati dai segreti del Key Vault.

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Successivamente, si proverà a smontare l'account di archiviazione nel caso in cui sia stato montato in precedenza.

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Verrà infine montato l'account di archiviazione.


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > È stato montato solo l'account di archiviazione di training. In questo caso i file di training e i file da cui estrarre le coppie chiave-valore sono disponibili nello account di archiviazione. Se gli account di archiviazione di assegnazione dei punteggi e training sono diversi, a questo punto sarà necessario montare entrambi gli account di archiviazione. 

### <a name="write-the-scoring-notebook"></a>Scrivere il notebook di assegnazione dei punteggi

A questo punto è possibile creare un notebook di assegnazione dei punteggi. Analogamente al notebook di training, verranno usati i file archiviati nelle cartelle dell'account di archiviazione di Azure Data Lake appena montato. Il nome della cartella viene passato come variabile. Verrà eseguito il ciclo di tutti i moduli nella cartella specificata e verranno estratte le coppie chiave-valore. 

1. Creare un nuovo notebook e assegnargli il nome **ScoreFormRecognizer**. 
1. Eseguire i notebook **Settings** e **MountDataLake**.

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Aggiungere quindi il codice seguente che chiama l'API [Analisi](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm).

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Nella cella successiva si otterranno i risultati dell'estrazione delle coppie chiave-valore. In questa cella verrà restituito il risultato. Dal momento che si vuole che il risultato in formato JSON venga elaborato ulteriormente nel database SQL di Azure o in Cosmos DB, il risultato verrà scritto in un file con estensione JSON. Il nome del file di output sarà il nome del file con punteggio, concatenato con "_output.json". Il file verrà archiviato nella stessa cartella del file di origine.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Eseguire la procedura di scrittura del file in una nuova cella:

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>Automatizzare il training e l'assegnazione dei punteggi con Azure Data Factory

L'unico passaggio rimanente consiste nel configurare il servizio Azure Data Factory per automatizzare i processi di training e assegnazione dei punteggi. Per prima cosa, seguire la procedura descritta in [Creare una data factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Dopo aver creato la risorsa Azure Data Factory, sarà necessario creare tre pipeline: una per il training e due per l'assegnazione dei punteggi (illustrata di seguito).

### <a name="training-pipeline"></a>Pipeline di training

La prima attività nella pipeline di training è una ricerca che consenta di leggere e restituire i valori della tabella di parametrizzazione nel database SQL di Azure. Dal momento che tutti i set di dati di training si troveranno nello stesso account di archiviazione e nello stesso contenitore (ma in cartelle potenzialmente diverse), verrà mantenuto il valore predefinito dell'attributo **First row only** (Solo prima riga) nelle impostazioni dell'attività di ricerca. Per ogni tipo di modulo per cui eseguire il training del modello, si eseguirà il training del modello usando tutti i file presenti in **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="pipeline di training nella data factory":::

La stored procedure accetta due parametri: **model_id** e **form_batch_group_id**. Il codice per restituire l'ID modello dal notebook di Databricks è `dbutils.notebook.exit(model_id)` e il codice per leggere il codice nell'attività della stored procedure nella data factory è `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id`.

### <a name="scoring-pipelines"></a>Pipeline di assegnazione dei punteggi

Per estrarre le coppie chiave-valore, verranno analizzate tutte le cartelle incluse nella tabella di parametrizzazione e, per ogni cartella, verranno estratte le coppie chiave-valore di tutti i file in essa contenuti. Al momento, Azure Data Factory non supporta cicli ForEach annidati. Verranno invece create due pipeline. La prima pipeline eseguirà la ricerca dalla tabella di parametrizzazione e passerà l'elenco di cartelle come parametro alla seconda pipeline.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="prima pipeline di assegnazione dei punteggi nella data factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="prima pipeline di assegnazione dei punteggi nella data factory, dettagli":::

La seconda pipeline userà un'attività GetMeta per ottenere l'elenco dei file nella cartella e lo passerà come parametro al notebook Databricks di assegnazione dei punteggi.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="seconda pipeline di assegnazione dei punteggi nella data factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="seconda pipeline di assegnazione dei punteggi nella data factory, dettagli":::

### <a name="specify-a-degree-of-parallelism"></a>Specificare un grado di parallelismo

Nelle pipeline di training e di assegnazione dei punteggi è possibile specificare il grado di parallelismo per elaborare più moduli contemporaneamente.

Per impostare il grado di parallelismo nella pipeline Azure Data Factory:

* Selezionare l'attività ForEach.
* Deselezionare la casella **Sequenziale**.
* Impostare il grado di parallelismo nella casella di testo **Numero di batch**. Per l'assegnazione dei punteggi è consigliabile un numero massimo di batch pari a 15.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="configurazione del parallelismo per l'attività di assegnazione dei punteggi in Azure Data Factory":::

## <a name="how-to-use"></a>Uso

A questo punto si ha una pipeline automatizzata per digitalizzare il backlog dei moduli ed eseguire alcune analisi. Quando si aggiungono nuovi moduli di un tipo noto a una cartella di archiviazione esistente, è sufficiente eseguire di nuovo le pipeline di assegnazione dei punteggi e verranno aggiornati tutti i file di output, inclusi i file di output per i nuovi moduli. 

Se si aggiungono nuovi moduli di un nuovo tipo, sarà necessario caricare anche un set di dati di training nel contenitore appropriato. Aggiungere quindi una nuova riga nella tabella di parametrizzazione, immettendo i percorsi dei nuovi documenti e il relativo set di dati di training. Immettere il valore -1 per **model_ID** per indicare che è necessario eseguire il training di un nuovo modello per tali moduli. Eseguire la pipeline di training in Azure Data Factory. Dopo la lettura dalla tabella, verrà eseguito il training di un modello e l'ID modello nella tabella verrà sovrascritto. È quindi possibile chiamare le pipeline di assegnazione dei punteggi per iniziare a scrivere i file di output.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si sono configurate le pipeline di Azure Data Factory per attivare il training e l'esecuzione di modelli di Riconoscimento modulo per digitalizzare un backlog di file di grandi dimensioni. Esaminare quindi l'API di Riconoscimento modulo per scoprire quali altri operazioni è possibile eseguire.

* [API REST di Riconoscimento modulo](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
