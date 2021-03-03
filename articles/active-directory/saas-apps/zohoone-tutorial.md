---
title: 'Esercitazione: Integrazione di Azure Active Directory con Zoho One | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Zoho One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 12ac4d9fbf30873f0392a6d767d7568129bad112
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650652"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Esercitazione: Integrazione di Azure Active Directory con Zoho One

In questa esercitazione si apprenderà come integrare Zoho One con Azure Active Directory (Azure AD). Quando si integra Zoho One con Azure AD, è possibile:

* Controllare in Azure AD chi ha accesso a Zoho One.
* Consentire agli utenti di accedere automaticamente a Zoho One con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Zoho One, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di Zoho Single Sign-On abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Zoho One supporta l'accesso SSO avviato da **SP** e **IDP**

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-zoho-one-from-the-gallery"></a>Aggiungere Zoho One dalla raccolta

Per configurare l'integrazione di Zoho One in Azure AD, è necessario aggiungere Zoho One dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Zoho One** nella casella di ricerca.
1. Selezionare **Zoho One** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Configurare e testare Azure AD SSO per Zoho One

Configurare e testare Azure AD SSO con Zoho One usando un utente di test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in Zoho One.

Per configurare e testare Azure AD SSO con Zoho One, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare Zoho One SSO](#configure-zoho-one-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di Zoho One](#create-zoho-one-test-user)** : per avere una controparte di B. Simon in Zoho One collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Zoho One** del portale di Azure trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    a. Nella casella di testo **Identificatore** digitare l'URL: `one.zoho.com`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Il valore di **URL di risposta** precedente non è reale. Il valore di `<saml-identifier>` si otterrà dal passaggio 4 della sezione **Configurare l'accesso Single Sign-On di Zoho One**, descritto più avanti nell'esercitazione.

    c. Fare clic su **Impostare URL aggiuntivi**.

    d. Nella casella di testo **Stato dell'inoltro** digitare l'URL: `https://one.zoho.com`

5. Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Il valore precedente dell'**URL di accesso** non è reale. Aggiornare il valore con l'URL di accesso effettivo ottenuto nella sezione **Configurare l'accesso Single Sign-On di Zoho One**, descritta più avanti nell'esercitazione. 

6. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

7. Nella sezione **Configura Zoho One** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a Zoho One.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Zoho One**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="configure-zoho-one-sso"></a>Configurare Zoho One SSO

1. In un'altra finestra del Web browser accedere al sito aziendale di Zoho One come amministratore.

2. Nella scheda **Organizzazione**, fare clic su **Installazione** nella sezione **Autenticazione SAML**.

    ![Organizzazione Zoho One](./media/zoho-one-tutorial/set-up.png)

3. Nella pagina popup eseguire i passaggi seguenti:

    ![Accesso Zoho One](./media/zoho-one-tutorial/save.png)

    a. Nella casella di testo **Sign-in URL** (URL di accesso) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    b. Nella casella di testo **Sign-out URL** (URL di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

    c. Fare clic su **Sfoglia** per caricare il **Certificato (Base64)** scaricato dal portale di Azure.

    d. Fare clic su **Salva**.

4. Dopo il salvataggio della configurazione dell'autenticazione SAML, copiare il valore dell'**identificatore SAML** e aggiungerlo all'**URL di risposta** al posto di `<saml-identifier>`, ad esempio `https://accounts.zoho.com/samlresponse/one.zoho.com`, quindi incollare il valore generato nella casella di testo **URL di risposta** nella sezione **Configurazione SAML di base**.

    ![SAML Zoho One](./media/zoho-one-tutorial/saml-identifier.png)

5. Passare alla scheda **Domini** e quindi fare clic su **Aggiungi dominio**.

    ![Dominio Zoho One](./media/zoho-one-tutorial/add-domain.png)

6. Nella pagina **Aggiungi dominio** seguire la procedura seguente:

    ![Aggiungi dominio Zoho One](./media/zoho-one-tutorial/add-domain-name.png)

    a. Nella casella di testo **Nome di dominio** digitare il dominio, ad esempio contoso.com.

    b. Fare clic su **Aggiungi**.

    >[!Note]
    >Dopo aver aggiunto il dominio seguire [questi](https://www.zoho.com/one/help/admin-guide/domain-verification.html) passaggi per verificarlo. Dopo la verifica del dominio, usare il nome di dominio in **URL di accesso** nella sezione **Configurazione SAML di base** del portale di Azure.

### <a name="create-zoho-one-test-user"></a>Creare l'utente di test di Zoho One

Per consentire agli utenti di Azure AD di accedere a Zoho One, è necessario effettuarne il provisioning in Zoho One. Nel caso di Zoho One il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Zoho One come amministratore della sicurezza.

2. Nella scheda **Utenti**, fare clic su **logo utente**.

    ![Utente Zoho One](./media/zoho-one-tutorial/user.png)

3. Nella pagina **Aggiungi utente** eseguire la procedura seguente:

    ![Aggiungi Utente Zoho One](./media/zoho-one-tutorial/add-user.png)
    
    a. Nella casella di testo **Nome** digitare il nome dell'utente, ad esempio **Britta Simon**.
    
    b. Nella casella di testo **Email address** (Indirizzo e-mail) immettere l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    >[!Note]
    >Selezionare il dominio verificato dall'elenco di domini.

    c. Fare clic su **Aggiungi**.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso Zoho One in cui è possibile avviare il flusso di accesso.  

* Passare direttamente a Zoho One Sign-on URL e avviare il flusso di accesso da qui.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente a Zoho One per cui si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul riquadro Zoho One in app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurata in modalità IDP, si dovrebbe accedere automaticamente a Zoho One per cui si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato Zoho One, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).