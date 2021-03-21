---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Freshworks | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Freshworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 64a8907ec359235c2856b1f92388d9daa38cd190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651723"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Freshworks

Questa esercitazione descrive come integrare Freshworks con Azure Active Directory (Azure AD). Integrando Freshworks con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Freshworks.
* Abilitare gli utenti per l'accesso automatico a Freshworks con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Freshworks abilitata per l'accesso Single Sign-On (SSO).

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Freshworks supporta SSO avviato da **SP e IDP**

## <a name="add-freshworks-from-the-gallery"></a>Aggiungere Freshworks dalla raccolta

Per configurare l'integrazione di Freshworks in Azure AD, è necessario aggiungere Freshworks dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Freshworks** nella casella di ricerca.
1. Selezionare **Freshworks** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-freshworks"></a>Configurare e testare Azure AD SSO per Freshworks

Configurare e testare l'accesso SSO di Azure AD con Freshworks usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Freshworks.

Per configurare e testare Azure AD SSO con Freshworks, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Freshworks](#configure-freshworks-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Freshworks](#create-freshworks-test-user)** : per avere una controparte di B.Simon in Freshworks collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Freshworks** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/CUSTOM_URL`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.freshworks.com/login`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Freshworks](mailto:support@freshworks.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Per modificare le opzioni di **Firma** in base alle esigenze, fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Certificato di firma SAML**.

     ![image](common/edit-certificate.png)

     ![Screenshot che mostra la finestra di dialogo "Certificato di firma SAML" con il pulsante "Modifica" selezionato.](./media/freshworks-tutorial/response.png)

    a. In **Opzione di firma** selezionare **Firma risposta SAML**.

    b. Fare clic su **Salva**.

1. Nella sezione **Configura Freshworks** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Freshworks.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Freshworks**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-freshworks-sso"></a>Configurare l'accesso Single Sign-On di Freshworks

1. Aprire una nuova finestra del Web browser, accedere al sito aziendale di Freshworks come amministratore e seguire questa procedura:

2. Sul lato sinistro del menu fare clic sull'icona **Sicurezza**, quindi selezionare l'opzione **Single Sign-On** e **SSO SAML** in **Metodi di autenticazione**.

    ![Screenshot che mostra la sezione "Security - Authentication Methods" con l'opzione "Single sign-on" attivata e "SAML SSO" selezionato.](./media/freshworks-tutorial/configure01.png)

3. Nella sezione **Single sign-on** seguire questa procedura:

    ![Configurazione di Freshworks](./media/freshworks-tutorial/configure02.png)

    a. Fare clic su **Copy** (Copia) per copiare il valore di **Service Provider(SP) Entity ID** (ID entità provider di servizi) relativo all'istanza e incollarlo nella casella di testo **Identificatore (ID entità)** nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Nella casella di testo **Entity ID provided by the IdP** (ID entità fornito da IdP) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    c. Nella casella di testo **SAML SSO URL** (URL accesso SSO SAML) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    d. Aprire il certificato con codifica Base64, copiarne il contenuto e incollarlo nella casella di testo **Security certificate** (Certificato di sicurezza).

    e. Fare clic su **Salva**.

### <a name="create-freshworks-test-user"></a>Creare l'utente di test di Freshworks

In questa sezione viene creato un utente di nome B.Simon in Freshworks. Collaborare con il [team di supporto di Freshworks](mailto:support@freshworks.com) per aggiungere gli utenti alla piattaforma Freshworks. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di Freshworks, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Freshworks e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al Freshworks per il quale si configura l'accesso SSO 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Freshworks in My Apps (app personali), se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al Freshworks per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Freshworks, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).