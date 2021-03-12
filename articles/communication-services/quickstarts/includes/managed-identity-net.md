---
ms.openlocfilehash: 8295849a7177eab774517816a239472677689434
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020840"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Aggiungere identità gestite alla soluzione Servizi di comunicazione (.NET)

### <a name="install-the-client-library-packages"></a>Installare i pacchetti della libreria client

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Usare i pacchetti della libreria client

Aggiungere le `using` direttive seguenti al codice per usare le librerie di identità di Azure e client di archiviazione di Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

Gli esempi seguenti usano [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Questa credenziale è adatta per ambienti di produzione e di sviluppo.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`e le `AZURE_TENANT_ID` variabili di ambiente sono necessarie per creare un `DefaultAzureCredential` oggetto. Per creare un'applicazione registrata nell'ambiente di sviluppo e configurare le variabili di ambiente, vedere [autorizzare l'accesso con l'identità gestita](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Creare un'identità ed emettere un token con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con token di Azure Active Directory.

Usare quindi il client per emettere un token per un nuovo utente:

```csharp
     public async Task<Response<AccessToken>> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
          var identity = identityResponse.Value;

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Inviare un SMS con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio SMS con identità gestita, quindi utilizzare il client per inviare un messaggio SMS:

```csharp
     public async Task SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );
      }
```

