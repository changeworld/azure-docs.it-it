---
title: Installare i contenitori Docker OCR di lettura da Visione artificiale
titleSuffix: Azure Cognitive Services
description: Usare i contenitori Docker read OCR Visione artificiale per estrarre testo da immagini e documenti, in locale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: locale, OCR, Docker, contenitore
ms.openlocfilehash: dead48d7d449d1d403359c518eb842b32a54c634
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779094"
---
# <a name="install-read-ocr-docker-containers"></a>Installare i contenitori Docker OCR di lettura

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

I contenitori consentono di eseguire le API di Visione artificiale nell'ambiente in uso. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati. Questo articolo descrive come scaricare, installare ed eseguire Visione artificiale contenitori.

Il *contenitore* Read OCR consente di estrarre testo stampato e scritto a mano da immagini e documenti con supporto per i formati di file JPEG, PNG, BMP, PDF e TIFF. Per altre informazioni, vedere la guida alle [modalità di lettura dell'API](Vision-API-How-to-Topics/call-read-api.md).

## <a name="read-32-container"></a>Leggere il contenitore 3.2

Il contenitore OCR Read 3.2 offre:
* Nuovi modelli per una maggiore accuratezza.
* Supporto per più lingue all'interno dello stesso documento.
* Supporto per un totale di 73 lingue. Vedere l'elenco completo delle [lingue supportate da OCR.](./language-support.md#optical-character-recognition-ocr)
* Una singola operazione per documenti e immagini.
* Supporto per documenti e immagini di dimensioni maggiori.
* Punteggi di attendibilità.
* Supporto per i documenti con testo stampato e scritto a mano.
* Possibilità di estrarre testo solo da pagine selezionate in un documento.
* Scegliere l'ordine di output della riga di testo dal valore predefinito a un ordine di lettura più naturale solo per le lingue latini.
* Classificazione delle righe di testo come stile scritto a mano o meno solo per le lingue latini.

Se attualmente si usano contenitori read 2.0, vedere la guida alla migrazione per informazioni sulle modifiche nelle nuove versioni. [](read-container-migration-guide.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di usare i contenitori, è necessario soddisfare i prerequisiti seguenti:

|Obbligatoria|Scopo|
|--|--|
|Motore Docker| È necessario il motore Docker installato in un [computer host](#the-host-computer). Docker offre pacchetti per la configurazione dell'ambiente Docker in [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Per una panoramica dei concetti fondamentali relativi a Docker e ai contenitori, vedere [Docker overview](https://docs.docker.com/engine/docker-overview/) (Panoramica di Docker).<br><br> Docker deve essere configurato per consentire ai contenitori di connettersi ai dati di fatturazione e inviarli ad Azure. <br><br> **In Windows** Docker deve essere configurato anche per supportare i contenitori Linux.<br><br>|
|Familiarità con Docker | È opportuno avere una conoscenza di base dei concetti relativi a Docker, tra cui registri, repository, contenitori e immagini dei contenitori, nonché dei comandi `docker` di base.| 
|Visione artificiale risorsa |Per usare il contenitore, è necessario disporre di:<br><br>Una risorsa **Visione artificiale** Azure e la chiave API associata all'URI dell'endpoint. Entrambi i valori sono disponibili nelle pagine Panoramica e Chiavi per la risorsa e sono necessari per avviare il contenitore.<br><br>**{API_KEY}**: una delle due chiavi di risorsa disponibili nella **pagina** Chiavi<br><br>**{ENDPOINT_URI}**: endpoint fornito nella **pagina Panoramica**|

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare.

## <a name="request-approval-to-run-the-container"></a>Richiedere l'approvazione per eseguire il contenitore

Compilare e inviare il [modulo di richiesta per](https://aka.ms/csgate) richiedere l'approvazione per eseguire il contenitore. 

[!INCLUDE [Request access to run the container](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Computer host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Supporto avanzato dell'estensione vettoriale

Il computer **host** è il computer che esegue il contenitore Docker. L'host *deve supportare* [AVX2 (Advanced Vector Extensions).](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) È possibile verificare il supporto di AVX2 in host Linux con il comando seguente:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> Il computer host è *necessario* per supportare AVX2. Il *contenitore non funzionerà* correttamente senza il supporto di AVX2.

### <a name="container-requirements-and-recommendations"></a>Indicazioni e requisiti per i contenitori

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Sono disponibili immagini del contenitore per La lettura.

| Contenitore | Registro Contenitori/Repository/Nome immagine |
|-----------|------------|
| Leggere la versione 2.0-preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Leggere la versione 3.2 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2` |

Usare il comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare un'immagine del contenitore.

### <a name="docker-pull-for-the-read-ocr-container"></a>Pull Docker per il contenitore Read OCR

# <a name="version-32"></a>[Versione 3.2](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[Versione 2.0-preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Come usare il contenitore

Dopo aver aggiunto il contenitore nel [computer host](#the-host-computer), seguire questa procedura per usare il contenitore.

1. [Eseguire il contenitore](#run-the-container-with-docker-run), con le impostazioni di fatturazione necessarie. Sono disponibili altri [esempi](computer-vision-resource-container-config.md) del comando `docker run`. 
1. [Eseguire una query sull'endpoint di stima del contenitore.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Eseguire il contenitore con `docker run`

Usare il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) per eseguire il contenitore. Per informazioni [dettagliate su come](#gathering-required-parameters) ottenere i valori e , vedere raccolta `{ENDPOINT_URI}` dei parametri `{API_KEY}` obbligatori.

[Sono](computer-vision-resource-container-config.md#example-docker-run-commands) disponibili `docker run` esempi del comando .

# <a name="version-32"></a>[Versione 3.2](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue il contenitore Read OCR dall'immagine del contenitore.
* Alloca 8 core CPU e 18 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

# <a name="version-20-preview"></a>[Versione 2.0-preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue il contenitore Read OCR dall'immagine del contenitore.
* Alloca 8 core CPU e 16 gigabyte (GB) di memoria.
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore.
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.

---


Sono disponibili altri [esempi](./computer-vision-resource-container-config.md#example-docker-run-commands) del comando `docker run`. 

> [!IMPORTANT]
> È necessario specificare le opzioni `Eula`, `Billing` e `ApiKey` per eseguire il contenitore. In caso contrario, il contenitore non si avvia.  Per altre informazioni, vedere[Fatturazione](#billing).

Se è necessaria una velocità effettiva più elevata, ad esempio quando si elaborano file a [](../../storage/common/storage-account-create.md) più pagine, è consigliabile distribuire più contenitori in un [cluster Kubernetes](deploy-computer-vision-on-premises.md)usando Archiviazione di Azure e [Coda di Azure.](../../storage/queues/storage-queues-introduction.md)

Se si usa il Archiviazione di Azure per archiviare le immagini [](../../storage/common/storage-configure-connection-string.md) per l'elaborazione, è possibile creare una stringa di connessione da usare quando si chiama il contenitore.

Per trovare la stringa di connessione:

1. Passare ad **Account di archiviazione** nel portale di Azure e trovare l'account.
2. Fare clic su **Chiavi di accesso** nell'elenco di spostamento a sinistra.
3. La stringa di connessione si trova sotto **Stringa di connessione**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Eseguire una query sull'endpoint di stima del contenitore

Il contenitore fornisce le API dell'endpoint di stima della query basata su REST. 

# <a name="version-32"></a>[Versione 3.2](#tab/version-3-2)

Usare l'host `http://localhost:5000` per le API del contenitore. È possibile visualizzare il percorso Swagger all'indirizzo: `http://localhost:5000/swagger/vision-v3.2-read/swagger.json` .

# <a name="version-20-preview"></a>[Versione 2.0-preview](#tab/version-2)

Usare l'host `http://localhost:5000` per le API del contenitore. È possibile visualizzare il percorso Swagger all'indirizzo: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Lettura asincrona


# <a name="version-32"></a>[Versione 3.2](#tab/version-3-2)

È possibile usare le operazioni e insieme per leggere in modo asincrono un'immagine, analogamente al modo in cui il servizio Visione artificiale `POST /vision/v3.2/read/analyze` usa le operazioni REST `GET /vision/v3.2/read/operations/{operationId}` corrispondenti. Il metodo POST asincrono restituirà `operationId` un oggetto usato come identifer alla richiesta HTTP GET.


Nell'interfaccia utente di Swagger selezionare `Analyze` per espanderlo nel browser. Selezionare quindi **Prova scegliere**  >  **il file**. In questo esempio si userà l'immagine seguente:

![tabulazioni e spazi](media/tabs-vs-spaces.png)

Al termine dell'esecuzione del post asincrono, viene restituito un **codice di stato HTTP 202.** Come parte della risposta, è presente `operation-location` un'intestazione che contiene l'endpoint risultato per la richiesta.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

è `operation-location` l'URL completo ed è accessibile tramite HTTP GET. Ecco la risposta JSON dall'esecuzione `operation-location` dell'URL dall'immagine precedente:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Versione 2.0-preview](#tab/version-2)

È possibile usare le operazioni e insieme per leggere in modo asincrono un'immagine, analogamente al modo in cui il servizio Visione artificiale `POST /vision/v2.0/read/core/asyncBatchAnalyze` usa le operazioni REST `GET /vision/v2.0/read/operations/{operationId}` corrispondenti. Il metodo POST asincrono `operationId` restituirà un oggetto usato come identifer alla richiesta HTTP GET.

Nell'interfaccia utente di Swagger selezionare `asyncBatchAnalyze` per espanderlo nel browser. Selezionare quindi **Prova Scegliere**  >  **il file**. In questo esempio si userà l'immagine seguente:

![tabulazioni e spazi](media/tabs-vs-spaces.png)

Al termine dell'esecuzione del post asincrono, viene restituito un **codice di stato HTTP 202.** Come parte della risposta, è presente `operation-location` un'intestazione che contiene l'endpoint risultato per la richiesta.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

è `operation-location` l'URL completo ed è accessibile tramite HTTP GET. Ecco la risposta JSON dall'esecuzione `operation-location` dell'URL dall'immagine precedente:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> Se si distribuiscono più contenitori read OCR dietro un servizio di bilanciamento del carico, ad esempio in Docker Compose o Kubernetes, è necessario disporre di una cache esterna. Poiché il contenitore di elaborazione e il contenitore di richieste GET potrebbero non essere uguali, una cache esterna archivia i risultati e li condivide tra contenitori. Per informazioni dettagliate sulle impostazioni della cache, vedere [Configurare Visione artificiale contenitori Docker.](./computer-vision-resource-container-config.md)

### <a name="synchronous-read"></a>Lettura sincrona

È possibile usare l'operazione seguente per leggere in modo sincrono un'immagine. 

# <a name="version-32"></a>[Versione 3.2](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Versione 2.0-preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Quando l'immagine viene letta nella sua interezza, l'API restituisce solo una risposta JSON. L'unica eccezione è se si verifica un errore. Quando si verifica un errore, viene restituito il codice JSON seguente:

```json
{
    "status": "Failed"
}
```

L'oggetto risposta JSON ha lo stesso oggetto grafico della versione asincrona. Se si è un utente JavaScript e si vuole l'type safety, è consigliabile usare TypeScript per eseguire il cast della risposta JSON.

Per un caso d'uso di esempio, vedere la <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">sandbox TypeScript qui</a> e selezionare **Esegui** per visualizzarne la facilità d'uso.

## <a name="stop-the-container"></a>Arrestare il contenitore

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si esegue il contenitore con un punto di [montaggio](./computer-vision-resource-container-config.md#mount-settings) di output e la registrazione attivata, il contenitore genera file di log utili per risolvere i problemi che si verificano durante l'avvio o l'esecuzione del contenitore.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fatturazione

I contenitori di Servizi cognitivi inviano informazioni di fatturazione ad Azure usando la risorsa corrispondente nell'account Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Per altre informazioni su queste opzioni, vedere [Configurare i contenitori](./computer-vision-resource-container-config.md). 

## <a name="summary"></a>Riepilogo

In questo articolo sono stati descritti i concetti e il flusso di lavoro per scaricare, installare ed eseguire i contenitori Visione artificiale. In sintesi:

* Visione artificiale un contenitore Linux per Docker, incapsulando Read.
* L'immagine del contenitore di lettura richiede che sia eseguita da un'applicazione. 
* Le immagini dei contenitori vengono eseguite in Docker.
* È possibile usare l'API REST o l'SDK per chiamare operazioni nei contenitori read OCR specificando l'URI host del contenitore.
* Quando si crea un'istanza di un contenitore, è necessario specificare le informazioni di fatturazione.

> [!IMPORTANT]
> I contenitori di Servizi cognitivi non sono concessi in licenza per l'esecuzione senza essere connessi ad Azure per la misurazione. I clienti devono consentire ai contenitori di comunicare sempre le informazioni di fatturazione al servizio di misurazione. I contenitori di Servizi cognitivi non inviano a Microsoft i dati dei clienti, ad esempio l'immagine o il testo analizzato.

## <a name="next-steps"></a>Passaggi successivi

* Rivedere [Configurare i contenitori](computer-vision-resource-container-config.md) per informazioni sulle impostazioni di configurazione.
* Per altre informazioni sul riconoscimento del testo stampato e scritto a mano, vedere la panoramica di [OCR](overview-ocr.md)
* Per informazioni [dettagliate sui](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) metodi supportati dal contenitore, vedere l'API Lettura.
* Fare riferimento alle [domande frequenti](FAQ.md) per risolvere i problemi correlati alle funzionalità di Analisi del testo.
* Usare altri [contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
