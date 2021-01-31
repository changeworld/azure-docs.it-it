---
title: Tenere traccia del comportamento degli utenti con Application Insights
titleSuffix: Azure AD B2C
description: Informazioni su come abilitare i registri eventi in Application Insights da Azure AD B2C percorsi utente.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218554"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Tenere traccia del comportamento degli utenti in Azure Active Directory B2C usando Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) supporta l'invio diretto di dati di evento a [Application Insights](../azure-monitor/app/app-insights-overview.md) usando la chiave di strumentazione fornita per Azure ad B2C. Con un profilo tecnico Application Insights, è possibile ottenere registri eventi dettagliati e personalizzati per i percorsi utente per:

* Ottenere informazioni dettagliate sul comportamento degli utenti.
* Risolvere i problemi relativi ai criteri in ambiente di sviluppo o di produzione.
* Misurare le prestazioni.
* Creare notifiche da Application Insights.

## <a name="overview"></a>Panoramica

Per abilitare i registri eventi personalizzati, è necessario aggiungere un profilo tecnico Application Insights. Nel profilo tecnico si definiscono la chiave di strumentazione Application Insights, il nome dell'evento e le attestazioni da registrare. Per pubblicare un evento, il profilo tecnico viene quindi aggiunto come passaggio di orchestrazione in un [percorso utente](userjourneys.md).

Quando si usa la Application Insights, tenere presente quanto segue:

