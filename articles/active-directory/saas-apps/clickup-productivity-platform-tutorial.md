---
title: 'Esercitazione: Integrazione di Azure Active Directory con ClickUp Productivity Platform | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ClickUp Productivity Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 29bc02466825aa2ea2c1a9ece2826b1262293392
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285201"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Esercitazione: Integrazione di Azure Active Directory con ClickUp Productivity Platform

Questa esercitazione illustra come integrare ClickUp Productivity Platform con Azure Active Directory (Azure AD). Quando si integra ClickUp Productivity Platform con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a ClickUp Productivity Platform.
* Consentire agli utenti di accedere automaticamente alla piattaforma ClickUp Productivity con i propri account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di ClickUp Productivity Platform Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* ClickUp Productivity Platform supporta SSO avviato da **SP** .

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>Aggiungere ClickUp Productivity Platform dalla raccolta

Per configurare l'integrazione di ClickUp Productivity Platform in Azure AD, è necessario aggiungere ClickUp Productivity Platform dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **ClickUp Productivity Platform** nella casella di ricerca.
1. Selezionare **ClickUp Productivity Platform** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Configurare e testare Azure AD SSO per la piattaforma di produttività ClickUp

Configurare e testare Azure AD SSO con ClickUp Productivity Platform usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in ClickUp Productivity Platform.

Per configurare e testare Azure AD SSO con ClickUp Productivity Platform, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare ClickUp Productivity Platform SSO](#configure-clickup-productivity-platform-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di ClickUp Productivity Platform](#create-clickup-productivity-platform-test-user)** : per avere una controparte di B. Simon in ClickUp Productivity Platform collegata al Azure ad rappresentazione dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **ClickUp Productivity Platform** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare l'URL: `https://app.clickup.com/login/sso`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Il valore dell'identificatore non è reale. Aggiornare questo valore con l'identificatore effettivo, come illustrato più avanti nell'esercitazione.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso alla piattaforma ClickUp Productivity.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **ClickUp Productivity Platform**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-clickup-productivity-platform-sso"></a>Configurare ClickUp Productivity Platform SSO

1. In un'altra finestra del Web browser accedere al tenant ClickUp Productivity Platform come amministratore.

2. Fare clic su **User profile** (Profilo utente) e quindi selezionare **Settings** (Impostazioni).

    ![Screenshot che mostra il tenant ClickUp Productivity con l'icona delle impostazioni selezionata.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Screenshot che mostra l'opzione Settings.](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. In Single Sign-On (SSO) Provider (Provider SSO) selezionare **Microsoft**.

    ![Screenshot che mostra il riquadro Authentication con l'opzione Microsoft selezionata.](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. Nella pagina **Configure Microsoft Single Sign On** (Configura Single Sign-On Microsoft) seguire questa procedura:

    ![Screenshot che mostra la pagina Configure Microsoft Single Sign On in cui è possibile copiare l'ID entità e salvare l'URL dei metadati di federazione di Azure.](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Fare clic su **Copy** (Copia) per copiare il valore dell'ID entità e incollarlo nella casella di testo **Identificatore (ID entità)** della sezione **Configurazione SAML di base** del portale di Azure.

    b. Nella casella di testo **Azure Federation Metadata URL** (URL dei metadati di federazione di Azure) incollare il valore dell'URL dei metadati di federazione dell'app copiato dal portale di Azure e quindi fare clic su **Save** (Salva).

5. Per completare la configurazione, fare clic su **Authenticate With Microsoft to complete setup** (Esegui l'autenticazione con Microsoft per completare la configurazione) ed eseguire l'autenticazione con l'account Microsoft.

    ![Screenshot che mostra il pulsante Authenticate with Microsoft to complete setup.](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>Creare l'utente di test di ClickUp Productivity Platform

1. In un'altra finestra del Web browser accedere al tenant ClickUp Productivity Platform come amministratore.

2. Fare clic su **User profile** (Profilo utente) e quindi selezionare **People** (Persone).

    ![Screenshot che mostra il tenant ClickUp Productivity.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Screenshot che mostra il collegamento People selezionato.](./media/clickup-productivity-platform-tutorial/user-1.png)

3. Immettere l'indirizzo di posta elettronica dell'utente nella casella di testo e fare clic su **Invite** (Invita).

    ![Screenshot che mostra la schermata Team Users Settings in cui è possibile invitare utenti tramite posta elettronica.](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > L'utente riceverà la notifica e dovrà accettare l'invito per attivare l'account.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di ClickUp Productivity Platform in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di ClickUp Productivity Platform e avviare il flusso di accesso da questa posizione.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro ClickUp Productivity Platform in app personali, questo verrà reindirizzato all'URL di accesso di ClickUp Productivity Platform. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata la piattaforma di produttività ClickUp, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).