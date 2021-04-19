---
title: Profili tecnici di accesso condizionale nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni di riferimento sui criteri personalizzati per i profili tecnici di accesso condizionale in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebdc1c9c92f6e3debf08cb640e46424c4ad9d5ad
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107721889"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico di accesso condizionale in un Azure Active Directory B2C criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) l'accesso condizionale è lo strumento usato da Azure AD B2C per riunire i segnali, prendere decisioni e applicare criteri organizzativi. L'automazione della valutazione dei rischi con le condizioni dei criteri significa che gli accessi a rischio sono identificati immediatamente e vengono risolti o bloccati.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. **L'attributo** handler deve contenere il nome completo dell'assembly del gestore di protocollo usato Azure AD B2C:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

L'esempio seguente illustra un profilo tecnico di accesso condizionale:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Valutazione dell'accesso condizionale

Per ogni accesso, Azure AD B2C valuta tutti i criteri e garantisce che tutti i requisiti siano soddisfatti prima di concedere l'accesso all'utente. "Blocca accesso" sostituisce tutte le altre impostazioni di configurazione. La **modalità** di valutazione del profilo tecnico di accesso condizionale valuta i segnali raccolti Azure AD B2C durante l'accesso con un account locale. Il risultato del profilo tecnico di accesso condizionale è un set di attestazioni derivate dalla valutazione dell'accesso condizionale. Il Azure AD B2C criteri usa queste attestazioni in un passaggio di orchestrazione successivo per eseguire un'azione, ad esempio bloccare l'utente o richiedere all'utente l'autenticazione a più fattori. Per questa modalità è possibile configurare le opzioni seguenti.

### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Tipo operazione | Sì | Deve essere **Evaluation.**  |

### <a name="input-claims"></a>Attestazioni di input

**L'elemento InputClaims** contiene un elenco di attestazioni da inviare all'accesso condizionale. È anche possibile eseguire il mapping del nome dell'attestazione al nome definito nel profilo tecnico di accesso condizionale.

| ClaimReferenceId | Obbligatoria | Tipo di dati | Descrizione |
| --------- | -------- | ----------- |----------- |
| UserId | Sì | string | Identificatore dell'utente che accede. |
| AuthenticationMethodsUsed | Sì |stringCollection | Elenco di metodi usati dall'utente per l'accesso. Valori possibili: `Password` , e `OneTimePasscode` . |
| IsFederated | Sì |boolean | Indica se un utente ha eseguito o meno l'accesso con un account federato. Il valore deve essere `false`. |
| IsMfaRegistered | Sì |boolean | Indica se l'utente ha già registrato un numero di telefono per l'autenticazione a più fattori. |


**L'elemento InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuove prima di inviarle al servizio di accesso condizionale.

### <a name="output-claims"></a>Attestazioni di output

**L'elemento OutputClaims** contiene un elenco di attestazioni generate da ConditionalAccessProtocolProvider. È anche possibile eseguire il mapping del nome dell'attestazione al nome definito di seguito.

| ClaimReferenceId | Obbligatoria | Tipo di dati | Descrizione |
| --------- | -------- | ----------- |----------- |
| Problematiche | Sì |stringCollection | Elenco di azioni per correggere la minaccia identificata. Valori possibili: `block` |
| MultiConditionalAccessStatus | Sì | stringCollection |  |

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="example-evaluation"></a>Esempio: Valutazione

L'esempio seguente mostra un profilo tecnico di accesso condizionale usato per valutare la minaccia di accesso.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Soluzione

La **modalità di correzione** del profilo tecnico di accesso condizionale informa Azure AD B2C che la minaccia identificata per l'accesso è stata corretti. Le opzioni seguenti possono essere configurate per la modalità di correzione.

### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Tipo operazione | Sì | Deve essere **Correzione**.  |

### <a name="input-claims"></a>Attestazioni di input

**L'elemento InputClaims** contiene un elenco di attestazioni da inviare all'accesso condizionale. È anche possibile eseguire il mapping del nome dell'attestazione al nome definito nel profilo tecnico di accesso condizionale.

| ClaimReferenceId | Obbligatoria | Tipo di dati | Descrizione |
| --------- | -------- | ----------- |----------- |
| ProblemiSatisfied | Sì | stringCollection| Elenco di sfide soddisfatte per correggere la minaccia identificata come ritorno dalla modalità di valutazione, attestazione delle sfide.|


**L'elemento InputClaimsTransformations** può contenere una raccolta di **elementi InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuove prima di chiamare il servizio di accesso condizionale.

### <a name="output-claims"></a>Attestazioni di output

Il provider del protocollo di accesso condizionale non restituisce **outputClaims,** quindi non è necessario specificare attestazioni di output. È tuttavia possibile includere attestazioni che non vengono restituite dal provider del protocollo di accesso condizionale, purché si sia impostato `DefaultValue` l'attributo .

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="example-remediation"></a>Esempio: correzione

L'esempio seguente illustra un profilo tecnico di accesso condizionale usato per correggere la minaccia di accesso.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [aggiungere l'accesso condizionale ai flussi utente in Azure Active Directory B2C](conditional-access-user-flow.md).
