---
title: 'Esercitazione: integrazione di Azure Active Directory con Cisco Umbrella admin SSO | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e Cisco Umbrella admin SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 6073142b21a28f1242e0e6ec65ee2945a354b60b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592507"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Esercitazione: integrazione di Azure Active Directory con Cisco Umbrella admin SSO

In questa esercitazione si apprenderà come integrare Cisco Umbrella admin SSO con Azure Active Directory (Azure AD). Quando si integra Cisco Umbrella admin SSO con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a Cisco Umbrella admin SSO.
* Consentire agli utenti di accedere automaticamente a Cisco Umbrella admin SSO con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Cisco Umbrella admin SSO Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Cisco Umbrella admin SSO supporta SSO avviato da **SP e IDP** .

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Aggiungere Cisco Umbrella admin SSO dalla raccolta

Per configurare l'integrazione di Cisco Umbrella admin SSO in Azure AD, è necessario aggiungere Cisco Umbrella admin SSO dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Cisco Umbrella admin SSO** nella casella di ricerca.
1. Selezionare **Cisco Umbrella admin SSO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Configurare e testare Azure AD SSO per Cisco Umbrella admin SSO

Configurare e testare Azure AD SSO con Cisco Umbrella admin SSO usando un utente test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Cisco Umbrella admin SSO.

Per configurare e testare Azure AD SSO con Cisco Umbrella admin SSO, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Cisco Umbrella admin](#configure-cisco-umbrella-admin-sso-sso)** SSO SSO: per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Cisco Umbrella admin SSO](#create-cisco-umbrella-admin-sso-test-user)** : per avere una controparte di B. Simon in Cisco Umbrella admin SSO collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Cisco Umbrella admin SSO** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

    a. Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:

    b. Fare clic su **Impostare URL aggiuntivi**.

    c. Nella casella di testo **URL di accesso** Digitare l'URL: `https://login.umbrella.com/sso`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **file XML dei metadati** definito dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione configurare l'accesso Single Sign-on di **Cisco Umbrella admin** , copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Cisco Umbrella admin SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Cisco Umbrella admin SSO**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Configurare Cisco Umbrella admin SSO SSO

1. In un'altra finestra del browser accedere al sito aziendale di Cisco Umbrella admin SSO come amministratore.

2. Dal lato sinistro del menu, fare clic su **Amministratore** e passare a **Autenticazione**, quindi fare clic su **SAML**.

    ![Amministrazione](./media/cisco-umbrella-tutorial/admin.png)

3. Scegliere **Altro** e fare clic su **AVANTI**.

    ![Il menu Altro](./media/cisco-umbrella-tutorial/other.png)

4. Nella pagina **Cisco Umbrella admin SSO Metadata** fare clic su **Next**.

    ![I metadati](./media/cisco-umbrella-tutorial/metadata.png)

5. Nella scheda **Carica metadati**, se è stato preconfigurato SAML, selezionare l'opzione **Fai clic qui per modificare** e attenersi ai passaggi seguenti.

    ![Il pulsante AVANTI](./media/cisco-umbrella-tutorial/next.png)

6. Nell' **opzione a: caricare il file XML** caricare il file **XML dei metadati di federazione** scaricato dal portale di Azure e dopo il caricamento dei metadati i valori seguenti vengono popolati automaticamente automaticamente quindi fare clic su **Avanti**.

    ![Finestra di selezione file](./media/cisco-umbrella-tutorial/choose-file.png)

7. Nella sezione **Convalida configurazione SAML** fare clic su **TESTA LA CONFIGURAZIONE SAML**.

    ![Test](./media/cisco-umbrella-tutorial/test.png)

8. Fare clic su **SAVE**.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Creare un utente di test di Cisco Umbrella admin SSO

Per consentire agli utenti di Azure AD di accedere a Cisco Umbrella admin SSO, è necessario eseguirne il provisioning in Cisco Umbrella admin SSO.  
Nel caso di Cisco Umbrella admin SSO, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. In un'altra finestra del browser accedere al sito aziendale di Cisco Umbrella admin SSO come amministratore.

2. Dal lato sinistro del menu, fare clic su **Amministratore** e passare a **Account**.

    ![Account](./media/cisco-umbrella-tutorial/account.png)

3. Nella pagina **Account**, fare clic su **Aggiungi** in alto a destra e attenersi ai passaggi seguenti.

    ![Utente](./media/cisco-umbrella-tutorial/create-user.png)

    a. Nel campo **Nome** immettere il nome, ad esempio **Britta**.

    b. Nel campo **Cognome** immettere il cognome, ad esempio **simon**.

    c. In **Scegli ruolo amministratore con delega** selezionare il proprio ruolo.

    d. Nel campo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio **brittasimon\@contoso.com**.

    e. Immettere la password nel campo **Password**.

    f. Reimmettere la password nel campo **Conferma password**.

    g. Fare clic su **Create** (Crea).

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso SSO amministratore di Cisco Umbrella, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente a Cisco Umbrella admin SSO URL di accesso e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a Cisco Umbrella admin SSO per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Cisco Umbrella admin SSO in app personali, se configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente a Cisco Umbrella admin SSO per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Cisco Umbrella admin SSO è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).