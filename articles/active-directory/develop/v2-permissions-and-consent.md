---
title: Ambiti, autorizzazioni e autorizzazioni di Microsoft Identity Platform & consenso
description: Informazioni sull'autorizzazione nell'endpoint Microsoft Identity Platform, inclusi ambiti, autorizzazioni e consenso.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2a975a0aba06ecfd010fe328ef6c8cda75290f2b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515584"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Autorizzazioni e consenso in Microsoft Identity Platform

Le applicazioni che si integrano con Microsoft Identity Platform seguono un modello di autorizzazione che consente a utenti e amministratori di controllare come è possibile accedere ai dati. L'implementazione del modello di autorizzazione è stata aggiornata in Microsoft Identity Platform. Modifica il modo in cui un'app deve interagire con Microsoft Identity Platform. Questo articolo illustra i concetti di base di questo modello di autorizzazione, inclusi gli ambiti, le autorizzazioni e il consenso.

## <a name="scopes-and-permissions"></a>Ambiti e autorizzazioni

Microsoft Identity Platform implementa il protocollo di autorizzazione [OAuth 2.0](active-directory-v2-protocols.md). OAuth 2.0 è un metodo tramite cui un'applicazione di terze parti può accedere alle risorse ospitate sul Web per conto dell'utente. Qualsiasi risorsa ospitata sul Web che si integra con Microsoft Identity Platform ha un identificatore di risorsa o un *URI dell'ID applicazione*. 

Ecco alcuni esempi di risorse ospitate sul Web Microsoft:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 API Mail: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Lo stesso vale per le risorse di terze parti integrate con Microsoft Identity Platform. Tali risorse possono anche definire un set di autorizzazioni che può essere usato per suddividere le funzionalità di tale risorsa in blocchi più piccoli. Ad esempio, [Microsoft Graph](https://graph.microsoft.com) ha definito le autorizzazioni per eseguire le attività seguenti, tra le altre:

* Lettura del calendario dell'utente
* Scrittura nel calendario dell'utente
* Invio di messaggi di posta elettronica come utente

A causa di questi tipi di definizioni di autorizzazione, la risorsa ha un controllo granulare sui dati e sulla modalità di esposizione delle funzionalità API. Un'app di terze parti può richiedere queste autorizzazioni da utenti e amministratori, che devono approvare la richiesta prima che l'app possa accedere ai dati o agire per conto di un utente. 

Quando le funzionalità di una risorsa vengono suddivise in set di autorizzazioni di piccole dimensioni, è possibile creare app di terze parti per richiedere solo le autorizzazioni necessarie per eseguire la propria funzione. Gli utenti e gli amministratori possono conoscere i dati a cui l'app può accedere. E possono essere più sicuri che l'app non si comporti con finalità dannose. Gli sviluppatori devono sempre rispettare il principio dei privilegi minimi, richiedendo solo le autorizzazioni necessarie per il funzionamento delle applicazioni.

In OAuth 2.0 questi tipi di set di autorizzazioni sono denominati *ambiti*. Vengono anche spesso definite *autorizzazioni*. In Microsoft Identity Platform un'autorizzazione è rappresentata come valore stringa. Un'app richiede le autorizzazioni necessarie specificando l'autorizzazione nel parametro `scope` di query. Identity Platform supporta diversi ambiti [](#openid-connect-scopes) di OpenID Connect ben definiti, nonché autorizzazioni basate sulle risorse (ogni autorizzazione viene indicata aggiungendo il valore di autorizzazione all'identificatore della risorsa o all'URI dell'ID applicazione). Ad esempio, la stringa di autorizzazione `https://graph.microsoft.com/Calendars.Read` viene usata per richiedere l'autorizzazione per leggere i calendari degli utenti Microsoft Graph.

Un'app richiede in genere queste autorizzazioni specificando gli ambiti nelle richieste all'endpoint di autorizzazione di Microsoft Identity Platform. Tuttavia, alcune autorizzazioni con privilegi elevati possono essere concesse solo tramite il consenso dell'amministratore. Possono essere richieste o concesse tramite [l'endpoint di consenso dell'amministratore](#admin-restricted-permissions). Continuare a leggere per altre informazioni.

