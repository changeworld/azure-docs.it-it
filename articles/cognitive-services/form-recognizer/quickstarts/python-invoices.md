---
title: 'Avvio rapido: Estrarre dati dalle fatture con Python - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si userà l'API REST Riconoscimento modulo con Python per estrarre dati dalle fatture
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/12/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 7c6b5406728817c3dd700ec285d9af77c9d3cf60
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96009314"
---
# <a name="quickstart-extract-invoice-data-using-the-form-recognizer-rest-api-with-python"></a>Avvio rapido: Estrarre dati dalle fatture tramite l'API REST Riconoscimento modulo con Python

In questo argomento di avvio rapido si userà l'API REST Riconoscimento modulo di Azure con Python per estrarre e identificare informazioni pertinenti nelle fatture.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:
- [Python](https://www.python.org/downloads/) installato, se si vuole eseguire l'esempio in locale.
- Una fattura. Per questo argomento di avvio rapido è possibile usare una [fattura di esempio](../media/sample-invoice.jpg).

> [!NOTE]
> Questa guida di avvio rapido usa un file locale. Per usare invece una fattura accessibile tramite URL, vedere la [documentazione di riferimento](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeInvoiceAsync).

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-an-invoice"></a>Analizzare una fattura

Per iniziare ad analizzare una fattura, chiamare l'API **[Analyze Invoice](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** usando lo script Python seguente. Prima di eseguire lo script, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `<path to your invoice>` con il percorso locale in cui è stata salvata la fattura di esempio.

    ```python
        ########### Python Form Recognizer Async Invoice #############
    
        import json
        import time
        from requests import get, post
        
        # Endpoint URL
        endpoint = r"<Endpoint>"
        apim_key = "<subscription key>"
        post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
        source = r"<path to your invoice>"
        
        headers = {
            # Request headers
            'Content-Type': '<file type>',
            'Ocp-Apim-Subscription-Key': apim_key,
        }
        
        params = {
            "includeTextDetails": True
            "locale": "en-US"
        }
        
        with open(source, "rb") as f:
            data_bytes = f.read()
        
        try:
            resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
            if resp.status_code != 202:
                print("POST analyze failed:\n%s" % resp.text)
                quit()
            print("POST analyze succeeded:\n%s" % resp.headers)
            get_url = resp.headers["operation-location"]
        except Exception as e:
            print("POST analyze failed:\n%s" % str(e))
            quit()
    ```

1. Salvare il codice in un file con estensione py. Ad esempio *form-recognizer-invoice.py*.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python form-recognizer-invoice.py`.

Si riceverà una risposta `202 (Success)` contenente un'intestazione **Operation-Location**, che lo script stamperà nella console. Questa intestazione contiene un ID operazione che è possibile usare per eseguire una query sullo stato dell'operazione asincrona e ottenere i risultati. Nel valore di esempio seguente, la stringa dopo `operations/` è l'ID operazione.

## <a name="get-the-invoice-results"></a>Ottenere i risultati della fattura

Dopo aver chiamato l'API **Analyze Invoice**, chiamare l'API **[Get Analyze Invoice Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** per ottenere lo stato dell'operazione e i dati estratti. Aggiungere il codice seguente in fondo allo script Python. Viene usato il valore dell'ID operazione in una nuova chiamata API. Questo script chiama l'API a intervalli regolari finché non vengono ottenuti i risultati. Si consiglia un intervallo di almeno un secondo.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Invoice results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Invoice Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Invoice Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Salvare lo script.
1. Usare nuovamente il comando `python` per eseguire l'esempio. Ad esempio: `python form-recognizer-invoice.py`.

### <a name="examine-the-response"></a>Esaminare i risultati

Questo script stamperà le risposte nella console fino al completamento dell'operazione **Analyze Invoice**. Stamperà quindi i dati di testo estratti in formato JSON. Il campo `"readResults"` contiene tutte le righe di testo estratte dalla fattura, il campo `"pageResults"` include le tabelle e i contrassegni di selezione estratti dalla fattura e il campo `"documentResults"` contiene le informazioni delle coppie chiave-valore per le sezioni più pertinenti della fattura.

Vedere la fattura seguente e il corrispondente output JSON:

* [Esempio di fattura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)
* [Output JSON della fattura di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)


### <a name="sample-python-script-to-extract-invoice-or-a-batch-of-invoices-into-a-csv-file"></a>Script Python di esempio per estrarre una fattura o un batch di fatture in un file CSV

Questo script Python di esempio mostra come iniziare a usare l'API fattura. Può essere eseguito con una singola fattura come parametro o con una cartella e restituirà il file JSON ".invoice.json" e un file _invoiceResutls.csv_ con i risultati dei valori estratti. Se viene eseguito in una cartella, analizzerà tutti i file "pdf","jpg","jpeg","png","bmp","tif","tiff" e quindi li eseguirà con l'API. 
 
```python
########### Python Form Recognizer Async Invoice #############

import json
import time
import os
import ntpath
import sys
from requests import get, post
import csv


def analyzeInvoice(filename):
    invoiceResultsFilename = filename + ".invoice.json"

    # do not run analyze if .invoice.json file is present on disk
    if os.path.isfile(invoiceResultsFilename):
        with open(invoiceResultsFilename) as json_file:
            return json.load(json_file)

    # Endpoint URL
    #endpoint = r"<Endpoint>"
    #apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
    headers = {
        # Request headers
        'Content-Type': 'application/octet-stream',
        'Ocp-Apim-Subscription-Key': apim_key,
    }

    params = {
        "includeTextDetails": True
    }

    with open(filename, "rb") as f:
        data_bytes = f.read()

    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            return None
        print("POST analyze succeeded: %s" % resp.headers["operation-location"])
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        return None

    n_tries = 50
    n_try = 0
    wait_sec = 6

    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
            resp_json = json.loads(resp.text)
            if resp.status_code != 200:
                print("GET Invoice results failed:\n%s" % resp_json)
                return None
            status = resp_json["status"]
            if status == "succeeded":
                print("Invoice analysis succeeded.")
                with open(invoiceResultsFilename, 'w') as outfile:
                    json.dump(resp_json, outfile, indent=4)
                return resp_json
            if status == "failed":
                print("Analysis failed:\n%s" % resp_json)
                return None
            # Analysis still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1     
        except Exception as e:
            msg = "GET analyze results failed:\n%s" % str(e)
            print(msg)
            return None

    return resp_json

def parseInvoiceResults(resp_json):
    docResults = resp_json["analyzeResult"]["documentResults"]
    invoiceResult = {}
    for docResult in docResults:
        for fieldName, fieldValue in sorted(docResult["fields"].items()):
            valueFields = list(filter(lambda item: ("value" in item[0]) and ("valueString" not in item[0]), fieldValue.items()))
            invoiceResult[fieldName] = fieldValue["text"]            
            if len(valueFields) == 1:
                print("{0:26} : {1:50}      NORMALIZED VALUE: {2}".format(fieldName , fieldValue["text"], valueFields[0][1]))
                invoiceResult[fieldName + "_normalized"] = valueFields[0][1]
            else:
                print("{0:26} : {1}".format(fieldName , fieldValue["text"]))
    print("")
    return invoiceResult

def main(argv):
    if (len(argv)  != 2):
        print("ERROR: Please provide invoice filename or root directory with invoice PDFs/images as an argument to the python script")
        return

    # list of invoice to analyze
    invoiceFiles = []
    csvPostfix = '-invoiceResults.csv'
    if os.path.isfile(argv[1]):
        # Single invoice
        invoiceFiles.append(argv[1])
        csvFileName = argv[1] + csvPostfix
    else:
        # Folder of invoices
        supportedExt = ['.pdf', '.jpg','.jpeg','.tif','.tiff','.png','.bmp']
        invoiceDirectory = argv[1]
        csvFileName = os.path.join(invoiceDirectory, os.path.basename(os.path.abspath(invoiceDirectory)) + csvPostfix)
        for root, directories, filenames in os.walk(invoiceDirectory):
            for invoiceFilename in filenames:
                ext = os.path.splitext(invoiceFilename)[-1].lower()
                if ext in supportedExt:
                    fullname = os.path.join(root, invoiceFilename)
                    invoiceFiles.append(fullname)
                    
    with open(csvFileName, mode='w', newline='\n', encoding='utf-8') as csv_file:
        fieldnames = ['Filename',
                      'FullFilename','InvoiceTotal','InvoiceTotal_normalized','AmountDue','AmountDue_normalized','SubTotal','SubTotal_normalized','TotalTax','TotalTax_normalized','CustomerName','VendorName',
                      'InvoiceId','CustomerId','PurchaseOrder','InvoiceDate','InvoiceDate_normalized','DueDate','DueDate_normalized',
                      'VendorAddress','VendorAddressRecipient','BillingAddress','BillingAddressRecipient','ShippingAddress','ShippingAddressRecipient','CustomerAddress','CustomerAddressRecipient','ServiceAddress','ServiceAddressRecipient','RemittanceAddress','RemittanceAddressRecipient', 'ServiceStartDate','ServiceStartDate_normalized','ServiceEndDate','ServiceEndDate_normalized','PreviousUnpaidBalance','PreviousUnpaidBalance_normalized']
        writer = csv.DictWriter(csv_file, fieldnames=fieldnames)
        writer.writeheader()
        counter = 0
        for invoiceFullFilename in invoiceFiles:
            counter = counter + 1
            invoiceFilename = ntpath.basename(invoiceFullFilename)
            print("----- Processing {0}/{1} : {2} -----".format(counter, len(invoiceFiles),invoiceFullFilename))
            
            resp_json = analyzeInvoice(invoiceFullFilename)

            if (resp_json is not None):
                invoiceResults = parseInvoiceResults(resp_json)
                invoiceResults["FullFilename"] = invoiceFullFilename
                invoiceResults["Filename"] = invoiceFilename
                writer.writerow(invoiceResults)
                    
if __name__ == '__main__':
    main(sys.argv)
```

1. Salvare il codice in un file con estensione py. Ad esempio *form-recognizer-invoice-to-csv.py*.
1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio `python form-recognizer-invoice.py {file name or folder name}`. Lo script Python può essere eseguito con una singola fattura o con una cartella come parametro e restituirà il file JSON ".invoice.json" e i valori estratti dalle fatture in un file CSV "-invoiceResults.csv" con i risultati. Se viene eseguito in una cartella, analizzerà tutti i file "pdf","jpg","jpeg","png","bmp","tif","tiff" e quindi li eseguirà con l'API.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata l'API REST Riconoscimento modulo con Python per estrarre il contenuto delle fatture. A questo punto, vedere la documentazione di riferimento per esplorare l'API di Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeInvoiceAsync)

   
