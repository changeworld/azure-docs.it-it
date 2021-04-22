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
ms.openlocfilehash: 0e6a872891f09f60ea963fa783e6f49dc4e94a54
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861860"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Aggiungere l'accesso condizionale ai flussi utente in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

L'accesso condizionale può essere aggiunto ai flussi utente Azure Active Directory B2C (Azure AD B2C) o ai criteri personalizzati per gestire gli accessi a rischio alle applicazioni. Azure Active Directory (Azure AD) l'accesso condizionale è lo strumento usato da Azure AD B2C per riunire i segnali, prendere decisioni e applicare criteri organizzativi.

![Flusso di accesso condizionale](media/conditional-access-user-flow/conditional-access-flow.png)

L'automazione della valutazione dei rischi con le condizioni dei criteri implica che gli accesso a rischio vengono identificati immediatamente e quindi corretti o bloccati.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Panoramica del servizio

Azure AD B2C valuta ogni evento di accesso e garantisce che tutti i requisiti dei criteri siano soddisfatti prima di concedere l'accesso all'utente. Durante questa **fase di** valutazione, il servizio di accesso condizionale valuta i segnali raccolti dai rilevamenti dei rischi di Identity Protection durante gli eventi di accesso. Il risultato di questo processo di valutazione è un set di attestazioni che indica se l'accesso deve essere concesso o bloccato. Il Azure AD B2C criteri usa queste attestazioni per eseguire un'azione all'interno del flusso utente, ad esempio bloccando l'accesso o sfidando l'utente con una correzione specifica, ad esempio Multi-Factor Authentication (MFA). "Blocca accesso" sostituisce tutte le altre impostazioni.

::: zone pivot="b2c-custom-policy"
L'esempio seguente illustra un profilo tecnico di accesso condizionale usato per valutare la minaccia di accesso.

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

Nella fase **di correzione che** segue, all'utente viene richiesta l'autenticazione a più fattori. Al termine, Azure AD B2C identity protection che la minaccia di accesso identificata è stata corretti e con quale metodo. In questo esempio, Azure AD B2C che l'utente ha completato correttamente la richiesta di autenticazione a più fattori. 

::: zone pivot="b2c-custom-policy"

L'esempio seguente illustra un profilo tecnico di accesso condizionale usato per correggere la minaccia identificata:

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

