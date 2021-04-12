---
ms.openlocfilehash: 1c4aab27eb72afa473f95f1c0956b5e3d66c3940
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073545"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) versione 8 o successiva.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Una risorsa di Servizi di comunicazione distribuita e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>Aggiungere identità gestite alla soluzione Servizi di comunicazione (Java)

### <a name="install-the-sdk-packages"></a>Installare i pacchetti SDK
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

### <a name="use-the-sdk-packages"></a>Usare i pacchetti SDK

Aggiungere le `import` direttive seguenti al codice per usare Azure Identity e Azure Communication SDK.

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.io.IOException;
import java.util.*;
```

Gli esempi seguenti usano [DefaultAzureCredential](/java/api/com.azure.identity.defaultazurecredential). Questa credenziale è adatta per ambienti di produzione e di sviluppo.

Per un modo semplice per passare all'uso dell'autenticazione di identità gestita, vedere [autorizzare l'accesso con identità gestita](../managed-identity-from-cli.md)

Per un'analisi più approfondita del funzionamento dell'oggetto DefaultAzureCredential e del modo in cui è possibile usarlo in modi non specificati in questa Guida introduttiva, vedere [libreria client di identità di Azure per Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Creare un'identità ed emettere un token con identità gestita

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con identità gestita.
Usare quindi il client per emettere un token per un nuovo utente:

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
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

          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .buildClient();

          // Send the message and check the response for a message id
          SmsSendResult response = smsClient.send(
               "<from-phone-number>",
               "<to-phone-number>",
               "your message"
          );
          return response;
    }
```

