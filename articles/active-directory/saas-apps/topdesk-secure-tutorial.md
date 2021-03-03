---
title: 'Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Secure | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TOPdesk - Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 5ed23889d8648c65ea0887d2f0f3406b50291f12
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654299"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Secure

In questa esercitazione si apprenderà come integrare i componenti di protezione con Azure Active Directory (Azure AD). Quando si integra la protezione da tavolo con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso al servizio di protezione dall'utente.
* Consentire agli utenti di eseguire l'accesso automatico a un servizio di protezione dall'accesso ai propri account di Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:
 
 * Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per la protezione da Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TOPdesk - Secure supporta l'accesso SSO avviato da **SP**

## <a name="add-topdesk---secure-from-the-gallery"></a>Aggiungere il servizio di protezione dalla raccolta

Per configurare l'integrazione di TOPdesk - Secure in Azure AD, è necessario aggiungere TOPdesk - Secure dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** Digitare **todesk-Secure** nella casella di ricerca.
1. Selezionare **todesk-Secure** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Configurare e testare Azure AD SSO per il servizio di protezione dall'accesso

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TOPdesk - Secure usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TOPdesk - Secure.

Per configurare e testare Azure AD Single Sign-On con il servizio protezione dall'utente, è necessario eseguire i passaggi seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    2. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare](#configure-topdesk---secure-sso)** l'accesso Single Sign-on protetto-per configurare le singole impostazioni di Sign-On sul lato applicazione.
    1. **[Creare un utente di test di TOPdesk - Secure](#create-topdesk---secure-test-user)**: per avere una controparte di Britta Simon in TOPdesk - Secure collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare Single Sign-On di Azure AD con TOPdesk - Secure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione per la **protezione** da parte del portale di Azure selezionare **Single Sign-on**.

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<companyname>.topdesk.net`

    b. Nella casella di testo **Identifier URL** (URL dell'identificatore) inserire l'URL dei metadati di TOPdesk ottenuto dalla configurazione TOPdesk. Il formato da usare è: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di TOPdesk - Secure](https://www.topdesk.com/us/support/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura TOPdesk - Secure** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso al servizio di protezione dell'utente.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **TOPdesk - Secure**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-topdesk---secure-sso"></a>Configurare l'accesso SSO sicuro

1. Accedere al sito della società **TOPdesk - Secure** come amministratore.

2. Scegliere **Settings** dal menu **TOPdesk**.

    ![Impostazioni](./media/topdesk-secure-tutorial/ic790598.png "Impostazioni")

3. Fare clic su **Login Settings**.

    ![Impostazioni di accesso](./media/topdesk-secure-tutorial/ic790599.png "Login Settings")

4. Espandere il menu **Login Settings** e quindi fare clic su **General**.

    ![Generale](./media/topdesk-secure-tutorial/ic790600.png "Generale")

5. Nell'area **Secure** della sezione di configurazione **SAML login** seguire la procedura seguente:

    ![Impostazioni tecniche](./media/topdesk-secure-tutorial/ic790855.png "Technical Settings")

    a. Fare clic su **Download** per scaricare il file di metadati pubblici e salvarlo in locale nel computer.

    b. Aprire il file di metadati e individuare il nodo **AssertionConsumerService**.

    ![AssertionConsumerService](./media/topdesk-secure-tutorial/ic790856.png "AssertionConsumerService")

    c. Copiare il valore **AssertionConsumerService** incollarlo nella casella di testo URL di risposta nella sezione **URL e dominio TOPdesk - Secure**.

6. Per creare un file del certificato, seguire questa procedura:

    ![Certificate](./media/topdesk-secure-tutorial/ic790606.png "Certificato")

    a. Aprire il file di metadati scaricato dal portale di Azure.

    b. Espandere il nodo **RoleDescriptor** con **xsi:type** uguale a **fed:ApplicationServiceType**.

    c. Copiare il valore del nodo **X509Certificate**.

    d. Salvare il valore copiato di **X509Certificate** in un file locale nel computer.

7. Nella sezione **Public** fare clic su **Add**.

    ![Aggiungere](./media/topdesk-secure-tutorial/ic790607.png "Add")

8. Nella pagina **SAML configuration assistant** eseguire la procedura seguente:

    ![Assistente alla configurazione SAML](./media/topdesk-secure-tutorial/ic790608.png "SAML configuration assistant")

    a. Per caricare il file di metadati scaricato dal portale di Azure, in **Metadati della federazione** fare clic su **Sfoglia**.

    b. Per caricare il file del certificato, in **Certificate (RSA)** fare clic su **Browse**.

    c. Per la **chiave privata (RSA, PKCS8, DER)**, è possibile caricare la propria chiave privata o, in alternativa, contattare [il team di supporto TOPdesk - Secure Client](https://www.topdesk.com/us/support) per ottenere la chiave privata.

    d. Per caricare il file del logo ottenuto dal team di supporto di TOPdesk, in **Logo icon** fare clic su **Browse**.

    e. Nella casella di testo **User name attribute** (Attributo nome utente) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Nella casella di testo **Display name** digitare un nome per la configurazione.

    g. Fare clic su **Salva**.

### <a name="create-topdesk---secure-test-user"></a>Creare un utente di test di TOPdesk - Secure

Per consentire agli utenti di Azure AD di accedere a TOPdesk - Secure, è necessario eseguirne il provisioning in TOPdesk - Secure.  
Nel caso di TOPdesk - Secure, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:

1. Accedere al sito aziendale di **TOPdesk - Secure** come amministratore.

2. Nel menu in alto fare clic su **TOPdesk \> New (Nuovo) \> Support Files (File di supporto) \> Operator (Operatore)**.

    ![Operatore](./media/topdesk-secure-tutorial/ic790610.png "Operatore")

3. Nella finestra di dialogo **New Operator** eseguire la procedura seguente:

    ![Operatore New](./media/topdesk-secure-tutorial/ic790611.png "Operatore New")

    a. Fare clic sulla scheda **General** (Generale).

    b. Nella casella di testo **Cognome** digitare Cognome dell'utente, ad esempio **Simon**.

    c. Selezionare un **sito** per l'account nella sezione **Location**.

    d. Nella casella di testo **Login Name** della sezione **TOPdesk Login** digitare un nome di accesso per l'utente.

    e. Fare clic su **Salva**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da TOPdesk - Secure per effettuare il provisioning degli account utente di Azure AD.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. In questo modo verrà reindirizzato all'URL di accesso con protezione dall'utente in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL del servizio di accesso protetto e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro di protezione dall'utente in app personali, si dovrebbe accedere automaticamente al servizio di protezione per la configurazione dell'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la protezione da tavolo è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).