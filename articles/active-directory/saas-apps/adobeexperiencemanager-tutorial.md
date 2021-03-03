---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Experience Manager | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 1a0340b9d8971446113862465c67143674bd5668
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653355"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Experience Manager

In questa esercitazione si apprenderà come integrare Adobe Experience Manager con Azure Active Directory (Azure AD). Quando si integra Adobe Experience Manager con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso ad Adobe Experience Manager.
* Consentire agli utenti di accedere automaticamente ad Adobe Experience Manager con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per Single Sign-On Adobe Experience Manager (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Adobe Experience Manager supporta l'accesso SSO avviato da **SP e IDP**

* Adobe Experience Manager supporta il provisioning utenti **Just In Time** (JIT)

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Aggiunta di Adobe Experience Manager dalla raccolta

Per configurare l'integrazione di Adobe Experience Manager in Azure AD, è necessario aggiungere Adobe Experience Manager dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Adobe Experience Manager** nella casella di ricerca.
1. Selezionare **Adobe Experience Manager** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Configurare e testare Azure AD SSO for Adobe Experience Manager

Configurare e testare Azure AD SSO con Adobe Experience Manager usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Adobe Experience Manager.

Per configurare e testare Azure AD SSO con Adobe Experience Manager, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare l'accesso SSO di Adobe Experience Manager](#configure-adobe-experience-manager-sso)** : per configurare le impostazioni del singolo Sign-On sul lato applicazione.
    1. **[Creare un utente di test di Adobe Experience Manager](#create-adobe-experience-manager-test-user)** : per avere una controparte di Britta Simon in Adobe Experience Manager collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Adobe Experience Manager** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un valore univoco definito anche nel server Adobe Experience Manager.

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Il valore di URL di risposta non è reale. Aggiornare questo valore con l'URL di risposta effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL del server Adobe Experience Manager.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura Adobe Experience Manager** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso ad Adobe Experience Manager.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Adobe Experience Manager**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-adobe-experience-manager-sso"></a>Configurare l'accesso SSO di Adobe Experience Manager

1. In un'altra finestra del browser, aprire il portale di amministrazione di **Adobe Experience Manager**.

2. Selezionare **Settings** (Impostazioni) > **Security** (Sicurezza) > **Users** (Utenti).

    ![Screenshot che mostra il riquadro Users in Adobe Experience Manager.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Selezionare **Administrator** (Amministratore) o un altro utente rilevante.

    ![Screenshot con l'utente Administrator evidenziato.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Selezionare **Account Settings** (Impostazioni account) > **Manage TrustStore** (Gestisci archivio trust).

    ![Screenshot che mostra l'opzione Manage TrustStore in Account settings.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. In **Add Certificate from CER file** (Aggiungi certificato da file CER), fare clic su **Select Certificate File** (Seleziona file certificato). Individuare e selezionare il file di certificato, scaricato in precedenza dal portale di Azure.

    ![Screenshot con il pulsante Select Certificate File evidenziato.](./media/adobe-experience-manager-tutorial/user-2.png)

6. Il certificato viene aggiunto all'archivio trust. Notare l'alias del certificato.

    ![Screenshot che mostra che il certificato è stato aggiunto a TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. Nella pagina **Users** (Utenti) selezionare **authentication-service**.

    ![Sreenshot con authentication-service evidenziato nella schermata.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Selezionare **Account Settings** (Impostazioni account) > **Create/Manage KeyStore** (Crea/Gestisci archivio chiavi). Creare l'archivio chiavi specificando una password.

    ![Screenshot con Manage KeyStore evidenziato.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Tornare alla schermata di amministrazione e quindi selezionare **Settings** (Impostazioni) > **Operations** (Operazioni) > **Web Console** (Console Web).

    ![Screenshot con Web Console evidenziato in Operations nella sezione Settings.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Viene aperta la pagina di configurazione.

    ![Pulsante di salvataggio per la configurazione dell'accesso Single Sign-On](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Cercare **Adobe Granite SAML 2.0 Authentication Handler** e quindi selezionare l'icona **Aggiungi**.

    ![Screenshot con Adobe Granite SAML 2.0 Authentication Handler evidenziato.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. In questa pagina eseguire le azioni seguenti.

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. Nella casella **Path** (Percorso) immettere **/** .

    b. Nella casella **IDP URL** (URL IDP) immettere il valore **URL di accesso** copiato dal portale di Azure.

    c. Nella casella **IDP Certificate Alias** (Alias certificato IDP) immettere il valore di **Certificate Alias** (Alias certificato) aggiunto nell'archivio trust.

    d. Nella casella **Security Provided Entity ID** (ID entità provider di servizi) immettere il valore **Identificatore Azure AD**  univoco configurato nel portale di Azure.

    e. Nella casella **Assertion Consumer Service URL** (URL del servizio consumer di asserzione) immettere il valore di **URL di risposta** configurato nel portale di Azure.

    f. Nella casella **Password of Key Store** (Password archivio chiavi) immettere la **Password** impostata nell'archivio chiavi.

    g. Nella casella **User Attribute ID** (ID attributo utente) immettere l'**ID nome** o un altro ID utente pertinente.

    h. Selezionare **Autocreate CRX Users** (Creazione automatica utenti CRX).

    i. Nella casella **Logout URL** (URL di disconnessione) incollare il valore **URL di disconnessione** ottenuto dal portale di Azure.

    j. Selezionare **Salva**.

### <a name="create-adobe-experience-manager-test-user"></a>Creare un utente di test di Adobe Experience Manager

In questa sezione viene creato un utente di nome Britta Simon in Adobe Experience Manager. Se è stata selezionata l'opzione **Autocreate CRX Users** (Creazione automatica utenti CRX), gli utenti vengono creati automaticamente dopo l'autenticazione.

Se si vogliono creare utenti manualmente, collaborare con il [team di supporto di Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) per aggiungere gli utenti nella piattaforma Adobe Experience Manager.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Adobe Experience Manager in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Adobe Experience Manager e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente ad Adobe Experience Manager per il quale si configura l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Adobe Experience Manager in app personali, se è configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente ad Adobe Experience Manager per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Adobe Experience Manager, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).