---
title: includere file
description: includere file
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 3f49977ce5bf32f03dbb301e2d7a37449d7de4cc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630288"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7 o 3.6 +.
- Una stringa di connessione e una risorsa attiva di Servizi di comunicazione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

1. Aprire la finestra del terminale o di comando per creare una nuova directory per l'app e passare a tale directory.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Usare un editor di testo per creare un file denominato **issue-access-tokens.py** nella directory radice del progetto e aggiungere la struttura per il programma, inclusa la gestione delle eccezioni di base. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Installare il pacchetto

Quando si è ancora nella directory dell'applicazione, installare il pacchetto Azure Communication Services Identity SDK per Python usando il `pip install` comando.

```console
pip install azure-communication-identity
```

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di `CommunicationIdentityClient` con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Vedere come [gestire la stringa di connessione della risorsa](../create-communication-resource.md#store-your-connection-string).

Aggiungere questo codice all'interno del blocco `try`:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

In alternativa, se è stata impostata l'identità gestita, vedere [usare identità gestite](../managed-identity.md). è anche possibile eseguire l'autenticazione con l'identità gestita.
```python
endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"]
client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
```

## <a name="create-an-identity"></a>Creare un'identità

Servizi di comunicazione di Azure gestisce una directory di identità leggera. Usare il metodo `create_user` per creare una nuova voce nella directory con `Id` univoco. Archiviare l'identità ricevuta con il mapping agli utenti dell'applicazione. Ad esempio, archiviarla nel database del server applicazioni. L'identità sarà necessaria in seguito per emettere i token di accesso.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier)
```

## <a name="issue-access-tokens"></a>Risolvere i problemi dei token di accesso

Usare il metodo `get_token` per emettere un token di accesso per un'identità esistente di Servizi di comunicazione. Il parametro `scopes` definisce il set di primitive che autorizzeranno questo token di accesso. Vedere l'[elenco delle azioni supportate](../../concepts/authentication.md). È possibile creare una nuova istanza del parametro `CommunicationUserIdentifier` in base alla rappresentazione di stringa dell'identità di Servizi di comunicazione di Azure.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

I token di accesso sono credenziali di breve durata che devono essere riemesse. In caso contrario, si potrebbe verificare un'interruzione nell'esperienza degli utenti dell'applicazione. La proprietà della risposta `expires_on` indica la durata del token di accesso.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Creare un'identità ed emettere un token di accesso all'interno della stessa richiesta

Usare il `create_user_and_token` metodo per creare un'identità dei servizi di comunicazione ed emettere un token di accesso per tale identità. Il parametro `scopes` definisce il set di primitive che autorizzeranno questo token di accesso. Vedere l'[elenco delle azioni supportate](../../concepts/authentication.md).

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0].identifier
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nCreated an identity with ID: " + identity)
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>Aggiornare i token di accesso

Per aggiornare un token di accesso, usare l'oggetto `CommunicationUserIdentifier` per riemetterlo:

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Revocare i token di accesso

In alcuni casi, è possibile revocare esplicitamente i token di accesso, ad esempio quando l'utente di un'applicazione cambia la password che usa per l'autenticazione con il servizio. Il metodo `revoke_tokens` invalida tutti i token di accesso attivi che sono stati emessi all'identità.

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Eliminare un'identità

L'eliminazione di un'identità determina la revoca di tutti i token di accesso attivi e ne impedisce l'emissione per l'identità. Comporta inoltre la rimozione di tutto il contenuto persistente associato all'identità.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>Eseguire il codice

Da un prompt della console passare alla directory contenente il file *Issue-Access-Tokens.py* , quindi eseguire il comando seguente `python` per eseguire l'app.

```console
python ./issue-access-tokens.py
```
