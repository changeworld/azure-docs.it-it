---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAML SSO for Jira by resolution GmbH | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAML SSO for Jira di resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 450db7c01de4ea7db0133ceca962941ae1a927e5
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173136"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Esercitazione: Integrazione di Azure Active Directory con SAML SSO for Jira by resolution GmbH

In questa esercitazione si apprenderà come integrare SAML SSO for JIRA di Resolution GmbH con Azure Active Directory (Azure AD). Quando si integra SAML SSO for JIRA di Resolution GmbH con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a SAML SSO for JIRA di Resolution GmbH.
* Abilitare gli utenti per l'accesso automatico a SAML SSO for JIRA di Resolution GmbH con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per SAML SSO for JIRA di Resolution GmbH Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAML SSO for JIRA di Resolution GmbH supporta la funzionalità SSO avviata da **SP** e **IDP** .

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Aggiungere SAML SSO for JIRA di Resolution GmbH dalla raccolta

Per configurare l'integrazione di SAML SSO for Jira di resolution GmbH in Azure AD, è necessario aggiungere SAML SSO for Jira di resolution GmbH dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAML SSO for JIRA di Resolution GmbH** nella casella di ricerca.
1. Selezionare **SAML SSO for JIRA di Resolution GmbH** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Configurare e testare Azure AD SSO for SAML SSO for JIRA di Resolution GmbH

Configurare e testare Azure AD SSO con SAML SSO for JIRA di Resolution GmbH usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in SAML SSO for JIRA di Resolution GmbH.

