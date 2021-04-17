---
title: "Esercitazione: Azure Active Directory'integrazione con Clarizen One | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Clarizen One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: f7e90ff4c69e03482a1608185bc947ccb8604187
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516830"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>Esercitazione: Azure Active Directory'integrazione con Clarizen One

Questa esercitazione illustra come integrare Clarizen One con Azure Active Directory (Azure AD). Integrando Clarizen One con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Clarizen One.
* Abilitare gli utenti per l'accesso automatico a Clarizen One con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Clarizen One abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Clarizen One supporta l'accesso SSO avviato da **IDP.**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-clarizen-one-from-the-gallery"></a>Aggiungere Clarizen One dalla raccolta

Per configurare l'integrazione di Clarizen One in Azure AD, è necessario aggiungere Clarizen One dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta digitare** **Clarizen One** nella casella di ricerca.
1. Selezionare **Clarizen One nel** pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Configurare e testare l Azure AD SSO per Clarizen One

Configurare e testare Azure AD SSO con Clarizen One usando un utente di test di **nome B.Simon**. Per il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un Azure AD utente e l'utente correlato in Clarizen One.

Per configurare e testare Azure AD SSO con Clarizen One, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Clarizen One:](#configure-clarizen-one-sso)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Clarizen:](#create-clarizen-one-test-user)** per avere una controparte di B.Simon in Clarizen One collegata alla Azure AD dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure di integrazione dell'applicazione **Clarizen One** individuare la **sezione** Gestione e selezionare **Single Sign-On.**
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    a. Nella casella **di** testo Identificatore digitare il valore: `Clarizen`

    b. Nella casella di testo **URL di risposta** digitare il valore `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Clarizen One** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Clarizen One.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Clarizen One**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-clarizen-one-sso"></a>Configurare l'accesso SSO di Clarizen One

1. In un'altra finestra del Web browser accedere al sito aziendale di Clarizen One come amministratore.

1. Fare clic sul nome utente e quindi su **Settings** (Impostazioni).

    ![Clic sulla voce relativa alle impostazioni sotto il nome utente](./media/clarizen-tutorial/setting.png "Impostazioni")

1. Fare clic **sulla scheda Impostazioni** globali. Accanto a Autenticazione **federata fare** clic su **Modifica.**

    ![Scheda delle impostazioni globali](./media/clarizen-tutorial/authentication.png "Impostazioni globali")

1. Nella finestra di dialogo **Federated Authentication** (Autenticazione federata) seguire questa procedura:

    ![Finestra di dialogo di autenticazione federata](./media/clarizen-tutorial/federated-authentication.png "Autenticazione federata")

    a. Selezionare **Enable Federated Authentication** (Abilita autenticazione federata).

    b. Fare clic su **Upload** per caricare il certificato scaricato.

    c. Nella casella **Sign-in URL** (URL di accesso) immettere il valore di **Login URL** (URL di accesso) dalla finestra di configurazione dell'applicazione di Azure AD.

    d. Nella casella **Sign-out URL** (URL di disconnessione) immettere il valore di **Logout URL** (URL di disconnessione) dalla finestra di configurazione dell'applicazione di Azure AD.

    e. Selezionare **Use POST**.

    f. Fare clic su **Salva**.

### <a name="create-clarizen-one-test-user"></a>Creare un utente di test di Clarizen One

Questa sezione contiene l'obiettivo di creare un utente di nome Britta Simon in Clarizen One.

**Per creare un utente manualmente, seguire questa procedura:**

Per consentire Azure AD utenti di accedere a Clarizen One, è necessario effettuare il provisioning degli account utente. Nel caso di Clarizen One, il provisioning è un'attività manuale.

1. Accedere al sito aziendale di Clarizen One come amministratore.

2. Fare clic su **People** (Persone).

    ![Clic sull'opzione relativa alle persone](./media/clarizen-tutorial/people.png "Persone")

3. Fare clic su **Invite User**.

    ![Pulsante di invito dell'utente](./media/clarizen-tutorial/user.png "Invitare gli utenti")

1. Nella finestra di dialogo **Invite People** (Invita persone) seguire questa procedura:

    ![Finestra di dialogo di invito delle persone](./media/clarizen-tutorial/invite-people.png "Invitare persone")

    a. Nella casella **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    b. Fare clic su **Invita**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su Test this application in portale di Azure per accedere automaticamente all'applicazione Clarizen One per cui si è configurato l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Clarizen One nel App personali, si dovrebbe accedere automaticamente all'applicazione Clarizen One per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Clarizen One, è possibile applicare il controllo sessione, che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).