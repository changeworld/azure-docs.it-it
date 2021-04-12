---
title: Configurare le azioni del set di regole standard/Premium di Azure front door
description: Questo articolo fornisce un elenco delle varie azioni che è possibile eseguire con il set di regole di front-end di Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e4698a1c1576d15042dd050e0123b83dba39a3e3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064752"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Azioni di set di regole standard/Premium (anteprima) di Azure front door

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Un [set](concept-rule-set.md) di regole standard/Premium di Azure front door è costituito da regole con una combinazione di condizioni e azioni di corrispondenza. Questo articolo fornisce una descrizione dettagliata delle azioni che è possibile usare nel set di regole standard/Premium di Azure front door. L'azione definisce il comportamento che viene applicato a un tipo di richiesta identificato da una o più condizioni di corrispondenza. In un set di regole di Azure front door (standard/Premium), una regola può contenere fino a cinque azioni.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le azioni seguenti sono disponibili per l'uso nel set di regole di front-end di Azure.  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a> Scadenza cache

Usare l'azione di **scadenza della cache** per sovrascrivere il valore TTL (time to Live) dell'endpoint per le richieste specificate dalle condizioni di corrispondenza delle regole.

> [!NOTE]
> Le origini possono specificare di non memorizzare nella cache risposte specifiche usando l' `Cache-Control` intestazione con il valore `no-cache` , `private` o `no-store` . In questi casi, lo sportello anteriore non memorizza mai nella cache il contenuto e questa azione non avrà alcun effetto.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-------|------------------|
| Comportamento cache | <ul><li>**Ignora cache:** Il contenuto non deve essere memorizzato nella cache. Nei modelli ARM impostare la `cacheBehavior` proprietà su `BypassCache` .</li><li>**Sostituzione:** Il valore TTL restituito dall'origine viene sovrascritto con il valore specificato nell'azione. Questo comportamento verrà applicato solo se la risposta è memorizzabile nella cache. Nei modelli ARM impostare la `cacheBehavior` proprietà su `Override` .</li><li>**Imposta se mancante:** Se dall'origine non viene restituito alcun valore TTL, la regola imposta la durata (TTL) sul valore specificato nell'azione. Questo comportamento verrà applicato solo se la risposta è memorizzabile nella cache. Nei modelli ARM impostare la `cacheBehavior` proprietà su `SetIfMissing` .</li></ul> |
| Durata cache | Quando il _comportamento della cache_ è impostato su `Override` o `Set if missing` , questi campi devono specificare la durata della cache da usare. La durata massima è di 366 giorni.<ul><li>Nella portale di Azure: specificare i giorni, le ore, i minuti e i secondi.</li><li>Nei modelli ARM: specificare la durata nel formato `d.hh:mm:ss` . |

### <a name="example"></a>Esempio

In questo esempio si sostituisce la scadenza della cache a 6 ore, per le richieste corrispondenti che non specificano già una durata della cache.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="Screenshot del portale che mostra l'azione di scadenza della cache.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a> Stringa di query della chiave della cache

Usare l'azione **stringa di query della chiave di cache** per modificare la chiave della cache in base alle stringhe di query. La chiave della cache è il modo in cui l'anta anteriore identifica le richieste univoche da memorizzare nella cache.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-------|------------------|
| Comportamento | <ul><li>**Includi:** Le stringhe di query specificate nei parametri vengono incluse quando viene generata la chiave di cache. Nei modelli ARM impostare la `queryStringBehavior` proprietà su `Include` .</li><li>**Memorizza nella cache tutti gli URL univoci:** Ogni URL univoco ha una propria chiave di cache. Nei modelli ARM usare il `queryStringBehavior` di `IncludeAll` .</li><li>**Escludi:** Le stringhe di query specificate nei parametri vengono escluse quando viene generata la chiave di cache. Nei modelli ARM impostare la `queryStringBehavior` proprietà su `Exclude` .</li><li>**Ignora stringhe di query:** Le stringhe di query non vengono considerate quando viene generata la chiave di cache. Nei modelli ARM impostare la `queryStringBehavior` proprietà su `ExcludeAll` .</li></ul>  |
| Parametri | Elenco di nomi di parametri di stringa di query separati da virgole. |

### <a name="example"></a>Esempio

In questo esempio viene modificata la chiave della cache in modo da includere un parametro di stringa di query denominato `customerId` .

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="Screenshot del portale che mostra l'azione della stringa di query della chiave della cache.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a> Modificare l'intestazione della richiesta

