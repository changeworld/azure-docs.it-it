---
title: Aggiungere connettori API ai flussi utente (anteprima)
description: Configurare un connettore API da usare in un flusso utente.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 03/24/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 86e9b13ce56e1924b0e24a7f4971da18620617de
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043632"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow-preview"></a>Aggiungere un connettore API a un flusso utente di iscrizione (anteprima)

> [!IMPORTANT]
> Connettori API per l'iscrizione è una funzionalità di anteprima pubblica di Azure AD B2C. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per usare un [connettore API](api-connectors-overview.md), è necessario innanzitutto creare il connettore API e quindi abilitarlo in un flusso utente.

## <a name="create-an-api-connector"></a>Creare un connettore API

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. In **servizi di Azure** selezionare **Azure ad B2C**.
4. Selezionare **connettori API (anteprima)** e quindi fare clic su **nuovo connettore API**.

   ![Aggiungere un nuovo connettore API](./media/add-api-connector/api-connector-new.png)

5. Consente di specificare un nome visualizzato per la chiamata. Ad esempio, **convalidare le informazioni utente**.
6. Specificare l' **URL dell'endpoint** per la chiamata API.
7. Scegliere il **tipo di autenticazione** e configurare le informazioni di autenticazione per chiamare l'API. Vedere la sezione seguente per le opzioni di sicurezza dell'API.

    ![Configurare un connettore API](./media/add-api-connector/api-connector-config.png)

8. Selezionare **Salva**.

## <a name="securing-the-api-endpoint"></a>Protezione dell'endpoint API
È possibile proteggere l'endpoint API usando l'autenticazione di base HTTP o l'autenticazione del certificato client HTTPS (anteprima). In entrambi i casi, si forniscono le credenziali che Azure AD B2C utilizzerà quando si chiama l'endpoint API. L'endpoint API controlla quindi le credenziali ed esegue le decisioni di autorizzazione.

### <a name="http-basic-authentication"></a>Autenticazione HTTP di base
L'autenticazione HTTP di base è definita in [RFC 2617](https://tools.ietf.org/html/rfc2617). Azure AD B2C invia una richiesta HTTP con le credenziali client ( `username` e `password` ) nell' `Authorization` intestazione. Le credenziali vengono formattate come stringa con codifica Base64 `username:password` . L'API controlla quindi questi valori per determinare se rifiutare o meno una chiamata API.

### <a name="https-client-certificate-authentication-preview"></a>Autenticazione del certificato client HTTPS (anteprima)

> [!IMPORTANT]
> Questa funzionalità è disponibile in anteprima e viene fornita senza un contratto di servizio. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'autenticazione del certificato client è un metodo di autenticazione reciproca basato sui certificati, in cui il client fornisce al server un certificato client per dimostrare la propria identità. In questo caso, Azure AD B2C utilizzerà il certificato caricato come parte della configurazione del connettore API. Questo avviene nell'ambito dell'handshake SSL. Il servizio API può quindi limitare l'accesso ai soli servizi con certificati appropriati. Il certificato client è un certificato digitale X. 509 PKCS12 (PFX). Negli ambienti di produzione deve essere firmato da un'autorità di certificazione. 

Per creare un certificato, è possibile usare [Azure Key Vault](../key-vault/certificates/create-certificate.md), che include opzioni per i certificati autofirmati e le integrazioni con i provider di autorità di certificazione per i certificati firmati. Le impostazioni consigliate includono:
- **Oggetto**: `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **Tipo di contenuto**: `PKCS #12`
- **Tipo di durata Acton**: `Email all contacts at a given percentage lifetime` o `Email all contacts a given number of days before expiry`
- **Tipo di chiave**: `RSA`
- **Dimensioni chiave**: `2048`
- **Chiave privata esportabile**: (per poter `Yes` esportare il file PFX)

È quindi possibile [esportare il certificato](../key-vault/certificates/how-to-export-certificate.md). In alternativa, è possibile usare il [cmdlet New-SelfSignedCertificate](../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional) di PowerShell per generare un certificato autofirmato.

