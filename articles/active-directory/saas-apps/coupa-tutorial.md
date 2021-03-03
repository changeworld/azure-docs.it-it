---
title: 'Esercitazione: Integrazione di Azure Active Directory con Coupa | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d6a686b38c9b67ed8b1a7801c2a6ba95ef29558c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652983"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Esercitazione: Integrazione di Azure Active Directory con Coupa

In questa esercitazione si apprenderà come integrare Coupa con Azure Active Directory (Azure AD). Quando si integra Coupa con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a Coupa.
* Consentire agli utenti di accedere automaticamente a Coupa con i loro account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di coupa abilitata per la Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Coupa supporta l'accesso SSO avviato da **SP**

## <a name="add-coupa-from-the-gallery"></a>Aggiungere Coupa dalla raccolta

Per configurare l'integrazione di Coupa in Azure AD, è necessario aggiungere Coupa dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **coupa** nella casella di ricerca.
1. Selezionare **coupa** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Configurare e testare Azure AD SSO per Coupa

Configurare e testare Azure AD SSO con Coupa usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Coupa.

Per configurare e testare Azure AD SSO con Coupa, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare coupa SSO](#configure-coupa-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di coupa](#create-coupa-test-user)** : per avere una controparte di B. Simon incoupa collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure della pagina di integrazione dell'applicazione **coupa** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Poiché il valore di URL accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere questo valore, contattare [il team di supporto client Coupa](https://success.coupa.com/Support/Contact_Us?).

    b. Nella casella **Identificatore** digitare l'URL 

    | Environment  | URL |
    |:-------------|----|
    | Sandbox | `sso-stg1.coupahost.com`|
    | Produzione | `sso-prd1.coupahost.com`|
    | | |

    c. Nella casella di testo **URL di risposta** digitare il valore 

    | Environment | URL |
    |------------- |----|
    | Sandbox | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Produzione | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Set up Coupa** (Configura Coupa) copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Coupa.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Coupa**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-coupa-sso"></a>Configurazione di Coupa SSO

1. Accedere al sito aziendale di Coupa come amministratore.

2. Passare a **Setup (Configurazione) \> Security Control (Controllo di sicurezza)**.

    ![Controlli di sicurezza](./media/coupa-tutorial/setup.png "Security Controls")

3. Nella sezione **Log in using Coupa credentials** eseguire la procedura seguente:

    ![Metadati provider di servizi Coupa](./media/coupa-tutorial/login.png "Coupa SP metadata")

    a. Selezionare **Log in using SAML**.

    b. Fare clic su **Sfoglia** per caricare i metadati scaricati dal portale di Azure.

    c. Fare clic su **Salva**.

### <a name="create-coupa-test-user"></a>Creare l'utente di test di Coupa

Per consentire agli utenti di Azure AD di accedere a Coupa, è necessario eseguirne il provisioning in Coupa.  

* Nel caso di Coupa, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere al sito della società **Coupa** come amministratore.

2. Nel menu in alto fare clic su **Setup** e quindi su **Users**.

    ![Utenti](./media/coupa-tutorial/user.png "Utenti")

3. Fare clic su **Crea**.

    ![Creare utenti](./media/coupa-tutorial/create.png "Creare gli utenti")

4. Nella sezione **User Create** eseguire la procedura seguente:

    ![Dettagli utente](./media/coupa-tutorial/details.png "Dettagli utente")

    a. Nelle apposite caselle di testo digitare l'**account di accesso**, il **nome**, il **cognome**, l'**ID Single Sign-On** e l'**indirizzo e-mail** di un account di Azure Active Directory valido di cui si vuole eseguire il provisioning.

    b. Fare clic su **Crea**.

    >[!NOTE]
    >Il titolare dell'account Azure Active Directory riceve un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.
    >

>[!NOTE]
>È possibile usare qualsiasi altro strumento o API di creazione di account utente fornito da Coupa per effettuare il provisioning degli account utente Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Coupa in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a Coupa e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Coupa in app personali, si dovrebbe accedere automaticamente al Coupa per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Coupa, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)