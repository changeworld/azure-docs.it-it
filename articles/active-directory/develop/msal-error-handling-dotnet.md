---
title: Gestire errori ed eccezioni in MSAL.NET
titleSuffix: Microsoft identity platform
description: Informazioni su come gestire gli errori e le eccezioni, le richieste di attestazioni di accesso condizionale e i tentativi in MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584045"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Gestire errori ed eccezioni in MSAL.NET

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Gestione degli errori in MSAL.NET

Durante l'elaborazione delle eccezioni in .NET, è possibile usare il tipo di eccezione stesso e il membro `ErrorCode` per distinguere le eccezioni. I valori di `ErrorCode` sono costanti di tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

È anche possibile esaminare i campi di [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Se viene generata l'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception), vedere [Codici di errore di autenticazione e autorizzazione](reference-aadsts-error-codes.md) per verificare se il codice restituito è presente nell'elenco.

### <a name="common-net-exceptions"></a>Eccezioni .NET comuni

Di seguito sono riportate le eccezioni più comuni che possono essere generate e alcune possibili mitigazioni:  

| Eccezione | Codice di errore | Strategia di riduzione del rischio|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID '{appId}' denominata '{appName}'. Inviare una richiesta di autorizzazione interattiva per questo utente e questa risorsa.| Ottenere prima il consenso dell'utente. Se non si usa .NET Core (che non dispone di alcuna interfaccia utente Web), chiamare (una sola volta) `AcquireTokeninteractive`. Se si usa .NET Core o non si vuole eseguire `AcquireTokenInteractive`, l'utente può passare a un URL per fornire il consenso: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`. Per chiamare `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: l'utente deve usare l'[autenticazione a più fattori (MFA)](../authentication/concept-mfa-howitworks.md).| Non è disponibile alcuna mitigazione. Se l'autenticazione a più fattori è configurata per il tenant e Azure Active Directory (AAD) decide di applicarla, eseguire il fallback a un flusso interattivo, ad esempio `AcquireTokenInteractive` o `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: il tipo di concessione non è supportato tramite gli endpoint */common* o */consumers*. Usare */organizations* o l'endpoint specifico del tenant. È stato usato */common*.| Come spiegato nel messaggio di Azure AD, l'autorità deve disporre di un tenant oppure di */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: Il corpo della richiesta deve contenere il parametro seguente: `client_secret or client_assertion`.| Questa eccezione può essere generata se l'applicazione non è stata registrata come un'applicazione client pubblica in Azure AD. Nel portale di Azure modificare il manifesto per l'applicazione e impostare `allowPublicClient` su `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: non è stato possibile identificare l'utente connesso| La libreria non è stata in grado di eseguire query sull'utente che ha eseguito l'accesso a Windows corrente oppure l'utente non è un annuncio o Azure AD aggiunto (gli utenti aggiunti al posto di lavoro non sono supportati). Mitigazione 1: nella piattaforma UWP verificare che l'applicazione disponga delle funzionalità seguenti: Autenticazione aziendale, Reti private (client e server), Informazioni account utente. Mitigazione 2: implementare la propria logica per recuperare il nome utente (ad esempio, john@contoso.com) e usare il modulo `AcquireTokenByIntegratedWindowsAuth`, che accetta il nome utente.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Questo metodo si basa su un protocollo esposto da Active Directory (AD). Se un utente è stato creato in Azure AD senza il supporto di Active Directory (utente "gestito"), questo metodo avrà esito negativo. Gli utenti creati in Active Directory e supportati da Azure AD (utenti federati) possono trarre vantaggio da questo metodo di autenticazione non interattivo. Mitigazione: usare l'autenticazione interattiva.|

### `MsalUiRequiredException`

Uno dei codici di stato comuni restituiti da MSAL.NET quando si chiama `AcquireTokenSilent()` è `MsalError.InvalidGrantError`. Questo codice di stato indica che l'applicazione deve chiamare nuovamente la libreria di autenticazione, ma in modalità interattiva (AcquireTokenInteractive o AcquireTokenByDeviceCodeFlow per le applicazioni client pubbliche, si verificano problemi nelle app Web). Questo perché è necessaria un'interazione utente aggiuntiva prima di poter rilasciare il token di autenticazione.

Nella maggior parte dei casi, quando `AcquireTokenSilent` ha esito negativo, è perché la cache dei token non contiene token corrispondenti alla richiesta. I token di accesso scadono entro 1 ora e `AcquireTokenSilent` tenterà di recuperarne uno nuovo in base a un token di aggiornamento (in termini di OAuth2, si tratta del flusso 'Token di aggiornamento'). Questo flusso può anche avere esito negativo per diversi motivi, ad esempio se un amministratore del tenant configura criteri di accesso più rigorosi. 

L'interazione è finalizzata all'esecuzione di un'azione da parte dell'utente. Alcune di queste condizioni sono facili da risolvere per gli utenti, ad esempio accettare le condizioni per l'utilizzo con un solo clic, e alcune non possono essere risolte con la configurazione corrente, ad esempio quando computer in questione deve connettersi a una rete aziendale specifica. Alcune consentono all'utente di configurare l'autenticazione a più fattori o di installare Microsoft Authenticator nel dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>Enumerazione della classificazione `MsalUiRequiredException`

MSAL espone un `Classification` campo, che è possibile leggere per offrire un'esperienza utente migliore. Ad esempio, per indicare all'utente che la password è scaduta o che è necessario fornire il consenso per l'uso di alcune risorse. I valori supportati sono parte dell'enumerazione `UiRequiredExceptionClassification`:

| Classificazione    | Significato           | Gestione consigliata |
|-------------------|-------------------|----------------------|
| BasicAction | La condizione può essere risolta tramite l'interazione dell'utente durante il flusso di autenticazione interattiva. | Chiamare AcquireTokenInteractively(). |
| AdditionalAction | La condizione può essere risolta tramite un'interazione correttiva aggiuntiva con il sistema, all'esterno del flusso di autenticazione interattiva. | Chiamare AcquireTokenInteractively() per visualizzare un messaggio in cui viene illustrata l'azione correttiva. L'applicazione chiamante può scegliere di nascondere i flussi che richiedono additional_action se è improbabile che l'utente completi l'azione correttiva. |
| MessageOnly      | Non è possibile risolvere la condizione in questo momento. Se si avvia il flusso di autenticazione interattiva, viene visualizzato un messaggio che spiega la condizione. | Chiamare AcquireTokenInteractively() per visualizzare un messaggio in cui viene illustrata la condizione. AcquireTokenInteractively() restituirà l'errore UserCanceled dopo che l'utente legge il messaggio e chiude la finestra. L'applicazione chiamante può scegliere di nascondere i flussi che generano message_only se l'utente non può trarre vantaggio dal messaggio.|
| ConsentRequired  | Il consenso dell'utente è mancante o è stato revocato. | Chiamare AcquireTokenInteractively() per consentire all'utente di fornire il consenso. |
| UserPasswordExpired | La password dell'utente è scaduta. | Chiamare AcquireTokenInteractively() in modo che l'utente possa reimpostare la password. |
| PromptNeverFailed| È stata chiamata l'autenticazione interattiva con il parametro prompt=never, forzando MSAL a basarsi sui cookie del browser e non visualizzare il browser. L'operazione non è riuscita. | Chiamare AcquireTokenInteractively() senza Prompt.None |
| AcquireTokenSilentFailed | MSAL SDK non dispone di informazioni sufficienti per recuperare un token dalla cache. È possibile che non siano presenti token nella cache o che non sia stato trovato alcun account. Il messaggio di errore contiene informazioni dettagliate.  | Chiamare AcquireTokenInteractively(). |
| nessuno    | Non sono fornite ulteriori informazioni dettagliate. La condizione può essere risolta tramite l'interazione dell'utente durante il flusso di autenticazione interattiva. | Chiamare AcquireTokenInteractively(). |

## <a name="net-code-example"></a>Esempio di codice .NET

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Quando si chiama un'API che richiede l'Accesso Condizionale da MSAL.NET, l'applicazione dovrà gestire le eccezioni relative alla richiesta di attestazione. Verrà generata un'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception), in cui la proprietà [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) non sarà vuota.

Per gestire la richiesta di verifica dell'attestazione, sarà necessario usare il metodo `.WithClaim()` della classe `PublicClientApplicationBuilder`.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>Codici di errore HTTP 500 e 600

MSAL.NET implementa un semplice meccanismo per l'esecuzione di un nuovo tentativo per gli errori con codici di errore HTTP 500 e 600.

L'eccezione [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) presenta `System.Net.Http.Headers.HttpResponseHeaders` come una proprietà `namedHeaders`. È quindi possibile sfruttare le informazioni aggiuntive nel codice errore per migliorare l'affidabilità delle applicazioni. Nel caso descritto è possibile usare `RetryAfterproperty` (di tipo `RetryConditionHeaderValue`) e calcolare quando ripetere il tentativo.

Di seguito è riportato un esempio di applicazione daemon con il flusso di credenziali client. È possibile adattarlo a qualsiasi metodo per l'acquisizione di un token.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Passaggi successivi

Provare ad abilitare la [registrazione in MSAL.NET](msal-logging-dotnet.md) per semplificare la diagnosi e il debug dei problemi.
