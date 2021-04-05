---
title: Definire attributi personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Definire attributi personalizzati per l'applicazione in Azure Active Directory B2C per raccogliere informazioni sui clienti.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 17c73257db371bbec0c72a23b1303847a8d14102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607918"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definire attributi personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Nell'articolo [aggiungere attestazioni e personalizzare l'input dell'utente tramite criteri personalizzati](configure-user-input.md) viene illustrato come usare [gli attributi predefiniti del profilo utente](user-profile-attributes.md). In questo articolo viene abilitato un attributo personalizzato nella directory Azure Active Directory B2C (Azure AD B2C). Successivamente, è possibile usare il nuovo attributo come attestazione personalizzata in [flussi utente](user-flow-overview.md) o [criteri personalizzati](custom-policy-get-started.md) simultaneamente.

La directory di Azure AD B2C include un [set predefinito di attributi](user-profile-attributes.md). Tuttavia, è spesso necessario creare attributi personalizzati per gestire uno scenario specifico, ad esempio quando:

* Un'applicazione per il cliente deve salvare in modo permanente un attributo **LoyaltyId** .
* Un provider di identità ha un identificatore utente univoco, **uniqueUserGUID**, che deve essere reso permanente.
* Un percorso utente personalizzato deve salvare in modo permanente lo stato dell'utente, **migrationStatus**, per l'uso di altre logiche.

I termini *proprietà di estensione*, *attributo personalizzato* e *attestazione personalizzata* fanno riferimento allo stesso concetto nel contesto di questo articolo. Il nome varia in base al contesto, ad esempio applicazione, oggetto o criteri.

Azure AD B2C consente di estendere il set di attributi archiviati in ogni account utente. Questi attributi possono anche essere scritti e letti usando l'[API Microsoft Graph](microsoft-graph-operations.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Creare un attributo personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **Attributi utente**, quindi selezionare **Aggiungi**.
1. Fornire un **Nome** per l'attributo personalizzato (ad esempio, "ShoeSize")
1. Scegliere un **Tipo di dati**. Sono disponibili solo **Stringa**, **Booleano** e **Int**.
1. Facoltativamente, immettere una **Descrizione** esclusivamente a scopo informativo.
1. Fare clic su **Crea**.

L'attributo personalizzato è ora disponibile nell'elenco degli **attributi utente** e per l'uso nei flussi utente. Un attributo personalizzato viene creato solo la prima volta che viene usato in un flusso utente e non quando lo si aggiunge all'elenco degli **attributi utente**.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Usare un attributo personalizzato nel flusso utente

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul criterio, ad esempio "B2C_1_SignupSignin", per aprirlo.
1. Fare clic su **Attributi utente** e selezionare l'attributo personalizzato (ad esempio, "ShoeSize"). Fare clic su **Salva**.
1. Fare clic su **Attestazioni dell'applicazione** e selezionare l'attributo personalizzato.
1. Fare clic su **Salva**.

Dopo aver creato un nuovo utente usando un flusso utente, che usa l'attributo personalizzato appena creato, è possibile eseguire query sull'oggetto in [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). In alternativa, è possibile usare la funzionalità [Esegui flusso utente](./tutorial-create-user-flows.md) nel flusso utente per verificare l'esperienza del cliente. Ora si dovrebbe vedere **ShoeSize** nell'elenco di attributi che vengono raccolti durante il percorso dell'utente e nel token inviato all'applicazione.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>App Azure AD B2C Extensions

Gli attributi di estensione possono essere registrati solo in un oggetto applicazione, anche se possono contenere dati per un utente. L'attributo di estensione è associato all'applicazione denominata `b2c-extensions-app` . Non modificare questa applicazione, perché viene usata da Azure AD B2C per archiviare i dati utente. Questa applicazione è reperibile in Azure AD B2C, registrazioni per l'app. Ottenere le proprietà dell'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **registrazioni app**, quindi selezionare **tutte le applicazioni**.
1. Selezionare l'applicazione `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Copiare negli Appunti e salvare gli identificatori seguenti:
    * **ID applicazione**. Esempio: `11111111-1111-1111-1111-111111111111`.
    * **ID oggetto**. Esempio: `22222222-2222-2222-2222-222222222222`.

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>Modificare i criteri personalizzati