Quando si dispone di un certificato, è possibile caricarlo come parte della configurazione del connettore API. Si noti che la password è obbligatoria solo per i file di certificato protetti da una password.

L'API deve implementare l'autorizzazione basata sui certificati client inviati per proteggere gli endpoint dell'API. Per app Azure servizio e funzioni di Azure, vedere [configurare l'autenticazione reciproca TLS](../app-service/app-service-web-configure-tls-mutual-auth.md) per informazioni su come abilitare e *convalidare il certificato dal codice API*.  È anche possibile usare gestione API di Azure per [controllare le proprietà del certificato client](
../api-management/api-management-howto-mutual-certificates-for-clients.md)  rispetto ai valori desiderati usando le espressioni di criteri.

È consigliabile impostare gli avvisi di promemoria per la scadenza del certificato. Sarà necessario generare un nuovo certificato e ripetere i passaggi precedenti. Il servizio API può continuare temporaneamente ad accettare certificati obsoleti e nuovi durante la distribuzione del nuovo certificato. Per caricare un nuovo certificato in un connettore API esistente, selezionare il connettore API in **connettori API** e fare clic su **Carica nuovo certificato**. Il certificato caricato più di recente che non è scaduto ed è oltre la data di inizio verrà usato automaticamente da Azure Active Directory.

### <a name="api-key"></a>Chiave API
Alcuni servizi usano un meccanismo di "chiave API" per offuscare l'accesso agli endpoint HTTP durante lo sviluppo. Per [funzioni di Azure](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys), è possibile eseguire questa operazione includendo `code` come parametro di query nell' **URL dell'endpoint**. Ad esempio, `https://contoso.azurewebsites.net/api/endpoint` <b>`?code=0123456789`</b> ). 

Non si tratta di un meccanismo da usare da solo nell'ambiente di produzione. Pertanto, la configurazione per l'autenticazione di base o di certificato è sempre obbligatoria. Se non si vuole implementare alcun metodo di autenticazione (scelta non consigliata) a scopo di sviluppo, è possibile scegliere l'autenticazione di base e usare i valori temporanei per `username` e `password` che l'API può ignorare mentre si implementa l'autorizzazione nell'API.

