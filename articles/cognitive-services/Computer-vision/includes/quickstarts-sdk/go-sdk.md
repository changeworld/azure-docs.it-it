---
title: 'Guida introduttiva: libreria client per il riconoscimento ottico dei caratteri per go'
titleSuffix: Azure Cognitive Services
description: Introduzione alla libreria client per il riconoscimento ottico dei caratteri per go con questa Guida introduttiva.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 0ae059054c68da662e05342525987f6925184906
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073455"
---
<a name="HOLTop"></a>

Usare la libreria client OCR per leggere testo stampato e scritto a mano dalle immagini.

[Documentazione di riferimento](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [Pacchetto](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Versione più recente di [Go](https://golang.org/dl/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale </a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-go-project-directory"></a>Creare una directory di progetto Go

In una finestra della console, ad esempio cmd, PowerShell o Bash, creare e passare a un'area di lavoro per il progetto Go, denominato `my-app`.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

L'area di lavoro conterrà tre cartelle:

* **src**: questa directory conterrà il codice sorgente e i pacchetti. Tutti i pacchetti installati con il comando `go get` saranno disponibili in questa directory.
* **pkg**: questa directory conterrà gli oggetti pacchetto Go compilati. Tutti i file hanno estensione `.a`.
* **bin**: questa directory conterrà i file eseguibili binari creati durante l'esecuzione di `go install`.

> [!TIP]
> Per altre informazioni sulla struttura di un'area di lavoro Go, vedere la [documentazione del linguaggio Go](https://golang.org/doc/code.html#Workspaces). Questa guida include informazioni per l'impostazione di `$GOPATH` e `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installare la libreria client per Go

Successivamente, installare la libreria client per Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

oppure, se si usa DEP, all'interno del repository eseguire:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Creare un'applicazione Go 

Successivamente, creare un file nella directory **src** denominato `sample-app.go`:

```bash
cd src
touch sample-app.go
```

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go), che contiene gli esempi di codice di questo argomento.

Aprire `sample-app.go` nell'IDE o nell'editor di testo preferito.

Dichiarare un contesto alla radice dello script. Questo oggetto sarà necessario per eseguire la maggior parte delle chiamate di funzione Visione artificiale.

### <a name="find-the-subscription-key-and-endpoint"></a>Trovare la chiave e l'endpoint della sottoscrizione

Accedere al portale di Azure. Se la risorsa Visione artificiale creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. È possibile trovare la chiave e l'endpoint della sottoscrizione nella pagina **chiave ed endpoint** della risorsa in **Gestione risorse**. 

Creare variabili per la chiave e l'endpoint della sottoscrizione Visione artificiale. Incollare la chiave e l'endpoint della sottoscrizione nel codice seguente, dove indicato. Il formato dell'endpoint del Visione artificiale è `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Ricordarsi di rimuovere la chiave di sottoscrizione dal codice al termine dell'operazione senza pubblicarla. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, ad esempio [Azure Key Vault](../../../../key-vault/general/overview.md).

Si inizierà quindi ad aggiungere codice per eseguire diverse operazioni OCR.

> [!div class="nextstepaction"]
> [Il client è stato configurato](?success=set-up-client#object-model) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di OCR go SDK.

|Nome|Descrizione|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale, ad esempio l'analisi delle immagini e la lettura del testo. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per eseguire la maggior parte delle operazioni con le immagini.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Questo tipo contiene i risultati di un'operazione di lettura batch. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice illustrano come eseguire le attività seguenti con la libreria client OCR per go:

* [Autenticare il client](#authenticate-the-client)
* [Leggere il testo stampato e scritto a mano](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticare il client

> [!NOTE]
> Questo passaggio presuppone che siano state [create variabili di ambiente](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) per la chiave e l'endpoint del servizio Visione artificiale, denominate rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

Creare una funzione `main` e aggiungervi il codice seguente per creare un'istanza di un client con l'endpoint e la chiave.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Il client è stato autenticato](?success=authenticate-client#read-printed-and-handwritten-text) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Leggere il testo stampato e scritto a mano

Il servizio OCR può leggere il testo visibile in un'immagine e convertirlo in un flusso di caratteri. Il codice in questa sezione definisce una funzione, `RecognizeTextReadAPIRemoteImage`, che usa l'oggetto client per rilevare ed estrarre il testo stampato o scritto a mano nell'immagine.

Aggiungere il riferimento all'immagine di esempio e la chiamata di funzione nella funzione `main`.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> È anche possibile estrarre il testo da un'immagine locale. Vedere i metodi [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient), ad esempio **BatchReadFileInStream**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).

### <a name="call-the-read-api"></a>Chiamare l'API di lettura

Definire la nuova funzione per la lettura del testo, `RecognizeTextReadAPIRemoteImage`. Aggiungere il codice seguente, che chiama il metodo **BatchReadFile** per l'immagine specificata. Questo metodo restituisce un ID operazione e avvia un processo asincrono per leggere il contenuto dell'immagine.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Ottenere risultati della lettura

Ottenere quindi l'ID operazione restituito dalla chiamata a **BatchReadFile** e usarlo con il metodo **GetReadOperationResult** per eseguire una query sul servizio per ottenere i risultati dell'operazione. Il codice seguente controlla l'operazione a intervalli di un secondo fino a quando non vengono restituiti i risultati. Stampa quindi i dati di testo estratti nella console.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Visualizzare i risultati della lettura

Aggiungere il codice seguente per analizzare e visualizzare i dati di testo recuperati e terminare la definizione della funzione.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Il testo è stato letto](?success=read-printed-handwritten-text#run-the-application) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla directory dell'applicazione con il comando `go run`.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [L'applicazione è stata eseguita](?success=run-the-application#clean-up-resources) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Le risorse sono state pulite](?success=clean-up-resources#next-steps) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come installare la libreria client OCR e usare l'API Read. Quindi, altre informazioni sulle funzionalità dell'API di lettura.

> [!div class="nextstepaction"]
>[Chiamare l'API di lettura](../../Vision-API-How-to-Topics/call-read-api.md)

* [Panoramica dell'OCR](../../overview-ocr.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
