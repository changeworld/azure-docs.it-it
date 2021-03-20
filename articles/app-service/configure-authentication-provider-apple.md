---
title: Configurare l'accesso con Apple (anteprima)
description: Informazioni su come configurare l'accesso con Apple come provider di identità per il servizio app o l'app funzioni di Azure.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603228"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Configurare il servizio app o l'app funzioni di Azure per eseguire l'accesso usando un provider Apple (anteprima)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare app Azure servizio o funzioni di Azure per usare l'accesso con Apple come provider di autenticazione. 

Per completare la procedura descritta in questo articolo, è necessario essere iscritti al programma Apple Developer. Per eseguire la registrazione al programma Apple Developer, passare a [Developer.Apple.com/programs/Enroll](https://developer.apple.com/programs/enroll/).

> [!CAUTION]
> L'abilitazione dell'accesso con Apple Disabilita la gestione della funzionalità di autenticazione/autorizzazione del servizio app per l'applicazione tramite alcuni client, ad esempio portale di Azure, l'interfaccia della riga di comando di Azure e Azure PowerShell. La funzionalità si basa su una nuova superficie API che, durante l'anteprima, non è ancora stata contabilizzata in tutte le esperienze di gestione.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Creare un'applicazione nel portale per sviluppatori Apple
È necessario creare un ID app e un ID servizio nel portale Apple Developer.

1. Nel portale Apple Developer passare a **certificati, identificatori, & profili**.
2. Nella scheda **identificatori** selezionare il pulsante **(+)** .
3. Nella pagina **registra un nuovo identificatore** scegliere **ID app** e selezionare **continua**. Gli ID app includono uno o più ID servizio. ![Registrazione di un nuovo identificatore di app nel portale per sviluppatori Apple](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. Nella pagina **registra ID app** specificare una descrizione e un ID bundle e selezionare **Accedi con Apple** dall'elenco delle funzionalità. Selezionare quindi **Continua**. Prendere nota del **prefisso dell'ID app (ID Team)** da questo passaggio, che sarà necessario in un secondo momento.
![Configurazione di un nuovo identificatore di app nel portale per sviluppatori Apple](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Esaminare le informazioni di registrazione dell'app e selezionare **registra**.
6. Anche in questo caso, nella scheda **identificatori** selezionare il pulsante **(+)** .
![Creazione di un nuovo identificatore di servizio nel portale per sviluppatori Apple](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. Nella pagina **registra un nuovo identificatore** scegliere **ID Servizi** e selezionare **continua**.
![Registrazione di un nuovo identificatore del servizio nel portale per sviluppatori Apple](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. Nella pagina **registra ID Servizi** specificare una descrizione e un identificatore. La descrizione è quella che verrà visualizzata all'utente nella schermata del consenso. L'identificatore sarà l'ID client usato per configurare il provider Apple con il servizio app. Selezionare quindi **Configura**.
![Fornire una descrizione e un identificatore](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. Nella finestra popup impostare l'ID app principale sull'ID app creato in precedenza. Specificare il dominio dell'applicazione nella sezione dominio. Per l'URL restituito, usare l'URL `<app-url>/.auth/login/apple/callback` . Ad esempio: `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Quindi selezionare **Aggiungi** e **Salva**.
![Specifica del dominio e dell'URL restituito per la registrazione](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Esaminare le informazioni di registrazione del servizio e selezionare **Salva**.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Genera il segreto client
Apple richiede agli sviluppatori di app di creare e firmare un token JWT come valore del segreto client. Per generare questo segreto, generare e scaricare prima una chiave privata a curva ellittica dal portale Apple Developer. Usare quindi la chiave per [firmare un JWT](#sign-the-client-secret-jwt) con un [payload specifico](#structure-the-client-secret-jwt).

### <a name="create-and-download-the-private-key"></a>Creare e scaricare la chiave privata
1. Nella scheda **chiavi** del portale Apple Developer fare clic su **Crea una chiave** o selezionare il pulsante **(+)** .
2. Nella pagina **registra una nuova chiave** assegnare un nome alla chiave, selezionare la casella accanto a **Accedi con Apple** e selezionare **Configura**.
3. Nella pagina **Configura chiave** collegare la chiave all'ID app principale creato in precedenza e selezionare **Salva**.
4. Per completare la creazione della chiave, confermare le informazioni e selezionare **continue (continua** ) e quindi esaminare le informazioni e selezionare Register ( **registra**).
5. Nella pagina **scaricare la chiave** scaricare la chiave. Verrà scaricato come `.p8` file (PKCS # 8). verrà usato il contenuto del file per firmare il segreto client JWT.

### <a name="structure-the-client-secret-jwt"></a>Strutturare il segreto client JWT
Apple richiede che il segreto client sia la codifica Base64 di un token JWT. Il token JWT decodificato deve avere un payload strutturato come nell'esempio seguente:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **Sub**: ID del client Apple (anche ID del servizio)
- **ISS**: ID del team per sviluppatori Apple
- **AUD**: Apple riceve il token, quindi sono i destinatari
- **Exp**: non più di sei mesi dopo **NBF**

La versione con codifica Base64 del payload precedente ha un aspetto simile al seguente: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Nota: Apple non accetta token JWT segreto client con una data di scadenza superiore a sei mesi dopo la data di creazione (o NBF). Ciò significa che sarà necessario ruotare il segreto client almeno ogni sei mesi._

Altre informazioni sulla generazione e la convalida dei token sono disponibili nella [documentazione per gli sviluppatori di Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens). 

### <a name="sign-the-client-secret-jwt"></a>Firmare il segreto client JWT
Il `.p8` file scaricato in precedenza verrà usato per firmare il segreto client JWT. Questo file è un [file PCKS # 8](https://en.wikipedia.org/wiki/PKCS_8) che contiene la chiave di firma privata in formato PEM. Sono disponibili molte librerie che consentono di creare e firmare i JWT. 

Ci sono diversi tipi di librerie open source disponibili online per la creazione e la firma di token JWT. Per ulteriori informazioni sulla generazione di token JWT, vedere jwt.io. Ad esempio, un modo per generare il segreto client consiste nell'importare il [pacchetto NuGet Microsoft. IdentityModel. Tokens](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) ed eseguire una piccola quantità di codice C# mostrato di seguito.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**: ID del team per sviluppatori Apple
- **ClientID**: ID del client Apple (anche ID del servizio)
- **p8key**: chiave di formato PEM: è possibile ottenere la chiave aprendo il `.p8` file in un editor di testo e copiando tutti gli elementi compresi tra `-----BEGIN PRIVATE KEY-----` e `-----END PRIVATE KEY-----` senza interruzioni di riga
- **keyId**: ID della chiave scaricata

Questo token restituito è il valore del segreto client che verrà usato per configurare il provider Apple.

> [!IMPORTANT]
> Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.
>

Aggiungere il segreto client come [impostazione dell'applicazione](./configure-common.md#configure-app-settings) per l'app, usando un nome di impostazione scelto. Prendere nota di questo nome per un momento successivo.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Aggiungere informazioni sul provider all'applicazione

> [!NOTE]
> La configurazione richiesta è in un nuovo formato API, attualmente supportata solo dalla [configurazione basata su file (anteprima)](.\app-service-authentication-how-to.md#config-file). È necessario attenersi alla procedura seguente usando tale file.

In questa sezione viene illustrato come aggiornare la configurazione per includere il nuovo IDP. Di seguito è riportata una configurazione di esempio.

1. All'interno dell' `identityProviders` oggetto aggiungere un `apple` oggetto, se non ne esiste già uno.
2. Assegnare un oggetto alla chiave con un `registration` oggetto al suo interno e, facoltativamente, un `login` oggetto:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. All'interno dell' `registration` oggetto impostare sull' `clientId` ID client raccolto.
    
    b. All'interno dell' `registration` oggetto, impostare sul `clientSecretSettingName` nome dell'impostazione dell'applicazione in cui è stato archiviato il segreto client.
    
    c. All'interno dell' `login` oggetto, è possibile scegliere di impostare la `scopes` matrice in modo da includere un elenco di ambiti usati per l'autenticazione con Apple, ad esempio "nome" e "posta elettronica". Se gli ambiti sono configurati, verranno richiesti in modo esplicito nella schermata di consenso quando gli utenti accedono per la prima volta.

Una volta impostata questa configurazione, si è pronti per usare il provider Apple per l'autenticazione nell'app.

Una configurazione completa potrebbe avere un aspetto simile all'esempio seguente, in cui l'impostazione di APPLE_GENERATED_CLIENT_SECRET punta a un'impostazione dell'applicazione contenente un JWT generato:

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
