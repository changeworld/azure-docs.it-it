---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con KnowledgeOwl | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e KnowledgeOwl.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: c1c53da8c62acc1b4aacd2b0fa79dd968c65c13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645638"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con KnowledgeOwl

Questa esercitazione descrive come integrare KnowledgeOwl con Azure Active Directory (Azure AD). Integrando KnowledgeOwl con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a KnowledgeOwl.
* Abilitare gli utenti per l'accesso automatico a KnowledgeOwl con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di KnowledgeOwl abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* KnowledgeOwl supporta SSO avviato da **SP e IDP** .
* KnowledgeOwl supporta il provisioning degli utenti **just-in-Time** .

## <a name="add-knowledgeowl-from-the-gallery"></a>Aggiungere KnowledgeOwl dalla raccolta

Per configurare l'integrazione di KnowledgeOwl in Azure AD, è necessario aggiungere KnowledgeOwl dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **KnowledgeOwl** nella casella di ricerca.
1. Selezionare **KnowledgeOwl** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-knowledgeowl"></a>Configurare e testare Azure AD SSO per KnowledgeOwl

Configurare e testare l'accesso SSO di Azure AD con KnowledgeOwl usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in KnowledgeOwl.

Per configurare e testare Azure AD SSO con KnowledgeOwl, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di KnowledgeOwl](#configure-knowledgeowl-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di KnowledgeOwl](#create-knowledgeowl-test-user)** : per avere una controparte di B.Simon in KnowledgeOwl collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **KnowledgeOwl** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **identificatore** Digitare l'URL usando uno dei modelli seguenti:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. Nella casella di testo **URL di risposta** Digitare l'URL usando uno dei modelli seguenti:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** Digitare l'URL usando uno dei modelli seguenti:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli in seguito con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. La procedura è descritta più avanti nell'esercitazione.

1. L'applicazione KnowledgeOwl prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione KnowledgeOwl prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine | Spazio dei nomi |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (base)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificateraw.png)

1. Nella sezione **Configura KnowledgeOwl** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a KnowledgeOwl.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **KnowledgeOwl**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-knowledgeowl-sso"></a>Configurare l'accesso Single Sign-On di KnowledgeOwl

1. In un'altra finestra del Web browser accedere al sito aziendale di KnowledgeOwl come amministratore.

1. Fare clic su **Settings** (Impostazioni) e quindi selezionare **Security** (Sicurezza).

    ![Screenshot che mostra l'opzione Security selezionata nel menu Settings.](./media/knowledgeowl-tutorial/configure-1.png)

1. Scorrere verso il basso fino a **SAML SSO Integration** (Integrazione SSO SAML) e seguire questa procedura:

    ![Screenshot che mostra la pagina SAML SSO Integration in cui è possibile apportare le modifiche descritte qui.](./media/knowledgeowl-tutorial/configure-2.png)

    a. Selezionare **Enable SAML SSO** (Abilita SSO SAML).

    b. Copiare il valore di **SP Entity ID** (ID entità SP) e incollarlo in **Identifier (Entity ID)** (Identificatore - ID entità) nella sezione **Configurazione SAML di base** del portale di Azure.

    c. Copiare il valore di **SP Login URL** (URL di accesso del provider di servizi) e incollarlo nelle caselle di testo **URL di accesso e URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    d. Nella casella di testo **IdP entityID** (ID entità IdP) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    e. Nella casella di testo **IdP Login URL** (URL di accesso IdP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    f. Nella casella di testo **IDP Logout URL (URL disconnessione IDP** ) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    g. Caricare il certificato scaricato dal portale di Azure facendo clic su **Upload IdP Certificate** (Carica certificato IdP).

    h. Fare clic su **Map SAML Attributes** (Esegui il mapping degli attributi SAML) per eseguire il mapping degli attributi e seguire questa procedura:

    ![Screenshot che mostra la pagina Map SAML Attributes in cui è possibile apportare le modifiche descritte qui.](./media/knowledgeowl-tutorial/configure-3.png)

    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` nella casella di testo **SSO ID** (ID SSO)
    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` nella casella di testo **Username/Email** (Nome utente/indirizzo di posta elettronica).
    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` nella casella di testo **First Name** (Nome).
    * Immettere `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` nella casella di testo **Last Name** (Cognome).
    * Fare clic su **Salva**.

    i. Fare clic su **Save** nella parte inferiore della pagina.

    ![Screenshot che mostra il pulsante Save.](./media/knowledgeowl-tutorial/configure-4.png)

### <a name="create-knowledgeowl-test-user"></a>Creare un utente di test di KnowledgeOwl

In questa sezione viene creato un utente di nome B.Simon in KnowledgeOwl. KnowledgeOwl supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in KnowledgeOwl, ne viene creato uno nuovo dopo l'autenticazione.

> [!Note]
> Se è necessario creare un utente manualmente, contattare il [team di supporto di KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di KnowledgeOwl, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di KnowledgeOwl e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al KnowledgeOwl per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro KnowledgeOwl in My Apps (app personali), se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al KnowledgeOwl per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato KnowledgeOwl, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).