---
title: Configurare i requisiti di complessità delle password
titleSuffix: Azure AD B2C
description: Come configurare i requisiti di complessità delle password specificate dagli utenti in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 81c6e58e34f30d5736c40c77a308321dee28ae34
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224266"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configurare i requisiti di complessità delle password in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) supporta la modifica dei requisiti di complessità delle password specificate da un utente finale durante la creazione di un account. Per impostazione predefinita, Azure AD B2C usa password di complessità **Alta**. Azure AD B2C supporta anche opzioni di configurazione per controllare la complessità delle password che i clienti possono usare.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Applicazione delle regole delle password

Durante la registrazione o la reimpostazione di una password, l'utente finale deve specificare una password che soddisfi le regole di complessità, che vengono applicate in base al flusso utente di riferimento. È possibile che un flusso utente richieda un pin a quattro cifre durante l'iscrizione mentre un altro flusso utente richiede una stringa di otto caratteri durante l'iscrizione. È possibile, ad esempio, usare un flusso utente con una complessità delle password diversa per gli adulti e per i bambini.

La complessità delle password non viene mai applicata durante l'accesso. Durante la registrazione, infatti, agli utenti non viene mai chiesto di modificare la password perché non soddisfa i requisiti di complessità correnti.

La complessità delle password può essere configurata nei tipi di flussi utente seguenti:

- Flusso utente di iscrizione o accesso
- Flusso utente di reimpostazione delle password

Se si usano criteri personalizzati, è possibile [configurare la complessità delle password nei criteri personalizzati](password-complexity.md).

## <a name="configure-password-complexity"></a>Configurare la complessità delle password

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
3. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
4. Selezionare **Flussi utente**.
2. Selezionare un flusso utente e fare clic su **Proprietà**.
3. In **Complessità password** impostare la complessità delle password per questo flusso utente su **Semplice**, **Alta** o **Personalizzata**.

### <a name="comparison-chart"></a>Grafico di confronto

| Complessità | Descrizione |
| --- | --- |
| Semplice | Una password con un numero di caratteri compreso tra 8 e 64. |
| Assoluta | Una password con un numero di caratteri compreso tra 8 e 64. Richiede almeno tre dei quattro tipi di carattere seguenti: lettere minuscole, lettere maiuscole, numeri e simboli. |
| Personalizzato | Questa opzione offre il massimo controllo sulle regole di complessità delle password.  Consente infatti di configurare una lunghezza personalizzata  o di accettare password solo numeriche (PIN). |

## <a name="custom-options"></a>Opzioni personalizzate

### <a name="character-set"></a>Set di caratteri

Consente di accettare solo cifre (PIN) o l'intero set di caratteri.

- **Solo numeri** consente di immettere solo cifre (0-9) durante la configurazione di una password.
- **Tutti** consente qualsiasi lettera, numero o simbolo.

### <a name="length"></a>Length

Consente di controllare i requisiti di lunghezza della password.

- Il valore **Lunghezza minima** deve essere almeno 4.
- La **lunghezza massima** deve essere maggiore o uguale alla lunghezza minima e può essere di 256 caratteri.

### <a name="character-classes"></a>Classi di caratteri

Consente di controllare i diversi tipi di carattere usati nella password.

- **2 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (2 di 4: carattere minuscolo, carattere maiuscolo, numero (0-9), simbolo): garantisce che la password contenga almeno due tipi di carattere, ad esempio un numero e un carattere minuscolo.
- **3 di 4: carattere minuscolo, carattere maiuscolo, numero (0-9), il simbolo** garantisce che la password contenga almeno tre tipi di caratteri. ad esempio un numero, un carattere minuscolo e un carattere maiuscolo.
- **4 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (4 di 4: carattere minuscolo, carattere maiuscolo, numero (0-9), simbolo): garantisce che la password contenga tutti i quattro tipi di carattere.

    > [!NOTE]
    > L'opzione **4 of 4** (4 di 4) può determinare frustrazione nell'utente finale. Alcuni studi hanno dimostrato inoltre che questo requisito non migliora l'entropia delle password. Vedere [NIST Password Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) (Linee guida sulle password NIST)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Convalida del predicato password

