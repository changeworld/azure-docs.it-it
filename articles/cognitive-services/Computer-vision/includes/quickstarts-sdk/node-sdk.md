---
title: 'Guida introduttiva: libreria client per il riconoscimento ottico dei caratteri per Node.js'
description: Introduzione alla libreria client per il riconoscimento ottico dei caratteri per Node.js con questa Guida introduttiva
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: cb4679152740b73d6bb9cf7288fcaa811b6d6141
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073300"
---
<a name="HOLTop"></a>

Usare la libreria client per il riconoscimento ottico dei caratteri per leggere testo stampato e scritto con l'API Read.

[Documentazione di riferimento](/javascript/api/@azure/cognitiveservices-computervision/) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Pacchetto (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Esempi](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Versione corrente di [Node.js](https://nodejs.org/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale </a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
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

Installare i pacchetti NPM `ms-rest-azure` e `@azure/cognitiveservices-computervision`:

```console
npm install @azure/cognitiveservices-computervision
```

Installare anche il modulo asincrono:

```console
npm install async
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), che contiene gli esempi di codice di questo argomento.

Creare un nuovo file, *index.js*, e aprirlo in un editor di testo.

### <a name="find-the-subscription-key-and-endpoint"></a>Trovare la chiave e l'endpoint della sottoscrizione

Accedere al portale di Azure. Se la risorsa Visione artificiale creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. È possibile trovare la chiave e l'endpoint della sottoscrizione nella pagina **chiave ed endpoint** della risorsa in **Gestione risorse**. 

Creare variabili per la chiave e l'endpoint della sottoscrizione Visione artificiale. Incollare la chiave e l'endpoint della sottoscrizione nel codice seguente, dove indicato. Il formato dell'endpoint del Visione artificiale è `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Ricordarsi di rimuovere la chiave di sottoscrizione dal codice al termine dell'operazione senza pubblicarla. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, ad esempio [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Il client è stato configurato](?success=set-up-client#object-model) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di OCR Node.js SDK.

|Nome|Descrizione|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per eseguire la maggior parte delle operazioni con le immagini.|

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice illustrano come eseguire le attività seguenti con la libreria client OCR per Node.js:

* [Autenticare il client](#authenticate-the-client)
* [Leggere il testo stampato e scritto a mano](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticare il client


Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) con la chiave e l'endpoint e usarlo per creare un oggetto [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Definire quindi una funzione `computerVision` e dichiarare una serie asincrona con la funzione primaria e la funzione di callback. Aggiungere il codice di questo avvio rapido nella funzione primaria e chiamare `computerVision` nella parte inferiore dello script. Il resto del codice in questa sezione verrà inserito nella funzione `computerVision`.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Il client è stato autenticato](?success=authenticate-client#read-printed-and-handwritten-text) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Leggere il testo stampato e scritto a mano

Il servizio OCR può estrarre il testo visibile in un'immagine e convertirlo in un flusso di caratteri. Questo esempio usa le operazioni di lettura.

### <a name="set-up-test-images"></a>Configurare le immagini di test

Salvare un riferimento dell'URL delle immagini da cui si vuole estrarre il testo.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> È anche possibile leggere il testo da un'immagine locale. Vedere i metodi [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient), ad esempio **readInStream**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).

### <a name="call-the-read-api"></a>Chiamare l'API di lettura

Definire i campi seguenti nella funzione per indicare i valori di stato della chiamata di lettura.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Aggiungere il codice seguente, che chiama la funzione `readTextFromURL`per le immagini specificate.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definire la funzione `readTextFromURL`. Questa funzione chiama il metodo **read** sull'oggetto client, che restituisce un ID operazione e avvia un processo asincrono per leggere il contenuto dell'immagine. Usa quindi l'ID operazione per controllare lo stato dell'operazione fino a quando non vengono restituiti i risultati. Restituisce infine i risultati estratti.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Definire quindi la funzione helper `printRecText`, che visualizza i risultati delle operazioni di lettura nella console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [Il testo è stato letto](?success=read-printed-handwritten-text#run-the-application) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node` nel file quickstart.

```console
node index.js
```

> [!div class="nextstepaction"]
> [L'applicazione è stata eseguita](?success=run-the-application#clean-up-resources) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Le risorse sono state pulite](?success=clean-up-resources#next-steps) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come installare la libreria client OCR e usare l'API Read. Quindi, altre informazioni sulle funzionalità dell'API di lettura.

> [!div class="nextstepaction"]
>[Chiamare l'API di lettura](../../Vision-API-How-to-Topics/call-read-api.md)

* [Panoramica dell'OCR](../../overview-ocr.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
