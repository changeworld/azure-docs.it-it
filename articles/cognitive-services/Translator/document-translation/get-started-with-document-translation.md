---
title: Introduzione alla traduzione dei documenti
description: Come creare un servizio di traduzione documenti usando linguaggi e piattaforme di programmazione C#, go, Java, Node.js o Python
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: f6b68ea756253a30f006983c214e287c75637278
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642448"
---
# <a name="get-started-with-document-translation-preview"></a>Introduzione alla conversione di documenti (anteprima)

 In questo articolo si apprenderà come usare la traduzione dei documenti con i metodi dell'API REST HTTP. La traduzione dei documenti è una funzionalità basata sul cloud del servizio di [conversione di Azure](../translator-info-overview.md) .  L'API traduzione documenti consente la conversione di documenti interi mantenendo la struttura del documento di origine e la formattazione del testo.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Un [**account Azure**](https://azure.microsoft.com/free/cognitive-services/)attivo.  Se non si dispone di un account, è possibile [**creare un account gratuito**](https://azure.microsoft.com/free/).

* Una risorsa del servizio di [**conversione**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (**non** una risorsa Servizi cognitivi). 

* Un [**account di archiviazione BLOB di Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). L'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione.

> [!NOTE]
> La traduzione dei documenti è attualmente supportata solo nella risorsa di conversione (a servizio singolo), **non** nella risorsa Servizi cognitivi (multiservizio).

## <a name="get-your-custom-domain-name-and-subscription-key"></a>Ottenere il nome di dominio personalizzato e la chiave di sottoscrizione

> [!IMPORTANT]
>
> * Non è possibile usare l'endpoint trovato nelle chiavi di risorsa portale di Azure e nella pagina dell' _endpoint_ né nell'endpoint di conversione globale, `api.cognitive.microsofttranslator.com` per eseguire richieste HTTP alla conversione dei documenti.
> * **Tutte le richieste API al servizio di traduzione documenti richiedono un endpoint del dominio personalizzato**.

### <a name="what-is-the-custom-domain-endpoint"></a>Che cos'è l'endpoint del dominio personalizzato? 

L'endpoint del dominio personalizzato è un URL formattato con il nome della risorsa, il nome host e le sottodirectory di Translator:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>Trovare il nome di dominio personalizzato

Il parametro **Name-of-your-Resource** , denominato anche *nome di dominio personalizzato*, è il valore immesso nel campo **nome** quando è stata creata la risorsa di conversione.

:::image type="content" source="../media/instance-details.png" alt-text="Immagine dei campi portale di Azure, Crea risorsa, dettagli istantanei, nome.":::

### <a name="get-your-subscription-key"></a>Ottenere la chiave di sottoscrizione

Le richieste al servizio di conversione richiedono una chiave di sola lettura per l'autenticazione dell'accesso.

1. Se è stata creata una nuova risorsa, dopo la distribuzione selezionare **Vai alla risorsa**. Se si dispone di una risorsa di traduzione documenti esistente, passare direttamente alla pagina delle risorse.
1. Nella barra di sinistra fare clic su **chiavi ed endpoint** in *Gestione risorse*.
1. Copiare e incollare la chiave di sottoscrizione in un percorso appropriato, ad esempio il *blocco note di Microsoft*.
1. È possibile incollarlo nel codice riportato di seguito per autenticare la richiesta al servizio di traduzione documenti.

:::image type="content" source="../media/translator-keys.png" alt-text="Immagine del campo ottenere la chiave della sottoscrizione in portale di Azure.":::

## <a name="create-your-azure-blob-storage-containers"></a>Creare i contenitori di archiviazione BLOB di Azure

È necessario  [**creare contenitori**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container) nell' [**account di archiviazione BLOB di Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) per i file di origine, di destinazione e di glossario facoltativo.

* **Contenitore di origine**. Questo contenitore consente di caricare i file per la conversione (obbligatorio).
* **Contenitore di destinazione**. Questo è il contenitore in cui verranno archiviati i file tradotti (obbligatorio).  
* **Contenitore Glossario**. Questo contenitore consente di caricare i file di glossario (facoltativo).  

*Vedere* **creare token di accesso SAS per la traduzione dei documenti**

Gli elementi `sourceUrl` , `targetUrl` e facoltativi `glossaryUrl`  devono includere un token di firma di accesso condiviso (SAS), aggiunto come stringa di query. Il token può essere assegnato al contenitore o a BLOB specifici.

* Il contenitore o il BLOB di **origine** deve avere designato l'accesso in  **lettura** ed **elenco** .
* Il contenitore o il BLOB di **destinazione** deve avere l'accesso in  **scrittura** ed **elenco** designato.
* Il contenitore o il BLOB del **Glossario** deve avere un accesso di  **lettura** ed **elenco** selezionato.

> [!TIP]
>
> * Se si esegue la conversione di **più** file (BLOB) in un'operazione, **delegare l'accesso SAS a livello di contenitore**.  
> * Se si sta traducendo un **singolo** file (BLOB) in un'operazione, **delegare l'accesso SAS a livello di BLOB**.  
>

## <a name="set-up-your-coding-platform"></a>Configurare la piattaforma di codifica

### <a name="c"></a>[C#](#tab/csharp)

* Creare un nuovo progetto.
* Sostituire Program.cs con il codice C# visualizzato di seguito.
* Impostare l'endpoint. chiave di sottoscrizione e valori URL del contenitore in Program.cs.
* Per elaborare i dati JSON, aggiungere [Newtonsoft.Jsnel pacchetto usando l'interfaccia](https://www.nuget.org/packages/Newtonsoft.Json/)della riga di comando di .NET.
* Eseguire il programma dalla directory del progetto.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Creare un nuovo progetto Node.js.
* Installare la libreria Axios con `npm i axios` .
* Copiare il codice incollato di seguito nel progetto.
* Impostare i valori dell'endpoint, della chiave di sottoscrizione e dell'URL del contenitore.
* Eseguire il programma.

### <a name="python"></a>[Python](#tab/python)  

* Creare un nuovo progetto.
* Copiare e incollare il codice di uno degli esempi nel progetto.
* Impostare i valori dell'endpoint, della chiave di sottoscrizione e dell'URL del contenitore.
* Eseguire il programma. Ad esempio: `python translate.py`.
  
### <a name="java"></a>[Java](#tab/java)

* Creare una directory di lavoro per il progetto. Ad esempio:

```powershell
mkdir sample-project
```

* Nella directory del progetto creare la struttura di sottodirectory seguente:  

  src</br>
&emsp; └ principale</br>
&emsp;&emsp;&emsp;└ Java

```powershell
mkdir -p src/main/java/
```

* I file di origine Java (ad esempio, _Sample. Java_) risiedono in src/main/**Java**.

* Nella directory radice, ad esempio esempio *-progetto*, inizializzare il progetto con gradle:

```powershell
gradle init --type basic
```

* Quando viene chiesto di scegliere un linguaggio **DSL**, selezionare **Kotlin**.
* Aggiornare il `build.gradle.kts`  file. Tenere presente che è necessario aggiornare il `mainClassName` a seconda dell'esempio:

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Creare un file Java nella directory **Java** e copiare e incollare il codice dall'esempio specificato. Non dimenticare di aggiungere la chiave e l'endpoint della sottoscrizione.
**Compilare ed eseguire l'esempio dalla directory radice**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)  

* Creare un nuovo progetto go.
* Aggiungere il codice riportato di seguito.
* Impostare i valori dell'endpoint, della chiave di sottoscrizione e dell'URL del contenitore.
* Salvare il file con estensione "go".
* Aprire un prompt dei comandi in un computer in cui è installato Go.
* Compilare il file, ad esempio: "go build example-code.go".
* Eseguire il file, ad esempio: "example-code".

---

## <a name="make-document-translation-requests"></a>Eseguire richieste di traduzione di documenti

Una richiesta di traduzione di documenti batch viene inviata all'endpoint del servizio di conversione tramite una richiesta POST. In caso di esito positivo, il metodo POST restituisce un `202 Accepted`  codice di risposta e la richiesta batch viene creata dal servizio.

### <a name="http-headers"></a>Intestazioni HTTP

Le intestazioni seguenti sono incluse in ogni richiesta dell'API di conversione dei documenti:

|Intestazione HTTP|Descrizione|
|---|--|
|Ocp-Apim-Subscription-Key|**Obbligatorio**: il valore è la chiave di sottoscrizione di Azure per la risorsa di traduzione o servizi cognitivi.|
|Content-Type|**Required**: specifica il tipo di contenuto del payload. I valori accettati sono Application/JSON o charset = UTF-8.|
|Content-Length|**Required**: lunghezza del corpo della richiesta.|

### <a name="post-request-body-properties"></a>Proprietà del corpo della richiesta POST

* Il corpo della richiesta POST è un oggetto JSON denominato `inputs` .
* L' `inputs` oggetto contiene  `sourceURL` `targetURL`  gli indirizzi del contenitore e per le coppie di lingue di origine e di destinazione e può facoltativamente contenere un `glossaryURL` indirizzo del contenitore.
* I `prefix` `suffix` campi e (facoltativo) vengono utilizzati per filtrare i documenti nel contenitore, incluse le cartelle.
* `glossaries`Quando il documento viene convertito, viene applicato un valore per il campo (facoltativo).
* `targetUrl`Per ogni lingua di destinazione deve essere univoco.

>[!NOTE]
> Se nella destinazione esiste già un file con lo stesso nome, verrà sovrascritto.

### <a name="post-a-translation-request"></a>Invia una richiesta di traduzione

> [!IMPORTANT]
>
> * Per gli esempi di codice riportati di seguito, potrebbe essere necessario aggiornare i campi seguenti, a seconda dell'operazione:

>> [!div class="checklist"]
>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`  (ID processo)
>>
> * È possibile trovare il processo `id`  nel valore dell'URL dell'intestazione della risposta del metodo Post `Operation-Location`  . L'ultimo parametro dell'URL è il processo dell'operazione **`id`** .  
> * È anche possibile usare una richiesta GET Jobs per recuperare il processo `id`  per un'operazione di conversione di documenti.
> * Per gli esempi riportati di seguito, è necessario impostare come hardcoded la chiave e l'endpoint indicati; Ricordarsi di rimuovere la chiave dal codice al termine dell'operazione senza pubblicarla.  
>
> Vedere [sicurezza dei servizi cognitivi di Azure](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp) per informazioni su come archiviare in modo sicuro le credenziali e accedervi.

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-without-optional-glossaryurl"></a>Invia il corpo della richiesta senza glossaryURL facoltativo

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
                "storageSource": "AzureBlob",
                "filter": {
                    "prefix": "News",
                    "suffix": ".txt"
                },
                "language": "en"
            },
            "targets": [
                {
                    "targetUrl": "<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="post-request-body-with-optional-glossaryurl"></a>Invia il corpo della richiesta con glossaryURL facoltativo

```json
{
  "inputs":[
    {
      "source":{
        "sourceUrl":"<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
        "storageSource":"AzureBlob",
        "filter":{
          "prefix":"News",
          "suffix":".txt"
        },
        "language":"en"
      },
      "targets":[
        {
          "targetUrl":"<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
          "storageSource":"AzureBlob",
          "category":"general",
          "language":"de",
          "glossaries":[
            {
              "glossaryUrl":"<https://YOUR-GLOSSARY-URL-WITH-READ-LIST-ACCESS-SAS>",
              "format":"xliff",
              "version":"1.2"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="_post-document-translation_-request-code-samples"></a>Esempi di codice di richiesta di _traduzione di documenti post_

Inviare una richiesta di traduzione di documenti batch al servizio di traduzione.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_-code-samples"></a>Esempi di codice per _ottenere formati di file_

Recuperare un elenco di formati di file supportati. Se ha esito positivo, questo metodo restituisce un `200 OK` codice di risposta.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_-code-samples"></a>Esempi di codice di _stato del processo di ottenimento_

Ottenere lo stato corrente per un singolo processo e un riepilogo di tutti i processi in una richiesta di traduzione del documento. Se ha esito positivo, questo metodo restituisce un `200 OK` codice di risposta.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_-code-samples"></a>Esempi di codice di _stato del documento_

### <a name="brief-overview"></a>Breve panoramica

Recuperare lo stato di un documento specifico in una richiesta di traduzione del documento. Se ha esito positivo, questo metodo restituisce un `200 OK` codice di risposta.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_-code-samples"></a>_Eliminare_ gli esempi di codice del processo

### <a name="brief-overview"></a>Breve panoramica

Annulla l'elaborazione o il processo in coda. Solo i documenti per i quali non è stata avviata la traduzione verranno annullati.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Limiti di contenuto

La tabella seguente elenca i limiti per i dati inviati alla traduzione del documento.

|Attributo | Limite|
|---|---|
|Dimensioni del documento| ≤ 40 MB |
|Numero totale di file.|≤ 1000 |
|Dimensioni totali del contenuto in un batch | ≤ 250 MB|
|Numero di lingue di destinazione in un batch| ≤ 10 |
|Dimensioni del file di memoria della traduzione| ≤ 10 MB|

> [!NOTE]
> I limiti di contenuto precedenti sono soggetti a modifiche prima della versione pubblica.

## <a name="learn-more"></a>Altre informazioni

* [Informazioni di riferimento sull'API Translator v3](../reference/v3-0-reference.md)
* [Lingue supportate](../language-support.md)
* [Sottoscrizioni in gestione API di Azure](/azure/api-management/api-management-subscriptions).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un sistema di linguaggio personalizzato usando un convertitore personalizzato](../custom-translator/overview.md)
>
>
