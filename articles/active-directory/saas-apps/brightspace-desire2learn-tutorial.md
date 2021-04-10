---
title: 'Esercitazione: Integrazione di Azure Active Directory con Brightspace by Desire2Learn | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Brightspace by Desire2Learn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: 61c4125038ea0c64ff8489a25f9c63c5cbb82a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583820"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Esercitazione: Integrazione di Azure Active Directory con Brightspace by Desire2Learn

In questa esercitazione si apprenderà come integrare Brightspace by Desire2Learn con Azure Active Directory (Azure AD). Quando si integra Brightspace by Desire2Learn con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Brightspace by Desire2Learn.
* Abilitare gli utenti per l'accesso automatico a Brightspace by Desire2Learn con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Brightspace di Desire2Learn Single Sign-On sottoscrizione abilitata.

> [!NOTE]
> È possibile usare questa integrazione anche dall'ambiente cloud US Government di Azure AD. Questa applicazione è disponibile nella raccolta di applicazioni cloud US Government di Azure AD e la procedura di configurazione è analoga a quella eseguita dal cloud pubblico.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Brightspace di Desire2Learn supporta l'accesso SSO avviato da **IDP** .

## <a name="add-brightspace-by-desire2learn-from-the-gallery"></a>Aggiungere Brightspace by Desire2Learn dalla raccolta

Per configurare l'integrazione di Brightspace by Desire2Learn in Azure AD, è necessario aggiungere Brightspace by Desire2Learn dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Brightspace by Desire2Learn** nella casella di ricerca.
1. Selezionare **Brightspace by Desire2Learn** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-brightspace-by-desire2learn"></a>Configurare e testare Azure AD SSO per Brightspace by Desire2Learn

Configurare e testare Azure AD SSO con Brightspace by Desire2Learn usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Brightspace by Desire2Learn.

Per configurare e testare Azure AD SSO con Brightspace by Desire2Learn, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Brightspace by DESIRE2LEARN SSO](#configure-brightspace-by-desire2learn-sso)** per configurare le impostazioni Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Brightspace by Desire2Learn](#create-brightspace-by-desire2learn-test-user)** : per avere una controparte di B. Simon in Brightspace by Desire2Learn collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Brightspace by Desire2Learn** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    a. Nella casella di testo **identificatore** Digitare uno degli URL usando i modelli seguenti:

    ```http
    https://<companyname>.tenants.brightspace.com/samlLogin
    https://<companyname>.desire2learn.com/shibboleth-sp
    ```

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere questi valori contattare il [team di supporto clienti di Brightspace by Desire2Learn](https://www.d2l.com/contact/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Brightspace by Desire2Learn** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Brightspace by Desire2Learn.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Brightspace by Desire2Learn**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-brightspace-by-desire2learn-sso"></a>Configurare Brightspace by Desire2Learn SSO

Per configurare l'accesso Single Sign-On sul lato **Brightspace by Desire2Learn**, è necessario inviare il file di **XML metadati federazione** scaricato e gli URL appropriati copiati dal portale di Azure al [team di supporto di Brightspace by Desire2Learn](https://www.d2l.com/contact/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-brightspace-by-desire2learn-test-user"></a>Creare l'utente di test di Brightspace by Desire2Learn

In questa sezione viene creato un utente di nome Britta Simon in Brightspace by Desire2Learn. Collaborare con il [team di supporto di Brightspace by Desire2Learn](https://www.d2l.com/contact/) per aggiungere gli utenti alla piattaforma Brightspace by Desire2Learn. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Brightspace by Desire2Learn per eseguire il provisioning degli account utente di Azure Active Directory.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

* Fare clic su test questa applicazione in portale di Azure e si dovrebbe accedere automaticamente a Brightspace by Desire2Learn per il quale si configura l'accesso SSO.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro Brightspace by Desire2Learn in My Apps (app personali), si dovrebbe accedere automaticamente a Brightspace by Desire2Learn per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Brightspace by Desire2Learn è possibile imporre il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
