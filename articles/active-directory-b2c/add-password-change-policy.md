---
title: Configurare la modifica della password tramite criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare gli utenti a modificare le password usando criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 21da8f79772d9648836bedec89cb5d7014486dc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798360"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Configurare la modifica delle password usando criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In Azure Active Directory B2C (Azure AD B2C), è possibile abilitare gli utenti che hanno eseguito l'accesso con un account locale per modificare la password senza dover dimostrare la propria identità tramite la verifica tramite posta elettronica. Il flusso di modifica della password prevede i passaggi seguenti:

1. L'utente accede al proprio account locale. Se la sessione è ancora attiva, Azure AD B2C autorizza l'utente e passa al passaggio successivo.
1. L'utente verifica la **vecchia password** e quindi crea e conferma la **nuova password**.

![Flusso di modifica della password](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> Il flusso di modifica della password consente agli utenti di modificare la password solo quando l'utente conosce la password e vuole modificarla. È consigliabile abilitare anche la [reimpostazione della password self-service](add-password-reset-policy.md) per supportare i casi in cui l'utente dimentica la password.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Prerequisiti

* Completare le procedure illustrate in [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md).
* Se non è già stato fatto, [registrare un'applicazione Web in Azure Active Directory B2C](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Aggiungere gli elementi

1. Aprire il file *TrustframeworkExtensions.xml* e aggiungere il seguente elemento **ClaimType** con un identificatore di `oldPassword` per l'elemento [ClaimsSchema](claimsschema.md):

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Un elemento [ClaimsProvider](claimsproviders.md) contiene il profilo tecnico che autentica l'utente. Aggiungere i provider di attestazioni seguenti all'elemento **ClaimsProviders**:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

3. L'elemento [UserJourney](userjourneys.md) definisce il percorso dell'utente durante l'interazione con l'applicazione. Aggiungere l'elemento **UserJourneys**, se non esiste, con **UserJourney** identificato come `PasswordChange`:

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Salvare il file dei criteri *TrustFrameworkExtensions.xml*.
5. Copiare il file *ProfileEdit* scaricato con il pacchetto starter e denominarlo *ProfileEditPasswordChange.xml*.
6. Aprire il nuovo file e aggiornare l'attributo **PolicyId** con un valore univoco. Questo valore è il nome dei criteri. Ad esempio, *B2C_1A_profile_edit_password_change*.
7. Modificare l'attributo **ReferenceId** in `<DefaultUserJourney>`, in modo che corrisponda all'ID del nuovo percorso utente creato. Ad esempio, *PasswordChange*.
8. Salvare le modifiche.

## <a name="upload-and-test-the-policy"></a>Caricare e testare i criteri

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
5. Nella pagina dei criteri personalizzati, fare clic su **Carica criterio**.
6. Selezionare **Sovrascrivi il criterio se esistente**, quindi cercare e selezionare il file *TrustframeworkExtensions.xml*.
7. Fare clic su **Carica**.
8. Ripetere i passaggi da 5 a 7 per il file relying party, ad esempio *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Esegui il criterio

1. Aprire il criterio che è stato modificato. Ad esempio, *B2C_1A_profile_edit_password_change*.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui adesso**. Accedere con l'account creato in precedenza. È ora possibile modificare la password.

## <a name="next-steps"></a>Passaggi successivi

- Trovare i criteri di esempio in [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
- Informazioni su come configurare la [complessità delle password in Azure ad B2C](password-complexity.md).
- Configurare un [flusso di reimpostazione della password](add-password-reset-policy.md).

::: zone-end
