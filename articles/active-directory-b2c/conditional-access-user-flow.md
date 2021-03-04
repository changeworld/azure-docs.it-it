---
title: Aggiungere l'accesso condizionale a un flusso utente in Azure AD B2C
description: Informazioni su come aggiungere l'accesso condizionale ai flussi utente di Azure AD B2C. Configurare le impostazioni di autenticazione a più fattori (MFA) e i criteri di accesso condizionale nei flussi utente per applicare i criteri e correggere gli accessi a rischio.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6325a890ea297a3aa2bdad76a1d95c10448a7b61
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033914"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Aggiungere l'accesso condizionale ai flussi utente in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

L'accesso condizionale può essere aggiunto ai flussi utente Azure Active Directory B2C (Azure AD B2C) o ai criteri personalizzati per gestire gli accessi a rischio per le applicazioni. L'accesso condizionale Azure Active Directory (Azure AD) è lo strumento usato da Azure AD B2C per riunire i segnali, prendere decisioni e applicare i criteri dell'organizzazione.

![Flusso di accesso condizionale](media/conditional-access-user-flow/conditional-access-flow.png)

L'automazione della valutazione dei rischi con le condizioni dei criteri significa che gli accessi a rischio vengono identificati immediatamente e quindi corretti o bloccati.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Panoramica del servizio

Azure AD B2C valuta ogni evento di accesso e garantisce che tutti i requisiti dei criteri siano soddisfatti prima di concedere l'accesso utente. Durante questa fase di **valutazione** , il servizio di accesso condizionale valuta i segnali raccolti dai rilevamenti dei rischi di Identity Protection durante gli eventi di accesso. Il risultato di questo processo di valutazione è un set di attestazioni che indica se è necessario concedere o bloccare l'accesso. I criteri di Azure AD B2C usano queste attestazioni per eseguire un'azione all'interno del flusso utente, ad esempio il blocco dell'accesso o l'esecuzione di una correzione per l'utente con una correzione specifica come multi-factor authentication. "Blocca accesso" esegue l'override di tutte le altre impostazioni.

::: zone pivot="b2c-custom-policy"
Nell'esempio seguente viene illustrato un profilo tecnico di accesso condizionale usato per valutare la minaccia di accesso.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

Nella fase di **correzione** che segue, l'utente è in grado di richiedere l'autenticazione a più fattori. Al termine, Azure AD B2C informa Identity Protection che la minaccia di accesso identificato è stata risolta e con quale metodo. In questo esempio Azure AD B2C segnala che l'utente ha completato correttamente la richiesta di autenticazione a più fattori. 

::: zone pivot="b2c-custom-policy"

Nell'esempio seguente viene illustrato un profilo tecnico di accesso condizionale usato per correggere la minaccia identificata:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

## <a name="components-of-the-solution"></a>Componenti della soluzione

Questi sono i componenti che abilitano l'accesso condizionale in Azure AD B2C:

