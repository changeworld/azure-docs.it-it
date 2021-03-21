---
title: Configurare app Web statiche di Azure
description: Informazioni su come configurare le route, applicare le regole di sicurezza e le impostazioni globali per le app Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: cshoe
ms.openlocfilehash: 324a8e75488d74fc6aa52e499b8dde616cd9beb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034048"
---
# <a name="configure-azure-static-web-apps"></a>Configurare app Web statiche di Azure

La configurazione per le app Web statiche di Azure è definita nella _staticwebapp.config.jssu_ file, che controlla le impostazioni seguenti:

- Routing
- Authentication
- Autorizzazione
- Regole di fallback
- Override della risposta HTTP
- Definizioni di intestazioni HTTP globali
- Tipi MIME personalizzati

## <a name="file-location"></a>Percorso del file

La posizione consigliata per la _staticwebapp.config.js_ in è la cartella impostata come `app_location` nel file del [flusso di lavoro](./github-actions-workflow.md). Tuttavia, il file può essere inserito in qualsiasi posizione all'interno della cartella del codice sorgente dell'applicazione.

Per informazioni dettagliate, vedere il file di [configurazione di esempio](#example-configuration-file) .

> [!IMPORTANT]
> Il [ _routes.jsnel_ file](./routes.md) viene ignorato se esiste una _staticwebapp.config.js_ .

## <a name="routes"></a>Route

Le regole di route consentono di definire il modello di URL che consentono l'accesso all'applicazione sul Web. Le route sono definite come una matrice di regole di routing. Per esempi di utilizzo, vedere il [file di configurazione di esempio](#example-configuration-file) .

- Le regole vengono eseguite nell'ordine in cui sono visualizzate nella matrice `routes`.
- La valutazione delle regole viene arrestata in corrispondenza delle prime regole di routing non concatenate.
- Si ha il controllo completo sui nomi di ruoli personalizzati.
  - Sono disponibili alcuni nomi di ruolo predefiniti, tra cui [`anonymous`](./authentication-authorization.md) e [`authenticated`](./authentication-authorization.md) .

Il routing riguarda in modo significativo la sovrapposizione con i concetti relativi all'autenticazione (identificazione dell'utente) e all'autorizzazione (assegnazione delle funzionalità all'utente). Leggere la guida all'[autenticazione e all'autorizzazione](authentication-authorization.md) insieme a questo articolo.

Il file predefinito per il contenuto statico è il file *index.html* .

## <a name="defining-routes"></a>Definizione delle route