Usare l'azione **modifica intestazione richiesta** per modificare le intestazioni nella richiesta quando viene inviata all'origine.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-------|------------------|
| Operatore | <ul><li>**Accoda:** L'intestazione specificata viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore viene aggiunto al valore dell'intestazione esistente usando la concatenazione di stringhe. Non vengono aggiunti delimitatori. Nei modelli ARM usare il `headerAction` di `Append` .</li><li>**Sovrascrivi:** L'intestazione specificata viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore specificato sovrascrive quello esistente. Nei modelli ARM usare il `headerAction` di `Overwrite` .</li><li>**Elimina:** Se è presente l'intestazione specificata nella regola, l'intestazione viene eliminata dalla richiesta. Nei modelli ARM usare il `headerAction` di `Delete` .</li></ul> |
| Nome intestazione | Nome dell'intestazione da modificare. |
| Valore intestazione | Valore da accodare o sovrascrivere. |

### <a name="example"></a>Esempio

In questo esempio il valore viene accodato all' `AdditionalValue` `MyRequestHeader` intestazione della richiesta. Se l'origine imposta l'intestazione della risposta su un valore di `ValueSetByClient` , dopo l'applicazione di questa azione l'intestazione della richiesta avrà il valore `ValueSetByClientAdditionalValue` .

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="Screenshot del portale che mostra l'azione modifica intestazione richiesta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a> Modifica intestazione risposta

Usare l'azione **modifica intestazione risposta** per modificare le intestazioni presenti nelle risposte prima che vengano restituite ai client.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|-------|------------------|
| Operatore | <ul><li>**Accoda:** L'intestazione specificata viene aggiunta alla risposta con il valore specificato. Se l'intestazione è già presente, il valore viene aggiunto al valore dell'intestazione esistente usando la concatenazione di stringhe. Non vengono aggiunti delimitatori. Nei modelli ARM usare il `headerAction` di `Append` .</li><li>**Sovrascrivi:** L'intestazione specificata viene aggiunta alla risposta con il valore specificato. Se l'intestazione è già presente, il valore specificato sovrascrive quello esistente. Nei modelli ARM usare il `headerAction` di `Overwrite` .</li><li>**Elimina:** Se è presente l'intestazione specificata nella regola, l'intestazione viene eliminata dalla risposta.  Nei modelli ARM usare il `headerAction` di `Delete` .</li></ul> |
| Nome intestazione | Nome dell'intestazione da modificare. |
| Valore intestazione | Valore da accodare o sovrascrivere. |

### <a name="example"></a>Esempio

In questo esempio si elimina l'intestazione con il nome `X-Powered-By` dalle risposte prima che vengano restituite al client.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="Screenshot del portale che mostra l'azione modifica intestazione risposta.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a> Reindirizzamento URL

Usare l'azione di **Reindirizzamento URL** per reindirizzare i client a un nuovo URL. Ai client viene inviata una risposta di reindirizzamento dalla porta anteriore.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|----------|------------------|
| Tipo di Reindirizzamento | Tipo di risposta da restituire al richiedente. <ul><li>Nel portale di Azure: trovato (302), spostato (301), Reindirizzamento temporaneo (307), Reindirizzamento permanente (308).</li><li>Nei modelli ARM: `Found` , `Moved` , `TemporaryRedirect` , `PermanentRedirect`</li></ul> |
| Protocollo di reindirizzamento | <ul><li>Nella portale di Azure: `Match Request` , `HTTP` , `HTTPS`</li><li>Nei modelli ARM: `MatchRequest` , `Http` , `Https`</li></ul> |
| Host di destinazione | Nome host a cui si desidera reindirizzare la richiesta. Lasciare vuoto questo campo per mantenere l'host in ingresso. |
| Percorso di destinazione | Percorso da usare nel reindirizzamento. Includere il carattere iniziali `/` . Lasciare vuoto questo campo per mantenere il percorso in ingresso. |
| Stringa di query | Stringa di query utilizzata nel reindirizzamento. Non includere il carattere iniziali `?` . Lasciare vuoto questo campo per mantenere la stringa di query in ingresso. |
| Frammento di destinazione | Frammento da utilizzare nel reindirizzamento. Lasciare vuoto questo campo per mantenere il frammento in ingresso. |

### <a name="example"></a>Esempio

In questo esempio, la richiesta viene reindirizzata a `https://contoso.com/exampleredirection?clientIp={client_ip}` , conservando il frammento. Viene usato un reindirizzamento temporaneo HTTP (307). L'indirizzo IP del client viene usato al posto del `{client_ip}` token all'interno dell'URL tramite la `client_ip` [variabile server](#server-variables).

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="Screenshot del portale che mostra l'azione di reindirizzamento URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a> Riscrittura URL

Usare l'azione di **riscrittura URL** per riscrivere il percorso di una richiesta che viene indirizzata all'origine.

### <a name="properties"></a>Proprietà

