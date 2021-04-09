---
title: 'Esercitazione: Integrazione di Azure Active Directory con TalentLMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TalentLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 84d50d19a8356418a5cbf1f93784e5b8816d4be5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689352"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Esercitazione: Integrazione di Azure Active Directory con TalentLMS

Questa esercitazione illustra come integrare TalentLMS con Azure Active Directory (Azure AD). Quando si integra TalentLMS con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a TalentLMS.
* Abilitare gli utenti per l'accesso automatico a TalentLMS con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TalentLMS, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di TalentLMS Single Sign-On abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* TalentLMS supporta l'accesso Single Sign-On avviato da **provider di servizi**

## <a name="add-talentlms-from-the-gallery"></a>Aggiungere TalentLMS dalla raccolta

Per configurare l'integrazione di TalentLMS in Azure AD, è necessario aggiungere TalentLMS dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **TalentLMS** nella casella di ricerca.
1. Selezionare **TalentLMS** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Configurare e testare Azure AD SSO per TalentLMS

Configurare e testare Azure AD SSO con TalentLMS usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in TalentLMS.

Per configurare e testare Azure AD SSO con TalentLMS, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare TALENTLMS SSO](#configure-talentlms-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di TalentLMS](#create-talentlms-test-user)** : per avere una controparte di B. Simon in TalentLMS collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **TalentLMS** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di TalentLMS](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<tenant-name>.TalentLMSapp.com`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di TalentLMS](https://www.talentlms.com/contact). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella sezione **Certificato di firma SAML**  fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML** .

    ![Modificare il certificato di firma SAML](common/edit-certificate.png)

6. Nella sezione **Certificato di firma SAML** copiare il valore **IDENTIFICAZIONE PERSONALE** e salvarlo nel computer.

    ![Copiare il valore di Identificazione personale](common/copy-thumbprint.png)

7. Nella sezione **Configura TalentLMS** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a TalentLMS.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **TalentLMS**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-talentlms-sso"></a>Configurare TalentLMS SSO

1. In un'altra finestra del Web browser accedere al sito aziendale di TalentLMS come amministratore.

1. Nella sezione **Account & Settings** (Account e impostazioni) fare clic sulla scheda **Users** (Utenti).

    ![Account & Settings](./media/talentlms-tutorial/IC777296.png "Account e impostazioni")

1. Fare clic su **Single Sign-On (SSO)**,

1. Nella sezione Single Sign-On seguire questa procedura:

    ![Single Sign-On](./media/talentlms-tutorial/saml.png "Single Sign-On")

    a. Nell'elenco **SSO integration type** selezionare **SAML 2.0**.

    b. Nella casella di testo **Identity Provider (IDP)** (Provider di entità) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Incollare il valore di **identificazione personale** da portale di Azure nella casella di testo **impronta digitale certificato** .

    d.  Nella casella di testo **Remote sign-in URL** (URL accesso remoto) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    e. Nella casella di testo **Remote sign-out URL** (URL di disconnessione remota) incollare il valore di **URL di disconnessione** copiato dal portale di Azure.

    f. Specificare le informazioni seguenti:

    * Nella casella di testo **TargetedID** (ID destinazione) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Nella casella di testo **First name** (Nome) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Nella casella di testo **Last name** (Cognome) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Nella casella di testo **Email** (Posta elettronica) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Fare clic su **Salva**.

### <a name="create-talentlms-test-user"></a>Creare l'utente di test di TalentLMS

Per consentire agli utenti di Azure AD di accedere a TalentLMS, è necessario eseguirne il provisioning in TalentLMS. Nel caso di TalentLMS, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant **TalentLMS**.

1. Fare clic su **Users** e quindi su **Add User**.

1. Nella pagina della finestra di dialogo **Add User** eseguire la procedura seguente:

    ![Add User](./media/talentlms-tutorial/IC777299.png "Aggiunta di un utente")  

    a. Nella casella di testo **First Name (nome** ) immettere il nome dell'utente, ad esempio `Britta` .

    b. Nella casella di testo **Last Name (cognome** ) immettere il cognome dell'utente, ad esempio `Simon` .
 
    c. Nella casella di testo **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `brittasimon@contoso.com`.

    d. Fare clic su **Add User**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente forniti da TalentLMS per effettuare il provisioning degli account utente di Azure AD.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di TalentLMS, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di TalentLMS e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro TalentLMS in app personali, questo verrà reindirizzato all'URL di accesso di TalentLMS. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato TalentLMS, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).