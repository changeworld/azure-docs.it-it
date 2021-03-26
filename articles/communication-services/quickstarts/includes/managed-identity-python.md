---
ms.openlocfilehash: b37ebebdb99530ab615a313d9b269b9ce937b7f1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110748"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Aggiungere identità gestite alla soluzione Servizi di comunicazione

### <a name="install-the-sdk-packages"></a>Installare i pacchetti SDK

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>Usare i pacchetti SDK

Aggiungere quanto segue `import` al codice per usare l'identità di Azure.

```python
from azure.identity import DefaultAzureCredential
```

Gli esempi seguenti usano [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential). Questa credenziale è adatta per ambienti di produzione e di sviluppo.

Per registrare l'applicazione nell'ambiente di sviluppo e configurare le variabili di ambiente, vedere [autorizzare l'accesso con l'identità gestita](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>Creare un'identità ed emettere un token

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con identità gestita, quindi utilizzare il client per emettere un token per un nuovo utente:

```python
from azure.communication.identity import CommunicationIdentityClient 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Inviare un SMS con identità gestita di Azure

L'esempio di codice seguente illustra come creare un oggetto client del servizio con identità gestita di Azure, quindi usare il client per inviare un messaggio SMS:

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```
