---
title: 'Esercitazione: Esercitazione: Integrazione di Azure Active Directory con Canvas | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a71dac55c860348f31ce8da27ab050a6c71a5c68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653044"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Esercitazione: Integrazione di Azure Active Directory con Canvas

In questa esercitazione si apprenderà come integrare Canvas con Azure Active Directory (Azure AD). Quando si integra Canvas con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso all'area di disegno.
* Abilitare gli utenti per l'accesso automatico a Canvas con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:
 
* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per Canvas Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Canvas supporta l'accesso SSO avviato da **SP**

## <a name="add-canvas-from-the-gallery"></a>Aggiungere Canvas dalla raccolta

Per configurare l'integrazione di Canvas in Azure AD, è necessario aggiungere Canvas dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Canvas** nella casella di ricerca.
1. Selezionare **Canvas** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Configurare e testare Azure AD SSO per Canvas

Configurare e testare Azure AD SSO con Canvas usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Canvas.

Per configurare e testare Azure AD SSO con Canvas, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso SSO Canvas](#configure-canvas-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Canvas](#create-canvas-test-user)** : per avere una controparte di B. Simon in canvas collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure nella pagina di integrazione dell'applicazione **Canvas** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Canvas](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<tenant-name>.instructure.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Canvas](https://community.canvaslms.com/community/help). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML**  fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML** .

    ![Modificare il certificato di firma SAML](common/edit-certificate.png)

6. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE** e salvarlo nel computer.

    ![Copiare il valore di Identificazione personale](common/copy-thumbprint.png)

7. Nella sezione **Configura Canvas** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso all'area di disegno.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Canvas**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-canvas-sso"></a>Configurare l'accesso Single Sign-on canvas

1. In un'altra finestra del Web browser accedere al sito aziendale di Canvas come amministratore.

2. Passare a **Courses (Corsi) \> Managed Accounts (Account gestiti) \> Microsoft**.

    ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. Nel riquadro di spostamento sinistro selezionare **Authentication** (Autenticazione) e quindi fare clic su **Add New SAML Config** (Aggiungi nuova configurazione SAML).

    ![autenticazione](./media/canvas-lms-tutorial/ic775991.png "Authentication")

4. Nella pagina Current Integration seguire questa procedura:

    ![Integrazione corrente](./media/canvas-lms-tutorial/save.png "Current Integration")

    a. Nella casella di testo **IdP Entity ID** (ID entità provider di identità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella di testo **Login URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Log Out URL** (URL di disconnessione) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    d. Nella casella di testo **Change Password Link** (Collegamento modifica password) incollare il valore dell'**URL di modifica password** copiato dal portale di Azure.

    e. Nella casella di testo **Certificate Fingerprint** (Impronta digitale certificato) incollare il valore **Identificazione personale** del certificato copiato dal portale di Azure.

    f. Nell'elenco **Login Attribute** (Attributo accesso) selezionare **NameID**.

    g. Nell'elenco **Identifier Format** (Formato identificatore) selezionare **emailAddress**.

    h. Fare clic su **Save authentication settings**.

### <a name="create-canvas-test-user"></a>Creare un utente di test di Canvas

Per consentire agli utenti di Azure AD di accedere a Canvas, è necessario effettuarne il provisioning in Canvas. Nel caso di Canvas, il provisioning utenti è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **Canvas** .

2. Passare a **Courses (Corsi) \> Managed Accounts (Account gestiti) \> Microsoft**.

   ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. Fare clic su **Users**.

   ![Screenshot che mostra il menu Canvas con l'opzione Users selezionata.](./media/canvas-lms-tutorial/ic775995.png "Utenti")

4. Fare clic su **Add new user**.

   ![Screenshot che mostra l'opzione Add a new User.](./media/canvas-lms-tutorial/ic775996.png "Utenti")

5. Nella finestra di dialogo Add a New User seguire questa procedura:

   ![Add User](./media/canvas-lms-tutorial/ic775997.png "Aggiunta di un utente")

   a. Nella casella di testo **Full Name** (Nome completo) immettere il nome dell'utente, ad esempio **BrittaSimon**.

   b. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.

   c. Nella casella di testo **Login** (Account di accesso) immettere l'indirizzo di posta elettronica di Azure AD dell'utente, ad esempio **brittasimon\@contoso.com**.

   d. Selezionare **Email the user about this account creation**.

   e. Fare clic su **Add User**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Canvas per effettuare il provisioning degli account utente Azure AD.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso con Canvas in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso all'area di disegno e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Canvas in app personali, si dovrebbe accedere automaticamente all'area di disegno per la quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata l'area di disegno, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).