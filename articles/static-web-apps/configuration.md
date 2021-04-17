---
title: Configurare App Web statiche di Azure
description: Informazioni su come configurare le route, applicare regole di sicurezza e impostazioni globali per App Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 9494bcc9941491bbb82c6a948dce720cb9e51424
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502284"
---
# <a name="configure-azure-static-web-apps"></a>Configurare App Web statiche di Azure

La configurazione App Web statiche di Azure è definita nelstaticwebapp.config.js _file,_ che controlla le impostazioni seguenti:

- Routing
- Autenticazione
- Autorizzazione
- Regole di fallback
- Override della risposta HTTP
- Definizioni di intestazione HTTP globali
- Tipi MIME personalizzati

## <a name="file-location"></a>Percorso del file

Il percorso consigliato per il _staticwebapp.config.js_ è nella cartella impostata come `app_location` nel file del flusso di [lavoro](./github-actions-workflow.md). Tuttavia, il file può essere inserito in qualsiasi percorso all'interno della cartella del codice sorgente dell'applicazione.

Per informazioni [dettagliate, vedere](#example-configuration-file) il file di configurazione di esempio.

> [!IMPORTANT]
> Il [ _routes.jssul_ file](./routes.md) viene ignorato se esistestaticwebapp.config.js _esistente._

## <a name="routes"></a>Route

Le regole di route consentono di definire il modello di URL che consentono l'accesso all'applicazione sul Web. Le route sono definite come una matrice di regole di routing. Per esempi [di utilizzo, vedere il file](#example-configuration-file) di configurazione di esempio.

- Le regole vengono definite nella `routes` matrice, anche se è presente una sola route.
- Le regole vengono eseguite nell'ordine in cui sono visualizzate nella matrice `routes`.
- La valutazione delle regole si arresta alla prima corrispondenza: le regole di routing non vengono concatenate tra loro.
- Si ha il controllo completo sui nomi dei ruoli personalizzati.
  - Sono disponibili alcuni nomi di ruolo predefiniti che includono [`anonymous`](./authentication-authorization.md) e [`authenticated`](./authentication-authorization.md) .

Il routing riguarda in modo significativo la sovrapposizione con i concetti di autenticazione (identificazione dell'utente) e autorizzazione (assegnazione di capacità all'utente). Leggere la guida all'[autenticazione e all'autorizzazione](authentication-authorization.md) insieme a questo articolo.

Il file predefinito per il contenuto statico è il file _index.html._

## <a name="defining-routes"></a>Definizione delle route

Ogni regola è costituita da un modello di route, insieme a una o più proprietà della regola facoltative. Le regole di route sono definite nella `routes` matrice. Per esempi [di utilizzo, vedere il file](#example-configuration-file) di configurazione di esempio.

| Proprietà regola                       | Obbligatoria | Valore predefinito                        | Comment                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------- | -------- | ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `route`                             | Sì      | n/d                                  | Modello di route richiesto dal chiamante.<ul><li>I [caratteri jolly](#wildcards) sono supportati alla fine dei percorsi di route.<ul><li>Ad esempio, la route _admin/\*_ cerca le corrispondenze con qualsiasi route nel percorso _admin_.</ul></ul>                                                                                                                                                                                                                                                                                                                                                                                                        |
| `rewrite`                           | No       | n/d                                  | Definisce il file o il percorso restituito dalla richiesta.<ul><li>Si escludono a vicenda a una `redirect` regola<li>Le regole di riscrittura non modificano il percorso del browser.<li>I valori devono essere relativi alla radice dell'app</ul>                                                                                                                                                                                                                                                                                                                                                                                                      |
| `redirect`                          | No       | n/d                                  | Definisce la destinazione di reindirizzamento del file o del percorso per una richiesta.<ul><li>Si escludono a vicenda a una `rewrite` regola.<li>Le regole di reindirizzamento modificano il percorso del browser.<li>Il codice di risposta predefinito è [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) un (reindirizzamento temporaneo), ma è possibile eseguire l'override con [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) un (reindirizzamento permanente).</ul>                                                                                                                                                                                                              |
| `allowedRoles`                      | No       | anonymous                            | Definisce un elenco di nomi di ruolo necessari per accedere a una route. <ul><li>I caratteri validi includono `a-z`, `A-Z`, `0-9` e `_`.<li>Il ruolo predefinito, [`anonymous`](./authentication-authorization.md) , si applica a tutti gli utenti non autenticati<li>Il ruolo predefinito, [`authenticated`](./authentication-authorization.md) , si applica a qualsiasi utente connesso.<li>Gli utenti devono appartenere ad almeno un ruolo.<li>Per la corrispondenza dei ruoli viene usato l'operatore _OR_.<ul><li>Se un utente è incluso in uno dei ruoli elencati, viene concesso l'accesso.</ul><li>I singoli utenti sono associati ai ruoli tramite [inviti](authentication-authorization.md).</ul> |
| `headers`<a id="route-headers"></a> | No       | n/d                                  | Set di [intestazioni HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) aggiunte alla risposta. <ul><li>Le intestazioni specifiche della route eseguono l'override quando l'intestazione specifica della route corrisponde [`globalHeaders`](#global-headers) all'intestazione globale nella risposta.<li>Per rimuovere un'intestazione, impostare il valore su una stringa vuota.</ul>                                                                                                                                                                                                                                                                                          |
| `statusCode`                        | No       | `200`, `301` o per i `302` reindirizzamenti | Codice [di stato HTTP](https://developer.mozilla.org/docs/Web/HTTP/Status) della risposta.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `methods`                           | No       | Tutti i metodi                          | Elenco di metodi di richiesta che corrispondono a una route. I metodi disponibili includono: `GET` , , , , , , , `HEAD` e `POST` `PUT` `DELETE` `CONNECT` `OPTIONS` `TRACE` `PATCH` .                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

Ogni proprietà ha uno scopo specifico nella pipeline di richiesta/risposta.

| Scopo                                        | Proprietà                                                                                   |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Trovare le corrispondenze delle route                                   | `route`, `methods`                                                                           |
| Autorizzare dopo la corrispondenza di una route             | `allowedRoles`                                                                               |
| Elaborare dopo la corrispondenza e l'autorizzazione di una regola | `rewrite` (modifica la richiesta) <br><br>`redirect`, `headers` , `statusCode` (modifica la risposta) |

## <a name="securing-routes-with-roles"></a>Protezione delle route con i ruoli

Le route vengono protette aggiungendo uno o più nomi di ruolo nella matrice di una regola e gli utenti sono associati a ruoli personalizzati `allowedRoles` tramite [inviti](./authentication-authorization.md). Per esempi [di utilizzo, vedere il file](#example-configuration-file) di configurazione di esempio.

Per impostazione predefinita, ogni utente appartiene al ruolo `anonymous` predefinito e tutti gli utenti connessi sono membri del ruolo `authenticated`.

Ad esempio, per limitare una route solo agli utenti autenticati, aggiungere il ruolo `authenticated` predefinito alla matrice `allowedRoles`.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

È possibile creare nuovi ruoli in base alle esigenze nella matrice `allowedRoles`. Per limitare una route solo agli amministratori, è possibile definire il proprio ruolo denominato `administrator` nella `allowedRoles` matrice .

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Si ha il controllo completo sui nomi dei ruoli. non è presente alcun elenco a cui i ruoli devono essere conformi.
- I singoli utenti sono associati ai ruoli tramite [inviti](authentication-authorization.md).

## <a name="wildcards"></a>Caratteri jolly

Le regole con caratteri jolly corrispondono a tutte le richieste in un modello di route, sono supportate solo alla fine di un percorso e possono essere filtrate in base all'estensione di file. Per esempi [di utilizzo, vedere il file](#example-configuration-file) di configurazione di esempio.

Ad esempio, per implementare route per un'applicazione di calendario, è possibile riscrivere tutti gli URL che rientrano nella _route_ del calendario per gestire un singolo file.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

Il file _calendar.html_ può quindi usare il routing lato client per restituire una visualizzazione diversa per le variazioni degli URL, ad esempio `/calendar/january/1`, `/calendar/2020` e `/calendar/overview`.

È possibile filtrare le corrispondenze con caratteri jolly in base all'estensione di file. Ad esempio, se si vuole aggiungere una regola che corrisponda solo ai file HTML in un determinato percorso, è possibile creare la regola seguente:

```json
{
  "route": "/articles/*.html",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Per filtrare in base a più estensioni di file, includere le opzioni tra parentesi graffe, come illustrato in questo esempio:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

I casi d'uso comuni per le route con caratteri jolly includono:

- Gestione di un file specifico per un modello di percorso intero
- Mapping di metodi HTTP diversi a un modello di percorso intero
- Applicazione delle regole di autenticazione e autorizzazione
- Implementare regole di memorizzazione nella cache specializzate

## <a name="fallback-routes"></a>Route di fallback

Le applicazioni a pagina singola si basano spesso sul routing lato client. Queste regole di routing lato client aggiornano la posizione della finestra del browser senza inviare di nuovo richieste al server. Se si aggiorna la pagina o si passa direttamente agli URL generati dalle regole di routing sul lato client, è necessaria una route di fallback sul lato server per gestire la pagina HTML appropriata,che in genere è _ilindex.html_ per l'app sul lato client.

È possibile configurare l'app per l'uso di regole che implementano una route di fallback, come illustrato nell'esempio seguente che usa un carattere jolly di percorso con filtro di file:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

La struttura di file di esempio seguente consente di ottenere i risultati seguenti con questa regola.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| Richieste a...                                         | Restituisce...                                                                                                    | con lo stato ... |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- | ------------------ |
| _/about/_                                              | Il _file /index.html_                                                                                        | `200`              |
| _/images/logo.png_                                     | File di immagine                                                                                                | `200`              |
| _/images/icon.svg_                                     | Il _file /index.html:_ poiché l'estensione del file _svg_ non è elencata nel `/images/*.{png,jpg,gif}` filtro | `200`              |
| _/images/unknown.png_                                  | Errore file non trovato                                                                                          | `404`              |
| _/css/unknown.css_                                     | Errore file non trovato                                                                                          | `404`              |
| _/css/global.css_                                      | File del foglio di stile                                                                                           | `200`              |
| Qualsiasi altro file all'esterno _delle cartelle /images_ _o /css_ | Il _file /index.html_                                                                                        | `200`              |

## <a name="global-headers"></a>Intestazioni globali

La sezione fornisce un set di intestazioni HTTP applicate a ogni risposta, a meno che non venga sottoposta a override da una regola di intestazione di route. In caso contrario, viene restituita l'unione di entrambe le intestazioni della route e `globalHeaders` delle intestazioni globali. [](https://developer.mozilla.org/docs/Web/HTTP/Headers) [](#route-headers)

Per esempi [di utilizzo, vedere il file](#example-configuration-file) di configurazione di esempio.

Per rimuovere un'intestazione, impostare il valore su una stringa vuota ( `""` ).

Alcuni casi d'uso comuni per le intestazioni globali includono:

- Regole di memorizzazione nella cache personalizzate
- Applicazione dei criteri di sicurezza
- Impostazioni di codifica

## <a name="response-overrides"></a>Sostituzioni delle risposte

La `responseOverrides` sezione offre l'opportunità di definire una risposta personalizzata quando il server restituirebbe in caso contrario un codice di errore. Per esempi [di utilizzo, vedere il file](#example-configuration-file) di configurazione di esempio.

Per eseguire l'override sono disponibili i codici HTTP seguenti:

| Codice di stato                                                   | Significato      | Possibile causa                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Richiesta non valida  | Collegamento all'invito non valido                                                                                                                                                                                                                                                                            |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Non autorizzata | Richiesta a pagine con restrizioni senza autenticazione                                                                                                                                                                                                                                                  |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Accesso negato    | <ul><li>L'utente è connesso ma non ha i ruoli necessari per visualizzare la pagina.<li>L'utente è connesso ma il runtime non può ottenere i dettagli dell'utente dalle relative attestazioni di identità.<li>Sono presenti troppi utenti connessi al sito con ruoli personalizzati, pertanto il runtime non può accedere all'utente.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Non trovato    | Impossibile trovare il file                                                                                                                                                                                                                                                                                     |

La configurazione di esempio seguente illustra come eseguire l'override di un codice di errore.

```json
{
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html",
      "statusCode": 200
    },
    "401": {
      "statusCode": 302,
      "redirect": "/login"
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html",
      "statusCode": 200
    },
    "404": {
      "rewrite": "/custom-404.html",
      "statusCode": 200
    }
  }
}
```

## <a name="example-configuration-file"></a>File di configurazione di esempio

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/images/*",
      "headers": {
        "cache-control": "must-revalidate, max-age=15770000"
      }
    },
    {
      "route": "/api/*",
      "methods": ["GET"],
      "allowedRoles": ["registeredusers"]
    },
    {
      "route": "/api/*",
      "methods": ["PUT", "POST", "PATCH", "DELETE"],
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/*",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "rewrite": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": 404
    },
    {
      "route": "/logout",
      "redirect": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "rewrite": "/calendar.html"
    },
    {
      "route": "/specials",
      "redirect": "/deals",
      "statusCode": 301
    }
  ],
  "navigationFallback": {
    "rewrite": "index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  },
  "responseOverrides": {
    "400": {
      "rewrite": "/invalid-invitation-error.html"
    },
    "401": {
      "redirect": "/login",
      "statusCode": 302
    },
    "403": {
      "rewrite": "/custom-forbidden-page.html"
    },
    "404": {
      "rewrite": "/404.html"
    }
  },
  "globalHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
    ".json": "text/json"
  }
}
```

In base alla configurazione precedente, esaminare gli scenari seguenti.

| Richieste a...                                                    | risultati in...                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _/profile_                                                        | Agli utenti autenticati viene restituito il file _/profile/index.html_. Gli utenti non autenticati vengono reindirizzati a _/login._                                                                                                                                                                                                                                                                                                                              |
| _/admin/_                                                         | Agli utenti autenticati nel _ruolo di_ amministratore viene servito il file _/admin/index.html._ Agli utenti autenticati non nel _ruolo di_ amministratore viene restituito un `403` errore <sup>1.</sup> Gli utenti non autenticati vengono reindirizzati a _/login._                                                                                                                                                                                                          |
| _/logo.png_                                                       | Serve l'immagine con una regola della cache personalizzata in cui la validità massima è di poco più di 182 giorni (15.770.000 secondi).                                                                                                                                                                                                                                                                                                                                   |
| _/api/admin_                                                      | `GET` Le richieste degli utenti autenticati nel _ruolo registeredusers_ vengono inviate all'API. Agli utenti autenticati non nel _ruolo registeredusers_ e agli utenti non autenticati viene restituito un `401` errore.<br/><br/>`POST`Le `PUT` richieste , , e provenienti da utenti `PATCH` `DELETE` autenticati nel ruolo _di_ amministratore vengono inviate all'API. Agli utenti autenticati non nel _ruolo di_ amministratore e agli utenti non autenticati viene restituito un `401` errore. |
| _/customers/contoso_                                              | Agli utenti autenticati che appartengono ai ruoli _di_ amministratore _o customers_contoso_ viene servito il file _/customers/contoso/index.html._ Agli utenti autenticati non presenti nei ruoli _di_ amministratore _o customers_contoso_ viene restituito un `403` errore <sup>1.</sup> Gli utenti non autenticati vengono reindirizzati a _/login._                                                                                                                            |
| _/login_                                                          | Agli utenti non autenticati viene richiesto di eseguire l'autenticazione con GitHub.                                                                                                                                                                                                                                                                                                                                                                             |
| _/.auth/login/twitter_                                            | Poiché l'autorizzazione con Twitter è disabilitata dalla regola di route, viene restituito un errore, che viene restituito al servizio `404` _/index.html_ con un `200` codice di stato.                                                                                                                                                                                                                                                                                     |
| _/logout_                                                         | Gli utenti vengono disconnessi da qualsiasi provider di autenticazione.                                                                                                                                                                                                                                                                                                                                                                                          |
| _/calendar/2021/01_                                               | Al browser viene restituito il file _/calendar.html_.                                                                                                                                                                                                                                                                                                                                                                                              |
| _/specials_                                                       | Il browser viene reindirizzato in modo permanente a _/deals._                                                                                                                                                                                                                                                                                                                                                                                            |
| _/data.jssu_                                                      | File servito con il `text/json` tipo MIME.                                                                                                                                                                                                                                                                                                                                                                                               |
| _/about_ o qualsiasi cartella corrispondente ai modelli di routing lato client | Il _file /index.html_ viene servito con un codice di `200` stato.                                                                                                                                                                                                                                                                                                                                                                                    |
| Un file inesistente nella _cartella /images/_                     | `404`Errore.                                                                                                                                                                                                                                                                                                                                                                                                                                |

<sup>1</sup> È possibile specificare una pagina di errore personalizzata usando una regola di [override della risposta](#response-overrides).

## <a name="restrictions"></a>Restrizioni

Per ilstaticwebapps.config.js _file esistono le restrizioni_ seguenti.

- Le dimensioni massime del file sono 100 KB
- Massimo 50 ruoli distinti

Vedere [l'articolo Quote](quotas.md) per le restrizioni e le limitazioni generali.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare l'autenticazione e l'autorizzazione](authentication-authorization.md)
