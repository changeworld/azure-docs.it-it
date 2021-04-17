---
title: "Esercitazione: Azure Active Directory'integrazione dell'accesso Single Sign-On (SSO) con desknets NEO | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e desknets NEO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: ef3e024e1b704cc9f8711d9ca78ee916acf72680
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580765"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Esercitazione: Azure Active Directory'integrazione dell'accesso Single Sign-On (SSO) con NEO di desknet

Questa esercitazione illustra come integrare NEO di desknet con Azure Active Directory (Azure AD). Integrando NEO di desknet con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere al neo di desknet.
* Abilitare gli utenti per l'accesso automatico al dispositivo NEO di desknet con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di DESKNET abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* NEO di desknet supporta l'accesso SSO avviato da **SP.**

## <a name="adding-desknets-neo-from-the-gallery"></a>Aggiunta di NEO di desknet dalla raccolta

Per configurare l'integrazione di NEO di desknet in Azure AD, è necessario aggiungere NEO di desknet dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella **sezione Aggiungi dalla raccolta** digitare **neo di desknet** nella casella di ricerca.
1. Selezionare **NEO di desknet nel** pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Configurare e testare Azure AD SSO per NEO di desknet

Configurare e testare Azure AD SSO con NEO di desknet usando un utente di test di **nome B.Simon**. Per il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in NEO di Desknet.

Per configurare e testare Azure AD SSO con NEO di desknet, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di DESKNET NEO:](#configure-desknets-neo-sso)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di DESKNET NEO:](#create-desknets-neo-test-user)** per avere una controparte di B.Simon in NEO di desknet collegata alla Azure AD dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure'applicazione NEO di **desknet** individuare la **sezione** Gestione e selezionare **Single Sign-On.**
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<CUSTOMER_NAME>.dn-cloud.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per [ottenere questi valori,](mailto:cloudsupport@desknets.com) contattare il team di supporto clienti NEO di desknet. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura desknet's NEO** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a desknet's NEO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **desknet's NEO**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-desknets-neo-sso"></a>Configurare l'accesso SSO NEO di desknet

1. Accedere al sito aziendale di DESKNET NEO come amministratore.

1. Nel menu fare clic **sull'icona delle impostazioni del collegamento di autenticazione SAML.**

    ![Screenshot per le impostazioni del collegamento di autenticazione SAML.](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. In **Impostazioni comuni fare** clic su **Usa** da SAML Authentication Collaboration.

    ![Screenshot per l'uso dell'autenticazione SAML.](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. Seguire questa procedura nella sezione Saml authentication link settings (Impostazioni **di collegamento per l'autenticazione SAML).**

    ![Screenshot per la sezione delle impostazioni del collegamento di autenticazione SAML.](./media/desknets-neo-tutorial/saml-authentication.png)

    a. Nella casella **di testo URL** di accesso incollare il valore di **URL** di accesso copiato dal portale di Azure.

    b. Nella casella **di testo SP Entity ID (ID** entità SP) incollare il valore **Di** identificatore copiato dal portale di Azure.

    c. Fare **clic su Scegli file** per caricare il file certificato **(Base64)** scaricato dal portale di Azure nella casella di testo **x.509 Certificate (Certificato x.509).**

    d. Fare clic **su Cambia**.

### <a name="create-desknets-neo-test-user"></a>Creare l'utente di test NEO di desknet

1. Accedere al sito aziendale di DESKNET NEO come amministratore.

1. Nel **menu fare clic** sull'icona **Impostazioni amministratore.**

    ![Screenshot per le impostazioni dell'amministratore.](./media/desknets-neo-tutorial/administrator-settings.png)

1. Fare **clic sull'icona** impostazioni **e selezionare Gestione utenti** in Impostazioni **personalizzate.**

    ![Screenshot delle impostazioni di Gestione utenti.](./media/desknets-neo-tutorial/user-management.png)

1. Fare clic **su Crea informazioni utente**.

    ![Screenshot per il pulsante Informazioni utente.](./media/desknets-neo-tutorial/create-new-user.png)

1. Compilare i campi obbligatori nella pagina seguente e fare clic su **creazione**.

    ![Screenshot per la sezione Creazione utenti.](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato all'URL di accesso NEO di desknet, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso NEO di desknet e avviare il flusso di accesso da questa pagina.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro NEO della rete di desknet nel App personali, si verrà reindirizzati all'URL di accesso NEO di desknet. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato NEO di desknet, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


