---
title: 'Firmare una richiesta HTTP con C #'
description: Si tratta della versione C# della firma di una richiesta HTTP con una firma HMAC per i servizi di comunicazione.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 3c1b56f81e5164bbdfa94fdaeca5f5f1f55b3b51
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551874"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:
- Creare un account Azure con una sottoscrizione attiva. Per informazioni dettagliate, vedere [Creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installare [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Creare una risorsa di Servizi di comunicazione di Azure. Per informazioni dettagliate, vedere [creare una risorsa di comunicazione di Azure](../../quickstarts/create-communication-resource.md). Per questa esercitazione è necessario registrare i **resourceEndpoint** e  **resourceAccessKey** .



## <a name="sign-an-http-request-with-c"></a>Firmare una richiesta HTTP con C #
Per generare una firma HMAC per ogni richiesta HTTP, l'autenticazione della chiave di accesso usa una chiave privata condivisa. Questa firma viene generata con l'algoritmo SHA256 e viene inviata nell' `Authorization` intestazione usando lo `HMAC-SHA256` schema. Ad esempio:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature`È costituito da: 

- Verbo HTTP (ad esempio `GET` o `PUT` )
- Percorso della richiesta HTTP
- Data
- Host
- x-ms-content-SHA256

## <a name="setting-up"></a>Configurazione
I passaggi seguenti descrivono come costruire l'intestazione di autorizzazione:

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `SignHmacTutorial`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: **Program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Impostare la directory sulla cartella dell'app appena creata e usare il comando `dotnet build` per compilare l'applicazione.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Installare il pacchetto

Installare il pacchetto `Newtonsoft.Json` , usato per la serializzazione del corpo:

```console
dotnet add package Newtonsoft.Json
```

Aggiornare la `Main` dichiarazione di metodo per supportare il codice asincrono. Per iniziare, usare il codice seguente:

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here
        }
    }
}

```
## <a name="create-a-request-message"></a>Creazione di un messaggio di richiesta

Per questo esempio si firmerà una richiesta per creare una nuova identità usando l'API di autenticazione di servizi di comunicazione (versione `2021-03-07` )

Aggiungere il codice seguente al `Main` Metodo:

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create an uri you are going to call
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Sostituire `resourceEndpoint` con il valore dell'endpoint della risorsa reale.

## <a name="create-content-hash"></a>Crea hash contenuto

L'hash del contenuto fa parte della firma HMAC. Usare il codice seguente per calcolare l'hash del contenuto. È possibile aggiungere questo metodo a `Progam.cs` nel `Main` metodo.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Calcolare una firma
Usare il codice seguente per creare un metodo per calcolare la firma HMAC.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Sostituire `resourceAccessKey` con la chiave di accesso della risorsa reale di servizi di comunicazione di Azure.

## <a name="create-an-authorization-header-string"></a>Creare una stringa di intestazione di autorizzazione

Verrà ora creata la stringa che verrà aggiunta all'intestazione dell'autorizzazione:

1. Calcolo di un hash di contenuto
2. Specificare il timestamp UTC (Coordinated Universal Time)
3. Preparare una stringa da firmare
4. Calcolo della firma
5. Concatena la stringa, che verrà usata nell'intestazione dell'autorizzazione
 
Aggiungere il codice seguente al `Main` Metodo:

```csharp
// Compute a content hash
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in authorization header
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Aggiungere intestazioni a requestMessage

Usare il codice seguente per aggiungere le intestazioni obbligatorie al `requestMessage` :

```csharp
//Add content hash header
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//add date header
requestMessage.Headers.Add("Date", date);
//add Authorization header
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Testare il client
Chiamare l'endpoint usando `HttpClient` e controllare la risposta.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
