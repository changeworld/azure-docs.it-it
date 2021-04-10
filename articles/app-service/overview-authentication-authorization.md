---
title: Autenticazione e autorizzazione
description: Informazioni sul supporto integrato per l'autenticazione e l'autorizzazione nel servizio app Azure e funzioni di Azure e su come può aiutare a proteggere l'app da accessi non autorizzati.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 35513abdfb61d889abdbd4af7125b1fbb556d7b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612756"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Autenticazione e autorizzazione nel servizio app Azure e funzioni di Azure

App Azure servizio fornisce funzionalità di autenticazione e autorizzazione predefinite (talvolta denominate "Easy auth"), in modo che sia possibile accedere agli utenti e accedere ai dati scrivendo codice minimo o senza codice nell'app Web, API RESTful e back-end per dispositivi mobili e anche [funzioni di Azure](../azure-functions/functions-overview.md). Questo articolo descrive in che modo il servizio app aiuta a semplificare l'autenticazione e l'autorizzazione per l'app.

## <a name="why-use-the-built-in-authentication"></a>Perché usare l'autenticazione predefinita?

Non è necessario usare questa funzionalità per l'autenticazione e l'autorizzazione. È possibile usare le funzionalità di sicurezza in bundle nel framework Web preferito oppure è possibile scrivere utilità personalizzate. Tuttavia, sarà necessario assicurarsi che la soluzione rimanga aggiornata con gli aggiornamenti più recenti relativi a sicurezza, protocollo e browser.

L'implementazione di una soluzione protetta per l'autenticazione (utenti di accesso) e l'autorizzazione (che fornisce l'accesso ai dati protetti) possono richiedere un notevole sforzo. È necessario assicurarsi di seguire gli standard e le procedure consigliate per il settore e di aggiornare l'implementazione. La funzionalità di autenticazione predefinita per il servizio app e funzioni di Azure consente di risparmiare tempo e impegno grazie all'autenticazione predefinita con i provider di identità federati, consentendo di concentrarsi sul resto dell'applicazione.

- App Azure servizio permette di integrare un'ampia gamma di funzionalità di autenticazione nell'app Web o nell'API senza implementarle autonomamente.
- Viene creato direttamente nella piattaforma e non richiede alcun linguaggio, SDK, esperienza di sicurezza o anche codice da usare.
- È possibile eseguire l'integrazione con più provider di accesso. Ad esempio, Azure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>Provider di identità

Il servizio app usa l'[identità federata](https://en.wikipedia.org/wiki/Federated_identity), in cui un provider di identità di terze parti gestisce le identità utente e il flusso di autenticazione. Per impostazione predefinita, sono disponibili i seguenti provider di identità:

