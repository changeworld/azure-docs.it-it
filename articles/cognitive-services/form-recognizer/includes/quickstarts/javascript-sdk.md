---
title: 'Avvio rapido: Libreria client di Riconoscimento modulo per JavaScript'
description: Usare la libreria client di Riconoscimento modulo per JavaScript per creare un'app per l'elaborazione di moduli che estrae coppie chiave/valore e dati di tabelle dai documenti personalizzati.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: feff8b003428fd61fba826d05f8212fa8d9788f9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601936"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Vedere la documentazione di riferimento di seguito.

[Documentazione di riferimento](../../index.yml) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [Pacchetto (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Versione corrente di [Node.js](https://nodejs.org/)
* Un BLOB di Archiviazione di Azure contenente un set di dati di training. Consultare [Compilare un training set per un modello personalizzato](../../build-training-data-set.md) per suggerimenti e opzioni per la creazione di un set di dati di training. Per questo argomento di avvio rapido, è possibile usare i file inclusi nella cartella **Train** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451) (scaricare ed estrarre *sample_data.zip*).
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="creare una risorsa di Riconoscimento modulo"  target="_blank">creare una risorsa di Riconoscimento modulo </a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
  * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Riconoscimento modulo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
  * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a una nuova directory per l'app.

```console
mkdir myapp && cd myapp
```

Eseguire il comando `npm init` per creare un'applicazione Node con un file `package.json`.

```console
npm init
```

### <a name="install-the-client-library"></a>Installare la libreria client

Installare il pacchetto NPM `ai-form-recognizer`:

```console
npm install @azure/ai-form-recognizer
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

Creare un file denominato `index.js`, aprirlo e importare le librerie seguenti:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js), che contiene gli esempi di codice di questo argomento.

Creare le variabili per l'endpoint e la chiave di Azure della risorsa.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa di Riconoscimento modulo creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**.
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](../../../cognitive-services-security.md) di Servizi cognitivi.

## <a name="object-model"></a>Modello a oggetti

Con Riconoscimento modulo è possibile creare due diversi tipi di client. Il primo, `FormRecognizerClient`, viene usato per eseguire query sul servizio per riconoscere campi modulo e contenuti. Il secondo, `FormTrainingClient` viene usato per creare e gestire modelli personalizzati che è possibile usare per migliorare il riconoscimento.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` fornisce le operazioni per:

* Riconoscimento dei campi modulo e del contenuto usando modelli personalizzati con training per analizzare i moduli personalizzati. Questi valori vengono restituiti in una raccolta di oggetti `RecognizedForm`.
* Riconoscere i contenuti dei moduli, incluse tabelle, righe e parole, senza la necessità di eseguire il training di un modello. I contenuti dei moduli vengono restituiti in una raccolta di oggetti `FormPage`.
* Riconoscimento di campi comuni da ricevute, biglietti da visita, fatture e documenti di identità degli Stati Uniti usando un modello con training preliminare nel riconoscimento modulo servizio.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` fornisce le operazioni per:

* Training di modelli personalizzati per analizzare tutti i campi e i valori trovati nei moduli personalizzati. Viene restituito un valore che indica i tipi di modulo che verranno analizzati dal modello e `CustomFormModel` i campi che estrarrà per ogni tipo di modulo. _Per_ altri [dettagli, vedere la documentazione](#train-a-model-without-labels) del servizio sul training del modello senza etichetta.
* Training di modelli personalizzati per analizzare campi e valori specifici specificati etichettando i moduli personalizzati. Viene restituito un `CustomFormModel` che indica i campi che verranno estratti dal modello e l'accuratezza stimata per ogni campo. Per una spiegazione più dettagliata dell'applicazione di etichette a un training set, vedere la [documentazione del servizio sul training di modelli etichettati](#train-a-model-with-labels).
* Gestire i modelli creati nell'account.
* Copiare un modello personalizzato da una risorsa Riconoscimento modulo a un'altra.

> [!NOTE]
> È possibile eseguire il training dei modelli anche con un'interfaccia utente grafica, ad esempio con lo [strumento di etichettatura di Riconoscimento modulo](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Riconoscimento modulo per JavaScript:

* [Autenticare il client](#authenticate-the-client)
* [Analizzare il layout](#analyze-layout)
* [Analizzare ricevute](#analyze-receipts)
* [Analizzare biglietti da visita](#analyze-business-cards)
* [Analizzare fatture](#analyze-invoices)
* [Analizzare i documenti di identità](#analyze-identity-documents)
* [Eseguire il training di un modello personalizzato](#train-a-custom-model)
* [Analizzare i moduli con un modello personalizzato](#analyze-forms-with-a-custom-model)
* [Gestire i modelli personalizzati](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Autenticare il client

Eseguire l'autenticazione di un oggetto client usando le variabili di sottoscrizione definite in precedenza. Si userà un oggetto `AzureKeyCredential` in modo che, se necessario, sia possibile aggiornare la chiave API senza creare nuovi oggetti client. Verrà inoltre creato un oggetto client di training.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Ottenere le risorse per il test

Sarà inoltre necessario aggiungere riferimenti agli URL per i dati di training e di test.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recupero dell'URL di firma di accesso condiviso":::
* Usare il modulo di esempio e le immagini di ricevute inclusi negli esempi seguenti (disponibili anche in [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/assets)) oppure usare la procedura precedente per ottenere l'URL di firma di accesso condiviso di un singolo documento nell'archivio BLOB.

## <a name="analyze-layout"></a>Analizzare il layout

È possibile usare riconoscimento modulo per analizzare tabelle, linee e parole nei documenti, senza dover eseguire il training di un modello. Per altre informazioni sull'estrazione del layout, vedere la [Guida concettuale al layout](../../concept-layout.md). Per analizzare il contenuto di un file in un URI specifico, usare il `beginRecognizeContentFromUrl` metodo .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]

> [!TIP]
> È anche possibile ottenere contenuto da un file locale. Vedere i metodi [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient), ad esempio **beginRecognizeContent**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).

### <a name="output"></a>Output

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>Analizzare ricevute

Questa sezione illustra come analizzare ed estrarre campi comuni dalle ricevute degli Stati Uniti, usando un modello di ricevuta con training preliminare. Per altre informazioni sull'analisi delle ricevute, vedere la [Guida concettuale alle ricevute](../../concept-receipts.md).

Per analizzare le ricevute da un URI, usare il `beginRecognizeReceiptsFromUrl` metodo . Il codice seguente elabora una ricevuta in corrispondenza dell'URI specificato e stampa i campi e i valori principali nella console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> È anche possibile analizzare le immagini delle ricevute locali. Vedere i metodi [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient), ad esempio **beginRecognizeReceipts**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).

### <a name="output"></a>Output

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="analyze-business-cards"></a>Analizzare biglietti da visita

Questa sezione illustra come analizzare ed estrarre campi comuni da biglietti da visita in lingua inglese, usando un modello con training preliminare. Per altre informazioni sull'analisi dei biglietti da visita, vedere la [Guida concettuale ai biglietti da visita](../../concept-business-cards.md).

Per analizzare i biglietti da visita da un URL, usare il `beginRecognizeBusinessCardsFromURL` metodo .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_bc":::

> [!TIP]
> È anche possibile analizzare immagini di biglietti da visita locali. Vedere i metodi di [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient), ad esempio **beginRecognizeBusinessCards**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).

## <a name="analyze-invoices"></a>Analizzare fatture

Questa sezione illustra come analizzare ed estrarre campi comuni dalle fatture di vendita usando un modello con training preliminare. Per altre informazioni sull'analisi della fattura, vedere la [Guida concettuale della fattura.](../../concept-invoices.md)

Per analizzare le fatture da un URL, usare il `beginRecognizeInvoicesFromUrl` metodo .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_invoice":::

> [!TIP]
> È anche possibile analizzare le immagini dei biglietti da visita locali. Vedere i metodi di [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient), ad esempio **beginRecognizeInvoices**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).

## <a name="analyze-identity-documents"></a>Analizzare i documenti di identità

Questa sezione illustra come analizzare ed estrarre le informazioni chiave dai documenti di identificazione rilasciati dal governo, ovvero passaporti a livello mondiale e licenze del driver statunitense, usando il modello di ID predefinito riconoscimento modulo. Per altre informazioni sull'analisi delle fatture, vedere la guida [concettuale al modello di identificazione predefinito](../../concept-identification-cards.md).

Per analizzare i documenti di identità da un URL, usare il `beginRecognizeIdDocumentsFromUrl` metodo .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_id":::

## <a name="train-a-custom-model"></a>Eseguire il training di un modello personalizzato

Questa sezione illustra come eseguire il training di un modello con i dati personali. Un modello sottoposto a training restituisce dati strutturati che includono le relazioni chiave-valore del file originale. Dopo il training del modello, è possibile testarlo, ripeterne il training e infine usarlo per estrarre dati in modo affidabile da altri moduli in base alle proprie esigenze.

> [!NOTE]
> È anche possibile eseguire il training dei modelli con un'interfaccia utente grafica, ad esempio con lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Eseguire il training di un modello senza etichette

Eseguire il training di modelli personalizzati per analizzare tutti i campi e i valori presenti nei moduli personalizzati senza etichettare manualmente i documenti di training.

La funzione seguente esegue il training di un modello su un set di documenti specificato e ne stampa lo stato nella console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]

### <a name="output"></a>Output

Si tratta dell'output di un modello con training con i dati di training disponibili in [JavaScript SDK.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer) Questo output di esempio è stato troncato per una leggibilità.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors:
...
```

### <a name="train-a-model-with-labels"></a>Eseguire il training di un modello con etichette

È inoltre possibile eseguire il training di modelli personalizzati etichettando manualmente i documenti di training. Il training con etichette consente prestazioni migliori in alcuni scenari. Per eseguire il training con etichette, è necessario avere file speciali di informazioni sulle etichette (`\<filename\>.pdf.labels.json`) nel contenitore di archiviazione BLOB insieme ai documenti di training. Lo [Strumento di etichettatura campioni Riconoscimento modulo](../../quickstarts/label-tool.md) fornisce un'interfaccia utente che consente di creare questi file di etichette. Quando sono disponibili è possibile chiamare il metodo `beginTraining` con il parametro `uselabels` impostato su `true`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]

### <a name="output"></a>Output

Si tratta dell'output di un modello con training con i dati di training disponibili in [JavaScript SDK.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) Questo output di esempio è stato troncato per una leggibilità.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizzare i moduli con un modello personalizzato

Questa sezione illustra come estrarre informazioni chiave-valore e altro contenuto dai tipi di modulo personalizzati, usando i modelli a cui è stato eseguito il training con moduli personalizzati.

> [!IMPORTANT]
> Per implementare questo scenario è necessario avere già eseguito il training di un modello in modo da poter passare il relativo ID al metodo seguente. Vedere la sezione [Eseguire il training di un modello](#train-a-model-without-labels).

Verrà usato il metodo `beginRecognizeCustomFormsFromUrl`. Il valore restituito è una raccolta di oggetti `RecognizedForm`: uno per ogni pagina nel documento inviato.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> È anche possibile analizzare i file locali. Vedere i metodi [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient), ad esempio **beginRecognizeCustomForms**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples).

### <a name="output"></a>Output

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Gestire i modelli personalizzati

Questa sezione illustra come gestire modelli personalizzati archiviati nell'account. Il codice seguente esegue tutte le attività di gestione dei modelli in una singola funzione, come esempio.

### <a name="get-number-of-models"></a>Ottenere il numero di modelli

Il blocco di codice seguente ottiene il numero di modelli attualmente presenti nell'account.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]

### <a name="get-list-of-models-in-account"></a>Ottenere l'elenco di modelli nell'account

Il blocco di codice seguente fornisce un elenco completo dei modelli disponibili nell'account, incluse le informazioni sulla data di creazione del modello e sullo stato corrente.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]

### <a name="output"></a>Output

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Ottenere l'elenco degli ID modello per pagina

Questo blocco di codice fornisce un elenco impaginato di modelli e ID modello.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]

### <a name="output"></a>Output

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Ottenere il modello in base all'ID

La funzione seguente ottiene l'oggetto modello corrispondente a un ID modello. Questa funzione non viene chiamata per impostazione predefinita.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]

### <a name="delete-a-model-from-the-resource-account"></a>Eliminare un modello dall'account della risorsa

È inoltre possibile eliminare un modello dall'account facendo riferimento al relativo ID. Questa funzione elimina il modello con l'ID specificato. Questa funzione non viene chiamata per impostazione predefinita.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]

### <a name="output"></a>Output

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node` nel file quickstart.

```console
node index.js
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="enable-logs"></a>Abilitare i log

È possibile impostare la variabile di ambiente seguente per visualizzare i log di debug quando si usa questa libreria.

```console
export DEBUG=azure*
```

Per istruzioni più dettagliate su come abilitare i log, vedere la [documentazione sul pacchetto @azure/logger](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata la libreria client di Riconoscimento modulo per JavaScript per eseguire il training di modelli e analizzare i moduli in modi diversi. In seguito, è possibile ottenere suggerimenti per creare un set di dati di training migliore e produrre modelli più accurati.

> [!div class="nextstepaction"]
> [Creare un set di dati di training](../../build-training-data-set.md)

## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](../../overview.md)
* Il codice di esempio di questa guida è reperibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js).