Per configurare la complessità delle password, eseguire l'override dei `newPassword` `reenterPassword` [tipi di attestazione](claimsschema.md) e con un riferimento alle [convalide del predicato](predicates.md#predicatevalidations). L'elemento PredicateValidations raggruppa un set di predicati per formare una convalida dell'input dell'utente che può essere applicata a un tipo di attestazione. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere le `newPassword` `reenterPassword` attestazioni e all'elemento **ClaimsSchema** .

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="newPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
        <ClaimType Id="reenterPassword">
          <PredicateValidationReference Id="CustomPassword" />
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. I [predicati](predicates.md) definiscono una convalida di base per verificare il valore di un tipo di attestazione e restituisce true o false. La convalida viene eseguita utilizzando un elemento del metodo specificato e un set di parametri relativi al metodo. Aggiungere i predicati seguenti all'elemento **BuildingBlocks** immediatamente dopo la chiusura dell' `</ClaimsSchema>` elemento:

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <Predicates>
        <Predicate Id="LengthRange" Method="IsLengthRange">
          <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
          <Parameters>
            <Parameter Id="Minimum">6</Parameter>
            <Parameter Id="Maximum">64</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Lowercase" Method="IncludesCharacters">
          <UserHelpText>a lowercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">a-z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Uppercase" Method="IncludesCharacters">
          <UserHelpText>an uppercase letter</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">A-Z</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Number" Method="IncludesCharacters">
          <UserHelpText>a digit</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">0-9</Parameter>
          </Parameters>
        </Predicate>
        <Predicate Id="Symbol" Method="IncludesCharacters">
          <UserHelpText>a symbol</UserHelpText>
          <Parameters>
            <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
          </Parameters>
        </Predicate>
      </Predicates>
    <!-- 
    </BuildingBlocks>-->
    ```

1. Aggiungere le convalide del predicato seguenti all'elemento **BuildingBlocks** , immediatamente dopo la chiusura dell' `</Predicates>` elemento:

    ```xml
    <!-- 
    <BuildingBlocks>-->
      <PredicateValidations>
        <PredicateValidation Id="CustomPassword">
          <PredicateGroups>
            <PredicateGroup Id="LengthGroup">
              <PredicateReferences MatchAtLeast="1">
                <PredicateReference Id="LengthRange" />
              </PredicateReferences>
            </PredicateGroup>
            <PredicateGroup Id="CharacterClasses">
              <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
              <PredicateReferences MatchAtLeast="3">
                <PredicateReference Id="Lowercase" />
                <PredicateReference Id="Uppercase" />
                <PredicateReference Id="Number" />
                <PredicateReference Id="Symbol" />
              </PredicateReferences>
            </PredicateGroup>
          </PredicateGroups>
        </PredicateValidation>
      </PredicateValidations>
    <!-- 
    </BuildingBlocks>-->
    ```

## <a name="disable-strong-password"></a>Disabilita password complessa 

I profili tecnici seguenti sono [Active Directory profili tecnici](active-directory-technical-profile.md)che leggono e scrivono i dati Azure Active Directory. Eseguire l'override di questi profili tecnici nel file di estensione. Utilizzare `PersistedClaims` per disabilitare i criteri password complesse. Trovare l'elemento **ClaimsProviders**.  Aggiungere i seguenti provider di attestazioni come indicato di seguito:

```xml
<!-- 
<ClaimsProviders>-->
  <ClaimsProvider>
    <DisplayName>Azure Active Directory</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
        <PersistedClaims>
          <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </PersistedClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders>-->
```

Se si usano i criteri [di accesso basati sul nome utente](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin) , aggiornare `AAD-UserWriteUsingLogonEmail` i `AAD-UserWritePasswordUsingObjectId` `LocalAccountWritePasswordUsingObjectId` profili tecnici, e con i criteri *DisableStrongPassword* .

Salvare il file dei criteri.

## <a name="test-your-policy"></a>Verificare i criteri

### <a name="upload-the-files"></a>Caricare i file

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
5. Nella pagina dei criteri personalizzati, fare clic su **Carica criterio**.
6. Selezionare **Sovrascrivi il criterio se esistente**, quindi cercare e selezionare il file *TrustFrameworkExtensions.xml* .
7. Fare clic su **Carica**.

### <a name="run-the-policy"></a>Esegui il criterio

1. Aprire i criteri di iscrizione o di accesso. Ad esempio, *B2C_1A_signup_signin*.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui adesso**.
4. Selezionare **Iscriversi adesso** e immettere un indirizzo di posta elettronica e una nuova password. Vengono presentate linee guida relative alle restrizioni delle password. Immettere le informazioni utente e quindi fare clic su **Crea**. Viene visualizzato il contenuto del token restituito.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare la modifica della password in Azure Active Directory B2C](add-password-change-policy.md).
- Altre informazioni sui [predicati](predicates.md) e sugli elementi [PREDICATEVALIDATIONS](predicates.md#predicatevalidations) nel riferimento Framework dell'esperienza.


::: zone-end
