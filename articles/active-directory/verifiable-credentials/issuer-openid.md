---
title: Esempi di comunicazione del servizio autorità emittente (anteprima) - Azure Active Directory credenziali verificabili
description: Dettagli della comunicazione tra il provider di identità e il servizio dell'autorità emittente
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 6aa502e1ed0e49192220174d5a8573690035a4a3
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739135"
---
# <a name="issuer-service-communication-examples-preview"></a>Esempi di comunicazione del servizio autorità emittente (anteprima)

Il Azure AD credenziali verificabili può rilasciare credenziali verificabili recuperando le attestazioni da un token ID generato dal provider di identità conforme a OpenID dell'organizzazione. Questo articolo illustra come configurare il provider di identità in modo che Authenticator possa comunicare con esso e recuperare il token ID corretto da passare al servizio emittente. 

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Per rilasciare una credenziale verificabile, authenticator viene indicato tramite il download del contratto per raccogliere l'input dall'utente e inviare tali informazioni al servizio emittente. Se è necessario usare un token ID, è necessario configurare il provider di identità per consentire ad Authenticator di accedere a un utente usando il OpenID Connect autenticazione. Le attestazioni nel token ID risultante vengono usate per popolare il contenuto delle credenziali verificabili. Authenticator autentica l'utente usando il flusso OpenID Connect del codice di autorizzazione. Il provider OpenID deve supportare le funzionalità OpenID Connect seguenti: 

| Funzionalità | Descrizione |
| ------- | ----------- |
| Tipo di concessione | Deve supportare il tipo di concessione del codice di autorizzazione. |
| Formato del token | Deve produrre JWT compatti non crittografati. |
| Algoritmo di firma | Deve produrre JWT firmati con RS 256. |
| Documento di configurazione | Deve supportare OpenID Connect documento di configurazione e `jwks_uri` . | 
| Registrazione client | Deve supportare la registrazione client pubblica usando `redirect_uri` il valore `vcclient://openid/` . | 
| PKCE | Consigliato per motivi di sicurezza, ma non obbligatorio. |

Di seguito sono riportati esempi di richieste HTTP inviate al provider di identità. Il provider di identità deve accettare e rispondere a queste richieste in base OpenID Connect standard di autenticazione.

## <a name="client-registration"></a>Registrazione client

Per ricevere una credenziale verificabile, gli utenti devono accedere all'IDP dall'app Microsoft Authenticator verificabile. 

Per abilitare questo scambio, registrare un'applicazione con il provider di identità. Se si usa Azure AD, è possibile trovare le istruzioni [qui.](../develop/quickstart-register-app.md) Usare i valori seguenti durante la registrazione.

| Impostazione | Valore |
| ------- | ----- |
| Nome applicazione | `<Issuer Name> Verifiable Credential Service` |
| URI di reindirizzamento | `vcclient://openid/ ` |


Dopo aver registrato un'applicazione con il provider di identità, registrarne l'ID client. Verrà utilizzato nella sezione seguente. È anche necessario annotarlo nell'endpoint noto per il provider di identità compatibile con OIDC. Il servizio emittente usa questo endpoint per scaricare le chiavi pubbliche necessarie per convalidare il token ID una volta inviato da Authenticator.

L'URI di reindirizzamento configurato viene usato da Authenticator per sapere quando l'accesso è stato completato e può recuperare il token ID. 

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
| `redirect_uri` | Deve usare `vcclient://openid/` . |
| `response_mode` | Deve supportare `query` . |
| `response_type` | Deve supportare `code` . |
| `scope` | Deve supportare `openid` . |
| `state` | Deve essere restituito al client in base OpenID Connect standard. |
| `nonce` | Deve essere restituita come attestazione nel token ID in base OpenID Connect standard. |

Quando riceve una richiesta di autorizzazione, il provider di identità deve autenticare l'utente ed eseguire i passaggi necessari per completare l'accesso, ad esempio l'autenticazione a più fattori.

È possibile personalizzare il processo di accesso per soddisfare le proprie esigenze. È possibile chiedere agli utenti di fornire informazioni aggiuntive, accettare le condizioni per l'utilizzo del servizio, pagare per le proprie credenziali e altro ancora. Al termine di tutti i passaggi, rispondere alla richiesta di autorizzazione reindirizzando all'URI di reindirizzamento, come illustrato di seguito. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parametro | Valore |
| ------- | ----------- |
| `code` |  Codice di autorizzazione restituito dal provider di identità. |
| `state` | Deve essere restituito al client in base OpenID Connect standard. |

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
| `redirect_uri` | Deve usare `vcclient://openid/` . |
| `scope` | Deve supportare `openid` . |
| `grant_type` | Deve supportare `authorization_code` . |
| `code` | Codice di autorizzazione restituito dal provider di identità. |

Quando riceve la richiesta di token, il provider di identità deve rispondere con un token ID.

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

Il token ID deve usare il formato di serializzazione compatta JWT e non deve essere crittografato. Il token ID deve contenere le attestazioni seguenti.

| Attestazione | Valore |
| ------- | ----------- |
| `kid` | Identificatore di chiave della chiave utilizzata per firmare il token ID, corrispondente a una voce nell'oggetto del provider `jwks_uri` OpenID. |
| `aud` | ID client ottenuto durante il processo di registrazione dell'applicazione. |
| `iss` | Deve essere il `issuer` valore nel documento OpenID Connect configurazione. |
| `exp` | Deve contenere l'ora di scadenza del token ID. |
| `iat` | Deve contenere l'ora di emissione del token ID. |
| `nonce` | Valore incluso nella richiesta di autorizzazione. |
| Attestazioni aggiuntive | Il token ID deve contenere eventuali attestazioni aggiuntive i cui valori verranno inclusi nella credenziale verificabile che verrà emessa. In questa sezione è necessario includere gli attributi relativi all'utente, ad esempio il nome. |

## <a name="next-steps"></a>Passaggi successivi

- [Come personalizzare le credenziali Azure Active Directory verificabili](credential-design.md)
