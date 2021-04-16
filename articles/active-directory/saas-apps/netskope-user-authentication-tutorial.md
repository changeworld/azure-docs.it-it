---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Netskope User Authentication | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Netskope User Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 78ef49740c3e74c4ad5a30838d9721969ca70b36
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516889"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Netskope User Authentication

Questa esercitazione descrive come integrare Netskope User Authentication con Azure Active Directory (Azure AD). Integrando Netskope User Authentication con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Netskope User Authentication.
* Abilitare gli utenti per l'accesso automatico a Netskope User Authentication con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Netskope User Authentication abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Netskope User Authentication supporta l'accesso SSO avviato da SP e **IDP.**

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Aggiungere Netskope User Authentication dalla raccolta

Per configurare l'integrazione di Netskope User Authentication in Azure AD è necessario aggiungere Netskope User Authentication dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Netskope User Authentication** nella casella di ricerca.
1. Selezionare **Netskope User Authentication** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-netskope-user-authentication"></a>Configurare e testare l Azure AD'accesso SSO per Netskope User Authentication

Configurare e testare l'accesso SSO di Azure AD con Netskope User Authentication usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Netskope User Authentication.

Per configurare e testare l'accesso SSO di Azure AD con Netskope User Authentication, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Netskope User Authentication](#configure-netskope-user-authentication-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di Netskope User Authentication](#create-netskope-user-authentication-test-user)** : per avere una controparte di B.Simon in Netskope User Authentication collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure'applicazione **Netskope User Authentication** individuare la  sezione Gestione e selezionare **Single Sign-On.**
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<tenantname>.goskope.com/<customer entered string>`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Questi valori verranno spiegati più avanti nell'esercitazione.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Poiché il valore di URL accesso non è reale, aggiornarlo con l'URL di accesso effettivo. Per ottenere il valore dell'URL di accesso, contattare il [team di supporto clienti di Netskope User Authentication](mailto:support@netskope.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Netskope User Authentication** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Netskope User Authentication.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Netskope User Authentication**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-netskope-user-authentication-sso"></a>Configurare l'accesso Single Sign-On di Netskope User Authentication

1. Aprire una nuova scheda nel browser e accedere al sito aziendale di Netskope User Authentication come amministratore.

1. Fare clic sulla scheda **Active Platform** (Piattaforma attiva).

    ![Screenshot che mostra la scheda Active Platform selezionata nelle impostazioni.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Scorrere verso il basso fino a **FORWARD PROXY** (PROXY DI INOLTRO) e selezionare **SAML**.

    ![Screenshot che mostra l'opzione SAML selezionata in Active Platform.](./media/netskope-user-authentication-tutorial/configuration.png)

1. Nella pagina delle **impostazioni SAML** seguire questa procedura:

    ![Screenshot che mostra la sezione SAML Settings in cui è possibile immettere i valori descritti.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Copiare il valore di **SAML Entity ID** (ID entità SAML) e incollarlo nella casella di testo **Identificatore** nella sezione **Configurazione SAML di base** del portale di Azure.

    b. Copiare il valore di **SAML ACS URL** (URL ACS entità SAML) e incollarlo nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

1. Fare clic su **ADD ACCOUNT** (AGGIUNGI ACCOUNT).

    ![Screenshot che mostra l'opzione ADD ACCOUNT selezionata nel riquadro SAML.](./media/netskope-user-authentication-tutorial/add-account.png)

1. Nella pagina **Add SAML Account** (Aggiungi account SAML) seguire questa procedura:

    ![Screenshot che mostra la sezione Add SAML Account in cui è possibile immettere i valori descritti.](./media/netskope-user-authentication-tutorial/configure-settings.png)

    a. Nela casella di testo **NAME** (NOME) immettere un nome, ad esempio Azure AD.

    b. Nella casella di testo **IDP URL** (URL dell'IDP) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **IDP ENTITY ID** (ID ENTITÀ IDP) incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    d. Aprire il file dei metadati scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **IDP CERTIFICATE** (CERTIFICATO IDP).

    e. Fare clic su **SAVE**.

### <a name="create-netskope-user-authentication-test-user"></a>Creare l'utente di test di Netskope User Authentication

1. Aprire una nuova scheda nel browser e accedere al sito aziendale di Netskope User Authentication come amministratore.

1. Fare clic sulla scheda **Settings** (Impostazioni) dal riquadro di spostamento sinistro.

    ![Screenshot che mostra la scheda Settings selezionata.](./media/netskope-user-authentication-tutorial/configuration-settings.png)

1. Fare clic sulla scheda **Active Platform** (Piattaforma attiva).

    ![Screenshot che mostra la scheda Active Platform selezionata nelle impostazioni.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Fare clic sulla scheda **Utenti** scheda.

    ![Screenshot che mostra la scheda Users selezionata in Active Platform.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Fare clic su **ADD USERS** (AGGIUNGI UTENTI).

    ![Screenshot che mostra la finestra di dialogo Users in cui è possibile selezionare ADD USERS.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Immettere l'indirizzo di posta elettronica dell'utente che si vuole aggiungere e fare clic su **ADD** (AGGIUNGI).

    ![Screenshot che mostra la schermata Add Users in cui è possibile immettere un elenco di utenti.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato all'URL di accesso di Netskope User Authentication da cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di Netskope User Authentication e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic **su Test this application** in portale di Azure per accedere automaticamente all'autenticazione utente di Netskope per cui si è configurato l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Netskope User Authentication nel App personali, se configurato in modalità SP si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente all'autenticazione utente di Netskope per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Netskope User Authentication, è possibile applicare il controllo sessione, che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
