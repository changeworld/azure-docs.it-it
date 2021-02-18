---
title: Tenere traccia del comportamento degli utenti usando Application Insights
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
ms.openlocfilehash: dfe53f67ee9c6645a6dadda58573b892b676b739
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651274"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Tenere traccia del comportamento degli utenti in Azure AD B2C usando Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

In Azure Active Directory B2C (Azure AD B2C), è possibile inviare i dati degli eventi direttamente al [Application Insights](../azure-monitor/app/app-insights-overview.md) usando la chiave di strumentazione fornita per Azure ad B2C. Con un profilo tecnico Application Insights, è possibile ottenere registri eventi dettagliati e personalizzati per i percorsi utente per:

- Ottenere informazioni dettagliate sul comportamento degli utenti.
- Risolvere i problemi relativi ai criteri in ambiente di sviluppo o di produzione.
- Misurare le prestazioni.
- Creare notifiche da Application Insights.

## <a name="overview"></a>Panoramica

Per abilitare i registri eventi personalizzati, aggiungere una Application Insights profilo tecnico. Nel profilo tecnico si definiscono la chiave di strumentazione Application Insights, il nome dell'evento e le attestazioni da registrare. Per pubblicare un evento, aggiungere il profilo tecnico come passaggio di orchestrazione in un [percorso utente](userjourneys.md).

Quando si utilizza Application Insights, tenere presente quanto segue:

