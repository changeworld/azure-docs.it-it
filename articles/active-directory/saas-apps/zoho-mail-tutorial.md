---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zoho | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zoho.
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
ms.openlocfilehash: 50e54b8518ecdbb4ef9f9a55ba50510da6becedc
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820911"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Esercitazione: Integrazione di Azure Active Directory con Zoho

In questa esercitazione si apprenderà come integrare Zoho con Azure Active Directory (Azure AD). Quando si integra Zoho con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Zoho.
* Abilitare gli utenti per l'accesso automatico a Zoho con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zoho One, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Zoho Single Sign-On abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zoho supporta l'accesso SSO avviato da **provider di servizi**

## <a name="add-zoho-from-the-gallery"></a>Aggiungere Zoho dalla raccolta

Per configurare l'integrazione di Zoho in Azure AD, è necessario aggiungere Zoho dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Zoho** nella casella di ricerca.
1. Selezionare **Zoho** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Configurare e testare Azure AD SSO per Zoho

Configurare e testare Azure AD SSO con Zoho usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Zoho.

Per configurare e testare Azure AD SSO con Zoho, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Zoho SSO](#configure-zoho-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Zoho](#create-zoho-test-user)** : per avere una controparte di B. Simon in Zoho collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Zoho** del portale di Azure trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<company name>.zohomail.com`

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Zoho](https://www.zoho.com/mail/contact.html). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Zoho** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Zoho.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Zoho**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-zoho-sso"></a>Configurare Zoho SSO

1. In un'altra finestra del Web browser accedere al sito aziendale di Zoho Mail come amministratore.

2. Passare a **Control panel**.
   
    ![Pannello di controllo](./media/zoho-mail-tutorial/control-panel.png "Control panel")

3. Fare clic sulla scheda **SAML Authentication** .
   
    ![Autenticazione SAML](./media/zoho-mail-tutorial/saml-authentication.png "Autenticazione SAML")

4. Nella sezione **SAML Authentication Details** seguire questa procedura:
   
    ![Dettagli dell'autenticazione SAML](./media/zoho-mail-tutorial/details.png "SAML Authentication Details")
   
    a. Nella casella di testo **Login URL** (URL di accesso) incollare l'**URL di accesso** copiato dal portale di Azure.
   
    b. Nella casella di testo **Logout URL** (URL di disconnessione) incollare l'**URL di disconnessione** copiato dal portale di Azure.
   
    c. Nella casella di testo **Change Password URL** (URL di modifica password) incollare l'**URL di modifica password** copiato dal portale di Azure.
       
    d. Aprire nel Blocco note il certificato con codifica Base 64 scaricato dal portale di Azure, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **PublicKey** (Chiave pubblica).
   
    e. In **Algorithm** (Algoritmo) selezionare **RSA**.
   
    f. Fare clic su **OK**.

### <a name="create-zoho-test-user"></a>Creare un utente di test di Zoho

Per consentire agli utenti di Azure AD di accedere a Zoho Mail, è necessario eseguirne il provisioning in Zoho Mail. Nel caso di Zoho Mail, il provisioning è un'attività manuale.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Zoho Mail per effettuare il provisioning degli account utente Azure AD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito aziendale di **Zoho Mail** come amministratore.

1. Passare a **Control Panel \> Mail & Docs**.

1. Passare a **User Details \> Add User** (Dettagli utente > Aggiungi utente).
   
    ![Screenshot che mostra il sito Zoho Mail con le opzioni User Details e Add User selezionate.](./media/zoho-mail-tutorial/add-user-1.png "Aggiunta di un utente")

1. Nella finestra di dialogo **Add users** seguire questa procedura:
   
    ![Screenshot che mostra la finestra di dialogo Add users in cui è possibile immettere i valori descritti.](./media/zoho-mail-tutorial/add-user-2.png "Aggiunta di un utente")
   
    a. Nella casella di testo **First Name** (Nome) digitare il nome dell'utente, ad esempio **Britta**.

    b. Nella casella di testo **Last name** (Cognome) digitare il cognome dell'utente, ad esempio **Simon**.

    c. Nella casella di testo **ID di posta elettronica** Digitare l'ID di posta elettronica dell'utente, ad esempio **brittasimon \@ contoso.com**.

    d. Nella casella di testo **Password** digitare la password dell'utente.
   
    e. Fare clic su **OK**.  
      
    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso Zoho in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a Zoho e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Zoho in app personali, si dovrebbe accedere automaticamente a Zoho per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Zoho è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
