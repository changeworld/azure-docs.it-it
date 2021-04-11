---
ms.openlocfilehash: b9ac58a7cf09463020e0bcd424f5fc5974133657
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113430"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 o versione successiva
- Una risorsa di Servizi di comunicazione distribuita e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Aprire il terminale o la finestra di comando e creare una nuova directory per l'app, quindi passare a tale directory.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Usare un editor di testo per creare un file denominato phone_numbers_sample. py nella directory radice del progetto e aggiungere il codice seguente. Il codice di avvio rapido rimanente verrà aggiunto nelle sezioni seguenti.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>Installare il pacchetto

Rimanendo nella directory dell'applicazione, installare la libreria client per gli SMS di Servizi di comunicazione di Azure per il pacchetto Python usando il comando `pip install`.

```console
pip install azure-communication-phone-numbers
```

## <a name="authenticate-the-phone-numbers-client"></a>Autenticare il client dei numeri di telefono

L'oggetto `PhoneNumbersClient` è abilitato per l'utilizzo dell'autenticazione Azure Active Directory. L'uso dell' `DefaultAzureCredential` oggetto rappresenta il modo più semplice per iniziare a usare Azure Active Directory ed è possibile installarlo tramite il `pip install` comando.

```console
pip install azure-identity
```

Per la creazione di un `DefaultAzureCredential` oggetto è necessario disporre di `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` e `AZURE_TENANT_ID` già impostati come variabili di ambiente con i valori corrispondenti dall'applicazione Azure ad registrata.

Una volta installata la `azure-identity` libreria, è possibile continuare l'autenticazione del client.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

In alternativa, è possibile usare anche l'endpoint e la chiave di accesso della risorsa di comunicazione per l'autenticazione.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Funzioni

Una volta `PhoneNumbersClient` autenticato, è possibile iniziare a lavorare sulle diverse funzioni che è possibile eseguire.

### <a name="search-for-available-phone-numbers"></a>Cerca i numeri di telefono disponibili

Per acquistare i numeri di telefono, è necessario innanzitutto cercare i numeri di telefono disponibili. Per cercare i numeri di telefono, fornire il prefisso, il tipo di assegnazione, le [funzionalità del numero](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)di telefono, il tipo di [numero di telefono](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)e la quantità (la quantità predefinita è impostata su 1). Si noti che per il tipo di numero di telefono con numero verde, l'indicazione del prefisso è facoltativa.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Acquistare i numeri di telefono

Il risultato della ricerca dei numeri di telefono è `PhoneNumberSearchResult` . Contiene un oggetto `searchId` che può essere passato all'API dei numeri di acquisto per acquisire i numeri nella ricerca. Si noti che la chiamata all'API per l'acquisto dei numeri di telefono comporta un addebito per l'account Azure.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient,
    PhoneNumberCapabilityType,
    PhoneNumberAssignmentType,
    PhoneNumberType,
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Ricevi i numeri di telefono acquistati

Dopo un numero di acquisto, è possibile recuperarlo dal client.

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

È anche possibile recuperare tutti i numeri di telefono acquistati.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Aggiorna le funzionalità del numero di telefono

È possibile aggiornare le funzionalità di un numero di telefono acquistato in precedenza.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Numero di telefono versione

È possibile rilasciare un numero di telefono acquistato.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>Eseguire il codice

Da un prompt della console passare alla directory contenente il file phone_numbers_sample. py, quindi eseguire il comando Python seguente per eseguire l'app.

```console
./phone_numbers_sample.py
```