- Si verifica un breve ritardo, in genere meno di cinque minuti, prima che i nuovi log siano disponibili in Application Insights.
- Azure AD B2C consente di scegliere le attestazioni da registrare. Non includere attestazioni con dati personali.
- Per registrare una sessione utente, è possibile usare un ID di correlazione per unificare gli eventi.
- Chiamare il Application Insights profilo tecnico direttamente da un [percorso utente](userjourneys.md) o da un [percorso secondario](subjourneys.md). Non usare un profilo tecnico Application Insights come [profilo tecnico di convalida](validation-technical-profile.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Quando si usa Application Insights con Azure AD B2C, è sufficiente creare una risorsa e ottenere la chiave di strumentazione. Per informazioni, vedere [creare una risorsa Application Insights](../azure-monitor/app/create-new-resource.md).

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Assicurarsi di usare la directory con la sottoscrizione di Azure. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory che contiene la sottoscrizione di Azure. Questo tenant non è il tenant del Azure AD B2C.
1. Scegliere **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure, quindi cercare e selezionare **Application Insights**.
1. Selezionare **Crea**.
1. Per **nome** immettere un nome per la risorsa.
1. In **Tipo di applicazione** selezionare **Applicazione Web ASP.NET**.
1. In **Gruppo di risorse** selezionare un gruppo esistente o immettere un nome per un nuovo gruppo.
1. Selezionare **Crea**.
1. Aprire la nuova risorsa Application Insights, espandere **Essentials** e copiare la chiave di strumentazione.

![Screenshot che mostra la chiave di strumentazione nella scheda Panoramica Application Insights.](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definire attestazioni

Un'attestazione fornisce un'archiviazione temporanea dei dati durante l'esecuzione dei criteri di Azure AD B2C. Si dichiarano le attestazioni nell' [elemento ClaimsSchema](claimsschema.md).

1. Aprire il file di estensioni dei criteri, Il file potrebbe essere simile a `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** .
1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non viene visualizzato, aggiungerlo.
1. Trovare l'elemento **ClaimsSchema** . Se l'elemento non viene visualizzato, aggiungerlo.
1. Aggiungere le attestazioni seguenti all'elemento **ClaimsSchema** :

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

I profili tecnici possono essere considerati funzioni nei criteri personalizzati. Queste funzioni usano l'approccio di [inclusione del profilo tecnico](technicalprofiles.md#include-technical-profile) , in cui un profilo tecnico include un altro profilo tecnico e modifica le impostazioni o aggiunge nuove funzionalità. Nella tabella seguente vengono definiti i profili tecnici utilizzati per aprire una sessione e inviare eventi.

| Profilo tecnico | Attività |
| ----------------- | -----|
| AppInsights-Common | Il profilo tecnico comune con la configurazione tipica. Include la chiave di strumentazione Application Insights, una raccolta di attestazioni da registrare e la modalità sviluppatore. Gli altri profili tecnici includono il profilo tecnico comune e aggiungono altre attestazioni, ad esempio il nome dell'evento. |
| AppInsights-SignInRequest | Registra un evento **SignInRequest** con un set di attestazioni quando è stata ricevuta una richiesta di accesso. |
| AppInsights-UserSignUp | Registra un evento **UserSignUp** quando l'utente attiva l'opzione di iscrizione in un percorso di iscrizione o di accesso. |
| AppInsights-SignInComplete | Registra un evento **SignInComplete** al completamento dell'autenticazione, quando un token è stato inviato all'applicazione relying party. |

Aprire il file di *TrustFrameworkExtensions.xml* dallo Starter Pack. Aggiungere i profili tecnici all'elemento **ClaimsProvider** :

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
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

Aggiungere nuovi passaggi di orchestrazione che fanno riferimento ai profili tecnici.

> [!IMPORTANT]
> Dopo avere aggiunto i nuovi passaggi di orchestrazione, rinumerare i passaggi in sequenza senza ignorare alcun numero intero, da 1 a N.

1. Chiamare `AppInsights-SignInRequest` come secondo passaggio di orchestrazione. Questo passaggio tiene traccia della ricezione di una richiesta di iscrizione o accesso.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Prima del `SendClaims` passaggio di orchestrazione, aggiungere un nuovo passaggio che chiama `AppInsights-UserSignup` . Viene attivato quando l'utente seleziona il pulsante di iscrizione in un percorso di iscrizione o di accesso.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
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

1. Dopo il `SendClaims` passaggio di orchestrazione, chiamare `AppInsights-SignInComplete` . Questo passaggio mostra un percorso completato correttamente.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Caricare il file, eseguire i criteri e visualizzare gli eventi

Salvare e caricare il file *TrustFrameworkExtensions.xml*. Chiamare quindi i criteri di relying party dall'applicazione o usare **Esegui ora** nella portale di Azure. Attendere che gli eventi siano disponibili in Application Insights.

1. Aprire la risorsa di **Application Insights** nel tenant di Azure Active Directory.
1. Selezionare **utilizzo**, quindi selezionare **eventi**.
1. Impostare **Durante** su **Ultima ora** e **Da** su **3 minuti**. Potrebbe essere necessario aggiornare la finestra per visualizzare i risultati.

![Screenshot che mostra Application Insights statistiche evento.](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Raccogliere altri dati

Per soddisfare le esigenze aziendali, potrebbe essere necessario registrare più attestazioni. Per aggiungere un'attestazione, [definire innanzitutto un'attestazione](#define-claims), quindi aggiungere l'attestazione alla raccolta di attestazioni di input. Le attestazioni aggiunte al profilo tecnico **comune AppInsights** vengono visualizzate in tutti gli eventi. Le attestazioni aggiunte a un profilo tecnico specifico vengono visualizzate solo nell'evento. L'elemento Claim di input contiene gli attributi seguenti:

- **ClaimTypeReferenceId** è il riferimento a un tipo di attestazione.
- **PartnerClaimType** è il nome della proprietà visualizzato in Azure Insights. Utilizzare la sintassi di `{property:NAME}` , dove `NAME` è una proprietà aggiunta all'evento.
- **DefaultValue** è un valore predefinito da registrare, ad esempio un nome di evento. Se un'attestazione utilizzata nel percorso utente è vuota, viene utilizzato il valore predefinito. Ad esempio, l' `identityProvider` attestazione viene impostata dai profili tecnici della Federazione, ad esempio Facebook. Se l'attestazione è vuota, indica che l'utente ha eseguito l'accesso con un account locale. Quindi, il valore predefinito è impostato su **local**. È anche possibile registrare un [resolver di attestazioni](claim-resolver-overview.md) con un valore contestuale, ad esempio l'ID dell'applicazione o l'indirizzo IP dell'utente.

### <a name="manipulate-claims"></a>Modificare le attestazioni

È possibile usare le [trasformazioni di attestazioni di input](custom-policy-trust-frameworks.md#manipulating-your-claims) per modificare le attestazioni di input o generarne di nuove prima di inviarle a Application Insights. Nell'esempio seguente, il profilo tecnico include la `CheckIsAdmin` trasformazione delle attestazioni di input.

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

Per aggiungere un evento, creare un nuovo profilo tecnico che includa il `AppInsights-Common` profilo tecnico. Aggiungere quindi il nuovo profilo tecnico come passaggio di orchestrazione al [percorso utente](custom-policy-trust-frameworks.md#orchestration-steps). Usare l'elemento [precondition](userjourneys.md#preconditions) per attivare l'evento quando si è pronti. Ad esempio, segnalare l'evento solo quando gli utenti eseguono l'autenticazione a più fattori.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Quando si aggiunge un evento al percorso utente, ricordarsi di rinumerare i passaggi dell'orchestrazione in modo sequenziale.

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

Quando si usa Application Insights per definire gli eventi, è possibile indicare se la modalità sviluppatore è abilitata. La modalità sviluppatore controlla il modo in cui gli eventi vengono memorizzati nel buffer. In un ambiente di sviluppo con un volume di eventi minimo, l'abilitazione della modalità sviluppatore genera eventi immediatamente inviati a Application Insights. Il valore predefinito è `false`. Non abilitare la modalità di sviluppo negli ambienti di produzione.

Per abilitare la modalità sviluppatore, modificare i `DeveloperMode` metadati `true` in nel `AppInsights-Common` profilo tecnico:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Disabilitare telemetria

Per disabilitare i log di Application Insights, modificare i `DisableTelemetry` metadati `true` nel `AppInsights-Common` profilo tecnico:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare dashboard KPI personalizzati usando applicazione Azure Insights](../azure-monitor/learn/tutorial-app-dashboards.md).

::: zone-end
