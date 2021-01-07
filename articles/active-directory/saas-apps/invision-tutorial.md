---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con InVision | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e InVision.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 1767730fdd2ca714f4041afcf956d0b367c3e42b
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814517"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con InVision

Questa esercitazione descrive come integrare InVision con Azure Active Directory (Azure AD). Integrando InVision con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a InVision.
* Abilitare gli utenti per l'accesso automatico a InVision con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di InVision abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* InVision supporta l'accesso SSO avviato da **SP e IDP**

## <a name="adding-invision-from-the-gallery"></a>Aggiunta di InVision dalla raccolta

Per configurare l'integrazione di InVision in Azure AD, è necessario aggiungere InVision dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **InVision** nella casella di ricerca.
1. Selezionare **InVision** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-invision"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per InVision

Configurare e testare l'accesso SSO di Azure AD con InVision usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in InVision.

Per configurare e testare l'accesso SSO di Azure AD con InVision, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di InVision](#configure-invision-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di InVision](#create-invision-test-user)** : per avere una controparte di B.Simon in InVision collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **InVision** del portale di Azure individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.invisionapp.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di InVision](mailto:support@invisionapp.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Nella sezione **Configura InVision** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a InVision.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **InVision**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-invision-sso"></a>Configurare l'accesso Single Sign-On di InVision

1. Per automatizzare la configurazione all'interno di InVision, è necessario installare l'**estensione del browser per l'accesso sicuro ad App personali** facendo clic su **Installa l'estensione**.

    ![Estensione MyApps](common/install-myappssecure-extension.png)

2. Dopo aver aggiunto l'estensione al browser, fare clic su **Configura InVision** per passare direttamente all'applicazione InVision. Specificare quindi le credenziali di amministratore per accedere a InVision. L'estensione del browser configurerà automaticamente l'applicazione e automatizzerà i passaggi da 3 a 6.

    ![Eseguire la configurazione](common/setup-sso.png)

3. Se si vuole configurare InVision manualmente, in un'altra finestra del Web browser accedere al sito aziendale di InVision come amministratore.

1. Fare clic su **Team** e selezionare **Settings** (Impostazioni).

    ![Screenshot che mostra la scheda Team con l'opzione Settings selezionata.](./media/invision-tutorial/config1.png)

1. Scorrere verso il basso fino a **Single sign-on** e quindi fare clic su **Change** (Modifica).

    ![Screenshot che mostra il pulsante Change per l'opzione Single sign-on.](./media/invision-tutorial/config3.png)

1. Nella pagina **Single sign-on** seguire questa procedura:

    ![Screenshot che mostra la pagina Single sign-on in cui immettere i valori in questo passaggio.](./media/invision-tutorial/config4.png)

    a. Impostare **Require SSO for every member of < account name >** (Richiedi accesso SSO per ogni membro di <nome account>) su **On** (Attivato).

    b. Nella casella di testo **name** (nome) immettere il nome, ad esempio `azureadsso`.

    c. Nella casella di testo **Sign-in URL** (URL di accesso) immettere il valore dell'URL di accesso.

    d. Nella casella di testo **Sign-out URL** (URL di disconnessione) incollare il valore di **URL disconnessione** copiato dal portale di Azure.

    e. Nella casella di testo **SAML Certificate** (Certificato SAML) aprire il **certificato (Base64)** scaricato nel Blocco note, copiarne il contenuto e incollarlo nella casella di testo.

    f. Nella casella di testo **Name ID Format** (Formato ID nome) usare `Unspecified` come valore di **Name ID Format**.

    g. Selezionare **SHA-256** nell'elenco a discesa per **HASH Algorithm** (Algoritmo hash).

    h. Immettere il nome appropriato per l'**etichetta del pulsante SSO**.

    i. Attivare **Allow Just-in-Time provisioning** (Consenti provisioning JIT).

    j. Fare clic su **Update**.

### <a name="create-invision-test-user"></a>Creare l'utente di test di InVision

1. In un'altra finestra del Web browser accedere al sito Web di InVision come amministratore.

1. Fare clic su **Team** e selezionare **People** (Persone).

    ![Screenshot che mostra la scheda Team con l'opzione People selezionata.](./media/invision-tutorial/config2.png)

1. Fare clic sull'icona **+** per aggiungere un nuovo utente.

    ![Screenshot che mostra l'icona + per aggiungere un utente.](./media/invision-tutorial/user1.png)

1. Immettere l'indirizzo di posta elettronica dell'utente e fare clic su **Next** (Avanti).

    ![Screenshot che mostra la finestra di dialogo Invite to in cui è possibile immettere gli indirizzi.](./media/invision-tutorial/user2.png)

1. Verificare l'indirizzo di posta elettronica e quindi fare clic su **Invite** (Invita).

    ![Screenshot che mostra la finestra di dialogo Invite in cui è possibile selezionare Invite per continuare.](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti.

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di InVision, in cui è possibile avviare il flusso di accesso.

* Passare direttamente all'URL di accesso di InVision e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Dopo aver fatto clic su **Test this application** (Testa questa applicazione) nel portale di Azure, si dovrebbe accedere automaticamente all'istanza di InVision per cui si è configurato l'accesso SSO

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro di InVision in App personali, se è stato configurato in modalità SP, si dovrebbe essere reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso; se invece è configurato in modalità IDP, si dovrebbe accedere automaticamente all'istanza di InVision per cui si è configurato l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato InVision, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).