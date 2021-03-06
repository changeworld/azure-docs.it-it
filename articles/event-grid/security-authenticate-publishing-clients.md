---
title: Autenticare i client che pubblicano eventi in argomenti o domini personalizzati di griglia di eventi
description: Questo articolo descrive le diverse modalità di autenticazione dei client che pubblicano eventi in argomenti personalizzati di griglia di eventi.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c415b7e1bb6bd7a2116da82c7d8f1de205009d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94886261"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Autenticare i client di pubblicazione (griglia di eventi di Azure)
Questo articolo fornisce informazioni sull'autenticazione dei client che pubblicano eventi negli argomenti o nei domini di griglia di eventi di Azure usando la **chiave di accesso** o il token di **firma di accesso condiviso (SAS)** . È consigliabile usare il token di firma di accesso condiviso, ma l'autenticazione con chiave fornisce una programmazione semplice ed è compatibile con molti autori di Webhook esistenti.  

## <a name="authenticate-using-an-access-key"></a>Eseguire l'autenticazione con una chiave di accesso
L'autenticazione della chiave di accesso è la forma più semplice di autenticazione. È possibile passare la chiave di accesso come intestazione HTTP o parametro di query URL. 

### <a name="access-key-in-a-http-header"></a>Chiave di accesso in un'intestazione HTTP
Passare la chiave di accesso come valore per l'intestazione HTTP: `aeg-sas-key` .

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Chiave di accesso come parametro di query
Come parametro di query è anche possibile specificare `aeg-sas-key`. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Per istruzioni su come ottenere le chiavi di accesso per un argomento o un dominio, vedere [ottenere le chiavi di accesso](get-access-keys.md).

## <a name="authenticate-using-a-sas-token"></a>Eseguire l'autenticazione con un token di firma di accesso condiviso
I token SAS per una risorsa di griglia di eventi includono la risorsa, l'ora di scadenza e una firma. Il formato del token di firma di accesso condiviso è: `r={resource}&e={expiration}&s={signature}`.

La risorsa è il percorso dell'argomento di Griglia di eventi a cui si inviano gli eventi. Un percorso di risorsa valido, ad esempio, è: `https://<yourtopic>.<region>.eventgrid.azure.net/api/events`. Per visualizzare tutte le versioni dell'API supportate, vedere [Tipi di risorsa di Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). 

Per prima cosa, generare un token di firma di accesso condiviso a livello di codice e quindi usare l'intestazione `aeg-sas-token` o l' `Authorization SharedAccessSignature` intestazione per l'autenticazione con griglia di eventi. 

### <a name="generate-sas-token-programmatically"></a>Genera token SAS a livello di codice
L'esempio seguente crea un token di firma di accesso condiviso per l'uso con Griglia di eventi:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Uso dell'intestazione AEG-SAS-token
Di seguito è riportato un esempio di passaggio del token SAS come valore per l' `aeg-sas-token` intestazione. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Utilizzo dell'intestazione Authorization
Di seguito è riportato un esempio di passaggio del token SAS come valore per l' `Authorization` intestazione. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sull'autenticazione con i gestori eventi per il recapito di eventi, vedere [autenticazione del recapito](security-authentication.md) degli eventi. 
