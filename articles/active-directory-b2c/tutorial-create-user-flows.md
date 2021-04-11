---
title: 'Esercitazione: creare flussi utente e criteri personalizzati-Azure Active Directory B2C'
description: Seguire questa esercitazione per informazioni su come creare flussi utente e criteri personalizzati nel portale di Azure per abilitare l'iscrizione, l'accesso e la modifica del profilo utente per le applicazioni in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e41c1e74dbe428ee38d4480a1587050b7f96a55f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226227"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Esercitazione: Creare flussi utente in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Nelle applicazioni possono essere presenti flussi utente che consentono agli utenti di effettuare l'iscrizione, accedere o gestire il proprio profilo. È possibile creare più flussi utente di tipi diversi nel proprio tenant di Azure Active Directory B2C (Azure AD B2C) e usarli nelle applicazioni in base alle esigenze. I flussi utente possono essere usati per più applicazioni.

::: zone pivot="b2c-user-flow"
Un flusso utente consente di determinare il modo in cui gli utenti interagiscono con l'applicazione quando eseguono operazioni come l'accesso, l'iscrizione, la modifica di un profilo o la reimpostazione della password. In questo articolo vengono illustrate le operazioni seguenti:
::: zone-end

::: zone pivot="b2c-custom-policy"
I [criteri personalizzati](custom-policy-overview.md) sono file di configurazione che definiscono il comportamento del tenant di Azure Active Directory B2C (Azure AD B2C). In questo articolo vengono illustrate le operazioni seguenti:
::: zone-end

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Abilitare la reimpostazione self-service delle password
> * Creare un flusso utente di modifica del profilo

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> Il modo in cui si fa riferimento alle versioni dei flussi utente è stato cambiato. In precedenza, venivano offerte le versioni V1 (pronte per l'ambiente di produzione) e le versioni V 1.1 e V2 (anteprima). Ora questi flussi utente sono stati consolidati nelle versioni **Consigliata** (anteprima di nuova generazione) e **Standard** (disponibile a livello generale). Tutti i flussi utente delle anteprime legacy V 1.1 e V2 verranno deprecati entro il **1° agosto 2021**. Per informazioni dettagliate, vedere [Versioni dei flussi utente in Azure AD B2C](user-flow-versions.md).
::: zone-end

## <a name="prerequisites"></a>Prerequisiti

::: zone pivot="b2c-user-flow"
- In assenza di un tenant, [creare un tenant di Azure AD B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.
- [Registrare l'applicazione](tutorial-register-applications.md) nel tenant creato in modo che possa comunicare con Azure AD B2C.
::: zone-end

::: zone pivot="b2c-custom-policy"
- In assenza di un tenant, [creare un tenant di Azure AD B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.
- [Registrare l'applicazione](tutorial-register-applications.md) nel tenant creato in modo che possa comunicare con Azure AD B2C.
- Completare la procedura descritta in [Configurare l'iscrizione e l'accesso con un account Facebook](identity-provider-facebook.md) per configurare un'applicazione Facebook. Sebbene la disponibilità di un'applicazione Facebook non sia un requisito da soddisfare per poter usare criteri personalizzati, viene usata in questa procedura dettagliata per dimostrare come abilitare l'accesso dai social network nei criteri personalizzati.
::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creare un flusso utente di iscrizione e accesso

Il flusso utente di iscrizione e accesso consente di gestire le esperienze di iscrizione e accesso tramite una singola configurazione. Gli utenti dell'applicazione vengono indirizzati sul percorso corretto a seconda del contesto.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

    ![Tenant B2C, riquadro Directory e sottoscrizione, portale di Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Flussi utente** e quindi **Nuovo flusso utente**.

    ![Pagina Flussi utente nel portale con il pulsante Nuovo flusso utente evidenziato](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Iscrizione e accesso**.

    ![Pagina Selezionare un tipo di flusso utente con il flusso Iscrizione e accesso evidenziato](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. In **Selezionare una versione** selezionare **Consigliata**, quindi selezionare **Crea**. Per altre informazioni sulle versioni dei flussi utente, vedere [qui](user-flow-versions.md).

    ![Pagina Crea un flusso utente nel portale di Azure con proprietà evidenziate](./media/tutorial-create-user-flows/select-version.png)

1. Immettere un **nome** per il flusso utente. Ad esempio, *signupsignin1*.
1. Per **Provider di identità** selezionare **Iscrizione posta elettronica**.
1. Per **Attributi e attestazioni utente**, scegliere le attestazioni e gli attributi che si vogliono raccogliere e inviare all'utente durante l'iscrizione. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Paese/Area geografica**, **Nome visualizzato** e **Codice postale**. Fare clic su **OK**.

    ![Pagina di selezione di attributi e attestazioni utente con tre attestazioni selezionate](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Fare clic su **Crea** per aggiungere il flusso utente. Viene automaticamente aggiunto al nome il prefisso *B2C_1*.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi selezionare **Iscriversi adesso**.

    ![Pagina Esegui il flusso utente nel portale con il pulsante Esegui il flusso utente evidenziato](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Immettere un indirizzo di posta elettronica valido, fare clic su **Invia codice di verifica**, immettere il codice di verifica ricevuto e quindi selezionare **Verifica codice**.
1. Immettere una nuova password e confermarla.
1. Selezionare il paese e l'area geografica, immettere il nome visualizzato desiderato, immettere un codice postale e quindi fare clic su **Crea**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.
1. Ora si può eseguire di nuovo il flusso utente e dovrebbe essere possibile accedere con l'account creato. Il token restituito include le attestazioni selezionate di paese/area geografica, nome e codice postale.

> [!NOTE]
> L'esperienza "Esegui il flusso utente" non è attualmente compatibile con l'URL di risposta di tipo applicazione a pagina singola con il flusso di codice di autorizzazione. Per usare l'esperienza "Esegui il flusso utente" con questi tipi di app, registrare un URL di risposta di tipo "Web" e abilitare il flusso implicito come descritto [qui](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

Per abilitare la [reimpostazione della password self-service](add-password-reset-policy.md) per il flusso utente di iscrizione o accesso:

1. Selezionare il flusso utente di iscrizione o accesso creato.
1. In **Impostazioni** nel menu a sinistra selezionare **Proprietà**.
1. In **complessità password** selezionare **reimpostazione password self-service**.
1. Selezionare **Salva**.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui il flusso utente**.
1. Dalla pagina di iscrizione o accesso selezionare **password dimenticata**.
1. Verificare l'indirizzo di posta elettronica dell'account creato in precedenza e quindi selezionare **continue (continua**).
1. Ora è possibile modificare la password per l'utente. Cambiare la password e selezionare **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

## <a name="create-a-profile-editing-user-flow"></a>Creare un flusso utente di modifica del profilo

Se si vuole abilitare nell'applicazione la modifica del profilo da parte degli utenti, usare un flusso utente di modifica del profilo.

1. Nel menu della pagina di panoramica del tenant Azure AD B2C selezionare **Flussi utente** e quindi **Nuovo flusso utente**.
1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Modifica del profilo**. 
1. In **Selezionare una versione** selezionare **Consigliata**, quindi selezionare **Crea**.
1. Immettere un **nome** per il flusso utente. Ad esempio, *profileediting1*.
1. Per **Provider di identità** selezionare **Accesso all'account locale**.
2. Per **Attributi utente** scegliere gli attributi che vuole che il cliente possa modificate nel proprio profilo. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Nome visualizzato** e **Posizione**. Fare clic su **OK**.
3. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi accedere con l'account creato in precedenza.
1. A questo punto si ha la possibilità di modificare il nome visualizzato e la posizione dell'utente. Fare clic su **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.
::: zone-end

::: zone pivot="b2c-custom-policy"
> [!TIP]
> Questo articolo illustra come configurare manualmente il tenant. È possibile automatizzare l'intero processo da questo articolo. L'automazione consente di distribuire il Azure AD B2C [Starter Pack SocialAndLocalAccountsWithMFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack), che fornirà i percorsi di iscrizione e accesso, di reimpostazione della password e di modifica del profilo. Per automatizzare la procedura dettagliata riportata di seguito, visitare l' [app di installazione Framework dell'esperienza](https://aka.ms/iefsetup) e seguire le istruzioni.


## <a name="add-signing-and-encryption-keys"></a>Aggiungere le chiavi di firma e di crittografia

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Nella pagina Panoramica, nella sezione **Criteri**, selezionare **Framework dell'esperienza di gestione delle identità**.

### <a name="create-the-signing-key"></a>Creazione della chiave di firma

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Generate`.
1. In **Nome** immettere `TokenSigningKeyContainer`. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
1. Per **Tipo di chiave** selezionare **RSA**.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

### <a name="create-the-encryption-key"></a>Creazione della chiave di crittografia

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Generate`.
1. In **Nome** immettere `TokenEncryptionKeyContainer`. È possibile che il prefisso `B2C_1A`_ venga aggiunto automaticamente.
1. Per **Tipo di chiave** selezionare **RSA**.
1. In **Uso chiave** selezionare **Crittografia**.
1. Selezionare **Create** (Crea).

### <a name="create-the-facebook-key"></a>Creazione della chiave Facebook

Aggiungere la [Chiave privata app](identity-provider-facebook.md) dell'applicazione Facebook come chiave dei criteri. È possibile usare la chiave privata dell'applicazione creata nell'ambito dei prerequisiti di questo articolo.

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Per **Nome** immettere `FacebookSecret`. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
1. In **Segreto** immettere la *Chiave privata app* dell'applicazione Facebook da developers.facebook.com. Questo valore è la chiave privata, non l'ID applicazione.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

## <a name="register-identity-experience-framework-applications"></a>Registrare le applicazioni del framework dell'esperienza di gestione delle identità

Azure AD B2C richiede di registrare due applicazioni che verranno usate per registrare e far accedere gli utenti con account locali: L'API Web *IdentityExperienceFramework* e l'app nativa *ProxyIdentityExperienceFramework*, con autorizzazione delegata dall'app IdentityExperienceFramework. Gli utenti possono iscriversi con un indirizzo di posta elettronica o un nome utente e una password per accedere alle applicazioni registrate nel tenant, in modo da creare un "account locale". Gli account locali esistono solo nel tenant di Azure AD B2C.

È necessario registrare queste due applicazioni nel tenant di Azure AD B2C una sola volta.

### <a name="register-the-identityexperienceframework-application"></a>Registrare l'applicazione IdentityExperienceFramework

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza **registrazioni app** .

1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Per **Nome** immettere `IdentityExperienceFramework`.
1. In **Tipi di account supportati** selezionare **Account solo in questa directory organizzativa**.
1. In **URI di reindirizzamento** selezionare **Web** e quindi immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il nome di dominio del tenant di Azure AD B2C.
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)** , che sarà necessario in un passaggio successivo.

Esporre quindi l'API aggiungendo un ambito:

1. Nel menu a sinistra, in **Gestisci**, selezionare **esporre un'API**.
1. Selezionare **Aggiungi un ambito** e quindi **Salva e continua** per accettare l'URI dell'ID applicazione predefinito.
1. Immettere i valori seguenti per creare un ambito che consenta l'esecuzione di criteri personalizzati nel tenant di Azure AD B2C:
    * **Nome ambito**: `user_impersonation`
    * **Nome visualizzato per il consenso amministratore**: `Access IdentityExperienceFramework`
    * **Descrizione del consenso amministratore**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Selezionare **Aggiungi ambito**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrare l'applicazione ProxyIdentityExperienceFramework

1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Per **Nome** immettere `ProxyIdentityExperienceFramework`.
1. In **Tipi di account supportati** selezionare **Account solo in questa directory organizzativa**.
1. In **URI di reindirizzamento** usare l'elenco a discesa per selezionare **Client pubblico/nativo (per dispositivi mobili e desktop)** .
1. In **URI di reindirizzamento** immettere `myapp://auth`.
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)** , che sarà necessario in un passaggio successivo.

Specificare quindi che l'applicazione deve essere considerata come un client pubblico:

1. Nel menu a sinistra, in **Gestisci**, selezionare **Autenticazione**.
1. In **Impostazioni avanzate**, nella sezione **Consenti flussi client pubblici** , impostare **Abilita i flussi per dispositivi mobili e desktop seguenti** su **Sì**. Verificare che nel manifesto dell'applicazione sia impostato **"allowPublicClient": true**. 
1. Selezionare **Salva**.

È ora necessario concedere le autorizzazioni all'ambito dell'API esposto in precedenza nella registrazione di *IdentityExperienceFramework*:

1. Nel menu a sinistra, in **Gestisci**, selezionare autorizzazioni per le **API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **Le mie API** e quindi selezionare l'applicazione **IdentityExperienceFramework**.
1. In **Autorizzazione** selezionare l'ambito **user_impersonation** definito in precedenza.
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere con il passaggio successivo.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)** .
1. Selezionare l'account amministratore attualmente connesso oppure accedere con un account nel tenant di Azure AD B2C a cui sia stato assegnato almeno il ruolo di *amministratore applicazione cloud*.
1. Selezionare **Accetto**.
1. Selezionare **Aggiorna**, quindi verificare che "concesso per..." viene visualizzato in **stato** per gli ambiti-offline_access, openid e user_impersonation. La propagazione delle autorizzazioni potrebbe richiedere alcuni minuti.

* * *

## <a name="custom-policy-starter-pack"></a>Pacchetto Starter per i criteri personalizzati

I criteri personalizzati sono costituiti da un set di file XML che viene caricato nel tenant di Azure AD B2C per definire i profili tecnici e i percorsi utente. Per ridurre i tempi, sono già disponibili pacchetti Starter con alcuni criteri predefiniti. Ogni pacchetto Starter contiene il numero minimo di profili tecnici e percorsi utente necessario per conseguire gli scenari descritti:

- **LocalAccounts** - Consente l'uso solo di account locali.
- **SocialAccounts** - Consente l'uso solo di account di social networking (o federati).
- **SocialAndLocalAccounts** - Consente sia l'uso di account locali che di account di social networking.
- **SocialAndLocalAccountsWithMFA** - Consente l'uso di opzioni di social networking, locali e di autenticazione a più fattori.

Ogni pacchetto Starter contiene:

- **File di base** - Sono necessarie alcune modifiche alla configurazione di base. Esempio: *TrustFrameworkBase.xml*
- **Extension file** - Questo è il file in cui deve essere apportata la maggior parte delle modifiche di configurazione. Esempio: *TrustFrameworkExtensions.xml*
- **File relying party** - File di attività specifiche chiamati dall'applicazione. Esempi: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

In questo articolo si modificheranno i file dei criteri personalizzati XML presenti nel pacchetto Starter **SocialAndLocalAccounts**. Se occorre un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.

### <a name="get-the-starter-pack"></a>Ottenere il pacchetto Starter

Ottenere i pacchetti Starter dei criteri personalizzati da GitHub e aggiornare quindi i file XML del pacchetto Starter SocialAndLocalAccounts con il nome del tenant di Azure AD B2C.

1. È possibile [scaricare il file ZIP](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o clonare il repository:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. In tutti i file nella directory **SocialAndLocalAccounts** sostituire la stringa `yourtenant` con il nome del tenant di Azure AD B2C.

    Se, ad esempio, il nome del tenant B2C è *contosotenant*, tutte le istanze di `yourtenant.onmicrosoft.com` diventeranno `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Aggiungere gli ID dell'applicazione al criterio personalizzato

Aggiungere gli ID delle applicazioni al file delle estensioni *TrustFrameworkExtensions.xml*.

1. Aprire `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** e trovare l'elemento `<TechnicalProfile Id="login-NonInteractive">`.
1. Sostituire entrambe le istanze di `IdentityExperienceFrameworkAppId` con l'ID dell'applicazione IdentityExperienceFramework creata in precedenza.
1. Sostituire entrambe le istanze di `ProxyIdentityExperienceFrameworkAppId` con l'ID dell'applicazione ProxyIdentityExperienceFramework creata in precedenza.
1. Salvare il file.

## <a name="upload-the-policies"></a>Caricare i criteri

1. Selezionare la voce di menu **Identity Experience Framework** nel tenant B2C nel portale di Azure.
1. Selezionare **Carica criteri personalizzati**.
1. Caricare i file dei criteri, nell'ordine in cui sono elencati:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Mentre si caricano i file, Azure aggiunge il prefisso `B2C_1A_` a ognuno di essi.

> [!TIP]
> Se l'editor XML in uso supporta la convalida, convalidare i file rispetto allo schema XML `TrustFrameworkPolicy_0.3.0.0.xsd` che si trova nella directory radice del pacchetto Starter. La convalida dello schema XML identifica gli errori prima del caricamento.

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. In **Criteri personalizzati** selezionare **B2C_1A_signup_signin**.
1. Per **Seleziona applicazione** nella pagina di panoramica dei criteri personalizzati, selezionare l'applicazione Web denominata *webapp1* registrata in precedenza.
1. Verificare che l'**URL di risposta** sia `https://jwt.ms`.
1. Selezionare **Esegui adesso**.
1. Iscriversi usando un indirizzo di posta elettronica.
1. Selezionare di nuovo **Esegui adesso**.
1. Accedere con lo stesso account per verificare che la configurazione sia corretta.

## <a name="add-facebook-as-an-identity-provider"></a>Aggiungere Facebook come provider di identità

Come specificato nella sezione [Prerequisiti](#prerequisites), Facebook *non* è necessario per l'uso dei criteri personalizzati, ma viene usato qui per dimostrare come poter abilitare l'accesso ai criteri personalizzati tramite un account di social networking federato.

1. Nel file `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** sostituire il valore di `client_id` con l'ID dell'applicazione Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Caricare il file *TrustFrameworkExtensions.xml* nel tenant.
1. In **Criteri personalizzati** selezionare **B2C_1A_signup_signin**.
1. Selezionare **Esegui ora** e quindi Facebook per accedere con Facebook e testare i criteri personalizzati.

::: zone-end
## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Creare un flusso utente di modifica del profilo
> * Creare un flusso utente di reimpostazione delle password

Successivamente, si apprenderà come usare Azure AD B2C per accedere e iscrivere gli utenti in un'applicazione. Seguire l'applicazione Web ASP.NET collegata sotto oppure passare a un'altra applicazione nel sommario in **autenticare gli utenti**.

> [!div class="nextstepaction"]
> [Esercitazione: abilitare l'autenticazione in un'applicazione Web usando Azure AD B2C >](tutorial-web-app-dotnet.md)
