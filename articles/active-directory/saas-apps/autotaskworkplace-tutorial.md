---
title: 'Esercitazione: Integrazione di Azure Active Directory con Autotask Workplace | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Autotask Workplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 1a2602ee3b8fad6e87a778ab966f7cb1f8aad8c2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649952"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Esercitazione: Integrazione di Azure Active Directory con Autotask Workplace

In questa esercitazione si apprenderà come integrare AutoTask area di lavoro con Azure Active Directory (Azure AD). Quando si integra l'area di lavoro di Autotask con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso ad AutoTask area di lavoro.
* Consentire agli utenti di accedere automaticamente all'area di lavoro di Autotask con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione abilitata per l'area di lavoro Autotask Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Autotask Workplace supporta l'accesso SSO avviato da **SP e IDP**

## <a name="add-autotask-workplace-from-the-gallery"></a>Aggiungere l'area di lavoro di Autotask dalla raccolta

Per configurare l'integrazione di Autotask Workplace in Azure AD, è necessario aggiungere Autotask Workplace dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** Digitare **AutoTask area di lavoro** nella casella di ricerca.
1. Selezionare **AutoTask area di lavoro** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Configurare e testare Azure AD SSO per l'area di lavoro di Autotask

Configurare e testare Azure AD SSO con l'area di lavoro di Autotask usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso Single Sign-on, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in AutoTask area di lavoro.

Per configurare e testare Azure AD SSO con AutoTask area di lavoro, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare](#configure-autotask-workplace-sso)** l'accesso SSO per l'area di lavoro per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di AutoTask area di lavoro](#create-autotask-workplace-test-user)** : per avere una controparte di B. Simon nell'area di lavoro di Autotask collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **AutoTask area di lavoro** della portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Screenshot che mostra la sezione Configurazione SAML di base in cui è possibile immettere l'identificatore e l'URL di risposta prima di selezionare Salva.](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Screenshot che mostra l'opzione Impostare URL aggiuntivi che consente di immettere un URL di accesso.](common/metadata-upload-additional-signon.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Set up Autotask Workplace** (Configura Autotask Workplace) copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso all'area di lavoro di Autotask.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **Autotask Workplace**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-autotask-workplace-sso"></a>Configurare l'accesso SSO per l'area di lavoro Autotask

1. In un'altra finestra del Web browser accedere a Workplace Online usando le credenziali di amministratore.

    > [!Note]
    > Quando si configura l'IdP, è necessario specificare un sottodominio. Per verificare che il sottodominio sia corretto, accedere a Workplace Online. Una volta effettuato l'accesso, prendere nota del sottodominio nell'URL. Il sottodominio è la parte tra "https://" e ".awp.autotask.net/" e deve essere us, eu, ca o au.

2. Passare a **Configuration** (Configurazione)  > **Single Sign-On** e seguire questa procedura:

    ![Configurazione Single Sign-On per Autotask](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. Selezionare l'opzione **XML Metadata File** (File di metadati XML) e quindi caricare il file **XML metadati federazione** scaricato dal portale di Azure.

    b. Fare clic su **ENABLE SSO** (Abilita SSO).

    ![Approvazione della configurazione Single Sign-On per Autotask](./media/autotaskworkplace-tutorial/configuration-2.png)

    c. Selezionare la casella di controllo **I confirm this information is correct and I trust this IdP** (Confermo che le informazioni sono corrette e che l'IdP è attendibile).

    d. Fare clic su **APPROVE** (Approva).

> [!Note]
> Se è necessario supporto per la configurazione di Autotask Workplace, vedere [questa pagina](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) per ottenere assistenza per l'account Workplace.

### <a name="create-autotask-workplace-test-user"></a>Creare l'utente di test di Autotask Workplace

In questa sezione viene creato un utente di nome Britta Simon in Autotask Workplace. Collaborare con il [team di supporto di Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) per aggiungere gli utenti alla piattaforma Autotask Workplace.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso all'area di lavoro di Autotask in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso all'area di lavoro di Autotask e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente all'area di lavoro Autotask per la quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro area di lavoro Autotask in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente all'area di lavoro Autotask per la quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurata l'area di lavoro di Autotask, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).