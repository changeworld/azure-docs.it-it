---
title: Configurare Azure AD B2C provider di identità dell'account locale
titleSuffix: Azure AD B2C
description: Definire i tipi di identità usati da usare per iscriversi o accedere (posta elettronica, nome utente, numero di telefono) nel tenant del Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d9eb28ad19d53df542769a89b839668bbb205e30
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256107"
---
# <a name="set-up-the-local-account-identity-provider"></a>Configurare il provider di identità dell'account locale

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C offre diversi modi in cui gli utenti possono autenticare un utente. Gli utenti possono accedere a un account locale usando il nome utente e la password, la verifica telefonica (nota anche come autenticazione senza password) o i provider di identità basati su social network. L'iscrizione tramite posta elettronica è abilitata per impostazione predefinita nelle impostazioni del provider di identità dell'account locale. 

Questo articolo descrive il modo in cui gli utenti creano gli account locali a questo tenant Azure AD B2C. Per le identità sociali o aziendali, in cui l'identità dell'utente è gestita da un provider di identità federato come Facebook e Google, vedere [aggiungere un provider di identità](add-identity-provider.md).

## <a name="email-sign-in"></a>Accesso alla posta elettronica

Con l'opzione di posta elettronica, gli utenti possono accedere con l'indirizzo di posta elettronica e la password seguenti:

- **Accesso**, agli utenti viene richiesto di fornire la posta elettronica e la password.
- Per l' **iscrizione**, agli utenti verrà richiesto di specificare un indirizzo di posta elettronica, che verrà verificato al momento dell'iscrizione (facoltativo) e diventerà l'ID di accesso. L'utente immette quindi eventuali altre informazioni richieste nella pagina di iscrizione, ad esempio nome visualizzato, nome e cognome. Quindi selezionare continua per creare l'account.
- **Reimpostazione della password**, gli utenti devono immettere e verificare la posta elettronica, dopo la quale l'utente può reimpostare la password

