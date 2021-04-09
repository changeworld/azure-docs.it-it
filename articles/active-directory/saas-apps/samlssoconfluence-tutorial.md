---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAML SSO for Confluence by resolution GmbH | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAML SSO for Confluence di resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9290272920bbb20144f4e0d957ba2d9ce60d06a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651281"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Esercitazione: Integrazione di Azure Active Directory con SAML SSO for Confluence by resolution GmbH

Questa esercitazione illustra come integrare SAML SSO per la convergenza di Resolution GmbH con Azure Active Directory (Azure AD). Quando si integra SAML SSO for confluenza di Resolution GmbH con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a SAML SSO for confluenza di Resolution GmbH.
* Consentire agli utenti di eseguire l'accesso automatico a SAML SSO for confluenza di Resolution GmbH con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* SAML SSO for confluenza di Resolution GmbH Single Sign-On sottoscrizione abilitata (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAML SSO for Confluence by resolution GmbH supporta l'accesso SSO avviato da **SP e IDP**

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Aggiungere SAML SSO for confluenza di Resolution GmbH dalla raccolta

Per configurare l'integrazione di SAML SSO for Confluence di resolution GmbH in Azure AD, è necessario aggiungere SAML SSO for Confluence di resolution GmbH dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAML SSO for confluenza by Resolution GmbH** nella casella di ricerca.
1. Selezionare **SAML SSO for confluenza by Resolution GmbH** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Configurare e testare Azure AD SSO per SAML SSO for confluenza di Resolution GmbH

Configurare e testare Azure AD SSO con SAML SSO for confluenza di Resolution GmbH usando un utente test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in SAML SSO for confluenza di Resolution GmbH.

