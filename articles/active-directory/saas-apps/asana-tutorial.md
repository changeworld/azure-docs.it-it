---
title: 'Esercitazione: Integrazione di Azure Active Directory con Asana | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a06c94eed6c90d7b38f28d37f3c8145d4ac1151d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651009"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Esercitazione: Integrazione di Azure Active Directory con Asana

In questa esercitazione si apprenderà come integrare asana con Azure Active Directory (Azure AD). Quando si integra asana con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso ad asana.
* Consentire agli utenti di accedere automaticamente ad asana con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di asana Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Asana supporta l'accesso SSO avviato da **SP**

* Asana supporta il [**provisioning utenti automatico**](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Aggiungere asana dalla raccolta

Per configurare l'integrazione di Asana in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **asana** nella casella di ricerca.
1. Selezionare **asana** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Configurare e testare Azure AD SSO per asana

Configurare e testare Azure AD SSO con asana usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in asana.

Per configurare e testare Azure AD SSO con asana, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare asana SSO](#configure-asana-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di asana](#create-asana-test-user)** : per avere una controparte di B. Simon in asana collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **asana** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Asana](common/sp-identifier.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL: `https://app.asana.com/`

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `https://app.asana.com/`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Asana** copiare gli URL appropriati in base alle proprie esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso ad asana.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Asana**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-asana-sso"></a>Configurare asana SSO

1. In una finestra del browser diversa accedere all'applicazione Asana. Per configurare SSO in Asana, accedere alle impostazioni dell'area di lavoro facendo clic sul nome dell'area di lavoro in alto a destra della schermata. Fare quindi clic su **\<your workspace name\> Settings** (Impostazioni di <nome area di lavoro>).

    ![Impostazioni SSO di Asana](./media/asana-tutorial/settings.png)

2. Nella finestra **Organization settings** (Impostazioni organizzazione) fare clic su **Administation** (Amministrazione). Fare clic su **Members must log in via SAML** (I membri devono accedere tramite SAML) per abilitare la configurazione di SSO. Eseguire quindi questa procedura:

    ![Configurare le impostazioni dell'accesso Single Sign-On per l'organizzazione](./media/asana-tutorial/save.png)  

    a. Nella casella di testo **URL pagina di accesso** incollare il valore di **URL di accesso**.

    b. Fare clic con il pulsante destro del mouse sul certificato scaricato dal portale Azure, quindi aprire il file di certificato nel Blocco note o nell'editor di testo preferito. Copiare il contenuto tra l'inizio e la fine del titolo del certificato e incollarlo nella casella di testo **Certificato X.509**.

3. Fare clic su **Salva**. Per altre informazioni e assistenza, vedere la [guida di Asana per la configurazione di SSO](https://asana.com/guide/help/premium/authentication#gl-saml) .

### <a name="create-asana-test-user"></a>Creare un utente di test di Asana

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Asana. Asana supporta il provisioning utenti automatico, che è abilitato per impostazione predefinita. Altri dettagli su come configurare il provisioning utenti automatico sono disponibili [qui](asana-provisioning-tutorial.md).

**Per creare un utente manualmente, seguire questa procedura:**

In questa sezione viene creato un utente di nome Britta Simon in Asana.

1. In **Asana**, passare alla sezione **Teams** (Team) nel pannello sinistro. Fare clic sul pulsante con il segno più.

    ![Creazione di un utente di test di Azure AD](./media/asana-tutorial/teams.png)

2. Digitare l'indirizzo di posta elettronica dell'utente, ad esempio **britta.simon\@contoso.com** nella casella di testo e quindi selezionare **Invite** (Invita).

3. Fare clic su **Send Invite** (Invia invito). Il nuovo utente riceverà un messaggio di posta elettronica nel proprio account di posta elettronica. L'utente dovrà creare e convalidare l'account.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso asana in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso asana e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro asana in app personali, questo verrà reindirizzato all'URL di accesso asana. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la configurazione asana, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).