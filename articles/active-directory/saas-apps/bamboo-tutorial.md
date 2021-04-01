---
title: 'Esercitazione: integrazione di Azure Active Directory con SAML SSO for Bamboo di resolution GmbH | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAML SSO for Bamboo di resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 8ec834d76692f78d1d7bc60ddbd4c73fe4adaede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646109"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Esercitazione: integrazione di Azure Active Directory con SAML SSO for Bamboo di resolution GmbH

In questa esercitazione si apprenderà come integrare SAML SSO for Bamboo di Resolution GmbH con Azure Active Directory (Azure AD). Quando si integra SAML SSO for Bamboo di Resolution GmbH con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a SAML SSO for Bamboo di Resolution GmbH.
* Consentire agli utenti di eseguire l'accesso automatico a SAML SSO for Bamboo di Resolution GmbH con i relativi account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAML SSO for Bamboo di resolution GmbH, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione attiva del Single Sign-On di SAML SSO for Bamboo by resolution GmbH

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* SAML SSO for Bamboo by resolution GmbH supporta l'accesso SSO avviato da **SP e IDP**
* SAML SSO for Bamboo by resolution GmbH supporta il provisioning utenti **JIT**

## <a name="add-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Aggiungere SAML SSO for Bamboo di Resolution GmbH dalla raccolta

Per configurare l'integrazione di SAML SSO for Bamboo di resolution GmbH in Azure AD è necessario aggiungere SAML SSO for Bamboo di resolution GmbH dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **SAML SSO for Bamboo di Resolution GmbH** nella casella di ricerca.
1. Selezionare **SAML SSO for Bamboo di Resolution GmbH** dal pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Configurare e testare Azure AD SSO con SAML SSO for Bamboo di Resolution GmbH

Configurare e testare Azure AD SSO con SAML SSO for Bamboo di Resolution GmbH, usando un utente test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione collegata tra un utente Azure AD e l'utente correlato in SAML SSO for Bamboo di Resolution GmbH.

Per configurare e testare Azure AD SSO con SAML SSO for Bamboo di Resolution GmbH, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
     1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
     1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
2. **[Configurare SAML SSO for Bamboo di Resolution GmbH SSO](#configure-saml-sso-for-bamboo-by-resolution-gmbh-sso)** : per configurare le impostazioni del singolo Sign-On sul lato applicazione.
    1. **[Creare un utente di test di SAML SSO for Bamboo di Resolution GmbH](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** : per avere una controparte di Britta Simon in SAML SSO for Bamboo di Resolution GmbHby Resolution GmbH collegata alla rappresentazione Azure ad dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione si abilita l'accesso Single Sign-On di Azure AD nel portale di Azure.
 
1. Nella pagina di integrazione dell'applicazione **SAML SSO for Bamboo di Resolution GmbH** della portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita relativa a **Configurazione SAML di base** per modificare le impostazioni.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)
4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

5. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

     Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Contattare il [team di supporto del client di SAML SSO for Bamboo di resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) per ottenere i valori. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

7. Nella sezione **Configura SAML SSO for Bamboo by resolution GmbH** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)


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

In questa sezione viene abilitato B. Simon per l'uso di Azure Single Sign-On concedendo l'accesso a SAML SSO for Bamboo di Resolution GmbH.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **SAML SSO for Bamboo di Resolution GmbH**.
1. Nella pagina di panoramica dell'app individuare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente**. Nella finestra di dialogo **Aggiungi assegnazione** selezionare quindi **Utenti e gruppi**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco degli utenti. Scegliere quindi **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-sso"></a>Configurare SAML SSO for Bamboo di Resolution GmbH SSO

1. Accedere al sito aziendale di SAML SSO for Bamboo di resolution GmbH come amministratore.

1. Sul lato destro della barra degli strumenti principale fare clic su **Settings** (Impostazioni)  > **Add-ons** (Componenti aggiuntivi).

    ![Le impostazioni](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Passare alla sezione SECURITY e fare clic su **SAML SingleSignOn** sulla barra dei menu.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Nella pagina **SAML SingleSignOn Plugin Configuration** (Configurazione plug-in SAML SingleSignOn) fare clic su **Add idp** (Aggiungi IdP).

    ![Add idp (Aggiungi IdP)](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Nella pagina **Choose your SAML Identity Provider** (Scegliere il provider di identità SAML) eseguire la procedura seguente:

    ![Provider di identità](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Come **Idp Type** (Tipo IdP) selezionare **AZURE AD**.

    b. Nella casella di testo **Name** (Nome) digitare un nome.

    c. Nella casella di testo **Description** (Descrizione) digitare la descrizione.

    d. Fare clic su **Avanti**.

1. Nella pagina **Identity provider configuration** (Configurazione provider di identità) fare clic su **Next** (Avanti).

    ![Configurazione identità](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Nella pagina **Import SAML Idp Metadata** (Importa metadati IdP SAML) fare clic su **Load File** (Carica file) per caricare il file **METADATA XML** scaricato dal portale di Azure.

    ![idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Fare clic su **Avanti**.

1. Fare clic su **Save settings** (Salva impostazioni).

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Creare un utente di test di SAML SSO for Bamboo by resolution GmbH

L'obiettivo di questa sezione è creare l'utente Britta Simon in SAML SSO for Bamboo di resolution GmbH. SAML SSO for Bamboo di resolution GmbH supporta il provisioning JIT e anche la creazione manuale di utenti. Contattare il [team di assistenza per il client SAML SSO for Bamboo di resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) in base ai propri requisiti.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso SAML SSO for Bamboo di Resolution GmbH, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL accesso SAML SSO for Bamboo di Resolution GmbH e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a SAML SSO for Bamboo di Resolution GmbH per il quale si configura l'accesso SSO.

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro SAML SSO for Bamboo di Resolution GmbH in app personali, se configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente a SAML SSO for Bamboo di Resolution GmbH per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato SAML SSO for Bamboo di Resolution GmbH, è possibile applicare il controllo della sessione, che protegge exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).