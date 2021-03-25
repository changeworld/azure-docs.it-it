---
title: Server FHIR Postman in Azure - API di Azure per FHIR
description: In questa esercitazione verranno illustrati i passaggi necessari per usare il post per accedere a un server FHIR. Postman è utile per eseguire il debug delle applicazioni che accedono alle API.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 03/16/2021
ms.openlocfilehash: e9031dc77054a2bbac8015bbbdd7b9ed2a35e84f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043343"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Accedere all'API di Azure per FHIR con Postman

Un'applicazione client può accedere all'API di Azure per FHIR tramite un' [API REST](https://www.hl7.org/fhir/http.html). Per inviare richieste, visualizzare le risposte ed eseguire il debug dell'applicazione durante la compilazione, usare uno strumento di test delle API di propria scelta. In questa esercitazione verranno illustrate le procedure per accedere al server FHIR con il [post](https://www.getpostman.com/). 

## <a name="prerequisites"></a>Prerequisiti

- Un endpoint FHIR in Azure. 

   Per distribuire l'API di Azure per FHIR (un servizio gestito), è possibile usare la [portale di Azure](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md)o l'interfaccia della riga di comando di [Azure](fhir-paas-cli-quickstart.md).
- [Applicazione client riservata](register-confidential-azure-ad-client-app.md) registrata per accedere al servizio FHIR.
- Sono state concesse le autorizzazioni per l'applicazione client riservata, ad esempio "FHIR data Contributor", per accedere al servizio FHIR. Per altre informazioni, vedere [Configurare RBAC di Azure per FHIR](./configure-azure-rbac.md).
- Postman installato. 
    
    Per altre informazioni, vedere la pagina relativa all' [Introduzione a postazione](https://www.getpostman.com).

## <a name="fhir-server-and-authentication-details"></a>Dettagli sull'autenticazione e sul server FHIR

Per usare il post, sono necessari i parametri di autenticazione seguenti:

- L'URL del server FHIR, ad esempio `https://MYACCOUNT.azurehealthcareapis.com`

- Il provider di identità `Authority` per il server FHIR, ad esempio `https://login.microsoftonline.com/{TENANT-ID}`

- Oggetto configurato `audience` che corrisponde in genere all'URL del server FHIR, ad esempio `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` o `https://azurehealthcareapis.com` .

- ID dell'applicazione `client_id` [client riservata](register-confidential-azure-ad-client-app.md) usato per accedere al servizio FHIR.

- `client_secret`Segreto dell'applicazione o dell'applicazione client riservata.

Infine, è necessario verificare che `https://www.getpostman.com/oauth2/callback` sia un URL di risposta registrato per l'applicazione client.

## <a name="connect-to-fhir-server"></a>Connettersi al server FHIR

Aprire il post, quindi selezionare **Get** per effettuare una richiesta `https://fhir-server-url/metadata` .

![Dichiarazione di funzionalità dei metadati Postman](media/tutorial-postman/postman-metadata.png)

L'URL dei metadati per l'API di Azure per FHIR è `https://MYACCOUNT.azurehealthcareapis.com/metadata`. 

In questo esempio, l'URL del server FHIR è `https://fhirdocsmsft.azurewebsites.net` e l'istruzione capability del server è disponibile all'indirizzo `https://fhirdocsmsft.azurewebsites.net/metadata` . Questo endpoint è accessibile senza autenticazione.

Se si tenta di accedere a risorse limitate, si verifica una risposta "autenticazione non riuscita".

![Autenticazione non riuscita](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Come ottenere un token di accesso
Per ottenere un token di accesso valido, selezionare **autorizzazione** e selezionare **OAuth 2,0** dal menu a discesa **tipo** .

![Impostare OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Selezionare **Get New Access Token** (Ottieni nuovo token di accesso).

![Richiedere il token di accesso](media/tutorial-postman/postman-request-token.png)

Nella finestra di dialogo **Ottieni nuovo token di accesso** immettere i dettagli seguenti:

| Campo                 | Valore di esempio                                                                                                   | Comment                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Token Name (Nome del token)            | MYTOKEN                                                                                                         | Un nome a scelta          |
| Grant Type (Tipo di concessione)            | Codice di autorizzazione                                                                                              |                            |
| Callback URL (URL callback)          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Auth URL (URL autorizzazione)              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` è `https://MYACCOUNT.azurehealthcareapis.com` per l'API di Azure per FHIR |
| Access Token URL (URL token di accesso)      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| ID client             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | ID applicazione             |
| Client Secret         | `XXXXXXXX`                                                                                                        | Chiave client segreta          |
| Scope | `<Leave Blank>` |
| State                |  `1234`                                                                                                           |                            |
| Client Authentication (Autenticazione client) | Invia le credenziali del client nel corpo                                                                                 |                 

Selezionare il **token della richiesta** da guidare attraverso il flusso di autenticazione del Azure Active Directory e verrà restituito un token a postazione. Se si verifica un errore di autenticazione, fare riferimento alla console dei post per altri dettagli. **Nota**: sulla barra multifunzione selezionare **Visualizza, quindi selezionare** Mostra **console dei post**. Il tasto di scelta rapida per la console dei post è **ALT + CTRL + C**.

Scorrere verso il basso per visualizzare la schermata token restituito, quindi selezionare **USA token**.

![Usare il token](media/tutorial-postman/postman-use-token.png)

Per visualizzare il token appena popolato, fare riferimento al campo **token di accesso** . Se si seleziona **Invia** per ripetere la `Patient` ricerca di risorse,  `200 OK` viene restituito uno stato. Uno stato restituito `200 OK` indica una richiesta http riuscita.

![200 - OK](media/tutorial-postman/postman-200-OK.png)

Nell'esempio di *ricerca di pazienti* , nel database non sono presenti pazienti in modo tale che il risultato della ricerca sia vuoto.

È possibile esaminare il token di accesso usando uno strumento come [JWT.ms](https://jwt.ms). Di seguito è riportato un esempio del contenuto.

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Per la risoluzione dei problemi, la verifica che il destinatario sia corretto (attestazione `aud`) è un buon punto di partenza. Se il token viene emesso dall'autorità emittente corretta ( `iss` attestazione) e dispone dei destinatari corretti ( `aud` attestazione), ma non è ancora possibile accedere all'API FHIR, è probabile che l'utente o l'entità servizio ( `oid` attestazione) non abbia accesso al piano dati FHIR. È consigliabile usare il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](configure-azure-rbac.md) per assegnare i ruoli del piano dati agli utenti. Se si usa un tenant esterno di Azure Active Directory secondario per il piano dati, è necessario configurare il controllo degli accessi in base al ruolo locale per le assegnazioni [di FHIR](configure-local-rbac.md) .

È anche possibile ottenere un token per l' [API di Azure per FHIR usando l'interfaccia della](get-healthcare-apis-access-token-cli.md)riga di comando di Azure. Se si usa un token ottenuto con l'interfaccia della riga di comando di Azure, è necessario usare il *token di porta* del tipo di autorizzazione. Incollare il token direttamente in.

## <a name="inserting-a-patient"></a>Inserimento di un paziente

Con un token di accesso valido, è ora possibile inserire un nuovo paziente. In post, modificare il metodo selezionando **post**, quindi aggiungere il documento JSON seguente nel corpo della richiesta.

[!code-json[](../samples/sample-patient.json)]

Selezionare **Send (Invia** ) per determinare che il paziente è stato creato correttamente.

![Screenshot che indica che il paziente è stato creato correttamente.](media/tutorial-postman/postman-patient-created.png)

Se si ripete la ricerca del paziente, si dovrebbe ora visualizzare il record corrispondente:

![Paziente creato](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato eseguito l'accesso all'API di Azure per FHIR con il post. Per ulteriori informazioni sulle funzionalità dell'API di Azure per FHIR, vedere
 
>[!div class="nextstepaction"]
>[Funzionalità supportate](fhir-features-supported.md)