## <a name="permission-types"></a>Tipi di autorizzazioni

Microsoft Identity Platform supporta due tipi di autorizzazioni: *autorizzazioni delegate e* autorizzazioni *dell'applicazione.*

* Le **autorizzazioni delegate** sono usate dalle app con un utente connesso. Per queste app, l'utente o un amministratore acconsente alle autorizzazioni richieste dall'app. All'app viene delegata l'autorizzazione a fungere da utente connesso quando effettua chiamate alla risorsa di destinazione. 

    Alcune autorizzazioni delegate possono essere concesse da utenti non amministratori. Tuttavia, alcune autorizzazioni con privilegi elevati richiedono il [consenso dell'amministratore](#admin-restricted-permissions). Per informazioni sui ruoli di amministratore che possono concedere il consenso alle autorizzazioni delegate, vedere Autorizzazioni dei ruoli di amministratore [in Azure Active Directory (Azure AD).](../roles/permissions-reference.md)

* **Le autorizzazioni** dell'applicazione vengono usate dalle app eseguite senza un utente connesso, ad esempio le app eseguite come servizi in background o daemon. Solo [un amministratore può concedere il consenso alle autorizzazioni](#requesting-consent-for-an-entire-tenant) dell'applicazione.

_Le autorizzazioni_ effettive sono le autorizzazioni che l'app ha quando effettua richieste alla risorsa di destinazione. È importante comprendere la differenza tra le autorizzazioni delegate e le autorizzazioni dell'applicazione concesse all'app e le autorizzazioni effettive concesse all'app quando effettua chiamate alla risorsa di destinazione.

- Per le autorizzazioni  delegate, le autorizzazioni effettive dell'app sono l'intersezione con privilegi minimi tra le autorizzazioni delegate concesse all'app (per consenso) e i privilegi dell'utente attualmente connesso. L'app non può mai avere più privilegi rispetto all'utente connesso. 

   All'interno delle organizzazioni, i privilegi dell'utente connesso possono essere determinati dai criteri o dall'appartenenza a uno o più ruoli di amministratore. Per informazioni su quali ruoli di amministratore possono fornire il consenso per le autorizzazioni delegate, vedere [Autorizzazioni del ruolo di amministratore in Azure AD](../roles/permissions-reference.md).

   Si supponga, ad esempio, che all'app sia stata concessa l'autorizzazione delegata _User.ReadWrite.All_. Questa autorizzazione concede nominalmente all'app l'autorizzazione per leggere e aggiornare il profilo di ogni utente in un'organizzazione. Se l'utente connesso è un amministratore globale, l'app può aggiornare il profilo di ogni utente dell'organizzazione. Tuttavia, se l'utente connesso non ha un ruolo di amministratore, l'app può aggiornare solo il profilo dell'utente connesso. Non può aggiornare i profili di altri utenti dell'organizzazione perché l'utente di cui è autorizzato ad agire per conto di non dispone di tali privilegi.

- Per le autorizzazioni dell'applicazione, _le_ autorizzazioni effettive dell'app sono il livello completo di privilegi impliciti nell'autorizzazione. Un'app che ha l'autorizzazione dell'applicazione _User.ReadWrite.All_ può ad esempio aggiornare il profilo di tutti gli utenti dell'organizzazione.

## <a name="openid-connect-scopes"></a>Ambiti di OpenID Connect

L'implementazione di Microsoft Identity Platform OpenID Connect ha alcuni ambiti ben definiti che sono ospitati anche in Microsoft Graph: `openid` , `email` , e `profile` `offline_access` . Gli `address` ambiti OpenID Connect e non sono `phone` supportati.

Se si richiedono gli OpenID Connect e un token, si otterrà un token per chiamare [l'endpoint UserInfo](userinfo.md).

### <a name="openid"></a>openid

Se un'app accede [usando](active-directory-v2-protocols.md)OpenID Connect , deve richiedere l'ambito. `openid` `openid`L'ambito viene visualizzato nella pagina di consenso dell'account aziendale come autorizzazione **Accedi.** Nella pagina di account Microsoft di consenso viene visualizzata come Visualizza il profilo e connettiti ad app e servizi usando **l'autorizzazione** account Microsoft sicurezza. 

Usando questa autorizzazione, un'app può ricevere un identificatore univoco per l'utente sotto forma di `sub` attestazione. L'autorizzazione concede anche all'app l'accesso all'endpoint UserInfo. `openid`L'ambito può essere usato nell'endpoint del token di Microsoft Identity Platform per acquisire i token ID. L'app può usare questi token per l'autenticazione.

### <a name="email"></a>email

`email`L'ambito può essere usato con `openid` l'ambito e con qualsiasi altro ambito. Consente all'applicazione di accedere all'indirizzo di posta elettronica primario dell'utente sotto forma di attestazione `email`. 

L'attestazione è inclusa in un token solo se un indirizzo di posta elettronica è associato all'account utente, come non `email` sempre. Se l'app usa l'ambito , l'app deve essere in grado di gestire un caso in cui non `email` `email` esiste alcuna attestazione nel token.

### <a name="profile"></a>profile

`profile`L'ambito può essere utilizzato con `openid` l'ambito e qualsiasi altro ambito. Consente all'app di accedere a una grande quantità di informazioni sull'utente. Le informazioni a cui può accedere includono, ma non solo, il nome, il cognome, il nome utente preferito e l'ID oggetto dell'utente. 

Per un elenco completo delle `profile` attestazioni disponibili nel parametro per un utente `id_tokens` specifico, vedere il [ `id_tokens` riferimento](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` L'ambito](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) consente all'app di accedere alle risorse per conto dell'utente per un lungo periodo di tempo. Nella pagina di consenso questo ambito viene visualizzato come Mantenere l'accesso ai dati a cui è stato assegnato **l'accesso all'autorizzazione.** 

Quando un utente approva `offline_access` l'ambito, l'app può ricevere token di aggiornamento dall'endpoint del token di Microsoft Identity Platform. I token di aggiornamento sono di lunga durata. L'applicazione può ottenere nuovi token di accesso quando i vecchi scadono.

> [!NOTE]
> Questa autorizzazione viene attualmente visualizzata in tutte le pagine di consenso, anche per i flussi che non forniscono un token di aggiornamento, ad esempio il [flusso implicito](v2-oauth2-implicit-grant-flow.md). Questa configurazione risolve gli scenari in cui un client può iniziare all'interno del flusso implicito e quindi passare al flusso di codice in cui è previsto un token di aggiornamento.

In Microsoft Identity Platform (richieste effettuate all'endpoint 2.0), l'app deve richiedere in modo esplicito l'ambito `offline_access` per ricevere i token di aggiornamento. Pertanto, quando si riscatta un codice di autorizzazione nel flusso del codice di autorizzazione [OAuth 2.0,](active-directory-v2-protocols.md)si riceverà solo un token di accesso `/token` dall'endpoint. 

Il token di accesso è valido per un breve periodo. In genere scade tra un'ora. A questo punto, l'app reindirizza l'utente all'endpoint `/authorize` per recuperare un nuovo codice di autorizzazione. Durante il reindirizzamento, a seconda del tipo di app, l'utente potrebbe dover immettere nuovamente le proprie credenziali o fornire il consenso per le autorizzazioni.

Per altre informazioni su come ottenere e usare i token di aggiornamento, vedere le informazioni di riferimento sul [protocollo Microsoft Identity Platform.](active-directory-v2-protocols.md)

## <a name="requesting-individual-user-consent"></a>Richiesta di consenso per un singolo utente

In una richiesta di autorizzazione [OpenID Connect o OAuth 2.0](active-directory-v2-protocols.md) un'app può richiedere le autorizzazioni necessarie usando il parametro di query `scope`. Ad esempio, quando un utente accede a un'app, l'app invia una richiesta come nell'esempio seguente. Le interruzioni di riga vengono aggiunte per la leggibilità.

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

Il parametro `scope` è un elenco di autorizzazioni delegate separate da spazi richieste dall'app. Ogni autorizzazione viene indicata aggiungendo il valore dell'autorizzazione all'identificatore della risorsa (URI ID applicazione). Nella richiesta di esempio precedente, l'app richiede l'autorizzazione per la lettura del calendario dell'utente e l'invio di messaggi di posta elettronica come utente.

Dopo che l'utente ha immesso le proprie credenziali, Microsoft Identity Platform verifica la presenza di un record corrispondente di *consenso dell'utente.* Se l'utente non ha acconsentito a nessuna delle autorizzazioni richieste in passato e se l'amministratore non ha acconsentito a queste autorizzazioni per conto dell'intera organizzazione, Microsoft Identity Platform chiede all'utente di concedere le autorizzazioni richieste.

A questo punto, l'autorizzazione ("Mantieni l'accesso ai dati a cui è stato assegnato l'accesso") e l'autorizzazione "Accedi e leggi il tuo profilo") vengono incluse automaticamente nel consenso iniziale a `offline_access` `user.read` un'applicazione.  Queste autorizzazioni sono in genere necessarie per la corretta funzionalità dell'app. `offline_access`L'autorizzazione consente all'app di accedere ai token di aggiornamento critici per le app native e le app Web. `user.read`L'autorizzazione concede l'accesso `sub` all'attestazione. Consente al client o all'app di identificare correttamente l'utente nel tempo e di accedere alle informazioni rudimentari dell'utente.

![Screenshot di esempio che mostra il consenso dell'account aziendale.](./media/v2-permissions-and-consent/work_account_consent.png)

Quando l'utente approva la richiesta di autorizzazione, viene registrato il consenso. L'utente non deve acconsentire di nuovo quando in un secondo momento accede all'applicazione.

## <a name="requesting-consent-for-an-entire-tenant"></a>Richiesta di consenso per un intero tenant

Quando un'organizzazione acquista una licenza o una sottoscrizione per un'applicazione, spesso vuole configurare in modo proattivo l'applicazione per l'uso da parte di tutti i membri dell'organizzazione. Come parte di questo processo, un amministratore può concedere il consenso all'applicazione ad agire per conto di qualsiasi utente nel tenant. Se l'amministratore concede il consenso per l'intero tenant, gli utenti dell'organizzazione non visualizzano una pagina di consenso per l'applicazione.

Per richiedere il consenso per le autorizzazioni delegate per tutti gli utenti in un tenant, l'app può usare l'endpoint di consenso amministratore.

Inoltre, le applicazioni devono usare l'endpoint di consenso amministratore per richiedere le autorizzazioni dell'applicazione.

## <a name="admin-restricted-permissions"></a>Autorizzazioni riservate all'amministratore

Alcune autorizzazioni con privilegi elevati nelle risorse Microsoft possono essere impostate su *con restrizioni di amministratore.* Ecco alcuni esempi di questi tipi di autorizzazioni:

* Lettura dei profili completi degli utenti tramite `User.Read.All`
* Scrittura di dati in una directory aziendale tramite `Directory.ReadWrite.All`
* Lettura di tutti i gruppi nella directory aziendale tramite `Groups.Read.All`

Anche se un utente consumer può concedere a un'applicazione l'accesso a questo tipo di dati, gli utenti dell'organizzazione non possono concedere l'accesso allo stesso set di dati aziendali sensibili. Se l'applicazione richiede l'accesso a una di queste autorizzazioni da un utente dell'organizzazione, l'utente riceve un messaggio di errore che indica che non è autorizzato a fornire il consenso alle autorizzazioni dell'applicazione.

Se l'app richiede ambiti per le autorizzazioni con restrizioni di amministratore, l'amministratore di un'organizzazione deve fornire il consenso a tali ambiti per conto degli utenti dell'organizzazione. Per evitare di visualizzare richieste agli utenti che richiedono il consenso per le autorizzazioni che non possono concedere, l'app può usare l'endpoint di consenso amministratore. L'endpoint di consenso dell'amministratore viene trattato nella sezione successiva.

Se l'applicazione richiede autorizzazioni delegate con privilegi elevati e un amministratore concede queste autorizzazioni tramite l'endpoint di consenso amministratore, viene concesso il consenso per tutti gli utenti nel tenant.

Se l'applicazione richiede le autorizzazioni dell'applicazione e un amministratore concede queste autorizzazioni tramite l'endpoint di consenso amministratore, questa concessione non viene eseguita per conto di un utente specifico. Al contrario, all'applicazione client le autorizzazioni verranno concesse *direttamente*. Questi tipi di autorizzazioni vengono usati solo dai servizi daemon e da altre applicazioni non interattive eseguite in background.

## <a name="using-the-admin-consent-endpoint"></a>Uso dell'endpoint di consenso dell'amministratore

Dopo aver utilizzato l'endpoint di consenso amministratore per concedere il consenso dell'amministratore, l'operazione è terminata. Gli utenti non devono eseguire altre azioni. Dopo aver concesso il consenso dell'amministratore, gli utenti possono ottenere un token di accesso tramite un flusso di autenticazione tipico. Il token di accesso risultante dispone delle autorizzazioni concesse.

Quando un amministratore globale usa l'applicazione e viene indirizzato all'endpoint di autorizzazione, Microsoft Identity Platform rileva il ruolo dell'utente. Chiede se l'amministratore globale vuole concedere il consenso per conto dell'intero tenant per le autorizzazioni richieste. È invece possibile usare un endpoint di consenso amministratore dedicato per richiedere in modo proattivo a un amministratore di concedere l'autorizzazione per conto dell'intero tenant. Questo endpoint è necessario anche per richiedere le autorizzazioni dell'applicazione. Le autorizzazioni dell'applicazione non possono essere richieste usando l'endpoint di autorizzazione.

Seguendo questa procedura, l'app può richiedere le autorizzazioni per tutti gli utenti di un tenant, inclusi gli ambiti riservati all'amministratore. Questa operazione è un privilegio elevato. Usare l'operazione solo se necessario per lo scenario in uso.

Per un esempio di codice che implementa i passaggi, vedere l'esempio di ambiti con restrizioni [di amministrazione](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) in GitHub.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione dell'app

Nel portale di registrazione delle app le applicazioni possono elencare le autorizzazioni necessarie, incluse le autorizzazioni delegate e le autorizzazioni dell'applicazione. Questa configurazione consente l'uso `/.default` dell'ambito e dell'portale di Azure'opzione **Concedi** consenso amministratore.  

In generale, le autorizzazioni devono essere definite in modo statico per una determinata applicazione. Devono essere un superset delle autorizzazioni che l'app richiederà in modo dinamico o incrementale.

> [!NOTE]
>Le autorizzazioni dell'applicazione possono essere richieste solo tramite l'uso di [`/.default`](#the-default-scope) . Pertanto, se l'app richiede autorizzazioni per l'applicazione, assicurarsi che siano elencate nel portale di registrazione delle app.

Per configurare l'elenco delle autorizzazioni richieste in modo statico per un'applicazione:

1. Passare all'applicazione <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">nell'portale di Azure - Registrazioni app</a> di avvio rapido.
1. Selezionare un'applicazione o [crearne una,](quickstart-register-app.md) se non è già stata creata.
1. Nella pagina Panoramica  dell'applicazione, in **Gestisci** selezionare Autorizzazioni **API**  >  **Aggiungi un'autorizzazione.**
1. Selezionare **Microsoft Graph** dall'elenco delle API disponibili. Aggiungere quindi le autorizzazioni richieste dall'app.
1. Selezionare **Aggiungi autorizzazioni.**

### <a name="recommended-sign-the-user-in-to-your-app"></a>Consigliato: connettere l'utente dall'applicazione

In genere, durante la compilazione di un'applicazione che usa l'endpoint di consenso dell'amministratore, l'app necessita di una pagina o vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione. Questa pagina può essere:

* Parte del flusso di iscrizione dell'app.
* Parte delle impostazioni dell'app.
* Un flusso di "connessione" dedicato. 

In molti casi, è opportuno che l'app mostri questa visualizzazione di "connessione" solo dopo che un utente ha eseguito l'accesso con un account aziendale o dell'istituto di account Microsoft dell'account Microsoft.

Quando si accede all'app, è possibile identificare l'organizzazione a cui appartiene l'amministratore prima di chiedere di approvare le autorizzazioni necessarie. Anche se questo passaggio non è strettamente necessario, può essere utile per creare un'esperienza più intuitiva per gli utenti dell'organizzazione. 

Per l'accesso dell'utente, seguire le [esercitazioni sul protocollo Microsoft Identity Platform](active-directory-v2-protocols.md).

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
| `tenant` | Obbligatoria | Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere fornito in un formato di nome descrittivo o GUID. Oppure è possibile farvi riferimento in modo generico con le organizzazioni, come illustrato nell'esempio. Non usare "common", perché gli account personali non possono fornire il consenso dell'amministratore se non nel contesto di un tenant. Per garantire la migliore compatibilità con gli account personali che gestiscono i tenant, usare l'ID tenant quando possibile. |
| `client_id` | Necessario | ID applicazione (client) che l'portale di Azure [- Registrazioni app'esperienza](https://go.microsoft.com/fwlink/?linkid=2083908) assegnata all'app. |
| `redirect_uri` | Necessario |URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale di registrazione delle applicazioni. |
| `state` | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Usare questo stato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
|`scope`        | Necessario        | Definisce il set di autorizzazioni richieste dall'applicazione. Gli ambiti possono essere statici (tramite [`/.default`](#the-default-scope) ) o dinamici.  Questo set può includere gli OpenID Connect seguenti ( `openid` , `profile` , `email` ). Se sono necessarie autorizzazioni per l'applicazione, è necessario usare per richiedere l'elenco di autorizzazioni `/.default` configurate in modo statico.  |


A questo punto, Azure AD richiede che solo un amministratore tenant possa accedere per completare la richiesta. All'amministratore viene richiesto di approvare tutte le autorizzazioni richieste nel `scope` parametro .  Se è stato usato un valore statico ( ), funzionerà come l'endpoint di consenso dell'amministratore v1.0 e richiederà il consenso per tutti gli ambiti trovati nelle autorizzazioni necessarie per `/.default` l'app.

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

Se l'amministratore non approva le autorizzazioni per l'app, la risposta non riuscita è simile alla seguente:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | Descrizione |
| --- | --- |
| `error` | Stringa di codice di errore che può essere usata per classificare i tipi di errore che si verificano. Può anche essere usato per reagire agli errori. |
| `error_description` | Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore. |

Dopo aver ricevuto una risposta con esito positivo dall'endpoint di consenso dell'amministratore, l'applicazione ha ottenuto le autorizzazioni richieste. Successivamente, è possibile richiedere un token per la risorsa desiderata.

## <a name="using-permissions"></a>Uso delle autorizzazioni

Dopo che l'utente ha dato il consenso alle autorizzazioni per l'app, l'app può acquisire token di accesso che rappresentano l'autorizzazione dell'app ad accedere a una risorsa in una certa capacità. Un token di accesso può essere usato solo per una singola risorsa. Tuttavia, la codifica all'interno del token di accesso è ogni autorizzazione concessa all'app per tale risorsa. Per acquisire un token di accesso, l'app può effettuare una richiesta all'endpoint del token di Microsoft Identity Platform, come nell'esempio seguente:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

È possibile usare il token di accesso risultante nelle richieste HTTP per la risorsa. Indica in modo affidabile alla risorsa che l'app ha l'autorizzazione appropriata per eseguire un'attività specifica.

Per altre informazioni sul protocollo OAuth 2.0 e su come ottenere i token di accesso, vedere le informazioni di riferimento sul protocollo [endpoint di Microsoft Identity Platform.](active-directory-v2-protocols.md)

## <a name="the-default-scope"></a>Ambito /.default

È possibile usare `/.default` l'ambito per eseguire la migrazione delle app dall'endpoint v1.0 all'endpoint di Microsoft Identity Platform. `/.default`L'ambito è incorporato per ogni applicazione che fa riferimento all'elenco statico di autorizzazioni configurate nella registrazione dell'applicazione. 

Dal `scope` punto di vista `https://graph.microsoft.com/.default` funzionale, il valore è uguale a quello `resource=https://graph.microsoft.com` dell'endpoint v1.0. Specificando l'ambito nella richiesta, l'applicazione richiede un token di accesso che include gli ambiti per ogni autorizzazione Microsoft Graph selezionata per l'app nel portale di registrazione `https://graph.microsoft.com/.default` delle app. L'ambito viene costruito usando l'URI della risorsa e `/.default` . Pertanto, se l'URI della risorsa `https://contosoApp.com` è , l'ambito richiesto è `https://contosoApp.com/.default` .  Per i casi in cui è necessario includere una seconda barra per richiedere correttamente il token, vedere la [sezione sulle barre finali.](#trailing-slash-and-default)

`/.default`L'ambito può essere usato in qualsiasi flusso OAuth 2.0. Ma è necessario nel flusso [On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) e nel flusso [delle credenziali client.](v2-oauth2-client-creds-grant-flow.md) È necessario anche quando si usa l'endpoint di consenso amministratore v2 per richiedere le autorizzazioni dell'applicazione.

I client non possono combinare il consenso statico ( `/.default` ) e il consenso dinamico in una singola richiesta. Pertanto, `scope=https://graph.microsoft.com/.default+mail.read` viene generato un errore perché combina i tipi di ambito.

### <a name="default-and-consent"></a>/.default e consenso

L'ambito `/.default` attiva il comportamento dell'endpoint v1.0 anche per `prompt=consent`. Richiede il consenso per tutte le autorizzazioni registrate dall'applicazione, indipendentemente dalla risorsa. Se è incluso come parte della richiesta, l'ambito restituisce un token che contiene `/.default` gli ambiti per la risorsa richiesta.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default quando l'utente ha già dato il consenso

Dal punto di vista funzionale, l'ambito è identico al comportamento `/.default` `resource` dell'endpoint 1.0 incentrato su . Contiene anche il comportamento di consenso dell'endpoint v1.0. Ciò significa che attiva una richiesta di consenso solo se l'utente non ha concesso `/.default` alcuna autorizzazione tra il client e la risorsa. 

Se esiste un consenso di questo tipo, il token restituito contiene tutti gli ambiti concessi dall'utente per tale risorsa. Tuttavia, se non è stata concessa alcuna autorizzazione o se è stato fornito il parametro , viene visualizzata una richiesta di consenso per tutti gli ambiti registrati `prompt=consent` dall'applicazione client.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Esempio 1: l'utente o l'amministratore tenant ha concesso le autorizzazioni

In questo esempio l'utente o un amministratore tenant ha concesso Microsoft Graph autorizzazioni `mail.read` `user.read` al client. 

Se il client richiede , non viene visualizzata alcuna richiesta di consenso, indipendentemente dal contenuto delle autorizzazioni registrate `scope=https://graph.microsoft.com/.default` dell'applicazione client per Microsoft Graph. Il token restituito contiene gli ambiti `mail.read` e `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Esempio 2: l'utente non ha concesso le autorizzazioni tra il client e la risorsa

In questo esempio l'utente non ha concesso il consenso tra il client e Microsoft Graph. Il client ha registrato per le autorizzazioni `user.read` e `contacts.read` . È stato registrato anche per l'ambito `https://vault.azure.net/user_impersonation` Azure Key Vault. 

Quando il client richiede un token per , l'utente visualizza una pagina di consenso per l'ambito, l'ambito e Key Vault `scope=https://graph.microsoft.com/.default` `user.read` `contacts.read` `user_impersonation` ambiti. Il token restituito contiene solo `user.read` gli `contacts.read` ambiti e . Può essere usato solo in Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Esempio 3: l'utente ha concesso il consenso e il client richiede più ambiti

In questo esempio l'utente ha già acconsentito `mail.read` a per il client. Il client è stato registrato per `contacts.read` l'ambito. 

Quando il client richiede un token usando e richiede il consenso tramite , l'utente visualizza una pagina di consenso per tutte (e solo) le autorizzazioni registrate `scope=https://graph.microsoft.com/.default` `prompt=consent` dall'applicazione. `contacts.read`L'ambito si trova nella pagina di consenso, ma non lo `mail.read` è. Il token restituito è per Microsoft Graph. Contiene `mail.read` e `contacts.read` .

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

Questo esempio di codice genera una pagina di consenso per tutte le autorizzazioni registrate se le descrizioni precedenti del consenso e si `/.default` applicano allo scenario. Il codice restituisce quindi , `id_token` anziché un token di accesso.  

Questo comportamento supporta alcuni client legacy che si spostano da Autenticazione di Azure AD Library (ADAL) a Microsoft Authentication Library (MSAL). Questa configurazione *non deve essere usata* dai nuovi client che hanno come destinazione Microsoft Identity Platform.

### <a name="client-credentials-grant-flow-and-default"></a>Flusso di concessione delle credenziali client e /.default  

Un altro uso di è richiedere autorizzazioni dell'applicazione (o ruoli ) in un'applicazione non interattiva come un'app daemon che usa il flusso di concessione delle credenziali client per chiamare `/.default` un'API Web.  [](v2-oauth2-client-creds-grant-flow.md)

Per creare autorizzazioni dell'applicazione (ruoli) per un'API Web, vedere [Aggiungere ruoli dell'app nell'applicazione.](howto-add-app-roles-in-azure-ad-apps.md)

Le richieste di credenziali client nell'app client *devono* includere `scope={resource}/.default` . Ecco `{resource}` l'API Web che l'app intende chiamare. L'emissione di una richiesta di credenziali client usando singole autorizzazioni dell'applicazione (ruoli) *non è* supportata. Tutte le autorizzazioni dell'applicazione (ruoli) concesse per l'API Web sono incluse nel token di accesso restituito.

Per concedere l'accesso alle autorizzazioni dell'applicazione definite, inclusa la concessione del consenso dell'amministratore per l'applicazione, vedere Configurare un'applicazione client per [accedere a un'API Web.](quickstart-configure-app-access-web-apis.md)

### <a name="trailing-slash-and-default"></a>Barra finale e /.default

Alcuni URI di risorsa hanno una barra finale, ad esempio, `https://contoso.com/` anziché `https://contoso.com` . La barra finale può causare problemi con la convalida dei token. I problemi si verificano principalmente quando viene richiesto un token per Azure Resource Manager ( `https://management.azure.com/` ). In questo caso, una barra finale nell'URI della risorsa indica che la barra deve essere presente quando viene richiesto il token.  Pertanto, quando si richiede un token per e si `https://management.azure.com/` usa , è necessario richiedere `/.default` `https://management.azure.com//.default` (notare la doppia barra!). In generale, se si verifica che il token venga emesso e se il token viene rifiutato dall'API che lo deve accettare, provare ad aggiungere una seconda barra e riprovare. 

## <a name="troubleshooting-permissions-and-consent"></a>Risoluzione dei problemi di autorizzazioni e consenso

Per la procedura di risoluzione dei problemi, vedere [Errore imprevisto durante l'esecuzione del consenso a un'applicazione.](../manage-apps/application-sign-in-unexpected-user-consent-error.md)

## <a name="next-steps"></a>Passaggi successivi

* [Token ID in Microsoft Identity Platform](id-tokens.md)
* [Token di accesso in Microsoft Identity Platform](access-tokens.md)
