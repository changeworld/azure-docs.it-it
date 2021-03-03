---
ms.openlocfilehash: c5d83cc709c334e15a1c13e64ba17ef24835fed0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657656"
---
## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>Aggiungere identità gestite alla soluzione Servizi di comunicazione (Java)

### <a name="install-the-client-library-packages"></a>Installare i pacchetti della libreria client
Nel file pom.xml aggiungere gli elementi di dipendenza seguenti al gruppo di dipendenze.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-client-library-packages"></a>Usare i pacchetti della libreria client

Aggiungere le `import` direttive seguenti al codice per usare le librerie client di Azure Identity e Azure Communication.

```java
import com.azure.identity.*;
import com.azure.communication.sms.*;
import com.azure.communication.identity.*;
import com.azure.communication.common.*;
```

Gli esempi seguenti usano [DefaultAzureCredential](/java/api/azure.identity.defaultazurecredential). Questa credenziale è adatta per ambienti di produzione e di sviluppo.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`e le `AZURE_TENANT_ID` variabili di ambiente sono necessarie per creare un `DefaultAzureCredential` oggetto. Per creare un'applicazione registrata nell'ambiente di sviluppo e configurare le variabili di ambiente, vedere [autorizzare l'accesso con l'identità gestita](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Creare un'identità ed emettere un token con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con identità gestita.
Usare quindi il client per emettere un token per un nuovo utente:

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          AccessToken userToken = communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
          return userToken;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Inviare un SMS con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con identità gestita, quindi utilizzare il client per inviare un messaggio SMS:

```java
     public SendSmsResponse sendSms() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          // Send the message and check the response for a message id
          SendSmsResponse response = smsClient.sendMessage(
               new PhoneNumberIdentifier("<leased-phone-number>"),
               to,
               "your message",
               options /* Optional */
          );
          return response;
    }
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'autenticazione](../concepts/authentication.md)

Può essere utile vedere anche gli articoli seguenti:

- [Altre informazioni sul controllo degli accessi in base al ruolo di Azure](../../../../articles/role-based-access-control/index.yml)
- [Altre informazioni su Azure Identity Library per Java](/java/api/overview/azure/identity-readme)
- [Creazione dei token di accesso utente](../../quickstarts/access-tokens.md)
- [Inviare SMS](../../quickstarts/telephony-sms/send.md)
- [Altre informazioni sugli SMS](../../concepts/telephony-sms/concepts.md)
