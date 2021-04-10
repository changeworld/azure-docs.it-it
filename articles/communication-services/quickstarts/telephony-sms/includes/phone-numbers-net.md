---
ms.openlocfilehash: 07a8d792bbb17df1401b5892b09fb7ff2f5f8e52
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629377"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- La versione più recente della [libreria client .NET Core](https://dotnet.microsoft.com/download/dotnet-core) per il sistema operativo in uso.
- Una risorsa attiva di Servizi di comunicazione e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In una finestra del terminale o di comando eseguire il comando `dotnet` per verificare se la libreria client .NET è installata.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `PhoneNumbersQuickstart`. Questo comando crea un semplice progetto C# "Hello World" con un singolo file di origine: **Program.cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Impostare la directory sulla cartella dell'app appena creata e usare il comando `dotnet build` per compilare l'applicazione.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installare il pacchetto

Nella directory dell'applicazione installare la libreria client di PhoneNumbers per la comunicazione di Azure per il pacchetto .NET usando il `dotnet add package` comando.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.5
```

Aggiungere una `using` direttiva all'inizio di **Program. cs** per includere gli spazi dei nomi.

```csharp
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
using Azure.Communication.PhoneNumbers.Models;
```

Aggiornare la `Main` firma della funzione in modo che sia Async.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>Autenticare il client

I client dei numeri di telefono possono essere autenticati usando la stringa di connessione acquisita da risorse di comunicazione di Azure nel [portale di Azure] [azure_portal].

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

I client dei numeri di telefono hanno anche la possibilità di eseguire l'autenticazione con Azure Active Directory l'autenticazione. Con questa opzione, `AZURE_CLIENT_SECRET` `AZURE_CLIENT_ID` e le `AZURE_TENANT_ID` variabili di ambiente devono essere configurate per l'autenticazione.

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Gestire i numeri di telefono

### <a name="search-for-available-phone-numbers"></a>Cerca i numeri di telefono disponibili

Per acquistare i numeri di telefono, è necessario innanzitutto cercare i numeri di telefono disponibili. Per cercare i numeri di telefono, fornire il prefisso, il tipo di assegnazione, le [funzionalità del numero](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)di telefono, il tipo di [numero di telefono](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)e la quantità. Si noti che per il tipo di numero di telefono con numero verde, l'indicazione del prefisso è facoltativa.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Acquistare i numeri di telefono

Il risultato della ricerca dei numeri di telefono è `PhoneNumberSearchResult` . Contiene un oggetto `SearchId` che può essere passato all'API dei numeri di acquisto per acquisire i numeri nella ricerca. Si noti che la chiamata all'API per l'acquisto dei numeri di telefono comporta un addebito per l'account Azure.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionAsync();
```

### <a name="get-phone-numbers"></a>Ottenere i numeri di telefono

Dopo un numero di acquisto, è possibile recuperarlo dal client.
```csharp
var getPhoneNumberResponse = await client.GetPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

È anche possibile recuperare tutti i numeri di telefono acquistati.
``` csharp
var purchasedPhoneNumbers = client.GetPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Aggiorna le funzionalità del numero di telefono

Con un numero acquistato, è possibile aggiornare le funzionalità.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Numero di telefono versione

È possibile rilasciare un numero di telefono acquistato.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionAsync();
````

## <a name="run-the-code"></a>Eseguire il codice

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```console
dotnet run
```

## <a name="sample-code"></a>Codice di esempio

È possibile scaricare l'app di esempio da [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers)
