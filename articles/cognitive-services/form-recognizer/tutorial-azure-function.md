---
title: "Esercitazione: usare una funzione di Azure per l'elaborazione di documenti archiviati"
titleSuffix: Azure Cognitive Services
description: Questa guida illustra come usare una funzione di Azure per attivare l'elaborazione dei documenti caricati in un contenitore di archiviazione BLOB di Azure.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: pafarley
ms.openlocfilehash: ee91a9e881ee5e8707feb862162b4272e3552d5f
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102663336"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>Esercitazione: usare una funzione di Azure per l'elaborazione di documenti archiviati

È possibile usare il riconoscimento moduli come parte di una pipeline di elaborazione dati automatizzata compilata con funzioni di Azure. Questa guida illustra come usare una funzione di Azure per elaborare i documenti caricati in un contenitore di archiviazione BLOB di Azure. Questo flusso di lavoro estrae i dati della tabella da documenti archiviati usando il servizio di layout riconoscimento form e salva i dati della tabella in un file con estensione CSV in Azure. È quindi possibile visualizzare i dati utilizzando Microsoft Power BI (non illustrati qui).

> [!div class="mx-imgBorder"]
> ![diagramma del flusso di lavoro dei servizi di Azure](./media/tutorial-azure-function/workflow-diagram.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un account di Archiviazione di Azure
> * Creare un progetto di Funzioni di Azure
> * Estrai i dati di layout dai moduli caricati
> * Caricare i dati di layout in archiviazione di Azure

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Creare una risorsa di riconoscimento del modulo "  target="_blank"> creare una risorsa di riconoscimento del modulo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> nella portale di Azure per ottenere la chiave e l'endpoint del sistema di riconoscimento del modulo. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
  * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Riconoscimento modulo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
  * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* Documento PDF locale da analizzare. È possibile scaricare questo [documento di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf) per l'uso di.
* [Python 3.8. x](https://www.python.org/downloads/) installato.
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) installato.
* [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cbash#install-the-azure-functions-core-tools) installato.
* Visual Studio Code con le seguenti estensioni installate:
  * [Estensione funzioni di Azure](https://docs.microsoft.com/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Estensione di Python](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

[Creare un account di archiviazione di Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) nel portale di Azure. Selezionare **archiviazione V2** come tipo di account.

Nel riquadro sinistro selezionare la scheda **CORS** e rimuovere il criterio CORS esistente, se esistente.

Una volta distribuite, creare due contenitori di archiviazione BLOB vuoti, denominati **test** e **output**.

## <a name="create-an-azure-functions-project"></a>Creare un progetto di Funzioni di Azure

Aprire Visual Studio Code. Se è stata installata l'estensione funzioni di Azure, nel riquadro di spostamento a sinistra dovrebbe essere visualizzato un logo di Azure. Selezionarla. Creare un nuovo progetto e, quando richiesto, creare una cartella locale **coa_new** per contenere il progetto.

![Pulsante Crea funzione VSCode](./media/tutorial-azure-function/vs-code-create-function.png)


Verrà richiesto di configurare una serie di impostazioni:
* Nella richiesta **Select a Language** selezionare Python.
* Nella finestra di messaggio **selezionare un modello** selezionare trigger di archiviazione BLOB di Azure. Assegnare quindi al trigger predefinito un nome.
* Nella richiesta di **impostazione selezionare** scegliere di creare nuove impostazioni dell'app locale.
* Selezionare la sottoscrizione di Azure con l'account di archiviazione creato. Quindi, è necessario immettere il nome del contenitore di archiviazione (in questo caso `test/{name}` )
* Scegliere di aprire il progetto nella finestra corrente. 

![Esempio di richiesta di creazione VSCode](./media/tutorial-azure-function/vs-code-prompt.png)

Una volta completati questi passaggi, VSCode aggiungerà un nuovo progetto di funzione di Azure con uno script Python *\_ \_ init \_ \_ . py* . Questo script viene attivato quando un file viene caricato nel contenitore di archiviazione di **test** , ma non esegue alcuna operazione.

## <a name="test-the-function"></a>Testare la funzione

Premere F5 per eseguire la funzione di base. VSCode chiederà di selezionare un account di archiviazione con cui eseguire l'interfaccia. Selezionare l'account di archiviazione creato e continuare.

Aprire Azure Storage Explorer e caricare un documento PDF di esempio nel contenitore di **test** . Controllare quindi il terminale VSCode. Lo script deve registrare che è stato attivato dal caricamento in formato PDF.

![Test Terminal VSCode](./media/tutorial-azure-function/vs-code-terminal-test.png)


Arrestare lo script prima di continuare.

## <a name="add-form-processing-code"></a>Aggiungere il codice di elaborazione del modulo

Successivamente, si aggiungerà il proprio codice allo script Python per chiamare il servizio di riconoscimento del modulo e analizzare i documenti caricati usando l' [API del layout](concept-layout.md)di riconoscimento del modulo.

In VSCode passare al file *requirements.txt* della funzione. Definisce le dipendenze per lo script. Aggiungere i pacchetti Python seguenti al file:

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

Quindi, aprire lo script *\_ \_ init \_ \_ . py* . Aggiungere le istruzioni `import` seguenti:

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

È possibile lasciare invariata la `main` funzione generata. Si aggiungerà il codice personalizzato all'interno di questa funzione.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

Il blocco di codice seguente chiama il modulo Recognizer [Analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) API di layout nel documento caricato. Inserire i valori dell'endpoint e della chiave. 


# <a name="version-20"></a>[versione 2.0](#tab/2-0)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```

# <a name="version-21-preview"></a>[versione 2.1-preview](#tab/2-1)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa di Riconoscimento modulo creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza di servizi cognitivi](../cognitive-services-security.md) .

Aggiungere quindi il codice per eseguire una query sul servizio e ottenere i dati restituiti. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

Aggiungere quindi il codice seguente per connettersi al contenitore di **output** di archiviazione di Azure. Inserire i propri valori per il nome e la chiave dell'account di archiviazione. È possibile ottenere la chiave nella scheda **chiavi di accesso** della risorsa di archiviazione nel portale di Azure.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

Il codice seguente analizza la risposta del riconoscitore del form restituito, costruisce un file con estensione CSV e lo carica nel contenitore di **output** . 


> [!IMPORTANT]
> Probabilmente sarà necessario modificare il codice in modo che corrisponda alla struttura dei documenti del modulo.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

Infine, l'ultimo blocco di codice carica i dati della tabella e del testo estratti nell'elemento di archiviazione BLOB.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>Eseguire la funzione

Premere F5 per eseguire di nuovo la funzione. Usare Azure Storage Explorer per caricare un modulo PDF di esempio nel contenitore di archiviazione di **test** . Questa azione dovrebbe attivare l'esecuzione dello script ed è quindi possibile visualizzare il file CSV risultante (visualizzato come tabella) nel contenitore di **output** .

È possibile connettere questo contenitore a Power BI per creare visualizzazioni avanzate dei dati in esso contenuti.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare una funzione di Azure scritta in Python per elaborare automaticamente i documenti PDF caricati e restituire il relativo contenuto in un formato più adatto ai dati. Successivamente, viene illustrato come utilizzare Power BI per visualizzare i dati.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [Informazioni su Riconoscimento modulo](overview.md)
* Altre informazioni sull' [API di layout](concept-layout.md)