Per configurare e testare Azure AD SSO con SAML SSO for confluenza di Resolution GmbH, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare SAML SSO for confluenza by Resolution GmbH SSO](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** : per configurare le singole impostazioni di Sign-On sul lato applicazione.
    1. **[Creare un utente di test di SAML SSO for Confluence by resolution GmbH](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** : per avere una controparte di Britta Simon in SAML SSO for Confluence by resolution GmbH che sia collegata alla rappresentazione di Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAML SSO for confluenza di Resolution GmbH** della portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Contattare il [team di supporto del client di SAML SSO for Confluence di resolution GmbH](https://www.resolution.de/go/support) per ottenere i valori. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)


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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a SAML SSO for confluenza di Resolution GmbH.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAML SSO for Confluence by resolution GmbH** (SAML SSO for Confluence di resolution GmbH).
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Configurare SAML SSO for confluenza di Resolution GmbH SSO

1. In un'altra finestra del Web browser accedere al **portale di amministrazione di SAML SSO for Confluence di resolution GmbH** come amministratore.

2. Passare il puntatore del mouse sulla rotellina e scegliere **Add-ons** (Componenti aggiuntivi).
    
    ![Screenshot che mostra l'icona "Cog" selezionata e l'opzione "Add-ons" selezionata nel menu a discesa.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. Si verrà reindirizzati alla pagina di Administrator Access (Accesso come amministratore). Immettere la password e fare clic sul pulsante **Confirm** (Conferma).

    ![Screenshot che mostra la pagina "Administrator Access" con il pulsante "Confirm" selezionato.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. Nella scheda **ATLASSIAN MARKETPLACE** (MARKETPLACE DI ATLASSIAN) fare clic su **Find new add-ons** (Trova nuovi componenti aggiuntivi). 

    ![Screenshot che mostra la scheda "Attlassian Marketplace" con l'opzione "Find new add-ons" selezionata.](./media/saml-sso-confluence-tutorial/add-on.png)

5. Cercare **SAML Single Sign On (SSO) for Confluence** e fare clic su **Install** (Installa) per installare il nuovo plug-in di SAML.

    ![Screenshot che mostra la pagina "Find new add-ons" con "SAML Single Sign On (SSO) for Confluence" nella casella di ricerca e il pulsante "Install" selezionato.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. Viene avviata l'installazione del plug-in. Fare clic su **Close**.

    ![Screenshot che mostra la finestra di dialogo "Installing".](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Screenshot che mostra la finestra di dialogo "Installed and ready to go!" con l'azione "Close" selezionata.](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Fare clic su **Manage**.

    ![Screenshot che mostra la pagina dell'app "SAML Single Sign On (SSO) for Confluence" con il pulsante "Manage" selezionato.](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Fare clic su **Configure** (Configura) per configurare il nuovo plug-in.

    ![Screenshot che mostra la pagina "Manage" con il pulsante "Configure" selezionato.](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Questo nuovo plug-in è disponibile anche nella scheda **USERS & SECURITY** (UTENTI E SICUREZZA).

    ![Screenshot che mostra la scheda "Users & Security" con l'opzione "SAML SingleSignOn" selezionata.](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Nella pagina **SAML SingleSignOn Plugin Configuration** (Configurazione del plug-in SAML SingleSignOn) fare clic sul pulsante **Add new IdP** (Aggiungi nuovo provider di identità) per configurare le impostazioni del provider di identità.

    ![Screenshot che mostra la pagina "SAML SingleSignOn Plugin Configuration" con il pulsante "Add New IdP" selezionato.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Nella pagina **Choose your SAML Identity Provider** (Scegliere il provider di identità SAML) eseguire la procedura seguente:

    ![Screenshot che mostra la pagina "Choose your SAML Identity Provider" con le caselle di testo "IdP Type", "Name" e "Description" evidenziate.](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Impostare **AD Azure** come tipo di provider di identità.
    
    b. Aggiungere il **nome** del provider di identità, ad esempio Azure AD.
    
    c. Aggiungere la **descrizione** del provider di identità, ad esempio Azure AD.
    
    d. Fare clic su **Avanti**.
    
12. Nella pagina **Identity provider configuration** (Configurazione provider di identità) fare clic sul pulsante **Next** (Avanti).

    ![Screenshot che mostra la pagina "Identity provider configuration" con il pulsante "Next" selezionato.](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Nella pagina **Import SAML IdP Metadata** (Importa metadati provider di identità SAML) seguire questa procedura:

    ![Screenshot che mostra la pagina "Import SAML IdP Metadata" con i pulsanti "Import", "Load File" e "Next" selezionati.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Fare clic sul pulsante **Load File** (Carica file) e selezionare il file XML di metadati scaricato al passaggio 5.

    b. Fare clic sul pulsante **Import** (Importa).
    
    c. Attendere il completamento dell'importazione.
    
    d. Fare clic qui sul pulsante **Next** (Avanti).
    
14. Nella pagina **User ID attribute and transformation** (Attributo e trasformazione ID utente) fare clic sul pulsante **Next** (Avanti).

    ![Screenshot che mostra la pagina "User ID attribute and transformation" con il pulsante "Next" selezionato.](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. Nella pagina **User creation and update** (Creazione e aggiornamento utente) fare clic su **Save & Next** (Salva e continua) per salvare le impostazioni.   
    
    ![Screenshot che mostra la pagina "User creation and update" con il pulsante "Save & Next" selezionato.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. Nella pagina **Test your settings** (Test impostazioni) fare clic su **Skip test & configure manually** (Ignora test e configura manualmente) per ignorare il test dell'utente. Il test verrà eseguito nella sezione successiva e richiede alcune impostazioni nel portale di Azure. 
    
    ![Screenshot che mostra la pagina "Test your settings" con il pulsante "Skip test & configure manually" selezionato.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. Nella finestra di dialogo visualizzata, contenente il messaggio **Skipping the test means...** (Significato della mancata esecuzione del test), fare clic su **OK**.
    
    ![Configure Single Sign-On](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Creare un utente di test di SAML SSO for Confluence by resolution GmbH

Per consentire agli utenti di Azure AD di accedere a SAML SSO for Confluence di resolution GmbH, è necessario eseguire il provisioning in SAML SSO for Confluence di resolution GmbH.  
In SAML SSO for Confluence di resolution GmbH il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di SAML SSO for Confluence di resolution GmbH come amministratore.

2. Passare il puntatore del mouse e fare clic su **User management** (Gestione utenti).

    ![Screenshot che mostra l'icona "Cog" selezionata e l'opzione "User management" selezionata nel menu.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. Nella sezione Users (Utenti) fare clic sula scheda **Add users** (Aggiungi utenti). Nella pagina della finestra di dialogo **"Add a User"** (Aggiungi un utente) eseguire la procedura seguente:

    ![Aggiungere un dipendente](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica di un utente come Britta Simon.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente, ad esempio Britta Simon.

    c. Nella casella di testo **Email** digitare l'indirizzo di posta elettronica dell'utente, ad esempio Brittasimon@contoso.com.

    d. Nella casella di testo **Password** digitare la password di Britta Simon.

    e. Fare clic sul pulsante **Confirm** (Conferma) e immettere di nuovo la password.
    
    f. Fare clic sul pulsante **Aggiungi**.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento a SAML SSO for confluenza by Resolution GmbH URL di accesso in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso SAML SSO for confluenza by Resolution GmbH e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a SAML SSO for confluenza by Resolution GmbH per il quale si configura l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro SAML SSO for confluenza by Resolution GmbH in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente a SAML SSO for confluenza di Resolution GmbH per cui si configura SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurato SAML SSO for confluenza by Resolution GmbH, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).