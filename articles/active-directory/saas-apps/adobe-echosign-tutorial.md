---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Sign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 7162c38aae2fec4ea21aae56fa8c3649f7a55425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649965"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Sign

In questa esercitazione si apprenderà come integrare Adobe Sign con Azure Active Directory (Azure AD). Quando si integra Adobe Sign con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso ad Adobe Sign.
* Consentire agli utenti di accedere automaticamente ad Adobe Sign con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:
 
* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per Adobe Sign Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Adobe Sign supporta l'accesso SSO avviato da **SP**

## <a name="add-adobe-sign-from-the-gallery"></a>Aggiungere Adobe Sign dalla raccolta

Per configurare l'integrazione di Adobe Sign in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Adobe Sign** nella casella di ricerca.
1. Selezionare **Adobe Sign** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Configurare e testare Azure AD SSO per Adobe Sign

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adobe Sign usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Sign.

Per configurare e testare Azure AD Single Sign-On con Adobe Sign, è necessario eseguire i passaggi seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Adobe Sign SSO](#configure-adobe-sign-sso)** : per configurare le singole impostazioni di Sign-On sul lato applicazione.
    1. **[Creare un utente di test di Adobe Sign](#create-adobe-sign-test-user)** : per avere una controparte di Britta Simon in Adobe Sign collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Adobe Sign, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Adobe Sign** del portale di Azure selezionare **Single Sign-on**.

1. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<companyname>.echosign.com/`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Set up Adobe Sign** (Configura Adobe Sign) copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso ad Adobe Sign.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Adobe Sign**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-adobe-sign-sso"></a>Configurazione di Adobe Sign SSO

1. Prima della configurazione, contattare il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html) per aggiungere il dominio nell'elenco elementi consentiti di Adobe Sign. Di seguito viene illustrato come aggiungere il dominio:

    a. Il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html) invia un token generato in modo casuale. Il token per il proprio dominio sarà simile al seguente: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Pubblicare il token di verifica in un record DNS TEXT e comunicarlo al [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > L'operazione può richiedere alcuni giorni. Eventuali ritardi nella propagazione dei record DNS indicano che un valore pubblicato nel DNS potrebbe non essere visibile per almeno un'ora. Si suppone che l'amministratore IT sappia come pubblicare questo token in un record DNS TEXT.

    c. Una volta avvisato il [team di supporto clienti di Adobe Sign](https://helpx.adobe.com/in/contact/support.html) tramite il ticket di supporto, dopo la pubblicazione del token, il dominio verrà convalidato e aggiunto al proprio account.

    d. Procedura da eseguire per pubblicare il token in un record DNS:

    * Accedere al proprio account di dominio.
    * Trovare la pagina per l'aggiornamento del record DNS. Questa pagina potrebbe essere denominata Gestione DNS, Gestione server dei nomi o Impostazioni avanzate.
    * Trovare i record TXT relativi al proprio dominio.
    * Aggiungere un record TXT con il valore completo del token fornito da Adobe.
    * Salvare le modifiche.

1. In un'altra finestra del Web browser accedere al sito aziendale di Adobe Sign come amministratore.

1. Nel menu SAML, selezionare **Impostazioni account** > **Impostazioni SAML**.

    ![Screenshot della pagina delle impostazioni SAML di Adobe Sign](./media/adobe-echosign-tutorial/settings.png "Account")

1. Nella sezione **SAML Settings** (Impostazioni SAML), seguire questa procedura:

    ![Screenshot con le impostazioni SAML evidenziate, inclusa SAML Mandatory.](./media/adobe-echosign-tutorial/saml1.png "Impostazioni SAML")

   ![Screenshot delle impostazioni SAML](./media/adobe-echosign-tutorial/saml.png "Impostazioni SAML")

   a. Per **SAML Mode** (Modalità SAML), selezionare **SAML Mandatory** (SAML obbligatorio).

   b. Selezionare **Allow Echosign Account Administrators to log in using their Echosign Sign Credentials** (Consenti agli amministratori account di Echosign Sign di accedere con le credenziali di Echosign Sign).

   c. In **User Creation** (Creazione utente), selezionare **Automatically add users authenticated through SAML** (Aggiungi automaticamente gli utenti autenticati tramite SAML).

   d. Incollare l'**identificatore di Azure AD** copiato dal portale di Azure nella casella di testo **Idp Entity ID** (ID entità IdP).

   e. Incollare il valore di **URL di accesso**, copiato dal portale di Azure, nella casella di testo **Idp Login URL** (URL di accesso IdP).

   f. Incollare l'**URL di disconnessione** copiato dal portale di Azure nella casella di testo **IdP Logout URL** (ID disconnessione IdP).

   g. Aprire il file del **certificato (Base64)** scaricato nel blocco note. Copiare il contenuto negli Appunti e quindi incollarlo nella casella di testo **IDP Certificate** (Certificato IDP).

   h. Selezionare **Save changes** (Salva modifiche).

### <a name="create-adobe-sign-test-user"></a>Creare l'utente di test di Adobe Sign

Per consentire agli utenti di Azure AD di accedere a Adobe Sign, è necessario eseguirne il provisioning in Adobe Sign. Si tratta di un processo manuale.

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da Adobe Sign per eseguire il provisioning degli account utente di Azure AD. 

1. Accedere al sito aziendale di **Adobe Sign** come amministratore.

2. Nel menu in alto, selezionare **Account**. Quindi, nel riquadro sinistro, selezionare **Utenti e gruppi** > **Crea un nuovo utente**.

    ![Screenshot del sito aziendale di Adobe Site, con evidenziate le opzioni relative all'account, agli utenti e ai gruppi e alla creazione di un nuovo utente](./media/adobe-echosign-tutorial/account.png "Account")

3. Nella sezione **Create New User** seguire questa procedura:

    ![Screenshot della sezione di creazione di un nuovo utente](./media/adobe-echosign-tutorial/user.png "Crea utente")

    a. Digitare l'**indirizzo di posta elettronica**, il **nome** e il **cognome** di un account utente di Azure AD valido di cui si vuole eseguire il provisioning.

    b. Selezionare **Crea utente**.

>[!NOTE]
>Il titolare dell'account di Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. 

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di Adobe Sign-on in cui è possibile avviare il flusso di accesso. 

* Passare all'URL di Adobe Sign-on direttamente e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Adobe Sign in My Apps (app personali), si dovrebbe accedere automaticamente al segno Adobe per il quale si configura SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Adobe Sign è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).