---
title: 'Esercitazione: Integrazione di Azure Active Directory con MobileIron | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: c47092b1488a79805db69308bcb9a8efde1c0d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653043"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Esercitazione: integrazione di Azure Active Directory con MobileIron

 Questa esercitazione illustra come integrare MobileIron con Azure Active Directory (Azure AD). Quando si integra MobileIron con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a MobileIron.
* Abilitare gli utenti per l'accesso automatico a MobileIron con i relativi account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per MobileIron Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* MobileIron supporta SSO avviato da **SP e IDP** .

## <a name="add-mobileiron-from-the-gallery"></a>Aggiungere MobileIron dalla raccolta

Per configurare l'integrazione di MobileIron in Azure AD, è necessario aggiungere MobileIron dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **MobileIron** nella casella di ricerca.
1. Selezionare **MobileIron** nei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Configurare e testare Azure AD SSO per MobileIron

Configurare e testare Azure AD SSO con MobileIron usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione collegata tra un utente Azure AD e l'utente correlato in MobileIron.

Per configurare e testare Azure AD SSO con MobileIron, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
     1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare MOBILEIRON SSO](#configure-mobileiron-sso)** : per configurare le impostazioni del singolo Sign-On sul lato applicazione.
    1. **[Creare un utente di test di MobileIron](#create-mobileiron-test-user)** : per avere una controparte di Britta Simon in MobileIron collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure.
 
1. Nella pagina di integrazione dell'applicazione **MobileIron** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://www.MobileIron.com/<key>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

     Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Si otterranno i valori di chiave e host dal portale di amministrazione di MobileIron illustrato più avanti nell'esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi annotare la password.
   1. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione viene abilitato B. Simon per l'uso di Azure Single Sign-On concedendo l'accesso a MobileIron.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **MobileIron**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-mobileiron-sso"></a>Configurare MobileIron SSO

1. In un'altra finestra del Web browser accedere al sito aziendale di MobileIron come amministratore.

2. Passare a **Admin** > **Identity** (Amministratore > Identità) e selezionare l'opzione **AAD** nel campo **Info on Cloud IDP Setup** (Informazioni sulla configurazione del provider di identità cloud).

    ![Screenshot che mostra la scheda Admin del sito MobileIron con l'opzione Identity selezionata.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Copiare i valori di **Chiave** e **Host** e incollarli per completare gli URL nella sezione **Configurazione SAML di base** del portale di Azure.

    ![Screenshot che mostra l'opzione Setting Up SAML con una chiave e un valore host.](./media/MobileIron-tutorial/key.png)

4. Nel campo **Export metadata file from AAD and import to MobileIron Cloud** (Esporta file di metadati da AAD e importa nel cloud di MobileIron) fare clic su **Scegli file** per caricare i metadati scaricati dal portale di Azure. Dopo il caricamento fare clic su **Fatto**.

    ![Pulsante per i metadati amministratore nella configurazione dell'accesso Single Sign-On](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>Creare l'utente di test di MobileIron

Per consentire agli utenti di Azure AD di accedere a MobileIron, è necessario eseguire il provisioning degli utenti in MobileIron.  
Nel caso di MobileIron, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di MobileIron come amministratore.

1. Passare a **Utenti** e fare clic su **Aggiungi** > **Utente singolo**.

    ![Pulsante Utenti nella configurazione dell'accesso Single Sign-On](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. Nella pagina **"Utente singolo"** seguire questa procedura:

    ![Pulsante per l'aggiunta di utenti nella configurazione dell'accesso Single Sign-On](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    b. Nella casella di testo **First Name** (Nome) immettere il nome dell'utente, ad esempio Britta.

    c. Nella casella di testo **Last Name** (Nome) immettere il cognome dell'utente, ad esempio Simon.

    d. Fare clic su **Done**.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di MobileIron, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di MobileIron e avviare il flusso di accesso da qui.

### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al MobileIron per il quale si configura l'accesso SSO.

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro MobileIron in My Apps (app personali), se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al MobileIron per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la MobileIron, è possibile applicare i controlli della sessione, che proteggono exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
