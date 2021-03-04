---
title: 'Esercitazione: Estrarre i dati di moduli in blocco con Azure Data Factory - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: Configurare Azure Data Factory attività per attivare il training e l'esecuzione di modelli di riconoscimento moduli e digitalizzare un backlog di grandi dimensioni dei documenti.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: d0c95312e1794e2f78bbbef217ef5530a993146d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040907"
---
# <a name="tutorial-extract-form-data-in-bulk-by-using-azure-data-factory"></a>Esercitazione: estrarre i dati del modulo in blocco usando Azure Data Factory

In questa esercitazione verrà illustrato come usare Servizi di Azure per inserire un batch di moduli di grandi dimensioni in contenuto multimediale. Nell'esercitazione viene illustrato come automatizzare l'inserimento di dati da un data Lake di Azure in un database SQL di Azure. Saranno sufficienti pochi clic per eseguire rapidamente il training dei modelli ed elaborare nuovi documenti.

## <a name="business-need"></a>Esigenza aziendale

La maggior parte delle organizzazioni è ora in grado di riconoscere il valore dei dati disponibili in vari formati (PDF, immagini, video). È quindi alla ricerca delle procedure consigliate e delle soluzioni più convenienti per digitalizzare tali asset.

Inoltre, i clienti hanno spesso diversi tipi di moduli che provengono da numerosi client e clienti. Diversamente dalle [guide introduttive](./quickstarts/client-library.md), in questa esercitazione viene illustrato come eseguire automaticamente il training di un modello con tipi nuovi e diversi di form utilizzando un approccio basato sui metadati. Se non si dispone di un modello esistente per il tipo di modulo specificato, il sistema ne creerà uno e fornirà l'ID modello. 

Estraendo i dati dai moduli e combinandoli ai sistemi e i data warehouse esistenti, le aziende possono ottenere dati analitici e produrre valore per i propri clienti e utenti aziendali.

Il riconoscimento di Azure Form consente alle organizzazioni di usare i propri dati, automatizzare i processi (pagamenti di fatture, elaborazioni fiscali e così via), risparmiare denaro e tempo e ottimizzare la precisione dei dati.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare Azure Data Lake per archiviare i moduli.
> * Usare un database di Azure per creare una tabella parametrizzazione.
> * Usare Azure Key Vault per archiviare le credenziali riservate.
> * Eseguire il training del modello di riconoscimento form in un notebook Azure Databricks.
> * Estrarre i dati del modulo usando un notebook di databricks.
> * Automatizzare il training e l'estrazione dei moduli usando Azure Data Factory.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/cognitive-services/).
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" creare una risorsa di riconoscimento "  target="_blank"> del modulo creare una risorsa di riconoscimento del modulo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> nella portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione della risorsa selezionare **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Riconoscimento modulo. La chiave e l'endpoint verranno incollati nel codice più avanti in questa Guida introduttiva.
    * È possibile usare il piano tariffario gratuito (F0) per provare il servizio. Sarà quindi possibile eseguire l'aggiornamento in un secondo momento a un livello a pagamento per la produzione.
