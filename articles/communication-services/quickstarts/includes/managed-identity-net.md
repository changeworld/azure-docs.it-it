---
ms.openlocfilehash: 2c816f005dea452c3ffa2889aa7d2742a5762759
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657647"
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
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Inviare un SMS con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio SMS con identità gestita, quindi utilizzare il client per inviare un messaggio SMS:

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'autenticazione](../concepts/authentication.md)

Può essere utile vedere anche gli articoli seguenti:

- [Altre informazioni sul controllo degli accessi in base al ruolo di Azure](../../../../articles/role-based-access-control/index.yml)
- [Altre informazioni su Azure Identity Library per .NET](/dotnet/api/overview/azure/identity-readme)
- [Creazione dei token di accesso utente](../../quickstarts/access-tokens.md)
- [Inviare SMS](../telephony-sms/send.md)
- [Altre informazioni sugli SMS](../../concepts/telephony-sms/concepts.md)
