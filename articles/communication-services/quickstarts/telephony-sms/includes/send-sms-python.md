---
title: includere file
description: includere file
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: aba9b71ec2fbfedecf08577c7bd2eae7a28a5588
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644337"
---
Introduzione ai servizi di comunicazione di Azure tramite i servizi di comunicazione Python SMS SDK per inviare messaggi SMS.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- [Python](https://www.python.org/downloads/) 2,7 o 3.6 +.
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un numero di telefono abilitato per gli SMS. [Ottenere un numero di telefono](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In una finestra del terminale o di comando eseguire il comando `python --version` per verificare se Python è installato.
- Per visualizzare i numeri di telefono associati alla risorsa di Servizi di comunicazione, accedere al [portale di Azure](https://portal.azure.com/), individuare la risorsa di Servizi di comunicazione e aprire la scheda dei **numeri di telefono** nel riquadro di spostamento sinistro.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Aprire la finestra del terminale o di comando, creare una nuova directory per l'app e passare a tale directory.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Usare un editor di testo per creare un file denominato **start-sms.py** nella directory radice del progetto e aggiungere la struttura per il programma, inclusa la gestione delle eccezioni di base. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Installare il pacchetto

Quando si è ancora nella directory dell'applicazione, installare il pacchetto SMS SDK per Azure Communication Services per Python usando il `pip install` comando.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Azure Communication Services SMS SDK per Python.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Questa classe è necessaria per tutte le funzionalità SMS. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per inviare messaggi SMS.                                                                                                                 |
| SmsSendResult               | Questa classe contiene il risultato del servizio SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di **SmsClient** con la stringa di connessione. Informazioni su come [gestire la stringa di connessione della risorsa](../../create-communication-resource.md#store-your-connection-string).

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
Per semplicità si usano stringhe di connessione in questa Guida introduttiva, ma negli ambienti di produzione è consigliabile usare [identità gestite](../../../quickstarts/managed-identity.md) perché sono più sicure e gestibili su larga scala.


## <a name="send-a-11-sms-message"></a>Invia un messaggio SMS 1:1

Per inviare un messaggio SMS a un singolo destinatario, chiamare il ```send``` metodo da **SmsClient** con un singolo numero di telefono del destinatario. È anche possibile passare parametri facoltativi per specificare se il report di recapito deve essere abilitato e impostare tag personalizzati. Aggiungere questo codice alla fine del blocco `try` in **send-sms.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

È necessario sostituire `<from-phone-number>` con un numero di telefono abilitato per gli SMS associato al servizio di comunicazione e `<to-phone-number>` con il numero di telefono a cui inviare un messaggio. 

> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, (ad esempio: + 14255550123).

## <a name="send-a-1n-sms-message"></a>Invia un messaggio SMS 1: N

Per inviare un messaggio SMS a un elenco di destinatari, chiamare il ```send``` metodo da **SmsClient** con un elenco di numeri di telefono del destinatario. È anche possibile passare parametri facoltativi per specificare se il report di recapito deve essere abilitato e impostare tag personalizzati. Aggiungere questo codice alla fine del blocco `try` in **send-sms.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

È necessario sostituire `<from-phone-number>` con un numero di telefono abilitato per SMS associato al servizio di comunicazione e `<to-phone-number-1>` `<to-phone-number-2>` con i numeri di telefono a cui si desidera inviare un messaggio. 

> [!WARNING]
> Si noti che i numeri di telefono devono essere specificati nel formato standard internazionale E.164, (ad esempio: + 14255550123).

## <a name="optional-parameters"></a>Parametri facoltativi

Il parametro `enable_delivery_report` è facoltativo ed è possibile usarlo per configurare i report di recapito. È utile per gli scenari in cui si vogliono generare eventi quando vengono recapitati messaggi SMS. Per configurare i report di recapito per i messaggi SMS, vedere la guida di avvio rapido [Gestire gli eventi SMS](../handle-sms-events.md).

Il `tag` parametro è un parametro facoltativo che è possibile utilizzare per applicare un tag al report di recapito.

## <a name="run-the-code"></a>Eseguire il codice
Eseguire l'applicazione dalla directory dell'applicazione con il comando `python`.

```console
python send-sms.py
```

Lo script Python completo dovrebbe avere un aspetto simile al seguente:

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