* Un set di almeno cinque moduli dello stesso tipo. Se possibile, questo flusso di lavoro dovrebbe supportare set di documenti di grandi dimensioni. Vedere [creare un set di dati di training](./build-training-data-set.md) per suggerimenti e opzioni per riunire il set di dati di training. Per questa esercitazione, è possibile usare i file nella cartella Train del set di [dati di esempio](https://go.microsoft.com/fwlink/?linkid=2128080).

## <a name="project-architecture"></a>Architettura del progetto 

Questo progetto costituisce un set di pipeline di Azure Data Factory per attivare notebook Python che consentono di eseguire il training, l'analisi e l'estrazione di dati da documenti in un account di archiviazione Azure Data Lake.

L'API REST del riconoscitore del modulo richiede alcuni parametri come input. Per motivi di sicurezza, alcuni di questi parametri verranno archiviati in un insieme di credenziali delle chiavi di Azure. Altri parametri meno sensibili, come il nome della cartella BLOB di archiviazione, verranno archiviati in una tabella di parametrizzazione in un database SQL di Azure.

Per ogni tipo di modulo da analizzare, gli ingegneri dei dati o i data scientist popolano una riga della tabella dei parametri. Utilizzeranno quindi Azure Data Factory per scorrere l'elenco dei tipi di modulo rilevati e passare i parametri rilevanti a un notebook di databricks per eseguire il training o ripetere il training dei modelli di riconoscimento del modulo. In questo punto è anche possibile usare una funzione di Azure.

Il notebook Azure Databricks usa quindi i modelli sottoposti a training per estrarre i dati del modulo. Esporta i dati in un database SQL di Azure.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="Diagramma che illustra l'architettura del progetto.":::


## <a name="set-up-azure-data-lake"></a>Configurare Azure Data Lake

Il backlog dei moduli potrebbe trovarsi nell'ambiente locale o in un server FTP. Questa esercitazione usa i moduli in un account Azure Data Lake Storage Gen2. È possibile trasferire i file in questa posizione usando Azure Data Factory, Azure Storage Explorer o AzCopy. I set di impostazioni di training e di assegnazione dei punteggi possono trovarsi in contenitori diversi, ma i set di impostazioni di training per tutti i tipi di modulo devono trovarsi nello stesso contenitore. (Possono trovarsi in cartelle diverse).

Per creare un nuovo data Lake, seguire le istruzioni riportate in [creare un account di archiviazione da usare con Azure Data Lake storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

## <a name="create-a-parameterization-table"></a>Creare una tabella di parametrizzazione

Successivamente, verrà creata una tabella di metadati in un database SQL di Azure. Questa tabella conterrà i dati non sensibili richiesti dall'API REST di Riconoscimento modulo. Ogni volta che è presente un nuovo tipo di form nel set di dati, si inserirà un nuovo record in questa tabella e si attiveranno le pipeline di training e di assegnazione dei punteggi. Queste pipeline verranno implementate in un secondo momento.

Questi campi verranno usati nella tabella:

* **form_description**. Non richiesto come parte del training. Questo campo fornisce una descrizione del tipo di form per il quale si sta eseguendo il training del modello (ad esempio, "client A Forms", "Hotel B Forms").
* **training_container_name**. Nome del contenitore dell'account di archiviazione in cui è stato archiviato il set di dati di training. Può essere lo stesso contenitore specificato per **scoring_container_name**.
* **training_blob_root_folder**. La cartella all'interno dell'account di archiviazione in cui verranno archiviati i file per il training del modello.
* **scoring_container_name**. Nome del contenitore dell'account di archiviazione in cui sono stati archiviati i file da cui si vogliono estrarre le coppie chiave/valore. Può essere lo stesso contenitore specificato per **training_container_name**.
* **scoring_input_blob_folder**. La cartella nell'account di archiviazione in cui verranno archiviati i file da cui estrarre i dati.
* **MODEL_ID**. ID del modello di cui si vuole ripetere il training. Per la prima esecuzione, il valore deve essere impostato su-1, in modo che il notebook di training crei un nuovo modello personalizzato per il training. Il notebook di training restituirà il nuovo ID modello all'istanza di Azure Data Factory. Usando un'attività stored procedure, questo valore verrà aggiornato nel database SQL di Azure.

  Quando si desidera inserire un nuovo tipo di modulo, è necessario reimpostare manualmente l'ID modello su-1 prima di eseguire il training del modello.

* **file_type**. I tipi di form supportati sono `application/pdf` ,, `image/jpeg` `image/png` e `image/tif` .

  Se sono presenti moduli in altri tipi di file, è necessario modificare questo valore e **MODEL_ID** quando si esegue il training di un nuovo tipo di form.
* **form_batch_group_id**. Nel corso del tempo, è possibile che si disponga di più tipi di modulo di cui si esegue il training per lo stesso modello. Il campo **form_batch_group_id** consentirà di specificare tutti i tipi di modulo che hanno corso di training con un modello specifico.

### <a name="create-the-table"></a>Creare la tabella


[Creare un database SQL di Azure](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)e quindi eseguire lo script SQL nell' [editor di query](../../azure-sql/database/connect-query-portal.md) per creare la tabella richiesta:


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

Eseguire questo script per creare la procedura che aggiorna automaticamente **MODEL_ID** dopo il training.

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

### <a name="create-an-azure-key-vault"></a>Creare un insieme di credenziali delle chiavi di Azure

[Creare una risorsa Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Passare quindi alla risorsa Key Vault dopo che è stata creata e, nella sezione **Impostazioni** , selezionare **segreti** per aggiungere i parametri.

Verrà visualizzata una nuova finestra. Selezionare **genera/importa**. Immettere il nome del parametro e il relativo valore, quindi selezionare **Crea**. Completare i passaggi seguenti per i parametri seguenti:

* **CognitiveServiceEndpoint**. URL dell'endpoint dell'API di riconoscimento del modulo.
* **CognitiveServiceSubscriptionKey**. Chiave di accesso per il servizio di riconoscimento del modulo. 
* **StorageAccountName**. L'account di archiviazione in cui vengono archiviati il set di dati di training e i moduli da cui si desidera estrarre le coppie chiave/valore. Se questi elementi si trovano in account diversi, immettere il nome di ogni account come segreto separato. Tenere presente che i set di impostazioni di training devono trovarsi nello stesso contenitore per tutti i tipi di form. Possono trovarsi in cartelle diverse.
* **StorageAccountSasKey**. Firma di accesso condiviso (SAS) dell'account di archiviazione. Per recuperare l'URL della firma di accesso condiviso, passare alla risorsa di archiviazione. Nella scheda **Storage Explorer** passare al contenitore, fare clic con il pulsante destro del mouse e selezionare **Ottieni firma di accesso condiviso**. È importante ottenere la firma di accesso condiviso per il contenitore, non per l'account di archiviazione. Verificare che le autorizzazioni **lettura** ed **elenco** siano selezionate e quindi selezionare **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Il formato deve essere il seguente: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Eseguire il training di un modello di Riconoscimento modulo in un notebook di Databricks

Si userà Azure Databricks per archiviare ed eseguire il codice Python che interagisce con il servizio Riconoscimento modulo.

### <a name="create-a-notebook-in-databricks"></a>Creare un notebook in Databricks

[Creare una risorsa di Azure Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) nel portale di Azure. Passare alla risorsa dopo che è stata creata e aprire l'area di lavoro.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Creare un ambito dei segreti con supporto di Azure Key Vault


Per fare riferimento ai segreti nell'insieme di credenziali delle chiavi di Azure creato in precedenza, è necessario creare un ambito segreto in databricks. Attenersi alla procedura seguente: [creare un ambito Secret con supporto di Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Creare un cluster di Databricks

Un cluster è una raccolta di risorse di calcolo di Databricks. Per creare un cluster:

1. Nel riquadro sinistro selezionare il pulsante **cluster** .
1. Nella pagina **cluster** selezionare **Crea cluster**.
1. Nella pagina **Crea cluster** specificare un nome di cluster e quindi selezionare **7,2 (scala 2,12, Spark 3.0.0)** nell'elenco **Databricks Runtime versione** .
1. Selezionare **Crea cluster**.

### <a name="write-a-settings-notebook"></a>Scrivere un notebook di impostazioni

A questo punto si è pronti per aggiungere notebook Python. Per prima cosa, creare un notebook denominato **Settings**. Questo notebook assegnerà i valori nella tabella di parametrizzazione alle variabili nello script. In seguito Azure Data Factory passerà i valori in come parametri. Verranno anche assegnati i valori dei segreti nell'insieme di credenziali delle chiavi alle variabili. 

1. Per creare il notebook **delle impostazioni** , selezionare il pulsante **area di lavoro** . Nella scheda nuovo selezionare l'elenco a discesa e selezionare **Crea** e quindi **notebook**.
1. Nella finestra popup immettere un nome per il notebook e quindi selezionare **Python** come lingua predefinita. Selezionare il cluster databricks e quindi fare clic su **Crea**.
1. Nella prima cella del notebook si recuperano i parametri passati da Azure Data Factory:

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

1. Nella seconda cella si recuperano i segreti da Key Vault e li si assegna alle variabili:

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Scrivere un notebook di training

Dopo aver completato il notebook **Settings**, è possibile creare un notebook per eseguire il training del modello. Come indicato in precedenza, si useranno i file archiviati in una cartella in un account Azure Data Lake Storage Gen2 (**training_blob_root_folder**). Il nome della cartella è stato passato come variabile. Ogni set di tipi di form si troverà nella stessa cartella. Durante il ciclo della tabella dei parametri, verrà effettuato il training del modello usando tutti i tipi di form. 

1. Creare un notebook denominato **TrainFormRecognizer**. 
1. Nella prima cella eseguire il notebook **delle impostazioni** :

    ```python
    %run "./Settings"
    ```

1. Nella cella successiva assegnare le variabili dal file Settings e quindi eseguire il training dinamico del modello per ogni tipo di modulo, applicando il codice nell'[avvio rapido REST](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

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
        # Request headers.
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
    if model_id=="-1": # If you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key/value pairs.
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
    else :# If you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. Il passaggio finale del processo di training consiste nell'ottenere il risultato della formazione in formato JSON:

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

## <a name="extract-form-data-by-using-a-notebook"></a>Estrarre i dati del modulo tramite un notebook

### <a name="mount-the-azure-data-lake-storage"></a>Montare l'account di archiviazione di Azure Data Lake

Il passaggio successivo consiste nell'assegnare un punteggio ai vari formati disponibili usando il modello sottoposto a training. L'account Azure Data Lake Storage viene montato in databricks e si fa riferimento al montaggio durante il processo di inserimento.

Come nella fase di training, si userà Azure Data Factory per richiamare l'estrazione delle coppie chiave/valore dai form. Eseguiamo il ciclo sui moduli nelle cartelle specificate nella tabella dei parametri.

1. Creare il notebook per montare l'account di archiviazione in databricks. Chiamarlo **MountDataLake**. 
1. È prima necessario chiamare il notebook **Settings**:

    ```python
    %run "./Settings"
    ```

1. Nella seconda cella definire le variabili per i parametri sensibili, che verranno recuperati dal Key Vault Secrets:

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Provare a smontare l'account di archiviazione nel caso in cui sia stato montato in precedenza:

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Montare l'account di archiviazione:


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
    > È stato montato solo l'account di archiviazione di training. In questo caso, i file di training e i file da cui si desidera estrarre le coppie chiave/valore si trovano nello stesso account di archiviazione. Se gli account di archiviazione per punteggio e training sono diversi, sarà necessario montare entrambi gli account di archiviazione. 

### <a name="write-the-scoring-notebook"></a>Scrivere il notebook di assegnazione dei punteggi

È ora possibile creare un notebook di assegnazione dei punteggi. In questo modo, verrà usato un file archiviato in cartelle nell'account Azure Data Lake Storage appena montato, che verrà usato nel notebook di training. Il nome della cartella viene passato come variabile. Eseguiamo il ciclo di tutti i moduli nella cartella specificata ed Estrai le coppie chiave/valore. 

1. Creare un notebook e chiamarlo **ScoreFormRecognizer**. 
1. Eseguire le **Impostazioni** e i notebook di **MountDataLake** :

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Aggiungere questo codice, che chiama l'API [Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) :

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
        # Request headers.
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

1. Nella cella successiva si otterranno i risultati dell'estrazione della coppia chiave/valore. In questa cella verrà restituito il risultato. Si vuole il risultato in formato JSON in modo che sia possibile elaborarlo ulteriormente nel database SQL di Azure o in Azure Cosmos DB. Scriviamo quindi il risultato in un file con estensione JSON. Il nome del file di output sarà il nome del file con punteggio concatenato a "_output.json". Il file verrà archiviato nella stessa cartella del file di origine.

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

## <a name="automate-training-and-scoring-by-using-azure-data-factory"></a>Automatizzare il training e l'assegnazione dei punteggi usando Azure Data Factory

L'unico passaggio rimanente consiste nel configurare il servizio Azure Data Factory per automatizzare i processi di training e assegnazione dei punteggi. Per prima cosa, seguire la procedura descritta in [Creare una data factory](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory). Dopo aver creato la risorsa di Azure Data Factory, è necessario creare tre pipeline: una per il training e due per l'assegnazione dei punteggi. (Verrà illustrato più avanti).

### <a name="training-pipeline"></a>Pipeline di training

La prima attività nella pipeline di training è una ricerca che consenta di leggere e restituire i valori della tabella di parametrizzazione nel database SQL di Azure. Tutti i set di impostazioni di training si troveranno nello stesso account di archiviazione e nel contenitore, ma in cartelle potenzialmente diverse. Quindi, la **prima riga** del valore predefinito dell'attributo verrà mantenuta solo nelle impostazioni dell'attività di ricerca. Per ogni tipo di form su cui eseguire il training del modello, si eseguirà il training del modello usando tutti i file in **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="Screenshot che mostra una pipeline di training in Data Factory.":::

Il stored procedure accetta due parametri: **MODEL_ID** e **form_batch_group_id**. Il codice per restituire l'ID modello dal notebook di databricks è `dbutils.notebook.exit(model_id)` . Il codice per leggere il codice nell'attività stored procedure in Data Factory è `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` .

### <a name="scoring-pipelines"></a>Pipeline di assegnazione dei punteggi

Per estrarre le coppie chiave/valore, verranno analizzate tutte le cartelle della tabella di parametrizzazione. Per ogni cartella verranno estratte le coppie chiave/valore di tutti i file al suo interno. Azure Data Factory attualmente non supporta i cicli ForEach annidati. Si creeranno invece due pipeline. La prima pipeline eseguirà la ricerca dalla tabella di parametrizzazione e passerà l'elenco di cartelle come parametro alla seconda pipeline.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="Screenshot che mostra la prima pipeline di assegnazione dei punteggi in Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="Screenshot che mostra i dettagli per la prima pipeline di assegnazione dei punteggi in Data Factory.":::

La seconda pipeline utilizzerà un'attività getmeta per ottenere l'elenco dei file nella cartella e passarlo come parametro al notebook databricks di assegnazione dei punteggi.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="Screenshot che mostra la seconda pipeline di assegnazione dei punteggi in Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="Screenshot che mostra i dettagli per la seconda pipeline di assegnazione dei punteggi in Data Factory.":::

### <a name="specify-a-degree-of-parallelism"></a>Specificare un grado di parallelismo

Nelle pipeline di training e di assegnazione dei punteggi è possibile specificare il grado di parallelismo, in modo da poter elaborare contemporaneamente più moduli.

Per impostare il grado di parallelismo nella pipeline Azure Data Factory:

1. Selezionare l'attività **foreach** .
1. Deselezionare la casella **sequenziale** .
1. Impostare il grado di parallelismo nella casella **batch count** . Per l'assegnazione dei punteggi è consigliabile un numero massimo di batch pari a 15.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="Screenshot che mostra la configurazione del parallelismo per l'attività di assegnazione dei punteggi in Azure Data Factory.":::

## <a name="how-to-use-the-pipeline"></a>Come usare la pipeline

A questo punto si ha una pipeline automatizzata per digitalizzare il backlog dei moduli ed eseguire alcune analisi. Quando si aggiungono nuove forme di un tipo familiare a una cartella di archiviazione esistente, è sufficiente eseguire nuovamente le pipeline di assegnazione dei punteggi. Aggiorneranno tutti i file di output, inclusi i file di output per i nuovi moduli. 

Se si aggiungono nuovi moduli di un nuovo tipo, sarà necessario caricare anche un set di dati di training nel contenitore appropriato. Si aggiungerà quindi una nuova riga nella tabella di parametrizzazione, inserendo i percorsi dei nuovi documenti e il set di dati di training. Immettere un valore pari a-1 per **model_ID** per indicare che è necessario eseguire il training di un nuovo modello per i moduli. Eseguire quindi la pipeline di training in Azure Data Factory. La pipeline leggerà dalla tabella, eseguirà il training di un modello e sovrascriverà l'ID modello nella tabella. È quindi possibile chiamare le pipeline di assegnazione dei punteggi per iniziare a scrivere i file di output.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si configurano le pipeline di Azure Data Factory per attivare il training e l'esecuzione di modelli di riconoscimento moduli e digitalizzare un backlog di grandi dimensioni di file. Esaminare quindi l'API di Riconoscimento modulo per scoprire quali altri operazioni è possibile eseguire.

* [API REST di Riconoscimento modulo](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
