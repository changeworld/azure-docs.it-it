---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: Specificare l'elemento TrustFrameworkPolicy di un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bf1cc197a7d6977ccb6ef69e157d9f8a76a58d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470734"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un criterio personalizzato è rappresentato come uno o più file in formato XML che fanno riferimento l'uno all'altro in una catena gerarchica. Gli elementi XML definiscono gli elementi del criterio, come lo schema delle attestazioni, le trasformazioni delle attestazioni, le definizioni di contenuto, i provider di attestazioni, i profili tecnici, il percorso utente e le procedure di orchestrazione. Ogni file dei criteri è definito all'interno dell'elemento di primo livello **TrustFrameworkPolicy** di un file di criteri.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


L'elemento **TrustFrameworkPolicy** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Sì | La versione dello schema che deve essere usata per eseguire il criterio. Il valore deve essere `0.3.0.0`. |
| TenantObjectId | No | Identificatore di oggetto univoco del tenant di Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Sì | L'identificatore univoco del tenant a cui appartiene questo criterio. |
| PolicyId | Sì | L'identificatore univoco del criterio. Questo identificatore deve essere preceduto da *B2C_1A_* |
| PublicPolicyUri | Sì | L'URI per il criterio, ovvero la combinazione dell'ID del tenant e l'ID del criterio. |
| DeploymentMode | No | Valori possibili: `Production` , o `Development` . `Production` è l'impostazione predefinita. Usare questa proprietà per eseguire il debug del criterio. Per ulteriori informazioni, vedere [raccolta di log](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | No | Endpoint utilizzato per la registrazione. Il valore deve essere impostato su `urn:journeyrecorder:applicationinsights` se l'attributo esiste. Per ulteriori informazioni, vedere [raccolta di log](troubleshoot-with-application-insights.md). |


Nell'esempio seguente viene illustrato come specificare l'elemento **TrustFrameworkPolicy**:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

L'elemento **TrustFrameworkPolicy** contiene gli attributi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| Identificatore di un criterio di base. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | Blocchi predefiniti dei criteri. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Raccolta di provider di attestazioni. |
| [UserJourneys](userjourneys.md) | 0:1 | Raccolta di percorsi utente. |
| [RelyingParty](relyingparty.md) | 0:1 | Definizione di un criterio di relying party. |

Per ereditare un criterio da un altro criterio, un elemento **BasePolicy** deve essere dichiarato sotto l'elemento **TrustFrameworkPolicy** del file di criteri. L'elemento **BasePolicy** è un riferimento ai criteri di base da cui deriva questo criterio.

L'elemento **BasePolicy** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identificatore del tenant di Azure AD B2C. |
| PolicyId | 1:1 | Identificatore del criterio padre. |


L'esempio seguente mostra come specificare un criterio di base. Questo criterio **B2C_1A_TrustFrameworkExtensions** deriva dal criterio **B2C_1A_TrustFrameworkBase**.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