## <a name="the-request-sent-to-your-api"></a>La richiesta inviata all'API
Un connettore API si materializza come una richiesta **http post** , inviando attributi utente ("claims") come coppie chiave-valore in un corpo JSON. Gli attributi vengono serializzati in modo analogo alle proprietà [Microsoft Graph](/graph/api/resources/user#properties) utente. 

**Richiesta di esempio**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Nella richiesta sono disponibili solo le proprietà utente e gli attributi personalizzati elencati nel **Azure ad B2C**  >  esperienza **degli attributi utente** .

Gli attributi personalizzati sono disponibili nel formato **extension_ \<extensions-app-id> _CustomAttribute**  nella directory. L'API deve essere in attesa di ricevere attestazioni in questo stesso formato serializzato. Per altre informazioni sugli attributi personalizzati, vedere [definire attributi personalizzati in Azure ad B2C](user-flow-custom-attributes.md).

Inoltre, l'attestazione delle **impostazioni locali dell'interfaccia utente (' ui_locales ')** viene inviata per impostazione predefinita in tutte le richieste. Fornisce le impostazioni locali dell'utente come configurate sul dispositivo che possono essere usate dall'API per restituire le risposte internazionalizzate.

> [!IMPORTANT]
> Se un'attestazione non dispone di un valore nel momento in cui viene chiamato l'endpoint dell'API, l'attestazione non verrà inviata all'API. L'API deve essere progettata per controllare in modo esplicito e gestire il caso in cui un'attestazione non è presente nella richiesta.

> [!TIP] 
> le [**identità (' identità')**](/graph/api/resources/objectidentity) e l' **indirizzo di posta elettronica (' email ')** attestazioni possono essere usate dall'API per identificare un utente prima che dispongano di un account nel tenant. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>Abilitare il connettore API in un flusso utente

Seguire questi passaggi per aggiungere un connettore API a un flusso utente di iscrizione.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. In **servizi di Azure** selezionare **Azure ad B2C**.
4. Selezionare **flussi utente** e quindi selezionare il flusso utente a cui si vuole aggiungere il connettore API.
5. Selezionare **connettori API** e quindi selezionare gli endpoint API da richiamare nei passaggi seguenti del flusso utente:

   - **Dopo aver eseguito l'accesso con un provider di identità**
   - **Prima della creazione dell'utente**

   ![Aggiungere API al flusso utente](./media/add-api-connector/api-connectors-user-flow-select.png)

6. Selezionare **Salva**.

## <a name="after-signing-in-with-an-identity-provider"></a>Dopo aver eseguito l'accesso con un provider di identità

Un connettore API in questo passaggio del processo di iscrizione viene richiamato immediatamente dopo l'autenticazione dell'utente con un provider di identità (ad esempio Google, Facebook, & Azure AD). Questo passaggio precede la ***pagina raccolta attributi***, che è il form presentato all'utente per raccogliere gli attributi utente. Questo passaggio non viene richiamato se un utente sta effettuando la registrazione con un account locale.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Richiesta di esempio inviata all'API in questo passaggio
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Le attestazioni esatte inviate all'API dipendono dalle informazioni fornite dal provider di identità. ' email ' viene sempre inviato.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipi di risposta previsti dall'API Web in questo passaggio

Quando l'API Web riceve una richiesta HTTP da Azure AD durante un flusso utente, può restituire le risposte seguenti:

- Risposta continuazione
- Risposta di blocco

#### <a name="continuation-response"></a>Risposta continuazione

Una risposta di continuazione indica che il flusso utente deve continuare con il passaggio successivo: la pagina della raccolta di attributi.

In una risposta di continuazione, l'API può restituire attestazioni. Se un'attestazione viene restituita dall'API, l'attestazione esegue le operazioni seguenti:

- Inserisce il campo di input nella pagina della raccolta di attributi.

Vedere un esempio di [risposta di continuazione](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Risposta di blocco

Una risposta di blocco esce dal flusso utente. Può essere rilasciata intenzionalmente dall'API per arrestare la continuazione del flusso utente visualizzando una pagina di blocco per l'utente. Nella pagina blocco viene visualizzato l'oggetto `userMessage` fornito dall'API.

Vedere un esempio di [risposta di blocco](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Prima della creazione dell'utente

Un connettore API in questo passaggio del processo di iscrizione viene richiamato dopo la pagina della raccolta di attributi, se disponibile. Questo passaggio viene sempre richiamato prima della creazione di un account utente.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Richiesta di esempio inviata all'API in questo passaggio

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
Le attestazioni esatte inviate all'API dipendono dalle informazioni raccolte dall'utente o fornite dal provider di identità.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipi di risposta previsti dall'API Web in questo passaggio

Quando l'API Web riceve una richiesta HTTP da Azure AD durante un flusso utente, può restituire le risposte seguenti:

- Risposta continuazione
- Risposta di blocco
- Risposta di convalida

#### <a name="continuation-response"></a>Risposta continuazione

Una risposta di continuazione indica che il flusso utente deve continuare con il passaggio successivo: creare l'utente nella directory.

In una risposta di continuazione, l'API può restituire attestazioni. Se un'attestazione viene restituita dall'API, l'attestazione esegue le operazioni seguenti:

- Esegue l'override di qualsiasi valore che è già stato assegnato all'attestazione dalla pagina della raccolta di attributi.

Vedere un esempio di [risposta di continuazione](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Risposta di blocco
Una risposta di blocco esce dal flusso utente. Può essere rilasciata intenzionalmente dall'API per arrestare la continuazione del flusso utente visualizzando una pagina di blocco per l'utente. Nella pagina blocco viene visualizzato l'oggetto `userMessage` fornito dall'API.

Vedere un esempio di [risposta di blocco](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Convalida-risposta di errore
 Quando l'API risponde con una risposta di errore di convalida, il flusso utente rimane nella pagina della raccolta degli attributi e un oggetto `userMessage` viene visualizzato all'utente. L'utente può quindi modificare e inviare nuovamente il modulo. Questo tipo di risposta può essere usato per la convalida dell'input.

Vedere un esempio di [risposta di errore di convalida](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Risposte di esempio

### <a name="example-of-a-continuation-response"></a>Esempio di risposta di continuazione

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parametro                                          | Tipo              | Obbligatoria | Descrizione                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| azione                                             | string            | Sì      | Il valore deve essere `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | I valori restituiti possono sovrascrivere i valori raccolti da un utente. Possono anche essere restituiti nel token se selezionato come **attestazione dell'applicazione**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | L'attestazione non deve contenere `_<extensions-app-id>_` . I valori restituiti possono sovrascrivere i valori raccolti da un utente. Possono anche essere restituiti nel token se selezionato come **attestazione dell'applicazione**.  |

### <a name="example-of-a-blocking-response"></a>Esempio di una risposta di blocco

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parametro   | Tipo   | Obbligatoria | Descrizione                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | string | Sì      | Versione dell'API.                                                    |
| azione      | string | Sì      | Il valore deve essere `ShowBlockPage`                                              |
| userMessage | string | Sì      | Messaggio da visualizzare all'utente.                                            |

**Esperienza dell'utente finale con una risposta di blocco**

![Pagina blocco di esempio](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Esempio di una risposta di errore di convalida

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Parametro   | Tipo    | Obbligatoria | Descrizione                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | string  | Sì      | Versione dell'API.                                                    |
| azione      | string  | Sì      | Il valore deve essere `ValidationError`.                                           |
| status      | Integer/stringa | Sì      | Deve essere `400` un valore o `"400"` una risposta ValidationError.  |
| userMessage | string  | Sì      | Messaggio da visualizzare all'utente.                                            |

> [!NOTE]
> Il codice di stato HTTP deve essere "400" oltre al valore "status" nel corpo della risposta.

**Esperienza dell'utente finale con una risposta di errore di convalida**

![Pagina di convalida di esempio](./media/add-api-connector/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Procedure consigliate e risoluzione dei problemi

### <a name="using-serverless-cloud-functions"></a>Uso di funzioni cloud senza server
Le funzioni senza server, come i trigger HTTP in funzioni di Azure, offrono un modo semplice per creare endpoint API da usare con il connettore API. È possibile usare la funzione cloud senza server per eseguire, [ad esempio](code-samples.md#api-connectors), la logica di convalida e limitare le iscrizioni a specifici domini di posta elettronica. La funzione cloud senza server può anche chiamare e richiamare altre API Web, archivi utente e altri servizi cloud per scenari più complessi.

### <a name="best-practices"></a>Procedure consigliate
Assicurarsi che:
* L'API segue i contratti di richiesta e risposta dell'API, come descritto in precedenza. 
* L' **URL dell'endpoint** del connettore API punta all'endpoint API corretto.
* L'API verifica in modo esplicito la presenza di valori Null delle attestazioni ricevute.
* L'API risponde nel minor tempo possibile per garantire un'esperienza utente fluida.
    * Se si utilizza una funzione senza server o un servizio Web scalabile, utilizzare un piano di hosting che contenga l'API "attiva" o "caldo". nell'ambiente di produzione. Per funzioni di Azure, è consigliabile usare il [piano Premium](../azure-functions/functions-scale.md)
 

### <a name="use-logging"></a>USA registrazione
In generale, è utile usare gli strumenti di registrazione abilitati dal servizio API Web, ad esempio [Application Insights](../azure-functions/functions-monitoring.md), per monitorare l'API per i codici di errore imprevisti, le eccezioni e le prestazioni insufficienti.
* Monitorare i codici di stato HTTP che non sono HTTP 200 o 400.
* Un codice di stato HTTP 401 o 403 indica in genere la presenza di un problema con l'autenticazione. Controllare il livello di autenticazione dell'API e la configurazione corrispondente nel connettore API.
* Se necessario, usare livelli di registrazione più aggressivi (ad esempio "Trace" o "debug") in fase di sviluppo.
* Monitora l'API per tempi di risposta lunghi.

## <a name="next-steps"></a>Passaggi successivi
- Inizia a usare gli [esempi](code-samples.md#api-connectors).