Ogni regola è costituita da un modello di route, insieme a una o più proprietà della regola facoltative. Per esempi di utilizzo, vedere il [file di configurazione di esempio](#example-configuration-file) .

| Proprietà regola  | Obbligatoria | Valore predefinito | Comment                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Sì      | n/d          | Modello di route richiesto dal chiamante.<ul><li>I [caratteri jolly](#wildcards) sono supportati alla fine dei percorsi di route.<ul><li>Ad esempio, la route _admin/\*_ cerca le corrispondenze con qualsiasi route nel percorso _admin_.</ul></ul>|
| `rewrite`        | No       | n/d          | Definisce il file o il percorso restituito dalla richiesta.<ul><li>Si escludono a vicenda una `redirect` regola<li>Le regole di riscrittura non cambiano la posizione del browser.<li>I valori devono essere relativi alla radice dell'app</ul>  |
| `redirect`        | No       | n/d          | Definisce la destinazione di reindirizzamento del file o del percorso per una richiesta.<ul><li>Si escludono a vicenda una `rewrite` regola.<li>Le regole di reindirizzamento cambiano la posizione del browser.<li>Il codice di risposta predefinito è [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (Reindirizzamento temporaneo), ma è possibile eseguire l'override di con [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (Reindirizzamento permanente).</ul> |
| `allowedRoles` | No       | anonymous     | Definisce un elenco di nomi di ruoli necessari per accedere a una route. <ul><li>I caratteri validi includono `a-z`, `A-Z`, `0-9` e `_`.<li>Il ruolo predefinito, [`anonymous`](./authentication-authorization.md) , si applica a tutti gli utenti non autenticati<li>Il ruolo predefinito, [`authenticated`](./authentication-authorization.md) , si applica a qualsiasi utente connesso.<li>Gli utenti devono appartenere ad almeno un ruolo.<li>Per la corrispondenza dei ruoli viene usato l'operatore _OR_.<ul><li>Se un utente è incluso in uno dei ruoli elencati, viene concesso l'accesso.</ul><li>I singoli utenti sono associati ai ruoli tramite [inviti](authentication-authorization.md).</ul> |
| `headers`<a id="route-headers"></a> | No | n/d | Set di [intestazioni HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) aggiunte alla risposta. <ul><li>Le intestazioni specifiche della route [`globalHeaders`](#global-headers) vengono ignorate quando l'intestazione specifica della route corrisponde a quella dell'intestazione globale nella risposta.<li>Per rimuovere un'intestazione, impostare il valore su una stringa vuota.</ul> |
| `statusCode`   | No       | `200`, `301` o `302` per i reindirizzamenti | [Codice di stato http](https://developer.mozilla.org/docs/Web/HTTP/Status) della risposta. |
| `methods` | No | Tutti i metodi | Elenco di metodi di richiesta che corrispondono a una route. I metodi disponibili includono: `GET` ,, `HEAD` `POST` , `PUT` , `DELETE` , `CONNECT` , `OPTIONS` , `TRACE` e `PATCH` . |

Ogni proprietà ha uno scopo specifico nella pipeline di richiesta/risposta.

| Scopo | Proprietà |
|---|---|
| Corrispondenze Route | `route`, `methods` |
| Autorizza dopo la corrispondenza di una route | `allowedRoles` |
| Processo dopo la corrispondenza e l'autorizzazione di una regola | `rewrite` (modifica la richiesta) <br><br>`redirect`, `headers` , `statusCode` (modifica la risposta) |

## <a name="securing-routes-with-roles"></a>Protezione delle route con i ruoli

Le route sono protette mediante l'aggiunta di uno o più nomi di ruoli in una matrice di regole `allowedRoles` e gli utenti sono associati ai ruoli personalizzati tramite gli [inviti](./authentication-authorization.md). Per esempi di utilizzo, vedere il [file di configurazione di esempio](#example-configuration-file) .

Per impostazione predefinita, ogni utente appartiene al ruolo `anonymous` predefinito e tutti gli utenti connessi sono membri del ruolo `authenticated`.

Ad esempio, per limitare una route solo agli utenti autenticati, aggiungere il ruolo `authenticated` predefinito alla matrice `allowedRoles`.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

È possibile creare nuovi ruoli in base alle esigenze nella matrice `allowedRoles`. Per limitare una route solo agli amministratori, è possibile definire il proprio ruolo denominato `administrator` , nella `allowedRoles` matrice.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Si ha il controllo completo sui nomi dei ruoli. non è presente alcun elenco a cui devono attenersi i ruoli.
- I singoli utenti sono associati ai ruoli tramite [inviti](authentication-authorization.md).

## <a name="wildcards"></a>Caratteri jolly

Le regole con caratteri jolly corrispondono a tutte le richieste in un modello di route, sono supportate solo alla fine di un percorso e possono essere filtrate per estensione di file. Per esempi di utilizzo, vedere il [file di configurazione di esempio](#example-configuration-file) .

Ad esempio, per implementare le route per un'applicazione calendario, è possibile riscrivere tutti gli URL che rientrano nella route del _Calendario_ per gestire un singolo file.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

Il file _calendar.html_ può quindi usare il routing lato client per restituire una visualizzazione diversa per le variazioni degli URL, ad esempio `/calendar/january/1`, `/calendar/2020` e `/calendar/overview`.

È possibile filtrare le corrispondenze con caratteri jolly per estensione di file. Ad esempio, se si desidera aggiungere una regola che corrisponde solo ai file HTML in un percorso specificato, è possibile creare la regola seguente:

```json
{
  "route": "/articles/*.html",
  "headers" : {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Per filtrare in più estensioni di file, includere le opzioni tra parentesi graffe, come illustrato nell'esempio seguente:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

I casi di utilizzo comuni per le route con caratteri jolly includono:

- Servire un file specifico per un intero modello di percorso
- Mapping di metodi HTTP diversi a un intero modello di percorso
- Applicazione delle regole di autenticazione e autorizzazione
- Implementare regole di memorizzazione nella cache specializzate

## <a name="fallback-routes"></a>Route di fallback

Le applicazioni a pagina singola spesso si basano sul routing lato client. Queste regole di routing lato client aggiornano la posizione della finestra del browser senza inviare di nuovo richieste al server. Se si aggiorna la pagina o si passa direttamente agli URL generati dalle regole di routing lato client, è necessaria una route di fallback sul lato server per gestire la pagina HTML appropriata (che in genere è la _index.html_ per l'app sul lato client).

È possibile configurare l'app per l'uso di regole che implementano una route di fallback, come illustrato nell'esempio seguente che usa un carattere jolly percorso con filtro file:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

La struttura del file di esempio seguente illustra i risultati possibili con questa regola.

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

| Richieste a... | restituisce... | con lo stato... |
| --- | --- | --- |
| *su* | File */index.html* | `200` |
| *logo.png/images/* | Il file di immagine  | `200` |
| */images/icon.svg* | Il file */index.html* , perché l'estensione del file *SVG* non è elencata nel `/images/*.{png,jpg,gif}` filtro   | `200` |
| *unknown.png/images/* | Errore file non trovato  | `404` |
| */css/unknown.css* | Errore file non trovato  | `404` |
| */css/global.css* | Il file del foglio di stile | `200` |
| Qualsiasi altro file all'esterno delle cartelle */images* o */CSS* | File */index.html* | `200` |

## <a name="global-headers"></a>Intestazioni globali

La `globalHeaders` sezione fornisce un set di [intestazioni HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) applicate a ogni risposta, a meno che non venga sottoposto a override da una regola di [intestazione della route](#route-headers) , in caso contrario viene restituita l'Unione di entrambe le intestazioni dalla Route e delle intestazioni globali.

Per esempi di utilizzo, vedere il [file di configurazione di esempio](#example-configuration-file) .

Per rimuovere un'intestazione, impostare il valore su una stringa vuota ( `""` ).

Alcuni casi d'uso comuni per le intestazioni globali includono:

- Regole di memorizzazione nella cache personalizzate
- Applicazione di criteri di sicurezza
- Impostazioni di codifica

## <a name="response-overrides"></a>Override della risposta

La `responseOverrides` sezione fornisce la possibilità di definire una risposta personalizzata quando il server restituisce in caso contrario un codice di errore. Per esempi di utilizzo, vedere il [file di configurazione di esempio](#example-configuration-file) .

Per eseguire l'override sono disponibili i codici HTTP seguenti:

| Codice di stato | Significato | Possibile causa |
| --- | --- | --- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Richiesta non valida | Collegamento invito non valido |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Non autorizzata | Richieste a pagine con restrizioni durante la non autenticazione |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Accesso negato |<ul><li>L'utente ha effettuato l'accesso ma non ha i ruoli necessari per visualizzare la pagina.<li>L'utente è connesso, ma il runtime non è in grado di ottenere i dettagli dell'utente dalle attestazioni di identità.<li>Ci sono troppi utenti connessi al sito con ruoli personalizzati, quindi il runtime non può accedere all'utente.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Non trovato | Impossibile trovare il file |

Nella configurazione di esempio seguente viene illustrato come eseguire l'override di un codice di errore.

```json
{
    "responseOverrides": {
        "400" : {
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
            "methods": [ "GET" ],
            "allowedRoles": ["registeredusers"]
        },
        {
            "route": "/api/*",
            "methods": [ "PUT", "POST", "PATCH", "DELETE" ],
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
            "statusCode": 404,
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
        "400" : {
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
    },
}
```

In base alla configurazione precedente, esaminare gli scenari seguenti.

| Richieste a... | Risultati in... |
| --- | --- |
| _/profile_ | Agli utenti autenticati viene restituito il file _/profile/index.html_. Gli utenti non autenticati vengono reindirizzati a _/login_. |
| _/admin_ | Gli utenti autenticati con il ruolo di _amministratore_ vengono serviti il file _/admin/index.html_ . Agli utenti autenticati che non fanno parte del ruolo _Administrator_ viene servito un `403` errore <sup>1</sup>. Gli utenti non autenticati vengono reindirizzati a _/login_. |
| _/logo.png_ | Consente di usare l'immagine con una regola della cache personalizzata in cui la validità massima è di poco più di 182 giorni (15.770.000 secondi). |
| _/api/admin_ | `GET` le richieste provenienti da utenti autenticati nel ruolo _RegisteredUsers_ vengono inviate all'API. Agli utenti autenticati che non fanno parte del ruolo _RegisteredUsers_ e agli utenti non autenticati viene servito un `401` errore.<br/><br/>`POST`le `PUT` richieste,, `PATCH` e `DELETE` degli utenti autenticati con il ruolo di _amministratore_ vengono inviate all'API. Agli utenti autenticati non inclusi nel ruolo di _amministratore_ e agli utenti non autenticati viene servito un `401` errore. |
| _/customers/contoso_ | Gli utenti autenticati che appartengono ai ruoli di _amministratore_ o dei _clienti di \_ Contoso_ sono serviti al file _/Customers/contoso/index.html_ . Agli utenti autenticati non inclusi nei ruoli di _amministratore_ o dei _clienti \_ Contoso_ viene servito un `403` errore <sup>1</sup>. Gli utenti non autenticati vengono reindirizzati a _/login_. |
| _/login_ | Agli utenti non autenticati viene richiesto di eseguire l'autenticazione con GitHub. |
| _/.auth/login/twitter_ | Poiché l'autorizzazione con Twitter è disabilitata dalla regola di route, `404` viene restituito Error, che esegue il fallback a serving _/index.html_ con un `200` codice di stato. |
| _/logout_ | Gli utenti vengono disconnessi da qualsiasi provider di autenticazione. |
| _/calendar/2021/01_ | Al browser viene restituito il file _/calendar.html_. |
| _/specials_ | Il browser viene reindirizzato in modo permanente a _/Deals_. |
| _/data.js_ | File servito con il `text/json` tipo MIME. |
| _/About_ o qualsiasi cartella corrispondente ai modelli di routing lato client | Il file _/index.html_ viene servito con un `200` codice di stato. |
| Un file non esistente nella cartella _/images/_ | `404`Errore. |

<sup>1</sup> è possibile specificare una pagina di errore personalizzata usando una [regola di override della risposta](#response-overrides).

## <a name="restrictions"></a>Restrizioni

Per il _staticwebapps.config.jssu_ file sono disponibili le restrizioni seguenti.

- Le dimensioni massime del file sono pari a 100 KB
- Massimo 50 ruoli distinti

Vedere l' [articolo sulle quote](quotas.md) per limitazioni e limitazioni generali.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare l'autenticazione e l'autorizzazione](authentication-authorization.md)
