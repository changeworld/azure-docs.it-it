---
title: Esempi di comunicazione del servizio emittente (anteprima)
description: Dettagli della comunicazione tra il provider di identità e il servizio emittente
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: bd1b7560967ff0c458639a5737a02fca27ffc9f5
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170172"
---
# <a name="issuer-service-communication-examples-preview"></a>Esempi di comunicazione del servizio emittente (anteprima)

Il servizio emittente di credenziali verificabile può emettere credenziali verificabili recuperando le attestazioni da un token ID generato dal provider di identità OpenID conforme alla propria organizzazione. Questo articolo fornisce istruzioni su come configurare il provider di identità in modo che l'autenticatore possa comunicare con esso e recuperare il token ID corretto da passare al servizio emittente. 

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Per emettere una credenziale verificabile, l'autenticatore viene istruito tramite il download del contratto per raccogliere l'input dall'utente e inviare tali informazioni al servizio emittente. Se è necessario usare un token ID, è necessario configurare il provider di identità per consentire a Authenticator di accedere a un utente usando il protocollo OpenID Connect. Le attestazioni nel token ID risultante vengono utilizzate per popolare il contenuto della credenziale verificabile. L'autenticatore autentica l'utente usando il flusso del codice di autorizzazione OpenID Connect. Il provider OpenID deve supportare le funzionalità di OpenID Connect seguenti: 

| Funzionalità | Descrizione |
| ------- | ----------- |
| Tipo di concessione | Deve supportare il tipo di concessione del codice di autorizzazione. |
| Formato token | Deve produrre token JWT Compact non crittografati. |
| Algoritmo di firma | Deve produrre token JWT firmato usando RSA 256. |
| Documento di configurazione | Deve supportare il documento di configurazione di OpenID Connect e `jwks_uri` . | 
| Registrazione client | Deve supportare la registrazione dei client pubblici usando un `redirect_uri` valore di `vclient://openid/` . | 
| PKCE | Consigliato per motivi di sicurezza, ma non obbligatorio. |

Di seguito sono elencati alcuni esempi di richieste HTTP inviate al provider di identità. Il provider di identità deve accettare e rispondere a queste richieste in conformità con lo standard di autenticazione OpenID Connect.

## <a name="client-registration"></a>Registrazione client

Per ricevere credenziali verificabili, gli utenti devono accedere all'IDP dall'app Microsoft Authenticator. 

Per abilitare questo scambio, registrare un'applicazione con il provider di identità. Se si usa Azure AD, è possibile trovare le istruzioni [qui](../develop/quickstart-register-app.md). Usare i valori seguenti per la registrazione.

| Impostazione | Valore |
| ------- | ----- |
| Nome applicazione | `<Issuer Name> Verifiable Credential Service` |
| URI di reindirizzamento | `vcclient://openid/ ` |


Dopo aver registrato un'applicazione con il provider di identità, registrarne l'ID client. Verrà usato nella sezione seguente. È anche necessario prendere nota dell'URL dell'endpoint noto per il provider di identità compatibile con OIDC. Il servizio emittente usa questo endpoint per scaricare le chiavi pubbliche necessarie per convalidare il token ID una volta inviato dall'autenticatore.

L'URI di reindirizzamento configurato viene usato dall'autenticatore per sapere quando l'accesso è stato completato e può recuperare il token ID. 

## <a name="authorization-request"></a>Richiesta di autorizzazione

La richiesta di autorizzazione inviata al provider di identità usa il formato seguente.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parametro | Valore |
| ------- | ----------- |
| `client_id` | ID client ottenuto durante il processo di registrazione dell'applicazione. |
| `redirect_uri` | Deve utilizzare `vcclient://openid/` . |
| `response_mode` | Deve supportare `query` . |
| `response_type` | Deve supportare `code` . |
| `scope` | Deve supportare `openid` . |
| `state` | Deve essere restituito al client in base allo standard OpenID Connect. |
| `nonce` | Deve essere restituito come attestazione nel token ID in base allo standard OpenID Connect. |

Quando riceve una richiesta di autorizzazione, il provider di identità deve autenticare l'utente ed eseguire tutti i passaggi necessari per completare l'accesso, ad esempio l'autenticazione a più fattori.

Il licenziatario potrà personalizzare il processo di accesso per soddisfare le proprie esigenze. È possibile chiedere agli utenti di fornire informazioni aggiuntive, accettare le condizioni del servizio, pagare le credenziali e altro ancora. Al termine di tutti i passaggi, rispondere alla richiesta di autorizzazione reindirizzando l'URI di reindirizzamento come illustrato di seguito. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parametro | Valore |
| ------- | ----------- |
| `code` |  Codice di autorizzazione restituito dal provider di identità. |
| `state` | Deve essere restituito al client in base allo standard OpenID Connect. |

## <a name="token-request"></a>Richiesta di token

La richiesta di token inviata al provider di identità avrà il formato seguente.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parametro | Valore |
| ------- | ----------- |
| `client_id` | ID client ottenuto durante il processo di registrazione dell'applicazione. |
| `redirect_uri` | Deve utilizzare `vcclient://openid/` . |
| `scope` | Deve supportare `openid` . |
| `grant_type` | Deve supportare `authorization_code` . |
| `code` | Codice di autorizzazione restituito dal provider di identità. |

Al momento della ricezione della richiesta di token, il provider di identità deve rispondere con un token ID.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

Il token ID deve usare il formato di serializzazione JWT Compact e non deve essere crittografato. Il token ID deve contenere le attestazioni seguenti.

| Attestazione | Valore |
| ------- | ----------- |
| `kid` | Identificatore di chiave della chiave usata per firmare il token ID, corrispondente a una voce nell'oggetto del provider OpenID `jwks_uri` . |
| `aud` | ID client ottenuto durante il processo di registrazione dell'applicazione. |
| `iss` | Deve essere il `issuer` valore nel documento di configurazione di OpenID Connect. |
| `exp` | Deve contenere l'ora di scadenza del token ID. |
| `iat` | Deve contenere l'ora in cui è stato emesso il token ID. |
| `nonce` | Valore incluso nella richiesta di autorizzazione. |
| Attestazioni aggiuntive | Il token ID deve contenere eventuali attestazioni aggiuntive i cui valori verranno inclusi nelle credenziali verificabili che verranno rilasciate. In questa sezione è necessario includere tutti gli attributi relativi all'utente, ad esempio il nome. |