Per configurare e testare Azure AD SSO con SAML SSO for JIRA di Resolution GmbH, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare SAML SSO for JIRA di Resolution GmbH SSO](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** : per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di SAML SSO for JIRA di Resolution GmbH](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** : per avere una controparte di B. Simon in SAML SSO for JIRA di Resolution GmbH collegata al Azure ad rappresentazione dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **SAML SSO for JIRA di Resolution GmbH** della portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura, se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura, se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Per l'identificatore, l'URL di risposta e l'URL di accesso, sostituire **\<server-base-url>** con l'URL di base dell'istanza di Jira. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure. In caso di problemi, contattare il [team di supporto del client di SAML SSO for Jira by resolution GmbH](https://www.resolution.de/go/support).

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, scaricare il file **XML dei metadati della federazione** e salvarlo nel computer in uso.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a SAML SSO for JIRA di Resolution GmbH.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAML SSO for Jira by resolution GmbH** (SAML SSO for Jira di resolution GmbH).
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Configurare SAML SSO for JIRA di Resolution GmbH SSO 

1. In un'altra finestra del Web browser accedere all'istanza di Jira come amministratore.

2. Passare il puntatore sull'icona dell'ingranaggio a destra e fare clic su **Manage apps** (Gestisci app).
    
    ![Screenshot di una freccia che punta all'icona "Cog" e l'opzione "Manage apps" selezionata nel menu a discesa.](./media/samlssojira-tutorial/add-on-1.png)

3. Se si viene reindirizzati alla pagina Administrator Access (Accesso amministratore), immettere la **password** e fare clic sul pulsante **Confirm** (Conferma).

    ![Screenshot che mostra la pagina "Administrator Access".](./media/samlssojira-tutorial/add-on-2.png)

4. In genere, Jira reindirizza l'utente al Marketplace di Atlassian. In caso contrario, fare clic su **Find new apps** (Trova nuove app) nel pannello a sinistra. Cercare **SAML Single Sign On (SSO) for JIRA** e fare clic sul pulsante **Install** (Installa) per installare il plug-in di SAML.

    ![Screenshot che mostra la pagina "Atlassian Marketplace for JIRA" con una freccia che punta al pulsante "Install" per l'app "SAML Single Sign On (SSO) Jira, SAM L/SSO".](./media/samlssojira-tutorial/store.png)

5. Viene avviata l'installazione del plug-in. Al termine, fare clic sul pulsante **Close** (Chiudi).

    ![Screenshot che mostra la finestra di dialogo "Installing".](./media/samlssojira-tutorial/store-2.png)

    ![Screenshot che mostra la finestra di dialogo "Installed and ready to go!" con il pulsante "Close" selezionato.](./media/samlssojira-tutorial/store-3.png)

6. Fare quindi clic su **Manage** (Gestisci).

    ![Screenshot che mostra l'app "SAML Single Sign On (SSO) Jira, SAML/SSO" con il pulsante "Manage" selezionato.](./media/samlssojira-tutorial/store-4.png)
    
7. In seguito, fare clic su **Configure** (Configura) per configurare il plug-in appena installato.

    ![Screenshot che mostra la pagina "Manage apps" con il pulsante "Configure" selezionato per l'app "SAML SingleSignOn for Jira".](./media/samlssojira-tutorial/store-5.png)

8. Nella procedura guidata **SAML Single Sign-On Plugin Configuration** (Configurazione del plug-in SAML Single Sign-On) fare clic sul pulsante **Add new IdP** (Aggiungi nuovo provider di identità) per configurare Azure AD come nuovo provider di identità.

    ![Screenshot mostra la pagina "Welcome" con il pulsante "Add New IdP" selezionato.](./media/samlssojira-tutorial/add-on-4.png) 

9. Nella pagina **Choose your SAML Identity Provider** (Scegliere il provider di identità SAML) seguire questa procedura:

    ![Screenshot che mostra la pagina "Choose your SAML Identity Provider" con le caselle di testo "IdP Type" e "Name" evidenziate e il pulsante "Next" selezionato.](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Impostare **AD Azure** come tipo di provider di identità.
    
    b. Aggiungere il **nome** del provider di identità, ad esempio Azure AD.
    
    c. Aggiungere una **descrizione** facoltativa del provider di identità, ad esempio Azure AD.
    
    d. Fare clic su **Avanti**.
    
10. Nella pagina **Identity provider configuration** (Configurazione provider di identità), fare clic su **Next** (Avanti).
 
    ![Screenshot che mostra la pagina "Identity provider configuration".](./media/samlssojira-tutorial/configuration.png)

11. Nella pagina **Import SAML IdP Metadata** (Importa metadati provider di identità SAML) seguire questa procedura:

    ![Screenshot che mostra la pagina "Import SAML IdP Metadata" con l'azione "Select Metadata XML File" selezionata.](./media/samlssojira-tutorial/metadata.png)

    a. Fare clic sul pulsante **Select Metadata XML File** (Seleziona file XML dei metadati) e selezionare il file **XML dei metadati della federazione** scaricato in precedenza.

    b. Fare clic sul pulsante **Import** (Importa).
     
    c. Attendere il completamento dell'importazione.  
     
    d. Fare clic sul pulsante **Next** (Avanti).
    
12. Nella pagina **User ID attribute and transformation** (Attributo e trasformazione ID utente) fare clic sul pulsante **Next** (Avanti).

    ![Screenshot che mostra la pagina "User I D attribute and transformation" con il pulsante "Next" selezionato.](./media/samlssojira-tutorial/transformation.png)
    
13. Nella pagina **User creation and update** (Creazione e aggiornamento utente) fare clic su **Save & Next** (Salva e continua) per salvare le impostazioni.
    
    ![Screenshot che mostra la pagina "User creation and update" con il pulsante "Save & Next" selezionato.](./media/samlssojira-tutorial/update.png)
    
14. Nella pagina **Test your settings** (Test impostazioni) fare clic su **Skip test & configure manually** (Ignora test e configura manualmente) per ignorare il test dell'utente. Il test verrà eseguito nella sezione successiva e richiede alcune impostazioni nel portale di Azure.
    
    ![Screenshot che mostra la pagina "Test your settings" con il pulsante "Skip test & configure manually" selezionato.](./media/samlssojira-tutorial/test.png)
    
15. Fare clic su **OK** per ignorare l'avviso.
    
    ![Screenshot che mostra la finestra di dialogo di avviso con il pulsante "OK" selezionato.](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Creare l'utente di test di SAML SSO for Jira by resolution GmbH

Per consentire agli utenti di Azure AD di accedere a SAML SSO for Jira by resolution GmbH, è necessario eseguire il provisioning in SAML SSO for Jira by resolution GmbH. Nel caso specifico di questa esercitazione, è necessario eseguire il provisioning manualmente. Sono tuttavia disponibili anche altri modelli di provisioning per il plug-in SAML SSO by resolution, ad esempio il provisioning **Just-In-Time**. Vedere la relativa documentazione in [SAML SSO di resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). In caso di domande, contattare il [supporto di resolution](https://www.resolution.de/go/support).

**Per eseguire il provisioning di un account utente manualmente, seguire questa procedura:**

1. Accedere all'istanza di Jira come amministratore.

2. Passare il puntatore sull'icona dell'ingranaggio e selezionare **User management** (Gestione utenti).

   ![Screenshot di una freccia che punta all'icona "Cog" e l'opzione "User Management" selezionata nel menu a discesa.](./media/samlssojira-tutorial/user-1.png)

3. Se si viene reindirizzati alla pagina Administrator Access (Accesso amministratore), immettere la **password** e fare clic sul pulsante **Confirm** (Conferma).

    ![Screenshot che mostra la pagina "Administrator Access" con la casella di testo "Password" evidenziata.](./media/samlssojira-tutorial/user-2.png) 

4. Nella sezione della scheda **User management** (Gestione utenti) fare clic su **Create user** (Crea utente).

    ![Screenshot che mostra la scheda "User management" con il pulsante "Create user" selezionato.](./media/samlssojira-tutorial/user-3-new.png) 

5. Nella pagina della finestra di dialogo **Create New User** (Crea nuovo utente) effettuare le operazioni seguenti. È necessario creare l'utente esattamente come in Azure AD:

    ![Aggiungere un dipendente](./media/samlssojira-tutorial/user-4-new.png) 

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente: <b>BrittaSimon@contoso.com</b>.

    b. Nella casella di testo **Full Name** (Nome completo) digitare il nome completo dell'utente: **Britta Simon**.

    c. Nella casella di testo **Username** (Nome utente) digitare l'indirizzo di posta elettronica dell'utente: <b>BrittaSimon@contoso.com</b>. 

    d. Nella casella di testo **Password** immettere la password dell'utente.

    e. Fare clic su **Create user** (Crea utente) per completare la creazione dell'utente.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso SAML SSO for JIRA di Resolution GmbH, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso SAML SSO for JIRA di Resolution GmbH e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a SAML SSO for JIRA di Resolution GmbH per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro SAML SSO for JIRA di Resolution GmbH in app personali, se configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente a SAML SSO for JIRA di Resolution GmbH per il quale si configura SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="enable-sso-redirection-for-jira"></a>Abilita reindirizzamento SSO per JIRA

Come indicato nella sezione precedente, esistono attualmente due modi per attivare l'accesso Single Sign-On. Tramite il **portale di Azure** o tramite **un collegamento specifico all'istanza di Jira**. Il plug-in SAML SSO di resolution GmbH consente inoltre di attivare l'accesso Single Sign-On semplicemente tramite l'**accesso a qualsiasi URL che punta all'istanza di Jira**.

In sostanza, tutti gli utenti che accedono a Jira verranno reindirizzati all'accesso Single Sign-On dopo aver attivato un'opzione del plug-in.

Per attivare il reindirizzamento SSO, eseguire le operazioni seguenti nell'**istanza di Jira**:

1. Accedere alla pagina di configurazione del plug-in SAML SSO in Jira.
1. Fare clic su **Redirection** (Reindirizzamento) nel riquadro sinistro.

   ![Screenshot parziale della pagina di configurazione del plug-in SAML SSO in Jira che evidenzia il collegamento di reindirizzamento nel percorso di spostamento a sinistra.](./media/samlssojira-tutorial/configure-1.png)

1. Contrassegnare con un segno di spunta **Enable SSO Redirect** (Abilita il reindirizzamento SSO).

   ![Screenshot parziale della pagina di configurazione del plug-in SAML SSO in Jira in cui è evidenziata la casella di controllo "Enable SSO Redirect" selezionata.](./media/samlssojira-tutorial/configure-2.png) 

1. Scegliere il pulsante **Save Settings** (Salva impostazioni) nell'angolo in alto a destra.

Dopo aver attivato l'opzione, è comunque possibile tornare al prompt di nome utente/password, se l'opzione **Enable nosso** (Abilita nosso) è contrassegnata con un segno di spunta, passando a `https://<server-base-url>/login.jsp?nosso`. Come nei casi precedenti, sostituire **\<server-base-url>** con l'URL di base.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SAML SSO for JIRA di Resolution GmbH, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).