---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Kendis-Scaling piattaforma agile | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e Kendis-Scaling piattaforma agile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: e02ff4926897fafc72e1a5081366faad5d2f03ba
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509786"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis-scaling-agile-platform"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con Kendis-Scaling piattaforma agile

In questa esercitazione si apprenderà come integrare Kendis-Scaling piattaforma agile con Azure Active Directory (Azure AD). Quando si integra Kendis-Scaling piattaforma agile con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a Kendis-Scaling piattaforma agile.
* Consentire agli utenti di accedere automaticamente alla piattaforma Kendis-Scaling agile con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Kendis-Scaling sottoscrizione abilitata per la piattaforma agile Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Kendis-Scaling piattaforma agile supporta SSO avviato da **SP e IDP**
* Kendis-Scaling piattaforma agile supporta il provisioning degli utenti **just-in-Time**


## <a name="adding-kendis-scaling-agile-platform-from-the-gallery"></a>Aggiunta di Kendis-Scaling piattaforma agile dalla raccolta

Per configurare l'integrazione di Kendis-Scaling piattaforma agile in Azure AD, è necessario aggiungere Kendis-Scaling piattaforma agile dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **kendis-scaling agile Platform** nella casella di ricerca.
1. Selezionare **kendis-scaling agile Platform** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-kendis-scaling-agile-platform"></a>Configurare e testare Azure AD SSO per Kendis-Scaling piattaforma agile

Configurare e testare Azure AD SSO con Kendis-Scaling piattaforma agile usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Kendis-Scaling piattaforma agile.

Per configurare e testare Azure AD SSO con Kendis-Scaling piattaforma agile, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Kendis-Scaling SSO della piattaforma agile](#configure-kendis-scaling-agile-platform-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare Kendis-Scaling utente test di agile Platform](#create-kendis-scaling-agile-platform-test-user)** : per avere una controparte di B. Simon in Kendis-Scaling piattaforma agile collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella portale di Azure della pagina di integrazione dell'applicazione **kendis-scaling agile Platform** trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.kendis.io`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di kendis-scaling agile Platform](mailto:support@kendis.io) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **configurare Kendis-Scaling agile Platform** copiare gli URL appropriati in base ai requisiti.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Kendis-Scaling piattaforma agile.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **kendis-scaling agile Platform**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-kendis-scaling-agile-platform-sso"></a>Configurare Kendis-Scaling agile Platform SSO

1. In un'altra finestra del Web browser accedere al sito aziendale di Kendis-Scaling agile Platform come amministratore.

1. Passare a **impostazioni > configurazioni SAML**.

    ![impostazioni per le configurazioni SAML](./media/kendis-scaling-agile-platform-tutorial/settings.png)

1. Fare clic sul pulsante **modifica** nella parte inferiore della pagina e seguire questa procedura.

    ![Configurazioni SAML](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Copiare il valore di **URL callback** , incollare questo valore nella casella di testo **URL di risposta** nella sezione di configurazione SAML di base nel portale di Azure.

    b. Nella casella di testo URL di accesso **Single Sign-on del provider di identità** incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c.  Nella casella di testo **autorità di certificazione del provider di identità** incollare il valore dell' **identificatore Azure ad (ID entità)** copiato dall'portale di Azure.

    d. Aprire nel Blocco note il file **Certificato (Base64)** scaricato dal portale di Azure e incollarne il contenuto nella casella di testo **Certificato X.509**.

    e. **Selezionare gruppo predefinito** dall'elenco di opzioni. 

    f. Fare clic su **Salva**.

### <a name="create-kendis-scaling-agile-platform-test-user"></a>Creare Kendis-Scaling utente test di agile Platform

In questa sezione viene creato un utente di nome Britta Simon in Kendis-Scaling piattaforma agile. Kendis-Scaling piattaforma agile supporta il provisioning degli utenti just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già in Kendis-Scaling piattaforma agile, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento a Kendis-Scaling URL di accesso alla piattaforma agile in cui è possibile avviare il flusso di accesso.  

* Passare a Kendis-Scaling URL di accesso alla piattaforma agile direttamente e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente alla piattaforma Kendis-Scaling agile per la quale si configura l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Kendis-Scaling agile Platform in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente alla piattaforma Kendis-Scaling agile per la quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Una volta configurata Kendis-Scaling piattaforma agile, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


