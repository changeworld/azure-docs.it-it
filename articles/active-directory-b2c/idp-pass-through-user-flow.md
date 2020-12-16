---
title: Passare un token di accesso del provider di identità all'app
titleSuffix: Azure AD B2C
description: Informazioni su come passare un token di accesso per i provider di identità OAuth 2,0 come attestazione in un flusso utente in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e379b3fc77716eeea28b3dbeb9c3a022f0f16106
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516226"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Passare un token di accesso del provider di identità all'applicazione in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Un [flusso utente](user-flow-overview.md) in Azure Active Directory B2C (Azure ad B2C) consente agli utenti dell'applicazione di iscriversi o accedere con un provider di identità. Quando viene avviato il percorso, Azure AD B2C riceve un [token di accesso](tokens-overview.md) dal provider di identità. Azure AD B2C usa tale token per recuperare informazioni sull'utente. Si attiva un'attestazione nel flusso utente da passare il token attraverso le applicazioni registrate in Azure AD B2C.

::: zone pivot="b2c-user-flow"

Azure AD B2C supporta il passaggio del token di accesso dei provider di identità [OAuth 2,0](add-identity-provider.md) , tra cui [Facebook](identity-provider-facebook.md) e [Google](identity-provider-google.md). Per tutti gli altri provider di identità, l'attestazione viene restituita vuota.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C supporta il passaggio del token di accesso dei provider di identità [OAuth 2.0](authorization-code-flow.md) e [OpenID Connect](openid-connect.md). Per tutti gli altri provider di identità, l'attestazione viene restituita vuota.

::: zone-end

Il diagramma seguente illustra il modo in cui un token del provider di identità torna all'app: 

![Flusso di pass-through del provider di identità](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Abilitare l'attestazione

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e selezionare la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **flussi utente (criteri)**, quindi selezionare il flusso utente. Ad esempio, **B2C_1_signupsignin1**.
5. Selezionare **Attestazioni dell'applicazione**.
6. Abilitare l'attestazione del **token di accesso del provider di identità** .

    ![Abilitare l'attestazione del token di accesso del provider di identità](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. Fare clic su **Salva** per salvare il flusso utente.

## <a name="test-the-user-flow"></a>Testare il flusso utente

Durante il test delle applicazioni in Azure AD B2C, può essere utile avere restituito il token di Azure AD B2C a `https://jwt.ms` per esaminare le attestazioni in essa.

1. Nella pagina Panoramica del flusso utente, selezionare **Esegui il flusso utente**.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token nell'esempio seguente, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui il flusso utente** e quindi accedere con le credenziali dell'account. Verrà visualizzato il token di accesso del provider di identità nell'attestazione **idp_access_token**.

    Verrà visualizzato un testo simile al seguente esempio:

    ![Token decodificato in jwt.ms con idp_access_token blocco evidenziato](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Aggiungere gli elementi di attestazione

1. Aprire il file *TrustframeworkExtensions.xml* e aggiungere il seguente elemento **ClaimType** con un identificatore di `identityProviderAccessToken` per l'elemento **ClaimsSchema**:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Aggiungere l'elemento **OutputClaim** all'elemento **TechnicalProfile** per ogni provider di identità OAuth 2.0 per cui si desidera il token di accesso. L'esempio seguente illustra l'elemento aggiunto al profilo tecnico di Facebook:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Salvare il file *TrustframeworkExtensions.xml*.
4. Aprire un file dei criteri relying party come *SignUpOrSignIn.xml* e aggiungere l'elemento **OutputClaim** elemento al **TechnicalProfile**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Salvare il file dei criteri.

## <a name="test-your-policy"></a>Verificare i criteri

Durante il test delle applicazioni in Azure AD B2C, può essere utile avere restituito il token di Azure AD B2C a `https://jwt.ms` per riuscire a esaminare le attestazioni in essa.

### <a name="upload-the-files"></a>Caricare i file

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C facendo clic sul filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
5. Nella pagina dei criteri personalizzati, fare clic su **Carica criterio**.
6. Selezionare **Sovrascrivi il criterio se esistente**, quindi cercare e selezionare il file *TrustframeworkExtensions.xml*.
7. Selezionare **Carica**.
8. Ripetere i passaggi da 5 a 7 per il file relying party, ad esempio *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Esegui il criterio

1. Aprire il criterio che è stato modificato. Ad esempio, *B2C_1A_signup_signin*.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token nell'esempio seguente, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Selezionare **Esegui adesso**.

    Verrà visualizzato un testo simile al seguente esempio:

    ![Token decodificato in jwt.ms con idp_access_token blocco evidenziato](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [Panoramica dei token Azure ad B2C](tokens-overview.md).