- Si verifica un breve ritardo, in genere inferiore a cinque minuti, prima che i nuovi log siano disponibili in Application Insights.
- Azure AD B2C consente di scegliere le attestazioni da registrare. Non includere attestazioni con dati personali.
- Per registrare una sessione utente, gli eventi possono essere unificati usando un ID di correlazione. 
- Chiamare il Application Insights profilo tecnico direttamente da un [percorso utente](userjourneys.md) o da un [percorso secondario](subjourneys.md). Non usare Application Insights profilo tecnico come [profilo tecnico di convalida](validation-technical-profile.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Quando si usa Application Insights con Azure AD B2C, è sufficiente creare una risorsa e ottenere la chiave di strumentazione. Per informazioni, vedere [creare una risorsa Application Insights](../azure-monitor/app/create-new-resource.md)

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Assicurarsi di usare la directory che contiene la sottoscrizione di Azure selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene la sottoscrizione. Questo non è il tenant di Azure AD B2C.
3. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure e quindi cercare e selezionare **Application Insights**.
4. Fare clic su **Crea**.
5. Immettere un **nome** per la risorsa.
6. In **Tipo di applicazione** selezionare **Applicazione Web ASP.NET**.
7. In **Gruppo di risorse** selezionare un gruppo esistente o immettere un nome per un nuovo gruppo.
8. Fare clic su **Crea**.
4. Dopo aver creato la risorsa di Application Insights, espandere **Informazioni di base** e prendere nota della chiave di strumentazione.

![Panoramica e chiave di strumentazione di Application Insights](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definire attestazioni

Un'attestazione fornisce un'archiviazione temporanea dei dati durante l'esecuzione di un Azure AD B2C criteri. Lo [schema di attestazioni](claimsschema.md) è la posizione in cui si dichiarano le attestazioni.

1. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema](claimsschema.md). Se l'elemento non esiste, aggiungerlo.
1. Aggiungere le attestazioni seguenti all'elemento **ClaimsSchema**. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Aggiungere nuovi profili tecnici

I profili tecnici possono essere considerati funzioni nei criteri personalizzati. Questa tabella definisce i profili tecnici usati per aprire una sessione e pubblicare gli eventi. La soluzione USA l'approccio di [inclusione del profilo tecnico](technicalprofiles.md#include-technical-profile) . Dove un profilo tecnico include un altro profilo tecnico per modificare le impostazioni o aggiungere nuove funzionalità.

| Profilo tecnico | Attività |
| ----------------- | -----|
| AppInsights-Common | Profilo tecnico comune con il set di configurazione comune. Inclusi, la chiave di strumentazione Application Insights, la raccolta di attestazioni da registrare e la modalità di sviluppo. I seguenti profili tecnici includono il profilo tecnico comune e aggiungono altre attestazioni, ad esempio il nome dell'evento. |
| AppInsights-SignInRequest | Registra un `SignInRequest` evento con un set di attestazioni quando è stata ricevuta una richiesta di accesso. |
| AppInsights-UserSignUp | Registra un `UserSignUp` evento quando l'utente attiva l'opzione di iscrizione in un percorso di iscrizione/accesso. |
| AppInsights-SignInComplete | Registra un `SignInComplete` evento al completamento di un'autenticazione quando un token è stato inviato all'applicazione relying party. |

Aggiungere i profili al file *TrustFrameworkExtensions.xml* dallo starter pack. Aggiungere questi elementi all'elemento **ClaimsProviders**:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Modificare la chiave di strumentazione nel profilo tecnico `AppInsights-Common` con il GUID fornito dalla risorsa di Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Aggiungere i profili tecnici come passaggi di orchestrazione

Chiamare `AppInsights-SignInRequest` come secondo passaggio di orchestrazione per tenere traccia della ricezione di una richiesta di accesso/iscrizione:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Immediatamente *prima* del passaggio di orchestrazione `SendClaims`, aggiungere un nuovo passaggio che chiama `AppInsights-UserSignup`. L'attivazione avviene quando l'utente fa clic sul pulsante di iscrizione in un percorso di iscrizione/accesso.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Immediatamente dopo il passaggio di orchestrazione `SendClaims`, chiamare `AppInsights-SignInComplete`. Questo passaggio mostra un percorso completato correttamente.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Dopo avere aggiunto i nuovi passaggi di orchestrazione, rinumerare i passaggi in sequenza senza ignorare alcun numero intero, da 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Caricare il file, eseguire i criteri e visualizzare gli eventi

Salvare e caricare il file *TrustFrameworkExtensions.xml*. Chiamare quindi i criteri della relying party dall'applicazione o usare **Esegui adesso** nel portale di Azure. Attendere un minuto e gli eventi saranno disponibili in Application Insights.

1. Aprire la risorsa di **Application Insights** nel tenant di Azure Active Directory.
2. Selezionare **utilizzo**, quindi selezionare **eventi**.
3. Impostare **Durante** su **Ultima ora** e **Da** su **3 minuti**.  Potrebbe essere necessario selezionare **Aggiorna** per visualizzare i risultati.

![Pannello degli eventi di utilizzo in Application Insights](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Raccogliere altri dati

Per soddisfare le esigenze aziendali, è consigliabile registrare più attestazioni. Per aggiungere un'attestazione, [definire innanzitutto un'attestazione](#define-claims), quindi aggiungere l'attestazione alla raccolta di attestazioni di input. Le attestazioni aggiunte al profilo tecnico *comune AppInsights* verranno visualizzate in tutti gli eventi. Le attestazioni aggiunte a un profilo tecnico specifico verranno visualizzate solo nell'evento. L'elemento Claim di input contiene gli attributi seguenti:

- **ClaimTypeReferenceId** : riferimento a un tipo di attestazione. 
- **PartnerClaimType** : nome della proprietà visualizzata in Azure Insights. Usare la sintassi `{property:NAME}`, dove `NAME` è la proprietà da aggiungere all'evento.
- **DefaultValue** : valore predefinito da registrare, ad esempio il nome dell'evento. Attestazione utilizzata nel percorso utente, ad esempio il nome del provider di identità. Se l'attestazione è vuota, verrà utilizzato il valore predefinito. Ad esempio, l' `identityProvider` attestazione viene impostata dai profili tecnici della Federazione, ad esempio Facebook. Se l'attestazione è vuota, indica l'accesso dell'utente con un account locale. Quindi, il valore predefinito è impostato su *local*. È anche possibile registrare un [resolver di attestazioni](claim-resolver-overview.md) con un valore contestuale, ad esempio l'ID dell'applicazione o l'indirizzo IP dell'utente.

### <a name="manipulating-claims"></a>Modifica di attestazioni

È possibile usare le [trasformazioni di attestazioni di input](custom-policy-trust-frameworks.md#manipulating-your-claims) per modificare le attestazioni di input o generarne di nuove prima di inviare a Application Insights. Nell'esempio seguente, il profilo tecnico include la trasformazione delle attestazioni di input *CheckIsAdmin* . 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Aggiungere gli eventi

Per aggiungere un evento, creare un nuovo profilo tecnico che includa il profilo tecnico *comune AppInsights* . Aggiungere quindi il profilo tecnico come passaggio di orchestrazione al [percorso utente](custom-policy-trust-frameworks.md#orchestration-steps). Utilizzare la [precondizione](userjourneys.md#preconditions) per attivare l'evento quando si desidera. Ad esempio, segnalare l'evento solo quando gli utenti eseguono l'autenticazione a più fattori.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Ora che si dispone di un profilo tecnico, aggiungere l'evento al percorso utente. Quindi rinumerare i passaggi in sequenza senza ignorare i numeri interi da 1 a N.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Abilitare la modalità sviluppatore

Quando si usa il Application Insights per definire gli eventi, è possibile indicare se è abilitata la modalità sviluppatore. La modalità sviluppatore controlla il modo in cui gli eventi vengono memorizzati nel buffer. In un ambiente di sviluppo con un volume di eventi minimo, l'abilitazione della modalità sviluppatore genera eventi immediatamente inviati a Application Insights. Il valore predefinito è `false`. Non abilitare la modalità di sviluppo negli ambienti di produzione.

Per abilitare la modalità sviluppatore, nel profilo tecnico *comune AppInsights* modificare i metadati in `DeveloperMode` `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Disabilitare telemetria

Per disabilitare i log di Application Insights, nel profilo tecnico *comune di AppInsights* , modificare i `DisableTelemetry` metadati in `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare dashboard KPI personalizzati usando applicazione Azure Insights](../azure-monitor/learn/tutorial-app-dashboards.md). 

::: zone-end