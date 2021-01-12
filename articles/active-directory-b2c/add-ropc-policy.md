---
title: Configurare un flusso di credenziali password del proprietario della risorsa
titleSuffix: Azure AD B2C
description: Informazioni su come configurare il flusso delle credenziali password del proprietario della risorsa (ROPC) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 565d5203f057aab74cce30729b3e14494b1edf4d
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127006"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Configurare un flusso di credenziali password del proprietario della risorsa in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In Azure Active Directory B2C (Azure AD B2C), il flusso delle credenziali password del proprietario della risorsa (ROPC) è un flusso di autenticazione standard OAuth. In questo flusso, un'applicazione, nota anche come relying party, scambia credenziali valide con token. Le credenziali includono un ID utente e una password. I token restituiti sono un token ID, un token di accesso e un token di aggiornamento.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>Note sul flusso ROPC

In Azure Active Directory B2C (Azure AD B2C) sono supportate le opzioni seguenti:

- **Client nativo**: l'interazione dell'utente durante l'autenticazione avviene quando il codice viene eseguito in un dispositivo lato utente. Il dispositivo può essere un'applicazione per dispositivi mobili in esecuzione in un sistema operativo nativo, ad esempio Android e iOS.
- **Flusso client pubblico**: nella chiamata API vengono inviate solo le credenziali utente, raccolte da un'applicazione. Le credenziali dell'applicazione non vengono inviate.
- **Aggiunta di nuove attestazioni**: il contenuto del token ID può essere modificato per aggiungere nuove attestazioni.

I flussi seguenti non sono supportati:

- **Da server a server**: il sistema di protezione delle identità richiede un indirizzo IP affidabile raccolto dal chiamante (il client nativo) come parte dell'interazione. In una chiamata API lato server viene usato solo l'indirizzo IP del server. Se viene superata una soglia dinamica di autenticazioni non riuscite, il sistema di protezione delle identità può identificare un indirizzo IP ripetuto come un utente malintenzionato.
- **Flusso client riservato**: l'ID client dell'applicazione viene convalidato, ma non viene convalidato il segreto dell'applicazione.

Quando si usa il flusso ROPC, tenere presente quanto segue:

- ROPC non funziona quando si verifica un'interruzione del flusso di autenticazione che richiede l'interazione dell'utente. Ad esempio, quando una password è scaduta o deve essere modificata, è necessaria [l'autenticazione](multi-factor-authentication.md) a più fattori o quando è necessario raccogliere più informazioni durante l'accesso, ad esempio il consenso dell'utente.
- ROPC supporta solo account locali. Gli utenti non possono accedere con [provider di identità federati](add-identity-provider.md) come Microsoft, Google +, Twitter, ad FS o Facebook.
- La [gestione delle sessioni](session-behavior.md), inclusa l'opzione [Mantieni l'accesso (KMSI)](session-behavior.md#enable-keep-me-signed-in-kmsi), non è applicabile.


## <a name="register-an-application"></a>Registrare un'applicazione

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>Creare un flusso utente per il proprietario della risorsa

1. Accedere al portale di Azure come amministratore globale del tenant di Azure AD B2C.
2. Per passare al tenant di Azure AD B2C, selezionare la directory B2C nell'angolo superiore destro del portale.
3. Selezionare **flussi utente** e selezionare **nuovo flusso utente**.
4. Selezionare **Accedi usando le credenziali password del proprietario della risorsa (ROPC)**.
5. In **versione** verificare che sia selezionata l'opzione **Anteprima** e quindi selezionare **Crea**.
7. Specificare un nome per il flusso utente, ad esempio *ROPC_Auth*.
8. In **Attestazioni dell'applicazione** fare clic su **Mostra dettagli**.
9. Selezionare le attestazioni necessarie per l'applicazione, ad esempio Nome visualizzato, Indirizzo di posta elettronica e Provider di identità.
10. Selezionare **OK**, quindi **Crea**.

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>Creare un criterio per il proprietario della risorsa

1. Aprire il file *TrustFrameworkExtensions.xml*.
2. Se non esiste già, aggiungere un elemento **ClaimsSchema** e i relativi elementi figlio come primo elemento sotto l'elemento **BuildingBlocks**:

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Dopo **ClaimsSchema**, aggiungere un elemento **ClaimsTransformations** e i relativi elementi figlio all'elemento **BuildingBlocks**:

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Individuare l'elemento **ClaimsProvider** con un **DisplayName** di `Local Account SignIn` e aggiungere il profilo tecnico seguente:

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Sostituire il valore **DefaultValue** di **client_id** con l'ID applicazione dell'applicazione ProxyIdentityExperienceFramework creato nell'esercitazione preliminare. Sostituire quindi il valore **DefaultValue** di **client_id** con l'ID applicazione dell'applicazione IdentityExperienceFramework, anche questo creato nell'esercitazione preliminare.

5. Aggiungere gli elementi **ClaimsProvider** seguenti con i relativi profili tecnici all'elemento **ClaimsProviders**:

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Aggiungere un elemento **UserJourneys** e i relativi elementi figlio all'elemento **TrustFrameworkPolicy**:

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
8. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *TrustFrameworkExtensions.xml*.
9. Fare clic su **Carica**.

## <a name="create-a-relying-party-file"></a>Creare un file relying party

È ora necessario aggiornare il file della relying party che avvierà il percorso utente appena creato:

1. Creare una copia del file *SignUpOrSignin.xml* nella directory di lavoro, quindi rinominare la copia *ROPC_Auth.xml*.
2. Aprire il nuovo file e aggiornare il valore dell'attributo **PolicyId** per **TrustFrameworkPolicy** con un valore univoco. L'ID criteri è il nome dei criteri. Ad esempio, **B2C_1A_ROPC_Auth**.
3. Modificare il valore dell'attributo **ReferenceId** in **DefaultUserJourney** con `ResourceOwnerPasswordCredentials`.
4. Modificare l'elemento **OutputClaims** affinché contenga soltanto le attestazioni seguenti:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Nella pagina **Criteri personalizzati** del tenant di Azure AD B2C selezionare **Carica il criterio**.
6. Abilitare **Sovrascrivi il criterio se esistente** e quindi cercare e selezionare il file *ROPC_Auth.xml*.
7. Fare clic su **Carica**.


::: zone-end

## <a name="test-the-ropc-flow"></a>Testare il flusso ROPC

Usare l'applicazione di sviluppo API preferita per generare una chiamata API ed esaminare la risposta per eseguire il debug del criterio. Costruire una chiamata come nell'esempio con le informazioni seguenti come corpo della richiesta POST:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Sostituire `<tenant-name>` con il nome del tenant di Azure AD B2C.
- Sostituire `B2C_1A_ROPC_Auth` con il nome completo dei criteri delle credenziali password del proprietario della risorsa.

| Chiave | valore |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Sostituire `user-account` con il nome di un account utente nel tenant.
- Sostituire `password1` con la password dell'account utente.
- Sostituire `application-id` con l'ID applicazione della registrazione di *ROPC_Auth_app*.
- *Offline_access* è facoltativo se si vuole ricevere un token di aggiornamento.

La richiesta POST effettiva è simile all'esempio seguente:

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Una risposta con esito positivo con l'accesso offline è simile all'esempio seguente:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Riscattare un token di aggiornamento

Costruire una chiamata POST come l'esempio seguente. Usare le informazioni riportate nella tabella seguente come corpo della richiesta:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Sostituire `<tenant-name>` con il nome del tenant di Azure AD B2C.
- Sostituire `B2C_1A_ROPC_Auth` con il nome completo dei criteri delle credenziali password del proprietario della risorsa.

| Chiave | valore |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Sostituire `application-id` con l'ID applicazione della registrazione di *ROPC_Auth_app*.
- Sostituire `refresh-token` con il **refresh_token** che è stato inviato nella risposta precedente.

Una risposta con esito positivo è simile all'esempio seguente:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Usare un SDK nativo o AppAuth

Azure AD B2C soddisfa gli standard OAuth 2.0 per le credenziali password del proprietario della risorsa client pubblico e dovrebbe essere compatibile con la maggior parte degli SDK client. Per informazioni aggiornate, vedere le informazioni relative all'[SDK di app native per OAuth 2.0 e OpenID Connect con implementazione delle procedure consigliate moderne](https://appauth.io/).

## <a name="next-steps"></a>Passaggi successivi

Scaricare esempi funzionanti, configurati per l'uso con Azure AD B2C da GitHub, [per Android](https://aka.ms/aadb2cappauthropc) e [per iOS](https://aka.ms/aadb2ciosappauthropc).
