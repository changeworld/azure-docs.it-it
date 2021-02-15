---
title: 'Esercitazione: Integrazione di Azure Active Directory con Absorb LMS | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Absorb LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 5e962592779494e1d60d03e9e8a167d53ac8bda2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364130"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Esercitazione: Integrazione di Azure Active Directory con Absorb LMS

In questa esercitazione si apprenderà come integrare assorbire LMS con Azure Active Directory (Azure AD). Quando si integra assorba LMS con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso per assorbire LMS.
* Consentire agli utenti di accedere automaticamente per assorbire LMS con gli account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Absorb LMS, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Assorbe la sottoscrizione abilitata per Single Sign-On LMS.

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Absorb LMS supporta l'accesso SSO avviato da **IdP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-absorb-lms-from-the-gallery"></a>Aggiungere Absorb LMS dalla raccolta

Per configurare l'integrazione di Absorb LMS in Azure AD, è necessario aggiungere Absorb LMS dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **assorbe LMS** nella casella di ricerca.
1. Selezionare **assorbe LMS** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Configurare e testare Azure AD SSO for assorba LMS

Configurare e testare Azure AD SSO con assorbire LMS usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in assorbi LMS.

Per configurare e testare Azure AD SSO con assorba LMS, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare assorbire l'accesso SSO LMS](#configure-absorb-lms-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Crea utente di test di ASSORBA LMS](#create-absorb-lms-test-user)** : per avere una controparte di B. Simon in assorbe LMS collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **ASSORBA LMS** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    Se si usa **Absorb 5 - UI**, usare la configurazione seguente:

    a. Nella casella di testo **identificatore** Digitare un URL usando il modello seguente: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. Nella casella di testo **URL di risposta** Digitare un URL usando il modello seguente: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Se si usa **Absorb 5 - New Learner Experience**, usare la configurazione seguente:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. Nella casella di testo **URL di risposta** Digitare un URL usando il modello seguente: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Absorb LMS](https://support.absorblms.com/hc/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. La schermata seguente illustra l'elenco degli attributi predefiniti in cui **nameidentifier** è associato a **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura Absorb LMS** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso per assorbire LMS.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni, selezionare **Absorb LMS**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-absorb-lms-sso"></a>Configurare assorbire l'accesso SSO LMS

1. In una nuova finestra del Web browser accedere al sito aziendale di Absorb LMS come amministratore.

2. Selezionare il pulsante **Account** in alto a destra.

    ![Pulsante Account](./media/absorblms-tutorial/account.png)

3. Nel riquadro Account selezionare **Portal Settings** (Impostazioni del portale).

    ![Collegamento Impostazioni del portale](./media/absorblms-tutorial/portal.png)

4. Selezionare la scheda **Manage SSO Settings** (Gestisci impostazioni SSO).

    ![La scheda Utenti](./media/absorblms-tutorial/sso.png)

5. Nella pagina **Manage Single Sign-On Settings** (Gestisci impostazioni Single Sign-On) eseguire le operazioni seguenti:

    ![Pagina di configurazione Single Sign-on](./media/absorblms-tutorial/settings.png)

    a. Nela casella di testo **Name** (Nome) immettere un nome, come Azure AD Marketplace SSO.

    b. In **Method** (Metodo) selezionare **SAML**.

    c. Nel blocco note, aprire il certificato scaricato dal portale di Azure. Rimuovere i tag **-----BEGIN CERTIFICATE-----** ed **-----END CERTIFICATE-----**. Quindi, nella casella **Key** (Chiave) incollare il contenuto rimanente.

    d. Nella casella **Mode** (Modalità) selezionare **Identity Provider Initiated** (Avviato dal provider di identità).

    e. Nella casella **Id Property** (ID proprietà) selezionare l'attributo configurato come identificatore dell'utente in Azure AD. Ad esempio, se in Azure AD è stato selezionato *nameidentifier*, selezionare **Username**.

    f. In **Signature Type** (Tipo di firma) selezionare **Sha256**.

    g. Nella casella **Login URL** (URL di accesso) incollare l'**URL di accesso utente** dalla pagina **Proprietà** dell'applicazione del portale di Azure.

    h. In **Logout URL** (URL disconnessione) incollare il valore dell'**URL di disconnessione** copiato dalla finestra **Configura accesso** del portale di Azure.

    i. Spostare l'interruttore **Automatically Redirect** (Reindirizzamento automatico) sulla posizione **attiva**.

6. Selezionare **Salva**.

    ![Abilitare Only Allow SSO Login](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Creare un utente di test di Absorb LMS

Affinché gli utenti di Azure AD possano accedere ad Absorb LMS devono essere configurati in Absorb LMS. In Absorb LMS il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito aziendale di Absorb LMS come amministratore.

2. Nel riquadro **Users** (Utenti) selezionare **Users** (Utenti).

    ![Collegamento Utenti](./media/absorblms-tutorial/users.png)

3. Selezionare la scheda **User** (Utente).

    ![Elenco a discesa Aggiungi nuovo](./media/absorblms-tutorial/add.png)

4. Nella pagina **Add User** (Aggiungi utente) eseguire la procedura seguente:

    ![Pagina Aggiungi utente](./media/absorblms-tutorial/user.png)

    a. Nella casella di testo **First Name** (Nome) digitare il nome, ad esempio **Britta**.

    b. Nella casella di testo **Last Name** (Cognome) digitare il cognome, ad esempio **Simon**.

    c. Nella casella di testo **Username** (Nome utente) digitare il nome completo, ad esempio **Britta Simon**.

    d. Nella casella **Password** digitare la password dell'utente.

    e. Nella casella **Confirm Password** (Conferma la password) ridigitare la password.

    f. Impostare il pulsante **Is Active** (È attivo) su **Active** (Attivo).

5. Selezionare **Salva**.

    ![Abilitare Only Allow SSO Login](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Per impostazione predefinita, il provisioning utenti non è abilitato nel Single Sign-On. Gli utenti che vogliono abilitare questa funzionalità devono configurarla nel modo indicato in [questa](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentazione. Si noti anche che il provisioning utenti è disponibile solo in **Absorb 5 - New Learner Experience** con l'URL di Controllo di accesso `https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente al LMS di assorbimento per il quale si configura l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro assorba LMS in app personali, si dovrebbe accedere automaticamente al LMS di assorbimento per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo la configurazione di assorba LMS è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
