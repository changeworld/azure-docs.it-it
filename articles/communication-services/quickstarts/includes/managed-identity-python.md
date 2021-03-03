---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657639"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Aggiungere identità gestite alla soluzione Servizi di comunicazione

### <a name="install-the-client-library-packages"></a>Installare i pacchetti della libreria client

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>Usare i pacchetti della libreria client

Aggiungere quanto segue `import` al codice per usare l'identità di Azure.

```python
from azure.identity import DefaultAzureCredential
```

Gli esempi seguenti usano [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential). Questa credenziale è adatta per ambienti di produzione e di sviluppo.

Per registrare l'applicazione nell'ambiente di sviluppo e configurare le variabili di ambiente, vedere [autorizzare l'accesso con l'identità gestita](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>Creare un'identità ed emettere un token

Nell'esempio di codice seguente viene illustrato come creare un oggetto client del servizio con identità gestita, quindi utilizzare il client per emettere un token per un nuovo utente:

```python
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Inviare un SMS con identità gestita di Azure

L'esempio di codice seguente illustra come creare un oggetto client del servizio con identità gestita di Azure, quindi usare il client per inviare un messaggio SMS:

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'autenticazione](../../concepts/authentication.md)

Può essere utile vedere anche gli articoli seguenti:

- [Altre informazioni sul controllo degli accessi in base al ruolo di Azure](../../../../articles/role-based-access-control/index.yml)
- [Altre informazioni su Azure Identity Library per Python](/net/api/overview/azure/identity-readme)
- [Creazione dei token di accesso utente](../../quickstarts/access-tokens.md)
- [Inviare SMS](../../quickstarts/telephony-sms/send.md)
- [Altre informazioni sugli SMS](../../concepts/telephony-sms/concepts.md)