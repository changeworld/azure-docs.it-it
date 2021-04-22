---
title: Creare il client per il modello distribuito come servizio Web
titleSuffix: Azure Machine Learning
description: Informazioni su come chiamare un endpoint del servizio Web generato quando un modello è stato distribuito da Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-csharp
ms.openlocfilehash: 8cab9331cdd4c15ab76b7c33f956be15ec2259ef
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861194"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Come usare un modello di Azure Machine Learning distribuito come servizio Web


Quando si distribuisce un modello di Azure Machine Learning come servizio Web, viene creato un endpoint dell'API REST. È possibile inviare dati a questo endpoint e ottenere la previsione restituita dal modello. Questo documento illustra come creare client per il servizio Web usando C#, Go, Java e Python.

Si crea un servizio Web quando si distribuisce un modello nell'ambiente locale, Istanze di Azure Container, servizio Azure Kubernetes o FPGA (Field Programmable Gate Array). È possibile recuperare l'URI usato per accedere al servizio Web usando [Azure Machine Learning SDK.](/python/api/overview/azure/ml/intro) Se l'autenticazione è abilitata, è anche possibile usare l'SDK per ottenere le chiavi o i token di autenticazione.

Il flusso di lavoro generale per creare un client che usa un servizio Web di Machine Learning è il seguente:

1. Ottenere le informazioni di connessione usando l'SDK.
1. Determinare il tipo di dati della richiesta usati dal modello.
1. Creare un'applicazione che chiama il servizio Web.

> [!TIP]
> Gli esempi in questo documento vengono creati manualmente senza l'uso di specifiche OpenAPI (Swagger). Se è stata abilitata una specifica OpenAPI per la distribuzione, è possibile usare strumenti come [swagger-codegen](https://github.com/swagger-api/swagger-codegen) per creare librerie client per il servizio.

## <a name="connection-information"></a>Informazioni di connessione

> [!NOTE]
> Usare l'SDK di Azure Machine Learning per ottenere le informazioni sul servizio Web. Si tratta di un SDK per Python. È possibile usare qualsiasi linguaggio per creare un client per il servizio.

La classe [azureml.core.Webservice](/python/api/azureml-core/azureml.core.webservice%28class%29) fornisce le informazioni necessarie per creare un client. Per la creazione di un'applicazione client sono utili le proprietà `Webservice` seguenti:

* `auth_enabled` - Se l'autenticazione con chiave è abilitata, `True` ; in caso contrario, `False` .
* `token_auth_enabled` - Se l'autenticazione basata su token è abilitata, `True` ; in caso contrario, `False` .
* `scoring_uri` - L'indirizzo dell'API REST.
* `swagger_uri` : indirizzo della specifica OpenAPI. Questo URI è disponibile se è stata abilitata la generazione automatica dello schema. Per altre informazioni, vedere [Distribuire modelli con Azure Machine Learning](how-to-deploy-and-where.md).

Esistono diversi modi per recuperare queste informazioni per i servizi Web distribuiti:

# <a name="python"></a>[Python](#tab/python)