![Esperienza di iscrizione o accesso tramite posta elettronica](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Accesso con nome utente

Con l'opzione User, gli utenti possono accedere con un nome utente e una password:

- **Accesso**: agli utenti viene richiesto di fornire il nome utente e la password.
- **Iscrizione**: agli utenti verrà richiesto un nome utente, che diventerà l'ID di accesso. Agli utenti verrà richiesto anche un indirizzo di posta elettronica, che verrà verificato al momento dell'iscrizione. L'indirizzo di posta elettronica verrà usato durante un flusso di reimpostazione della password. L'utente immette tutte le altre informazioni richieste nella pagina di iscrizione, ad esempio nome visualizzato, nome e cognome. L'utente seleziona quindi continua per creare l'account.
- **Reimpostazione della password**: gli utenti devono immettere il nome utente e l'indirizzo di posta elettronica associato. È necessario verificare l'indirizzo di posta elettronica, dopo il quale l'utente può reimpostare la password.

![Esperienza di iscrizione o accesso al nome utente](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Accesso tramite telefono (anteprima)

L'autenticazione senza password è un tipo di autenticazione in cui un utente non deve eseguire l'accesso con la password. Con l'iscrizione e l'accesso tramite telefono, l'utente può iscriversi all'app usando un numero di telefono come identificatore di accesso primario. L'utente avrà la seguente esperienza durante l'iscrizione e l'accesso:

- **Accesso**: se l'utente dispone di un account esistente con numero di telefono come identificatore, l'utente immette il numero di telefono e seleziona *Accedi*. Per confermare il paese e il numero di telefono, selezionare *continue (continua*). verrà inviato un codice di verifica una sola volta al telefono. L'utente immette il codice di verifica e seleziona *continua* per accedere.
- **Iscrizione**: se l'utente non dispone già di un account per l'applicazione, è possibile crearne uno facendo clic sul collegamento *Iscriviti ora* . 
    1. Viene visualizzata una pagina di iscrizione, in cui l'utente seleziona il *paese*, immette il numero di telefono e seleziona *Invia codice*. 
    1. Viene inviato un codice di verifica monouso al numero di telefono dell'utente. L'utente immette il *codice di verifica* nella pagina di iscrizione, quindi seleziona *Verifica codice*. Se l'utente non riesce a recuperare il codice, può selezionare *Invia nuovo codice*. 
    1. L'utente immette tutte le altre informazioni richieste nella pagina di iscrizione, ad esempio nome visualizzato, nome e cognome. Selezionare quindi Continua.
    1. Successivamente, all'utente viene richiesto di fornire un **messaggio di posta elettronica di ripristino**. L'utente immette il proprio indirizzo di posta elettronica e quindi seleziona *Invia codice di verifica*. Viene inviato un codice alla posta in arrivo dell'utente, che è possibile recuperare e immettere nella casella codice di verifica. L'utente seleziona quindi verifica codice.
    1. Una volta verificato il codice, l'utente seleziona *Crea* per creare il proprio account. 

![Esperienza di iscrizione o accesso tramite telefono](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Prezzi

Le password monouso vengono inviate agli utenti tramite messaggi di testo SMS. A seconda dell'operatore di rete mobile, è possibile che vengano addebitati i costi per ogni messaggio inviato. Per informazioni sui prezzi, vedere la sezione relativa agli **addebiti separati** di [Azure Active Directory B2C prezzi](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> Multi-factor authentication è disabilitato per impostazione predefinita quando si configura un flusso utente con l'iscrizione tramite telefono. È possibile abilitare l'autenticazione a più fattori nei flussi utente con l'iscrizione tramite telefono, ma poiché un numero di telefono viene usato come identificatore primario, il codice di posta elettronica monouso è l'unica opzione disponibile per il secondo fattore di autenticazione.

### <a name="phone-recovery"></a>Ripristino telefonico

Quando si Abilita l'iscrizione e l'accesso tramite telefono per i flussi utente, è anche consigliabile abilitare la funzionalità di posta elettronica di ripristino. Con questa funzionalità, un utente può specificare un indirizzo di posta elettronica che può essere usato per recuperare il proprio account quando non dispone del telefono. Questo indirizzo di posta elettronica viene usato solo per il recupero dell'account. Non può essere usato per l'accesso.

- Quando il messaggio di posta elettronica **di ripristino è attivo, viene** richiesto di verificare un messaggio di posta elettronica di backup per la prima volta. Un utente che non ha fornito un messaggio di posta elettronica di ripristino prima di viene richiesto di verificare un messaggio di posta elettronica di backup durante l'accesso successivo.

- Quando il messaggio di posta elettronica di ripristino è **disattivato**, un utente che effettua l'iscrizione o l'accesso non Visualizza il messaggio di posta elettronica di ripristino.
 
Gli screenshot seguenti illustrano il flusso di ripristino del telefono:

![Flusso utente per il ripristino telefonico](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Accesso tramite telefono o posta elettronica (anteprima)

È possibile scegliere di combinare l' [accesso tramite telefono](#phone-sign-in-preview)e l' [accesso tramite posta elettronica](#email-sign-in). Nella pagina di iscrizione o di accesso, l'utente può digitare un numero di telefono o un indirizzo di posta elettronica. In base all'input dell'utente, Azure AD B2C porta l'utente al flusso corrispondente. 

![Esperienza di iscrizione o accesso tramite telefono o posta elettronica](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Configurare le impostazioni del provider di identità dell'account locale

È possibile configurare i provider di identità locali disponibili per l'utilizzo all'interno di un flusso utente abilitando o disabilitando i provider (posta elettronica, nome utente o numero di telefono).  È possibile avere più di un provider di identità locale abilitato a livello di tenant.

Un flusso utente può essere configurato solo per usare uno dei provider di identità dell'account locale in qualsiasi momento. Ogni flusso utente può avere un set di provider di identità dell'account locale diverso, se ne è stato abilitato più di uno a livello di tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. In **Gestisci** selezionare **Provider di identità**.
1. Nell'elenco dei provider di identità, selezionare **Account locale**.
1. Nella pagina **Configura IDP locale** selezionare almeno uno dei tipi di identità consentiti che i consumer possono usare per creare gli account locali nel tenant Azure ad B2C.
1. Selezionare **Salva**.

## <a name="configure-your-user-flow"></a>Configurare il flusso utente

1. Nel menu a sinistra del portale di Azure selezionare **Azure ad B2C**.
1. In **Criteri** selezionare **Flussi utente (criteri)** .
1. Selezionare il flusso utente per cui si vuole configurare l'esperienza di iscrizione e accesso.
1. Selezionare i **provider di identità**
1. In **account locali** selezionare una delle opzioni seguenti: **iscrizione tramite posta elettronica**, iscrizione con  **ID utente**, **iscrizione telefonica**, **iscrizione via posta elettronica** o **Nessuna** voce.

### <a name="enable-the-recovery-email-prompt"></a>Abilita la richiesta di posta elettronica di ripristino

Se si **sceglie l'opzione per l'iscrizione** tramite telefono **/posta elettronica** , abilitare la richiesta di posta elettronica di ripristino.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In Azure AD B2C, in **criteri**, selezionare **flussi utente**.
1. Selezionare il flusso utente nell'elenco.
1. In **Impostazioni** selezionare **Proprietà**.
1. Accanto a **abilitare la richiesta di posta elettronica di ripristino per l'iscrizione al numero di telefono e l'accesso (anteprima)**, selezionare:
   - **Su** per visualizzare la richiesta di posta elettronica di ripristino durante l'iscrizione e l'accesso.
   - **Disattivato** per nascondere la richiesta di posta elettronica di ripristino.
1. Selezionare **Salva**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Ottenere il pacchetto Starter

I criteri personalizzati sono un set di file XML che vengono caricati nel tenant di Azure AD B2C per definire i percorsi utente. Sono disponibili Starter Pack con diversi criteri predefiniti. Scaricare lo Starter Pack pertinente: 

- [Accesso alla posta elettronica](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Accesso con nome utente](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Accesso tramite telefono](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Selezionare i criteri di relying party [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) . 
- [Accesso tramite telefono o posta elettronica](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Selezionare i criteri di relying party [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) .

Dopo aver scaricato lo Starter Pack.

1. In ogni file sostituire la stringa `yourtenant` con il nome del tenant del Azure ad B2C. Ad esempio, se il nome del tenant B2C è *contosob2c*, tutte le istanze di `yourtenant.onmicrosoft.com` diventano `contosob2c.onmicrosoft.com` .

1. Completare i passaggi della sezione [aggiungere ID applicazione ai criteri personalizzati](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy) di [Introduzione ai criteri personalizzati in Azure Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy). Ad esempio, aggiornare `/phone-number-passwordless/` **`Phone_Email_Base.xml`** con gli **ID dell'applicazione (client)** delle due applicazioni registrate quando si completano i prerequisiti, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.
1. Caricare i file dei criteri

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere provider di identità esterni](add-identity-provider.md)
- [Creare un flusso utente](tutorial-create-user-flows.md)