- **Flusso utente** o **criterio personalizzato** che guida l'utente attraverso il processo di accesso e iscrizione.
- **Criteri di accesso condizionale** che raggruppano i segnali per prendere decisioni e applicare i criteri dell'organizzazione. Quando un utente accede all'applicazione tramite un criterio di Azure AD B2C, i criteri di accesso condizionale usano Azure AD Identity Protection segnali per identificare gli accessi a rischio e presentano l'azione correttiva appropriata.
- **Applicazione registrata** che indirizza gli utenti al flusso di utenti Azure ad B2C appropriato o ai criteri personalizzati.
- [Browser Tor](https://www.torproject.org/download/) per simulare un accesso rischioso.

## <a name="service-limitations-and-considerations"></a>Limitazioni e considerazioni sui servizi

Quando si usa l'accesso condizionale Azure AD, tenere presente quanto segue:

- Identity Protection è disponibile sia per le identità locali che per quelle di social networking, ad esempio Google o Facebook. Per le identità di social networking, è necessario attivare manualmente l'accesso condizionale. Il rilevamento è limitato perché le credenziali dell'account di social networking sono gestite dal provider di identità esterno.
- In Azure AD B2C tenant è disponibile solo un subset di [Azure ad criteri di accesso condizionale](../active-directory/conditional-access/overview.md) .


## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Piano tariffario

Azure AD B2C **Premium P2** è necessario per creare criteri di accesso rischiosi. I tenant **Premium P1** possono creare un criterio basato su criteri basati su località, applicazioni, utenti o gruppi. Per altre informazioni, vedere [modificare il piano tariffario Azure ad B2C](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Preparare il tenant di Azure AD B2C

Per aggiungere un criterio di accesso condizionale, disabilitare le impostazioni predefinite di sicurezza:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
3. In **servizi di Azure** selezionare **Azure ad B2C**. In alternativa, utilizzare la casella di ricerca per trovare e selezionare **Azure ad B2C**.
4. Selezionare **Proprietà** e quindi **Gestisci le impostazioni predefinite per la sicurezza**.

   ![Disabilitare le impostazioni predefinite per la sicurezza](media/conditional-access-user-flow/disable-security-defaults.png)

5. In **Abilita impostazioni predefinite sicurezza** selezionare **No**.

   ![Impostare l'opzione Abilita le impostazioni predefinite per la sicurezza su No](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Aggiungere un criterio di accesso condizionale

Un criterio di accesso condizionale è un'istruzione if-then di assegnazioni e controlli di accesso. Un criterio di accesso condizionale raggruppa i segnali per prendere decisioni e applicare i criteri aziendali. L'operatore logico tra le assegnazioni è *e*. L'operatore in ogni assegnazione è *o*.

![Assegnazioni di accesso condizionale](media/conditional-access-user-flow/conditional-access-assignments.png)

Per aggiungere un criterio di accesso condizionale:

1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Sicurezza** selezionare **Accesso condizionale (anteprima)** . Viene visualizzata la pagina **Criteri di accesso condizionale**.
1. Selezionare **+ Nuovi criteri**.
1. Immettere un nome per il criterio, ad esempio *Blocca accesso rischioso*.
1. In **assegnazioni** scegliere **utenti e gruppi** e quindi selezionare una delle configurazioni supportate seguenti:

    |Includi  |Licenza | Note  |
    |---------|---------|---------|
    |**Tutti gli utenti** | P1, P2 |Se si sceglie di includere **tutti gli utenti**, questi criteri avranno effetto su tutti gli utenti. Per evitare di bloccarsi, escludere l'account amministrativo scegliendo **Escludi**, selezione **ruoli directory** e quindi **amministratore globale** nell'elenco. È anche possibile selezionare **utenti e gruppi** e quindi selezionare l'account nell'elenco **Seleziona utenti esclusi** .  | 
 
1. Selezionare **app Cloud o azioni**, quindi **selezionare app**. Cercare l' [applicazione relying party](tutorial-register-applications.md).

1. Selezionare **condizioni** e quindi selezionare una delle condizioni seguenti. Ad esempio, selezionare **rischio di accesso** e livelli di rischio **alto**, **medio** e **basso** .
    
    |Condizione  |Licenza  |Note  |
    |---------|---------|---------|
    |**Rischio utente**|P2|Il rischio utente rappresenta la probabilità che un'identità o un account specificato venga compromesso.|
    |**Rischio di accesso**|P2|Il rischio di accesso rappresenta la probabilità che una determinata richiesta di autenticazione non sia autorizzata dal proprietario dell'identità.|
    |**Piattaforme**|Non supportato| Caratterizzato dal sistema operativo in esecuzione su un dispositivo. Per altre informazioni, vedere [piattaforme del dispositivo](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms).|
    |**Percorsi**|P1, P2|Le località denominate possono includere le informazioni sulla rete IPv4 pubbliche, il paese o l'area geografica o aree sconosciute che non sono mappate a specifici paesi o aree geografiche. Per ulteriori informazioni, vedere [Sedi](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations). |
 
1. In **Controlli di accesso** selezionare **Concedi**. Quindi selezionare se bloccare o concedere l'accesso:
    
    |Opzione  |Licenza |Nota  |
    |---------|---------|---------|
    |**Blocca accesso**|P1, P2| Impedisce l'accesso in base alle condizioni specificate nei criteri di accesso condizionale.|
    |**Concessione dell'accesso** con **Richiedi autenticazione** a più fattori|P1, P2|In base alle condizioni specificate nei criteri di accesso condizionale, è necessario che l'utente attraversi Azure AD B2C autenticazione a più fattori.|

1. In **Abilita criteri** selezionare una delle opzioni seguenti:
    
    |Opzione  |Licenza |Nota  |
    |---------|---------|---------|
    |**Solo report**|P1, P2| Report: consente agli amministratori di valutare l'impatto dei criteri di accesso condizionale prima di abilitarli nel proprio ambiente. Si consiglia di controllare i criteri con questo stato e di determinare l'effetto degli utenti finali senza richiedere l'autenticazione a più fattori o bloccare gli utenti. Per altre informazioni, vedere [esaminare i risultati dell'accesso condizionale nel report di controllo](#review-conditional-access-outcomes-in-the-audit-report)|
    | **Sì**| P1, P2| Il criterio di accesso viene valutato e non applicato. |
    | **Disattivato** | P1, P2| Il criterio di accesso non è attivato e non ha alcun effetto sugli utenti. |

1. Per abilitare il criterio di accesso condizionale di test, selezionare **Crea**.

## <a name="add-conditional-access-to-a-user-flow"></a>Aggiungere l'accesso condizionale a un flusso utente

Dopo aver aggiunto i criteri di accesso condizionale Azure AD, abilitare l'accesso condizionale nel flusso utente o nel criterio personalizzato. Quando si Abilita l'accesso condizionale, non è necessario specificare un nome di criterio.

È possibile che più criteri di accesso condizionale si applichino a un singolo utente in qualsiasi momento. In questo caso, i criteri di controllo degli accessi più severi hanno la precedenza. Se, ad esempio, un criterio richiede multi-factor authentication, mentre l'altro blocca l'accesso, l'utente verrà bloccato.

## <a name="enable-multi-factor-authentication-optional"></a>Abilitare l'autenticazione a più fattori (facoltativo)

Quando si aggiunge l'accesso condizionale a un flusso utente, prendere in considerazione l'uso di **multi-factor authentication**. Gli utenti possono usare un codice monouso tramite SMS o Voice oppure una password monouso tramite posta elettronica per l'autenticazione a più fattori. Le impostazioni dell'autenticazione a più fattori sono indipendenti dalle impostazioni di accesso condizionale. È possibile impostare l'autenticazione a più fattori su **Always On** (Sempre attiva) in modo che l'autenticazione a più fattori sia obbligatoria indipendentemente dalla configurazione dell'accesso condizionale. In alternativa, è possibile impostare l'autenticazione a più fattori su **Condizionale**, in modo che l'autenticazione a più fattori sia obbligatoria solo se richiesta da un criterio di accesso condizionale attivo.

> [!IMPORTANT]
> Se il criterio di accesso condizionale concede l'accesso con l'autenticazione a più fattori, ma l'utente non ha registrato un numero di telefono, l'utente potrebbe essere bloccato.

::: zone pivot="b2c-user-flow"

Per abilitare l'accesso condizionale per un flusso utente, assicurarsi che la versione supporti l'accesso condizionale. Queste versioni del flusso utente sono contrassegnate come **Consigliata**.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

1. In **servizi di Azure** selezionare **Azure ad B2C**. In alternativa, utilizzare la casella di ricerca per trovare e selezionare **Azure ad B2C**.

1. In **Criteri** selezionare **Flussi utente**. Selezionare quindi il flusso utente.

1. Selezionare **Proprietà** e assicurarsi che il flusso utente supporti l'accesso condizionale cercando l'impostazione con etichetta **accesso condizionale**.
 
   ![Configurare l'autenticazione a più fattori e l'accesso condizionale nelle proprietà](media/conditional-access-user-flow/add-conditional-access.png)

1. Nella sezione **multi-factor authentication** selezionare il **Metodo** di autenticazione a più fattori desiderato, quindi in **imposizione** autenticazione a più fattori selezionare **condizionale (scelta consigliata)**.
 
1. Nella sezione **Accesso condizionale** selezionare la casella di controllo **Applica i criteri di accesso condizionale**.

1. Selezionare **Salva**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Aggiungere l'accesso condizionale ai criteri

1. Ottenere l'esempio di criteri di accesso condizionale in [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).
1. In ogni file sostituire la stringa `yourtenant` con il nome del tenant del Azure ad B2C. Ad esempio, se il nome del tenant B2C è *contosob2c*, tutte le istanze di `yourtenant.onmicrosoft.com` diventano `contosob2c.onmicrosoft.com` .
1. Caricare i file dei criteri.

## <a name="test-your-custom-policy"></a>Testare i criteri personalizzati

1. Selezionare il `B2C_1A_signup_signin_with_ca` `B2C_1A_signup_signin_with_ca_whatif` criterio o per aprire la relativa pagina di panoramica. Selezionare quindi **Esegui flusso utente**. In **Applicazione** selezionare *webapp1*. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Copiare l'URL in **Esegui l'endpoint del flusso utente**.

1. Per simulare un accesso rischioso, aprire il [browser Tor](https://www.torproject.org/download/) e usare l'URL copiato nel passaggio precedente per accedere all'app registrata.

1. Immettere le informazioni richieste nella pagina di accesso e quindi provare ad accedere. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato. Nel token decodificato jwt.ms si noterà che l'accesso è stato bloccato.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina Panoramica, quindi selezionare **Esegui flusso utente**. In **Applicazione** selezionare *webapp1*. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.

1. Copiare l'URL in **Esegui l'endpoint del flusso utente**.

1. Per simulare un accesso rischioso, aprire il [browser Tor](https://www.torproject.org/download/) e usare l'URL copiato nel passaggio precedente per accedere all'app registrata.

1. Immettere le informazioni richieste nella pagina di accesso e quindi provare ad accedere. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato. Nel token decodificato jwt.ms si noterà che l'accesso è stato bloccato.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Esaminare i risultati dell'accesso condizionale nel report di controllo

Per esaminare il risultato di un evento di accesso condizionale:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

3. In **servizi di Azure** selezionare **Azure ad B2C**. In alternativa, utilizzare la casella di ricerca per trovare e selezionare **Azure ad B2C**.

4. In **Attività** selezionare **Log di controllo**.

5. Filtrare il log di controllo impostando **Categoria** su **B2C** e **Tipo di risorsa attività** su **IdentityProtection**. Selezionare quindi **Applica**.

6. Esaminare l'attività di controllo per un massimo di sette giorni. Sono inclusi i tipi di attività seguenti:

   - **Valutazione dei criteri di accesso condizionale**: questa voce del log di controllo indica che durante un'autenticazione è stata eseguita una valutazione di accesso condizionale.
   - Correggi **utente**: questa voce indica che la concessione o i requisiti di un criterio di accesso condizionale sono stati soddisfatti dall'utente finale e questa attività è stata segnalata al motore di rischio per attenuare (ridurre il rischio di) dell'utente.

7. Selezionare una voce del log per la **valutazione dei criteri di accesso condizionale** nell'elenco per aprire la pagina **Dettagli attività: log di controllo**, che mostra gli identificatori dei log di controllo, unitamente a queste informazioni nella sezione **Altri dettagli**:

   - **ConditionalAccessResult**: concessione richiesta dalla valutazione dei criteri condizionali.
   - **AppliedPolicies**: elenco di tutti i criteri di accesso condizionale in cui sono state soddisfatte le condizioni e i criteri sono attivati.
   - **ReportingPolicies**: elenco dei criteri di accesso condizionale impostati sulla modalità di sola segnalazione e sulla posizione in cui sono state soddisfatte le condizioni.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare l'interfaccia utente in un flusso utente di Azure AD B2C](customize-ui-with-html.md)
