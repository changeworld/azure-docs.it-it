---
title: Guida introduttiva alla libreria client JavaScript Face
description: Usare la libreria client Face per JavaScript per rilevare visi, trovare un tipo simile (ricerca viso per immagine), identificare i visi (ricerca del riconoscimento facciale) ed eseguire la migrazione dei dati del viso.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: b4a63f76cbcd9e98295f5edcf7ff2d06979e6556
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244798"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Guida introduttiva: libreria client viso per JavaScript

Introduzione al riconoscimento facciale con la libreria client viso per JavaScript. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Il servizio Viso fornisce l'accesso ad algoritmi avanzati per il rilevamento e il riconoscimento dei visi umani nelle immagini.

Usare la libreria client Face per JavaScript per:

* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)
* [Creare un gruppo di persone](#create-a-person-group)
* [Identificare un viso](#identify-a-face)

[Documentazione di riferimento](/javascript/api/@azure/cognitiveservices-face/) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face) | [Pacchetto (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-face) | [Esempi](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* La versione più recente di [Node.js](https://nodejs.org/en/)
* Dopo aver creato la sottoscrizione di Azure, [creare una risorsa Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Viso. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
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

Installare i pacchetti NPM `ms-rest-azure` e `azure-cognitiveservices-face`:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

Il file `package.json` dell'app viene aggiornato con le dipendenze.

Creare un file denominato `index.js` e importare le librerie seguenti:

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](), che contiene gli esempi di codice di questo argomento.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Creare le variabili per l'endpoint e la chiave di Azure della risorsa. 

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa Face creata nella sezione **prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **passaggi successivi**. La chiave e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. 
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) di Servizi cognitivi.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client dell'API Viso per .NET:

|Nome|Descrizione|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | Questa classe rappresenta l'autorizzazione per l'utilizzo del servizio Viso ed è necessaria per tutte le funzionalità del servizio. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi. |
|[Viso](/javascript/api/@azure/cognitiveservices-face/face)|Questa classe gestisce le attività di rilevamento e riconoscimento di base che è possibile eseguire con i visi umani. |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|Questa classe rappresenta tutti i dati rilevati da un singolo viso in un'immagine. È possibile usarla per recuperare informazioni dettagliate sul viso.|
|[Facet](/javascript/api/@azure/cognitiveservices-face/facelist)|Questa classe gestisce i costrutti **FaceList** archiviati nel cloud, che archiviano un set assortito di visi. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| Questa classe gestisce i costrutti **Person** archiviati nel cloud, che archiviano un set di visi che appartengono a una singola persona.|
|[Gruppo](/javascript/api/@azure/cognitiveservices-face/persongroup)| Questa classe gestisce i costrutti **PersonGroup** archiviati nel cloud, che archiviano un set di oggetti **Person** assortiti. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client dell'API Viso per .NET:

* [Autenticare il client](#authenticate-the-client)
* [Rilevare i visi in un'immagine](#detect-faces-in-an-image)
* [Individuare visi simili](#find-similar-faces)
* [Creare un gruppo di persone](#create-a-person-group)
* [Identificare un viso](#identify-a-face)

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js), che contiene gli esempi di codice di questo argomento.

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto **[ApiKeyCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials)** con la chiave e usarlo con l'endpoint per creare un oggetto **[FaceClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient)** .

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Dichiarare i valori globali e la funzione di supporto

I valori globali seguenti sono necessari per diverse operazioni del viso che verranno aggiunte in un secondo momento.

L'URL punta a una cartella di immagini di esempio. L'UUID fungerà da nome e ID per il gruppo che si creerà.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

Si userà la funzione seguente per attendere il completamento del training del gruppo.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Rilevare i visi in un'immagine

### <a name="get-detected-face-objects"></a>Ottenere gli oggetti viso rilevati

Creare un nuovo metodo per rilevare i visi. Il metodo `DetectFaceExtract` elabora tre delle immagini all'URL specificato e crea un elenco di oggetti **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** nella memoria del programma. L'elenco di valori **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** specifica quali caratteristiche estrarre. 

Il `DetectFaceExtract` Metodo quindi analizza e stampa i dati dell'attributo per ogni viso rilevato. Ogni attributo deve essere specificato separatamente nella chiamata all'API di rilevamento viso originale (nell'elenco **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** ). Il codice seguente elabora ogni attributo, ma è probabile che sia necessario usarne solo uno o alcuni.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> È anche possibile rilevare i visi in un'immagine locale. Vedere i metodi [viso](/javascript/api/@azure/cognitiveservices-face/face) , ad esempio [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).

## <a name="find-similar-faces"></a>Individuazione di visi simili

Il codice seguente confronta un singolo viso rilevato (origine) con un set di altri visi (destinazione) per trovare corrispondenze (ricerca di volti per immagine). Quando trova una corrispondenza, visualizza l'ID del viso corrispondente nella console.

### <a name="detect-faces-for-comparison"></a>Rilevare visi per il confronto

Prima di tutto, definire un secondo metodo di rilevamento volto. Per poter confrontare i visi occorre prima rilevarli nelle immagini e questo metodo di rilevamento è ottimizzato per le operazioni di confronto. Non estrae gli attributi dettagliati dei visi come nella sezione precedente e usa un modello di riconoscimento diverso.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Trovare le corrispondenze

Il metodo seguente rileva i visi in un set di immagini di destinazione e in un'unica immagine di origine, quindi li confronta e trova tutte le immagini di destinazione simili all'immagine di origine. Infine, stampa i dettagli della corrispondenza nella console.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Identificare un viso

L'operazione di [Identificazione](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) acquisisce un'immagine di una persona (o più persone) e cerca di individuare l'identità di ogni viso nell'immagine (ricerca del riconoscimento facciale). Confronta ogni viso rilevato con un [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup), un database di oggetti [Person](/javascript/api/@azure/cognitiveservices-face/person) diversi le cui caratteristiche del viso sono note. Per eseguire l'operazione di identificazione, è prima di tutto necessario creare ed eseguire il training di un [gruppo](/javascript/api/@azure/cognitiveservices-face/persongroup).

### <a name="add-faces-to-person-group"></a>Aggiungi visi al gruppo person

Creare la funzione seguente per aggiungere visi a [gruppo](/javascript/api/@azure/cognitiveservices-face/persongroup).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-person-group"></a>Attendi training del gruppo person

Creare la funzione helper seguente per attendere che il gruppo person completi il training.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-person-group"></a>Creare un gruppo di persone

Il codice seguente:
- Crea un [gruppo](/javascript/api/@azure/cognitiveservices-face/persongroup)
- Aggiunge visi al gruppo person chiamando `AddFacesToPersonGroup` , definito in precedenza.
- Addestra il gruppo person.
- Identifica i visi nel gruppo person.

Questo gruppo **Person** e gli oggetti **Person** associati sono ora pronti per essere usati nelle operazioni di verifica, identificazione o raggruppamento.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> È anche possibile creare un **PersonGroup** dalle immagini locali. Vedere i metodi [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) , ad esempio [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="main"></a>Principale

Infine, creare la `main` funzione e chiamarla.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `node` nel file quickstart.

```console
node index.js
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come usare la libreria client Face per JavaScript per eseguire attività di riconoscimento facciale di base. Successivamente, esplorare la documentazione di riferimento per altre informazioni sulla libreria.

> [!div class="nextstepaction"]
> [Riferimento API Viso (JavaScript)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/)

* [Che cos'è il servizio Viso?](../../overview.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js).
