---
title: Percorsi secondari in Azure Active Directory B2C | Microsoft Docs
description: Specificare l'elemento subjourneys di un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97386868"
---
# <a name="sub-journeys"></a>Percorsi secondari

I percorsi secondari possono essere usati per organizzare e semplificare il flusso dei passaggi di orchestrazione in un percorso utente. I [percorsi utente](userjourneys.md) specificano percorsi espliciti attraverso cui un criterio consente a un'applicazione relying party di ottenere le attestazioni desiderate per un utente. L'utente segue questi percorsi per recuperare le attestazioni che devono essere presentate alla relying party. In altre parole, i percorsi utente definiscono la logica di business di ciò a cui accede un utente finale mentre il framework dell'esperienza di gestione delle identità di Azure AD B2C elabora la richiesta. Un percorso utente è costituito da una sequenza di orchestrazione da seguire per garantire l'esito positivo di una transazione. L'elemento [ClaimsExchange](userjourneys.md#claimsexchanges) di un passaggio di orchestrazione è associato a un singolo [profilo tecnico](technicalprofiles.md) eseguito.

Un percorso secondario è un raggruppamento di passaggi di orchestrazione che possono essere richiamati in qualsiasi punto all'interno di un percorso utente. È possibile utilizzare i percorsi secondari per creare sequenze di passaggi riutilizzabili o implementare la diramazione per rappresentare meglio la logica di business.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Diramazione percorso utente

I percorsi secondari si comportano come i [percorsi utente](userjourneys.md), perché entrambi sono rappresentati come una sequenza di orchestrazione che deve essere seguita per una transazione corretta. I percorsi utente possono essere richiamati in modo autonomo e richiedono l'esecuzione di un passaggio SendClaims. I percorsi secondari sono componenti dei percorsi utente e non possono essere richiamati in modo indipendente e vengono sempre chiamati da un percorso utente.

Il componente principale della creazione di rami consiste nel consentire una migliore elaborazione della logica di business in un percorso utente. I passaggi di orchestrazione comuni sono raggruppati in singole parti da richiamare separatamente. Un percorso secondario può semplificare un percorso in cui più passaggi di orchestrazione sono abbinati (con le stesse precondizioni). Un percorso secondario viene chiamato solo da un percorso utente, ma non deve chiamare un altro percorso secondario.

Esistono due tipi di percorsi secondari:

- **Call** : restituisce il controllo al chiamante. Il percorso secondario viene eseguito, quindi il controllo viene restituito al passaggio di orchestrazione attualmente in esecuzione nel percorso utente.
- **Transfer** -trasferisce il controllo al percorso secondario (diramazione irreversibile). Il percorso secondario deve avere un passaggio SendClaims per riportare le attestazioni all'applicazione relying party.

## <a name="example-scenarios"></a>Scenari di esempio

### <a name="call-sub-journey"></a>Chiama Sub-Journey

Una chiamata sub Journey è utile negli scenari seguenti:

- Controllo Age: per il controllo dell'età, sono presenti molti componenti condivisi tra i percorsi utente. La diramazione consente a di compilare gli elementi comuni in componenti condivisibili.  
- Consenso parentale: la diramazione consente di agevolare la progettazione del consenso dei genitori, consentendo di accedere alle attestazioni del percorso utente eseguito dal minore, insieme alla possibilità di creare rami in un percorso utente di consenso padre dopo aver individuato l'utente che richiede il consenso. 
- Iscriversi per eseguire l'accesso: si consideri uno scenario in cui un utente esiste già nella directory, ma potrebbe aver dimenticato che in realtà ha creato un account. In tal caso, potrebbe essere auspicabile che invece di indicare all'utente che le credenziali immesse esistono già e che l'utente possa riavviare il percorso in modo che i criteri siano in grado di eseguire un passaggio da un flusso di iscrizione a un flusso di accesso per tale utente.  

### <a name="transfer-sub-journey"></a>Sottopercorso di trasferimento

Un sub Transfer è utile negli scenari seguenti:

- Visualizzazione di una pagina di blocco.
- Test a/B tramite il routing della richiesta a un percorso secondario per l'esecuzione e l'emissione di un token.

## <a name="adding-a-subjourneys-element"></a>Aggiunta di un elemento subjourneys

Di seguito è riportato un esempio di un `SubJourney` elemento di tipo `Call` , che restituisce il controllo al percorso utente.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Di seguito è riportato un esempio di un `SubJourney` elemento di tipo `Transfer` , che restituisce un token all'applicazione relying party.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>Richiama un passaggio del percorso secondario

Un nuovo passaggio di orchestrazione di tipo `InvokeSubJourney` viene utilizzato per eseguire un percorso secondario. Di seguito è riportato un esempio che Mostra tutti gli elementi di esecuzione di questo passaggio dell'orchestrazione.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Un percorso secondario non deve chiamare un altro percorso secondario.

## <a name="components"></a>Componenti

Per definire i percorsi secondari supportati dal criterio, aggiungere un elemento **Subjourneys** sotto l'elemento di livello principale del file di criteri.

L'elemento **Subjourneys** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Sottopercorso | 1:n | Un percorso secondario che definisce tutti i costrutti necessari per un flusso utente completo. |

L'elemento **Subjourneys** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Id | Sì | Identificatore del percorso secondario che può essere utilizzato dal percorso utente per fare riferimento al percorso secondario nel criterio. L'elemento **SubJourneyReferenceId** dell'elemento [candidato](userjourneys.md#journeylist) punta a questo attributo. |
| Tipo | Sì | Valori possibili: `Call` , o `Transfer` . Per altre informazioni, vedere [diramazione del percorso utente](#user-journey-branching)|

L'elemento **subjourney** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Sequenza di orchestrazione da seguire per garantire l'esito positivo di una transazione. Ogni percorso utente è costituito da un elenco ordinato di passaggi di orchestrazione eseguiti in sequenza. Se un passaggio non riesce, la transazione ha esito negativo. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Per l'elenco completo degli elementi dei passaggi di orchestrazione, vedere [UserJourneys](userjourneys.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [UserJourneys](userjourneys.md)