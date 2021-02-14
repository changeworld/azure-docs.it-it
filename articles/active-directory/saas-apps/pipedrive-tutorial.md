---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Pipedrive | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Pipedrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 6f0002f3b8de76bded536c4f434f757c4ac96df6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382549"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Pipedrive

Questa esercitazione descrive come integrare Pipedrive con Azure Active Directory (Azure AD). Integrando Pipedrive con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Pipedrive.
* Abilitare gli utenti per l'accesso automatico a Pipedrive con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Pipedrive abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Pipedrive supporta l'accesso SSO avviato da **SP e IDP**

## <a name="add-pipedrive-from-the-gallery"></a>Aggiungere PipeDrive dalla raccolta

Per configurare l'integrazione di Pipedrive in Azure AD, è necessario aggiungere Pipedrive dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Pipedrive** nella casella di ricerca.
1. Selezionare **Pipedrive** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-pipedrive"></a>Configurare e testare Azure AD SSO per PipeDrive

Configurare e testare l'accesso SSO di Azure AD con Pipedrive usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Pipedrive.

Per configurare e testare Azure AD SSO con PipeDrive, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Pipedrive](#configure-pipedrive-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Pipedrive](#create-pipedrive-test-user)** : per avere una controparte di B.Simon in Pipedrive collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **PipeDrive** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Pipedrive](mailto:support@pipedrive.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione Pipedrive prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione Pipedrive prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine|
    | ------------ | --------- |
    | email | user.mail |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il **certificato (Base64)** e quindi selezionare **Scarica** per scaricare il certificato e salvarlo nel computer. Copiare anche l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](./media/pipedrive-tutorial/certificate-data.png)

1. Nella sezione **Configura Pipedrive** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Pipedrive.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Pipedrive**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-pipedrive-sso"></a>Configurare l'accesso Single Sign-On di Pipedrive

1. In un'altra finestra del browser accedere al sito Web di Pipedrive come amministratore.

1. Fare clic su **User Profile** (Profilo utente) e selezionare **Settings** (Impostazioni).

    ![Screenshot che mostra l'opzione "Settings" selezionata nel menu "User Profile".](./media/pipedrive-tutorial/configure-1.png)

1. Scorrere verso il basso fino al centro sicurezza e selezionare **Single sign-on**.

    ![Screenshot che mostra l'opzione "Single Sign-On" selezionata in "Security Center".](./media/pipedrive-tutorial/configure-2.png)

1. Nella sezione **SAML configuration for pipedrive** (Configurazione SAML per Pipedrive) seguire questa procedura:

    ![Screenshot che mostra la sezione "SAML Configuration for Pipedrive" con le caselle di testo evidenziate.](./media/pipedrive-tutorial/configure-3.png)

    a. Nella casella di testo **Issuer** (Autorità di certificazione) incollare il valore dell'**URL dei metadati di federazione dell'app** copiato dal portale di Azure.

    b. Nella casella di testo **Single Sign On (SSO) url** (URL di accesso SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo **Single Log Out (SLO) url** (URL di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    d. Nella casella di testo **x.509 certificate** (**Certificato x.509**) aprire nel Blocco note il file del **certificato (Base64)** scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella di testo, infine salvare le modifiche.

### <a name="create-pipedrive-test-user"></a>Creare l'utente di test di Pipedrive

1. In un'altra finestra del browser accedere al sito Web di Pipedrive come amministratore.

1. Scorrere verso il basso fino a company (società) e selezionare **manage users** (gestisci utenti).

    ![Screenshot che mostra l'opzione "Manage users" selezionata nel menu "Company".](./media/pipedrive-tutorial/user-1.png)

1. Fare clic su **Add users** (Aggiungi utenti).
    
    ![Screenshot che mostra la pagina "Manage users" con il pulsante "Add users" selezionato sul lato destro.](./media/pipedrive-tutorial/user-2.png)

1. Nella sezione **Manage users** (Gestisci utenti) seguire questa procedura:

    ![Configurazione di Pipedrive](./media/pipedrive-tutorial/user-3.png)

    a. Nella casella di testo **Email** (Posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `B.Simon@contoso.com`.

    b. Nella casella di testo **First name** (Nome) immettere il nome dell'utente.

    c. Nella casella di testo **Last name** (Cognome) immettere il cognome dell'utente.

    d. Fare clic su **Confirm and invite users** (Conferma e invita utenti).

## <a name="test-sso"></a>Testare l'accesso SSO 


In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di PipeDrive, in cui è possibile avviare il flusso di accesso.  

* Passare direttamente all'URL di accesso di PipeDrive e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al PipeDrive per il quale si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro PipeDrive in My Apps (app personali), se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al PipeDrive per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato PipeDrive, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