* Quando si distribuisce un modello, viene restituito un `Webservice` oggetto con informazioni sul servizio:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* È possibile usare `Webservice.list` per recuperare un elenco dei servizi Web distribuiti per i modelli presenti nella propria area di lavoro. È possibile aggiungere filtri per restringere l'elenco delle informazioni restituite. Per altre informazioni sui dati che è possibile filtrare, vedere la documentazione di riferimento di [Webservice.list](/python/api/azureml-core/azureml.core.webservice.webservice.webservice).

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Se si conosce il nome del servizio distribuito, è possibile creare una nuova istanza di `Webservice` e specificare il nome dell'area di lavoro e del servizio come parametri. Il nuovo oggetto contiene informazioni sul servizio distribuito.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Se si conosce il nome del servizio distribuito, usare il [comando az ml service show:](/cli/azure/ml/service#az_ml_service_show)

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[Portale](#tab/azure-portal)

In studio di Azure Machine Learning, selezionare __Endpoint,__ __Endpoint in tempo reale__ e quindi il nome dell'endpoint. Nei dettagli dell'endpoint, il campo __endpoint REST__ contiene l'URI di assegnazione dei punteggi. __L'URI di Swagger__ contiene l'URI Swagger.

---

La tabella seguente mostra l'aspetto di questi URI:

| Tipo di URI | Esempio |
| ----- | ----- |
| URI di assegnazione dei punteggi | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| Swagger URI | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> L'indirizzo IP sarà diverso per la distribuzione. Ogni cluster del servizio AKS avrà il proprio indirizzo IP condiviso dalle distribuzioni in tale cluster.

### <a name="secured-web-service"></a>Servizio Web protetto

Se il servizio Web distribuito è stato protetto usando un certificato TLS/SSL, è possibile usare [HTTPS](https://en.wikipedia.org/wiki/HTTPS) per connettersi al servizio usando l'URI di assegnazione dei punteggi o swagger. HTTPS consente di proteggere le comunicazioni tra un client e un servizio Web crittografando le comunicazioni tra i due. La crittografia [usa Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS è a volte ancora indicato come *Secure Sockets Layer* (SSL), che era il predecessore di TLS.

> [!IMPORTANT]
> I servizi Web distribuiti da Azure Machine Learning supportano solo TLS versione 1.2. Quando si crea un'applicazione client, assicurarsi che supporti questa versione.

Per altre informazioni, vedere [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Autenticazione per i servizi

Azure Machine Learning due modi per controllare l'accesso ai servizi Web.

|Metodo di autenticazione|Aci|Servizio Azure Kubernetes|
|---|---|---|
|Chiave|Disattivata per impostazione predefinita| Abilitata per impostazione predefinita|
|token| Non disponibile| Disattivato per impostazione predefinita |

Quando si invia una richiesta a un servizio protetto con una chiave o un token, usare l'intestazione __Authorization__ per passare la chiave o il token. La chiave o il token deve essere formattato come `Bearer <key-or-token>` , dove è il valore della chiave o del `<key-or-token>` token.

La differenza principale tra chiavi e token è che le chiavi sono **statiche** e possono essere rigenerate manualmente e i token devono essere aggiornati alla **scadenza.** L'autenticazione basata su chiave è supportata per Istanza di Azure Container e servizio Azure Kubernetes  servizi Web distribuiti e l'autenticazione basata su token è disponibile solo per servizio Azure Kubernetes distribuzione. Per altre informazioni sulla configurazione dell'autenticazione, vedere [Configurare l'autenticazione per i modelli distribuiti come servizi Web.](how-to-authenticate-web-service.md)


#### <a name="authentication-with-keys"></a>Autenticazione con chiavi

Quando si abilita l'autenticazione per una distribuzione, si creano automaticamente le chiavi di autenticazione.

* L'autenticazione è abilitata per impostazione predefinita quando si esegue la distribuzione nel servizio Azure Kubernetes.
* L'autenticazione è disabilitata per impostazione predefinita quando si esegue la distribuzione in Istanze di Azure Container.

Per controllare l'autenticazione, usare il parametro `auth_enabled` quando si crea o si aggiorna una distribuzione.

Se è abilitata l'autenticazione, è possibile usare il metodo `get_keys` per recuperare una chiave di autenticazione primaria e una secondaria:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se è necessario rigenerare una chiave, usare [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29) .

#### <a name="authentication-with-tokens"></a>Autenticazione con token

Quando si abilita l'autenticazione token per un servizio Web, un utente deve fornire un token JWT Azure Machine Learning al servizio Web per accedervi. 

* L'autenticazione dei token è disabilitata per impostazione predefinita quando si esegue la distribuzione in servizio Azure Kubernetes.
* L'autenticazione del token non è supportata quando si esegue la distribuzione in Istanze di Azure Container.

Per controllare l'autenticazione dei token, usare `token_auth_enabled` il parametro durante la creazione o l'aggiornamento di una distribuzione.

Se l'autenticazione basata su token è abilitata, è possibile usare il metodo per recuperare un bearer token e la data e l'ora di `get_token` scadenza dei token:

```python
token, refresh_by = service.get_token()
print(token)
```

Se si dispone dell'interfaccia della riga di comando di Azure e [dell'estensione di Machine Learning,](reference-azure-machine-learning-cli.md)è possibile usare il comando seguente per ottenere un token:

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> Attualmente l'unico modo per recuperare il token è usare l'SDK Azure Machine Learning o l'estensione di Machine Learning dell'interfaccia della riga di comando di Azure.

Sarà necessario richiedere un nuovo token dopo l'ora del `refresh_by` token. 

## <a name="request-data"></a>Dati richiesta

L'API REST prevede che il corpo della richiesta sia un documento JSON con la struttura seguente:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> La struttura dei dati deve corrispondere allo script di punteggio e al modello nelle stime del servizio. Lo script di punteggio può modificare i dati prima di trasferirli al modello.

### <a name="binary-data"></a>Dati binari

Per informazioni su come abilitare il supporto per i dati binari nel servizio, vedere [Dati binari](how-to-deploy-advanced-entry-script.md#binary-data).

> [!TIP]
> L'abilitazione del supporto per i dati binari avviene nel file score.py usato dal modello distribuito. Dal client usare la funzionalità HTTP del linguaggio di programmazione. Ad esempio, il frammento di codice seguente invia il contenuto di un file JPG a un servizio Web:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Condivisione di risorse tra le origini (CORS)

Per informazioni sull'abilitazione del supporto CORS nel servizio, vedere [Condivisione di risorse tra origini](how-to-deploy-advanced-entry-script.md#cors).

## <a name="call-the-service-c"></a>Chiamare il servizio (C#)

In questo esempio viene illustrato come utilizzare C# per chiamare il servizio Web creato dall’esempio [Eseguire il training sul notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb):

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

I risultati restituiti sono simili al seguente documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Chiamare il servizio (Go)

In questo esempio viene illustrato come utilizzare Go per chiamare il servizio Web creato dall’esempio [Eseguire il training sul notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb):

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

I risultati restituiti sono simili al seguente documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Chiamare il servizio (Java)

In questo esempio viene illustrato come utilizzare Java per chiamare il servizio Web creato dall’esempio [Eseguire il training sul notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb):

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

I risultati restituiti sono simili al seguente documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Chiamare il servizio (Python)

In questo esempio viene illustrato come utilizzare Python per chiamare il servizio Web creato dall’esempio [Eseguire il training sul notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb):

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

I risultati restituiti sono simili al seguente documento JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>Schema del servizio Web (specifica OpenAPI)

Se è stata usata la generazione automatica dello schema con la [distribuzione,](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri)è possibile ottenere l'indirizzo della specifica OpenAPI per il servizio usando la proprietà swagger_uri . (Ad esempio, `print(service.swagger_uri)` .) Usare una richiesta GET o aprire l'URI in un browser per recuperare la specifica.

Il documento JSON seguente è un esempio di schema (specifica OpenAPI) generato per una distribuzione:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Per altre informazioni, vedere [Specifica OpenAPI.](https://swagger.io/specification/)

Per un'utilità in grado di creare librerie client dalla specifica, vedere [swagger-codegen](https://github.com/swagger-api/swagger-codegen).


> [!TIP]
> È possibile recuperare il documento JSON dello schema dopo aver distribuito il servizio. Usare la [swagger_uri](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) dal servizio Web distribuito (ad esempio, ) per ottenere `service.swagger_uri` l'URI del file Swagger del servizio Web locale.

## <a name="consume-the-service-from-power-bi"></a>Utilizzare il servizio da Power BI

Power BI supporta l'utilizzo Azure Machine Learning servizi Web per arricchire i dati in Power BI con stime. 

Per generare un servizio Web supportato per l'utilizzo in Power BI, lo schema deve supportare il formato richiesto da Power BI. [Informazioni su come creare uno Power BI supportato da .](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint)

Dopo aver distribuito il servizio Web, può essere utilizzato dai flussi di dati di Power BI. [Informazioni su come usare un servizio Web Azure Machine Learning da Power BI](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare un'architettura di riferimento per l'assegnazione dei punteggi in tempo reale dei modelli di Python e deep learning, passare al Centro [architetture di Azure.](/azure/architecture/reference-architectures/ai/realtime-scoring-python)
