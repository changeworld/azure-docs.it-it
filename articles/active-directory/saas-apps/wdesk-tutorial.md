---
title: 'Esercitazione: Integrazione di Azure Active Directory con Wdesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104603271"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Wdesk

Questa esercitazione descrive come integrare Wdesk con Azure Active Directory (Azure AD). Integrando Wdesk with Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Wdesk.
* Abilitare gli utenti per l'accesso automatico a Wdesk con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Wdesk abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* WDESK supporta SSO avviato da **SP** e **IDP** .

## <a name="add-wdesk-from-the-gallery"></a>Aggiungere WDESK dalla raccolta

Per configurare l'integrazione di Wdesk in Azure AD, è necessario aggiungere Wdesk dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Wdesk** nella casella di ricerca.
1. Selezionare **Wdesk** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Configurare e testare Azure AD SSO per WDESK

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Wdesk usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Wdesk.

Per configurare e testare Azure AD SSO con WDESK, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Wdesk](#configure-wdesk-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Wdesk](#create-wdesk-test-user)** : per avere una controparte di B.Simon in Wdesk collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **WDESK** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si ottengono questi valori dal portale di WDesk quando si configura l'accesso Single Sign-On.

4. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Wdesk** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a WDESK.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Wdesk**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-wdesk-sso"></a>Configurare l'accesso Single Sign-On di Wdesk

1. In un'altra finestra del Web browser accedere a Wdesk come amministratore della sicurezza.

1. In basso a sinistra fare clic su **Amministratore** e scegliere **Amministratore dell'account**:
 
    ![Screenshot che mostra l'opzione Account Admin selezionata nel menu Admin.](./media/wdesk-tutorial/account.png)

1. In Wdesk Admin passare a **Security** (Sicurezza) e quindi a **SAML** > **SAML Settings** (Impostazioni SAML):

    ![Screenshot che mostra l'opzione SAMLS Settings selezionata nella scheda SAML.](./media/wdesk-tutorial/settings.png)

1. In **SAML User ID Settings** (Impostazioni ID utente SAML) selezionare **SAML User ID is Wdesk Username** (L'ID utente SAML corrisponde al nome utente Wdesk).

    ![Screenshot che mostra la sezione SAML User ID Settings in cui è possibile selezionare SAML User ID is Wdesk Username.](./media/wdesk-tutorial/wdesk-username.png)

4. In **General Settings** (Impostazioni generali) selezionare **Enable SAML Single Sign On** (Abilita SAML Single Sign On):

    ![Screenshot che mostra la sezione Edit SAML Settings in cui è possibile selezionare l'opzione Enable SAML Single Sign-On.](./media/wdesk-tutorial/user-settings.png)

5. In **Service Provider Details** (Dettagli del provider di servizi) seguire questa procedura:

    ![Screenshot che mostra la sezione Service Provider Details in cui è possibile immettere i valori indicati.](./media/wdesk-tutorial/service-provider.png)

    1. Copiare l'**URL di accesso** e incollarlo nella casella di testo **URL di accesso** nel portale di Azure.

    1. Copiare l'**URL dei metadati** e incollarlo nella casella di testo **Identificatore** nel portale di Azure.

    1. Copiare l'**URL del servizio consumer** e incollarlo nella casella di testo **URL di risposta** nel portale di Azure.

    1. Fare clic su **Salva** nel portale di Azure per salvare le modifiche.      

1. Fare clic su **Configure IdP Settings** (Configura impostazioni IdP) per aprire la finestra di dialogo **Edit IdP Settings** (Modifica impostazioni IdP). Fare clic su **Choose File** (Scegli file) per trovare il file **Metadata.xml** salvato dal portale di Azure, quindi caricarlo.
    
    ![Screenshot che mostra la sezione Edit IdP Settings in cui è possibile caricare i metadati.](./media/wdesk-tutorial/metadata.png)
  
1. Fare clic su **Salva modifiche**.

    ![Screenshot che mostra il pulsante Save changes.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Creare l'utente di test di Wdesk

Per consentire agli utenti di Azure AD di accedere a Wdesk, è necessario effettuarne il provisioning in Wdesk. Nel caso di Wdesk il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Wdesk come amministratore della sicurezza.

2. Passare ad **Admin** (Amministratore) > **Account Admin** (Amministratore dell'account).

     ![Screenshot che mostra l'opzione Account Admin selezionata nel menu Admin.](./media/wdesk-tutorial/account.png)

3. Fare clic su **Members** (Membri) in **People** (Persone).

4. Fare clic su **Add Member** (Aggiungi membro) per aprire la finestra di dialogo **Add Member** (Aggiungi membro). 
   
    ![Screenshot che mostra la scheda Members in cui è possibile selezionare Add Member.](./media/wdesk-tutorial/create-user-1.png)  

5. Nella casella di testo **User** (Utente) immettere il nome utente dell'utente, ad esempio b.simon@contoso.com, quindi fare clic sul pulsante **Continua**.

    ![Screenshot che mostra la finestra di dialogo Add Member in cui è possibile immettere un utente.](./media/wdesk-tutorial/create-user-3.png)

6.  Immettere i dettagli come mostrato di seguito:
  
    ![Screenshot che mostra la finestra di dialogo Add Member in cui è possibile immettere aggiungere informazioni di base per un utente.](./media/wdesk-tutorial/create-user-4.png)
 
    a. Nella casella di testo **E-mail** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio b.simon@contoso.com.

    b. Nella casella di testo **First name** (Nome) immettere il nome dell'utente, ad esempio **B**.

    c. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio **Simon**.

7. Fare clic sul pulsante **Save Member** (Salva membro).  

    ![Screenshot che mostra la sezione Send welcome email con il pulsante Save Member.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di WDESK, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di WDESK e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al WDESK per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro WDESK in My Apps (app personali), se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al WDESK per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato WDESK, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
