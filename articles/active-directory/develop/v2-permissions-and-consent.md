---
title: Ambiti della piattaforma di identità Microsoft, autorizzazioni, consenso &
description: Informazioni sull'autorizzazione nell'endpoint della piattaforma di identità Microsoft, inclusi gli ambiti, le autorizzazioni e il consenso.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 35499810ae13a8ddc5b7bb6306deafef0ef24e0f
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246792"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Autorizzazioni e consenso nell'endpoint di Microsoft Identity Platform

Le applicazioni che si integrano con Microsoft Identity Platform seguono un modello di autorizzazione che offre a utenti e amministratori il controllo sulle modalità di accesso ai dati. L'implementazione del modello di autorizzazione è stata aggiornata nell'endpoint della piattaforma di identità Microsoft. Viene modificato il modo in cui un'app deve interagire con la piattaforma di identità Microsoft. Questo articolo illustra i concetti di base di questo modello di autorizzazione, inclusi gli ambiti, le autorizzazioni e il consenso.

## <a name="scopes-and-permissions"></a>Ambiti e autorizzazioni

Microsoft Identity Platform implementa il protocollo di autorizzazione [OAuth 2.0](active-directory-v2-protocols.md). OAuth 2.0 è un metodo tramite cui un'applicazione di terze parti può accedere alle risorse ospitate sul Web per conto dell'utente. Qualsiasi risorsa ospitata sul Web che si integra con la piattaforma di identità Microsoft dispone di un identificatore di risorsa o di un *URI ID applicazione*. 

Di seguito sono riportati alcuni esempi di risorse ospitate sul Web Microsoft:

