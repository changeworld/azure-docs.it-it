---
title: 'Esercitazione: Integrazione di Azure Active Directory con Jitbit Helpdesk | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Jitbit Helpdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: b3fbf73ab51092f614a416355477fc552a404fd3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581806"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Esercitazione: Integrazione di Azure Active Directory con Jitbit Helpdesk

Questa esercitazione illustra come integrare Jitbit helpdesk con Azure Active Directory (Azure AD). Quando si integra Jitbit helpdesk con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a Jitbit helpdesk.
* Abilitare gli utenti per l'accesso automatico a Jitbit helpdesk con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per Jitbit helpdesk Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Jitbit helpdesk supporta SSO avviato da **SP** .

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>Aggiungere Jitbit helpdesk dalla raccolta

Per configurare l'integrazione di Jitbit Helpdesk in Azure AD, è necessario aggiungere Jitbit Helpdesk dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Jitbit helpdesk** nella casella di ricerca.
1. Selezionare **Jitbit helpdesk** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Configurare e testare Azure AD SSO per Jitbit helpdesk

Configurare e testare Azure AD SSO con Jitbit helpdesk usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Jitbit helpdesk.

Per configurare e testare Azure AD SSO con Jitbit helpdesk, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Jitbit helpdesk SSO](#configure-jitbit-helpdesk-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Jitbit helpdesk](#create-jitbit-helpdesk-test-user)** : per avere una controparte di B. Simon in Jitbit helpdesk collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Jitbit helpdesk** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** Digitare uno degli URL usando il modello seguente:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere tale valore, contattare il [team di supporto clienti di Jitbit Helpdesk](https://www.jitbit.com/support/).

    b. Nella casella di testo **Identificatore (ID entità)** digitare l'URL: `https://www.jitbit.com/web-helpdesk/`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Jitbit Helpdesk** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Jitbit helpdesk.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Jitbit Helpdesk**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-jitbit-helpdesk-sso"></a>Configurare Jitbit helpdesk SSO

1. In un'altra finestra del Web browser accedere al sito aziendale di Jitbit Helpdesk come amministratore.

1. Nel barra degli strumenti in alto fare clic su **Administration** (Amministrazione).

    ![Administration](./media/jitbit-helpdesk-tutorial/settings.png "Amministrazione")

1. Fare clic su **General settings** (Impostazioni generali).

    ![Screenshot che mostra il collegamento General Settings.](./media/jitbit-helpdesk-tutorial/general.png "Users, companies, and permissions")

1. Nella sezione di configurazione **Authentication settings** seguire questa procedura:

    ![Authentication settings](./media/jitbit-helpdesk-tutorial/authentication.png "Authentication settings")

    a. Selezionare **Enable SAML 2.0 single sign on** (Abilita Single Sign-On SAML 2.0) per l'accesso Single Sign-On con **OneLogin**.

    b. Nella casella di testo **URL endpoint** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Aprire il certificato con codifica **base 64** nel blocco note, copiarne il contenuto negli appunti e incollarlo nella casella di testo **Certificato X.509**

    d. Fare clic su **Salva modifiche**.

### <a name="create-jitbit-helpdesk-test-user"></a>Creare l'utente di test di Jitbit Helpdesk

Per consentire agli utenti di Azure AD di accedere a Jitbit Helpdesk, è necessario eseguirne il provisioning in Jitbit Helpdesk. Nel caso di Jitbit Helpdesk, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Jitbit Helpdesk**.

1. Scegliere **Amministrazione** dal menu disponibile nella parte superiore.

    ![Amministrazione](./media/jitbit-helpdesk-tutorial/settings.png "Amministrazione")

1. Fare clic su **Users, companies and permissions**.

    ![Users, companies, and permissions](./media/jitbit-helpdesk-tutorial/users.png "Users, companies, and permissions")

1. Fare clic su **Add User**.

    ![Aggiungere l'utente](./media/jitbit-helpdesk-tutorial/add.png "Add User")

1. Nella sezione Create immettere i dati dell'account di Azure AD di cui si desidera eseguire il provisioning come indicato di seguito:

    ![Creare](./media/jitbit-helpdesk-tutorial/create-section.png "Create")

   a. Nella casella di testo **Username** (Nome utente) digitare il nome dell'utente, ad esempio **BrittaSimon**.

   b. Nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica dell'utente, ad esempio **BrittaSimon@contoso.com** .

   c. Digitare il nome dell'utente, ad esempio **Britta**, nella casella di testo **First Name** (Nome).

   d. Digitare il cognome dell'utente, ad esempio **Simon**, nella casella di testo **Last Name** (Cognome).

   e. Fare clic su **Crea**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Jitbit Helpdesk per eseguire il provisioning degli account utente di Azure AD.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Jitbit helpdesk in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di Jitbit helpdesk e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Jitbit helpdesk in app personali, questo verrà reindirizzato all'URL di accesso di Jitbit helpdesk. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Jitbit helpdesk è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).