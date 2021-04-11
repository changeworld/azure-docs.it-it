---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450506"
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

Gli esempi seguenti usano [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Questa credenziale è adatta per ambienti di produzione e di sviluppo.

Per un modo semplice per passare all'uso dell'autenticazione di identità gestita, vedere [autorizzare l'accesso con identità gestita](../managed-identity-from-cli.md)

Per un'analisi più approfondita del funzionamento dell'oggetto DefaultAzureCredential e del modo in cui è possibile usarlo in modi non specificati in questa Guida introduttiva, vedere [libreria client di identità di Azure per Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)

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

### <a name="list-all-your-purchased-phone-numbers"></a>Elencare tutti i numeri di telefono acquistati

L'esempio di codice seguente illustra come creare un oggetto client del servizio con identità gestita di Azure, quindi usare il client per visualizzare tutti i numeri di telefono acquistati della risorsa:

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```