| Provider | Endpoint di accesso | Guida How-To |
| - | - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [Accesso Azure AD del servizio app](configure-authentication-provider-aad.md) |
| [Account Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` | [Accesso all'account Microsoft del servizio app](configure-authentication-provider-microsoft.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [Accesso a Facebook del servizio app](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [Account di accesso di Google per il servizio app](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [Accesso a Twitter del servizio app](configure-authentication-provider-twitter.md) |
| Qualsiasi provider [OpenID Connect](https://openid.net/connect/) (anteprima) | `/.auth/login/<providerName>` | [Accesso OpenID Connect del servizio app](configure-authentication-provider-openid-connect.md) |

Quando si abilitano l'autenticazione e l'autorizzazione con uno di questi provider, il relativo endpoint di accesso è disponibile per l'autenticazione utente e per la convalida dei token di autenticazione del provider. È possibile fornire agli utenti un numero qualsiasi di queste opzioni di accesso.

## <a name="considerations-for-using-built-in-authentication"></a>Considerazioni sull'uso dell'autenticazione incorporata

Se si abilita questa funzionalità, tutte le richieste all'applicazione verranno reindirizzate automaticamente a HTTPS, indipendentemente dall'impostazione di configurazione del servizio app per applicare HTTPS. È possibile disabilitare questa impostazione con l'  `requireHttps` impostazione nella configurazione V2. Tuttavia, si consiglia di attenersi a HTTPS ed è necessario assicurarsi che non venga mai trasmesso alcun token di sicurezza su connessioni HTTP non protette.

Il servizio app può essere usato per l'autenticazione con o senza limitare l'accesso al contenuto e alle API del sito. Per limitare l'accesso alle app solo agli utenti autenticati, impostare l' **azione da eseguire quando la richiesta non viene autenticata** per accedere con uno dei provider di identità configurati. Per eseguire l'autenticazione ma non limitare l'accesso, impostare l' **azione da eseguire quando la richiesta non è autenticata** su "Consenti richieste anonime (nessuna azione)".

> [!NOTE]
> È necessario assegnare a ogni app la registrazione delle proprie autorizzazioni e il suo consenso. Evitare la condivisione delle autorizzazioni tra ambienti usando registrazioni di app separate per slot di distribuzione distinti. Quando si esegue il test di nuovo codice, questa procedura consente di evitare problemi che influiscono sull'app di produzione.

## <a name="how-it-works"></a>Funzionamento

[Architettura delle funzionalità in Windows (distribuzione non contenitore)](#feature-architecture-on-windows-non-container-deployment)

[Architettura delle funzionalità in Linux e contenitori](#feature-architecture-on-linux-and-containers)

[Flusso di autenticazione](#authentication-flow)

[Comportamento di autorizzazione](#authorization-behavior)

[Attestazioni dell'utente e dell'applicazione](#user-and-application-claims)

[Archivio di token](#token-store)

[Registrazione e traccia](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Architettura delle funzionalità in Windows (distribuzione non contenitore)

Il modulo di autenticazione e autorizzazione viene eseguito nello stesso ambiente sandbox del codice dell'applicazione. Quando è abilitato, ogni richiesta HTTP in ingresso passa attraverso tale modulo prima di essere gestita dal codice dell'applicazione.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="Diagramma dell'architettura che mostra le richieste intercettate da un processo nella sandbox del sito che interagisce con i provider di identità prima di consentire il traffico verso il sito distribuito" lightbox="media/app-service-authentication-overview/architecture.png":::

Il modulo gestisce diversi aspetti dell'app:

- Autentica gli utenti con il provider specificato
- Convalida, archivia e aggiorna i token
- Gestisce la sessione autenticata
- Inserisce le informazioni relative all'identità nelle intestazioni delle richieste

Il modulo viene eseguito separatamente dal codice dell'applicazione e viene configurato usando le impostazioni dell'app. Non sono necessari SDK, linguaggi specifici o modifiche al codice dell'applicazione. 

#### <a name="feature-architecture-on-linux-and-containers"></a>Architettura delle funzionalità in Linux e contenitori

Il modulo di autenticazione e autorizzazione viene eseguito in un contenitore separato, isolato dal codice dell'applicazione. L'uso di ciò che è noto come [modello Ambassador](/azure/architecture/patterns/ambassador), interagisce con il traffico in ingresso per eseguire funzionalità simili a quelle di Windows. Poiché non viene eseguito in-process, non è possibile l'integrazione diretta con Framework di linguaggio specifici. Tuttavia, le informazioni rilevanti richieste dall'app vengono passate usando le intestazioni della richiesta, come illustrato di seguito.

#### <a name="authentication-flow"></a>Flusso di autenticazione

Il flusso di autenticazione è uguale per tutti i provider, ma varia a in base al fatto che si desideri o meno accedere con l'SDK del provider:

- Senza SDK del provider: l'applicazione delega l'accesso federato al servizio app. Ciò avviene, in genere, con le app basate su browser, che possono presentare all'utente la pagina di accesso del provider. Il codice server gestisce il processo di accesso, quindi si parla anche di _flusso diretto dal server_ oppure _flusso server_. Questo caso si applica alle app basate su browser. Si applica anche alle app native che consentono l'accesso agli utenti con l'SDK client di App per dispositivi mobili, perché l'SDK consente di aprire una visualizzazione Web per l'accesso degli utenti con l'autenticazione del servizio app.
- Con l'SDK del provider: l'applicazione consente l'accesso manuale degli utenti al provider e quindi invia il token di autenticazione al servizio app per la convalida. Ciò avviene, in genere, con le app senza browser, che non possono presentare all'utente la pagina di accesso del provider. Il codice dell'applicazione gestisce il processo di accesso, quindi si parla anche di _flusso diretto dal client_ oppure _flusso client_. Questo caso si applica alle API REST, a [Funzioni di Azure](../azure-functions/functions-overview.md) e ai client browser JavaScript, oltre che alle app basate su browser che richiedono una maggiore flessibilità nel processo di accesso. Si applica anche alle app per dispositivi mobili native che consentono l'accesso degli utenti con l'SDK del provider.

Le chiamate da un'app browser attendibile nel servizio app a un'altra API REST nel servizio app o in [funzioni di Azure](../azure-functions/functions-overview.md) possono essere autenticate usando il flusso indirizzato al server. Per altre informazioni, vedere [Personalizzare l'autenticazione e l'autorizzazione in Servizio app di Azure](app-service-authentication-how-to.md).

La tabella seguente illustra i passaggi del flusso di autenticazione.

| Passaggio | Senza SDK del provider | Con SDK del provider |
| - | - | - |
| 1. accesso utente | Reindirizza il client a `/.auth/login/<provider>`. | Il codice client consente l'accesso utente direttamente con l'SDK del provider e riceve un token di autenticazione. Per informazioni, vedere la documentazione del provider. |
| 2. post-autenticazione | Il provider reindirizza il client a `/.auth/login/<provider>/callback`. | Il codice client [inserisce il token del provider](app-service-authentication-how-to.md#validate-tokens-from-providers) in `/.auth/login/<provider>` per la convalida. |
| 3. stabilire una sessione autenticata | Il servizio app aggiunge il cookie autenticato alla risposta. | Il servizio app restituisce il proprio token di autenticazione al codice client. |
| 4. gestire il contenuto autenticato | Il client include il cookie di autenticazione nelle richieste successive (gestite automaticamente dal browser). | Il codice client presenta il token di autenticazione nell'intestazione `X-ZUMO-AUTH` (gestita automaticamente dagli SDK client per app per dispositivi mobili). |

Per i browser client, il servizio app può indirizzare automaticamente tutti gli utenti non autenticati a `/.auth/login/<provider>`. È anche possibile presentare agli utenti uno o più collegamenti a `/.auth/login/<provider>` per consentire di accedere all'app con il provider desiderato.

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>Comportamento di autorizzazione

Nel [portale di Azure](https://portal.azure.com)è possibile configurare l'autorizzazione del servizio app con diversi comportamenti quando la richiesta in ingresso non è autenticata.

![Screenshot che mostra l'elenco a discesa "azione da eseguire quando la richiesta non è autenticata"](media/app-service-authentication-overview/authorization-flow.png)

I titoli seguenti descrivono le opzioni.

**Consenti richieste anonime (nessuna azione)**

Questa opzione rinvia l'autorizzazione del traffico non autenticato al codice dell'applicazione. Per le richieste autenticate, il servizio app passa anche le informazioni di autenticazione nelle intestazioni HTTP.

Questa opzione offre maggiore flessibilità nella gestione delle richieste anonime. Ad esempio consente di [presentare più opzioni di accesso](app-service-authentication-how-to.md#use-multiple-sign-in-providers) agli utenti. Tuttavia richiede di scrivere codice.

**Consentire solo le richieste autenticate**

L'opzione è **accesso con \<provider>**. Il servizio app reindirizza tutte le richieste anonime a `/.auth/login/<provider>` per il provider scelto. Se la richiesta anonima proviene da un'app per dispositivi mobili nativa, verrà restituita la risposta `HTTP 401 Unauthorized`.

Con questa opzione non è necessario scrivere codice di autenticazione nell'app. È possibile gestire un livello di autorizzazione più specifico, ad esempio l'autorizzazione specifica dei ruoli, esaminando le attestazioni utente (vedere [Accedere alle attestazioni utente](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> La limitazione dell'accesso in questo modo si applica a tutte le chiamate all'app, che potrebbero non essere desiderate per le app che vogliono un home page disponibile pubblicamente, come in molte applicazioni a singola pagina.

> [!NOTE]
> Per impostazione predefinita, qualsiasi utente nel tenant di Azure AD può richiedere un token per l'applicazione da Azure AD. È possibile [configurare l'applicazione in Azure ad](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) se si vuole limitare l'accesso all'app a un set di utenti definito.


#### <a name="user-and-application-claims"></a>Attestazioni dell'utente e dell'applicazione

Per tutti i Framework di linguaggio, il servizio app rende disponibili al codice le attestazioni nel token in ingresso, sia che si tratti di un utente finale autenticato o di un'applicazione client, inserendole nelle intestazioni della richiesta. Per le app ASP.NET 4.6, il servizio app popola [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) con le attestazioni dell'utente autenticato, quindi è possibile seguire il modello di codice .NET standard, incluso l'attributo `[Authorize]`. Analogamente, per le app PHP, il servizio app popola la variabile `_SERVER['REMOTE_USER']`. Per le app Java, le attestazioni sono [accessibili dal servlet Tomcat](configure-language-java.md#authenticate-users-easy-auth).

Per [funzioni di Azure](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` non viene popolato per il codice .NET, ma è comunque possibile trovare le attestazioni utente nelle intestazioni della richiesta o ottenere l' `ClaimsPrincipal` oggetto dal contesto della richiesta o anche tramite un parametro di associazione. Per ulteriori informazioni, vedere [utilizzo delle identità client](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) .

Per altre informazioni, vedere [Accedere alle attestazioni utente](app-service-authentication-how-to.md#access-user-claims).

Al momento, ASP.NET Core attualmente non supporta il popolamento dell'utente corrente con la funzionalità di autenticazione/autorizzazione. Tuttavia, per colmare questo gap sono disponibili [componenti middleware open source](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) .

#### <a name="token-store"></a>Archivio di token

Il servizio app fornisce un archivio di token predefinito, ovvero un repository di token associati agli utenti delle app Web, delle API o delle app per dispositivi mobili native. Quando si abilita l'autenticazione con qualsiasi provider, questo archivio di token diventa immediatamente disponibile per l'app. Se il codice dell'applicazione deve accedere ai dati di questi provider per conto dell'utente, ad esempio:

- pubblicare sul diario di Facebook dell'utente autenticato
- leggere i dati aziendali dell'utente usando l'API Microsoft Graph

In genere è necessario scrivere codice per raccogliere, archiviare e aggiornare questi token nell'applicazione. Con l'archivio di token, è sufficiente [recuperare i token](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) quando sono necessari e [fare in modo che il servizio app li aggiorni](app-service-authentication-how-to.md#refresh-identity-provider-tokens) quando non sono più validi. 

I token ID, i token di accesso e i token di aggiornamento vengono memorizzati nella cache per la sessione autenticata ed è possibile accedervi solo dall'utente associato.  

Se non è necessario usare i token nell'app, è possibile disabilitare l'archivio di token nella pagina di **autenticazione/autorizzazione** dell'app.

#### <a name="logging-and-tracing"></a>Registrazione e traccia

Se si [abilita la registrazione delle applicazioni](troubleshoot-diagnostic-logs.md), le tracce di autenticazione e autorizzazione possono essere visualizzate direttamente nei file di log. Se viene visualizzato un errore di autenticazione non previsto, è possibile trovare facilmente tutti i dettagli esaminando i log delle applicazioni esistenti. Se si abilita la [traccia delle richieste non riuscite](troubleshoot-diagnostic-logs.md), è possibile vedere esattamente il ruolo che il modulo di autenticazione e autorizzazione ha avuto nella mancata riuscita della richiesta. Nei log di traccia cercare i riferimenti a un modulo denominato `EasyAuthModule_32/64`.

## <a name="more-resources"></a>Altre risorse

- [Procedura: configurare il servizio app o l'app funzioni di Azure per usare Azure AD account di accesso](configure-authentication-provider-aad.md)
- [Uso avanzato dell'autenticazione e dell'autorizzazione in Servizio app di Azure](app-service-authentication-how-to.md)

Esempi:
- [Esercitazione: Aggiungere l'autenticazione all'app Web in esecuzione nel Servizio app di Azure](scenario-secure-app-authentication-app-service.md)
- [Esercitazione: autenticare e autorizzare gli utenti end-to-end nel servizio app Azure (Windows o Linux)](tutorial-auth-aad.md)
- [Integrazione di .NET Core di Azure AppService EasyAuth (terze parti)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [Come usare l'autenticazione del servizio app Azure con .NET Core (terze parti)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)