Per abilitare gli attributi personalizzati nei criteri, fornire l'ID **applicazione** e l' **ID oggetto** applicazione nei metadati del profilo tecnico AAD-Common. Il profilo tecnico *comune di AAD* si trova nel profilo tecnico di base [Azure Active Directory](active-directory-technical-profile.md) e fornisce il supporto per la gestione Azure ad utenti. Altri profili tecnici Azure AD includono i AAD-Common per sfruttare la configurazione. Eseguire l'override del profilo tecnico AAD-Common nel file di estensione.

1. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Trovare l'elemento ClaimsProviders. Aggiungere un nuovo ClaimsProvider all'elemento ClaimsProviders.
1. Inserire l' **ID applicazione** registrato in precedenza, tra gli elementi di apertura `<Item Key="ClientId">` e di chiusura `</Item>` .
1. Inserire l' **ObjectID dell'applicazione** registrato in precedenza, tra gli elementi di apertura `<Item Key="ApplicationObjectId">` e chiusura `</Item>` .

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="AAD-Common">
            <Metadata>
              <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
              <Item Key="ClientId"></Item>
              <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
              <Item Key="ApplicationObjectId"></Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles> 
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>Caricare i criteri personalizzati

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad B2C.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **carica criteri personalizzati**, quindi caricare i file di criteri TrustFrameworkExtensions.xml modificati.

> [!NOTE]
> La prima volta che il Azure AD profilo tecnico rende persistente l'attestazione nella directory, verifica se l'attributo personalizzato esiste. In caso contrario, crea l'attributo personalizzato.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Creazione di un attributo personalizzato tramite portale di Azure

Gli stessi attributi di estensione sono condivisi tra i criteri predefiniti e quelli personalizzati. Quando si aggiungono attributi personalizzati tramite l'esperienza del portale, questi attributi vengono registrati usando **B2C-Extensions-app** presente in ogni tenant B2C.

È possibile creare questi attributi usando l'interfaccia utente del portale prima o dopo averli usati nei criteri personalizzati. Quando si crea un attributo **loyaltyId** nel portale, è necessario farvi riferimento come indicato di seguito:

|Nome     |Campo di utilizzo |
|---------|---------|
|`extension_loyaltyId`  | Criteri personalizzati|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [API Microsoft Graph](microsoft-graph-operations.md)|

Nell'esempio seguente viene illustrato l'utilizzo di attributi personalizzati in un Azure AD B2C definizione di attestazione dei criteri personalizzata.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

Nell'esempio seguente viene illustrato l'utilizzo di un attributo personalizzato in Azure AD B2C criteri personalizzati in un profilo tecnico, input, output e attestazioni rese permanente.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>Uso di un attributo personalizzato con MS API Graph

L'API Microsoft Graph supporta la creazione e l'aggiornamento di un utente con attributi di estensione. Gli attributi di estensione nel API Graph vengono denominati usando la convenzione `extension_ApplicationClientID_attributename` , dove è l'ID dell'applicazione `ApplicationClientID` **(client)** dell' `b2c-extensions-app` applicazione. Si noti che l' **ID dell'applicazione (client)** come è rappresentato nel nome dell'attributo di estensione non include trattini. Ad esempio:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>Passaggi successivi

Seguire le istruzioni per [aggiungere attestazioni e personalizzare l'input utente usando criteri personalizzati](configure-user-input.md). Questo esempio usa un'attestazione predefinita "City". Per usare un attributo personalizzato, sostituire ' City ' con gli attributi personalizzati.