| Proprietà | Valori supportati |
|----------|------------------|
| Modello di origine | Definire il modello di origine nel percorso URL da sostituire. Attualmente, il modello di origine usa una corrispondenza basata sul prefisso. Per trovare la corrispondenza con tutti i percorsi URL, usare una barra ( `/` ) come valore del modello di origine. |
| Destination | Definire il percorso di destinazione da utilizzare nella riscrittura. Il percorso di destinazione sovrascrive il modello di origine. |
| Mantieni percorso senza corrispondenza | Se impostato su _Sì_, il percorso rimanente dopo il modello di origine viene aggiunto al nuovo percorso di destinazione. |

### <a name="example"></a>Esempio

In questo esempio vengono riscritte tutte le richieste al percorso `/redirection` e non si mantiene il resto del percorso.

# <a name="portal"></a>[Portale](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="Screenshot del portale che mostra l'azione di riscrittura URL.":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Variabili del server

Le variabili server del set di regole consentono di accedere a informazioni strutturate sulla richiesta. È possibile usare le variabili server per modificare dinamicamente le intestazioni di richiesta/risposta o i percorsi di riscrittura URL/stringhe di query, ad esempio quando viene eseguita una nuova pagina o quando viene pubblicato un modulo.

### <a name="supported-variables"></a>Variabili supportate

| Nome variabile    | Descrizione                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | Indirizzo IP della connessione diretta al perimetro di Azure front door. Se il client usa un proxy HTTP o un servizio di bilanciamento del carico per inviare la richiesta, il valore di `socket_ip` è l'indirizzo IP del proxy o del servizio di bilanciamento del carico.                                                                      |
| `client_ip`      | Indirizzo IP del client che ha effettuato la richiesta originale. Se nella richiesta è presente un' `X-Forwarded-For` intestazione, l'indirizzo IP del client viene selezionato dall'intestazione.                                                                                                               |
| `client_port`    | Porta IP del client che ha effettuato la richiesta.                                                                                                                                                                                                                                          |
| `hostname`       | Nome host nella richiesta dal client.                                                                                                                                                                                                                                             |
| `geo_country`    | Indica il paese/l'area geografica di origine del richiedente tramite il relativo codice paese/area geografica.                                                                                                                                                                                                       |
| `http_method`    | Metodo utilizzato per effettuare la richiesta URL, ad esempio `GET` o `POST` .                                                                                                                                                                                                                         |
| `http_version`   | Protocollo della richiesta. In genere `HTTP/1.0` , `HTTP/1.1` o `HTTP/2.0` .                                                                                                                                                                                                                      |
| `query_string`   | Elenco di coppie variabile/valore che seguono "?" nell'URL richiesto.<br />Nella richiesta, ad esempio `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , il `query_string` valore sarà `id=123&title=fabrikam` .                                                      |
| `request_scheme` | Schema di richiesta: `http` o `https` .                                                                                                                                                                                                                                                    |
| `request_uri`    | URI completo della richiesta originale (con argomenti).<br />Nella richiesta, ad esempio `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , il `request_uri` valore sarà `/article.aspx?id=123&title=fabrikam` .                                                                     |
| `ssl_protocol`   | Protocollo di una connessione TLS stabilita.                                                                                                                                                                                                                                            |
| `server_port`    | Porta del server che ha accettato una richiesta.                                                                                                                                                                                                                                           |
| `url_path`       | Identifica la risorsa specifica nell'host a cui il client Web vuole accedere. Questa è la parte dell'URI della richiesta senza gli argomenti.<br />Nella richiesta, ad esempio `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , il `uri_path` valore sarà `/article.aspx` . |

### <a name="server-variable-format"></a>Formato variabile server    

È possibile specificare le variabili server usando i formati seguenti:

* `{variable}`: Include l'intera variabile server. Se, ad esempio, l'indirizzo IP del client è, `111.222.333.444` il `{client_ip}` token restituirà `111.222.333.444` .
* `{variable:offset}`: Includere la variabile server dopo un offset specifico, fino alla fine della variabile. L'offset è in base zero. Se, ad esempio, l'indirizzo IP del client è, `111.222.333.444` il `{client_ip:3}` token restituirà `.222.333.444` .
* `{variable:offset:length}`: Consente di includere la variabile server dopo un offset specifico, fino alla lunghezza specificata. L'offset è in base zero. Se, ad esempio, l'indirizzo IP del client è, `111.222.333.444` il `{client_ip:4:3}` token restituirà `222` .

### <a name="supported-actions"></a>Azioni supportate

Le variabili server sono supportate nelle azioni seguenti:

* Stringa di query della chiave della cache
* Modifica intestazione richiesta
* Modifica intestazione risposta
* Reindirizzamento URL
* Riscrittura URL

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [set di regole standard/Premium di Azure front door](concept-rule-set.md).
* Altre informazioni sulle [condizioni di corrispondenza del set di regole](concept-rule-set-match-conditions.md).