* Microsoft Graph: `https://graph.microsoft.com`
* API di posta elettronica Microsoft 365: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Lo stesso vale per le risorse di terze parti integrate con Microsoft Identity Platform. Tali risorse possono anche definire un set di autorizzazioni che può essere usato per suddividere le funzionalità di tale risorsa in blocchi più piccoli. Ad esempio, [Microsoft Graph](https://graph.microsoft.com) ha definito le autorizzazioni per eseguire le attività seguenti, tra le altre:

* Lettura del calendario dell'utente
* Scrittura nel calendario dell'utente
* Invio di messaggi di posta elettronica come utente

A causa di questi tipi di definizioni delle autorizzazioni, la risorsa ha un controllo granulare sui dati e su come viene esposta la funzionalità dell'API. Un'app di terze parti può richiedere queste autorizzazioni da utenti e amministratori, che devono approvare la richiesta prima che l'app possa accedere ai dati o agire per conto di un utente. 

Quando la funzionalità di una risorsa viene suddivisa in blocchi in piccoli set di autorizzazioni, è possibile compilare app di terze parti per richiedere solo le autorizzazioni necessarie per eseguire la funzione. Gli utenti e gli amministratori possono conoscere i dati a cui l'app può accedere. E possono essere più sicuri che l'app non si comportano con finalità dannose. Gli sviluppatori devono sempre rispettare il principio dei privilegi minimi, chiedendo solo le autorizzazioni necessarie per il funzionamento delle applicazioni.

In OAuth 2,0 questi tipi di set di autorizzazioni sono denominati *ambiti*. Sono inoltre spesso denominate *autorizzazioni*. Nella piattaforma di identità Microsoft, un'autorizzazione viene rappresentata come valore stringa. Per il Microsoft Graph esempio, di seguito è riportato il valore stringa per ogni autorizzazione:

* Lettura del calendario dell'utente tramite `Calendars.Read`
* Scrittura del calendario dell'utente tramite `Calendars.ReadWrite`
* Invio di messaggi di posta elettronica come utente tramite `Mail.Send`

Un'app richiede in genere queste autorizzazioni specificando gli ambiti nelle richieste all'endpoint di autorizzazione della piattaforma di identità Microsoft. Tuttavia, alcune autorizzazioni con privilegi elevati possono essere concesse solo tramite il consenso dell'amministratore. Possono essere richiesti o concessi tramite l' [endpoint di consenso dell'amministratore](#admin-restricted-permissions). Continua a leggere per scoprire di più.

## <a name="permission-types"></a>Tipi di autorizzazioni

Microsoft Identity Platform supporta due tipi di autorizzazioni: *autorizzazioni delegate* e *autorizzazioni dell'applicazione*.

* Le **autorizzazioni delegate** sono usate dalle app con un utente connesso. Per queste app, l'utente o un amministratore acconsente alle autorizzazioni richieste dall'app. L'app è autorizzata a fungere da utente connesso quando effettua chiamate alla risorsa di destinazione. 

    Alcune autorizzazioni delegate possono essere consentite da utenti non amministratori. Tuttavia, alcune autorizzazioni con privilegi elevati richiedono il [consenso dell'amministratore](#admin-restricted-permissions). Per informazioni sui ruoli di amministratore che possono concedere il consenso alle autorizzazioni delegate, vedere autorizzazioni per i ruoli di [amministratore in Azure Active Directory (Azure ad)](../roles/permissions-reference.md).

* Le **autorizzazioni dell'applicazione** vengono usate dalle app eseguite senza un utente connesso, ad esempio le app che vengono eseguite come servizi o daemon in background. Solo [un amministratore può concedere il consenso alle](#requesting-consent-for-an-entire-tenant) autorizzazioni dell'applicazione.

Le _autorizzazioni valide_ sono le autorizzazioni dell'app quando effettua richieste alla risorsa di destinazione. È importante comprendere la differenza tra le autorizzazioni delegate e le autorizzazioni dell'applicazione concesse all'app e le autorizzazioni valide che l'app viene concessa quando effettua chiamate alla risorsa di destinazione.

- Per le autorizzazioni delegate, le _autorizzazioni effettive_ dell'app sono l'intersezione con privilegi minimi delle autorizzazioni delegate a cui l'app è stata concessa (dal consenso) e i privilegi dell'utente attualmente connesso. L'app non può mai avere più privilegi rispetto all'utente connesso. 

   All'interno delle organizzazioni, i privilegi dell'utente che ha eseguito l'accesso possono essere determinati in base ai criteri o all'appartenenza a uno o più ruoli di amministratore. Per informazioni su quali ruoli di amministratore possono fornire il consenso per le autorizzazioni delegate, vedere [Autorizzazioni del ruolo di amministratore in Azure AD](../roles/permissions-reference.md).

   Si supponga, ad esempio, che all'app sia stata concessa l'autorizzazione delegata _User.ReadWrite.All_. Questa autorizzazione concede nominalmente all'app l'autorizzazione per leggere e aggiornare il profilo di ogni utente in un'organizzazione. Se l'utente connesso è un amministratore globale, l'app può aggiornare il profilo di ogni utente nell'organizzazione. Tuttavia, se l'utente che ha eseguito l'accesso non dispone di un ruolo di amministratore, l'app può aggiornare solo il profilo dell'utente che ha eseguito l'accesso. Non è possibile aggiornare i profili di altri utenti dell'organizzazione perché l'utente che dispone delle autorizzazioni per agire per conto di non dispone di tali privilegi.

- Per le autorizzazioni dell'applicazione, le _autorizzazioni effettive_ dell'app sono il livello completo dei privilegi implicati dall'autorizzazione. Un'app che ha l'autorizzazione dell'applicazione _User.ReadWrite.All_ può ad esempio aggiornare il profilo di tutti gli utenti dell'organizzazione.

## <a name="openid-connect-scopes"></a>Ambiti di OpenID Connect

L'implementazione della piattaforma di identità Microsoft di OpenID Connect presenta alcuni ambiti ben definiti ospitati anche in Microsoft Graph: `openid` , `email` , `profile` e `offline_access` . Gli `address` `phone` ambiti e OpenID Connect non sono supportati.

Se si richiedono gli ambiti e un token di OpenID Connect, si otterrà un token per chiamare l' [endpoint UserInfo](userinfo.md).

### <a name="openid"></a>openid

Se un'app esegue l'accesso usando [OpenID Connect](active-directory-v2-protocols.md), deve richiedere l' `openid` ambito. L' `openid` ambito viene visualizzato nella pagina di consenso dell'account aziendale come autorizzazione per l' **accesso** . Nella pagina di consenso account Microsoft personale viene visualizzato come visualizzare il **profilo e connettersi ad app e servizi usando l'autorizzazione del account Microsoft** . 

Utilizzando questa autorizzazione, un'applicazione può ricevere un identificatore univoco per l'utente sotto forma di `sub` attestazione. L'autorizzazione concede anche all'app l'accesso all'endpoint UserInfo. L' `openid` ambito può essere usato nell'endpoint token della piattaforma di identità Microsoft per acquisire i token ID. L'app può usare questi token per l'autenticazione.

### <a name="email"></a>email

L' `email` ambito può essere utilizzato con l' `openid` ambito e qualsiasi altro ambito. Consente all'applicazione di accedere all'indirizzo di posta elettronica primario dell'utente sotto forma di attestazione `email`. 

L' `email` attestazione è inclusa in un token solo se l'indirizzo di posta elettronica è associato all'account utente, che non è sempre il caso. Se l'app usa l' `email` ambito, l'app deve essere in grado di gestire un caso in cui non `email` esista alcuna attestazione nel token.

### <a name="profile"></a>profile

L' `profile` ambito può essere utilizzato con l' `openid` ambito e qualsiasi altro ambito. Consente all'app di accedere a una grande quantità di informazioni sull'utente. Le informazioni a cui può accedere includono, a titolo esemplificativo, il nome, il cognome, il nome utente preferito e l'ID oggetto specificati dall'utente. 

Per un elenco completo delle `profile` attestazioni disponibili nel `id_tokens` parametro per un utente specifico, vedere il [ `id_tokens` riferimento](id-tokens.md).

### <a name="offline_access"></a>offline_access

L' [ `offline_access` ambito](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) consente all'app di accedere alle risorse per conto dell'utente per un periodo di tempo prolungato. Nella pagina di consenso, questo ambito viene visualizzato come **Mantieni l'accesso ai dati a cui è stato concesso l'accesso all'** autorizzazione. 

Quando un utente approva l' `offline_access` ambito, l'app può ricevere i token di aggiornamento dall'endpoint del token della piattaforma di identità Microsoft. I token di aggiornamento sono di lunga durata. L'applicazione può ottenere nuovi token di accesso quando i vecchi scadono.

> [!NOTE]
> Questa autorizzazione è attualmente disponibile in tutte le pagine di consenso, anche per i flussi che non forniscono un token di aggiornamento (ad esempio, il [flusso implicito](v2-oauth2-implicit-grant-flow.md)). Questa installazione risolve scenari in cui un client può iniziare all'interno del flusso implicito e quindi passare al flusso di codice in cui è previsto un token di aggiornamento.

Sulla piattaforma di identità Microsoft (richieste effettuate all'endpoint v 2.0), l'app deve richiedere esplicitamente l' `offline_access` ambito per ricevere i token di aggiornamento. Quindi, quando si riscatta un codice di autorizzazione nel [flusso del codice di autorizzazione OAuth 2,0](active-directory-v2-protocols.md), si riceverà solo un token di accesso dall' `/token` endpoint. 

Il token di accesso è valido per un breve periodo. In genere scade tra un'ora. A questo punto, l'app reindirizza l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento, a seconda del tipo di app, l'utente potrebbe dover immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni.

Per ulteriori informazioni su come ottenere e usare i token di aggiornamento, vedere il [riferimento al protocollo Microsoft Identity Platform](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Richiesta di consenso per un singolo utente

In una richiesta di autorizzazione [OpenID Connect o OAuth 2.0](active-directory-v2-protocols.md) un'app può richiedere le autorizzazioni necessarie usando il parametro di query `scope`. Ad esempio, quando un utente accede a un'app, l'app invia una richiesta come l'esempio seguente. (Le interruzioni di riga vengono aggiunte per la leggibilità).

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Il parametro `scope` è un elenco di autorizzazioni delegate separate da spazi richieste dall'app. Ogni autorizzazione è indicata aggiungendo il valore di autorizzazione all'identificatore della risorsa (URI ID applicazione). Nella richiesta di esempio precedente, l'app richiede l'autorizzazione per la lettura del calendario dell'utente e l'invio di messaggi di posta elettronica come utente.

Dopo che l'utente immette le proprie credenziali, l'endpoint della piattaforma di identità Microsoft verifica la presenza di un record corrispondente di *consenso dell'utente*. Se l'utente non ha acconsentito a nessuna delle autorizzazioni richieste in passato e se l'amministratore non ha acconsentito a queste autorizzazioni per conto dell'intera organizzazione, l'endpoint della piattaforma Microsoft Identity chiede all'utente di concedere le autorizzazioni richieste.

Al momento, l'autorizzazione `offline_access` ("Mantieni l'accesso ai dati a cui è stato concesso l'accesso a") e `user.read` ("Accedi e leggi il profilo") viene automaticamente inclusa nel consenso iniziale per un'applicazione.  Queste autorizzazioni sono in genere necessarie per le funzionalità di app appropriate. L' `offline_access` autorizzazione consente all'app di accedere ai token di aggiornamento cruciali per le app native e le app Web. L' `user.read` autorizzazione consente di accedere all' `sub` attestazione. Consente al client o all'app di identificare correttamente l'utente nel tempo e accedere a informazioni rudimentali sugli utenti.

![Schermata di esempio che mostra il consenso dell'account di lavoro.](./media/v2-permissions-and-consent/work_account_consent.png)

Quando l'utente approva la richiesta di autorizzazione, il consenso viene registrato. Non è necessario che l'utente acconsente di nuovo al momento dell'accesso all'applicazione.

## <a name="requesting-consent-for-an-entire-tenant"></a>Richiesta di consenso per un intero tenant

Quando un'organizzazione acquista una licenza o una sottoscrizione per un'applicazione, l'organizzazione spesso vuole impostare in modo proattivo l'applicazione per l'uso da parte di tutti i membri dell'organizzazione. Come parte di questo processo, un amministratore può concedere il consenso all'applicazione ad agire per conto di qualsiasi utente nel tenant. Se l'amministratore concede il consenso per l'intero tenant, gli utenti dell'organizzazione non visualizzeranno una pagina di consenso per l'applicazione.

Per richiedere il consenso per le autorizzazioni delegate per tutti gli utenti in un tenant, l'app può usare l'endpoint di consenso amministratore.

Inoltre, le applicazioni devono usare l'endpoint di consenso dell'amministratore per richiedere le autorizzazioni dell'applicazione.

## <a name="admin-restricted-permissions"></a>Autorizzazioni riservate all'amministratore

Alcune autorizzazioni con privilegi elevati nelle risorse Microsoft possono essere impostate su *amministratore con restrizioni*. Di seguito sono riportati alcuni esempi di questi tipi di autorizzazioni:

* Lettura dei profili completi degli utenti tramite `User.Read.All`
* Scrittura di dati in una directory aziendale tramite `Directory.ReadWrite.All`
* Lettura di tutti i gruppi nella directory aziendale tramite `Groups.Read.All`

Sebbene un utente consumer possa concedere a un'applicazione l'accesso a questo tipo di dati, gli utenti dell'organizzazione non possono concedere l'accesso allo stesso set di dati sensibili della società. Se l'applicazione richiede l'accesso a una di queste autorizzazioni da un utente dell'organizzazione, l'utente riceve un messaggio di errore che indica che non è autorizzato a fornire il consenso alle autorizzazioni dell'applicazione.

Se l'app richiede ambiti per le autorizzazioni con restrizioni di amministratore, l'amministratore di un'organizzazione deve concedere il consenso a tali ambiti per conto degli utenti dell'organizzazione. Per evitare di visualizzare i prompt degli utenti che richiedono il consenso per le autorizzazioni che non possono concedere, l'app può usare l'endpoint di consenso dell'amministratore. L'endpoint di consenso dell'amministratore è trattato nella sezione successiva.

Se l'applicazione richiede autorizzazioni delegate con privilegi elevati e un amministratore concede tali autorizzazioni tramite l'endpoint di consenso dell'amministratore, il consenso viene concesso a tutti gli utenti nel tenant.

Se l'applicazione richiede le autorizzazioni dell'applicazione e un amministratore concede tali autorizzazioni tramite l'endpoint di consenso dell'amministratore, la concessione non viene eseguita per conto di un utente specifico. Al contrario, all'applicazione client le autorizzazioni verranno concesse *direttamente*. Questi tipi di autorizzazioni vengono usati solo dai servizi daemon e da altre applicazioni non interattive eseguite in background.

## <a name="using-the-admin-consent-endpoint"></a>Uso dell'endpoint di consenso dell'amministratore

Dopo aver usato l'endpoint di consenso dell'amministratore per concedere il consenso dell'amministratore, l'operazione è terminata. Gli utenti non devono eseguire altre azioni. Una volta concesso il consenso dell'amministratore, gli utenti possono ottenere un token di accesso tramite un tipico flusso di autenticazione. Il token di accesso risultante ha le autorizzazioni consentite.

Quando un amministratore della società usa l'applicazione e viene indirizzato all'endpoint di autorizzazione, Microsoft Identity Platform rileva il ruolo dell'utente. Viene chiesto se l'amministratore della società vuole dare il consenso per conto dell'intero tenant per le autorizzazioni richieste. È invece possibile usare un endpoint di consenso dell'amministratore dedicato per richiedere in modo proattivo a un amministratore di concedere l'autorizzazione per conto dell'intero tenant. Questo endpoint è necessario anche per richiedere le autorizzazioni dell'applicazione. Non è possibile richiedere le autorizzazioni dell'applicazione usando l'endpoint di autorizzazione.

Seguendo questa procedura, l'app può richiedere le autorizzazioni per tutti gli utenti di un tenant, inclusi gli ambiti riservati all'amministratore. Questa operazione è con privilegi elevati. Usare l'operazione solo se necessario per lo scenario.

Per vedere un esempio di codice che implementa la procedura, vedere l' [esempio degli ambiti con restrizioni di amministratore](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) in GitHub.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione dell'app

Nel portale di registrazione delle app, le applicazioni possono elencare le autorizzazioni necessarie, incluse le autorizzazioni delegate e le autorizzazioni dell'applicazione. Questa configurazione consente di utilizzare l' `/.default` ambito e l'opzione di **consenso dell'amministratore di concessione** portale di Azure.  

In generale, le autorizzazioni devono essere definite in modo statico per una determinata applicazione. Devono essere un superset delle autorizzazioni che l'app richiederà in modo dinamico o incrementale.

> [!NOTE]
>Le autorizzazioni dell'applicazione possono essere richieste solo tramite l'utilizzo di [`/.default`](#the-default-scope) . Quindi, se l'app richiede le autorizzazioni dell'applicazione, assicurarsi che siano elencate nel portale di registrazione delle app.

Per configurare l'elenco delle autorizzazioni richieste in modo statico per un'applicazione:

1. Passare all'applicazione nell'esperienza di avvio rapido <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">portale di Azure <span class="docon docon-navigate-external x-hidden-focus"></span> registrazioni app</a> .
1. Selezionare un'applicazione oppure [creare un'app](quickstart-register-app.md) , se non è già stata eseguita.
1. Nella pagina **Panoramica** dell'applicazione, in **Gestisci**, selezionare **autorizzazioni API**  >  **Aggiungi un'autorizzazione**.
1. Selezionare **Microsoft Graph** dall'elenco delle API disponibili. Aggiungere quindi le autorizzazioni richieste dall'app.
1. Selezionare **Aggiungi autorizzazioni**.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Consigliato: connettere l'utente dall'applicazione

In genere, durante la compilazione di un'applicazione che usa l'endpoint di consenso dell'amministratore, l'app necessita di una pagina o vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione. Questa pagina può essere:

* Parte del flusso di iscrizione dell'app.
* Parte delle impostazioni dell'app.
* Un flusso "Connect" dedicato. 

In molti casi, è opportuno che l'app visualizzi questa visualizzazione "Connetti" solo dopo che un utente ha eseguito l'accesso con un account Microsoft di lavoro account Microsoft o dell'Istituto di istruzione.

Quando si esegue l'accesso dell'utente all'app, è possibile identificare l'organizzazione a cui appartiene l'amministratore prima di richiedere l'approvazione delle autorizzazioni necessarie. Sebbene questo passaggio non sia strettamente necessario, può essere utile per creare un'esperienza più intuitiva per gli utenti dell'organizzazione. 

Per accedere all'utente, seguire le [esercitazioni sul protocollo Microsoft Identity Platform](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory

Quando si è pronti per richiedere le autorizzazioni all'amministratore dell'organizzazione, è possibile reindirizzare l'utente all'endpoint di consenso dell'amministratore di Microsoft Identity Platform.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parametro        | Condizione        | Descrizione                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Obbligatoria | Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere specificato in un formato di nome descrittivo o GUID. In alternativa, è possibile fare riferimento in modo generico alle organizzazioni, come illustrato nell'esempio. Non usare "comune", perché gli account personali non possono fornire il consenso dell'amministratore tranne nel contesto di un tenant. Per garantire la migliore compatibilità con gli account personali per la gestione dei tenant, usare l'ID tenant quando possibile. |
| `client_id` | Necessario | ID dell'applicazione (client) che la [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) l'esperienza assegnata all'app. |
| `redirect_uri` | Necessario |URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale di registrazione delle applicazioni. |
| `state` | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Usare questo stato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
|`scope`        | Necessario        | Definisce il set di autorizzazioni richieste dall'applicazione. Gli ambiti possono essere statici (usando [`/.default`](#the-default-scope) ) o dinamici.  Questo set può includere gli ambiti di OpenID Connect ( `openid` , `profile` , `email` ). Se sono necessarie le autorizzazioni dell'applicazione, è necessario usare `/.default` per richiedere l'elenco di autorizzazioni configurate in modo statico.  |


A questo punto, Azure AD richiede che solo un amministratore tenant possa accedere per completare la richiesta. All'amministratore viene chiesto di approvare tutte le autorizzazioni richieste nel `scope` parametro.  Se è stato usato un valore statico ( `/.default` ), funzionerà come l'endpoint di consenso dell'amministratore v 1.0 e richiederà il consenso per tutti gli ambiti presenti nelle autorizzazioni necessarie per l'app.

#### <a name="successful-response"></a>Risposta di esito positivo

Se l'amministratore approva le autorizzazioni per l'app, la risposta con esito positivo si presenta come segue:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametro | Descrizione |
| --- | --- |
| `tenant` | Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID. |
| `state` | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
| `admin_consent` | Sarà impostato su `True`. |

#### <a name="error-response"></a>Risposta di errore

Se l'amministratore non approva le autorizzazioni per l'app, la risposta non riuscita ha un aspetto simile al seguente:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | Descrizione |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errore che si verificano. Può anche essere usato per rispondere agli errori. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore. |

Dopo aver ricevuto una risposta con esito positivo dall'endpoint di consenso dell'amministratore, l'applicazione ha ottenuto le autorizzazioni richieste. Successivamente, è possibile richiedere un token per la risorsa desiderata.

## <a name="using-permissions"></a>Uso delle autorizzazioni

Dopo che l'utente ha acconsentito alle autorizzazioni per l'app, l'app può acquisire i token di accesso che rappresentano l'autorizzazione dell'app ad accedere a una risorsa in una certa capacità. Un token di accesso può essere usato solo per una singola risorsa. Ma codificato all'interno del token di accesso è ogni autorizzazione a cui l'app è stata concessa per la risorsa. Per acquisire un token di accesso, l'app può eseguire una richiesta all'endpoint token della piattaforma di identità Microsoft, come indicato di seguito:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

È possibile usare il token di accesso risultante nelle richieste HTTP per la risorsa. Indica in modo affidabile alla risorsa che l'app dispone dell'autorizzazione appropriata per eseguire un'attività specifica.

Per ulteriori informazioni sul protocollo OAuth 2,0 e su come ottenere i token di accesso, vedere il [riferimento al protocollo dell'endpoint della piattaforma di identità Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Ambito /.default

È possibile usare l' `/.default` ambito per eseguire la migrazione delle app dall'endpoint v 1.0 all'endpoint della piattaforma Microsoft Identity. L' `/.default` ambito è incorporato per ogni applicazione che fa riferimento all'elenco statico di autorizzazioni configurate per la registrazione dell'applicazione. 

`scope`Il valore di `https://graph.microsoft.com/.default` è funzionalmente uguale a quello dell' `resource=https://graph.microsoft.com` endpoint v 1.0. Specificando l' `https://graph.microsoft.com/.default` ambito nella richiesta, l'applicazione richiede un token di accesso che include gli ambiti per ogni Microsoft Graph autorizzazione selezionata per l'app nel portale di registrazione delle app. L'ambito viene costruito usando l'URI della risorsa e `/.default` . Quindi, se l'URI della risorsa è `https://contosoApp.com` , l'ambito richiesto è `https://contosoApp.com/.default` .  Per i casi in cui è necessario includere una seconda barra per richiedere correttamente il token, vedere la [sezione sulle barre finali](#trailing-slash-and-default).

L' `/.default` ambito può essere usato in qualsiasi flusso OAuth 2,0. Ma è necessario per il [flusso di credenziali client](v2-oauth2-client-creds-grant-flow.md)e [di flusso per conto di](v2-oauth2-on-behalf-of-flow.md) . È necessario anche quando si usa l'endpoint di consenso dell'amministratore V2 per richiedere le autorizzazioni dell'applicazione.

I client non possono combinare il consenso statico ( `/.default` ) e il consenso dinamico in un'unica richiesta. `scope=https://graph.microsoft.com/.default+mail.read`Genera quindi un errore perché combina i tipi di ambito.

### <a name="default-and-consent"></a>/.default e consenso

L'ambito `/.default` attiva il comportamento dell'endpoint v1.0 anche per `prompt=consent`. Richiede il consenso per tutte le autorizzazioni registrate dall'applicazione, indipendentemente dalla risorsa. Se è incluso come parte della richiesta, l' `/.default` ambito restituisce un token contenente gli ambiti per la risorsa richiesta.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default quando l'utente ha già dato il consenso

L' `/.default` ambito è funzionalmente identico al comportamento dell' `resource` endpoint v 1.0 incentrato su. Comporta anche il comportamento di consenso dell'endpoint v 1.0. Ovvero `/.default` attiva una richiesta di consenso solo se l'utente non ha concesso alcuna autorizzazione tra il client e la risorsa. 

Se tale consenso esiste, il token restituito contiene tutti gli ambiti concessi dall'utente per la risorsa. Tuttavia, se non è stata concessa alcuna autorizzazione o se è `prompt=consent` stato fornito il parametro, viene visualizzata una richiesta di consenso per tutti gli ambiti registrati dall'applicazione client.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Esempio 1: l'utente o l'amministratore del tenant ha concesso le autorizzazioni

In questo esempio, l'utente o un amministratore tenant ha concesso le `mail.read` `user.read` autorizzazioni e Microsoft Graph al client. 

Se il client richiede `scope=https://graph.microsoft.com/.default` , non viene visualizzata alcuna richiesta di consenso, indipendentemente dal contenuto delle autorizzazioni registrate dell'applicazione client per Microsoft Graph. Il token restituito contiene gli ambiti `mail.read` e `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Esempio 2: l'utente non ha concesso le autorizzazioni tra il client e la risorsa

In questo esempio, l'utente non ha concesso il consenso tra il client e Microsoft Graph. Il client ha effettuato la registrazione per le autorizzazioni `user.read` e `contacts.read` . È stata anche registrata per l'ambito Azure Key Vault `https://vault.azure.net/user_impersonation` . 

Quando il client richiede un token per `scope=https://graph.microsoft.com/.default` , l'utente visualizza una pagina di consenso per l'ambito `user.read` , l' `contacts.read` ambito e gli `user_impersonation` ambiti di Key Vault. Il token restituito contiene solo gli `user.read` `contacts.read` ambiti e. Può essere utilizzato solo con Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Esempio 3: l'utente ha acconsentito e il client richiede un maggior numero di ambiti

In questo esempio l'utente ha già acconsentito a `mail.read` per il client. Il client è stato registrato per l' `contacts.read` ambito. 

Quando il client richiede un token tramite `scope=https://graph.microsoft.com/.default` e richiede il consenso tramite `prompt=consent` , l'utente visualizza una pagina di consenso per tutte (e solo) le autorizzazioni registrate dall'applicazione. L' `contacts.read` ambito si trova nella pagina di consenso ma `mail.read` non è. Il token restituito è per Microsoft Graph. Contiene `mail.read` e `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Uso dell'ambito /.default con il client

In alcuni casi, un client può richiedere il proprio `/.default` ambito. L'esempio seguente illustra questo scenario.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Questo esempio di codice genera una pagina di consenso per tutte le autorizzazioni registrate se le descrizioni precedenti del consenso e `/.default` si applicano allo scenario. Il codice restituisce quindi un `id_token` , anziché un token di accesso.  

Questo comportamento include alcuni client legacy che passano da Autenticazione di Azure AD Library (ADAL) a Microsoft Authentication Library (MSAL). Questa configurazione *non deve* essere usata dai nuovi client destinati all'endpoint della piattaforma Microsoft Identity.

### <a name="client-credentials-grant-flow-and-default"></a>Flusso di concessione delle credenziali client e/.default  

Un altro uso di `/.default` è quello di richiedere le autorizzazioni o i *ruoli* dell'applicazione in un'applicazione non interattiva come un'app daemon che usa il flusso di concessione delle [credenziali client](v2-oauth2-client-creds-grant-flow.md) per chiamare un'API Web.

Per creare le autorizzazioni dell'applicazione (ruoli) per un'API Web, vedere [aggiungere i ruoli dell'app nell'applicazione](howto-add-app-roles-in-azure-ad-apps.md).

Le richieste di credenziali client nell'app client *devono* includere `scope={resource}/.default` . Qui, `{resource}` è l'API Web che l'app intende chiamare. Il rilascio di una richiesta di credenziali client tramite le singole autorizzazioni dell'applicazione (ruoli) *non* è supportato. Tutte le autorizzazioni dell'applicazione (ruoli) concesse per l'API Web sono incluse nel token di accesso restituito.

Per concedere l'accesso alle autorizzazioni dell'applicazione definite, inclusa la concessione del consenso dell'amministratore per l'applicazione, vedere [configurare un'applicazione client per l'accesso a un'API Web](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Barra finale e/.default

Alcuni URI delle risorse hanno una barra finale, ad esempio, `https://contoso.com/` anziché `https://contoso.com` . La barra finale può causare problemi con la convalida dei token. I problemi si verificano principalmente quando viene richiesto un token per Azure Resource Manager ( `https://management.azure.com/` ). In questo caso, una barra finale nell'URI della risorsa indica che la barra deve essere presente quando viene richiesto il token.  Pertanto, quando si richiede un token per `https://management.azure.com/` e `/.default` si utilizza, è necessario richiedere `https://management.azure.com//.default` (si noti la barra doppia!). In generale, se si verifica che il token viene emesso e se il token viene rifiutato dall'API che la accetta, è consigliabile aggiungere una seconda barra e riprovare. 

## <a name="troubleshooting-permissions-and-consent"></a>Risoluzione dei problemi di autorizzazioni e consenso

Per la procedura di risoluzione dei problemi, vedere [errore imprevisto durante l'esecuzione del consenso per un'applicazione](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Passaggi successivi

* [Token ID nella piattaforma di identità Microsoft](id-tokens.md)
* [Token di accesso nella piattaforma di identità Microsoft](access-tokens.md)