- **Flusso utente** o **criteri personalizzati che** guidano l'utente nel processo di accesso e iscrizione.
- **Criteri di accesso condizionale** che riunisce i segnali per prendere decisioni e applicare i criteri aziendali. Quando un utente accede all'applicazione tramite un criterio Azure AD B2C, i criteri di accesso condizionale usa i segnali Azure AD Identity Protection per identificare gli accessi a rischio e presentano l'azione correttiva appropriata.
- **Applicazione registrata** che indirizza gli utenti al flusso utente Azure AD B2C o ai criteri personalizzati appropriati.
- [TOR Browser](https://www.torproject.org/download/) per simulare un accesso rischioso.

## <a name="service-limitations-and-considerations"></a>Limitazioni e considerazioni sui servizi

Quando si usa l Azure AD'accesso condizionale, tenere presente quanto segue:

- Identity Protection è disponibile sia per le identità locali che per le identità di social network, ad esempio Google o Facebook. Per le identità di social network, è necessario attivare manualmente l'accesso condizionale. Il rilevamento è limitato perché le credenziali dell'account di social network sono gestite dal provider di identità esterno.
- In Azure AD B2C tenant è disponibile solo un subset [Azure AD criteri di accesso](../active-directory/conditional-access/overview.md) condizionale.


## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Piano tariffario

Azure AD B2C premium **P2** è necessario per creare criteri di accesso rischiosi. I tenant **Premium P1** possono creare criteri basati su criteri basati su posizione, applicazione, utente o gruppo. Per altre informazioni, vedere [Modificare il piano Azure AD B2C prezzi](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Preparare il tenant Azure AD B2C

Per aggiungere criteri di accesso condizionale, disabilitare le impostazioni predefinite per la sicurezza:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
3. In **Servizi di Azure** selezionare **Azure AD B2C**. Oppure usare la casella di ricerca per trovare e **selezionare Azure AD B2C**.
4. Selezionare **Proprietà** e quindi **Gestisci le impostazioni predefinite per la sicurezza**.

   ![Disabilitare le impostazioni predefinite per la sicurezza](media/conditional-access-user-flow/disable-security-defaults.png)

5. In **Abilita impostazioni predefinite per la sicurezza** selezionare **No.**

   ![Impostare l'opzione Abilita le impostazioni predefinite per la sicurezza su No](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Aggiungere un criterio di accesso condizionale

Un criterio di accesso condizionale è un'istruzione if-then di assegnazioni e controlli di accesso. Un criterio di accesso condizionale riunisce i segnali per prendere decisioni e applicare i criteri dell'organizzazione. L'operatore logico tra le assegnazioni è *And*. L'operatore in ogni assegnazione è *Or*.

![Assegnazioni di accesso condizionale](media/conditional-access-user-flow/conditional-access-assignments.png)

Per aggiungere criteri di accesso condizionale:

1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Sicurezza** selezionare **Accesso condizionale (anteprima)** . Viene visualizzata la pagina **Criteri di accesso condizionale**.
1. Selezionare **+ Nuovi criteri**.
1. Immettere un nome per i criteri, ad esempio Blocca accesso *rischioso.*
1. In **Assegnazioni** scegliere **Utenti e gruppi** e quindi selezionare una delle configurazioni supportate seguenti:

    |Includi  |Licenza | Note  |
    |---------|---------|---------|
    |**Tutti gli utenti** | P1, P2 |Se si sceglie di includere **Tutti gli utenti,** questo criterio influirà su tutti gli utenti. Per assicurarsi di non bloccarsi, escludere l'account amministrativo scegliendo **Escludi**, selezionando **Ruoli** directory e quindi **Amministratore** globale nell'elenco. È anche possibile selezionare **Utenti e gruppi e** quindi selezionare l'account nell'elenco Selezionare gli utenti **esclusi.**  | 
 
1. Selezionare **App o azioni cloud** e quindi Selezionare le **app.** Cercare [l'applicazione relying party.](tutorial-register-applications.md)

1. Selezionare **Condizioni** e quindi selezionare una delle condizioni seguenti. Ad esempio, selezionare **Rischio di accesso** e Livelli **di** rischio Alto, **Medio** **e** Basso.
    
    |Condizione  |Licenza  |Note  |
    |---------|---------|---------|
    |**Rischio utente**|P2|Il rischio utente rappresenta la probabilità che una determinata identità o account venga compromessa.|
    |**Rischio di accesso**|P2|Il rischio di accesso rappresenta la probabilità che una determinata richiesta di autenticazione non sia autorizzata dal proprietario dell'identità.|
    |**Piattaforme**|Non supportato| Caratterizzato dal sistema operativo in esecuzione in un dispositivo. Per altre informazioni, vedere [Piattaforme per dispositivi](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms).|
    |**Percorsi**|P1, P2|Le località denominate possono includere le informazioni di rete IPv4 pubbliche, il paese o l'area geografica o aree sconosciute che non sono mappate a paesi o aree geografiche specifiche. Per ulteriori informazioni, vedere [Sedi](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations). |
 
1. In **Controlli di accesso** selezionare **Concedi**. Selezionare quindi se bloccare o concedere l'accesso:
    
    |Opzione  |Licenza |Nota  |
    |---------|---------|---------|
    |**Blocca accesso**|P1, P2| Impedisce l'accesso in base alle condizioni specificate nei criteri di accesso condizionale.|
    |**Concedere l'accesso** **con Richiedi autenticazione a più fattori**|P1, P2|In base alle condizioni specificate in questi criteri di accesso condizionale, l'utente deve eseguire l'autenticazione Azure AD B2C a più fattori.|

1. In **Abilita criteri** selezionare una delle opzioni seguenti:
    
    |Opzione  |Licenza |Nota  |
    |---------|---------|---------|
    |**Solo report**|P1, P2| Solo report consente agli amministratori di valutare l'impatto dei criteri di accesso condizionale prima di abilitarli nel proprio ambiente. È consigliabile controllare i criteri con questo stato e determinare l'impatto per gli utenti finali senza richiedere l'autenticazione a più fattori o bloccare gli utenti. Per altre informazioni, vedere [Esaminare i risultati dell'accesso condizionale nel report di controllo](#review-conditional-access-outcomes-in-the-audit-report)|
    | **Sì**| P1, P2| I criteri di accesso vengono valutati e non applicati. |
    | **Disattivato** | P1, P2| I criteri di accesso non sono attivati e non hanno alcun effetto sugli utenti. |

1. Per abilitare il criterio di accesso condizionale di test, selezionare **Crea**.

## <a name="add-conditional-access-to-a-user-flow"></a>Aggiungere l'accesso condizionale a un flusso utente

Dopo aver aggiunto i criteri di Azure AD, abilitare l'accesso condizionale nel flusso utente o nei criteri personalizzati. Quando si abilita l'accesso condizionale, non è necessario specificare un nome di criterio.

Più criteri di accesso condizionale possono essere applicati a un singolo utente in qualsiasi momento. In questo caso, i criteri di controllo di accesso più restrittivi hanno la precedenza. Ad esempio, se un criterio richiede l'autenticazione a più fattori (MFA), mentre l'altro blocca l'accesso, l'utente verrà bloccato.

## <a name="conditional-access-template-1-sign-in-risk-based-conditional-access"></a>Modello di accesso condizionale 1: Accesso condizionale basato sul rischio di accesso

La maggior parte degli utenti ha un comportamento normale monitorabile. In condizioni che esulano dalla normalità, potrebbe essere rischioso consentire loro semplicemente di accedere. È possibile bloccare l'utente o semplicemente chiedere di eseguire l'autenticazione a più fattori per dimostrare di essere effettivamente ciò che afferma di essere.

Un rischio di accesso rappresenta la probabilità che una determinata richiesta di autenticazione non sia autorizzata dal proprietario dell'identità. Le organizzazioni con licenze P2 possono creare criteri di accesso condizionale che incorporano Azure AD Identity Protection [di rischio di accesso.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk) Tenere presente le [limitazioni relative ai rilevamenti di Identity Protection per B2C.](https://docs.microsoft.com/azure/active-directory-b2c/identity-protection-investigate-risk?pivots=b2c-user-flow#service-limitations-and-considerations)

Se viene rilevato un rischio, gli utenti possono eseguire l'autenticazione a più fattori per eseguire autonomamente la correzione e chiudere l'evento di accesso rischioso per evitare inutili problemi per gli amministratori.

Le organizzazioni devono scegliere una delle opzioni seguenti per abilitare un criterio di accesso condizionale basato sul rischio di accesso che richiede l'autenticazione a più fattori quando il rischio di accesso è medio O alto.

### <a name="enable-with-conditional-access-policy"></a>Abilitare con i criteri di accesso condizionale

1. Accedere al **portale di Azure**.
2. Passare **a** Azure AD B2C  >  **sicurezza**  >  **condizionale**.
3. Selezionare **Nuovi criteri**.
4. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
5. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti**.
   2. In **Escludi** selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o gli account critici dell'organizzazione. 
   3. Selezionare **Operazione completata**.
6. In **App o azioni cloud**  >  **Includi** selezionare Tutte le app **cloud.**
7. In **Condizioni**  >  **Rischio di accesso** impostare **Configura** su **Sì.** In **Selezionare il livello di rischio di accesso a cui verranno applicati i criteri** 
   1. Selezionare **Alto** e **Medio.**
   2. Selezionare **Fine**.
8. In **Controlli di accesso** > **Concedi**, selezionare **Concedi accesso**, **Richiedi autenticazione a più fattori** e selezionare **Seleziona**.
9. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
10. Selezionare **Crea** per creare e abilitare i criteri.

### <a name="enable-with-conditional-access-apis"></a>Abilitare con le API di accesso condizionale

Per creare criteri di accesso condizionale basati sul rischio di accesso con LE API di accesso condizionale, vedere la documentazione relativa alle [API di accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-apis#graph-api).

Il modello seguente può essere usato per creare criteri di accesso condizionale con il nome visualizzato "CA002: Require MFA for medium+ sign-in risk" in modalità solo report.

```json
{
    "displayName": "Template 1: Require MFA for medium+ sign-in risk",
    "state": "enabledForReportingButNotEnforced",
    "conditions": {
        "signInRiskLevels": [ "high" ,
            "medium"
        ],
        "applications": {
            "includeApplications": [
                "All"
            ]
        },
        "users": {
            "includeUsers": [
                "All"
            ],
            "excludeUsers": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ]
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ]
    }
}
```

## <a name="enable-multi-factor-authentication-optional"></a>Abilitare l'autenticazione a più fattori (facoltativo)

Quando si aggiunge l'accesso condizionale a un flusso utente, considerare l'uso di **Multi-Factor Authentication (MFA).** Gli utenti possono usare un'unica time code tramite SMS o voce o una password una sola volta tramite posta elettronica per l'autenticazione a più fattori. Le impostazioni dell'autenticazione a più fattori sono indipendenti dalle impostazioni di accesso condizionale. È possibile impostare l'autenticazione a più fattori su **Always On** (Sempre attiva) in modo che l'autenticazione a più fattori sia obbligatoria indipendentemente dalla configurazione dell'accesso condizionale. In alternativa, è possibile impostare l'autenticazione a più fattori su **Condizionale**, in modo che l'autenticazione a più fattori sia obbligatoria solo se richiesta da un criterio di accesso condizionale attivo.

> [!IMPORTANT]
> Se i criteri di accesso condizionale concedono l'accesso con MFA ma l'utente non ha registrato un numero di telefono, l'utente potrebbe essere bloccato.

::: zone pivot="b2c-user-flow"

Per abilitare l'accesso condizionale per un flusso utente, verificare che la versione supporti l'accesso condizionale. Queste versioni del flusso utente sono contrassegnate come **Consigliata**.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

1. In **Servizi di Azure** selezionare **Azure AD B2C**. Oppure usare la casella di ricerca per trovare e **selezionare Azure AD B2C**.

1. In **Criteri** selezionare **Flussi utente**. Selezionare quindi il flusso utente.

1. Selezionare **Proprietà** e assicurarsi che il flusso utente supporti l'accesso condizionale cercando l'impostazione con etichetta **Accesso condizionale**.
 
   ![Configurare l'autenticazione a più fattori e l'accesso condizionale nelle proprietà](media/conditional-access-user-flow/add-conditional-access.png)

1. Nella sezione **Multi-Factor Authentication** selezionare il metodo **MFA** desiderato e quindi in Imposizione **MFA** selezionare **Condizionale (scelta consigliata).**
 
1. Nella sezione **Accesso condizionale** selezionare la casella di controllo **Applica i criteri di accesso condizionale**.

1. Selezionare **Salva**.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Aggiungere l'accesso condizionale ai criteri

1. Ottenere l'esempio di criteri di accesso condizionale in [GitHub.](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access)
1. In ogni file sostituire la stringa `yourtenant` con il nome del tenant Azure AD B2C servizio. Ad esempio, se il nome del tenant B2C è *contosob2c*, tutte le istanze di `yourtenant.onmicrosoft.com` diventano `contosob2c.onmicrosoft.com` .
1. Caricare i file dei criteri.

## <a name="test-your-custom-policy"></a>Testare i criteri personalizzati

1. Selezionare il `B2C_1A_signup_signin_with_ca` criterio o per aprire la relativa pagina di `B2C_1A_signup_signin_with_ca_whatif` panoramica. Selezionare quindi **Esegui flusso utente.** In **Applicazione** selezionare *webapp1*. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Copiare l'URL in **Esegui l'endpoint del flusso utente**.

1. Per simulare un accesso rischioso, aprire [Tor Browser](https://www.torproject.org/download/) e usare l'URL copiato nel passaggio precedente per accedere all'app registrata.

1. Immettere le informazioni richieste nella pagina di accesso e quindi provare ad accedere. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato. Nel jwt.ms token decodificato si dovrebbe vedere che l'accesso è stato bloccato.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la pagina di panoramica e quindi selezionare **Esegui flusso utente.** In **Applicazione** selezionare *webapp1*. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.

1. Copiare l'URL in **Esegui l'endpoint del flusso utente**.

1. Per simulare un accesso rischioso, aprire [Tor Browser](https://www.torproject.org/download/) e usare l'URL copiato nel passaggio precedente per accedere all'app registrata.

1. Immettere le informazioni richieste nella pagina di accesso e quindi provare ad accedere. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato. Nel jwt.ms token decodificato si dovrebbe vedere che l'accesso è stato bloccato.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Esaminare i risultati dell'accesso condizionale nel report di controllo

Per esaminare il risultato di un evento di accesso condizionale:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

3. In **Servizi di Azure** selezionare **Azure AD B2C**. Oppure usare la casella di ricerca per trovare e selezionare **Azure AD B2C**.

4. In **Attività** selezionare **Log di controllo**.

5. Filtrare il log di controllo impostando **Categoria** su **B2C** e **Tipo di risorsa attività** su **IdentityProtection**. Selezionare quindi **Applica**.

6. Esaminare l'attività di controllo per un massimo di sette giorni. Sono inclusi i tipi di attività seguenti:

   - **Valutazione dei criteri di accesso condizionale**: questa voce del log di controllo indica che durante un'autenticazione è stata eseguita una valutazione di accesso condizionale.
   - **Utente correttivo:** questa voce indica che la concessione o i requisiti di un criterio di accesso condizionale sono stati soddisfatti dall'utente finale e questa attività è stata segnalata al motore dei rischi per attenuare (ridurre il rischio) dell'utente.

7. Selezionare una voce del log per la **valutazione dei criteri di accesso condizionale** nell'elenco per aprire la pagina **Dettagli attività: log di controllo**, che mostra gli identificatori dei log di controllo, unitamente a queste informazioni nella sezione **Altri dettagli**:

   - **ConditionalAccessResult:** concessione richiesta dalla valutazione dei criteri condizionali.
   - **AppliedPolicies:** elenco di tutti i criteri di accesso condizionale in cui sono state soddisfatte le condizioni e i criteri sono ON.
   - **ReportingPolicies:** elenco dei criteri di accesso condizionale impostati sulla modalità solo report e in cui sono state soddisfatte le condizioni.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare l'interfaccia utente in un flusso utente di Azure AD B2C](customize-ui-with-html.md)
